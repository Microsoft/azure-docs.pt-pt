---
title: Interaja com a Azure Cosmos DB usando Apache Spark in Azure Synapse Link (pré-visualização)
description: Como interagir com a Azure Cosmos DB usando Apache Spark in Azure Synapse Link
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 07342cb31f1c44273f98a97b018620538f86c17f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91287734"
---
# <a name="interact-with-azure-cosmos-db-using-apache-spark-in-azure-synapse-link-preview"></a>Interaja com a Azure Cosmos DB usando Apache Spark in Azure Synapse Link (pré-visualização)

Neste artigo, você aprenderá a interagir com Azure Cosmos DB usando a Synapse Apache Spark. Com o seu total apoio para scala, Python, SparkSQL e C#, o Synapse Apache Spark é central para a análise, engenharia de dados, ciência de dados e cenários de exploração de dados em [Azure Synapse Link para Azure Cosmos DB](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

As seguintes capacidades são suportadas enquanto interagem com a Azure Cosmos DB:
* O Synapse Apache Spark permite-lhe analisar dados nos seus contentores DB Azure Cosmos que estão habilitados com a Azure Synapse Link em quase tempo real sem afetar o desempenho das suas cargas de trabalho transacionais. As duas opções estão disponíveis para consultar a [loja analítica](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) Azure Cosmos DB da Spark:
    + Carregar para Spark DataFrame
    + Criar mesa spark
* O Sinapse Apache Spark também permite ingerir dados em Azure Cosmos DB. É importante notar que os dados são sempre ingeridos em contentores DB da Azure Cosmos através da loja transacional. Quando o Synapse Link está ativado, quaisquer novas inserções, atualizações e eliminações são automaticamente sincronizadas na loja analítica.
* O Synapse Apache Spark também suporta o streaming estruturado spark com a Azure Cosmos DB como fonte, bem como uma pia. 

As secções seguintes acompanham-no através da sintaxe das capacidades acima. Os gestos no espaço de trabalho Azure Synapse Analytics são projetados para proporcionar uma experiência fora da caixa fácil para começar. Os gestos são visíveis quando clica à direita num recipiente DB Azure Cosmos no separador **Dados** do espaço de trabalho da Sinapse. Com os gestos, pode gerar rapidamente código e personalizá-lo de acordo com as suas necessidades. Os gestos também são perfeitos para detetar dados com um único clique.

## <a name="query-azure-cosmos-db-analytical-store"></a>Loja analítica Da Azure Cosmos DB

Antes de conhecer as duas opções possíveis para consultar a loja analítica Azure Cosmos DB, carregar para o Spark DataFrame e criar a tabela Spark, vale a pena explorar as diferenças de experiência para que possa escolher a opção que funciona para as suas necessidades.

A diferença de experiência está em torno de se as mudanças de dados subjacentes no contentor DB Azure Cosmos devem ser automaticamente refletidas na análise realizada na Spark. Quando um Spark DataFrame é registado ou uma tabela Spark é criada contra a loja analítica de um recipiente, os metadados em torno da imagem atual dos dados na loja analítica são recolhidos à Spark para um pushdown eficiente da análise subsequente. É importante notar que, uma vez que a Spark segue uma política de avaliação preguiçosa, a menos que seja invocada uma ação no Spark DataFrame ou numa consulta SparkSQL contra a tabela Spark, os dados reais não são recolhidos da loja analítica do recipiente subjacente.

No caso de **carregamento para o DataFrame de Faísca,** os metadados recolhidos são cached ao longo do tempo de vida da sessão Spark e, portanto, as ações subsequentes invocadas no DataFrame são avaliadas contra o instantâneo da loja analítica no momento da criação do DataFrame.

Por outro lado, no caso de **criar uma tabela Spark,** os metadados do estado da loja analítica não estão em cache na Spark e são recarregados em todas as execuções de consultas SparkSQL contra a tabela Spark.

Assim, pode escolher entre carregar para o Spark DataFrame e criar uma tabela Spark com base na questão de saber se pretende que a sua análise Spark seja avaliada com uma imagem fixa da loja analítica ou contra a última imagem da loja analítica, respectivamente.

> [!NOTE]
> Para consultar a Azure Cosmos DB API das contas da Mongo DB, saiba mais sobre a [representação total](../../cosmos-db/analytical-store-introduction.md#analytical-schema) do esquema de fidelidade na loja analítica e sobre os nomes de propriedade estendidos a serem usados.

### <a name="load-to-spark-dataframe"></a>Carregar para Spark DataFrame

Neste exemplo, irá criar um Spark DataFrame que aponta para a loja analítica Azure Cosmos DB. Em seguida, pode efetuar análises adicionais invocando ações spark contra o DataFrame. Esta operação não afeta a loja transacional.

A sintaxe em **Python** seria o seguinte:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .load()
```

A sintaxe equivalente em **Scala** seria a seguinte:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    load()
```

### <a name="create-spark-table"></a>Criar mesa spark

Neste exemplo, você vai criar uma tabela Spark que aponta a loja analítica Azure Cosmos DB. Em seguida, pode efetuar análises adicionais invocando consultas SparkSQL contra a tabela. Esta operação não tem impacto na loja transacional nem incorre em qualquer movimento de dados. Se decidir eliminar esta tabela Spark, o recipiente DB Azure Cosmos subjacente e a loja analítica correspondente não serão afetados. 

Este cenário é conveniente para reutilizar as tabelas Spark através de ferramentas de terceiros e fornecer acessibilidade aos dados subjacentes para o tempo de execução.

A sintaxe para criar uma tabela Spark é a seguinte:
```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService '<enter linked service name>',
    spark.cosmos.container '<enter container name>'
)
```

> [!NOTE]
> Se tiver cenários em que o esquema do contentor Azure Cosmos DB subjacente muda ao longo do tempo; e se pretender que o esquema atualizado reflita automaticamente nas consultas contra a tabela Spark, pode fazê-lo definindo a `spark.cosmos.autoSchemaMerge`  opção para `true` as opções de tabela Spark.


## <a name="write-spark-dataframe-to-azure-cosmos-db-container"></a>Escreva O Quadro de Dados de Faíscas para o recipiente DB do Azure Cosmos

Neste exemplo, você vai escrever um Spark DataFrame em um recipiente Azure Cosmos DB. Esta operação terá impacto no desempenho das cargas de trabalho transacionais e das unidades de pedido de consumo aprovisionadas no contentor DB Azure Cosmos ou na base de dados partilhada.

A sintaxe em **Python** seria o seguinte:
```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

A sintaxe equivalente em **Scala** seria a seguinte:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>Carregar o streaming de dados Do recipiente
Neste gesto, utilizará a capacidade de Streaming spark para carregar dados de um recipiente para um dataframe. Os dados serão armazenados na conta principal do lago de dados (e sistema de ficheiros) que ligou ao espaço de trabalho. 
> [!NOTE]
> Se procura fazer referência a bibliotecas externas em Synapse Apache Spark, saiba mais [aqui.](#external-library-management) Por exemplo, se você estiver procurando ingerir um DataFrame de faísca para um recipiente da Cosmos DB API para Mongo DB, você pode aproveitar o conector Mongo DB para spark [aqui](https://docs.mongodb.com/spark-connector/master/).

## <a name="load-streaming-dataframe-from-azure-cosmos-db-container"></a>Carregar streaming dataFrame a partir do recipiente DB Azure Cosmos
Neste exemplo, utilizará a capacidade de streaming estruturada da Spark para carregar dados de um contentor DB Azure Cosmos num DataFrame de streaming spark usando a funcionalidade de feed de mudança em Azure Cosmos DB. Os dados de verificação utilizados pela Spark serão armazenados na conta principal do lago de dados (e sistema de ficheiros) que ligou ao espaço de trabalho.

Se a pasta */localReadCheckpointFolder* não for criada (no exemplo abaixo), será criada automaticamente. Esta operação terá impacto no desempenho das cargas de trabalho transacionais e consumirá Unidades de Pedido aprovisionadas no contentor DB da Azure Cosmos ou na base de dados partilhada.

A sintaxe em **Python** seria o seguinte:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

A sintaxe equivalente em **Scala** seria a seguinte:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamQuery").
    load()
```

## <a name="write-streaming-dataframe-to-azure-cosmos-db-container"></a>Escreva o dado de streaming dataframe para o recipiente DB Azure Cosmos
Neste exemplo, você vai escrever um DataFrame de streaming em um recipiente DB Azure Cosmos. Esta operação terá impacto no desempenho das cargas de trabalho transacionais e consumirá Unidades de Pedido aprovisionadas no contentor DB da Azure Cosmos ou na base de dados partilhada. Se a pasta */localWriteCheckpointFolder* não for criada (no exemplo abaixo), será criada automaticamente. 

A sintaxe em **Python** seria o seguinte:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "<enter linked service name>")\
        .option("spark.cosmos.container", "<enter container name>")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

A sintaxe equivalente em **Scala** seria a seguinte:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "<enter linked service name>").
            option("spark.cosmos.container", "<enter container name>").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```

## <a name="external-library-management"></a>Gestão de bibliotecas externas

Neste exemplo, você aprenderá a fazer referência a bibliotecas externas a partir de ficheiros JAR ao usar cadernos Spark em espaços de trabalho Synpase Apache Spark. Pode colocar os ficheiros JAR num recipiente na conta principal do lago de dados que ligou ao espaço de trabalho e, em seguida, adicionar a seguinte `%configure` declaração no seu caderno Spark:

```cmd
%%configure -f
{
    "jars": [
        "abfss://<storage container name>@<data lake account name>.dfs.core.windows.net/<path to jar>"
    ]
}
```
Se procura submeter definições remotas de trabalho spark a uma piscina de Faíscas Synapse, pode aprender a fazer referência a bibliotecas externas seguindo este [tutorial.](../spark/apache-spark-job-definitions.md)

## <a name="next-steps"></a>Passos seguintes

* [Amostras para começar com Azure Synapse Link no GitHub](https://aka.ms/cosmosdb-synapselink-samples)
* [Saiba o que é suportado no Azure Synapse Link para Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Ligue-se à Synapse Link para Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
