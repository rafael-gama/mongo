# MongoDB com ReplicaSet (Scale out)



## Baixar imagem do mongoDB (Oficial) do repositório do docker: 

```
docker pull mongo
```

## Criar rede para o cluster mongo

```
docker network create net-cluster-mongo
```

## Inicializar os containers mongodb com parâmetro de ReplicaSet

##### Inicializando Primeiro nó:

```
docker run -d -p 30001:27017 --name btc-node01 -h btc-node01 --net net-cluster-mongo mongo mongod --replSet lab-btc_rs1
```

##### Inicializando Segundo nó:

```
docker run -d -p 30002:27017 --name btc-node02 -h btc-node02 --net net-cluster-mongo mongo mongod --replSet lab-btc_rs1
```

##### Inicializando Terceiro nó: 

```
docker run -d -p 30003:27017 --name btc-node03 -h btc-node03 --net net-cluster-mongo mongo mongod --replSet lab-btc_rs1
```

## Configurar replicaset nos nós

1. ### Abrir 3 terminais e acessar cada nó em seu terminal

   ```
   docker exec -it btc-node01 /bin/bash
   ```

   ```
   docker exec -it btc-node02 /bin/bash
   ```

   ```
   docker exec -it btc-node03 /bin/bash
   ```

   

2. ### Acessar node01 e Setar configurações do replicaSet para variável config, adicionando apenas dois servidores no cluster, neste primeiro momento:

   ```
   mongo
   ```

   ```
   config={_id : "lab-btc_rs1",members: [{ _id: 0, host: "btc-node01:27017"},{ _id: 1, host: "btc-node02:27017" }]}
   ```

   

3. ### Aplicando configuração no cluster

   ```
   rs.initiate(config)
   ```

4. ### Verificando status do cluster

   ```
   rs.status()
   ```

   

## Criar database e collection no nó primário

```
use BDBTC
```

```
db.databaseCriado.insert({"data_criacao_database":new Date(),"motivo_solicitacao":"Laboratório de Bootcamp"});
```

1. ### 	Conferir a collection criada e seu documento

   ```
   show collections
   ```

   ```
   db.databaseCriado.find().pretty()
   ```

2. ### Acessar o nó secundário (node 02) e conferir se a replicação foi efetuada com sucesso.

```
mongo 
```

##### Aplicar um dos comandos abaixo, necessário para que consiga realizar consultas pelo nó secundário:

```
rs.slaveOk()
rs.secondaryOk()
```

#### 3. Conferir se a replicação foi efetuada com sucesso no nó secundário:

```
show dbs
```

```
use BDBTC
```

```
show collections
```

```
db.databaseCriado.find().pretty()
```

### Caso a replicação tenha sido efetuada, siga a diante para adicionar o terceiro nó do replicaSet:

1. Acesse o terminal referente ao nó primário e execute o seguinte comando para adicionar o terceiro nó:

   ```
   rs.add("btc-node03:27017")
   ```

   

2. Verifique se o nó foi adicionado com sucesso ao cluster, execute o seguinte comando: 

   ```
   rs.status()
   ```

### Simulando queda do nó primário e verificando o comportamento do cluster:

1. Parando o container referente ao nó primário:

   ```
   docker stop btc-node01
   ```

2. Confirmando que há apenas dois nós em execução: 

   ```
   docker ps
   ```

3. Efetuar login no mongoDB através da string de conexão: 

   ```
   mongo --host "lab-btc_rs0/btc-node01:27017,btc-node02:27017,btc-node03:27017"
   ```

4. Executar o seguinte comando para verificar o status do cluster:

   ```
   rs.status();
   ```

O resultado deste ultimo comando mostrará que o servidor btc-node01 não está em execução e um dos outros dois assumiu como nó primário.

Dessa forma, concluimos a implementação básica com MongoDB ReplicaSet.