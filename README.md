# Домашнее задание к занятию 3. «Введение. Экосистема. Архитектура. Жизненный цикл Docker-контейнера» - Балдин Алексей

## Задача 1

Сценарий выполнения задачи:

- создайте свой репозиторий на https://hub.docker.com;
- выберите любой образ, который содержит веб-сервер Nginx;
- создайте свой fork образа;
- реализуйте функциональность:
запуск веб-сервера в фоне с индекс-страницей, содержащей HTML-код ниже:
```
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I’m DevOps Engineer!</h1>
</body>
</html>
```

Опубликуйте созданный fork в своём репозитории и предоставьте ответ в виде ссылки на https://hub.docker.com/username_repo.

## Решение:

- *Сначала стягиваю образ nginx командой ```docker pull nginx```*

- *Проверяю наличие на хосте нужного образа:* 

![DOCKER](images/1.jpg)

- *В нашем случае нет необходимости подкладывать в контейнер измененный файл индекс-страницы. Код небольшой. Думаю, проще внести изменения так:* 

```
#берем за основу скачанный образ nginx 
FROM nginx
#меняем содержимое индекс-страницы. И тут, кстати, апостроф некорректно отображается. Поэтому я его убрал.
RUN echo '<html><head>Hey, Netology</head><body><h1>I am DevOps Engineer!</h1></body></html>' > /usr/share/nginx/html/index.html
```

- *Собираю свой образ nginx: ```docker build -f Dockerfile -t baldinlex/nginx:2.0 .``` и запускаю его (на порту, допустим, 8000): ```docker run -p 8000:80 baldinlex/nginx:2.0```:*

![DOCKER](images/2.jpg)

[Ссылка на репозиторий](https://hub.docker.com/repository/docker/baldinlex/nginx/general)

---

## Задача 2

Посмотрите на сценарий ниже и ответьте на вопрос:
«Подходит ли в этом сценарии использование Docker-контейнеров или лучше подойдёт виртуальная машина, физическая машина? Может быть, возможны разные варианты?»

Детально опишите и обоснуйте свой выбор.

--

Сценарий - ***и тут же ответы по кейсам***:

- высоконагруженное монолитное Java веб-приложение - ***В этом случае, думаю, надо сделать акцент на оптимальную утилизацию резурсов. Поэтому отдал бы предпочтение физической машине (не надо тратить мощности на гипервизор, обслуживание виртуалок и т.д.);***
- Nodejs веб-приложение - ***Тут Docker 100% подойдет. С одной стороны он дает возможность развернуть среду со всеми зависимостями, а с другой обеспечит необходимую масштабируемость;***
- мобильное приложение c версиями для Android и iOS - ***В этом случае надо выбрать ВМ. Всё, что я нашел на просторах интернета про запуск iOS на Docker, так это различные ~~танцы с бубнами~~ эмуляторы. Думаю, что такая разработка будет неприятным процессом. Да и как в продакшене будет всё это дело работать, неизвестно;***
- шина данных на базе Apache Kafka - ***Думаю, да. Docker отлично подойдет. Он обеспечит нужную масштабируемость, изолированность сервисов, а также дает возможность отката на предыдущую версию. Как говорится "то, что доктор прописал". Образы на докерхаб есть готовые.***;
- Elasticsearch-кластер для реализации логирования продуктивного веб-приложения — три ноды elasticsearch, два logstash и две ноды kibana - ***Да, Docker подойдет. МОжно и на ВМ развернуть, но с Docker это быстрее будет***;
- мониторинг-стек на базе Prometheus и Grafana - ***Да, Docker подойдет. Нагуглил готовые решения.***;
- MongoDB как основное хранилище данных для Java-приложения - ***Docker может подойти. Готовые решения есть. Образ на докерхаб имеется. Но как основное хранилище данных... может всё-таки лучше железную машину для этого взять.***;
- Gitlab-сервер для реализации CI/CD-процессов и приватный (закрытый) Docker Registry - ***Тут я затрудняюсь между ВМ и Docker... Думаю, что выбирать надо именно между ними, так как физическая машина не даст нужной изолированности приложений.***.

---

## Задача 3

- Запустите первый контейнер из образа ***centos*** c любым тегом в фоновом режиме, подключив папку ```/data``` из текущей рабочей директории на хостовой машине в ```/data``` контейнера.
- Запустите второй контейнер из образа ***debian*** в фоновом режиме, подключив папку ```/data``` из текущей рабочей директории на хостовой машине в ```/data``` контейнера.
- Подключитесь к первому контейнеру с помощью ```docker exec``` и создайте текстовый файл любого содержания в ```/data```.
- Добавьте ещё один файл в папку ```/data``` на хостовой машине.
- Подключитесь во второй контейнер и отобразите листинг и содержание файлов в ```/data``` контейнера.

## Решение:

- *Командой ```docker run -v /data:/data --name centos-os -dt centos``` стягиваю образ centos, запускаю в фоне контейнер с именем ```centos-os```, а заодно создаю volume*
- *Почти той же командой запускаю его близнеца с образа debian: ```docker run -v /data:/data --name debian-os -dt debian```
- *Убеждаюсь, что оба контейнера запущены: docker ps*

![DOCKER](images/3.jpg)

- *Создаю файл в контейнере centos-os ```docker exec centos-os /bin/bash -c "echo hello world! > /data/centos_file"```. Сразу проверю, что файл появился и в соответствующей директории хостовой машины:

![DOCKER](images/4.jpg)

- *Далее создаю файл в той же директории из хостовой машины. И наконец проверяю наличие созданных файлов из контейнера debian-os:*  

![DOCKER](images/5.jpg)

---

## Задача 4 (*)

Воспроизведите практическую часть лекции самостоятельно.

Соберите Docker-образ с Ansible, загрузите на Docker Hub и пришлите ссылку вместе с остальными ответами к задачам.

