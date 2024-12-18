Принцип направлен на устранение дублирования кода. Он обеспечивает простоту поддержки и минимизацию ошибок за счет вынесения повторяющегося кода в отдельные функции, классы или модули.

### Подход к реализации:
1. Серверная часть:<br>
   - Универсализировать обработку запросов.<br>
   - Использовать общий механизм для обработки логов и предоставления метрик.<br>
2. Telegram-бот:<br>
   - Выделить общие части кода (обработка команд, запросы к серверу) в отдельные функции.<br>

### 1. Серверный код

from flask import Flask, request, jsonify<br>
import logging<br>

app = Flask(__name__)<br>

# Настройка логирования
logging.basicConfig(filename="logs/system.log", level=logging.INFO, format="%(asctime)s - %(message)s")<br>

def validate_request(data, keys):<br>
    "Общая функция для проверки входных данных."<br>
    if not data or not all(key in data for key in keys):<br>
        return False, jsonify({"error": "Invalid input"}), 400<br>
    return True, None, None<br>

@app.route('/collect-log', methods=['POST'])<br>
def collect_log():<br>
    "получение логов"<br>
    data = request.json<br>
    is_valid, response, status = validate_request(data, ['log'])<br>
    if not is_valid:<br>
        return response, status<br>

    log_message = data['log']<br>
    logging.info(log_message)<br>
    return jsonify({"status": "Log collected"}), 200<br>

@app.route('/grafana-metrics', methods=['GET'])<br>
def grafana_metrics():<br>
    # Пример метрики<br>
    return "example_metric{label='value'} 1\n"<br>

if __name__ == '__main__':<br>
    app.run(host='0.0.0.0', port=5000)<br>

### 2. Telegram-бот (Клиентская часть)

import telebot<br>
import requests<br>

# Токен Telegram-бота
TOKEN = "YOUR_TELEGRAM_BOT_TOKEN"<br>
bot = telebot.TeleBot(TOKEN)<br>

# Серверный адрес
SERVER_URL = "http://localhost:5000"<br>

def send_server_request(endpoint):<br>
    "Общая функция для отправки запросов на сервер."<br>
    try:<br>
        response = requests.get(f"{SERVER_URL}/{endpoint}")<br>
        if response.status_code == 200:<br>
            return response.text, None<br>
        else:<br>
            return None, f"Ошибка сервера: {response.status_code}"<br>
    except Exception as e:<br>
        return None, f"Ошибка: {str(e)}"<br>

@bot.message_handler(commands=['start'])<br>
def start(message):<br>
    bot.send_message(message.chat.id, "Привет! Я бот для мониторинга логов. Отправьте /report для получения отчета.")<br>

@bot.message_handler(commands=['report'])<br>
def report(message):<br>
    metrics, error = send_server_request("grafana-metrics")<br>
    if error:<br>
        bot.send_message(message.chat.id, error)<br>
    else:<br>
        bot.send_message(message.chat.id, f"Текущие метрики:\n{metrics}")<br>

if __name__ == "__main__":<br>
    bot.polling(none_stop=True)<br>

### 3. Grafana
Grafana подключается к метрикам через Prometheus. Сервер уже предоставляет метрики через API /grafana-metrics.

###Машинное обучение, модель
import pandas as pd<br>
from sklearn.model_selection import train_test_split<br>
from sklearn.ensemble import RandomForestClassifier<br>
from sklearn.metrics import classification_report<br>

class LogPredictor:<br>
    """Класс для предсказания на основе логов"""<br>
    
    def __init__(self, model=None):<br>
        self.model = model or RandomForestClassifier()<br>
    
    def preprocess_data(self, data):<br>
        """Обработка данных: удаление пропусков и нормализация"""<br>
        data = data.dropna()<br>
        X = data.drop(columns=["label"])<br>
        y = data["label"]<br>
        return X, y<br>

    def train(self, data):<br>
        """Обучение модели"""<br>
        X, y = self.preprocess_data(data)<br>
        X_train, X_val, y_train, y_val = train_test_split(X, y, test_size=0.2, random_state=42)<br>
        self.model.fit(X_train, y_train)<br>
        y_pred = self.model.predict(X_val)<br>
        print(classification_report(y_val, y_pred))<br>
    
    def predict(self, X):<br>
        """Предсказание новых данных"""<br>
        return self.model.predict(X)<br>

# Загружаем данные
log_data = pd.DataFrame({<br>
    "cpu_usage": [10, 20, 30, 40, 50],<br>
    "memory_usage": [5, 15, 25, 35, 45],<br>
    "label": [0, 0, 1, 1, 1]<br>
})

# классы без повторения кода
predictor = LogPredictor()<br>
predictor.train(log_data)<br>

# Тест предсказание
new_logs = pd.DataFrame({"cpu_usage": [25, 45], "memory_usage": [20, 40]})<br>
print("Predictions:", predictor.predict(new_logs))<br>
Как здесь соблюден DRY?<br>
1. Вынесены повторяющиеся этапы обработки данных:<br>
   - Функция preprocess_data исключает повторение обработки данных для обучения и предсказания.<br>
2. Обучение и валидация объединены в метод train:<br>
   - Логика разделения данных на обучающую и валидационную выборки реализована один раз.Использование параметризуемой моделили:<br>
   - Модель задается при инициализации класса, что позволяет легко использовать другие алгоритмы без дублирования кода.<br>


### Как применен принцип DRY:
1. Сервер:<br>
   - Введена функция validate_request для проверки входных данных, которая может использоваться в других эндпоинтах.<br>
   - Общая структура кода повторно используется для обработки запросов.<br>
   
2. Telegram-бот:<br>
   - Унифицирована логика запросов к серверу через функцию send_server_request.<br>
   - Исключено дублирование кода обработки ошибок.<br>

3. Простота расширения:<br>
   - Для добавления новых метрик или функциональности нужно лишь использовать существующие общие функции.<br>
