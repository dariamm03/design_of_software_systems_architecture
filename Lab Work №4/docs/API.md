Подробное описание реализуемого API, включая методы, параметры запросов, форматы данных и примеры запросов с тестированием.

1. Получение логов пользователя

Метод: GET

Строка запроса:  
http://127.0.0.1:5000/api/logs?user=user@example.com

Параметры запроса:  
- user (строка) — email пользователя, для которого нужно получить логи.

Ответ:  
- Код ответа: 200 OK (если пользователь найден).
- Формат ответа: JSON.
- Пример ответа:
    
    [
        "Log 1",
        "Log 2"
    ]
    
![image](https://github.com/user-attachments/assets/4fb7974c-2903-4094-ac00-c43dd89e3e61)


2. Создание нового лога

Метод: POST

Строка запроса:  
http://127.0.0.1:5000/api/logs

Тело запроса:  
{
    "user": "user@example.com",
    "log": "New log entry"
}


Параметры тела:  
- user (строка) — email пользователя.
- log (строка) — содержимое лога.

Ответ:  
- Код ответа: 201 Created (если лог успешно создан).
- Формат ответа: JSON.
- Пример ответа:
    
    {
        "message": "Log created"
    }
    
![image](https://github.com/user-attachments/assets/afe95081-acd6-4ce8-93b3-74515ce6b337)

3. Обновление лога по ID

Метод: PUT

Строка запроса:  
http://127.0.0.1:5000/api/logs/0

Тело запроса:  
{
    "log": "Updated log entry"
}


Параметры тела:  
- log (строка) — новое содержимое лога.

Ответ:  
- Код ответа: 200 OK (если лог успешно обновлен).
- Формат ответа: JSON.
- Пример ответа:
    
    {
        "message": "Log updated"
    }
    
![image](https://github.com/user-attachments/assets/31c2346b-852c-4553-9547-6d38416969ae)

4. Удаление лога по ID

Метод: DELETE

Строка запроса:  
http://127.0.0.1:5000/api/logs/0

Ответ:  
- Код ответа: 204 No Content (если лог успешно удален).
![image](https://github.com/user-attachments/assets/c8a594bb-efa3-4054-b2f0-24ac2b491cd3)


5. Получение списка пользователей

Метод: GET

Строка запроса:  
http://127.0.0.1:5000/api/users

Ответ:  
- Код ответа: 200 OK.
- Формат ответа: JSON.
- Пример ответа:
    
    [
        "user1@example.com",
        "user2@example.com",
        "user@example.com"
    ]
 ![image](https://github.com/user-attachments/assets/fc23bb91-bdcd-4d35-bcb9-6c32a10a4740)
   
6. Создание нового пользователя

Метод: POST

Строка запроса:  
http://127.0.0.1:5000/api/users

Тело запроса:  
{
    "email": "newuser@example.com"
}


Параметры тела:  
- email (строка) — email нового пользователя.

Ответ:  
- Код ответа: 201 Created (если пользователь успешно создан).
- Формат ответа: JSON.
- Пример ответа:
    
    {
        "message": "User created"
    }

![image](https://github.com/user-attachments/assets/1a9794fc-30a0-4106-8abc-2bcc1b45d6dc)
    
7. Получение логов для несуществующего пользователя

Метод: GET

Строка запроса:  
http://localhost:5000/api/logs?user=nonexistent@example.com

Ответ:  
- Код ответа: 404 Not Found (если фон не найден).
- Формат ответа: JSON.
- Пример ответа:
    
    {
        "error": "Logs not found for the specified user"
    }
![image](https://github.com/user-attachments/assets/0dfb727b-f6d6-49e4-beee-a612c4a5e73e)
    
8. Удаление несуществующего лога

Метод: DELETE

Строка запроса:  
http://localhost:5000/api/logs/5

Ответ:  
- Код ответа: 404 Not Found (если лог не найден).
- Формат ответа: JSON.
- Пример ответа:
    
    {
        "error": "Log not found"
    }
    
![image](https://github.com/user-attachments/assets/fb2dc90e-562d-4a78-b3b0-c15d5fb820c8)

9. Создание лога без необходимых полей

Метод: POST

Строка запроса:  
http://localhost:5000/api/logs

Тело запроса:  
{}


Ответ:  
- Код ответа: 400 Bad Request (если отсутствуют обязательные поля).
- Формат ответа: JSON.
- Пример ответа:
    
    {
        "error": "Bad Request: 'user' and 'log' fields are required"
    }
    
![image](https://github.com/user-attachments/assets/1fabf56d-bf32-42bd-9eab-15d693d0a270)

10. Обновление лога по email пользователя

Метод: PUT

Строка запроса:  
http://localhost:5000/api/logs/user@

Тело запроса:  
{
    "log": "Новый лог"
}


Параметры тела:  
- log (строка) — новое содержимое лога.

Ответ:  
- Код ответа: 404 Not Found (если логи пользователя не найдены).
- Формат ответа: JSON.
- Пример ответа:
    
    {
        "error": "Not Found: User logs not found"
    }
    ![image](https://github.com/user-attachments/assets/21805aa3-8e0f-4995-a5f5-71df31ed4b67)

