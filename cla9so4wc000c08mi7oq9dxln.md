# Dockerize an Django Based app along Postgres + Redis + ElasticSearch 🚀

Docker is a wonderful tool to containerise your website. A website have many micro services & inter dependent in most of the cases. Things even get more worse when we try to setup our app on different PC / Operating System. Docker is a lifesaver in this situation and solves the very popular problem which almost every programmer faces when shipping the code to another platform. 

![4WwcmWUBCXmZ4iV0J_esaqE7O_GVYjON52dbVkEv5_c-2849584195.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1668005656296/2JCAvjdH5.jpg align="left")

🚧 Before Moving ahead, you must have a basic knowledge of Docker & Docker Compose.

Here i am going to use my already built django based app [Movie Api](https://github.com/selftaughtdev-me/django-flix-video-streaming) which is using Postgres, Redis & ElasticSearch.

I will use a Docker compose file to keep every service in order as we can only starts our django server after postgres is ready and we can only starts elastic search after redis is ready. so these services are interdependent & I am going to link them properly so that they can start in an order, rather than just running independently and breaking things.

Before I start Explaining, have a look at my final Docker compose file.

![AwLookGIF.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1668006317898/Q3AenYKgb.gif align="left")

```python
version: '3.8'

services:
  django_server:
    build: .
    working_dir: /usr/src/app
    command: >
      sh -c "python manage.py wait_for_db &&
            python manage.py wait_for_elk &&
            python manage.py migrate &&
            apt install -y curl &&
            echo 'sending curl to Elastic hosts' &&
            curl -X GET ${ELK_CONNECTION_PROTOCOL}://${ELK_HOST}:${ELK_PORT} &&
            python manage.py generate_test_data 1000 &&
            gunicorn src.wsgi:application --bind 0.0.0.0:8000"
    ports:
      - 8000:8000
    env_file: .env
    depends_on:
      - elasticsearch7
      - postgresql_db
    container_name: django_server

  elasticsearch7:
    image: elasticsearch:7.17.5
    ports:
      - ${ELK_PORT}:${ELK_PORT}
      - 9300:9300
    container_name: ${ELK_HOST}
    restart: always
    environment:
      - discovery.type=single-node
      - node.name=django_flix
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"

  postgresql_db:
    image: postgres
    ports:
      - 5432:5432
    restart: always
    env_file: .env
    container_name: ${POSTGRES_HOST}


```

In above Code, I have added `depends_on` to make services wait for another one before starting up. I have not very good understanding of docker, so i don't know why the django server always get started before even fully starting the dependent services. So, to overcame this issue i made a custom command in django which stops the server until the Database and ElasticSearch are fully started. 

here is code for both of the files where i write custom commands.

```python
"""
Django Command to wait for DB to be available
"""
import time

from django.core.management import BaseCommand
from django.db.utils import OperationalError as DjangoDbUtilsOperationalError
from psycopg2 import OperationalError as Psycopg2OperationalError


class Command(BaseCommand):
    """Django command to pause execution until database is available"""

    def handle(self, *args, **options):
        self.stdout.write("Waiting for database...")
        db_is_up = False
        while not db_is_up:
            try:
                # try to connect with db
                self.check(databases=["default"])
                db_is_up = True
                self.stdout.write(self.style.SUCCESS("Database is available"))
            except (Psycopg2OperationalError, DjangoDbUtilsOperationalError):
                self.stdout.write(
                    self.style.WARNING(
                        "Database is still not ready. will try in 1 second"
                    )
                )
                time.sleep(1)
```

And this one below is for waiting for elastic search to start.

```python
"""
Django Command to wait for DB to be available
"""
import time

from django.conf import settings
from django.core.management import BaseCommand
from elasticsearch import Elasticsearch


class Command(BaseCommand):
    """Django command to pause execution until database is available"""

    def handle(self, *args, **options):
        self.stdout.write("Waiting for ElasticSearch...")
        elk_is_up = False
        host = settings.ELK_HOST
        port = settings.ELK_PORT
        while not elk_is_up:
            try:
                # try to connect with db
                self.stdout.write(
                    self.style.WARNING(
                        "trying to connect with elk at {}:{}".format(host, port)
                    )
                )
                es = Elasticsearch([{"host": host, "port": port}])
                es.cluster.health(wait_for_status="yellow", request_timeout=1)
                self.stdout.write(self.style.SUCCESS("✅ ElasticSearch is available"))
                elk_is_up = True
            except Exception:
                self.stdout.write(
                    self.style.WARNING(
                        "❌ ElasticSearch is still not ready. will try in 5 second"
                    )
                )
                time.sleep(5)
```
I also uses a enviroment file so that i can pass these values to docker compose file instead of defining them in the key value pair in docker compose file.

```bash
export DEBUG=1
export POSTGRES_DB="postgres"
export POSTGRES_USER="postgres"
export POSTGRES_PASSWORD="postgres"
export POSTGRES_HOST="postgresql_db_latest"
export POSTGRES_PORT=5432
export ES_JAVA_OPTS="-Xms8g -Xmx8g"
export ELK_HOST="elasticsearch_v7"
export ELK_PORT="9200"
export ELK_INDEX_NAME="django_flix"
export ELK_CONNECTION_PROTOCOL="http"

```

This way I was able to link these services and starts them in a order.

Helpful Links

- [Django](https://www.djangoproject.com/)
- [ElasticSearch 7 Installation guide](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-elasticsearch-on-ubuntu-20-04)
- [Django Custom Commands](https://docs.djangoproject.com/en/4.1/howto/custom-management-commands/)
- [Docker Compose](https://docs.docker.com/compose/)
- [Link to Code on GitHub](https://github.com/selftaughtdev-me/django-flix-video-streaming)

Disclaimer - I am not an expert & still learning. So, there may be some things which i may missed out or may be wrongly explained. As I got any comment about mistakes or figured out somewhere then i will correct it in blog. 
