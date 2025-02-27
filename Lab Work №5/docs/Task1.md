### Взяты 2 микросервиса: 
Сервис логирования - собирает и хранит логи.
Сервис уведомлений - отправляет уведомления пользователю.

## Папка log_service:

### log_service.py:

from flask import Flask, request, jsonify

app = Flask(__name__)

logs = []

@app.route('/api/logs', methods=['POST'])
def create_log():
    data = request.json
    logs.append(data)
    return jsonify({"message": "Log created"}), 201

@app.route('/api/logs', methods=['GET'])
def get_logs():
    return jsonify(logs), 200

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)

### Dockerfile:
FROM python:3.9
WORKDIR /app
COPY log_service.py .
RUN pip install Flask
CMD ["python", "log_service.py"]

## Папка notification_service:

### notification_service.py:
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route('/api/notify', methods=['POST'])
def notify_user():
    data = request.json
    # Здесь может быть логика по отправке уведомлений
    return jsonify({"message": "Notification sent"}), 200

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5001)

### Dockerfile:
FROM python:3.9
WORKDIR /app
COPY notification_service.py .
RUN pip install Flask
CMD ["python", "notification_service.py"]

Сборка Docker-образов:
Чтобы упаковать эти сервисы в Docker-контейнеры, нужно создать Docker-образы. Для этого:
Команду сборки образа:
docker build -t log_service ./log_service
docker build -t notification_service ./notification_service
Эти команды создадут образы с именами log_service и notification_service.

Запуск контейнеров:
После того как образы созданы, запускаю контейнеры:
docker run -d --name log_service -p 5000:5000 log_service
docker run -d --name notification_service -p 5001:5001 notification_service
создадутся и запустят контейнеры с сервисами, доступные на портах 5000 и 5001.

Проверка работы контейнеров:
docker ps
CONTAINER ID   IMAGE                  COMMAND                  CREATED             STATUS             PORTS                    NAMES
281c3207be53   notification_service   "python notification…"   About an hour ago   Up About an hour   0.0.0.0:5001->5001/tcp   notification_service
ac7add9836f2   log_service            "python log_service.…"   About an hour ago   Up About an hour   0.0.0.0:5000->5000/tcp   log_service
