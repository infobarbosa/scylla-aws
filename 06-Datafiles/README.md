# Observação de datafiles
Author: Prof. Barbosa<br>
Contact: infobarbosa@gmail.com<br>
Github: [infobarbosa](https://github.com/infobarbosa)

### Objetivo
Observar como sstables (data files) são geradas em disco.

### Método
A proposta agora é recriar a tabela e inserir, atualizar e deletar diversos registros.
A cada etapa vamos executar o comando `nodetool flush` e então observar como o Cassandra gerencia os arquivos (sstables) no sistema operacional.


Navegue para o diretório `/var/lib/scylla/data`, local padrão de armazenamento de dados do Cassandra. 
```
cd /var/lib/scylla/data
```

Liste os arquivos e subdiretórios presentes:
```
ls -la
```

Output:
```
ubuntu $ ll
total 40
drwxr-xr-x 10 scylla scylla 4096 Jan 17 01:31 ./
drwxr-xr-x  7 scylla scylla 4096 Jan 17 01:26 ../
-rwx------  1 scylla scylla    0 Jan 17 01:26 .lock*
drwxr-xr-x  2 scylla scylla 4096 Jan 17 01:30 infobarbank1/
drwxr-xr-x  3 scylla scylla 4096 Jan 17 01:31 infobarbank2/
drwxr-xr-x 33 scylla scylla 4096 Jan 17 01:26 system/
drwxr-xr-x  5 scylla scylla 4096 Jan 17 01:26 system_auth/
drwxr-xr-x  6 scylla scylla 4096 Jan 17 01:26 system_distributed/
drwxr-xr-x  3 scylla scylla 4096 Jan 17 01:26 system_distributed_everywhere/
drwxr-xr-x 17 scylla scylla 4096 Jan 17 01:26 system_schema/
drwxr-xr-x  7 scylla scylla 4096 Jan 17 01:26 system_traces/
```
Perceba que o diretório `/var/lib/scylla/data` contém apenas subdiretórios de sistema e de keyspaces dos laboratórios anteriores.<br>

### CREATE KEYSPACE
```
cqlsh -e "CREATE KEYSPACE infobarbank3
            WITH replication = {
                'class': 'SimpleStrategy',
                'replication_factor': 1};"
```

Agora vamos criar uma tabela `cliente`.
### `cqlsh` CREATE TABLE
```
cqlsh -e "CREATE TABLE infobarbank3.cliente(
            id uuid PRIMARY KEY,
            cpf text,
            nome text);"
```

### DESCRIBE TABLE
```
cqlsh -e "DESCRIBE TABLE infobarbank3.cliente;"
```

O output deve ser algo assim:
```
ubuntu $ cqlsh -e "DESCRIBE TABLE infobarbank3.cliente;"

CREATE TABLE infobarbank3.cliente (
    id uuid PRIMARY KEY,
    cpf text,
    nome text
) WITH bloom_filter_fp_chance = 0.01
    AND caching = {'keys': 'ALL', 'rows_per_partition': 'ALL'}
    AND comment = ''
    AND compaction = {'class': 'SizeTieredCompactionStrategy'}
    AND compression = {'sstable_compression': 'org.apache.cassandra.io.compress.LZ4Compressor'}
    AND crc_check_chance = 1.0
    AND dclocal_read_repair_chance = 0.0
    AND default_time_to_live = 0
    AND gc_grace_seconds = 864000
    AND max_index_interval = 2048
    AND memtable_flush_period_in_ms = 0
    AND min_index_interval = 128
    AND read_repair_chance = 0.0
    AND speculative_retry = '99.0PERCENTILE';
```

Se você obteve o output acima então a tabela foi criada corretamente.

Agora vamos voltar ao `bash` e verificar o que ocorreu em disco.
```
ls -la
```

Output:
```
ubuntu $ ls -la
total 44
drwxr-xr-x 11 scylla scylla 4096 Jan 17 01:45 .
drwxr-xr-x  7 scylla scylla 4096 Jan 17 01:26 ..
-rwx------  1 scylla scylla    0 Jan 17 01:26 .lock
drwxr-xr-x  2 scylla scylla 4096 Jan 17 01:30 infobarbank1
drwxr-xr-x  3 scylla scylla 4096 Jan 17 01:31 infobarbank2
drwxr-xr-x  3 scylla scylla 4096 Jan 17 01:46 infobarbank3
drwxr-xr-x 33 scylla scylla 4096 Jan 17 01:26 system
drwxr-xr-x  5 scylla scylla 4096 Jan 17 01:26 system_auth
drwxr-xr-x  6 scylla scylla 4096 Jan 17 01:26 system_distributed
drwxr-xr-x  3 scylla scylla 4096 Jan 17 01:26 system_distributed_everywhere
drwxr-xr-x 17 scylla scylla 4096 Jan 17 01:26 system_schema
drwxr-xr-x  7 scylla scylla 4096 Jan 17 01:26 system_traces
```

Vamos navegar para o diretório `/var/lib/scylla/data/infobarbank3`:
```
cd /var/lib/scylla/data/infobarbank3
```

Listando os subdiretórios
```
pwd

ls -la
```
Output:
```
ubuntu $ pwd
/var/lib/scylla/data/infobarbank3
ubuntu $ 
ubuntu $ ls -la  
total 12
drwxr-xr-x  3 scylla scylla 4096 Jan 17 01:46 .
drwxr-xr-x 11 scylla scylla 4096 Jan 17 01:45 ..
drwxr-xr-x  4 scylla scylla 4096 Jan 17 01:46 cliente-b5e14ea0960811ed9e4d98a449836cd1
```

**ATENÇÃO!** <br>
Perceba que foi criado um diretório com o nome da tabela (cliente) concatenado com um UUID aleatório.<br>
Por ser aleatório então você deverá adequar manualmente o comando a seguir para reproduzir exatamente o nome do diretório criado.

Vamos verificar os objetos dentro desse diretório:
```
cd cliente-b5e14ea0960811ed9e4d98a449836cd1/

ls -la
```

Output:
```
ubuntu $ ls -la
total 16
drwxr-xr-x 4 scylla scylla 4096 Jan 17 01:46 .
drwxr-xr-x 3 scylla scylla 4096 Jan 17 01:46 ..
drwxr-xr-x 2 scylla scylla 4096 Jan 17 01:46 staging
drwxr-xr-x 2 scylla scylla 4096 Jan 17 01:46 upload
ubuntu $ 
```

Veja que não existem objetos além de uma pastas de controle.<br>
Agora vamos executar um primeiro lote de inserções na tabela.

### `cqlsh` INSERT
Inserindo primeiro lote de registros
```
cqlsh -e "
insert into infobarbank3.cliente(id, cpf, nome) VALUES(2b162060-1017-11ed-861d-0242ac120002, '***.568.112-**', 'MARIVALDA KANAMARY');
insert into infobarbank3.cliente(id, cpf, nome) VALUES(2b16242a-1017-11ed-861d-0242ac120002, '***.150.512-**', 'JUCILENE MOREIRA CRUZ');
insert into infobarbank3.cliente(id, cpf, nome) VALUES(2b16256a-1017-11ed-861d-0242ac120002, '***.615.942-**', 'GRACIMAR BRASIL GUERRA');
insert into infobarbank3.cliente(id, cpf, nome) VALUES(2b16353c-1017-11ed-861d-0242ac120002, '***.264.482-**', 'ALDENORA VIANA MOREIRA');
insert into infobarbank3.cliente(id, cpf, nome) VALUES(2b1636ae-1017-11ed-861d-0242ac120002, '***.434.715-**', 'VERA LUCIA RODRIGUES SENA');
insert into infobarbank3.cliente(id, cpf, nome) VALUES(2b16396a-1017-11ed-861d-0242ac120002, '***.777.135-**', 'IVONE GLAUCIA VIANA DUTRA');
insert into infobarbank3.cliente(id, cpf, nome) VALUES(2b163bcc-1017-11ed-861d-0242ac120002, '***.881.955-**', 'LUCILIA ROSA LIMA PEREIRA');
insert into infobarbank3.cliente(id, cpf, nome) VALUES(2b163cda-1017-11ed-861d-0242ac120002, '***.580.583-**', 'FRANCISCA SANDRA FEITOSA');
insert into infobarbank3.cliente(id, cpf, nome) VALUES(2b163dde-1017-11ed-861d-0242ac120002, '***.655.193-**', 'BRUNA DE BRITO PAIVA');
insert into infobarbank3.cliente(id, cpf, nome) VALUES(2b163ed8-1017-11ed-861d-0242ac120002, '***.708.013-**', 'LUCILENE PAULO BARBOSA');"

cqlsh -e "SELECT COUNT(1) 
          FROM infobarbank3.cliente;"
```

```
ls -la
```

Provavelmente não houve alteração na disposição dos arquivos.
Agora vamos forçar o flush da **memtable** para o disco:
```
nodetool flush
```

Após a execução de `nodetool flush`, uma nova listagem de arquivos (`ls -la`) terá um output como:
```
ubuntu $ ls -la
total 16
drwxr-xr-x 4 scylla scylla 4096 Jan 17 01:46 .
drwxr-xr-x 3 scylla scylla 4096 Jan 17 01:46 ..
drwxr-xr-x 2 scylla scylla 4096 Jan 17 01:46 staging
drwxr-xr-x 2 scylla scylla 4096 Jan 17 01:46 upload
ubuntu $ 
ubuntu $ nodetool flush
ubuntu $ ls -la
total 56
drwxr-xr-x 4 scylla scylla 4096 Jan 17 01:52 .
drwxr-xr-x 3 scylla scylla 4096 Jan 17 01:46 ..
-rw-r--r-- 1 scylla scylla   66 Jan 17 01:52 me-1-big-CompressionInfo.db
-rw-r--r-- 1 scylla scylla  495 Jan 17 01:52 me-1-big-Data.db
-rw-r--r-- 1 scylla scylla   10 Jan 17 01:52 me-1-big-Digest.crc32
-rw-r--r-- 1 scylla scylla   24 Jan 17 01:52 me-1-big-Filter.db
-rw-r--r-- 1 scylla scylla  208 Jan 17 01:52 me-1-big-Index.db
-rw-r--r-- 1 scylla scylla  299 Jan 17 01:52 me-1-big-Scylla.db
-rw-r--r-- 1 scylla scylla 4671 Jan 17 01:52 me-1-big-Statistics.db
-rw-r--r-- 1 scylla scylla   92 Jan 17 01:52 me-1-big-Summary.db
-rw-r--r-- 1 scylla scylla  102 Jan 17 01:52 me-1-big-TOC.txt
drwxr-xr-x 2 scylla scylla 4096 Jan 17 01:46 staging
drwxr-xr-x 2 scylla scylla 4096 Jan 17 01:46 upload
```

Perceba que vários arquivos de dados foram criados. A importância de cada um será explicada em sala de aula.

Por ora vamos examinar o arquivo `nb-1-big-Data.db`:
```
cat nb-1-big-Data.db
```

O output será parecido com isso:
```
ubuntu $ 
ubuntu $ cat me-1-big-Data.db 
        +;B"$***.881.955-*LUCILIA ROSA LIMA PEREIRAO/%jOB+IOp615.942OGRACIMAR BRASIL GUERL/ `L2&Ka568.11KMARIVALDA KANAMARY/$*GB*HR150.5H JaENE MOQ CRUZK/>KB+Kq708.013-KPAULO BARBOS*/6LB.Lb434.71yPVERA Q|DRIGUES SENO/=OB)Oa655.19BRUNA DE BRITO PAIVJ9B.Jb777.13IVONE GLA0VIAX#UT/<ڙB-OOa580.58FRANCISCA SANDRA FEIT6/5<NB+Na264.48ALDENORMOREIRAq ubuntu $ 
```
