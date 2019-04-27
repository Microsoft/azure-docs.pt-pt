---
title: Analisar registos do Application Insight com o Spark - Azure HDInsight
description: Saiba como exportar registos do Application Insights para o armazenamento de BLOBs e, em seguida, analise os registos com o Spark no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/09/2018
ms.openlocfilehash: 806e5b6f764797d2e038cc7ed58ec1d04f678e2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60326653"
---
# <a name="analyze-application-insights-telemetry-logs-with-apache-spark-on-hdinsight"></a>Analisar registos de telemetria do Application Insights com o Apache Spark no HDInsight

Aprenda a usar [Apache Spark](https://spark.apache.org/) no HDInsight para analisar dados de telemetria do Application Insights.

[O Visual Studio Application Insights](../../azure-monitor/app/app-insights-overview.md) é um serviço de análise que monitoriza as suas aplicações web. Dados de telemetria gerados pelo Application Insights podem ser exportados para o armazenamento do Azure. Quando os dados estiverem no armazenamento do Azure, o HDInsight pode ser utilizado para analisá-lo.

## <a name="prerequisites"></a>Pré-requisitos

* Um aplicativo que está configurado para utilizar o Application Insights.

* Familiaridade com a criação de um cluster do HDInsight baseado em Linux. Para obter mais informações, consulte [criar o Apache Spark no HDInsight](apache-spark-jupyter-spark-sql.md).

  > [!IMPORTANT]  
  > Os passos neste documento exigem um cluster do HDInsight que utilize o Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* Um navegador da web.

Os seguintes recursos foram usados no desenvolvimento e teste deste documento:

* Dados de telemetria do Application Insights foi gerados com um [aplicação web de node. js configurada para utilizar o Application Insights](../../azure-monitor/app/nodejs.md).

* Spark baseado em Linux em clusters do HDInsight versão 3.5 foi utilizado para analisar os dados.

## <a name="architecture-and-planning"></a>Planejamento e arquitetura

O diagrama seguinte ilustra a arquitetura de serviço deste exemplo:

![diagrama que mostra o fluxo de dados do Application Insights para armazenamento de BLOBs, em seguida, a ser processado pelo Spark no HDInsight](./media/apache-spark-analyze-application-insight-logs/appinsightshdinsight.png)

### <a name="azure-storage"></a>Storage do Azure

O Application Insights pode ser configurado para continuamente exportar informações de telemetria para blobs. HDInsight, em seguida, pode ler os dados armazenados em blobs. No entanto, existem alguns requisitos que tem de seguir:

* **Localização**: Se a conta de armazenamento e o HDInsight estiverem em diferentes localizações, pode aumentar a latência. Também aumenta o custo, como Cobranças aplicam-se a movimentação entre regiões de dados de saída.

    > [!WARNING]  
    > Utilizar uma conta de armazenamento numa localização diferente do que o HDInsight não é suportada.

* **Tipo de blob**: O HDInsight suporta apenas blobs de blocos. Application Insights a predefinição é usando blobs de blocos, assim, deve funcionar por predefinição com o HDInsight.

Para obter informações sobre como adicionar armazenamento a um cluster existente, consulte a [adicionar mais contas de armazenamento](../hdinsight-hadoop-add-storage.md) documento.

### <a name="data-schema"></a>Esquema de dados

O Application Insights fornece [exportar o modelo de dados](../../azure-monitor/app/export-data-model.md) informações para o formato de dados de telemetria exportados para blobs. Os passos neste documento utilizam o Spark SQL para trabalhar com os dados. Spark SQL pode gerar automaticamente um esquema para a estrutura de dados JSON registado pelo Application Insights.

## <a name="export-telemetry-data"></a>Exportar dados de telemetria

Siga os passos em [configurar a exportação contínua](../../azure-monitor/app/export-telemetry.md) para configurar o Application Insights para exportar as informações de telemetria para um blob de armazenamento do Azure.

## <a name="configure-hdinsight-to-access-the-data"></a>Configurar o HDInsight para aceder aos dados

Se estiver a criar um cluster do HDInsight, adicione a conta de armazenamento durante a criação do cluster.

Para adicionar a conta de armazenamento do Azure a um cluster existente, utilize as informações a [adicionar contas de armazenamento adicionais](../hdinsight-hadoop-add-storage.md) documento.

## <a name="analyze-the-data-pyspark"></a>Analise os dados: PySpark

1. Do [portal do Azure](https://portal.azure.com), selecione o Spark num cluster do HDInsight. Do **ligações rápidas** secção, selecione **Dashboards de clusters**e, em seguida, selecione **bloco de notas do Jupyter** da secção Dashboard__ de Cluster.

    ![Os dashboards de cluster](./media/apache-spark-analyze-application-insight-logs/clusterdashboards.png)

2. No canto superior direito da página Jupyter, selecione **New**e, em seguida **PySpark**. É aberto um novo separador do browser que contém um bloco de notas do Jupyter com base em Python.

3. No primeiro campo (chamado um **célula**) na página, introduza o seguinte texto:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Esse código configura o Spark para o acesso de recursivamente a estrutura de diretório para os dados de entrada. Telemetria do Application Insights é registada numa estrutura de diretório semelhante do `/{telemetry type}/YYYY-MM-DD/{##}/`.

4. Uso **SHIFT + ENTER** para executar o código. No lado esquerdo da célula, um "\*' aparece entre parênteses para indicar que o código nessa célula está a ser executado. Depois de terminar, o '\*"alterações a um número e o resultado semelhante ao seguinte texto é apresentado abaixo na célula:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. Uma nova célula é criada abaixo primeiro. Introduza o seguinte texto na célula de novo. Substitua `CONTAINER` e `STORAGEACCOUNT` com o nome da conta de armazenamento do Azure e o nome do contentor de BLOBs que contém dados do Application Insights.

   ```python
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Uso **SHIFT + ENTER** para executar esta célula. Verá um resultado semelhante ao seguinte texto:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    O caminho de wasb devolvido é a localização dos dados de telemetria do Application Insights. Alteração da `hdfs dfs -ls` linha na célula de utilizar o caminho de wasb devolvido e, em seguida, utilize **SHIFT + ENTER** para executar a célula novamente. Desta vez, os resultados deverá apresentar os diretórios que contêm dados de telemetria.

   > [!NOTE]  
   > Para o resto dos passos nesta secção, o `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` foi utilizado o diretório. A estrutura de diretórios pode ser diferente.

6. Na próxima célula, introduza o seguinte código: Substitua `WASB_PATH` com o caminho do passo anterior.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Esse código cria um pacote de dados a partir dos ficheiros JSON exportados pelo processo de exportação contínua. Uso **SHIFT + ENTER** para executar esta célula.
7. Na próxima célula, introduza e execute o seguinte para ver o esquema que Spark criado para os ficheiros JSON:

   ```python
   jsonData.printSchema()
   ```

    O esquema para cada tipo de telemetria é diferente. O exemplo seguinte é o esquema que é gerado para solicitações da web (dados armazenados no `Requests` subdiretório):

        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)
8. Utilize o seguinte para registar o pacote de dados como uma tabela temporária e executar uma consulta relativamente aos dados:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    Esta consulta devolve as informações de cidade para os primeiros 20 registos onde context.location.city não é nulo.

   > [!NOTE]  
   > A estrutura de contexto está presente em toda a telemetria registada pelo Application Insights. O elemento de cidade não pode ser preenchido nos seus registos. Utilize o esquema para identificar outros elementos que pode consultar que podem conter dados para os seus registos.

    Esta consulta devolve informações semelhantes ao seguinte texto:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="analyze-the-data-scala"></a>Analise os dados: Scala

1. Do [portal do Azure](https://portal.azure.com), selecione o Spark num cluster do HDInsight. Do **ligações rápidas** secção, selecione **Dashboards de clusters**e, em seguida, selecione **bloco de notas do Jupyter** da secção Dashboard__ de Cluster.

    ![Os dashboards de cluster](./media/apache-spark-analyze-application-insight-logs/clusterdashboards.png)
2. No canto superior direito da página Jupyter, selecione **New**e, em seguida **Scala**. É apresentado um novo separador do browser que contém um bloco de notas do Jupyter com base em Scala.
3. No primeiro campo (chamado um **célula**) na página, introduza o seguinte texto:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Esse código configura o Spark para o acesso de recursivamente a estrutura de diretório para os dados de entrada. Telemetria do Application Insights é registada numa estrutura de diretório semelhante à `/{telemetry type}/YYYY-MM-DD/{##}/`.

4. Uso **SHIFT + ENTER** para executar o código. No lado esquerdo da célula, um "\*' aparece entre parênteses para indicar que o código nessa célula está a ser executado. Depois de terminar, o '\*"alterações a um número e o resultado semelhante ao seguinte texto é apresentado abaixo na célula:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. Uma nova célula é criada abaixo primeiro. Introduza o seguinte texto na célula de novo. Substitua `CONTAINER` e `STORAGEACCOUNT` com o nome da conta de armazenamento do Azure e o nome do contentor de BLOBs que contém o Application Insights registos.

   ```scala
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Uso **SHIFT + ENTER** para executar esta célula. Verá um resultado semelhante ao seguinte texto:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    O caminho de wasb devolvido é a localização dos dados de telemetria do Application Insights. Alteração da `hdfs dfs -ls` linha na célula de utilizar o caminho de wasb devolvido e, em seguida, utilize **SHIFT + ENTER** para executar a célula novamente. Desta vez, os resultados deverá apresentar os diretórios que contêm dados de telemetria.

   > [!NOTE]  
   > Para o resto dos passos nesta secção, o `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` foi utilizado o diretório. Este diretório pode não existir, a menos que os seus dados de telemetria são para uma aplicação web.

6. Na próxima célula, introduza o seguinte código: Substitua `WASB\_PATH` com o caminho do passo anterior.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Esse código cria um pacote de dados a partir dos ficheiros JSON exportados pelo processo de exportação contínua. Uso **SHIFT + ENTER** para executar esta célula.

7. Na próxima célula, introduza e execute o seguinte para ver o esquema que Spark criado para os ficheiros JSON:

   ```scala
   jsonData.printSchema
   ```

    O esquema para cada tipo de telemetria é diferente. O exemplo seguinte é o esquema que é gerado para solicitações da web (dados armazenados no `Requests` subdiretório):

        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)

8. Utilize o seguinte para registar o pacote de dados como uma tabela temporária e executar uma consulta relativamente aos dados:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city is not null limit 10").show()
   ```

    Esta consulta devolve as informações de cidade para os primeiros 20 registos onde context.location.city não é nulo.

   > [!NOTE]  
   > A estrutura de contexto está presente em toda a telemetria registada pelo Application Insights. O elemento de cidade não pode ser preenchido nos seus registos. Utilize o esquema para identificar outros elementos que pode consultar que podem conter dados para os seus registos.
   >
   >

    Esta consulta devolve informações semelhantes ao seguinte texto:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="next-steps"></a>Passos Seguintes

Para obter mais exemplos de utilização do Apache Spark para trabalhar com dados e serviços no Azure, consulte os seguintes documentos:

* [Apache Spark com BI: Efetuar análise de dados interativa com o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura de construção com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de registos de Web site com o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

Para obter informações sobre como criar e executar Spark aplicações, veja os documentos seguintes:

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Apache Spark com o Livy](apache-spark-livy-rest-interface.md)
