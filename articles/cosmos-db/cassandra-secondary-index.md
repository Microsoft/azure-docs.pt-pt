---
title: Indexação na conta Azure Cosmos DB Cassandra API
description: Saiba como funciona a indexação secundária na conta Azure Azure Cosmos DB Cassandra API.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: fc9c7c2f06b9d39243b8593b20ddeb7ffa2f886c
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092369"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Indexação secundária em Azure Cosmos DB Cassandra API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

A API Cassandra em Azure Cosmos DB aproveita a infraestrutura de indexação subjacente para expor a força de indexação que é inerente à plataforma. No entanto, ao contrário do núcleo da API SQL, a API Cassandra em Azure Cosmos DB não indexa todos os atributos por padrão. Em vez disso, apoia a indexação secundária para criar um índice sobre certos atributos, que se comporta da mesma forma que Apache Cassandra.  

Em geral, não é aconselhável executar consultas de filtro nas colunas que não são divididas. Deve utilizar explicitamente a sintaxe de filtragem, o que resulta numa operação que pode não funcionar bem. Em Azure Cosmos DB você pode executar tais consultas em atributos de baixa cardinalidade porque eles se espalham através de divisórias para recuperar os resultados.

Não é aconselhável criar um índice numa coluna frequentemente atualizada. É prudente criar um índice quando se define a tabela. Isto garante que os dados e os índices estão num estado consistente. Caso crie um novo índice sobre os dados existentes, atualmente, não é possível acompanhar a mudança de índice para a tabela. Se você precisa acompanhar o progresso para esta operação, você tem que solicitar a mudança de progresso através de um [bilhete de apoio]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).


> [!NOTE]
> O índice secundário não é suportado nos seguintes objetos:
> - tipos de dados, tais como tipos de recolha congelada, decimal e tipos de variantes.
> - Colunas estáticas
> - Chaves de agrupamento

## <a name="indexing-example"></a>Exemplo de indexação

Em primeiro lugar, crie um espaço e tabela de chaves de amostra executando os seguintes comandos na ordem da concha CQL:

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

Em seguida, insira os dados do utilizador da amostra com os seguintes comandos:

```shell
insert into sampleks.t1(user_id,lastname) values (1, 'nishu');
insert into sampleks.t1(user_id,lastname) values (2, 'vinod');
insert into sampleks.t1(user_id,lastname) values (3, 'bat');
insert into sampleks.t1(user_id,lastname) values (5, 'vivek');
insert into sampleks.t1(user_id,lastname) values (6, 'siddhesh');
insert into sampleks.t1(user_id,lastname) values (7, 'akash');
insert into sampleks.t1(user_id,lastname) values (8, 'Theo');
insert into sampleks.t1(user_id,lastname) values (9, 'jagan');
```

Se tentar executar a seguinte declaração, encontrará um erro que lhe pede para `ALLOW FILTERING` usar: 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

Embora a API Cassandra suporte a FILTRAgem ALLOW, como mencionado na secção anterior, não é recomendado. Em vez disso, deve criar um índice no como mostrado no seguinte exemplo:

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
Depois de criar um índice no campo "último nome", pode agora executar a consulta anterior com sucesso. Com a Cassandra API em Azure Cosmos DB, não precisa fornecer um nome de índice. É utilizado um índice predefinido com `tablename_columnname_idx` formato. Por exemplo, ` t1_lastname_idx` é o nome de índice para a tabela anterior.

## <a name="dropping-the-index"></a>Queda do índice 
Precisa saber qual é o nome do índice para baixar o índice. Faça o `desc schema` comando para obter a descrição da sua mesa. A saída deste comando inclui o nome do índice no formato `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)` . Em seguida, pode utilizar o nome do índice para baixar o índice como mostrado no seguinte exemplo:

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>Passos seguintes
* Saiba como funciona [a indexação automática](index-overview.md) em Azure Cosmos DB
* [Funcionalidades do Apache Cassandra suportadas pela API para Cassandra do Azure Cosmos DB](cassandra-support.md)
