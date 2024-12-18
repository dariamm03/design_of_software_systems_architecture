Этот принцип направлен на упрощение структуры и логики кода, минимизацию избыточной сложности. В данном примере код разбит на основные компоненты для ясности.

Клиентская часть: Telegram-бот
Клиентская часть реализована с использованием библиотеки python-telegram-bot. Бот позволяет получать уведомления и отправлять команды на сервер.


from telegram import Update
from telegram.ext import ApplicationBuilder, CommandHandler, ContextTypes
import requests

API_URL = "http://127.0.0.1:5000/api/logs"  # Адрес API сервера

# Команда для проверки состояния системы
async def status(update: Update, context: ContextTypes.DEFAULT_TYPE):<br>
    response = requests.get(f"{API_URL}/status")<br>
    if response.ok:<br>
        await update.message.reply_text(f"Состояние системы: {response.json().get('status')}")<br>
    else:<br>
        await update.message.reply_text("Ошибка при получении состояния системы.")<br>

# Команда для получения последних логов
async def logs(update: Update, context: ContextTypes.DEFAULT_TYPE):<br>
    response = requests.get(f"{API_URL}/latest")<br>
    if response.ok:<br>
        logs = response.json().get('logs', [])<br>
        await update.message.reply_text("\n".join(logs))<br>
    else:<br>
        await update.message.reply_text("Ошибка при получении логов.")<br>

# Основная настройка Telegram-бота
def main():<br>
    app = ApplicationBuilder().token("YOUR_TELEGRAM_BOT_TOKEN").build()<br>
    app.add_handler(CommandHandler("status", status))<br>
    app.add_handler(CommandHandler("logs", logs))<br>
    app.run_polling()<br>

if name == "main":<br>
    main()<br>

    
Принципы KISS:
Простая архитектура команд: каждая команда делает одно действие.<br>
Логика взаимодействия с сервером выделена в отдельные HTTP-запросы.<br>
Серверная часть<br>
Серверная часть реализована на Flask для обработки запросов клиента и взаимодействия с системой сбора логов.<br>


from flask import Flask, jsonify, request<br>
app = Flask(name)<br>

# Пример хранилища логов (в реальной системе это может быть база данных)
logs = [<br>
    "2024-12-18 10:00:00 INFO System started",<br>
    "2024-12-18 10:05:00 ERROR Connection timeout",<br>
    "2024-12-18 10:10:00 INFO Data processed successfully"<br>
]<br>

@app.route('/api/logs/status', methods=['GET'])<br>
def get_status():<br>
    return jsonify({"status": "OK"}), 200<br>

@app.route('/api/logs/latest', methods=['GET'])<br>
def get_latest_logs():<br>
    return jsonify({"logs": logs[-5:]}), 200<br>

@app.route('/api/logs', methods=['POST'])<br>
def add_log():<br>
    new_log = request.json.get('log')<br>
    if not new_log:<br>
        return jsonify({"error": "Log content is required"}), 400<br>
    logs.append(new_log)<br>
    return jsonify({"message": "Log added"}), 201<br>

if name == "main":<br>
    app.run(debug=True)<br>

    
Чёткое разделение API-эндпоинтов: каждый эндпоинт отвечает за конкретную задачу.<br>
Простое хранилище логов в виде списка для демонстрации.<br>
Модель машинного обучения<br>
Обучение модели вынесено в отдельный модуль. Для примера используется библиотека scikit-learn.<br>


from sklearn.ensemble import RandomForestClassifier<br>
from sklearn.model_selection import train_test_split<br>
from sklearn.metrics import accuracy_score<br>
import pickle<br>
import pandas as pd<br>

# Загружаем и подготавливаем данные
data = pd.read_csv("log_data.csv")<br>
X = data.drop("label", axis=1)<br>
y = data["label"]<br>

# Разделение на тренировочные и тестовые наборы
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)<br>

# Обучение модели
model = RandomForestClassifier()<br>
model.fit(X_train, y_train)<br>

# Оценка качества модели
y_pred = model.predict(X_test)<br>
print(f"Accuracy: {accuracy_score(y_test, y_pred):.2f}")<br>

# Сохранение модели
with open("model.pkl", "wb") as f:<br>
    pickle.dump(model, f)<br>

Простая структура обучения и сохранения модели.<br>
Использование готовых библиотек для минимизации ручной работы.<br>

Пояснение использования KISS:<br>
Минимизация сложности:<br>
Код каждого компонента содержит только базовый функционал, без избыточных зависимостей.<br>
Четкое разделение задач:<br>
Telegram-бот, сервер и модель машинного обучения реализованы в отдельных модулях.<br>
Переиспользуемость:<br>
Простая структура API позволяет легко добавлять новые функции.<br>
Простота тестирования:<br>
Минимум сложных зависимостей и функций упрощает отладку и тестирование.<br>
Данный подход позволяет быстро разрабатывать и поддерживать систему.<br>
