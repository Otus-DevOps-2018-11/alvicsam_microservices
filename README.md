# alvicsam_microservices
alvicsam microservices repository

### hints

Исправление ошибки " Unable to query docker version: Get https://34.76.63.196:2376/v1.15/version: x509: certificate is valid for ip_addr, not other_ip_addr"  
`docker-machine regenerate-certs docker-host`

### ДЗ №12 Введение в Docker

Установлены Docker, docker-compose, docker-machine  
Запущен `docker run hello-world`  
Запущен `docker run -it ubuntu:16.04 /bin/bash` с созданием файла в одном из контейнеров и убедился, что он отсутствует в следующем
Найден старый контейнер `docker ps -a`  
Подключился к старому контейнеру `docker start 7dafa500cceb && docker attach 7dafa500cceb`  
Создал образ на основе этого контейнера `docker commit 7dafa500cceb alvic/ubuntu-tmp-file`  
Сохранил вывод команды docker images в файл docker-monolith/docker-1.log  

### ДЗ №13 Docker под капотом

Создан новый проект в gce с именем docker  
Экспортирована переменная с id проекта `export GOOGLE_PROJECT=docker-######`  
Создана вм в gcp с помощью docker-machine  
```bash
$ docker-machine create --driver google \
--google-machine-image https://www.googleapis.com/compute/v1/projects/ubuntu-os-cloud/global/images/family/ubuntu-1604-lts \
--google-machine-type n1-standard-1 \
--google-zone europe-north1-b \
docker-host
```
Добавлены Dockerfile и дополнительные файлы для создания образа  
Создан образ  
Запущен контейнер `docker run --name reddit -d --network=host reddit:latest`  
Разрешён входящий TCP-трафик на порт 9292:
```bash
$ gcloud compute firewall-rules create reddit-app \
--allow tcp:9292 \
--target-tags=docker-machine \
--description="Allow PUMA connections" \
--direction=INGRESS
```
Проверено, что приложение стало доступным  
Аутентифицирован на docker hub  
Образ помечен тегом `docker tag reddit:latest alvicsam/otus-reddit:1.0`  
Образ загружен в docker.hub `docker push alvicsam/otus-reddit:1.0`  
Загруженный контейнер запущен локально `docker run --name reddit -d -p 9292:9292 alvicsam/otus-reddit:1.0`  
