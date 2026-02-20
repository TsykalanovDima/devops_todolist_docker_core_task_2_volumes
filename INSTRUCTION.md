# INSTRUCTION

> Replace `dtsy` with your own Docker Hub username if needed.

---

## 1. Build MySQL image from Dockerfile.mysql

```bash
docker build -f Dockerfile.mysql -t mysql-local:1.0.0 .
```

---

## 2. Push MySQL image to Docker Hub (mysql-local repository)

```bash
docker login
docker tag mysql-local:1.0.0 dtsy/mysql-local:1.0.0
docker push dtsy/mysql-local:1.0.0
```

---

## 3. Run MySQL container with a volume attached

Create volume:

```bash
docker volume create mysql_data
```

Run MySQL container:

```bash
docker run -d \
  --name mysql-db \
  -v mysql_data:/var/lib/mysql \
  -p 3306:3306 \
  dtsy/mysql-local:1.0.0
```

---

## 4. Get MySQL container IP address

```bash
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' mysql-db
```

Copy the printed IP address and update `todolist/settings.py`:

```python
DATABASES = {
    'default': {
        'ENGINE': 'mysql.connector.django',
        'NAME': 'app_db',
        'USER': 'app_user',
        'PASSWORD': '1234',
        'HOST': 'MYSQL_CONTAINER_IP',
        'PORT': '',
    }
}
```

> After changing `todolist/settings.py`, you must rebuild the Django image so the container includes the updated configuration.

---

## 5. Build Django application image (todoapp:2.0.0)

```bash
docker build -t todoapp:2.0.0 .
```

---

## 6. Push Django application image to Docker Hub

```bash
docker tag todoapp:2.0.0 dtsy/todoapp:2.0.0
docker push dtsy/todoapp:2.0.0
```

---

## 7. Run the application container

```bash
docker run -d \
  --name todo-app \
  -p 8080:8080 \
  dtsy/todoapp:2.0.0
```

---

## 8. Docker Hub repositories

MySQL image:
https://hub.docker.com/r/dtsy/mysql-local

Application image:
https://hub.docker.com/r/dtsy/todoapp

---

## 9. Access the application in browser

Open:
http://localhost:8080/

API endpoint:
http://localhost:8080/api/