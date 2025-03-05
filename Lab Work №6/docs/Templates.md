Порождающие шаблоны (3 шт.)<br>
1.	Singleton – можно применить для логгера, чтобы гарантировать, что у тебя есть только один экземпляр логирующего сервиса.<br>
2.	Factory Method – можно использовать для создания уведомлений разных типов (например, Email, Telegram, SMS).<br>
3.	Builder – для гибкого создания сложных объектов уведомлений (например, с заголовком, телом, вложениями и разными способами доставки).<br>
Структурные шаблоны (4 шт.)<br>
1.	Adapter – можно сделать адаптер, чтобы твой notification_service мог отправлять сообщения не только в Telegram, но и на Email или Slack.<br>
2.	Facade – объединить API логирования и уведомлений под одним фасадным классом.<br>
3.	Decorator – расширять функциональность уведомлений, например, добавлять их в очередь или записывать в базу перед отправкой.<br>
4.	Proxy – использовать для логирования запросов к Telegram API.<br>
Поведенческие шаблоны (5 шт.)<br>
1.	Observer – можно сделать подписчиков на уведомления (например, разные сервисы, которые реагируют на новые логи).<br>
2.	Strategy – добавить стратегии отправки уведомлений (Telegram, Email, SMS).<br>
3.	Command – для обработки и планирования уведомлений.<br>
4.	Chain of Responsibility – организовать обработку уведомлений разными способами (например, сначала пытаемся отправить в Telegram, если неудачно – в Email).<br>
5.	State – менять состояние уведомлений (отправлено, в очереди, ошибка).<br>
## Шаблоны проектирования GoF
### Порождающие шаблоны
1. Singleton (Одиночка)<br>
Назначение: Гарантирует, что у класса есть только один экземпляр, и предоставляет к нему глобальную точку доступа.<br>
UML:
 ![image](https://github.com/user-attachments/assets/4db0931f-f7e9-4c39-b3b5-c21733332d84)

Код:
class Logger:
    _instance = None
    logs = []

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super(Logger, cls).__new__(cls)
        return cls._instance
Логгер — это глобальный сервис, который должен существовать в одном экземпляре для записи логов. Чтобы избежать создания нескольких логгеров, которые могут дублировать или терять логи.
2. Factory Method (Фабричный метод)<br>
Назначение: Определяет интерфейс для создания объекта, но позволяет подклассам изменять тип создаваемых объектов.<br>
UML:
 ![image](https://github.com/user-attachments/assets/6d57fe84-59ad-4b62-8cf9-39d974bb5350)

Код:
class NotificationFactory:
    @staticmethod
    def create_notification(channel):
        if channel == "telegram":
            return TelegramNotification()
        elif channel == "email":
            return EmailNotification()
        else:
            raise ValueError("Unknown notification channel")
Позволяет динамически создавать объекты TelegramNotification, EmailNotification и легко добавлять новые.<br>
3. Builder (Строитель)<br>
Назначение: Позволяет создавать сложные объекты пошагово.<br>
UML:
 ![image](https://github.com/user-attachments/assets/0041ebab-ab1b-4bed-a64c-93489f1a9d10)

Код:
class NotificationBuilder:
    def __init__(self):
        self.recipient = None
        self.message = None
        self.attachment = None

    def set_recipient(self, recipient):
        self.recipient = recipient
        return self

    def set_message(self, message):
        self.message = message
        return self

    def set_attachment(self, attachment):
        self.attachment = attachment
        return self

    def build(self):
        return Notification(self.recipient, self.message, self.attachment)
Уведомления могут содержать разные параметры (адресат, текст, вложения), и удобно собирать их поэтапно.<br>

### Структурные шаблоны
1.	Adapter (Адаптер)<br>
 ![image](https://github.com/user-attachments/assets/17c38056-1ca7-485b-b2fa-cdda7f2d8a03)

Код:
class NotificationAdapter:
    def __init__(self, notification):
        self.notification = notification

    def send_message(self, text):
        self.notification.send(text)
Позволяет использовать один интерфейс для отправки уведомлений в Telegram, Email и Slack. Чтобы не менять код старого класса, а просто написать адаптер, который будет работать с новым API.<br>
2.	Facade (Фасад)<br>
 ![image](https://github.com/user-attachments/assets/da47c800-d841-4724-9664-10860e3c558c)

Код:
class NotificationFacade:
    def __init__(self):
        self.logger = Logger()

    def log_and_notify(self, message, channel):
        self.logger.log(message)
        notification = NotificationFactory.create_notification(channel)
        notification.send(message)
Чтобы упростить работу с логированием и отправкой уведомлений.<br>
3. Decorator (Декоратор)<br>
 ![image](https://github.com/user-attachments/assets/9e8efe9b-5428-4383-b3a0-5a89141c59f1)

Код:
class BaseNotifier:
    def send(self, message):
        pass

class NotifierDecorator(BaseNotifier):
    def __init__(self, notifier):
        self.notifier = notifier

    def send(self, message):
        self.notifier.send(message)

class LoggingNotifier(NotifierDecorator):
    def send(self, message):
        logger.log(f"Logging message: {message}")
        super().send(message)
Чтобы, например, перед отправкой сообщения записывать его в лог<br>
4. Proxy (Заместитель)<br>
 ![image](https://github.com/user-attachments/assets/7bf9213c-5469-43a9-9745-0e07c19d273d)

Код:
class NotificationProxy:
    def __init__(self, notification):
        self.notification = notification

    def send(self, message):
        logger.log(f"Sending notification: {message}")
        self.notification.send(message)
Чтобы логировать отправку сообщений.<br>

### Поведенческие шаблоны
1. Observer (Наблюдатель)<br>
 ![image](https://github.com/user-attachments/assets/2684863d-000f-4049-9a0e-a24580e4e561)

Код:
class NotificationSubject:
    def __init__(self):
        self.observers = []

    def add_observer(self, observer):
        self.observers.append(observer)

    def notify_observers(self, message):
        for observer in self.observers:
            observer.update(message)
Чтобы уведомлять всех подписчиков о новом сообщении.<br>
2. Strategy (Стратегия)<br>
 ![image](https://github.com/user-attachments/assets/ecbcadf3-2930-4eb6-8743-cf0c39f5e0c2)

Код:
class NotificationStrategy:
    def send(self, message):
        pass

class TelegramStrategy(NotificationStrategy):
    def send(self, message):
        print(f"Sending Telegram message: {message}")
Чтобы переключаться между Telegram, Email, SMS.<br>
3. Command (Команда)<br>
 ![image](https://github.com/user-attachments/assets/80fea615-4e81-4a3c-a21d-079ccd0c5bff)

Код:
class Command(ABC):
    @abstractmethod
    def execute(self):
        pass

class SendNotificationCommand(Command):
    def __init__(self, notifier, message):
        self.notifier = notifier
        self.message = message

    def execute(self):
        self.notifier.send(self.message)
Чтобы хранить команды и выполнять их позже.<br>
4. Chain of Responsibility (Цепочка обязанностей)<br>
 ![image](https://github.com/user-attachments/assets/5ddaf63a-fb65-4e07-b41f-3cc897310b0e)

Код:
class BaseHandler(ABC):
    def __init__(self, next_handler=None):
        self.next_handler = next_handler

    def handle(self, message):
        if self.next_handler:
            self.next_handler.handle(message)
Чтобы сначала пытаться отправить в Telegram, а если не получилось, отправлять в Email.<br>
5. State (Состояние)<br>
 ![image](https://github.com/user-attachments/assets/8d067ab6-ae0e-49dc-8f7a-c82978891a96)

Код:
class NotificationContext:
    def __init__(self):
        self.state = SentState()

    def request(self, message):
        self.state.handle(self, message)
Чтобы уведомления могли быть в статусах "отправлено", "ошибка", "в очереди"<br>
