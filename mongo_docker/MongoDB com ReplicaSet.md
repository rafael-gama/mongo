# MongoDB com ReplicaSet (Scale out)

## Criando rede para o cluster mongo

```
docker network create net-cluster-mongo2
```

## Criando o replicaSet com 3 nós

##### Inicializando Primeiro nó:

```
>docker run -d -p 30005:27017 --name btc-node05 -h btc-node05 --net net-cluster-mongo2 mongo mongod --replSet lab-btc_rs1
```

##### Inicializando Segundo nó:

```
docker run -d -p 30006:27017 --name btc-node06 -h btc-node06 --net net-cluster-mongo2 mongo mongod --replSet lab-btc_rs1
```

##### Inicializando Terceiro nó: 

```
docker run -d -p 30007:27017 --name btc-node07 -h btc-node07 --net net-cluster-mongo2 mongo mongod --replSet lab-btc_rs1
```

## Configurar replicaset nos nós.

1. ### Abrir dois terminais e acessar apenas dois nós, cada um terminais diferentes

   ### A princípio, será configurada apenas para dois nós.

   ```
   docker exec -it btc-node05 /bin/bash
   ```

   ```
   docker exec -it btc-node06 /bin/bash
   ```

2. ### Acesse node05 e Setar configurações do replicaSet para variável config

   ```
   mongo
   ```

   ```
   config={_id : "lab-btc_rs1",members: [{ _id: 0, host: "btc-node05:27017"},{ _id: 1, host: "btc-node06:27017" }]}
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

1. ### 	Conferir a collection criada e seu document

   ```
   show collections
   ```

   ```
   db.databaseCriado.find().pretty()
   ```

2. ### Acessar o nó secundário (node 06) e conferir se a replicação foi efetuada com sucesso.

```
mongo 
```

```
#Comando necessário para que consiga realizar consultas no nó secundário
rs.slaveOk()    ou  rs.secondaryOk()
```

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



1. ajhdkfjad]
2. alkdjnflakd
3. kandf