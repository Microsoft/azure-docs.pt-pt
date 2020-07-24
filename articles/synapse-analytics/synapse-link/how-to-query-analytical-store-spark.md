---
title: Consulta Azure Cosmos DB Analytical Store (pré-visualização) com Apache Spark
description: Como consultar a Azure Cosmos DB analítica com Apache Spark para Azure Synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 05/06/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: a7ee04c922e4373414dc27ed2b7c98be605280e5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089112"
---
# <a name="query-azure-cosmos-db-analytical-store-preview-with-apache-spark-for-azure-synapse-analytics"></a>Consulta Azure Cosmos DB Analytical Store (pré-visualização) com Apache Spark para Azure Synapse Analytics

Este artigo dá exemplos sobre como você pode interagir com a loja analítica a partir de gestos da Sinapse. Os gestos são visíveis quando clica com o botão direito num contentor. Com os gestos, pode gerar rapidamente código e personalizá-lo de acordo com as suas necessidades. Os gestos também são perfeitos para detetar dados com um único clique.

## <a name="load-to-dataframe"></a>Carregar para DataFrame

Neste passo, você vai ler dados da loja analítica Azure Cosmos DB em um Spark DataFrame. Apresentará 10 linhas a partir do DataFrame chamado ***df***. Uma vez que os seus dados estão no dataframe, pode efetuar análises adicionais.

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

## <a name="create-spark-table"></a>Criar mesa spark

Neste gesto, criará uma mesa Spark apontando para o recipiente que selecionou. Esta operação não incorre em nenhum movimento de dados. Se decidir apagar essa tabela, o recipiente subjacente (e a correspondente loja analítica) não será afetado. 

Este cenário é conveniente para reutilizar tabelas através de ferramentas de terceiros e fornecer acessibilidade aos dados para o tempo de funcionação.

```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService 'INFERRED',
    spark.cosmos.container 'INFERRED'
)
```

## <a name="write-dataframe-to-container"></a>Escreva DataFrame para o recipiente

Neste gesto, você vai escrever um quadro de dados em um recipiente. Esta operação terá impacto no desempenho transacional e consumirá Unidades de Pedido. Usar o desempenho transacional da Azure Cosmos DB é ideal para escrever transações. Certifique-se de que substitui o **SEUDATAFRAME** pelo dataframe que pretende voltar a escrever.

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

## <a name="load-streaming-dataframe-from-container"></a>Carregar o streaming de dados Do recipiente
Neste gesto, utilizará a capacidade de Streaming spark para carregar dados de um recipiente para um dataframe. Os dados serão armazenados na conta principal do lago de dados (e sistema de ficheiros) que ligou ao espaço de trabalho. 

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

## <a name="write-streaming-dataframe-to-container"></a>Escrever streaming DataFrame para contentor
Neste gesto, irá escrever um dataframe de streaming no recipiente DB Azure Cosmos que selecionou. Se a pasta */localReadCheckpointFolder* não for criada, será criada automaticamente. Esta operação terá impacto no desempenho transacional da Azure Cosmos DB.

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

* [Saiba o que é suportado entre a Synapse e a Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Ligue-se à Synapse Link para Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
