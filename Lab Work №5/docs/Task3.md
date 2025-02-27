В корне проекта создала папку .github/workflows.

Создала файл конфигурации CI:


name: CI

on:
  push:
    branches:
      - main  # Запускать только при изменениях в ветке main

jobs:
  build:
    runs-on: ubuntu-latest  # Выбираем операционную систему для выполнения действий
    
    steps:
    - name: Check out the repository
      uses: actions/checkout@v2  # Клонируем репозиторий в рабочую среду

    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v2  # Устанавливаем Docker для сборки

    - name: Build Docker images
      run: |
        docker build -t log_service ./log_service
        docker build -t notification_service ./notification_service

    - name: Run tests
      run: |
        # Если тесты находятся в папке tests, выполняем их
        python -m unittest discover tests
        
Этот файл настроил CI для проекта:
При каждом пуше в ветку main будет выполняться:
Скачивание репозитория, сборка Docker-образов для сервисов, запуск тестов.
![image](https://github.com/user-attachments/assets/73379d88-4b57-4c76-93cd-4d5d6e38fb7a)
