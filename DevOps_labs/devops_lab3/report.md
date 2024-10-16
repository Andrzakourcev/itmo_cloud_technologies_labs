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

* Также стоит упомянуть термин **пайплайн (англ. pipeline)**. Пайплайн (файл в формате `.yml`) представляет из себя набор инструкций, последовательностей выполнения стадий CI/CD. Каждая стадия включает в себя набор действий (команд).

> Отметим, что для написания пайплайнов мы пользовались синтаксисом и интсрументами GitHub Actions.

## Плохой pipeline

```
name: Bad Practices pipeline

on: workflow_dispatch

jobs:
  lint-test-build:
    runs-on: ubuntu-latest
    steps:
      - name: Chechout
        uses: actions/checkout@c3
      - name: Install dependencies
        run: npm install
      - name: Run lint
        run: npm run lint
      - name: Run tests
        run: npm test
        continue-on-error: true
      - name: Build project
        run: npm run build
```

## Хороший (исправленный) pipeline

```
name: Bad Practices pipeline

on: workflow_dispatch

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - name: Chechout
        uses: actions/checkout@c3
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
      - name: Install deps
        run: npm run ci
      - name: Test
        run: npm run test
        continue-on-error: true
  build:
    runs-on: ubuntu-latest
    needs: test
    steps:
      - name: Chechout
        uses: actions/checkout@c3
      - name: Install deps
        run: npm run ci
      - name: Build
        run: npm run build
      - name: Upload Artifact
        uses: actions/upload-artifact@v3
        with:
          path: build
          name: build-files
  deploy:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Get build Project
        uses: actions/ download-artifact@v3
        with:
          name: build-files
```
## Что поменялось?
