---
title: Visão geral de como usar o Lago Delta da Fundação Linux em Apache Spark para Azure Synapse Analytics
description: Aprenda a usar o Delta Lake em Apache Spark para a Azure Synapse Analytics, para criar e usar tabelas com propriedades ACID.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: euang
ms.topic: overview
ms.subservice: spark
ms.date: 07/28/2020
ms.author: euang
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 1e0dfd597e7f445eeba6cef332d8ea12b27dc3a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676313"
---
# <a name="linux-foundation-delta-lake-overview"></a>Visão geral do Lago Delta da Fundação Linux

Este artigo foi adaptado para maior clareza da sua congénere original [aqui.](https://docs.delta.io/latest/quick-start.html) Este artigo ajuda-o a explorar rapidamente as principais características do [Delta Lake.](https://delta.io) O artigo fornece códigos que mostram como ler e escrever para as tabelas do Delta Lake a partir de consultas interativas, de lote e streaming. Os código snippets também estão disponíveis em um conjunto de cadernos [PySpark aqui](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/PySpark/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Python.ipynb), [Scala aqui](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Scala/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Scala.ipynb), e [C# aqui](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Spark.NET%20C%23/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20CSharp.ipynb)

Eis o que vamos cobrir:

* Criar uma tabela
* Ler dados
* Atualizar dados da tabela
* Dados de tabela de substituição
* Atualização condicional sem sobrepor
* Leia versões mais antigas de dados usando a Viagem no Tempo
* Escreva um fluxo de dados para uma tabela
* Leia um fluxo de mudanças a partir de uma mesa
* Suporte SQL

## <a name="configuration"></a>Configuração

Certifique-se de que modifica o abaixo conforme apropriado para o seu ambiente.

:::zone pivot = "programming-language-python"

```python
import random

session_id = random.randint(0,1000000)
delta_table_path = "/delta/delta-table-{0}".format(session_id)

delta_table_path
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var sessionId = (new Random()).Next(10000000);
var deltaTablePath = $"/delta/delta-table-{sessionId}";

deltaTablePath
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val sessionId = scala.util.Random.nextInt(1000000)
val deltaTablePath = s"/delta/delta-table-$sessionId";
```

::: zone-end

Resultados em:

«/delta/delta-table-335323'

## <a name="create-a-table"></a>Criar uma tabela

Para criar uma tabela Delta Lake, escreva um DataFrame para um DataFrame no formato delta. Pode alterar o formato de Parquet, CSV, JSON, e assim por diante, para delta.

O código que se segue mostra como criar uma nova tabela Delta Lake utilizando o esquema inferido do seu DataFrame.

:::zone pivot = "programming-language-python"

```python
data = spark.range(0,5)
data.show()
data.write.format("delta").save(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(0,5);
data.Show();
data.Write().Format("delta").Save(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(0, 5)
data.show
data.write.format("delta").save(deltaTablePath)
```

::: zone-end

Resultados em:

| ID|
|---|
|  0|
|  1|
|  2|
|  3|
|  4|

## <a name="read-data"></a>Ler dados

Você lê dados na sua tabela Delta Lake especificando o caminho para os ficheiros e o formato delta.

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Load(deltaTablePath);
df.Show()
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").load(deltaTablePath)
df.show()
```

::: zone-end

Resultados em:

| ID|
|---|
|  1|
|  3|
|  4|
|  0|
|  2|

A ordem dos resultados é diferente de cima, uma vez que não havia nenhuma ordem explicitamente especificada antes da saída dos resultados.

## <a name="update-table-data"></a>Atualizar dados da tabela

Delta Lake suporta várias operações para modificar tabelas usando APIs dataframe padrão, esta é uma das grandes melhorias que o formato delta adiciona. O exemplo a seguir executa um trabalho de lote para substituir os dados na tabela.

:::zone pivot = "programming-language-python"

```python
data = spark.range(5,10)
data.write.format("delta").mode("overwrite").save(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(5,10);
data.Write().Format("delta").Mode("overwrite").Save(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(5, 10)
data.write.format("delta").mode("overwrite").save(deltaTablePath)
df.show()
```

::: zone-end

Resultados em:

| ID|
|---|
|  7|
|  8|
|  5|
|  9|
|  6|

Aqui pode ver que todos os cinco discos foram atualizados para manter novos valores.

## <a name="save-as-catalog-tables"></a>Guardar como tabelas de catálogo

Delta Lake pode escrever para tabelas de catálogos geridas ou externas.

:::zone pivot = "programming-language-python"

```python
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql("CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{0}'".format(delta_table_path))
spark.sql("SHOW TABLES").show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
data.Write().Format("delta").SaveAsTable("ManagedDeltaTable");
spark.Sql($"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{deltaTablePath}'");
spark.Sql("SHOW TABLES").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql(s"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '$deltaTablePath'")
spark.sql("SHOW TABLES").show
```

::: zone-end

Resultados em:

|base de dados|         tableName|isTemporário|
|--------|------------------|-----------|
| predefinição|externos|      false|
| predefinição| gestão de gestão|      false|

Com este código, criou uma nova tabela no catálogo a partir de um dataframe existente, referido como uma tabela gerida. Em seguida, definiu uma nova tabela externa no catálogo que utiliza uma localização existente, referida como uma tabela externa. Na saída pode ver ambas as tabelas, independentemente da forma como foram criadas, estão listadas no catálogo.

Agora você pode olhar para as propriedades estendidas de ambas as tabelas

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ManagedDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=false)
```

::: zone-end

Resultados em:

|col_name                    |data_type                                                                                                    |comentário|
|----------------------------|-------------------------------------------------------------------------------------------------------------|-------|
|ID                          |bigint                                                                                                       |nulo   |
|                            |                                                                                                             |       |
|Informações detalhadas sobre o quadro  |                                                                                                             |       |
|Base de Dados                    |predefinição                                                                                                      |       |
|Tabela                       |gestão de gestão                                                                                            |       |
|Proprietário                       |utilizador de serviço de confiança                                                                                         |       |
|Hora de Criação                |Sáb abr 25 00:35:34 UTC 2020                                                                                 |       |
|Último Acesso                 |Thu jan 01 00:00:00 UTC 1970                                                                                 |       |
|Criada Por                  |Faísca 2.4.4.2.6.99.201-11401300                                                                              |       |
|Tipo                        |Gerido                                                                                                      |       |
|Fornecedor                    |delta                                                                                                        |       |
|Propriedades de mesa            |[transient_lastDdlTime=1587774934]                                                                           |       |
|Estatísticas                  |2407 bytes                                                                                                   |       |
|Localização                    |abfss://data@ <data lake> .dfs.core.windows.net/synapse/workspaces/ <workspace name> /armazém/manageddeltatable|       |
|Biblioteca Serde               |org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe                                                           |       |
|InputFormat                 |org.apache.hadoop.mapred.SequenceFileInputFormat                                                             |       |
|OutputFormat                |org.apache.hadoop.hive.ql.io.HiveSequenceFileOutputFormat                                                    |       |
|Propriedades de armazenamento          |[serialização.formato=1]                                                                                     |       |

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ExternalDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=false)
```

::: zone-end

Resultados em:

|col_name                    |data_type                                                             |comentário|
|----------------------------|----------------------------------------------------------------------|-------|
|ID                          |bigint                                                                |nulo   |
|                            |                                                                      |       |
|Informações detalhadas sobre o quadro  |                                                                      |       |
|Base de Dados                    |predefinição                                                               |       |
|Tabela                       |externos                                                    |       |
|Proprietário                       |utilizador de serviço de confiança                                                  |       |
|Hora de Criação                |Sáb abr 25 00:35:38 UTC 2020                                          |       |
|Último Acesso                 |Thu jan 01 00:00:00 UTC 1970                                          |       |
|Criada Por                  |Faísca 2.4.4.2.6.99.201-11401300                                       |       |
|Tipo                        |EXTERNA                                                              |       |
|Fornecedor                    |DELTA                                                                 |       |
|Propriedades de mesa            |[transient_lastDdlTime=1587774938]                                    |       |
|Localização                    |abfss://data@ <data lake> .dfs.core.windows.net/delta/delta-table-587152|       |
|Biblioteca Serde               |org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe                    |       |
|InputFormat                 |org.apache.hadoop.mapred.SequenceFileInputFormat                      |       |
|OutputFormat                |org.apache.hadoop.hive.ql.io.HiveSequenceFileOutputFormat             |       |
|Propriedades de armazenamento          |[serialização.formato=1]                                              |       |

## <a name="conditional-update-without-overwrite"></a>Atualização condicional sem sobrepor

A Delta Lake fornece APIs programáticos para atualizar, eliminar e fundir (isto é vulgarmente referido como um upsert) dados em tabelas.

:::zone pivot = "programming-language-python"

```python
from delta.tables import *
from pyspark.sql.functions import *

delta_table = DeltaTable.forPath(spark, delta_table_path)

delta_table.update(
  condition = expr("id % 2 == 0"),
  set = { "id": expr("id + 100") })
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Delta;
using Microsoft.Spark.Extensions.Delta.Tables;
using Microsoft.Spark.Sql;
using static Microsoft.Spark.Sql.Functions;

var deltaTable = DeltaTable.ForPath(deltaTablePath);

deltaTable.Update(
  condition: Expr("id % 2 == 0"),
  set: new Dictionary<string, Column>(){{ "id", Expr("id + 100") }});
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
import io.delta.tables._
import org.apache.spark.sql.functions._

val deltaTable = DeltaTable.forPath(deltaTablePath)

// Update every even value by adding 100 to it
deltaTable.update(
  condition = expr("id % 2 == 0"),
  set = Map("id" -> expr("id + 100")))
deltaTable.toDF.show
```

::: zone-end

Resultados em:

| ID|
|---|
|106|
|108|
|  5|
|  7|
|  9|

Aqui acabaste de adicionar 100 a cada identificação.

:::zone pivot = "programming-language-python"

```python
delta_table.delete("id % 2 == 0")
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.Delete(condition: Expr("id % 2 == 0"));
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.delete(condition = expr("id % 2 == 0"))
deltaTable.toDF.show
```

::: zone-end

Resultados em:

| ID|
|---|
|  5|
|  7|
|  9|

Note que todas as linhas par foram apagadas.

:::zone pivot = "programming-language-python"

```python
new_data = spark.range(0,20).alias("newData")

delta_table.alias("oldData")\
    .merge(new_data.alias("newData"), "oldData.id = newData.id")\
    .whenMatchedUpdate(set = { "id": lit("-1")})\
    .whenNotMatchedInsert(values = { "id": col("newData.id") })\
    .execute()

delta_table.toDF().show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var newData = spark.Range(20).As("newData");

deltaTable
    .As("oldData")
    .Merge(newData, "oldData.id = newData.id")
    .WhenMatched()
        .Update(new Dictionary<string, Column>() {{"id", Lit("-1")}})
    .WhenNotMatched()
        .Insert(new Dictionary<string, Column>() {{"id", Col("newData.id")}})
    .Execute();

deltaTable.ToDF().Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val newData = spark.range(0, 20).toDF

deltaTable.as("oldData").
  merge(
    newData.as("newData"),
    "oldData.id = newData.id").
  whenMatched.
  update(Map("id" -> lit(-1))).
  whenNotMatched.
  insert(Map("id" -> col("newData.id"))).
  execute()

deltaTable.toDF.show()
```

::: zone-end

Resultados em:

| ID|
|---|
| 18|
| 15|
| 19|
|  2|
|  1|
|  6|
|  8|
|  3|
| -1|
| 10|
| 13|
|  0|
| 16|
|  4|
| -1|
| 12|
| 11|
| 14|
| -1|
| 17|

Aqui tem uma combinação dos dados existentes. Os dados existentes foram atribuídos ao valor -1 na trajetória de código de atualização (WhenMatched). Os novos dados que foram criados no topo do snippet e foram adicionados através da trajetória de código de inserção (WhenNotMatched), também foram adicionados.

### <a name="history"></a>Histórico

Delta Lake's tem a capacidade de permitir olhar para a história de uma mesa. Ou seja, as alterações que foram feitas na Tabela Delta subjacente. A célula abaixo mostra como é simples inspecionar a história.

:::zone pivot = "programming-language-python"

```python
delta_table.history().show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show(false)
```

::: zone-end

Resultados em:

|versão|          carimbo de data/hora|userId|userName|operation|                                                operaçãoParametros| tarefa|bloco de notas|clusterId|readVersão|isolamentoLevel|isBlindAppend|
|-------|-------------------|------|--------|---------|-------------------------------------------------------------------|----|--------|---------|-----------|--------------|-------------|
|      4|2020-04-25 00:36:27|  nulo|    nulo|    FUSÃO|                       [predicado -> (oldData.`ID` = `ID` novosData.]]|nulo|    nulo|     nulo|          3|          nulo|        false|
|      3|2020-04-25 00:36:08|  nulo|    nulo|   DELETE|[predicado -> ["(% `ID` CAST(2 AS BIGINT)) = CAST(0 AS BIGINT)]]|nulo|    nulo|     nulo|          2|          nulo|        false|
|      2|2020-04-25 00:35:51|  nulo|    nulo|   UPDATE| [predicado -> ((ID#744L % cast (2 como bigint)) = elenco (0 como bigint)]]|nulo|    nulo|     nulo|          1|          nulo|        false|
|      1|2020-04-25 00:35:05|  nulo|    nulo|    ESCREVER|                             [modo -> Overwrite, partitionBy -> []]|nulo|    nulo|     nulo|          0|          nulo|        false|
|      0|2020-04-25 00:34:34|  nulo|    nulo|    ESCREVER|                         [mode -> ErrorIfExists, partitionBy -> []]|nulo|    nulo|     nulo|       nulo|          nulo|         true|

Aqui pode ver todas as modificações feitas sobre os cortes de código acima.

## <a name="read-older-versions-of-data-using-time-travel"></a>Leia versões mais antigas de dados usando a Viagem no Tempo

É possível consultar fotos anteriores da sua tabela Delta Lake usando um recurso chamado Time Travel. Se pretender aceder aos dados que sobreou, pode consultar uma imagem instantânea da tabela antes de sobrepor o primeiro conjunto de dados utilizando a opção versionAsOf.

Uma vez que você executar a célula abaixo, você deve ver o primeiro conjunto de dados de antes de sobrepor-lo. A Time Travel é uma funcionalidade extremamente poderosa que aproveita o poder do registo de transações do Delta Lake para aceder a dados que já não estão na tabela. Remover a opção da versão 0 (ou especificar a versão 1) permitir-lhe-ia ver novamente os dados mais recentes. Para obter mais informações, consulte [uma imagem mais antiga de uma tabela.](https://docs.delta.io/latest/delta-batch.html#deltatimetravel)

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").option("versionAsOf", 0).load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Option("versionAsOf", 0).Load(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").option("versionAsOf", 0).load(deltaTablePath)
df.show()
```

::: zone-end

Resultados em:

| ID|
|---|
|  0|
|  1|
|  4|
|  3|
|  2|

Aqui pode ver que voltou à versão mais antiga dos dados.

## <a name="write-a-stream-of-data-to-a-table"></a>Escreva um fluxo de dados para uma tabela

Você também pode escrever para uma mesa Delta Lake usando Spark's Structured Streaming. O registo de transações do Delta Lake garante exatamente uma vez processado, mesmo quando existem outros streams ou consultas de lote em simultâneo contra a tabela. Por predefinição, os streams são executados no modo apêndice, que adiciona novos registos à tabela.

Para obter mais informações sobre a integração do Delta Lake com o Streaming Estruturado, consulte [leituras e escritos de streaming de mesa.](https://docs.delta.io/latest/delta-streaming.html)

Nas células abaixo, eis o que estamos a fazer:

* Célula 30 Mostrar os dados recentemente anexados
* Célula 31 Inspecionar o histórico
* Célula 32 Pare o trabalho de streaming estruturado
* Célula 33 Inspecionar o histórico <-- Vais reparar que os apêndices pararam

Primeiro, vais criar um simples trabalho de Streaming de Faíscas para gerar uma sequência e fazer o trabalho escrever para a tua Tabela Delta.

:::zone pivot = "programming-language-python"

```python
streaming_df = spark.readStream.format("rate").load()
stream = streaming_df\
    .selectExpr("value as id")\
    .writeStream\
    .format("delta")\
    .option("checkpointLocation", "/tmp/checkpoint-{0}".format(session_id))\
    .start(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var streamingDf = spark.ReadStream().Format("rate").Load();
var stream = streamingDf.SelectExpr("value as id").WriteStream().Format("delta").Option("checkpointLocation", $"/tmp/checkpoint-{sessionId}").Start(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val streamingDf = spark.readStream.format("rate").load()
val stream = streamingDf.select($"value" as "id").writeStream.format("delta").option("checkpointLocation", s"/tmp/checkpoint-$sessionId").start(deltaTablePath)
```

::: zone-end

## <a name="read-a-stream-of-changes-from-a-table"></a>Leia um fluxo de mudanças a partir de uma mesa

Enquanto o riacho está escrevendo para a mesa do Lago Delta, você também pode ler a partir dessa mesa como uma fonte de streaming. Por exemplo, pode iniciar outra consulta de streaming que imprime todas as alterações feitas na tabela Do Lago Delta.

:::zone pivot = "programming-language-python"

```python
delta_table.toDF().sort(col("id").desc()).show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.ToDF().Sort(Col("id").Desc()).Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.toDF.sort($"id".desc).show
```

::: zone-end

Resultados em:

| ID|
|---|
| 19|
| 18|
| 17|
| 16|
| 15|
| 14|
| 13|
| 12|
| 11|
| 10|
|  8|
|  6|
|  4|
|  3|
|  2|
|  1|
|  0|
| -1|
| -1|
| -1|

:::zone pivot = "programming-language-python"

```python
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show
```

::: zone-end

Resultados em:

|versão|          carimbo de data/hora|       operation|                                                                  operaçãoParametros|readVersão|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|ATUALIZAÇÃO DE STREAMING|[outputMode -> Append, queryId -> d26b4f8a-7e5a-44f2-a5fb-23a7bd02aef7, epoId -> 0]|          4|
|      4|2020-04-25 00:36:27|           FUSÃO|                                         [predicado -> (oldData.`id` = `id` novosData.]]|          3|
|      3|2020-04-25 00:36:08|          DELETE|                  [predicado -> ["(% `id` CAST(2 AS BIGINT)) = CAST(0 AS BIGINT)]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predicado -> ((id#744L % elenco (2 como bigint)) = elenco (0 como bigint)]]|          1|
|      1|2020-04-25 00:35:05|           ESCREVER|                                               [modo -> Overwrite, partitionBy -> []]|          0|
|      0|2020-04-25 00:34:34|           ESCREVER|                                           [mode -> ErrorIfExists, partitionBy -> []]|       nulo|

Aqui está a deixar cair algumas das colunas menos interessantes para simplificar a experiência de visualização da vista da história.

:::zone pivot = "programming-language-python"

```python
stream.stop()
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(100, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
stream.Stop();
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(100, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
stream.stop
deltaTable.history.show
```

Resultados em:

|versão|          carimbo de data/hora|       operation|                                                                  operaçãoParametros|readVersão|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|ATUALIZAÇÃO DE STREAMING|[outputMode -> Append, queryId -> d26b4f8a-7e5a-44f2-a5fb-23a7bd02aef7, epoId -> 0]|          4|
|      4|2020-04-25 00:36:27|           FUSÃO|                                         [predicado -> (oldData.`id` = `id` novosData.]]|          3|
|      3|2020-04-25 00:36:08|          DELETE|                  [predicado -> ["(% `id` CAST(2 AS BIGINT)) = CAST(0 AS BIGINT)]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predicado -> ((id#744L % elenco (2 como bigint)) = elenco (0 como bigint)]]|          1|
|      1|2020-04-25 00:35:05|           ESCREVER|                                               [modo -> Overwrite, partitionBy -> []]|          0|
|      0|2020-04-25 00:34:34|           ESCREVER|                                           [mode -> ErrorIfExists, partitionBy -> []]|       nulo|

::: zone-end

## <a name="convert-parquet-to-delta"></a>Converter Parquet para Delta

Pode fazer uma conversão no local do formato Parquet para a Delta.

Aqui vai testar se a tabela existente está em formato delta ou não.
:::zone pivot = "programming-language-python"

```python
parquet_path = "/parquet/parquet-table-{0}".format(session_id)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetPath = $"/parquet/parquet-table-{sessionId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetPath = s"/parquet/parquet-table-$sessionId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Resultados em:

Falso

Agora vai converter os dados em formato delta e verificar se funcionou.

:::zone pivot = "programming-language-python"

```python
DeltaTable.convertToDelta(spark, "parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
DeltaTable.ConvertToDelta(spark, $"parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
DeltaTable.convertToDelta(spark, s"parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Resultados em:

Verdadeiro

## <a name="sql-support"></a>Suporte SQL

A Delta suporta comandos de utilidade de mesa através do SQL. Pode utilizar o SQL para:

* Obtenha a história de um DeltaTable
* Aspirar um DeltaTable
* Converter um ficheiro Parquet para a Delta

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE HISTORY delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"DESCRIBE HISTORY delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"DESCRIBE HISTORY delta.`$deltaTablePath`").show()
```

::: zone-end

Resultados em:

|versão|          carimbo de data/hora|userId|userName|       operation| operaçãoParametros| tarefa|bloco de notas|clusterId|readVersão|isolamentoLevel|isBlindAppend|
|-------|-------------------|------|--------|----------------|--------------------|----|--------|---------|-----------|--------------|-------------|
|      5|2020-04-25 00:37:09|  nulo|    nulo|ATUALIZAÇÃO DE STREAMING|[outputMode -> Ap...|nulo|    nulo|     nulo|          4|          nulo|         true|
|      4|2020-04-25 00:36:27|  nulo|    nulo|           FUSÃO|[predicado -> (ol...|nulo|    nulo|     nulo|          3|          nulo|        false|
|      3|2020-04-25 00:36:08|  nulo|    nulo|          DELETE|[predicado -> ["(...|nulo|    nulo|     nulo|          2|          nulo|        false|
|      2|2020-04-25 00:35:51|  nulo|    nulo|          UPDATE|[predicado -> (i...|nulo|    nulo|     nulo|          1|          nulo|        false|
|      1|2020-04-25 00:35:05|  nulo|    nulo|           ESCREVER|[modo -> Overwrit...|nulo|    nulo|     nulo|          0|          nulo|        false|
|      0|2020-04-25 00:34:34|  nulo|    nulo|           ESCREVER|[modo -> ErrorIfE...|nulo|    nulo|     nulo|       nulo|          nulo|         true|

:::zone pivot = "programming-language-python"

```python
spark.sql("VACUUM delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"VACUUM delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"VACUUM delta.`$deltaTablePath`").show()
```

::: zone-end

Resultados em:

|                caminho|
|--------------------|
|abfss://data@arca...|

Agora vai verificar se uma tabela não é uma tabela de formato delta, depois convertê-la em formato delta usando Spark SQL e confirmar que foi convertida corretamente.

:::zone pivot = "programming-language-python"

```python
parquet_id = random.randint(0,1000)
parquet_path = "/parquet/parquet-table-{0}-{1}".format(session_id, parquet_path)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
spark.sql("CONVERT TO DELTA parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetId =  (new Random()).Next(10000000);
var parquetPath = $"/parquet/parquet-table-{sessionId}-{parquetId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath);
spark.Sql($"CONVERT TO DELTA parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetId = scala.util.Random.nextInt(1000)
val parquetPath = s"/parquet/parquet-table-$sessionId-$parquetId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
spark.sql(s"CONVERT TO DELTA parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Resultados em:

Verdadeiro

Para documentação completa, consulte a Página de [Documentação do Lago Delta](https://docs.delta.io/latest/delta-intro.html)

Para mais informações, consulte [o Projeto Delta Lake.](https://github.com/delta-io/delta)

## <a name="next-steps"></a>Passos seguintes

* [.NET para documentação Apache Spark](/dotnet/spark)
* [Azure Synapse Analytics](../index.yml)