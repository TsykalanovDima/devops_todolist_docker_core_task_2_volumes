# INSTRUCTION

## 1. Run MySQL container with a volume attached

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

## 2. Get MySQL container IP address

```bash
docker inspect mysql-db | grep IPAddress
```

Copy the IP address and update `todolist/settings.py`:

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

Replace `MYSQL_CONTAINER_IP` with the actual IP.

---

## 3. Build the Django application image

```bash
docker build -t todoapp:2.0.0 .
```

---

## 4. Run the application container

```bash
docker run -d \
  --name todo-app \
  -p 8080:8080 \
  dtsy/todoapp:2.0.0
```

---

## 5. Docker Hub repository

MySQL image:  
https://hub.docker.com/r/dtsy/mysql-local

Application image:  
https://hub.docker.com/r/dtsy/todoapp

---

## 6. Access the application in browser

Open:

http://localhost:8080/

API endpoint:

http://localhost:8080/api/