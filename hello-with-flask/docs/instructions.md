Vamos organizar o projeto conforme a sugestão da mentoria:

### 1. Estrutura do Projeto

Crie a seguinte estrutura de diretórios para o projeto:

```
my_flask_project/
│
├── app/
│   ├── __init__.py
│   ├── main.py
│
├── nginx/
│   ├── nginx.conf
│
├── docker-compose.yml
├── Dockerfile
└── requirements.txt
```

### 2. Conteúdo dos Arquivos

**`app/__init__.py`**

```python
from flask import Flask

def create_app():
    app = Flask(__name__)

    @app.route('/')
    def hello():
        return "Hello, World!"

    return app
```

**`app/main.py`**

```python
from app import create_app

app = create_app()

if __name__ == "__main__":
    app.run(host='0.0.0.0')
```

**`nginx/nginx.conf`**

```nginx
server {
    listen 80;

    location / {
        proxy_pass http://flask:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

**`requirements.txt`**

```
Flask==2.1.2
```

**`Dockerfile`**

```Dockerfile
# Use an official Python runtime as a parent image
FROM python:3.9-slim

# Set the working directory in the container
WORKDIR /usr/src/app

# Copy the current directory contents into the container at /usr/src/app
COPY . .

# Install any needed packages specified in requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# Make port 5000 available to the world outside this container
EXPOSE 5000

# Define environment variable
ENV FLASK_APP=app/main.py

# Run app.py when the container launches
CMD ["flask", "run", "--host=0.0.0.0"]
```

**`docker-compose.yml`**

```yaml
version: '3'

services:
  flask:
    build: .
    ports:
      - "5000:5000"
    networks:
      - mynetwork

  nginx:
    image: nginx:latest
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
    ports:
      - "80:80"
    depends_on:
      - flask
    networks:
      - mynetwork

networks:
  mynetwork:
    driver: bridge
```

### 3. Como Rodar o Projeto

1. **Navegue até o diretório do projeto:**

    ```sh
    cd my_flask_project
    ```

2. **Construa e inicie os contêineres:**

    ```sh
    docker-compose up --build
    ```

3. **Acesse a aplicação no navegador:**

    Abra o navegador e vá para `http://localhost`.

*** RESULTADO ***
![<alt-text>](hello-nginx-server.png)

### Próximos Passos

A partir daqui, podemos continuar a adicionar funcionalidades e ferramentas no Docker Compose, que serão úteis na produção. Isso facilitará a transição para o Kubernetes no futuro.

Alguns passos futuros podem incluir:
- Adicionar um banco de dados (por exemplo, PostgreSQL)
- Configurar logs e monitoramento
- Adicionar um servidor de cache (por exemplo, Redis)
- Configurar variáveis de ambiente e secrets

