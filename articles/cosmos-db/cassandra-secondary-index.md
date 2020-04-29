---
title: Indexação na conta API da Azure Cosmos DB Cassandra
description: Saiba como funciona a indexação secundária na conta API da Azure Azure Cosmos DB Cassandra.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 7de38097acdbfa1f9c9b90f3051c68dec5465b32
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80758030"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Indexação secundária em Azure Cosmos DB Cassandra API

A Cassandra API em Azure Cosmos DB aproveita a infraestrutura de indexação subjacente para expor a força de indexação que é inerente à plataforma. No entanto, ao contrário do núcleo SQL API, Cassandra API em Azure Cosmos DB não indexa todos os atributos por padrão. Em vez disso, suporta a indexação secundária para criar um índice sobre certos atributos, que se comporta da mesma forma que Apache Cassandra.  

Em geral, não é aconselhável executar consultas de filtro nas colunas que não são divididas. Deve utilizar explicitamente a sintaxe DE FILTRAÇÃO DE FILTRAÇÃO, o que resulta numa operação que pode não funcionar bem. No Azure Cosmos DB você pode executar tais consultas em atributos de baixa cardinalidade porque eles se espalham através de divisórias para recuperar os resultados.

Não é aconselhável criar um índice numa coluna frequentemente atualizada. É prudente criar um índice quando se define a tabela. Isto garante que os dados e índices estão num estado consistente. Caso crie um novo índice sobre os dados existentes, atualmente, não pode acompanhar a mudança de progresso do índice para a tabela. Se precisa acompanhar os progressos desta operação, tem de solicitar a alteração de progresso através de um bilhete de [apoio.]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)


> [!NOTE]
> O índice secundário não é suportado nos seguintes objetos:
> - tipos de dados, tais como tipos de recolha congelados, tipos decimais e variantes.
> - Colunas estáticas
> - Chaves de agrupamento

## <a name="indexing-example"></a>Exemplo de indexação

Em primeiro lugar, crie um espaço de chave e uma tabela de amostras executando os seguintes comandos no pedido de concha CQL:

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

Se tentar executar a seguinte declaração, terá um erro que `ALLOW FILTERING`lhe pede para utilizar: 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

Embora a Cassandra API suportes ALLOW FILTERING, como mencionado na secção anterior, não é recomendado. Em vez disso, deve criar um índice no exemplo que se segue:

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
Depois de criar um índice no campo "apelido", pode agora executar a consulta anterior com sucesso. Com Cassandra API em Azure Cosmos DB, você não precisa fornecer um nome de índice. É utilizado um `tablename_columnname_idx` índice predefinido com formato. Por exemplo, ` t1_lastname_idx` é o nome do índice para a tabela anterior.

## <a name="dropping-the-index"></a>Baixando o índice 
Precisa saber qual é o nome do índice para baixar o índice. Mande `desc schema` o comando para obter a descrição da sua mesa. A saída deste comando inclui o `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)`nome do índice no formato . Em seguida, pode utilizar o nome do índice para baixar o índice, como mostra o seguinte exemplo:

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>Passos seguintes
* Saiba como funciona [a indexação automática](index-overview.md) em Azure Cosmos DB
* [Funcionalidades do Apache Cassandra suportadas pela API para Cassandra do Azure Cosmos DB](cassandra-support.md)
