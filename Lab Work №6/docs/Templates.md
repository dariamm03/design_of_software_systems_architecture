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

Код:<br>
class Logger:<br>
    _instance = None<br>
    logs = []<br>

    def __new__(cls):<br>
        if cls._instance is None:<br>
            cls._instance = super(Logger, cls).__new__(cls)<br>
        return cls._instance<br>
Логгер — это глобальный сервис, который должен существовать в одном экземпляре для записи логов. Чтобы избежать создания нескольких логгеров, которые могут дублировать или терять логи.<br>
2. Factory Method (Фабричный метод)<br>
Назначение: Определяет интерфейс для создания объекта, но позволяет подклассам изменять тип создаваемых объектов.<br>
UML:
 ![image](https://github.com/user-attachments/assets/6d57fe84-59ad-4b62-8cf9-39d974bb5350)

Код:<br>
class NotificationFactory:<br>
    def create_notification(channel):<br>
        if channel == "telegram":<br>
            return TelegramNotification()<br>
        elif channel == "email":<br>
            return EmailNotification()<br>
        else:<br>
            raise ValueError("Unknown notification channel")<br>
Позволяет динамически создавать объекты TelegramNotification, EmailNotification и легко добавлять новые.<br>
3. Builder (Строитель)<br>
Назначение: Позволяет создавать сложные объекты пошагово.<br>
UML:
 ![image](https://github.com/user-attachments/assets/0041ebab-ab1b-4bed-a64c-93489f1a9d10)

Код:<br>
class NotificationBuilder:<br>
    def __init__(self):<br>
        self.recipient = None<br>
        self.message = None<br>
        self.attachment = None<br>

    def set_recipient(self, recipient):<br>
        self.recipient = recipient<br>
        return self<br>

    def set_message(self, message):<br>
        self.message = message<br>
        return self<br>

    def set_attachment(self, attachment):<br>
        self.attachment = attachment<br>
        return self<br>

    def build(self):<br>
        return Notification(self.recipient, self.message, self.attachment)<br>
Уведомления могут содержать разные параметры (адресат, текст, вложения), и удобно собирать их поэтапно.<br>

### Структурные шаблоны
1.	Adapter (Адаптер)<br>
 ![image](https://github.com/user-attachments/assets/17c38056-1ca7-485b-b2fa-cdda7f2d8a03)

Код:<br>
class NotificationAdapter:<br>
    def __init__(self, notification):<br>
        self.notification = notification<br>

    def send_message(self, text):<br>
        self.notification.send(text)<br>
Позволяет использовать один интерфейс для отправки уведомлений в Telegram, Email и Slack. Чтобы не менять код старого класса, а просто написать адаптер, который будет работать с новым API.<br>
2.	Facade (Фасад)<br>
 ![image](https://github.com/user-attachments/assets/da47c800-d841-4724-9664-10860e3c558c)

Код:<br>
class NotificationFacade:<br>
    def __init__(self):<br>
        self.logger = Logger()<br>

    def log_and_notify(self, message, channel):<br>
        self.logger.log(message)<br>
        notification = NotificationFactory.create_notification(channel)<br>
        notification.send(message)<br>
Чтобы упростить работу с логированием и отправкой уведомлений.<br>
3. Decorator (Декоратор)<br>
 ![image](https://github.com/user-attachments/assets/9e8efe9b-5428-4383-b3a0-5a89141c59f1)

Код:<br>
class BaseNotifier:<br>
    def send(self, message):<br>
        pass<br>

class NotifierDecorator(BaseNotifier):<br>
    def __init__(self, notifier):<br>
        self.notifier = notifier<br>

    def send(self, message):<br>
        self.notifier.send(message)<br>

class LoggingNotifier(NotifierDecorator):<br>
    def send(self, message):<br>
        logger.log(f"Logging message: {message}")<br>
        super().send(message)<br>
Чтобы, например, перед отправкой сообщения записывать его в лог<br>
4. Proxy (Заместитель)<br>
 ![image](https://github.com/user-attachments/assets/7bf9213c-5469-43a9-9745-0e07c19d273d)

Код:<br>
class NotificationProxy:<br>
    def __init__(self, notification):<br>
        self.notification = notification<br>

    def send(self, message):<br>
        logger.log(f"Sending notification: {message}")<br>
        self.notification.send(message)<br>
Чтобы логировать отправку сообщений.<br>

### Поведенческие шаблоны
1. Observer (Наблюдатель)<br>
 ![image](https://github.com/user-attachments/assets/2684863d-000f-4049-9a0e-a24580e4e561)

Код:<br>
class NotificationSubject:<br>
    def __init__(self):<br>
        self.observers = []<br>

    def add_observer(self, observer):<br>
        self.observers.append(observer)<br>

    def notify_observers(self, message):<br>
        for observer in self.observers:<br>
            observer.update(message)<br>
Чтобы уведомлять всех подписчиков о новом сообщении.<br>
2. Strategy (Стратегия)<br>
 ![image](https://github.com/user-attachments/assets/ecbcadf3-2930-4eb6-8743-cf0c39f5e0c2)

Код:<br>
class NotificationStrategy:<br>
    def send(self, message):<br>
        pass<br>

class TelegramStrategy(NotificationStrategy):<br>
    def send(self, message):<br>
        print(f"Sending Telegram message: {message}")<br>
Чтобы переключаться между Telegram, Email, SMS.<br>
3. Command (Команда)<br>
 ![image](https://github.com/user-attachments/assets/80fea615-4e81-4a3c-a21d-079ccd0c5bff)

Код:<br>
class Command(ABC):<br>
    @abstractmethod<br>
    def execute(self):<br>
        pass<br>
        
class SendNotificationCommand(Command):<br>
    def __init__(self, notifier, message):<br>
        self.notifier = notifier<br>
        self.message = message<br>

    def execute(self):<br>
        self.notifier.send(self.message)<br>
Чтобы хранить команды и выполнять их позже.<br>
4. Chain of Responsibility (Цепочка обязанностей)<br>
 ![image](https://github.com/user-attachments/assets/5ddaf63a-fb65-4e07-b41f-3cc897310b0e)

Код:<br>
class BaseHandler(ABC):<br>
    def __init__(self, next_handler=None):<br>
        self.next_handler = next_handler<br>

    def handle(self, message):<br>
        if self.next_handler:<br>
            self.next_handler.handle(message)<br>
Чтобы сначала пытаться отправить в Telegram, а если не получилось, отправлять в Email.<br>
5. State (Состояние)<br>
 ![image](https://github.com/user-attachments/assets/8d067ab6-ae0e-49dc-8f7a-c82978891a96)

Код:<br>
class NotificationContext:<br>
    def __init__(self):<br>
        self.state = SentState()<br>

    def request(self, message):<br>
        self.state.handle(self, message)<br>
Чтобы уведомления могли быть в статусах "отправлено", "ошибка", "в очереди"<br>
