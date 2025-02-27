Создание пользовательской сети:

docker network create mynetwork


Запуск контейнеров в созданной сети:
docker run -d --network mynetwork --name log_service -p 5000:5000 log_service
docker run -d --network mynetwork --name notification_service -p 5001:5001 notification_service

Проверка
 C:\Users\Admin>curl -X POST "http://localhost:5000/api/logs" -H "Content-Type: application/json" -d "{\"message\": \"Test log\"}"
{"message":"Log created"}

C:\Users\Admin>curl -X GET "http://localhost:5000/api/logs"
[{"message":"Test log"}]

C:\Users\Admin>curl -X POST "http://localhost:5001/api/notify" -H "Content-Type: application/json" -d "{\"message\": \"Test notification\"}"
{"message":"Notification sent"}
![image](https://github.com/user-attachments/assets/51760e5c-0205-4a90-a381-02c1fa045520)
