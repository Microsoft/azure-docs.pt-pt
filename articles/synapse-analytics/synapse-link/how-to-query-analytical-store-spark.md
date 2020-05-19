---
title: Consulta Azure Cosmos DB Analytical Store com Apache Spark para Azure Synapse Analytics
description: Como consultar azure Cosmos DB analítico com Apache Spark para Azure Synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 05/06/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 02d4b6a636bff5ef11686abba6efb52f45f04779
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83600070"
---
# <a name="query-azure-cosmos-db-analytical-store-with-apache-spark-for-azure-synapse-analytics"></a>Consulta Azure Cosmos DB Analytical Store com Apache Spark para Azure Synapse Analytics

Este artigo dá alguns exemplos de como pode interagir com a loja analítica a partir de gestos Synapse. Os gestos são visíveis quando clica à direita num recipiente. Com gestos, pode rapidamente gerar código e adaptá-lo às suas necessidades. Os gestos também são perfeitos para descobrir dados com um único clique.

## <a name="load-to-dataframe"></a>Carregar para DataFrame

Neste passo, você vai ler dados da loja analítica Azure Cosmos DB num Spark DataFrame. Apresentará 10 linhas do DataFrame chamado ***df***. Uma vez que os seus dados estão no dataframe, pode realizar análises adicionais.

Esta operação não afeta a loja transacional.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .load()

df.show(10)
```

O gesto de código equivalente em **Scala** seria o seguinte código:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    load()
```

## <a name="create-spark-table"></a>Criar mesa de faísca

Neste gesto, você vai criar uma mesa Spark apontando para o recipiente que selecionou. Esta operação não incorre em nenhum movimento de dados. Se decidir apagar essa tabela, o recipiente subjacente (e a correspondente loja analítica) não serão afetados. 

Este cenário é conveniente reutilizar tabelas através de ferramentas de terceiros e fornecer acessibilidade aos dados para o tempo de execução.

```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService 'INFERRED',
    spark.cosmos.container 'INFERRED'
)
```

## <a name="write-dataframe-to-container"></a>Escreva DataFrame para recipiente

Neste gesto, escreverá uma moldura de dados num recipiente. Esta operação terá impacto no desempenho transacional e consumirá Unidades de Pedido. A utilização do desempenho transacional Da Azure Cosmos DB é ideal para escrever transações. Certifique-se de que substitui o **SEUDATAFRAME** pelo quadro de dados a que pretende reescrever.

```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")


YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

O gesto de código equivalente em **Scala** seria o seguinte código:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>Fluxo de carga DataFrame do recipiente
Neste gesto, utilizará a capacidade de Streaming de Faíscas para carregar dados de um contentor para um quadro de dados. Os dados serão armazenados na conta principal do lago de dados (e sistema de ficheiros) que você ligou ao espaço de trabalho. 

Se a pasta */localReadCheckpointFolder* não for criada, será criada automaticamente. Esta operação terá impacto no desempenho transacional da Azure Cosmos DB.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

O gesto de código equivalente em **Scala** seria o seguinte código:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamTestRevin2").
    load()
```

## <a name="write-streaming-dataframe-to-container"></a>Escreva streaming DataFrame para recipiente
Neste gesto, irá escrever um quadro de dados de streaming no recipiente Azure Cosmos DB que selecionou. Se a pasta */localReadCheckpointFolder* não for criada, será criada automaticamente. Esta operação terá impacto no desempenho transacional da Azure Cosmos DB.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "INFERRED")\
        .option("spark.cosmos.container", "trafficSourceColl_sink")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

O gesto de código equivalente em **Scala** seria o seguinte código:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "pySparkSamplesDb").
            option("spark.cosmos.container", "test2").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```
## <a name="next-steps"></a>Passos seguintes

* [Saiba o que é apoiado entre Synapse e Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Ligue-se à Ligação Synapse para Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
