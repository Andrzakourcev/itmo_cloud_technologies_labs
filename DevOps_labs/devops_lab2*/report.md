# Лабораторная работа №2 (*)

## Задания

1. Написать “плохой” Docker compose файл, в котором есть не менее трех “bad practices” по их написанию.
2. Написать “хороший” Docker compose файл, в котором эти плохие практики исправлены.
3. В Readme описать каждую из плохих практик в плохом файле, почему она плохая и как в хорошем она была исправлена, как исправление повлияло на результат.
4. После предыдущих пунктов в хорошем файле настроить сервисы так, чтобы контейнеры в рамках этого compose-проекта так же поднимались вместе, но не "видели" друг друга по сети. В отчете описать, как этого добились и кратко объяснить принцип такой изоляции.

## Что такое Docker Compose

* Перед началом выполнения лабораторной работы вкратце разберем что из себя представляет **Docker Compose** и чем он отличается от **Docker** из прошлой лабораторной работы.
* Docker Compose — это инструментальное средство, входящее в состав Docker. Оно предназначено для решения задач, связанных с развёртыванием проектов.
* Docker применяется для управления отдельными контейнерами (сервисами), из которых состоит приложение.
* Docker Compose используется для одновременного управления несколькими контейнерами, входящими в состав приложения. Этот инструмент предлагает те же возможности, что и Docker, но позволяет работать с более сложными приложениями.

![image](https://github.com/user-attachments/assets/2149951f-2038-4fd2-afd9-44563212a330)

## Создание проекта

* Перед написанием `Docker Compose` - файла создадим небольшой php-проект, который будет выводить в окно нашего браузера приветствующее сообщение.

 ![Снимок экрана от 2024-11-23 08-57-58](https://github.com/user-attachments/assets/dd729744-c113-4f54-b260-e65c72e994af)

* Соберем образ.

![Снимок экрана от 2024-11-23 09-04-40](https://github.com/user-attachments/assets/7683f2b8-7ad0-4555-84e8-e8d03208d037)

* Командой `sudo docker images` - проверяем, что наш образ успешно собрался.

![Снимок экрана от 2024-11-23 09-05-45](https://github.com/user-attachments/assets/8e9554e3-9dcd-4b7b-a2dd-8340153169fc)

* `sudo docker run php-apache-app` - запускаем котнейнер и проверяем его работу.

![Снимок экрана от 2024-11-23 09-57-08](https://github.com/user-attachments/assets/7dc839bd-ee80-475d-93ac-35b0b5d0f4a6)

## Плохой Docker Compose

>  Сначала зайдем на **DockerHub** (https://hub.docker.com/), откуда подтянем несколько готовых образов. Пусть это будет образ `phpmyadmin` (графический интерфейс для управдения базами данных) и БД `mysql`.

![Снимок экрана от 2024-11-25 12-27-17](https://github.com/user-attachments/assets/a7349cf8-be15-40ec-96ad-326f5c1cc248)

![Снимок экрана от 2024-11-25 12-27-42](https://github.com/user-attachments/assets/3627cb78-6186-49bd-810f-755bf6602467)

* Ниже напишем Docker Compose файл с описанием выше упомянутых сервисов и нашим проектом.

```
version: '3.1'

services:
  php: 
    build: ./php
    ports:
      - 8081:80

  db:
    image: mysql
    restart: always
    command: --default-authentication-plugin=mysql_native_password
    environment:
      MYSQL_ROOT_PASSWORD: 12345678
    ports:
      - 3030:3030

  phpmyadmin:
    image: phpmyadmin
    restart: always
    ports:
      - 8080:80
    environment:
      - PMA_ARBITRARY=1
```

* `build: ./php` - явно указываем где находится Dockerfile со всеми настройками проекта.

* Давайте соберем и запустим наш Docker Compose файл
* `sudo docker compose build` - собрать проект.
* `sudo docker compose up` - запустить проект.

![image](https://github.com/user-attachments/assets/45980189-5e44-495c-adc4-8eb1862b97fd)

![Снимок экрана от 2024-11-25 13-08-24](https://github.com/user-attachments/assets/b6965f7e-fbb3-4c7a-9b64-cc662439e8b5)

![Снимок экрана от 2024-11-25 13-08-29](https://github.com/user-attachments/assets/d7806dc7-59d1-4bc4-80b4-45564125de20)

![Снимок экрана от 2024-11-25 13-08-40](https://github.com/user-attachments/assets/7cb3e5ff-2e31-4746-b7c4-4f8a2c4ccc45)

* Как можно увидеть у нас все запустилось. Но у нас не получается подключиться в phpmyadmin. Эту проблему мы решим дальше.

## Хороший Docker Compose
