<!-- Тут должно быть введение
Написать, что работаем на убунте
-->
# Установка nginx 
Используя утилиту sudo и команду apt install, устанавливаем nginx.

![image](https://github.com/user-attachments/assets/00613fb1-3bb7-4ffb-97a8-ff593b057e4a)

# Запуск nginx 
Запуск и проверка статуса осуществляем с помощью данных команд:
```
sudo systemctl start nginx
sudo systemctl status nginx
```
![image](https://github.com/user-attachments/assets/fad49ab8-c016-4136-8463-c70061355e0b)

Nginx успешно запущен. Чтобы это проверить, можно перейти в браузер и ввести адрес запущенного сервера, так как установка происходила локально, то вводим:
```
http://localhost
```
![image](https://github.com/user-attachments/assets/b20c8529-9538-4bfb-9ee0-20d074487e7b)

Действительно, нас встречает приветственная страница об успешном запуске. 

