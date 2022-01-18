# Лабораторная работа №3
> Цель работы: Поиск и устранение XSS уязвимостей.

## Выполнили:
> Миронова Светлана Владимировна
 
> Матвиенко Дарья Константиновна

## Задание 1
В папке ``lab3`` находится ``nodejs`` уязвимое приложение. Необходимо развернуть его, найти источники XSS и исправить. Модифицированное приложение загрузить в свой репозиторий GitHub.  

Для выполнения лабораторной потребуется проделать следующие шаги:  
1. Установить PostgreSQL сервер любой версии 
   
   Устанавливаем программу по ссылке: https://winitpro.ru/index.php/2019/10/25/ustanovka-nastrojka-postgresql-v-windows/ 
2. Создать БД ``lib``    
3. Применить к ней скрипты из папки ``db`` (либо создать объекты и вставить данные в таблицы руками). Скрипты выполнять в порядке указанном в имени файла.  
   
   3.1 Восстановить данные из файла ``data.sql``  
4. Установить ``nodejs`` версии 14.  
5. Перейти в папку ``lab3`` и выполнить в ней команду ``npm install``.  
6. Запустить сайт через Visual Studio Code или через команду ``npm start``.  
   Потребовалось установить модуль «cookie-parser» для последующей работы с файлами Cookie.

   ![30](https://user-images.githubusercontent.com/87654857/147643373-088fcf71-34fe-469a-bd33-64cf65bb916d.png)

7. Войти на сайт и увидеть список книг и авторов  
   
   ![31](https://user-images.githubusercontent.com/91056608/149960033-423fe88e-0d89-415c-82d1-695f97640639.png)

8. На странице со списком книг найти  
   
   8.1 Reflected XSS в поиске книг  
   
   ***img src=1 href=1 onerror='javascript:alert(1)'***

   ![32](https://user-images.githubusercontent.com/87654857/149493228-bbad7c91-0ee5-4568-896a-5eed2e886ca7.png)

   Исправление уязвимости:

   Для строки поиска книг можно воспользоваться prepared statement, как в ЛР2

   ![33](https://user-images.githubusercontent.com/87654857/147643430-2c1bf953-1975-4436-8704-a2026498ee9d.png)

   ![34](https://user-images.githubusercontent.com/87654857/147643442-890a0974-1ff0-48a7-8a25-fa0274aef0b0.png)
   
   8.2 Persisted (Stored) XSS при создании книги и отображении списка книг

   ***<html onclick=“alert(1)”>test</html>***
   
   Теперь при любом клике на сайте будет выскакивать окно:
   
   ![35](https://user-images.githubusercontent.com/87654857/149493211-75ee8caf-48a1-4494-a33f-74ae24f58933.png)
   
   Команда в исходном коде:
   
   ![36](https://user-images.githubusercontent.com/87654857/147643488-21384e19-9aa3-4a69-8fbe-a61fc55be902.png)
   
   Исправление уязвимости:
   
   При добавлении книг самый простой способ – просто заменять символы < и >, например:
   
   ***let bname = req.body.bookname.replace(‘<’, ‘(’).replace(‘>’, ‘)’)***
   
   ![37](https://user-images.githubusercontent.com/87654857/147643505-7fd47b50-267e-450e-85ae-829ff0c04a12.png)
   
   8.3 Потенциальную уязвимость через Cookie Injection  
   
   Добавляем в cookie:
   
   ![38](https://user-images.githubusercontent.com/87654857/147643520-f6e3dac6-dda7-484c-a7ef-04d8f79063ff.png)
   
   Результат при обновлении страницы:
   
   ![39](https://user-images.githubusercontent.com/87654857/147643544-9f0f7250-82fb-449f-bbd9-00cb5feb0d8c.png)


   8.4 Некорректное создание сессионной cookie, которое приводит к захвату сессии (Session hijacking) 

   ![40](https://user-images.githubusercontent.com/87654857/147643555-63a98d78-3125-4ef9-987c-741a906cd75e.png)
 
9.  Исправление уязвимостей:
    
    Для уязвимостей Cookie установим флаг http-only, который запрещает любой доступ к куки из JavaScript, а также изменим значение, выводимое браузером в Value с помощью алгоритма хеширования Sha256:
    
    ![41](https://user-images.githubusercontent.com/87654857/149473425-084be45c-ce3b-4659-9ef7-acd39bf7f1de.jpg)
    
    ![42](https://user-images.githubusercontent.com/87654857/147643711-3a52dee2-bb37-4ac1-87d3-20a24ebaf2c6.png)

Полезные ссылки
1. lecture 5 (cross site scripting).pptx lecture 6 (cross site scripting cont.).pptx
2. https://owasp.org/www-community/Types_of_Cross-Site_Scripting
3. https://www.ptsecurity.com/ww-en/analytics/knowledge-base/what-is-a-cross-site-scripting-xss-attack/
4. https://habr.com/ru/post/511318/
5. https://pugjs.org/api/getting-started.html
6. https://pugjs.org/language/code.html
7. https://developer.mozilla.org/ru/docs/Web/HTTP/Cookies
8. https://developer.mozilla.org/en-US/docs/Web/Security/Types_of_attacks#session_hijacking
9. https://owasp.org/www-community/attacks/Session_hijacking_attack
10. https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html
