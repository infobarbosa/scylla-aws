# Cluster 
Author: Prof. Barbosa<br>
Contact: infobarbosa@gmail.com<br>
Github: [infobarbosa](https://github.com/infobarbosa)

### Objetivo
Executar o Scylla em um cluster de 2 nós (ou nodes) e então uma aplicação cliente escrita em Python e observar os principais parâmetros oferecidos pelo driver.

### Instalando o `cqlsh`
Esse passo só é necessário se você não instalou o Scylla antes nesse host.
```
sudo snap install cqlsh
```

### Inicialiando o cluster
Por comodidade e também restrição de recursos de cpu e memória do nosso laboratório, vamos inicializar um cluster com apenas 2 nós.
```
docker compose up -d
```

Atenção! Em função das restrições de recursos, a inicialização deve demorar

```
docker compose exec -it scylla1 nodetool status
```


### `cqlsh` 

### CREATE KEYSPACE
Abra uma nova janela
```
cqlsh

create keyspace teste WITH replication = {'class': 'SimpleStrategy', 'replication_factor': 1 }

USE teste

create table t1(c1 text primary key, c2 text);

insert into t1(c1, c2) values('1', 'valor 1');
insert into t1(c1, c2) values('2', 'valor 2');
insert into t1(c1, c2) values('3', 'valor 3');
insert into t1(c1, c2) values('4', 'valor 4');
insert into t1(c1, c2) values('5', 'valor 5');
insert into t1(c1, c2) values('6', 'valor 6');
insert into t1(c1, c2) values('7', 'valor 7');
insert into t1(c1, c2) values('8', 'valor 8');
insert into t1(c1, c2) values('9', 'valor 9');
```

### Verificando tokens
```
nodetool ring
```

Output:
```
root@faed7a313b07:~# nodetool ring

Datacenter: BRA-SP-OLTP
==========
Address          Rack        Status State   Load            Owns                Token                                       
                                                                                8611242815775010767                         
172.18.0.2       rack1       Up     Normal  85.73 KiB       48.85%              -9017829325322190053                        
172.18.0.3       rack1       Up     Normal  80.54 KiB       51.15%              -8422526956361790802                        
172.18.0.2       rack1       Up     Normal  85.73 KiB       48.85%              -7942073444096610513                        
172.18.0.2       rack1       Up     Normal  85.73 KiB       48.85%              -7206157751385716872                        
172.18.0.3       rack1       Up     Normal  80.54 KiB       51.15%              -6545462626848804773 
```

```
nodetool describering -- teste
```

Output
```
root@faed7a313b07:~# nodetool describering -- teste
Schema Version:0b8f1313-7319-353e-aff0-3c23018dda7c
TokenRange: 
        TokenRange(start_token:2049906906371655395, end_token:2702379818519904657, endpoints:[172.18.0.3], rpc_endpoints:[172.18.0.3], endpoint_details:[EndpointDetails(host:172.18.0.3, datacenter:BRA-SP-OLTP, rack:rack1)])
        TokenRange(start_token:-6545462626848804773, end_token:-6126784943938115215, endpoints:[172.18.0.2], rpc_endpoints:[172.18.0.2], endpoint_details:[EndpointDetails(host:172.18.0.2, datacenter:BRA-SP-OLTP, rack:rack1)])
        TokenRange(start_token:271272605548068267, end_token:925891490664097384, endpoints:[172.18.0.3], rpc_endpoints:[172.18.0.3], endpoint_details:[EndpointDetails(host:172.18.0.3, datacenter:BRA-SP-OLTP, rack:rack1)])
        TokenRange(start_token:8611242815775010767, end_token:-9017829325322190053, endpoints:[172.18.0.2], rpc_endpoints:[172.18.0.2], endpoint_details:[EndpointDetails(host:172.18.0.2, datacenter:BRA-SP-OLTP, rack:rack1)])
        TokenRange(start_token:-1366829871956750069, end_token:-706872846968045808, endpoints:[172.18.0.2], rpc_endpoints:[172.18.0.2], endpoint_details:[EndpointDetails(host:172.18.0.2, datacenter:BRA-SP-OLTP, rack:rack1)])
        TokenRange(start_token:6521890794692550939, end_token:7139871826594751016, endpoints:[172.18.0.3], rpc_endpoints:[172.18.0.3], endpoint_details:[EndpointDetails(host:172.18.0.3, datacenter:BRA-SP-OLTP, rack:rack1)]) 
```

###

### Instalando o driver
```
pip install cassandra-driver
```

### Iniciando o python3
```
python3
```

### CREATE TABLE
```
from cassandra.cluster import Cluster

cluster = Cluster(['127.0.0.1'])
session = cluster.connect('infobarbank')

rows = session.execute('CREATE TABLE infobarbank.cliente(id uuid PRIMARY KEY, cpf text, nome text);')

```

### INSERT
```
from cassandra.cluster import Cluster

cluster = Cluster(['127.0.0.1'])
session = cluster.connect('infobarbank')

session.execute("insert into infobarbank.cliente(id, cpf, nome) VALUES(2b167754-1017-11ed-861d-0242ac120002, '***.790.738-**', 'SIDINEIDE NONATO DE SA');")

session.execute("insert into infobarbank.cliente(id, cpf, nome) VALUES(2b1678f8-1017-11ed-861d-0242ac120002, '***.416.131-**', 'NEUZA EVANGELISTA FERREIRA');")

session.execute("insert into infobarbank.cliente(id, cpf, nome) VALUES(2b1679fc-1017-11ed-861d-0242ac120002, '***.568.112-**', 'MARIVALDA KANAMARY');")

session.execute("insert into infobarbank.cliente(id, cpf, nome) VALUES(2b167bbe-1017-11ed-861d-0242ac120002, '***.150.512-**', 'JUCILENE MOREIRA CRUZ');")

session.execute("insert into infobarbank.cliente(id, cpf, nome) VALUES(2b167cb8-1017-11ed-861d-0242ac120002, '***.615.942-**', 'GRACIMAR BRASIL GUERRA');")

session.execute("insert into infobarbank.cliente(id, cpf, nome) VALUES(2b167e52-1017-11ed-861d-0242ac120002, '***.264.482-**', 'ALDENORA VIANA MOREIRA');")

session.execute("insert into infobarbank.cliente(id, cpf, nome) VALUES(2b167fa6-1017-11ed-861d-0242ac120002, '***.434.715-**', 'VERA LUCIA RODRIGUES SENA');")

session.execute("insert into infobarbank.cliente(id, cpf, nome) VALUES(2b1694f0-1017-11ed-861d-0242ac120002, '***.469.755-**', 'MARCOLINO CONCEICAO DE ALMEIDA');")

session.execute("insert into infobarbank.cliente(id, cpf, nome) VALUES(2b1696a8-1017-11ed-861d-0242ac120002, '***.777.135-**', 'IVONE GLAUCIA VIANA DUTRA');")

session.execute("insert into infobarbank.cliente(id, cpf, nome) VALUES(2b16989c-1017-11ed-861d-0242ac120002, '***.881.955-**', 'LUCILIA ROSA LIMA PEREIRA');")

session.execute("insert into infobarbank.cliente(id, cpf, nome) VALUES(2b169a2c-1017-11ed-861d-0242ac120002, '***.580.583-**', 'FRANCISCA SANDRA FEITOSA');")
```

### SELECT
```
from cassandra.cluster import Cluster

cluster = Cluster(['127.0.0.1'])
session = cluster.connect('infobarbank')

rows = session.execute('SELECT id, cpf, nome FROM cliente')

for cliente in rows:
    print (cliente.nome)

```


### UPDATE
```
from cassandra.cluster import Cluster

cluster = Cluster(['127.0.0.1'])
session = cluster.connect('infobarbank')

session.execute("UPDATE infobarbank.cliente SET nome = 'DAVE BRUBECK' WHERE id = 2b164bd0-1017-11ed-861d-0242ac120002")

rows = session.execute('SELECT id, cpf, nome FROM cliente WHERE id = 2b164bd0-1017-11ed-861d-0242ac120002')

for cliente in rows:
    print (cliente.nome)

```

### DELETE
```
from cassandra.cluster import Cluster

cluster = Cluster(['127.0.0.1'])
session = cluster.connect('infobarbank')

session.execute("DELETE FROM infobarbank.cliente WHERE id = 2b164bd0-1017-11ed-861d-0242ac120002")

rows = session.execute('SELECT id, cpf, nome FROM cliente WHERE id = 2b164bd0-1017-11ed-861d-0242ac120002')

for cliente in rows:
    print (cliente.nome)

```
