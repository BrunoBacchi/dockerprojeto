# Exercícios Docker

> **IMPORTANTE**: Evite usar a versão ``latest`` por uma questão de incompatibilidade com outras aplicações, opte por versões mais antigas e estáveis.


### 🍏 Nível Fácil 🍏

1. **Executando um container básico**
    - Baixe a imagem oficial do Nginx e rode um container para acessar a página padrão no navegador.
    - Exemplo de uso: Hospedar a página estática do TailwindCSS [landing page do TailwindCSS](https://github.com/tailwindtoolbox/Landing-Page "https://github.com/tailwindtoolbox/landing-page") dentro do container..

    ```bash
    docker pull nginx:stable-perl
    docker run --name nomedo-container -d -p 8080:80 nginx:stable-perl
    ```

2. **Criando e rodando um container interativo**
    - Primeiro, você vai rodar um container com a imagem Ubuntu e acessar o terminal para interagir com ele.
    - Exemplo prático: Você pode testar um script em Bash que, por exemplo, mostra logs do sistema ou instala alguns pacotes de forma interativa.

    ```bash
    docker pull ubuntu:stable-perl
    docker run -dti --name meu-container ubuntu:stable-perl
    docker exec -ti meu-container bash
    ```

    Dentro do container, você pode atualizar o sistema e instalar o nano (editor de texto):

    ```bash
    apt update && apt upgrade
    apt install nano
    ```

    Agora, crie um script chamado ¨exec.sh¨ usando o nano:

    ```bash
    #!/bin/bash
    apt update
    apt upgrade -y
    apt autoremove -y
    echo "Atualização concluída!"
    ```

    Depois, dê permissão para o script rodar e execute:

    ```bash
    chmod +x exec.sh
    ./exec.sh
    ```



3. **Listando e removendo os Containers**

    - Para listar todos os containers, tanto os que estão rodando quanto os que estão parados, é só usar o comando abaixo.
    - Se você quiser parar algum container que está em execução, use o comando de parar.
    - E, para remover um container específico, use o comando de remoção.

    ```bash
    docker ps -a
    docker stop nome-do-container
    docker rm nome-do-container
    ```


4. **Criando um Dockerfile para uma aplicação em Flask Simples!**
    - Vamos criar um Dockerfile para rodar uma aplicação Flask que retorna uma mensagem ao acessar um endpoint.
    - Exemplo: Vamos criar um endpoint básico que retorna uma saudação com o nome fornecido.

    - Estrutura do projeto

 - Antes de começar, criamos uma pasta para armazenar nosso projeto. Dentro dessa pasta, teremos três arquivos principais:
  
    ```dockerfile
    |── app.py                # Código da aplicação Flask
    │── requirements.txt      # Dependências da aplicação
    │── Dockerfile            # Instruções para criar o container
    ```
- Agora, vamos construir cada um deles

### Criando o ```app.py```

Esse será o código principal da nossa aplicação Flask. Ele cria um servidor web simples com um endpoint / que retorna uma mensagem.

- Passo 2: Aplicação Flask ```app.py``` 📝

- Agora, criamos o código da aplicação Flask que vai rodar um servidor e responder com uma saudação.
  
    ```python
    from flask import Flask
    
    app = Flask(__name__)
    
    @app.route('/')
    def padaria():
        return 'Não temos pão duro'  # Mensagem retornada na rota principal
    
    if __name__ == '__main__':
        app.run(host='0.0.0.0', port=5000)  # Executa o app na porta 5000
    ```
  - Explicação rápida:

  - Criamos um app Flask.
  - Definimos uma rota / que retorna uma string quando acessada.
  - O app roda na porta 5000 e fica acessível para qualquer IP (0.0.0.0).
   
### Criando o ```requirements.txt``` 📦
   
 - Esse arquivo lista as dependências do nosso projeto. Assim, quando subirmos o container, ele saberá quais pacotes instalar.

   ```txt
   Flask==2.3.2
   ```

 - O ```requirements.txt``` é essencial para que possamos instalar exatamente a versão necessária do Flask.

### Criando o ```Dockerfile``` 🏗️

- Agora, criamos o Dockerfile, que contém todas as instruções para construir nosso container:

```dockerfile
# Utiliza uma imagem do Python mais recente
FROM python:3.13

# Define o diretório de trabalho dentro do container
WORKDIR /app

# Copia o arquivo de dependências para o container
COPY requirements.txt .

# Instala as dependências necessárias
RUN pip install --no-cache-dir -r requirements.txt

# Copia todo o código para dentro do container
COPY . .

# Expõe a porta 5000 para que possamos acessar o serviço
EXPOSE 5000

# Comando que inicia o servidor Flask quando o container for executado
CMD ["python", "app.py"]
```

- ### Explicação passo a passo:

    
    - ```FROM python:3.13``` → Baixa uma imagem do Python na versão 3.13.
    - ```WORKDIR /app``` → Define o diretório dentro do container onde nosso código será armazenado.
    - ```COPY requirements.txt .``` → Copia o arquivo de dependências para o container.
    - ```RUN pip install --no-cache-dir -r requirements.txt``` → Instala os pacotes listados no requirements.txt.
    - ```COPY . .``` → Copia o restante do código para dentro do container.
    - ```EXPOSE 5000``` → Indica que o container usará a porta 5000.
    - ```CMD ["python", "app.py"]``` → Diz ao container que, ao rodar, deve executar app.py
   
  
- ### Criando e rodando o ```container``` 🚀

Agora que temos todos os arquivos prontos, vamos construir e executar nosso container:

- Passo 1: Construindo a imagem

No terminal, dentro da pasta do projeto, rodamos:

```bash
docker build . -t flask-app
```
 - Isso cria uma imagem chamada flask-app com base no Dockerfile.

 - Passo 2: Rodando o container:

```bash
docker run -dti --name meu-container -p 5000:5000 flask-app
```

- ### O que acontece aqui:

  - ```-d``` → Roda o container em segundo plano.
  - ```-t``` → Cria um terminal interativo (caso precisemos interagir com o container).
  - ```-i``` → Mantém o processo rodando, permitindo entrada de comandos (útil para debug).
  - ```--name meu-container``` → Dá um nome ao container.
  - ```-p 5000:5000``` → Mapeia a porta 5000 do host para a porta 5000 do container.
    
- Agora, é só abrir o navegador e acessar:

```txt
http://localhost:5000
```
- Se tudo estiver certo, veremos a mensagem: ```Não temos pão duro```

  ---

### 🍊 Nível Médio 🍊



5. **Criando e Usando Volumes para Persistência de Dados com MySQL**
    - Vamos rodar um container MySQL e configurar um volume para garantir que os dados do banco fiquem persistentes, mesmo quando o container for reiniciado ou removido.
    - Exemplo prático: Use um sistema de login e cadastro no Laravel Breeze, que usa MySQL para o banco de dados. [Laravel Breeze](https://github.com/laravel/breeze "https://github.com/laravel/breeze")

    Passo 1: Criando o Volume

    - Primeiro, criamos um volume para armazenar os dados do MySQL:

    ```bash
    docker volume create meu-volume
    docker volume ls  # Vai listar todos os volumes que você criou
    ```

    Passo 2: Rodando o Container MySQL com o Volume

    - Agora, vamos rodar o container com a imagem oficial do MySQL e conectar o volume:

    ```bash
    docker run -e MYSQL_ROOT_PASSWORD=1256 -dti -p 3306:3306 --name mysql-container --mount type=volume,src=meu-volume,dst=/var/lib/mysql mysql:9.2
    ```

    Explicando os parâmetros:
    ```
    -e MYSQL_ROOT_PASSWORD=1256: Define a senha do usuário root.
    --name mysql-container: Dá um nome para o container.
    -d: Roda o container em segundo plano.
    -p 3306:3306: Mapeia a porta 3306 do container para a porta 3306 do host.
    --mount type=volume,src=meu-volume,dst=/var/lib/mysql: Cria o volume para armazenar os dados persistentes do MySQL.
    mysql:9.2: Usamos a versão 9.2 da imagem do MySQL.
    ```
    
    Passo 3: Acessando o Container

    - Para entrar no container e interagir com ele via terminal, use o comando:
  
    ```bash
    docker exec -ti mysql-container bash
    ```

    Passo 4: Acessando o MySQL dentro do Container

    - Agora, para acessar o MySQL e começar a rodar comandos SQL, use o seguinte:
      
    ```bash
    mysql -u root -p --protocol=tcp --port=3306
    ```
    - Quando pedir a senha, coloque 1256 (a senha que você definiu).

    Passo 5: Comandos MySQL

    - Agora dentro do MySQL, vamos criar uma base de dados, uma tabela e adicionar alguns dados de exemplo:

    ```bash
    show databases;
    -- Cria a base de dados chamada 'mercado'
    create database mercado;
    -- Seleciona a base de dados que acabamos de criar
    use mercado;
    
    -- Cria a tabela 'produtos'
    CREATE TABLE produtos (id INTEGER PRIMARY KEY, nome TEXT NOT NULL, quantidade INTEGER NOT NULL);
    
    -- Insere alguns valores na tabela 'produtos'
    INSERT INTO produtos VALUES (1, 'Arroz', 10);
    INSERT INTO produtos VALUES (2, 'Feijão', 3);
    ```

    - Passo 6: vamos criar o segundo container:
  
    ```bash
    docker run -e MYSQL_ROOT_PASSWORD=1256 -dti -p 3306:3306 --name mysql-containerdois --mount type=volume,src=meu-volume,dst=/var/lib/mysql mysql:9.2
    ```
    
    - Passo 7: Acessando o Container 2

    - Para entrar no container e interagir com ele via terminal, use o comando:
  
    ```bash
    docker exec -ti mysql-containerdois bash
    ```

    Passo 8: Acessando o MySQL dentro do Container 2

    - Agora, para acessar o MySQL e começar a rodar comandos SQL, use o seguinte:
      
    ```bash
    mysql -u root -p --protocol=tcp --port=3306
    ```
    
    - Quando pedir a senha, coloque 1256 (a senha que você definiu).


    Passo 9: Agora selecionaremos os dados existentes:
   
    ```bash
    show databases;
    -- Mostra todos os produtos com quantidade maior ou igual a 5
    SELECT * FROM produtos WHERE quantidade >= 5;
    ```

    - Explicação: O motivo disto é que o container 1 foi criado para selecionar as instancias, enquanto o segundo container foi feito para listá-las, mesmo que seja apagado o container 1, ainda será mostrado o resultado pelo container 2 como se fosse um backup.

5. **Criando/Executando um Container Multi stage**
    - Vamos usar o multi-stage build para otimizar a imagem de uma aplicação Go, diminuindo o tamanho final dela. Nesse exemplo, vamos compilar e rodar uma API simples usando o Go Fiber Example dentro do container. [Go Fiber Example](https://github.com/gofiber/recipes/tree/main/docker-multistage-build "https://github.com/gofiber/recipes/tree/main/docker-multistage-build")
  
    - Passo 1: Baixar as Imagens Necessárias

    - Primeiro, você vai precisar baixar duas imagens:

    - A imagem base do Golang para compilar a aplicação.
    - A imagem base do Alpine para rodar o binário compilado de maneira mais leve.

    ```bash
    docker pull golang:1.24
    docker pull alpine:3.21
    ```

    - Passo 2: Criar o Arquivo app.go

    - Agora, vamos criar o código simples em Go. O programa vai pedir o nome do usuário e dar uma saudação.

    - Arquivo app.go: 

    ```go
    package main

    import(
      "fmt"
    )
    
    func main(){
      fmt.Println("Qual é o seu nome?")
      var name string
      fmt.Scanln(&name)
      fmt.Printf("Oi, %s! Eu sou a linguagem Go", name)
    }
    ```

    - Passo 3: Criar o Dockerfile para Multi-Stage Build

    - O próximo passo é criar o Dockerfile. Vamos usar duas etapas (stages) para deixar a imagem final bem mais leve:

    - No primeiro estágio, vamos usar a imagem golang:1.24 para compilar o código.
    - No segundo estágio, vamos pegar a aplicação compilada e rodar com a imagem alpine, que é bem mais leve.

    ```dockerfile
    # Primeiro estágio: usar a imagem do Golang para compilar o código

    FROM golang:1.24 as exec
    COPY app.go /go/src/app/
    ENV GO111MODULE=auto
    WORKDIR /go/src/app/
    RUN go build -o app.go .
    
    # Segundo estágio: usar a imagem do Alpine para rodar o binário compilado

    FROM alpine:3.21
    WORKDIR /appexec
    COPY --from=exec /go/src/app /appexec
    RUN chmod -R 755 /appexec
    ENTRYPOINT ./app.go
    ```

    - Passo 4: Criar a Imagem e Rodar o Container
      
    - Agora, vamos criar a imagem do Docker e rodar o container com o seguinte comando:

    ```bash
    docker image build -t nome-imagem .
    docker run -ti --name nome-container nome-imagem
    ```

    - Com isso, sua aplicação Go vai estar rodando dentro do container de forma otimizada, utilizando o multi-stage build para reduzir o tamanho da imagem final



6. **Criando uma Rede Docker para Conectar Containers Entre Si**
    - Vamos criar uma rede Docker personalizada e fazer dois containers (um Node.js e um MongoDB) se comunicarem. Para esse exemplo, vamos usar o projeto MEAN Todos para criar um app de tarefas com Node.js e MongoDB.
    - Exemplo do projeto Mean Todos:[MEAN Todos](https://github.com/luanphandinh/mean-todo "https://github.com/luanphandinh/mean-todo") 

    - Passo 1: Criar a Rede Docker
    - Primeiro, criamos uma rede Docker personalizada para os containers se comunicarem:

    ```bash
    docker network create nome_da_rede
    docker network ls Isso vai listar todas as redes que você tem no Docker
    ```

    - Passo 2: Baixar as Imagens
    - Agora, precisamos baixar as imagens do Node.js e MongoDB:

    ```bash
    docker pull node
    docker pull mongodb:mongodb-community-server
    ```

    - Passo 3: Criar os Containers
    - Agora que temos as imagens, vamos rodar os containers. Vamos criar o container do Node.js e o container do MongoDB, e ambos vão usar a mesma rede.


    ```bash
    docker run -dti --name nod --network nome_da_rede node
    docker run --name mon -d -p 27017:27017 --network nome_da_rede mongodb/mongodb-community-server
    ```
    
    - Aqui, o que estamos fazendo:
      
    ```
    --name nod: Estamos nomeando o container do Node.js como nod.
    --name mon: O container do MongoDB vai se chamar mon.
    --network nome_da_rede: Ambos os containers vão usar a rede personalizada que criamos.
    ```
    - Passo 4: Verificar a Rede
    - Se quiser ver os detalhes sobre a rede que você criou e os containers que estão conectados nela, use esse comando:

    ```bash
    docker network inspect nome_da_rede
    ```

    - Passo 5: Entrar nos Containers
    - Agora, se você quiser entrar nos containers para dar uma olhada ou fazer testes, usa o comando:

    ```bash
    docker exec -ti nome_do_container bash
    ```

    - Passo 6: Testar a Comunicação com o Ping
    - Dentro de cada container, vamos garantir que você consiga fazer o ping entre eles. Para isso, instale a ferramenta de ping nos containers:
  
    ```bash
    apt update
    apt install -y iputils-ping
    ```
    - Agora, você pode testar se os containers estão se comunicando. Por exemplo, dentro do container Node.js, faça o ping para o MongoDB:
  
    ```bash
    ping 172.18.0.2  # Exemplo de IP do container Node.js
    ```
    
    - E vice-versa, pingando o IP do Node.js de dentro do container MongoDB.
---



### 8. **Usando Docker Compose para Rodar uma Aplicação Django com PostgreSQL**

- Agora, vamos usar o Docker Compose para rodar uma aplicação Django com PostgreSQL de um jeito bem tranquilo. No exemplo, vamos pegar o projeto Django Polls App para criar uma pesquisa de opinião, tudo rodando em containe
    
- Passo 1: Criar um Volume para o Banco de Dados
- Antes de tudo, criaremos um volume para o banco de dados PostgreSQL, garantindo que os dados fiquem persistentes:

    ```bash
    docker volume create meuvolume
    ```

- Passo 2: Criar o Arquivo docker-compose.yml
- Agora, crie o arquivo docker-compose.yml para configurar os containers do PostgreSQL e Django. Aqui, vou te dar o template, mas você pode trocar o que quiser, como o nome da rede ou o nome do volume.

    ```yaml
    services:
  postgresql:
    image: postgres:14.17
    environment:
      POSTGRES_PASSWORD: "Senha123"  # A senha tem que estar entre aspas
      POSTGRES_DB: "minha_database"  # Pode colocar o nome que quiser para o banco
    ports:
      - "5432:5432"
    volumes:
      - meu_volume:/var/lib/postgresql/data
    networks:
      - minha_rede  # Você pode trocar esse nome de rede

  django:
    image: django:onbuild
    ports:
      - "8000:8000"
    networks:
      - minha_rede  # Mesmo nome de rede para os dois serviços

    networks:
      minha_rede:  # O nome da rede pode ser o que você quiser
        driver: bridge
    
    volumes:
      meu_volume:  # O nome do volume também pode ser alterado
    ```

    - Passo 3: Rodar o Docker Compose
    - Agora, para rodar tudo de uma vez com o Docker Compose, use esse comando:

    ```bash
    docker-compose up -d
    ```

    - Isso vai subir os containers em segundo plano, e sua aplicação Django vai estar pronta para se comunicar com o banco de dados PostgreSQL.
      
    - Passo 4: Parar e Apagar os Containers
    - Se precisar parar os containers e apagar tudo, use esse comando:

    ```bash
    docker-compose down
    ```

---



### 🍅 Nível Difícil 🍅

9. **Criando uma imagem personalizada com um servidor web e arquivos estáticos**
    
    - Vamos passar por todo o processo, desde a estrutura dos arquivos até a construção e execução do container
### Estrutura do Projeto 📂 

- Antes de começarmos a escrever código, vamos organizar os arquivos do projeto. Dentro de uma pasta principal (por exemplo, meu_site), teremos:
  
```bash
/execicio9
│── index.html          # Página principal do site
│── style.css           # Estilos do site
│── nginx.conf          # Configuração do servidor Nginx
│── Dockerfile          # Instruções para construir a imagem Docker
```

### Criando o index.html 📝

- Este será o arquivo principal do nosso site. É um HTML básico, com um cabeçalho, um texto central e um rodapé:

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Meu Site Simples</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <header>
        <h1>Site Simples</h1>
    </header>
    <main>
        <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum..</p>
    </main>
    <footer>
        <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.</p>
    </footer>
</body>
</html>
```

### Criando o style.css 🎨

- Agora, vamos definir o estilo do nosso site para deixá-lo mais apresentável.

```css
body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
    background-color: #f4f4f4;
    color: #333;
}

header {
    background-color: #333;
    color: #fff;
    padding: 20px;
    text-align: center;
}

main {
    padding: 20px;
    text-align: center;
}

footer {
    background-color: #333;
    color: #fff;
    text-align: center;
    padding: 10px;
    position: fixed;
    bottom: 0;
    width: 100%;
}
```

### Criando o nginx.conf ⚙️ 

- Este arquivo configura o Nginx para servir nosso site.

```nginx
server {
    listen 80;
    server_name localhost;

    location / {
        root /usr/share/nginx/html;
        index index.html;
    }
}
```

- Explicação:

- O servidor escuta na porta 80 (padrão para HTTP).
- O site está localizado em ```/usr/share/nginx/html```, que é o diretório padrão do Nginx para arquivos estáticos.
- Quando alguém acessa ```/```, o Nginx exibe o ```index.html```.

### Criando o Dockerfile 📦

- O Dockerfile contém todas as instruções para criar nossa imagem personalizada com Nginx:

```dockerfile
# Usa a imagem base do Nginx com Alpine Linux (versão leve)
FROM nginx:alpine

# Copia os arquivos HTML e CSS para a pasta do Nginx
COPY index.html /usr/share/nginx/html/
COPY style.css /usr/share/nginx/html/

# Copia o arquivo de configuração personalizado do Nginx
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Expõe a porta 80 para acessar o servidor
EXPOSE 80

# Mantém o Nginx rodando em primeiro plano
CMD ["nginx", "-g", "daemon off;"]
```

Explicação:

- ```FROM nginx:alpine``` → Usa uma imagem do Nginx baseada no Alpine Linux (mais leve e eficiente).
- ```COPY index.html /usr/share/nginx/html/``` → Copia a página HTML para o diretório padrão do Nginx.
- ```COPY style.css /usr/share/nginx/html/``` → Copia o arquivo de estilos.
- ```COPY nginx.conf /etc/nginx/conf.d/default.conf``` → Substitui a configuração padrão do Nginx pela nossa.
- ```EXPOSE 80``` → Indica que o container vai rodar na porta 80.
- ```CMD ["nginx", "-g", "daemon off;"]``` → Mantém o Nginx rodando em primeiro plano (necessário para o Docker não encerrar o processo).

### Construindo e Rodando o Container 🚀 

- Agora que temos todos os arquivos prontos, podemos construir e rodar nosso container!

- Passo 1: Construindo a Imagem

No terminal, dentro da pasta do projeto, rodamos:

```bash
docker image build -t meu-site .
```

- Isso cria uma imagem Docker personalizada chamada ```meu-site```

- Passo 2: Executando o Container

Agora, rodamos o container baseado na imagem criada:

```bash
docker run -dti --name meu-container -p 8080:80 meu-site
```

O que acontece aqui:

- ```-d``` → Roda o container em segundo plano.
- ```-t``` → Cria um terminal interativo.
- ```-i``` → Mantém a entrada ativa (para debug).
- ```--name meu-container``` → Dá um nome ao container.
- ```-p 8080:80``` → Mapeia a porta 80 do container para a porta 8080 do host.

Agora, basta abrir o navegador e acessar:

```txt
http://localhost:8080
``` 
