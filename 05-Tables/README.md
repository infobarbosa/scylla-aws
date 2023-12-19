# Operações de tabelas
Author: Prof. Barbosa<br>
Contact: infobarbosa@gmail.com<br>
Github: [infobarbosa](https://github.com/infobarbosa)

Nesse laboratório vamos trabalhar com tabelas do Cassandra/Scylla.

Abra o `cqlsh`:
```
cqlsh
```

Crie uma nova **keyspace** para este lab:
```
CREATE KEYSPACE infobarbank2
    WITH REPLICATION = {
        'class' : 'SimpleStrategy', 
        'replication_factor':1  
    };
```

### CREATE TABLE
```
CREATE TABLE infobarbank2.cliente(
    id uuid PRIMARY KEY, 
    cpf text, 
    nome text
);
```

### DESCRIBE TABLE
```
DESCRIBE TABLE infobarbank2.cliente
```

O output deve ser algo assim:
```
cqlsh> DESCRIBE TABLE infobarbank2.cliente

CREATE TABLE infobarbank2.cliente (
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

### INSERT
```
INSERT INTO infobarbank2.cliente(
    id, 
    cpf, 
    nome
) 
VALUES (
    6ad8b386-1015-11ed-861d-0242ac120002, 
    '11111111111', 
    'marcelo barbosa'
);
```

### SELECT
```
SELECT * 
FROM infobarbank2.cliente;
```

##### Busca pela chave
```
SELECT * 
FROM infobarbank2.cliente 
WHERE id = 6ad8b386-1015-11ed-861d-0242ac120002;
```
Output:
```
 id                                   | cpf         | nome
--------------------------------------+-------------+-----------------
 6ad8b386-1015-11ed-861d-0242ac120002 | 11111111111 | marcelo barbosa
```

##### Busca por um campo não-chave
```
SELECT * 
FROM infobarbank2.cliente 
WHERE nome = 'marcelo barbosa';
```

Output:
```
InvalidRequest: Error from server: code=2200 [Invalid query] message="Cannot execute this query as it might involve data filtering and thus may have unpredictable performance. If you want to execute this query despite the performance unpredictability, use ALLOW FILTERING"
```

### UPDATE
```
UPDATE infobarbank2.cliente 
SET nome = 'marcelo b.' 
WHERE id = 6ad8b386-1015-11ed-861d-0242ac120002;
```

### DELETE
```
DELETE FROM infobarbank2.cliente 
WHERE id = 6ad8b386-1015-11ed-861d-0242ac120002;
```

### TRUNCATE TABLE
```
TRUNCATE TABLE infobarbank2.cliente;
```

### DROP TABLE
```
DROP TABLE infobarbank2.cliente;
```
