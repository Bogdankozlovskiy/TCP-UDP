
UDP
===


```python
#server
import socket

server = socket.socket(
    socket.AF_INET,           # IPv4
    socket.SOCK_DGRAM         # UDP
    )
server.bind(('localhost', 8888))
result = server.recv(1024)
print(result.decode())
server.close()
```

    hello world
    


```python
#client
import socket
client = socket.socket(
    socket.AF_INET,           # IPv4
    socket.SOCK_DGRAM         # UDP
    )
client.sendto(b"hello world", ('localhost', 8888))
```




    11



loop
===


```python
#server
import socket

server = socket.socket(
    socket.AF_INET,           # IPv4
    socket.SOCK_DGRAM         # UDP
    )
server.bind(('localhost', 8888))

while True:
    try:
        result = server.recv(1024)
    except KeyboardInterrupt:
        server.close()
        break
    else:
        print(result.decode())
```

    hello world
    hello world
    hello world
    hello world
    hello world
    hello world
    hello world
    

UNIX FILE
===


```python
#server
import os
import socket

UNIX_SOCKET_NAME = "UNIX.sock"

server = socket.socket(
    socket.AF_UNIX,           # USE file socket not available in windows(
    socket.SOCK_DGRAM         # UDP
    )

if os.path.exists(UNIX_SOCKET_NAME):
    os.remove(UNIX_SOCKET_NAME)
    
server.bind(UNIX_SOCKET_NAME)
```


```python
#client
import socket

UNIX_SOCKET_NAME = "UNIX.sock"

client = socket.socket(
    socket.AF_UNIX,           # USE file socket not available in windows(
    socket.SOCK_DGRAM         # UDP
    )

client.sendto(b"hello world", UNIX_SOCKET_NAME)
```

TCP
===


```python
#server
import socket

server = socket.socket(
    socket.AF_INET,           # IPv4
    socket.SOCK_STREAM         # UDP
    )
server.bind(('localhost', 8889))
server.listen(5)   # set length of queue
server.setblocking(True) #блокировать сервер на момент ожидания подключения?
                         #в не блокирующем режиме(False) чтение вызывает исклчение socket.error
server.settimeout(5)     # 5 сек ожидать подключение иначе вызывает исклчение socket.error
#server.settimeout(0) -> server.setblocking(False)
#server.settimeout(None) -> server.setblocking(True)
    
client, addr = server.accept()
print(f'accept client by addr {addr}')

result = client.recv(1024)
print(result.decode())
client.close()
server.close()
```

    accept client by addr ('127.0.0.1', 51605)
    hello world
    


```python
#client
import socket

client = socket.socket(
    socket.AF_INET,           # IPv4
    socket.SOCK_STREAM         # UDP
    )
client.connect(('localhost', 8889))
client.send(b"hello world")
```

***


```python
import socketserver
```

***

HTTP
===


```python
#client
import socket
client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
client.connect(('185.227.99.68', 80))

content_items = ['GET / HTTP/1.1', 
                 'Host: 185.227.99.68',
                 'Connection: keep-alive',
                 'Accept: text/html',
                 '\n']
content = '\n'.join(content_items)
client.send(content.encode())
result = client.recv(1024)
result = result.decode()
print(result)
```

    HTTP/1.1 200 OK
    Server: nginx/1.17.6
    Date: Sun, 19 Jan 2020 11:16:26 GMT
    Content-Type: text/html; charset=utf-8
    Content-Length: 2364
    Connection: keep-alive
    X-Frame-Options: SAMEORIGIN
    
    <head>
        <title>My Django Project</title>
        <link href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" rel="stylesheet">
    </head>
    
    <body>
        <center>
            <div class="jumbotron">
                <h2>Hello World!!</h2>
            </div>
            <p>This Project is developed using
                <b>Django 2.2</b>.
            </p>
            <p>This page is being served through Gunicorn. You can access the
                <a href='/admin'>admin site</a> as well.</p>
    
            <p>Static contents are being served through Nginx.</p>
            <p>Postgres is used as database for this Project.</p>
            <p>Please check django logs in shell using command
                <b>make shell-web</b>.</p>
            <p>There you will see
                <b>Printing Hello from Celery</b>, which is printed from celery method.</p>
            <p>
    

Urlib
===


```python
from urllib import request

response = request.urlopen('http://185.227.99.68/')
print(response.status)
print(response.getcode())
print(response.msg)
print(response.reason)
print(response.headers)
print(response.getheaders())
```

    200
    200
    OK
    OK
    Server: nginx/1.17.6
    Date: Sun, 15 Dec 2019 23:17:38 GMT
    Content-Type: text/html; charset=utf-8
    Content-Length: 2364
    Connection: close
    X-Frame-Options: SAMEORIGIN
    
    
    [('Server', 'nginx/1.17.6'), ('Date', 'Sun, 15 Dec 2019 23:17:38 GMT'), ('Content-Type', 'text/html; charset=utf-8'), ('Content-Length', '2364'), ('Connection', 'close'), ('X-Frame-Options', 'SAMEORIGIN')]
    

***

requests
===


```python
from requests import get, put, post, delete
response = get('http://185.227.99.68/')
print(response.status_code)
print(response.text)
```

    200
    <head>
        <title>My Django Project</title>
        <link href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" rel="stylesheet">
    </head>
    
    <body>
        <center>
            <div class="jumbotron">
                <h2>Hello World!!</h2>
            </div>
            <p>This Project is developed using
                <b>Django 2.2</b>.
            </p>
            <p>This page is being served through Gunicorn. You can access the
                <a href='/admin'>admin site</a> as well.</p>
    
            <p>Static contents are being served through Nginx.</p>
            <p>Postgres is used as database for this Project.</p>
            <p>Please check django logs in shell using command
                <b>make shell-web</b>.</p>
            <p>There you will see
                <b>Printing Hello from Celery</b>, which is printed from celery method.</p>
            <p>Redis was used as broker for Celery.</p>
    
            <p class="lead">Enjoy your boilerplate for Django(with celery, postgresql and redis) and Docker.</p>
            
            <h2>Here are some demo contents from Demo model</h2>
            
            <h3>Rick</h3>
            <p>В центре сюжета - школьник по имени Морти и его дед Рик. Морти - самый обычный мальчик, кой ничем не различается от собственных ровесников. А вот его дедуля занимается необычными научными исследованиями и зачастую полностью неадекватен. Он может в любое время дня и ночи схватить внука и отправиться вместе с ним в межпространственные приключения с помощью построенной из разного хлама летающей тарелки, которая способна перемещаться сквозь временной тоннель. Каждый раз эта парочка оказывается в самых неожиданных местах и самых нелепых ситуациях.</p>
            <img src="/media/demo_images/1503056990-1097093764-rik-i-morti-KINOGO_BY.jpg">
            
            
            </div>
    
        </center>
    </body>
    
    <footer>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js"></script>
    </footer>
    

***


```python
from requests import get, put, post, delete
from json import dumps
from bs4 import BeautifulSoup

data = {'username':'bogdan','password':'gd0d46s'}
headers = {'Content-Type':'application/json'}

response = post('http://185.227.99.68/admin/login/', data=dumps(data), headers=headers)

print(response.status_code)
print(BeautifulSoup(response.text))
```

    403
    <!DOCTYPE html>
    <html lang="en">
    <head>
    <meta content="text/html; charset=utf-8" http-equiv="content-type"/>
    <meta content="NONE,NOARCHIVE" name="robots"/>
    <title>403 Forbidden</title>
    <style type="text/css">
        html * { padding:0; margin:0; }
        body * { padding:10px 20px; }
        body * * { padding:0; }
        body { font:small sans-serif; background:#eee; color:#000; }
        body>div { border-bottom:1px solid #ddd; }
        h1 { font-weight:normal; margin-bottom:.4em; }
        h1 span { font-size:60%; color:#666; font-weight:normal; }
        #info { background:#f6f6f6; }
        #info ul { margin: 0.5em 4em; }
        #info p, #summary p { padding-top:10px; }
        #summary { background: #ffc; }
        #explanation { background:#eee; border-bottom: 0px none; }
      </style>
    </head>
    <body>
    <div id="summary">
    <h1>Forbidden <span>(403)</span></h1>
    <p>CSRF verification failed. Request aborted.</p>
    <p>You are seeing this message because this site requires a CSRF cookie when submitting forms. This cookie is required for security reasons, to ensure that your browser is not being hijacked by third parties.</p>
    <p>If you have configured your browser to disable cookies, please re-enable them, at least for this site, or for 'same-origin' requests.</p>
    </div>
    <div id="explanation">
    <p><small>More information is available with DEBUG=True.</small></p>
    </div>
    </body>
    </html>
    
    


```python
from tqdm import tqdm, tqdm_notebook
from time import sleep
```


```python
for i in tqdm_notebook(range(100), desc="hello"):
    for i in tqdm_notebook(range(10), desc="world", leave=False):
        sleep(0.1)
```


    HBox(children=(IntProgress(value=0, description='hello', style=ProgressStyle(description_width='initial')), HT…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…



    HBox(children=(IntProgress(value=0, description='world', max=10, style=ProgressStyle(description_width='initia…


    
    


```python
import functools
import time

# кэширование до 12 различных результатов
@functools.lru_cache(maxsize=12)
def slow_func(x):
    time.sleep(2)  # Имитируем длительные вычисления
    return x

slow_func(1)  # ... ждём 2 секунды до возврата результата
slow_func(1)  # результат уже кэширован - он возвращается немедленно!

slow_func(3) 
```




    3




```python
slow_func(2)
```




    2


