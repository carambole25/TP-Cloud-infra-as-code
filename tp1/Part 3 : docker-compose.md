🌞 Installez un WikiJS en utilisant Docker

oui

🌞 Call me when it's done

Validé.

3. Make your own meow
🌞 Application python

```
azureuser@vmdetest:~/app_python$ ls
Dockerfile  app.py  docker-compose.yml  requirements.txt  templates
azureuser@vmdetest:~/app_python$ cat Dockerfile 
FROM python:3

WORKDIR /usr/src/app

COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

COPY app.py ./
RUN mkdir templates
COPY templates/index.html ./templates/index.html

CMD [ "python", "./app.py" ]
azureuser@vmdetest:~/app_python$ cat docker-compose.yml 
version: '2'
services:
  web:
    build: .
    ports:
      - "80:8888"
    depends_on:
      - db
  db:
    image: redis
    expose:
      - "6379"
```

```
curl http://20.197.17.211/
<h1>Add key</h1>
<form action="/add" method = "POST">

Key:
<input type="text" name="key" >

Value:
<input type="text" name="value" >

<input type="submit" value="Submit">
</form>

<h1>Check key</h1>
<form action="/get" method = "POST">

Key:
<input type="text" name="key" >
<input type="submit" value="Submit">
</form>

Host : a4487c11cb0a%
```