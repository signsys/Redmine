# Redmine
_Redmine 설치 및 관리_

Docker 이미지(Redmine, MySQL) 다운로드
~~~
$ sudo docker pull redmine
$ sudo docker pull mysql
~~~
Docker 및 Redmine 디렉토리 생성
~~~
$ cd /srv
$ sudo mkdir docker
$ cd docker
$ sudo mkdir redmine
$ sudo chmod 757 redmine
~~~

docker-compose.yml 작성
~~~
$ sudo vi docker-compose.yml
~~~
~~~
version: '3.2'

services:
     redmine:
          image: redmine
          restart: always
          container_name: redmine
          ports:
               - 3000:3000
          volumes:
               - ./redmine/files:/usr/src/redmine/files
               - ./redmine/plugins:/usr/src/redmine/plugins
               - ./redmine/themes:/usr/src/redmine/public/themes
          environment:
               REDMINE_DB_MYSQL: db
               REDMINE_DB_PASSWORD: redmine
               REDMINE_DB_DATABASE: redmine
               REDMINE_DB_ENCODING: utf8
     db:
          image: mysql
          restart: always
          ports:
               - 3306:3306
          volumes:
               - ./redmine/db:/var/lib/mysql
          environment:
               MYSQL_ROOT_PASSWORD: redmine
               MYSQL_DATABASE: redmine
          command:
               - --character-set-server=utf8mb4
               - --collation-server=utf8mb4_unicode_ci
~~~
docker container 생성 및 기동
~~~
$ sudo docker-compose up -d
$ sudo docker ps -a
~~~
docker container 접근
~~~
$ sudo docker exec -it redmine bash
$ sudo docker exec -it docker_db_1 bash
~~~
SMTP 설정파일(configuration.yml) 수정 및 복사 (삽질하지 말고 Gmail)
~~~
$ cd /srv/docker
$ sudo docker cp redmine:/usr/src/redmine/config/configuration.yml.example ./configuration.yml
$ sudo vi configuration.yml
$ sudo docker cp ./configuration.yml redmine:/usr/src/redmine/config/
$ sudo docker-compose restart
~~~
기본 데이터 입력
~~~
$ sudo docker exec -it redmine bash
# rake redmine:load_default_data RAILS_ENV=production
~~~
