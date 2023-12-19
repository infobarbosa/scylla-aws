# Criando Keyspaces
Author: Prof. Barbosa<br>
Contact: infobarbosa@gmail.com<br>
Github: [infobarbosa](https://github.com/infobarbosa)

Abra o `cqlsh`:
```
cqlsh
```

Criando uma keyspace simples:
```
CREATE KEYSPACE infobarbank1
    WITH REPLICATION = {
        'class' : 'SimpleStrategy', 
        'replication_factor':1  
    };
```

Uma `keyspace` é um repositório lógico para tabelas. <br>
Em outros bancos de dados é equivalente a um schema ou database.<br>
A sintaxe para criação de uma keyspace é:
```
CREATE  KEYSPACE [IF NOT EXISTS] keyspace_name 
   WITH REPLICATION = { 
      'class' : 'SimpleStrategy', 'replication_factor' : N } 
     | 'class' : 'NetworkTopologyStrategy', 
       'dc1_name' : N [, ...] 
   }
   [AND DURABLE_WRITES =  true|false] ;
```

É possível criar keyspaces diferentes para cada assunto. Ex.: pedidos, faturamento, contábil, etc.<br>
Outra abordagem é utilizar keyspaces diferentes para cada microserviço.<br>
A segmentação facilita a administração das aplicações.

### Descrevendo keyspaces
Caso você queira saber a definição de uma `keyspace`, é possível via comando `describe`:
```
describe keyspace infobarbank1;
```

### Outros exemplos

Em **produção** normalmente utilizaremos a classe `NetworkTopologyStrategy` para distribuição das réplicas em diversos datacenters, zonas de disponibilidade, regiões geográficas, etc.
A seguir proponho alguns exemplos para debate.<br> 

No caso abaixo estamos criando a keyspace **infobarbank1** com distribuição geográfica em 3 países diferentes: Brasil, EUA e Alemanha.<br>
Perceba que é possível determinar quantas cópias do dado queremos em cada cidade/região. Nsse caso 3 cópias no Brasil, 3 cópias nos EUA e 2 cópias na Alemanha.
```
CREATE KEYSPACE infobarbank1
  WITH REPLICATION = {
   'class'  : 'NetworkTopologyStrategy', 
   'bra-sp' : 3 , // São Paulo/Brasil
   'usa-ny' : 3 , // Nova Iorque/Estados Unidos
   'deu-fr' : 2   // Frankfurt/Alemanha
  };
```  

A seguir temos uma situação diferente, apenas uma região (Brasil). Porém, nesse caso também determinamos que há quatro instalações diferentes em duas zonas de disponibilidade.<br>
Trata-se de um único cluster segmentado em dois propósitos, OLTP (transacional) e OLAP (analítico).
Nos nós transacionais determinamos 3 cópias em casa zona (totalizando 6 cópias transacionais) enquanto que nos nós analíticos apenas 1 cópia em cada zona (totalizando 2 cópias analíticas).
```
CREATE KEYSPACE infobarbank1
  WITH REPLICATION = {
   'class'  : 'NetworkTopologyStrategy', 
   'bra-sp-az1-oltp' : 3 , // Transacional AZ1 em São Paulo/Brasil
   'bra-sp-az2-oltp' : 3 , // Transacional AZ2 em São Paulo/Brasil
   'bra-sp-az1-olap' : 1 , // Analítico AZ1 em São Paulo/Brasil
   'bra-sp-az2-olap' : 1   // Analítico AZ2 em São Paulo/Brasil
  };
```  

O comando a seguir entrega exatamente a mesma topologia do comando anterior. Porém, depende da instalação executada pelo administrador.<br>
Por ser um tópico mais avançado, discutiremos em sala de aula a diferença no nível físico. 
```
CREATE KEYSPACE infobarbank1
  WITH REPLICATION = {
   'class'  : 'NetworkTopologyStrategy', 
   'bra-sp-oltp' : 6, 
   'bra-sp-olap' : 2
  };
``` 
