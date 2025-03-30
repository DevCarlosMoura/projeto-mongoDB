# MongoDB Cluster com Docker

Este repositório contém um guia para configurar um cluster do MongoDB utilizando 4 containers Docker (mongo10, mongo20, mongo30, mongo40) sem utilizar o Docker Compose. O cluster será configurado manualmente com Docker e MongoDB Compass para visualização e gerenciamento.

## Pré-requisitos

- **Docker**: Instale o Docker na sua máquina. Caso não tenha o Docker, siga a documentação oficial para instalação:
  - [Docker Installation Guide](https://docs.docker.com/get-docker/)
- **MongoDB Compass**: Instale o MongoDB Compass para visualizar o cluster MongoDB. Faça o download em: [MongoDB Compass Download](https://www.mongodb.com/try/download/compass).

## Passos para criação do Cluster MongoDB

### 1. Criar os Containers MongoDB

Primeiro, vamos criar 4 containers Docker para as instâncias do MongoDB. Cada container representará um nó no cluster.

#### 1.1. Criar o container `mongo10`

Execute o comando abaixo para iniciar o container `mongo10`:

```bash
docker run -d --name mongo10 --network=mongo-cluster -p 27011:27017 mongo:latest --replSet rs0

1.2. Criar o container mongo20
Execute o comando abaixo para iniciar o container mongo20:
docker run -d --name mongo20 --network=mongo-cluster -p 27012:27017 mongo:latest --replSet rs0

1.3. Criar o container mongo30
Execute o comando abaixo para iniciar o container mongo30:
docker run -d --name mongo30 --network=mongo-cluster -p 27013:27017 mongo:latest --replSet rs0

1.4. Criar o container mongo40
Execute o comando abaixo para iniciar o container mongo40:
docker run -d --name mongo40 --network=mongo-cluster -p 27014:27017 mongo:latest --replSet rs0

2. Configuração do Replica Set
Agora que os containers estão em execução, é necessário configurar o Replica Set no MongoDB.

2.1. Acessar o mongo10
Conecte-se ao container mongo10:
docker exec -it mongo10 mongo

2.2. Inicializar o Replica Set
No shell do MongoDB, execute o comando abaixo para inicializar o Replica Set:
rs.initiate({
  _id: "rs0",
  members: [
    { _id: 0, host: "mongo10:27017" },
    { _id: 1, host: "mongo20:27017" },
    { _id: 2, host: "mongo30:27017" },
    { _id: 3, host: "mongo40:27017" }
  ]
})

2.3. Verificar o status do Replica Set
Para verificar se o Replica Set foi configurado corretamente, execute:
rs.status()
O status do Replica Set deve mostrar todos os membros (mongo10, mongo20, mongo30, mongo40) com o estado PRIMARY, SECONDARY ou ARBITER.

3. Conectar com o MongoDB Compass
Agora que o Replica Set está configurado, você pode conectar ao MongoDB utilizando o MongoDB Compass.

Abra o MongoDB Compass.
No campo de conexão, insira a URI de conexão do MongoDB. Exemplo:
mongodb://mongo10:27017,mongo20:27017,mongo30:27017,mongo40:27017/?replicaSet=rs0
Clique em "Connect" para visualizar e gerenciar o seu cluster MongoDB.

4. Testar a Alta Disponibilidade
Após configurar o Replica Set, você pode testar a alta disponibilidade do seu cluster.
Para testar a falha de um nó, pare o container mongo10:
docker stop mongo10
Verifique se o Replica Set se reconfigura automaticamente, promovendo um dos nós restantes a PRIMARY. Use o comando no shell do MongoDB:
rs.status()

5. Reiniciar o Nó
Após testar a falha de um nó, reinicie o container mongo10:
docker start mongo10

6. Concluir
Seu cluster MongoDB com 4 containers foi configurado com sucesso! Agora você pode gerenciar e operar com o MongoDB através do MongoDB Compass ou qualquer cliente MongoDB.
Considerações Finais
Você pode expandir ou reduzir o número de nós no seu Replica Set conforme necessário.
Sempre monitore a saúde do Replica Set utilizando rs.status() para garantir que todos os nós estejam funcionando corretamente.
Use o MongoDB Compass para gerenciar os dados e realizar operações no cluster com facilidade.
