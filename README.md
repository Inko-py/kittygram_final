
# Kittygram

## Описание проекта
Основные задачи для практики:  
-настроить запуск проекта Kittygram в контейнерах;
-настроить автоматическое тестирование и деплой этого проекта на удалённый сервер.
Проекты Taski был развернут ранее на удаленном сервере, задача автоматически разврернуть проект Kittygram на том же сервере.
Код проекта Kittygram а так же _виртуальный сервер_ были предоставлены **Яндекс.Практикум**.

## Проект доступен по адресу: 
https://kittygram-inko.serveblog.net

## Технологии
- Python 3.10.12
- Django 3.2
- Djangorestframework 3.12.4
- SQLite
- PostgreSQL
- SSH
- Git
- Nginx
- Gunicorn
- SSL
- Docker
- CI/CD: GitHub Actions

## Работа с проектом
Клонируйте проект на сервер.
Создайте файлы окружения .env и .env_db по образцам .env.example и .env_db.example
Установите **Nginx** :
```
sudo apt install nginx -y
```
Установите **Docker** и  **Docker Compose** :
```
sudo apt update
sudo apt install curl
curl -fSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh
sudo apt-get install docker-compose-plugin
```
Из корневой директории проекта выполните(необходимые файлы настроек - Dockerfile для каждого контейнера в соответствующих директориях и docker-compose.yml в корне есть в проекте) :
```
sudo docer compose up -d
```
Затем примените миграции, соберите статику бэкенда и скопируйте ее в контейнере :
```
sudo docker compose  exec backend python manage.py migrate
sudo docker compose  exec backend python manage.py collectstatic
sudo docker compose  exec backend cp -r /app/static/. /static/static/
```
Для автоматизации деплоя на Ваш сервер необходимо создать файл .github/workflows/main.yml по образцу из kittygram_worflow.yml.
Создайте в репозитории гитхаба переменные, указанные в main.yml.
Скопируйте файлы окружения и docker-compose.production.yml на сервер.
Установите на сервере необходимое ПО :
```
sudo apt install nginx -y
sudo apt update
sudo apt install curl
curl -fSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh
sudo apt-get install docker-compose-plugin
sudo snap install --classic certbot
```
Сконфигурируйте Nginx на сервере - в файле /etc/nginx/sites-enabled/default укажите :
```
server{
    <DNS имя сайта> <внешний IP>;
    location / {
        proxy_pass http://127.0.0.1:9000;
    }
```
Запустите **Nginx**
```
sudo systemctl start nginx
```
Получите **сертификат SSL** :
```
sudo certbot --nginx
```
Перезагрузите конфигурацию **Nginx** :
```
sudo systemctl reload nginx
```
Выгрузите обновление проекта на гитхаб :
```
git add .
git commit -m "<comment>"
git push
```
При обновлении кода в репозитории GitHub'a автоматически запускаются проверка кода на соответствие PEP8 и тесты.
При обновлении ветки main - после успешного выполнения вышеупомянутых тестов:
- собираются образы проекта и отправляются на DockerHub
- обновляются образы на сервере и перезапускается приложение при помощи Docker Compose
- выполняются команды для сборки статики в приложении бэкенда, переноса статики в volume; выполнятся миграции
- в случае успешного деплоя - отправляется сообщение в Телеграм

## Дополнительная информация
Копия настроек для .github/workflows/main.yml находится в файле kittygram_worflow.yml в корне проекта.
Отличие docker-compose.yml от docker-compose.production.yml
- docker-compose.yml предназначен для разработки/изменения проекта, с его помощью создаются новые образа для контейнеров.
- docker-compose.production.yml используется для готовых к публикации обновлений проекта, в нем используются заранее собранные образы.

![example workflow](https://github.com/Inko-py/kittygram_final/actions/workflows/main.yml/badge.svg)
## Об авторе
Студент 60-й когорты курса "Python-разработчик" Яндекс.Практикума
Чащихин Сергей
inko-75@yandex.ru
