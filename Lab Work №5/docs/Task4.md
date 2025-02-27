Также были добавлены интеграционные тесты

import unittest
import requests

class TestMicroservices(unittest.TestCase):

    def test_log_creation(self):
        response = requests.post(
            'http://log_service:5000/api/logs',
            json={'message': 'Test log'}
        )
        self.assertEqual(response.status_code, 201)

    def test_get_logs(self):
        response = requests.get('http://log_service:5000/api/logs')
        self.assertEqual(response.status_code, 200)

    def test_notification(self):
        response = requests.post(
            'http://notification_service:5001/api/notify',
            json={'message': 'Test notification'}
        )
        self.assertEqual(response.status_code, 200)



python -m unittest discover tests
...
----------------------------------------------------------------------
Ran 3 tests in 5.168s

OK

И в ci.yml есть обращение к тестам
