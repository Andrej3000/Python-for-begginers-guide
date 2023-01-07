[Главная](../../README.md) — [Запуск проекта](../../README.md) — Docker

# Docker

### Предисловие

**Синонимы в контексте статьи:** 
- Хост ≈ ОС ≈ Локальная система ≈ Файловая система  
- Имя ≈ тег (да, в докере путаница не редкость)

**Обозначения:**  
💁 — совет  
ℹ️ — инфо

**Ссылки на официальные доки:**
- [Файл Dockerfile](https://docs.docker.com/engine/reference/builder/)
- [Утилита docker-compose](https://docs.docker.com/compose/compose-file/#build)

### Учебные материалы

**Александр Волочнев — Докер для разработчиков: Основы**
- [Докер для разработчиков: Основы (Занятие 1/4)](https://www.youtube.com/watch?v=fgdcgEcI1KA) — 2 ч 13 мин
- [Докер для разработчиков: Основы (Занятие 2/4)](https://www.youtube.com/watch?v=xtPK-SoB7DY) — 1 ч 46 мин
- [Докер для разработчиков: Основы (Занятие 3/4)](https://www.youtube.com/watch?v=fXixKbuTR3Y) — 2 ч 00 мин
- [Докер для разработчиков: Основы (Занятие 4/4)](https://www.youtube.com/watch?v=39zlGay7NkU) — 2 ч 15 мин

- Материалы по курсу из видео: https://github.com/Entering-IT/Docker-Online-Course

Далее конспект на основе этих четырех видео.

## [↑](#Docker) Установка

**Engine** — версия без графического интерфейса, далее все про нее.

### Docker 

- Быстро, через скрипт (2 команды): https://github.com/docker/docker-install  


      curl -fsSL https://get.docker.com -o get-docker.sh
      sh get-docker.sh

- Вручную:  https://docs.docker.com/engine/install/ubuntu/

- Проверка установки (запуск тестового контейнера)  


      sudo docker run hello-world

### docker-compose (неотъемлемая утилита для докера)

Упрощает работу с контейнерами, настройки которых задаются в текстовом файле формата 
[YAML](https://ru.wikipedia.org/wiki/YAML). Не придется писать километровые команды,
удобно работать сразу с несколькими контейнерами одного проекта, например, запускать.

https://docs.docker.com/compose/install/other/

    curl -SL https://github.com/docker/compose/releases/download/v2.14.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose

💁 Если не запускается:

    sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

💁 Если нет доступа:

    sudo chmod +x /usr/local/bin/docker-compose;

Проверка установки:

    docker-compose version

## [↑](#Docker) Вводная часть

Понятия Образ и Контейнер на примерах 

| Образ — это как        | Контейнер(ы) — это как                                                                                                            |
|------------------------|-----------------------------------------------------------------------------------------------------------------------------------|
| Класс в ООП            | Инстанс(ы) класса                                                                                                                 |
| Дистрибутив приложения | Установленное приложение в системе (1 контейнер) или <br/>несколько с одинаковыми или разными параметрами (несколько контейнеров) |
| Шаблон документа       | Заполненный(е) документ(ы)                                                                                                        |

`python:3.8-slim` — пример названия образа (image), где

- `python` — любое имя  
- `3.8-slim` — это `тег` (метка), как и имя может быть любым, но лучше когда версия `3.8` + что-то об образе `slim` (облегченная)

`[image name]` = `[repository]:[tag]`

Образ можно создать самому на основе другого. Образ можно скачать из репозитория докера.
Образ `python:3.8` — это в образ Линукса `Debian`, в которую добавили версию языка python 3.8

Когда вы запускаете образ, то докер делает его копию (с нужными вам параметрами), запускает копию
и теперь это называется — контейнер. Это и есть все различие.


💁 Удобно задать в Linux alias на `d` командой `alias d='docker'`. Чтобы сохранить alias,
пропишите его в файле `.bashrc` в домашней директории (для Ubuntu).

ℹ️ `docker pull python` = `docker pull python:latest` (`latest` добавляется докером автоматически, 
если тег не указан)

💁 ID контейнера в командах можно писать частично, первыми символами  

💁 После команды можно указать как один, так и несколько названий/ID контейнеров через пробел

💁 Правило очередности в параметрах: **слева — хост, справа — контейнер.**

`-p 127.0.0.1:6379:6379`, где
- `127.0.0.1:6379` — хост
- `6379` — контейнер

`COPY . /project/app/`, где
- `.` — хост
- `/project/app/` — контейнер


## [↑](#Docker) Команды

`docker login`  
`docker logout`  

`docker push`  
`docker pull`

`docker build -t some_name .` — сбилдить/собрать/создать образ (image) с именем `some_name` 
по докерфайлу с именем `Dockerfile` (по умолчанию) из текущей директории `.`, 
указать путь нужно обязательно

`docker run` — запустить контейнер  

`docker stop` — остановить контейнер  
`docker kill` — убить контейнер (аналог kill в Linux)  

`docker rm` — удалить контейнер  
`docker rmi` — удалить образ  

Удалить все скопом: 

`docker container prune`  
`docker image prune`  
`docker network prune`  
`docker volume prune`  

`docker tag old_tag_name new_tag_name` — изменить тег/имя образа

`docker exec -it любая_команда` — выполнить команду в контейнере (`[Ctrl]` + `[d]` — выход из к.)


## [↑](#Docker) Dockerfile

Докерфайл — это файл, по умолчанию с названием `Dockerfile`, в который вы пишите для докера
инструкции по созданию образа.

### Директивы

ℹ️ Команды (директивы) делятся на две части:
- те, которые исполняются в момент билда/создания образа (`FROM`, ..)
- те, которые исполняются в момент запуска контейнера (`CMD`, ..)

`FROM python:3.8-slim` — пример строки с командой/директивой докерфайла

- `FROM` — директива `Создать на основе`  
- `python:3.8-slim` — имя/тег образа

`WORKDIR` — создает папку в контейнере если такой нет и задает ее как основную для всех
последующих команд  
`WORKDIR projects/app/`

`COPY` — копирование из хоста в контейнер  
`COPY . .` — скопировать все из текущей (`.`) директории хоста в текущую (`.`) в контейнере (`WORKDIR`)

`RUN` — выполнить команду в образе  
`RUN pip install --nocache-dir -r requiements.txt` — установить зависимости без кеширования
их (`--nocache-dir` — без сохранения "дистрибутивов" пакетов в контейнере)

`ENV` — переменные окружения  
`ENV USERNAME=Mark PASSWORD=Strong_Pass`


## [↑](#Docker) Общие «папки» между хостом и контейнером, 2 подхода: `bind mounts` и `volume`

️❗️ И `bind mounts` и `volume` запускаются одной командой `--volume`/`-v`

### `bind mounts` — обычная общая папка
Указанная папка внутри контейнера будет недоступна для контейнера (если она имеется), доступно будет то,
что лежит в папке на хосте (локальная информация имеет приоритет)

    docker run --volume LOCAL-PATH:CONTAINER-PATH  

    docker run -v /home/anna/project:/app/:ro (ro — режим readonly)  


    docker run -v $(pwd):/opt/project ($(pwd) — адрес текущей директории в Linux)
    =
    docker run --mount type=bind,source="$(pwd)",target=/opt/project

### `volume` —
Объект докера `volume` будет заполнен данными из папки контейнера
(информация внутри контейнера имеет приоритет), работа с `volume` происходит
с помощью докера.
`volume` можно подключать к контейнеру как флешки к компьютеру.

    docker run --volume VOLUME-NAME:CONTAINER-PATH (VOLUME-NAME — любое название)

    docker run -v project-data:/app/

    docker run --mount source=mysql-data,target=/var/lib/mysql

#### Команды для `volume`

    doker volume create NAME (редко используется, в основном создание происходит в RUN)
    doker volume ls
    doker volume inspect NAME
    doker volume rm NAME

### Резюмируя имеем:

    volume/bind mounts:  docker run -v LOCAL_PATH:CONTAINER_PATH  
    volume/mounts:       docker run -v VOLUME_NAME:CONTAINER_PATH

    volume/bind mounts:  Находятся в вашей файловой системе 
    volume/mounts:       Находятся в территории докера

    volume/bind mounts:  Удобно когда нужно, чтоб доступ был и у других, например, 
                         при работе в команде все подключаются к одной БД,
                         контейнер будет обращаться к папке как к своей и сохранять 
                         в нее файлы БД

    volume/mounts:       Удобно использовать, для локального/личного тестирования,
                         подключить свою версию базы данных для себя, например.


## [↑](#Docker) Отдельная локальная сеть внутри докера, между контейнерами

| Тип сети | Описание                                                                |
|----------|-------------------------------------------------------------------------|
| bridge   | Стандартный тип, с пробросом портов.                                    |
| host     | Контейнер подключен напрямую к сети своего хоста                        |
| overlay  |                                                                         |
| none     | Для контейнеров, которые вообще ни к чему не подключены, порты закрыты. |

❗ При запуске контейнера (команда `run`) и указании имени контейнера `-n`, 
это имя станет сетевым адресом контейнера (как google.com) внутри указанной сети докера и
можно обращаться к этому имени как к доменному имени/IP-адресу.

#### Команды `docker network`
             
    docker network ls

    docker network create NAME

    docker network inspect NAME

    docker network rm NAME

    docker network connect NETWORK_NAME CONTAINER_NAME
    (подключить контейнер к какой-то сети на лету)


## [↑](#Docker) docker-compose

💁 Удобно задать в Linux alias на `dc` командой `alias dc='docker-compose'` Чтобы сохранить alias,
пропишите его в файле `.bashrc` в домашней директории (для Ubuntu).

ℹ️ Имя проекта по умолчанию — это основное имя каталога проекта. Вы можете задать пользовательское имя проекта с 
помощью опции командной строки -p или переменной окружения `COMPOSE_PROJECT_NAME`.

💁 `docker-compose` не должен применяться на серьезном продакшене.

ℹ️ Сеть создается автоматически с именем папки где лежит этот `docker-compose.yaml`

Пример файла `docker-compose.yaml`

    version: '3.9'
    volumes:  # для наглядности, можно не указывать
        wordpress-data: {}  # для наглядности, можно не указывать
        mysql-data: {}  # для наглядности, можно не указывать
    services: 
        wordpress: # Определяет имя в сети, в которой сервисы будут общаться, люое придуманное название (называть по правилам доменных имен)
            image: wordpress:5.5.1-php7.3 # образ
            volumes: 
                - wordpress-data:/var/www/html # Монтируем volume wordpress-data в папку контейнера /var/www/html 
            depends_on: # Wordpress зависит от БД, он будет ждать пока стартанет контейнер database
                        # (именно контейнер, старт самой БД в контейнере ждать не будет)
                - database
            ports: # Внутренний порт контейнера 80 на 8080 хоста
                - "8000:80"
            restart: on-failure # Что делать если контейнер грохнется
                                # restart: "no" — по умолчанию, никогда не перезапускаться 
                                # restart: always — перезапускать в любом случае (не рекомендуется, все время будет перезапускаться)
                                # restart: on-failure — перезапустить если грохулся из-за ошибки (не рекомендуется, ведь на сервисе есть проблема значит)
                                # restart: unless-stopped
            environment:
                WORDPRESS_DB_HOST: database:3306
                WORDPRESS_DB_USER: wordpress
                WORDPRESS_DB_PASSWORD: wordpress 
                WORDPRESS_DB_NAME: wordpress
        database: 
            image: mysql:5.7 
            volumes: 
                - mysql-data:/var/lib/mysql 
            restart: on-failure 
            environment: 
                MYSQL_ROOT_PASSWORD: secretpassword
                MYSQL_DATABASE: wordpress
                MYSQL_USER: wordpress
                MYSQL_PASSWORD: wordpress

### Команды 

`docker-compose up` — запуск  
`docker-compose up -d` — запуск в фоне  

Если вместо `image: wordpress:5.5.1-php7.3` задать `build: ссылка_на_папку`, то 
`docker-compose build` — сбилдит билды

`docker-compose logs [-f] [your_service_name]` — то же самое, что docker logs (не имя контейнера, а имя сервиса в docker compose)

`docker-compose exec your_service_name любая_команда` — выполнить команду `любая_команда`, при этом добавляются параметры `-it` автоматически  

`docker-compose stop [your_service_name]`  

`docker-compose start [your_service_name]` — если указать сервис, который находится в зависимости `depends_on`, 
то запустится и тот, от которого зависимость  

`docker-compose restart [your_service_name]`  

`docker-compose kill [your_service_name]`  

`docker-compose down` — зачистка, удаляет сеть и контейнеры, volumes не удаляет  


## [↑](#Docker) Файлы переменных окружения

ℹ️ [Синтаксис переменных в docker-compose](https://docs.docker.com/compose/compose-file/#environment) 

Нужны для удобства, например, чтоб быстро сменить одни параметры контейнера на другие

~~❗ По умолчанию `docker-compose` будет использовать файл `.env`, 
даже если тот не прописан (_Convention over configuration_)~~

❗ Добавляйте файл `.env` в `.gitignore`

Параметр `env_file`

`docker-compose.yaml`:

    version: '3'
    services:
      generator:
        build: .
        environment:
          KILLRVIDEO_YOUTUBE_API_KEY2: "2wefwv435tgweg"
        env_file:
          - ./dev.env

`dev.env`:

          KILLRVIDEO_YOUTUBE_API_KEY="23423f4dfgfsdgrt34t3gweg"
          KILLRVIDEO_LOGGING_LEVEL=debug


## [↑](#Docker) На практике

💁 В подавляющем большинстве случаев должно быть так:  

    git clone
    docker-compose up -d


ℹ️ 2 сценария работы с приложением:
1. только используем — мы хотим запустить его и потыкать
2. разработка — нам нужно что-то менять

💁 Пример работы команды над приложением (фронтенд, бэкенд, БД) с докером.
- фронтендщик — бэкенд и БД в качестве образов
- бэкендщик — фронтенд и БД в качестве образов

💁 Пример docker-compose файла для 2-х сценариев.  

    $(pwd)/generator — место, где лежит мой локальный код

    1.
    services: 
        generator:
            # image: killrvideo/killrvideo-generator
            build: ./generator
        volumes:
            - "$(pwd)/generator":opt/killrvideo-generator

    2.
    services: 
        generator:
            image: killrvideo/killrvideo-generator
            # build: ./generator
        volumes:
            # - "$(pwd)/generator":opt/killrvideo-generator

💁 Докер-файлы должны билдиться на сервере автоматизации (для крупных проектов)

💁 Пример с переменными окружения

- Название переменных окружения начинаются с названия проекта
(чтоб не запутаться)

- `KILLRVIDEO_LOGGING_LEVEL: debug` - раскомменчен, по дефолту 
может быть `WARNING` или `ERROR`

- `command` — переопределение команды из докер-файла


    version: '3'
    services:
      # The KillrVideo Sample Data Generator
      generator:
        build: .
        #image: killrvideo/killrvideo-generator
        volumes:
          - .:/opt/killrvideo-generator
        ports:
          - "5858:5858"
        depends_on:
          - dse
          - backend
        environment:
          KILLRVIDEO_YOUTUBE_API_KEY: SET_YOUR_YOUTUBE_KEY_HERE
          KILLRVIDEO_LOGGING_LEVEL: debug
        command: "node --debug /opt/killrvideo-generator/dist/index.js"

ℹ️ На деплое в продакшене в не пет-проектах не используют docker-compose, а,
например, Kubernetes или OpenShift

💁 Smoke Testing (включить и если не задымился, то ОК) — просто запустить контейнер

## [↑](#Docker) На практике — Debug

(зависит от языка, IDE и т.п.)


## [↑](#Docker) На практике — Autobuild (CI/CD)

- Jenkins
- Travis CI
- Github Actions (бесплатно для opensource) 👍
- and more...

💁 `git tag` должен быть такой же, как и `docker tag`


## [↑](#Docker) На практике — ошибки

Changeset

    ❌
    RUN pip install redis
    RUN pip install requests

    ✅
    RUN pip install redis && pip install requests

    ✅✅✅
    RUN apt update && \
        pip3 install --no-cache-dir -r requirements.txt


## [↑](#Docker) На практике — многоэтапные билды

(голанг, джава...)

## [↑](#Docker) На практике — Labels

В крупных проектах помогает понять, что за образ/контейнер (это просто комментарии к образу, 
метаинформация)

- Repository
- Maintainer / Team
- Build Number / Link
- Build Date
- Git Commit Hash
- Etc.


## [↑](#Docker) На практике — Multi-Process Containers (когда сервис состоит из двух+ процессов)

❗ Избегать, но если надо, то 
https://github.com/just-containers/s6-overlay


## [↑](#Docker) Используйте лимиты

- Non-Root User ❤️
- Limit Memory and Swap
- Use :ro read-only volumes/mounts ❤️
- Bash strict mode set -euo pipefail ❤️
- Avoid --privileged, use --cap-add instead


## [↑](#Docker) Линтер для Dockerfile

https://hadolint.github.io/hadolint/

## [↑](#Docker) CMD vs ENTRYPOINT


### Как "работает" `CMD` (кладете то, что меняется часто)

    FROM python:3-alpine
    COPY . .
    CMD ["flask", "run", "--host=0.0.0.0", "--port=80"]

`docker run your_container_id ` = `docker run your_container_id flask run --host=0.0.0.0 --port=80`

### Как "работает" `ENTRYPOINT`  (кладете то, что меняется редко)

    FROM python:3-alpine
    COPY . .
    ENTRYPOINT ["flask", "run", "--host=0.0.0.0", "--port=80"]

`docker run your_container_id ` =  `docker run your_container_id flask run --host=0.0.0.0 --port=80`

### Как они "работают" вместе

    FROM python:3-alpine
    COPY . .
    CMD ["--host=0.0.0.0", "--port=80"]
    ENTRYPOINT ["flask", "run"]

`docker run your_container_id` = `docker run your_container_id ENTRYPOINT+CMD`

`docker run your_container_id ` = `docker run your_container_id flask run --host=0.0.0.0 --port=80`

Переопределяется только `CMD`  
`docker run your_container_id flask run --host=12.12.12.12 --port=80` = `docker run your_container_id --host=12.12.12.12`  

Переопределить `ENTRYPOINT` с `["flask", "run"]` на `["flask", "routes"]`  
`docker run --entrypoint="flask" your_container_id  routes`  


[↑ Наверх](#Docker) 

