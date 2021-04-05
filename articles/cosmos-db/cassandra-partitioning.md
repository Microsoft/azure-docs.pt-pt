---
title: Partição em Azure Cosmos DB Cassandra API
description: Conheça a partição em Azure Cosmos DB Cassandra API
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: ba615d3e41393afe007238a0fe1e694732ad123e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93087643"
---
# <a name="partitioning-in-azure-cosmos-db-cassandra-api"></a>Partição em Azure Cosmos DB Cassandra API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Este artigo descreve como a partição funciona em Azure Cosmos DB Cassandra API. 

A API Cassandra usa partição para escalar as tabelas individuais num espaço-chave para atender às necessidades de desempenho da sua aplicação. As divisórias são formadas com base no valor de uma chave de partição que está associada a cada registo numa tabela. Todos os registos de uma partição têm o mesmo valor de chave de partição. A Azure Cosmos DB gere de forma transparente e automática a colocação de divisórias através dos recursos físicos para satisfazer de forma eficiente as necessidades de escalabilidade e desempenho da tabela. À medida que os requisitos de produção e armazenamento de uma aplicação aumentam, a Azure Cosmos DB move-se e equilibra os dados através de um maior número de máquinas físicas.

Do ponto de vista do desenvolvedor, a partição comporta-se da mesma forma para a Azure Cosmos DB Cassandra API como na [nativa Apache Cassandra](https://cassandra.apache.org/). No entanto, há algumas diferenças nos bastidores. 


## <a name="differences-between-apache-cassandra-and-azure-cosmos-db"></a>Diferenças entre Apache Cassandra e Azure Cosmos DB

Em Azure Cosmos DB, cada máquina em que as divisórias são armazenadas é por si só referida como uma [divisória física.](partitioning-overview.md#physical-partitions) A partição física é semelhante a uma Máquina Virtual; uma unidade computacional dedicada, ou conjunto de recursos físicos. Cada divisória armazenada nesta unidade de computação é referida como uma [partição lógica](partitioning-overview.md#logical-partitions) em Azure Cosmos DB. Se já conhece Apache Cassandra, pode pensar em divisórias lógicas da mesma forma que pensa em divisórias regulares em Cassandra. 

Apache Cassandra recomenda um limite de 100-MB sobre o tamanho de um dado que pode ser armazenado numa divisória. A API cassandra para Azure Cosmos DB permite até 20 GB por partição lógica, e até 30GB de dados por partição física. Em Azure Cosmos DB, ao contrário de Apache Cassandra, a capacidade de computação disponível na partição física é expressa usando uma única métrica chamada unidades de [pedido,](request-units.md)que lhe permite pensar na sua carga de trabalho em termos de pedidos (leituras ou escritos) por segundo, em vez de núcleos, memória ou IOPS. Isto pode tornar o planeamento de capacidade mais simples, uma vez que você entenda o custo de cada pedido. Cada divisória física pode ter até 10000 RUs de computação disponíveis. Você pode aprender mais sobre opções de escalabilidade lendo o nosso artigo em [escala elástica](manage-scale-cassandra.md) em Cassandra API. 

Em Azure Cosmos DB, cada partição física consiste num conjunto de réplicas, também conhecidas como conjuntos de réplicas, com pelo menos 4 réplicas por partição. Isto contrasta com Apache Cassandra, onde é possível definir um fator de replicação de 1. No entanto, isto leva a uma baixa disponibilidade se o único nó com os dados descer. Na API de Cassandra há sempre um fator de replicação de 4 (quórum de 3). A Azure Cosmos DB gere automaticamente os conjuntos de réplicas, enquanto estes precisam de ser mantidos usando várias ferramentas em Apache Cassandra. 

Apache Cassandra tem um conceito de fichas, que são hashes de chaves de partição. Os tokens baseiam-se num haxixe murmurante de 3 64 byte, com valores que variam entre -2^63 e -2^63 - 1. Esta gama é comumente referida como o "anel simbólico" em Apache Cassandra. O anel simbólico é distribuído em gamas simbólicas, e estas gamas estão divididas entre os nós presentes num aglomerado nativo de Apache Cassandra. A partição para Azure Cosmos DB é implementada de forma semelhante, exceto que usa um algoritmo de haxixe diferente, e tem um anel de ficha interna maior. No entanto, externamente expomos a mesma gama simbólica que a Apache Cassandra, ou seja, -2^63 a -2^63 - 1.


## <a name="primary-key"></a>Chave primária

Todas as tabelas da API de Cassandra devem ter um `primary key` definido. A sintaxe para uma chave primária é mostrada abaixo:

```shell
column_name cql_type_definition PRIMARY KEY
```

Suponha que queremos criar uma tabela de utilizadores, que armazena mensagens para diferentes utilizadores:

```shell
CREATE TABLE uprofile.user ( 
   id UUID PRIMARY KEY, 
   user text,  
   message text);
```

Neste projeto, definimos o `id` campo como a chave primária. A chave primária funciona como o identificador para o registo na tabela e também é usado como a chave de partição em Azure Cosmos DB. Se a chave primária for definida da forma previamente descrita, haverá apenas um único registo em cada partição. Isto resultará numa distribuição perfeitamente horizontal e escalável ao escrever dados para a base de dados, e é ideal para casos de procura de valor-chave. A aplicação deve fornecer a chave primária sempre que ler os dados da tabela, para maximizar o desempenho da leitura. 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning.png" alt-text="partições" border="false":::


## <a name="compound-primary-key"></a>Chave primária composta

Apache Cassandra também tem um conceito  `compound keys` de. Um composto `primary key` é composto por mais de uma coluna; a primeira coluna é a , e `partition key` quaisquer colunas adicionais são a `clustering keys` . A sintaxe para a `compound primary key` é mostrada abaixo:

```shell
PRIMARY KEY (partition_key_column_name, clustering_column_name [, ...])
```

Suponha que queremos alterar o design acima e tornar possível recuperar eficientemente mensagens para um determinado utilizador:

```shell
CREATE TABLE uprofile.user (
   user text,  
   id int, 
   message text, 
   PRIMARY KEY (user, id));
```

Neste desenho, estamos agora definindo `user` como a chave de partição, e como a chave de `id` agrupamento. Pode definir quantas teclas de agrupamento desejar, mas cada valor (ou uma combinação de valores) para a chave de agrupamento deve ser único, de modo a que sejam adicionados múltiplos registos à mesma partição, por exemplo:

```shell
insert into uprofile.user (user, id, message) values ('theo', 1, 'hello');
insert into uprofile.user (user, id, message) values ('theo', 2, 'hello again');
```

Quando os dados são devolvidos, é classificado pela chave de agrupamento, como esperado em Apache Cassandra:

:::image type="content" source="./media/cassandra-partitioning/select-from-pk.png" alt-text="Screenshot que mostra os dados devolvidos que são classificados pela chave de clustering.":::

Com os dados modelados desta forma, vários registos podem ser atribuídos a cada partição, agrupados pelo utilizador. Assim, podemos emitir uma consulta que seja encaminhada de forma eficiente pelo `partition key` (neste `user` caso,) para obter todas as mensagens para um determinado utilizador. 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning2.png" alt-text="Diagrama que mostra como vários registos podem ser atribuídos a cada partição, agrupados pelo utilizador." border="false":::


## <a name="composite-partition-key"></a>Chave de partição composta

As teclas de partição composta funcionam essencialmente da mesma forma que as teclas compostas, exceto que pode especificar várias colunas como uma chave de partição composta. A seguinte alteração das teclas de partição composta é mostrada abaixo:

```shell
PRIMARY KEY (
   (partition_key_column_name[, ...]), 
    clustering_column_name [, ...]);
```
Por exemplo, pode ter o seguinte, onde a combinação única de e formaria a chave de `firstname` `lastname` partição, e `id` é a chave de agrupamento:

```shell
CREATE TABLE uprofile.user ( 
   firstname text, 
   lastname text,
   id int,  
   message text, 
   PRIMARY KEY ((firstname, lastname), id) );
```

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a partição e a escala horizontal em Azure Cosmos DB](partitioning-overview.md).
* Conheça a [produção a provisionada em Azure Cosmos DB](request-units.md).
* Conheça a [distribuição global em Azure Cosmos DB](distribute-data-globally.md).
