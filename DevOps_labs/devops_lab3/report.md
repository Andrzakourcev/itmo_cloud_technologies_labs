# Лабораторная работа №3

## Задания:

1. Написать “плохой” CI/CD файл, который работает, но в нем есть не менее пяти “bad practices” по написанию CI/CD
2. Написать “хороший” CI/CD, в котором эти плохие практики исправлены
3. В Readme описать каждую из плохих практик в плохом файле, почему она плохая и как в хорошем она была исправлена, как исправление повлияло на результат

## CI/CD

* Перед выполнение лабораторной работы вкратце разберемся, что из себя представляет аббревиатура **"CI/CD"**. CI/CD (Continuous Integration, Continuous Delivery/Deployment — непрерывная интеграция и доставка/развертывание) - это технология автоматизации тестирования и доставки кода.
* **CI** - сосредоточен на автоматизации интеграции и тестирования кода, чтобы быстро находить и устранять ошибки.
* **CD** - (как Continuous Delivery, так и Continuous Deployment) фокусируется на автоматизации процесса развертывания, чтобы новые изменения могли быть выпущены быстро и безопасно. Основные этапы CI/CD представлены на рисунке:
 
![image](https://github.com/user-attachments/assets/6709a67d-93a2-4643-bd76-46ea7de9b9ef)

* Также стоит упомянуть термин **пайплайн (англ. pipeline)**. Пайплайн (файл в формате `.yml`) представляет из себя набор инструкций, последовательностей выполнения стадий CI/CD. Каждая стадия включает в себя набор действий (команд). По сути пайплайн это и есть CI/CD файл, который просят нас написать.

> Отметим, что для написания пайплайнов мы пользовались синтаксисом и интсрументами GitHub Actions.

## Плохой pipeline

* Для лабораторный работы мы напишем пайплайн для атоматизации работы с каким-нибудь нашим приложением на `Node.js`.
  
```
name: Bad Practices pipeline

on: workflow_dispatch 

jobs:
  lint-test-build:
    runs-on: ubuntu-latest
    steps:
      - name: Chechout
        uses: actions/checkoutv3
      - name: Install dependencies
        run: npm install
      - name: Lint
        run: npm run lint
      - name: Tests
        run: npm test
        continue-on-error: true
      - name: Build
        run: npm run build
```

* Результат работы плохого пайплайна.

![image](https://github.com/user-attachments/assets/f0f3ed74-37ee-4316-8e62-8fd9c5f929a7)

![image](https://github.com/user-attachments/assets/4a70a137-f00c-4c72-aeea-085623c16aa2)


## Хороший (исправленный) pipeline

```
name: Bad Practices pipeline

on: workflow_dispatch

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - name: Chechout
        uses: actions/checkout@v3
      - name: Cache deps
        uses: actions/cache@v3
        with:
        path ~/.npm
        key: ${{ runner.os }}-npm-${{  hashFiles('**/package-lock.json') }}
        restore-keys: |
          ${{  runner.os }}-npm-
      - name: Install deps
        run: npm run ci
      - name: Lint
        run: npm run lint
  test:
    runs-on: ubuntu-latest
    needs: lint
    steps:
      - name: Chechout
        uses: actions/checkout@c3
      - name: Cache deps
             uses: actions/cache@v3
             with:
               path: "~/.npm"
               key: ${{ runner.os }}-npm-${{ hashFiles('**/package-lock.json') }}
               restore-keys: |
                 ${{ runner.os }}-npm-
      - name: Install deps
        run: npm run ci
      - name: Tests
        run: npm run test
        continue-on-error: true
  build:
    runs-on: ubuntu-latest
    needs: test
    steps:
      - name: Chechout
        uses: actions/checkout@v3
      - name: Cache deps
                   uses: actions/cache@v3
                   with:
                     path: "~/.npm"
                     key: ${{ runner.os }}-npm-${{ hashFiles('**/package-lock.json') }}
                     restore-keys: |
                       ${{ runner.os }}-npm-
      - name: Install deps
        run: npm run ci
      - name: Build
        run: npm run build
      - name: Upload Artifact
        uses: actions/upload-artifact@v3
        with:
          path: Build
          name: build-files
  download:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Get build Project
        uses: actions/ download-artifact@v3
        with:
          name: build-files
```

* Результат работы хорошего пайплайна.

![image](https://github.com/user-attachments/assets/ec297279-8eee-48fc-a5cb-b9a269d7a59a)

## Что поменялось?

* В плохом пайплайне все этапы выполнялись в одном задании (`lint-test-build`), что усложняло отладку и поиск ошибок. В хорошем пайплайне каждая этап (линтинг, тестирование, сборка и развертывание) вынесена в отдельные `jobs` (`lint`, `test`, `build`, `download`). Это делает наш пайплайн более модульным и упрощает диагностику ошибок.
* В хорошем пайплайне добавлен шаг для кэширования зависимостей с помощью `actions/cache@v3`. Это позволяет ускорить выполнение пайплайна за счет того, что зависимости не нужно будет устанавливать каждый раз заново, если они не изменились. Но стоит отметить, что использования кэширования не всегда будет являться актуальным решением. Например не всегда целесообразно использовать кеш в проектах, где часто меняются зависимости, или зависимостей в проекте немного и их установка выполняется быстро. Использования кэширования зависит от конкретных условий.
* В старом пайплайне использовалась команда `npm install`, которая может изменять зависимости, которые хранятся в файле `package-lock.json`, и вызывать несоответствия между окружениями. Новый пайплайн использует команду `npm ci`, что гарантирует, что зависимости будут установлены строго в соответствии с `package-lock.json`, обеспечивая согласованность между окружениями.
* Также теперь наши этапы выполняются не только отдельно, но и последовательно. То есть этап `test` начнется только при успешном окончании этапа `lint` и т.д. Это делает порядок выполнения более логичным, и упрощает процесс отслеживания ошибок.
* Также в хорошем пайплайне был убран флаг `continue-on-error: true`, что точно позволит нам увидеть ошибки на этапе тестирования программы. Но стоит отметить, что иногда оставления данного флага является хорошей практикой, например когда пайплайн включает несоклько уровней тестов или когда мы точно значем, что на определенномм тесте программа не будет работать, но мы хотим проверить остальные.
* Также в хорошем пайплайне добавлен шаг для загрузки артефактов с помощью `actions/upload-artifact@v3`. Это позволяет сохранить результаты сборки и использовать их в последующих этапах, например, на следующем этапе мы загружаем собранный проект.

## Выводы

* Были написаны два пайплайна
* Разобраны 5 плохих практик при написании пайплайнов и их решения
