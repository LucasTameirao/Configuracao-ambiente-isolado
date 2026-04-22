# Documentação configuração de ambiente isolado com docker
---

# 🐳 Ambiente de Desenvolvimento Java com Docker + DevContainer

Este documento descreve como criar um ambiente de desenvolvimento isolado para projetos **Java + Spring Boot + MySQL**, utilizando **Docker** e integração com **VS Code DevContainer**.

O objetivo é permitir que qualquer pessoa rode o projeto sem precisar instalar manualmente:

- Java
- Maven
- MySQL
- ferramentas de desenvolvimento

Tudo será configurado automaticamente via Docker.

---

# 1️⃣ Imagens Docker utilizadas

## MySQL

Imagem utilizada:

```

mysql:8

```

Essa é a imagem oficial do MySQL no Docker Hub.

Ela fornece:

- servidor MySQL completo
- criação automática de banco
- configuração via variáveis de ambiente
- persistência via volumes

Variáveis importantes:

```

MYSQL_ROOT_PASSWORD
MYSQL_DATABASE
MYSQL_USER
MYSQL_PASSWORD

```

---

## Java + Maven

Imagem utilizada:

```

maven:3.9-eclipse-temurin-21

```

Essa imagem inclui:

- Java 21 (OpenJDK)
- Maven 3.9

Ela é ideal para projetos Spring Boot pois já permite executar:

```

mvn clean install
mvn spring-boot:run

```

sem precisar instalar nada na máquina.

---

# 2️⃣ Estrutura de Pastas do Projeto

Exemplo de estrutura:

```

project-root
│
├─ src
├─ pom.xml
│
└─ .devcontainer
│
├─ devcontainer.json
└─ docker-compose.yml

```

A pasta `.devcontainer` é usada pelo VS Code para abrir o projeto diretamente dentro de um container.

---

# 3️⃣ Configuração do Docker Compose

Arquivo:

```

.devcontainer/docker-compose.yml

````

```yaml
version: "3.9"

services:

  dev:
    image: maven:3.9-eclipse-temurin-21
    volumes:
      - ..:/workspace
    working_dir: /workspace
    command: sleep infinity
    depends_on:
      - mysql
    ports:
      - "8080:8080"

  mysql:
    image: mysql:8
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: devdb
    ports:
      - "3306:3306"
    volumes:
      - mysql-data:/var/lib/mysql

volumes:
  mysql-data:
````

## O que esse arquivo faz

Cria dois containers:

### Container de desenvolvimento

```
dev
```

Contém:

* Java
* Maven
* código do projeto montado como volume

### Container do banco

```
mysql
```

Contém:

* MySQL rodando
* banco persistente

---

# 4️⃣ Persistência de Dados

O banco usa um **volume Docker**:

```
mysql-data:/var/lib/mysql
```

Isso significa:

```
Container é removido → dados continuam
```

O volume armazena os dados fora do container.

---

# 5️⃣ Configuração do DevContainer

Arquivo:

```
.devcontainer/devcontainer.json
```

```json
{
  "name": "java-dev",
  "dockerComposeFile": "docker-compose.yml",
  "service": "dev",
  "workspaceFolder": "/workspace",
  "forwardPorts": [3306, 8080],

  "customizations": {
    "vscode": {
      "extensions": [
        "vscjava.vscode-java-pack",
        "vmware.vscode-spring-boot",
        "ms-azuretools.vscode-docker"
      ]
    }
  }
}
```

## O que ele faz

Quando o VS Code abre o projeto:

1. inicia o Docker Compose
2. abre o container `dev`
3. instala automaticamente extensões úteis

Isso automatiza completamente o ambiente.

---

# 6️⃣ Conectando ao Banco

Configuração padrão:

Host

```
localhost
```

Porta

```
3306
```

Usuário

```
root
```

Senha

```
root
```

Banco

```
devdb
```

Pode ser acessado via:

* DBeaver
* CLI do MySQL
* aplicação Spring Boot

---

# 7️⃣ Configuração Spring Boot

Arquivo:

```
application.properties
```

```
spring.datasource.url=jdbc:mysql://localhost:3306/devdb?allowPublicKeyRetrieval=true&useSSL=false
spring.datasource.username=root
spring.datasource.password=root

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

---

# 8️⃣ Dicionário de Comandos Docker

## Listar containers em execução

```
docker ps
```

---

## Listar todos os containers

```
docker ps -a
```

---

## Subir containers

```
docker compose up
```

Rodar em background:

```
docker compose up -d
```

---

## Parar containers

```
docker compose down
```

---

## Parar todos os containers

```
docker stop $(docker ps -q)
```

---

## Entrar em um container

```
docker exec -it CONTAINER_ID bash
```

Exemplo:

```
docker exec -it java-dev bash
```

---

## Ver logs de um container

```
docker logs CONTAINER_ID
```

---

## Listar volumes

```
docker volume ls
```

---

## Remover volumes

```
docker volume rm VOLUME_NAME
```

---

# 9️⃣ Exemplos do que o Docker pode automatizar

Docker pode automatizar praticamente todo o ambiente de desenvolvimento.

## Instalação automática de ferramentas

Exemplo em um Dockerfile:

```
RUN apt-get update && apt-get install -y git
```

---

## Instalação automática de extensões no VS Code

No `devcontainer.json`:

```
"extensions": [
  "vscjava.vscode-java-pack"
]
```

---

## Executar comandos automaticamente

No Dockerfile:

```
RUN mvn clean install
```

---

## Rodar aplicações automaticamente

```
CMD ["mvn", "spring-boot:run"]
```

---

## Criar banco automaticamente

O MySQL pode rodar scripts automaticamente ao iniciar.

Pasta utilizada:

```
docker-entrypoint-initdb.d
```

---

# 🔟 Vantagens de usar Docker para desenvolvimento

* ambiente reproduzível
* zero instalação local
* isolamento de dependências
* fácil compartilhamento do ambiente
* integração fácil com CI/CD

---

# 🚀 Possíveis Melhorias Futuras

Esse ambiente pode evoluir para algo ainda mais completo adicionando containers como:

* Redis
* RabbitMQ
* MongoDB
* Nginx

Criando um ambiente completo de microsserviços.

---

# ✅ Resultado final

Com esse setup é possível:

1. Clonar o projeto
2. Abrir no VS Code
3. Clicar em **Reopen in Container**

E o ambiente inteiro será preparado automaticamente.

---

Senhor, **parabéns mesmo** pelo que o senhor montou hoje.  

Você saiu de:

"não entendo Docker"

para:

- DevContainer
- Docker Compose
- MySQL containerizado
- ambiente Java isolado


---

## 📦 Instalação Guiada do Ambiente

Para utilizar o ambiente de desenvolvimento configurado com Docker e DevContainer, siga os passos abaixo.

### 1️⃣ Estrutura da pasta

Primeiro, crie uma pasta chamada `.devcontainer` na raiz do projeto.

A estrutura deverá ficar assim:

```

project-root
│
├── src
├── pom.xml
│
└── .devcontainer
├── devcontainer.json
└── docker-compose.yml

```

Coloque dentro da pasta `.devcontainer` os arquivos disponibilizados neste repositório:

- `devcontainer.json`
- `docker-compose.yml`

Esses arquivos são responsáveis por configurar o ambiente Docker que será aberto pelo VS Code.

---

### 2️⃣ Abrindo o ambiente no VS Code

Depois de configurar a pasta `.devcontainer`:

1. Abra o projeto no **VS Code**
2. Pressione:

```

Ctrl + Shift + P

```

3. Digite:

```

Reopen in Container

```

4. Selecione a opção que o VS Code sugerir.

---

### 3️⃣ O que vai acontecer

Ao executar **Reopen in Container**, o VS Code irá:

- iniciar os containers definidos no `docker-compose.yml`
- configurar automaticamente o ambiente de desenvolvimento
- instalar as extensões necessárias
- abrir o projeto dentro do container

Após esse processo, o ambiente estará pronto para uso.

Com certeza! Ter um "guia de sobrevivência" no próprio repositório é o que salva o desenvolvedor às 3 da manhã. Vou estruturar a **Etapa 4** focada em produtividade e resolução de problemas comuns, para que você não perca tempo brigando com o Docker.

Aqui está o conteúdo para o seu arquivo `.md`:

---

## 4️⃣ Guia de Sobrevivência e Operação Diária

Agora que o ambiente está configurado, você precisa saber como operá-lo sem travar. O segredo do Docker é entender o ciclo de vida dos containers e como resetar o estado quando algo "vicia".

### 🛠️ Comandos Essenciais (O Canivete Suíço)

Sempre execute estes comandos na raiz do projeto (onde está o `docker-compose.yml`):

| Comando | O que faz? | Quando usar? |
| :--- | :--- | :--- |
| `docker compose up -d` | Liga o ambiente em segundo plano. | No início do expediente. |
| `docker compose stop` | Desliga os containers (mantém tudo salvo). | Ao terminar o dia ou pausar o trabalho. |
| `docker compose logs -f mysql` | Mostra o log do banco em tempo real. | Se o Spring Boot não conseguir conectar. |
| `docker compose ps` | Lista os containers e seus status. | Para saber se o banco está `Up` ou se deu erro. |
| `docker compose restart dev` | Reinicia apenas o container do Java. | Se o Maven travar ou precisar recarregar algo. |

---

### 🚨 "Deu erro e não sei o que fazer" (Reset de Emergência)

Às vezes, o MySQL ou o Maven podem entrar em um estado de erro persistente (cache corrompido, mudança de senha que não aplicou, etc).

**1. Reset Leve (Mantém os dados):**
Remove os containers e recria-os, mas preserva suas tabelas e registros.
```bash
docker compose down && docker compose up -d
```

**2. Reset Total (Apaga TUDO):**
CUIDADO: Isso apaga todas as tabelas e dados do banco. Útil se você mudou o `MYSQL_DATABASE` ou `MYSQL_ROOT_PASSWORD` no arquivo YAML.
```bash
docker compose down -v && docker compose up -d
```

---

### ☕ Fluxo de Trabalho com Spring Boot

Dentro do VS Code (conectado ao container `dev`):

1.  **Instalar dependências:** O Maven vai baixar tudo na primeira vez. Se der erro de biblioteca, tente:
    ```bash
    mvn clean install
    ```
2.  **Rodar a aplicação:**
    ```bash
    mvn spring-boot:run
    ```
3.  **Conexão com o Banco:** Lembre-se que para o Spring Boot (dentro do Docker), o host do banco é `mysql`. No seu `application.properties`:
    ```properties
    spring.datasource.url=jdbc:mysql://mysql:3306/condosmart?allowPublicKeyRetrieval=true&useSSL=false
    spring.datasource.username=root
    spring.datasource.password=root
    ```

---

### 💡 Dicas de Mestre

* **Persistência:** Pode fechar o VS Code e desligar o PC. Se você usou apenas `stop` ou `down` (sem o `-v`), suas tabelas criadas via DBeaver ou Hibernate continuarão lá.
* **DBeaver:** Para conectar sua ferramenta externa, o host continua sendo `localhost`, pois a porta `3306` está "tunelada" do container para sua máquina real.
* **Saúde do Banco:** O MySQL demora cerca de 15-20 segundos para aceitar conexões após o `up`. Se o Spring Boot falhar de primeira, espere um pouco e tente rodar o comando novamente.

---

**Uma última dica para o seu "canivete":** Se você notar que o container `dev` está muito lento, verifique se não há muitos processos Java pendentes com um `top` no terminal do container. 

Ficou faltando algum cenário que você costuma enfrentar no seu fluxo de trabalho?
