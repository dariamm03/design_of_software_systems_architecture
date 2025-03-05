## 1. Роли (обязанности) классов
### 1. Информационный эксперт (Information Expert)
Проблема: В коде должен быть объект, который знает всю необходимую информацию для выполнения задачи.<br>
Решение: Назначить класс, который уже владеет нужными данными, ответственным за их обработку.<br>
Пример кода:<br>
class Logger:
    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super(Logger, cls).__new__(cls)
            cls._instance.logs = []
        return cls._instance

    def log(self, message):
        self.logs.append(message)
Результат: Logger отвечает за хранение и обработку логов, что делает код более структурированным.<br>
Связь с другими паттернами: Singleton (Гарантирует единственный экземпляр класса).<br>

### 2 Создатель (Creator)
Проблема: Какой класс должен создавать объекты?<br>
Решение: Класс, который использует объект, должен быть ответственным за его создание.<br>
Пример кода:<br>
class NotificationFactory:
    @staticmethod
    def create_notification(channel):
        if channel == "telegram":
            return TelegramNotification()
        elif channel == "email":
            return EmailNotification()
        else:
            raise ValueError("Unknown notification channel")
Результат: NotificationFactory управляет созданием объектов, что делает код гибким.<br>
Связь с другими паттернами: Factory Method (Позволяет динамически создавать объекты).<br>

### 3 Контроллер (Controller)
Проблема: Как управлять взаимодействием между объектами?<br>
Решение: Создать отдельный класс для управления логикой приложения.<br>
Пример кода:<br>
class NotificationFacade:
    def __init__(self):
        self.logger = Logger()

    def log_and_notify(self, message, channel):
        self.logger.log(message)
        notification = NotificationFactory.create_notification(channel)
        notification.send(message)
Результат: NotificationFacade управляет процессами логирования и отправки сообщений.<br>
Связь с другими паттернами: Facade (Объединяет сложную логику в простой интерфейс).<br>

### 4 Политика (Pure Fabrication)
Проблема: Как добавить новую функциональность без изменения существующих классов?<br>
Решение: Ввести дополнительный класс, который решает задачу, не относясь к предметной области.<br>
Пример кода:
class NotificationAdapter:
    def __init__(self, notification):
        self.notification = notification

    def send_message(self, text):
        self.notification.send(text)
Результат: NotificationAdapter позволяет интегрировать новые типы уведомлений без изменения кода клиента.<br>
Связь с другими паттернами: Adapter (Позволяет работать с разными интерфейсами).<br>

### 5 Низкая связанность (Low Coupling)
Проблема: Классы слишком зависят друг от друга.<br>
Решение: Ввести посредник (LoggingService), который отделяет зависимости.<br>
Пример кода:
class LoggingService:
    def log(self, message):
        Logger.getInstance().log(message)
Результат: LoggingService снижает зависимость классов от Logger.<br>
Связь с другими паттернами: Proxy (Контролирует доступ к объекту).<br>

## 2. Принципы разработки
### 5 Низкая связанность (Low Coupling)
#### 1 Инкапсуляция (Encapsulation)
Проблема: Доступ к внутренним данным объектов нарушает принцип безопасности.<br>
Решение: Скрыть реализацию и предоставить только необходимые методы.<br>
Пример кода:
class Logger:
    _logs = []

    def log(self, message):
        self._logs.append(message)

    def get_logs(self):
        return self._logs
Результат: Внешний код не может напрямую изменять _logs, что предотвращает ошибки.<br>
Связь с другими паттернами: Singleton (Контролирует единственный доступ к логам).<br>

#### 2 Разделение интерфейсов (Interface Segregation)
Проблема: Классы зависят от интерфейсов, которые они не используют.<br>
Решение: Разделить интерфейсы на более мелкие, чтобы классы использовали только нужные методы.<br>
Пример кода:
class NotificationStrategy:
    def send(self, message):
        pass

class TelegramStrategy(NotificationStrategy):
    def send(self, message):
        print(f"Sending Telegram message: {message}")
Результат: Каждый класс реализует только нужные методы.<br>

#### 3 Низкая связанность (Low Coupling)
Проблема: Классы слишком зависят друг от друга.<br>
Решение: Использовать промежуточный сервис для уменьшения зависимости.<br>
Пример кода:<br>
class LoggingService:
    def log(self, message):
        Logger.getInstance().log(message)
Результат: Уменьшение зависимости классов от Logger.<br>
Связь с другими паттернами: Facade (Объединяет логику в одном месте).<br>

## 3. Свойство программы (Масштабируемость)
Проблема: Код сложно расширять без изменения существующей логики.<br>
Решение: Использовать динамическую регистрацию классов.<br>
Пример кода:<br>
class NotificationFactory:
    _registry = {}

    @staticmethod
    def register_notification(channel, cls):
        NotificationFactory._registry[channel] = cls

    @staticmethod
    def create_notification(channel):
        if channel in NotificationFactory._registry:
            return NotificationFactory._registry[channel]()
        raise ValueError("Unknown notification channel")
Результат: Легко добавлять новые каналы, просто зарегистрировав класс.<br>
Связь с другими паттернами: Factory Method (Позволяет динамически создавать объекты)<br>
