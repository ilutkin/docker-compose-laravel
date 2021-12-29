# docker-compose-laravel
Довольно упрощенный рабочий процесс Docker Compose, который настраивает сеть контейнеров LEMP для локальной разработки Laravel. Вы можете просмотреть полную статью, которая послужила вдохновением для этого репо [здесь] (https://dev.to/aschmelyun/the-beauty-of-docker-for-local-laravel-development-13c0).

## Различия

Является ответвлением aschmelyun/docker-compose-laravel

Главные отличия:

- Каталог проекта находится не в ./src , а в соседнем каталоге
- Добавлен том хранения данных для mysql
- Добавлен phpMyAdmin
- Настройки для пользователя root

## Подготовка

Для начала убедитесь, что в вашей системе установлен Docker.

Создайте структуру каталогов вашего проекта, <you project name> - название вашего проекта, например "admin"

    ├── <you project name>             
    │   ├── docker                  # Каталог с данным репозиторием
    │   ├── <you project name>      # Каталог проекта Laravel

Клонируйте данный репозиторий в каталог docker

- `cd ./<you project name>/docker`
- `git init`
- `git remote add origin https://github.com/ilyutkin/docker-compose-laravel.git`
- `git fetch`
- `git checkout -t -f origin/master`

Скопируйте файл `./<you project name>/docker/.env.example` в файл `./<you project name>/docker/.env`.

В файлах `./<you project name>/docker/docker-compose.yml` и `./<you project name>/docker/.env` замените `project` на название вашего проекта.

## Использование

Перейдите в своем терминале в каталог `./<you project name>/docker` и разверните контейнеры для веб-сервера, запустив `docker-compose up -d --build <you project name>`.

Запуск сети Docker Compose с помощью `<you project name>` вместо простого` up` гарантирует, что в начале будут запущены только контейнеры нашего сайта, а не все контейнеры команд. Следующие компоненты созданы для нашего веб-сервера с подробным описанием их открытых портов:

- **nginx** - `:80`
- **mysql** - `:3306`
- **php** - `:9000`
- **redis** - `:6379`
- **mailhog** - `:8025`
- **phpmyadmin** - `:9090`

Включены три дополнительных контейнера, которые обрабатывают команды Composer, NPM и Artisan без необходимости установки этих платформ на вашем локальном компьютере. Используйте следующие примеры команд из корня вашего проекта, изменяя их в соответствии с вашим конкретным вариантом использования.

- `docker-compose run --rm composer update`
- `docker-compose run --rm npm run dev`
- `docker-compose run --rm artisan migrate`

## Установка проекта Laravel

После клонирования или копирования вашего проекта в `./<you project name>/<you project name>`

- Установите зависимости composer `docker-compose run --rm composer update`
- В файле `./<you project name>/<you project name>/.env` настройте подключение к БД и отправку email

```
DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=<you project name>
DB_USERNAME=<you project name>
DB_PASSWORD=secret

MAIL_MAILER=smtp
MAIL_HOST=mailhog
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS=null
```

- Установите ключ приложения `docker-compose run --rm artisan key:generate`
- Выполните миграции `docker-compose run --rm artisan migrate`
- Запустите наполнители `docker-compose run --rm artisan db:seed`
- При необходимости установите зависимости node_modules `docker-compose run --rm npm install`

## phpMyAdmin

Чтобы зайти в интерфейс phpMyAdmin, посетите [localhost:9090] (http://localhost:9090) после запуска `docker-compose up -d <you project name>`.

```
login: root
password: secret
```

## MailHog

Текущая версия Laravel (8 на сегодняшний день) использует MailHog в качестве приложения по умолчанию для тестирования отправки электронной почты и общей работы SMTP во время локальной разработки. Используя предоставленный образ Docker Hub, настроить и подготовить экземпляр очень просто. Служба включена в файл docker-compose.yml и запускается вместе с веб-сервером и службами базы данных.

Чтобы увидеть панель управления и все электронные письма, поступающие через систему, посетите [localhost:8025] (http://localhost:8025) после запуска `docker-compose up -d <you project name>`. 