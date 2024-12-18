Следует разрабатывать только ту функциональность, которая необходима на данный момент, избегая добавления ненужных функций и избыточных деталей. <br>
Это позволяет ускорить разработку и сосредоточиться на решении ключевых задач. <br>

Учитываются только ключевые задачи: сбор логов, минимальная интеграция с Grafana и Telegram-ботом. <br>
Разработка машинного обучения и других сложных частей отложена до появления реальной необходимости.<br>


### 1. Серверный код

from flask import Flask, request, jsonify<br>
import logging<br>

app = Flask(__name__)<br>

# Настройка логирования
logging.basicConfig(filename="logs/system.log", level=logging.INFO, format="%(asctime)s - %(message)s")<br>

@app.route('/collect-log', methods=['POST'])<br>
def collect_log():<br>
    """Эндпоинт для получения логов"""<br>
    data = request.json<br>
    if not data or 'log' not in data:<br>
        return jsonify({"error": "Invalid input"}), 400<br>
    
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

@bot.message_handler(commands=['start'])<br>
def start(message):<br>
    bot.send_message(message.chat.id, "Привет! Я бот для мониторинга логов. Отправьте /report для получения отчета.")<br>

@bot.message_handler(commands=['report'])<br>
def report(message):<br>
    try:<br>
        # Запрос метрик с сервера<br>
        response = requests.get(f"{SERVER_URL}/grafana-metrics")<br>
        if response.status_code == 200:<br>
            bot.send_message(message.chat.id, f"Текущие метрики:\n{response.text}")<br>
        else:<br>
            bot.send_message(message.chat.id, "Не удалось получить метрики.")<br>
    except Exception as e:<br>
        bot.send_message(message.chat.id, f"Ошибка: {str(e)}")<br>


if __name__ == "__main__":<br>
    bot.polling(none_stop=True)<br>

### 3. Grafana
Для интеграции с Grafana можно использовать готовый Data Source — Prometheus. Достаточно настроить графики с помощью API /grafana-metrics.


### Учет принципов YAGNI
1. Минимальная функциональность: <br>
   - Реализованы только основные функции: сбор логов, предоставление метрик, базовая интеграция с Telegram-ботом.<br>
   - Сложные функции (например, обработка и анализ логов с помощью ML) не реализованы, так как их необходимость пока не подтверждена.<br>

2. Простота реализации: <br>
   - Использование Flask для сервера и Telebot для бота минимизирует количество кода.<br>
   - Нет избыточных библиотек или сложных конфигураций.<br>

3. Расширяемость: <br>
   - Архитектура позволяет в будущем добавить дополнительные компоненты, такие как аналитика, сложные дашборды, предиктивное моделирование, при возникновении потребности.
