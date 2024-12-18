Принципы SOLID используются для построения легко расширяемой и поддерживаемой архитектуры. <br>
Каждая сущность имеет единственную обязанность. Например, сервер отвечает только за обработку логов и предоставление метрик.<br>
Система открыта для расширения, но закрыта для модификации. Реализуем интерфейсы и классы, которые можно расширять без изменения существующего кода.<br>
Замена одного класса другим через общий интерфейс не нарушит работу системы.<br>
Интерфейсы не перегружены методами, используются только необходимые для каждой реализации.<br>
Зависимости настраиваются через абстракции, а не через конкретные реализации.<br>

### Серверный код 
Файловая структура:<br>
server/ ├── app.py # Основной сервер ├── services/ │ ├── log_service.py # Сервис логирования │ ├── metrics_service.py# Сервис метрик │ └── __init__.py └── interfaces/ ├── log_interface.py # Интерфейс для логов ├── metrics_interface.py # Интерфейс для метрик └── __init__.py 
interfaces/log_interface.py<br>
from abc import ABC, abstractmethod class LogInterface(ABC): @abstractmethod def save_log(self, log_message: str): pass <br>
interfaces/metrics_interface.py<br>
from abc import ABC, abstractmethod class MetricsInterface(ABC): @abstractmethod def get_metrics(self) -> str: pass <br>
services/log_service.py<br>
from interfaces.log_interface import LogInterface class LogService(LogInterface): def save_log(self, log_message: str): with open("logs/system.log", "a") as log_file: log_file.write(f"{log_message}\n") 
services/metrics_service.py<br>
from interfaces.metrics_interface import MetricsInterface class MetricsService(MetricsInterface): def get_metrics(self) -> str: # Пример статической метрики return "example_metric{label='value'} 1\n" 
app.py<br>
from flask import Flask, request, jsonify from services.log_service import LogService from services.metrics_service import MetricsService app = Flask(__name__) # Инстансы сервисов log_service = LogService() metrics_service = MetricsService() @app.route('/collect-log', methods=['POST']) def collect_log(): data = request.json if not data or 'log' not in data: return jsonify({"error": "Invalid input"}), 400 log_service.save_log(data['log']) return jsonify({"status": "Log collected"}), 200 @app.route('/grafana-metrics', methods=['GET']) def grafana_metrics(): return metrics_service.get_metrics() if __name__ == '__main__': app.run(host='0.0.0.0', port=5000) 

### Telegram-бот
Файловая структура:<br>
telegram_bot/ ├── bot.py # Основной бот ├── services/ │ ├── server_service.py # Сервис взаимодействия с сервером │ └── __init__.py <br>
services/server_service.py<br>
import requests class ServerService: def __init__(self, server_url: str): self.server_url = server_url def get_metrics(self): try: response = requests.get(f"{self.server_url}/grafana-metrics") if response.status_code == 200: return response.text return f"Error: Server returned {response.status_code}" except Exception as e: return f"Error: {str(e)}" 
bot.py<br>
import telebot from services.server_service import ServerService TOKEN = "YOUR_TELEGRAM_BOT_TOKEN" SERVER_URL = "http://localhost:5000" bot = telebot.TeleBot(TOKEN) server_service = ServerService(SERVER_URL) @bot.message_handler(commands=['start']) def start(message): bot.send_message(message.chat.id, "Привет! Я бот для мониторинга логов. Отправьте /report для получения отчета.") @bot.message_handler(commands=['report']) def report(message): metrics = server_service.get_metrics() bot.send_message(message.chat.id, f"Текущие метрики:\n{metrics}") if __name__ == "__main__": bot.polling(none_stop=True) 

###Модель машинного обучения
from sklearn.ensemble import RandomForestClassifier<br>
from sklearn.linear_model import LogisticRegression<br>
from sklearn.model_selection import train_test_split<br>
from sklearn.metrics import classification_report<br>
import pandas as pd<br>

# S: Класс отвечает только за обработку данных
class DataProcessor:<br>
    def preprocess(self, data):<br>
        """Подготовка данных: удаление пропусков и разделение на X и y"""<br>
        data = data.dropna()<br>
        X = data.drop(columns=["label"])<br>
        y = data["label"]<br>
        return X, y<br>

# O: Расширяемый набор моделей
class RandomForestModel:<br>
    def __init__(self):<br>
        self.model = RandomForestClassifier()<br>

    def train(self, X_train, y_train):<br>
        self.model.fit(X_train, y_train)<br>

    def predict(self, X):<br>
        return self.model.predict(X)<br>

class LogisticRegressionModel:<br>
    def __init__(self):<br>
        self.model = LogisticRegression()<br>

    def train(self, X_train, y_train):<br>
        self.model.fit(X_train, y_train)<br>

    def predict(self, X):<br>
        return self.model.predict(X)<br>

# L: Интерфейс модели обеспечивает взаимозаменяемость
class ModelInterface:<br>
    def train(self, X_train, y_train):<br>
        raise NotImplementedError<br>

    def predict(self, X):<br>
        raise NotImplementedError<br>

# D: Логика работы зависит от интерфейсов, а не от конкретных классов
class PredictionService:<br>
    def __init__(self, data_processor: DataProcessor, model: ModelInterface):<br>
        self.data_processor = data_processor<br>
        self.model = model<br>

    def train(self, data):<br>
        """Обучение модели"""<br>
        X, y = self.data_processor.preprocess(data)<br>
        X_train, X_val, y_train, y_val = train_test_split(X, y, test_size=0.2, random_state=42)<br>
        self.model.train(X_train, y_train)<br>
        y_pred = self.model.predict(X_val)<br>
        print(classification_report(y_val, y_pred))<br>

    def predict(self, new_data):<br>
        """Предсказание на новых данных"""<br>
        return self.model.predict(new_data)<br>

if __name__ == "__main__":
    # Данные для обучения<br>
    log_data = pd.DataFrame({<br>
        "cpu_usage": [10, 20, 30, 40, 50],<br>
        "memory_usage": [5, 15, 25, 35, 45],<br>
        "label": [0, 0, 1, 1, 1]<br>
    })<br>

    # Подготовка и выбор модели
    data_processor = DataProcessor()<br>
    model = RandomForestModel()  # Можно заменить на LogisticRegressionModel()<br>
    prediction_service = PredictionService(data_processor, model)<br>

    # Обучение и предсказание
    prediction_service.train(log_data)<br>
    new_logs = pd.DataFrame({"cpu_usage": [25, 45], "memory_usage": [20, 40]})<br>
    predictions = prediction_service.predict(new_logs)<br>
    print("Predictions:", predictions)<br>



Как учтены принципы SOLID:<br>
Каждая сущность (сервисы, интерфейсы) отвечает за одну задачу.<br>
Сервисы логов и метрик изолированы.<br>
Расширение функциональности возможно через добавление новых классов, реализующих существующие интерфейсы.<br>
Методы save_log и get_metrics в сервисах реализуют интерфейсы и могут быть заменены другими классами.<br>
Интерфейсы разделены: логирование и метрики не перегружены лишними методами.<br>
Telegram-бот и сервер взаимодействуют через абстракцию ServerService.<br>
Эта реализация следует принципам SOLID, что делает код легко поддерживаемым и расширяемым.<br>
