# Лабораторная работа №2

## Задания: 

1. Написать “плохой” Dockerfile, в котором есть не менее трех “bad practices” по написанию докерфайлов;
2. Написать “хороший” Dockerfile, в котором эти плохие практики исправлены;
3. В Readme описать каждую из плохих практик в плохом докерфайле, почему она плохая и как в хорошем она была исправлена, как исправление повлияло на результат;
4. В Readme описать 2 плохих практики по работе с контейнерами. ! Не по написанию докерфайлов, а о том, как даже используя хороший докерфайл можно накосячить именно в работе с контейнерами.

## Установка Docker

> Перед началом уточним, что работаем мы на ОС **Ubuntu 24.04 LTS**. Теперь можем начинать!

* Для установки Docker перейдем на официальный сайт `https://www.docker.com/get-started/`. Вы можете выбрать для себя более удобный способ установки, но мы воспользуемся консолью и выполним следующие команды:
```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```
