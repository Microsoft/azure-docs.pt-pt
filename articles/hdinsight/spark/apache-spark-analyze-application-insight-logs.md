---
title: Analisar logs do Application Insight com Spark – Azure HDInsight
description: Saiba como exportar logs do Application Insight para o armazenamento de BLOBs e, em seguida, analisar os logs com o Spark no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/09/2018
ms.openlocfilehash: 846239c0122f3f2cadc40e7965ae690d4ba3e538
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899866"
---
# <a name="analyze-application-insights-telemetry-logs-with-apache-spark-on-hdinsight"></a>Analisar Application Insights logs de telemetria com Apache Spark no HDInsight

Saiba como usar o [Apache Spark](https://spark.apache.org/) no HDInsight para analisar dados de telemetria do Application insights.

O [Visual Studio Application insights](../../azure-monitor/app/app-insights-overview.md) é um serviço de análise que monitora seus aplicativos Web. Os dados de telemetria gerados pelo Application Insights podem ser exportados para o armazenamento do Azure. Depois que os dados estiverem no armazenamento do Azure, o HDInsight poderá ser usado para analisá-lo.

## <a name="prerequisites"></a>Pré-requisitos

* Um aplicativo que está configurado para usar Application Insights.

* Familiaridade com a criação de um cluster HDInsight baseado em Linux. Para obter mais informações, consulte [criar Apache Spark no HDInsight](apache-spark-jupyter-spark-sql.md).

* Um navegador da Web.

Os seguintes recursos foram usados para desenvolver e testar este documento:

* Application Insights dados de telemetria foram gerados usando um [aplicativo Web node. js configurado para usar Application insights](../../azure-monitor/app/nodejs.md).

* Um cluster Spark no HDInsight baseado em Linux versão 3,5 foi usado para analisar os dados.

## <a name="architecture-and-planning"></a>Arquitetura e planejamento

O diagrama a seguir ilustra a arquitetura de serviço deste exemplo:

![diagrama mostrando dados que fluem de Application Insights para o armazenamento de BLOBs e, em seguida, sendo processados pelo Spark no HDInsight](./media/apache-spark-analyze-application-insight-logs/application-insights.png)

### <a name="azure-storage"></a>Storage do Azure

Application Insights pode ser configurado para exportar continuamente informações de telemetria para BLOBs. Em seguida, o HDInsight pode ler os dados armazenados nos BLOBs. No entanto, há alguns requisitos que você deve seguir:

* **Local**: Se a conta de armazenamento e o HDInsight estiverem em locais diferentes, isso poderá aumentar a latência. Ele também aumenta o custo, pois os encargos de egresso são aplicados à movimentação de dados entre regiões.

    > [!WARNING]  
    > Não há suporte para o uso de uma conta de armazenamento em um local diferente do HDInsight.

* **Tipo de blob**: O HDInsight suporta apenas blobs de blocos. Application Insights usa blobs de bloco como padrão, portanto, o deve funcionar por padrão com o HDInsight.

Para obter informações sobre como adicionar armazenamento a um cluster existente, consulte o documento [Adicionar contas de armazenamento adicionais](../hdinsight-hadoop-add-storage.md) .

### <a name="data-schema"></a>Esquema de dados

Application Insights fornece informações de [modelo de dados de exportação](../../azure-monitor/app/export-data-model.md) para o formato de dados de telemetria exportado para BLOBs. As etapas neste documento usam o Spark SQL para trabalhar com os dados. O Spark SQL pode gerar automaticamente um esquema para a estrutura de dados JSON registrada por Application Insights.

## <a name="export-telemetry-data"></a>Exportar dados de telemetria

Siga as etapas em [Configurar exportação contínua](../../azure-monitor/app/export-telemetry.md) para configurar seu Application insights para exportar informações de telemetria para um blob de armazenamento do Azure.

## <a name="configure-hdinsight-to-access-the-data"></a>Configurar o HDInsight para acessar os dados

Se você estiver criando um cluster HDInsight, adicione a conta de armazenamento durante a criação do cluster.

Para adicionar a conta de armazenamento do Azure a um cluster existente, use as informações no documento [Adicionar contas de armazenamento adicionais](../hdinsight-hadoop-add-storage.md) .

## <a name="analyze-the-data-pyspark"></a>Analise os dados: PySpark

1. No [portal do Azure](https://portal.azure.com), selecione seu Spark no cluster HDInsight. Na seção **links rápidos** , selecione **painéis do cluster**e, em seguida, selecione **Jupyter Notebook** na seção painel do cluster.

    ![Os painéis do cluster](./media/apache-spark-analyze-application-insight-logs/hdi-cluster-dashboards.png)

2. No canto superior direito da página Jupyter, selecione **novo**e, em seguida, **PySpark**. Uma nova guia do navegador contendo um Jupyter Notebook baseado em Python é aberta.

3. No primeiro campo (chamado de **célula**) na página, digite o seguinte texto:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Esse código configura o Spark para acessar recursivamente a estrutura de diretórios para os dados de entrada. Application Insights telemetria é registrada em uma estrutura de diretório `/{telemetry type}/YYYY-MM-DD/{##}/`semelhante ao.

4. Use **Shift + Enter** para executar o código. No lado esquerdo da célula, um '\*' aparece entre colchetes para indicar que o código nesta célula está sendo executado. Após a conclusão, o '\*' muda para um número e uma saída semelhante ao texto a seguir é exibida abaixo da célula:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. Uma nova célula é criada abaixo da primeira. Insira o texto a seguir na nova célula. Substitua `CONTAINER` e`STORAGEACCOUNT` pelo nome da conta de armazenamento do Azure e pelo nome do contêiner de BLOB que contém dados de Application insights.

   ```python
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Use **Shift + Enter** para executar esta célula. Você verá um resultado semelhante ao seguinte texto:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    O caminho WASB retornado é o local do Application Insights dados de telemetria. Altere a `hdfs dfs -ls` linha na célula para usar o caminho WASB retornado e, em seguida, use **Shift + Enter** para executar a célula novamente. Desta vez, os resultados devem exibir os diretórios que contêm dados de telemetria.

   > [!NOTE]  
   > Para o restante das etapas nesta seção, o `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` diretório foi usado. Sua estrutura de diretório pode ser diferente.

6. Na próxima célula, digite o seguinte código: Substituir `WASB_PATH` pelo caminho da etapa anterior.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Esse código cria um dataframe a partir dos arquivos JSON exportados pelo processo de exportação contínua. Use **Shift + Enter** para executar esta célula.
7. Na próxima célula, insira e execute o seguinte para exibir o esquema que o Spark criou para os arquivos JSON:

   ```python
   jsonData.printSchema()
   ```

    O esquema para cada tipo de telemetria é diferente. O exemplo a seguir é o esquema que é gerado para solicitações da Web (dados armazenados `Requests` no subdiretório):

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
8. Use o seguinte para registrar o dataframe como uma tabela temporária e executar uma consulta nos dados:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    Essa consulta retorna as informações de cidade dos primeiros 20 registros em que Context. Location. City não é nulo.

   > [!NOTE]  
   > A estrutura de contexto está presente em toda a telemetria registrada por Application Insights. O elemento City não pode ser preenchido em seus logs. Use o esquema para identificar outros elementos que você pode consultar que podem conter dados para seus logs.

    Essa consulta retorna informações semelhantes ao seguinte texto:

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

1. No [portal do Azure](https://portal.azure.com), selecione seu Spark no cluster HDInsight. Na seção **links rápidos** , selecione **painéis do cluster**e, em seguida, selecione **Jupyter Notebook** na seção painel do cluster.

    ![Os painéis do cluster](./media/apache-spark-analyze-application-insight-logs/hdi-cluster-dashboards.png)
2. No canto superior direito da página Jupyter, selecione **novo**e **escalabilidade**. Uma nova guia do navegador contendo uma Jupyter Notebook baseada em escala é exibida.
3. No primeiro campo (chamado de **célula**) na página, digite o seguinte texto:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Esse código configura o Spark para acessar recursivamente a estrutura de diretórios para os dados de entrada. Application Insights telemetria é registrada em uma estrutura de `/{telemetry type}/YYYY-MM-DD/{##}/`diretório semelhante a.

4. Use **Shift + Enter** para executar o código. No lado esquerdo da célula, um '\*' aparece entre colchetes para indicar que o código nesta célula está sendo executado. Após a conclusão, o '\*' muda para um número e uma saída semelhante ao texto a seguir é exibida abaixo da célula:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. Uma nova célula é criada abaixo da primeira. Insira o texto a seguir na nova célula. Substitua `CONTAINER` e`STORAGEACCOUNT` pelo nome da conta de armazenamento do Azure e pelo nome do contêiner de BLOB que contém logs de Application insights.

   ```scala
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Use **Shift + Enter** para executar esta célula. Você verá um resultado semelhante ao seguinte texto:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    O caminho WASB retornado é o local do Application Insights dados de telemetria. Altere a `hdfs dfs -ls` linha na célula para usar o caminho WASB retornado e, em seguida, use **Shift + Enter** para executar a célula novamente. Desta vez, os resultados devem exibir os diretórios que contêm dados de telemetria.

   > [!NOTE]  
   > Para o restante das etapas nesta seção, o `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` diretório foi usado. Esse diretório pode não existir, a menos que os dados de telemetria sejam para um aplicativo Web.

6. Na próxima célula, digite o seguinte código: Substituir `WASB\_PATH` pelo caminho da etapa anterior.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Esse código cria um dataframe a partir dos arquivos JSON exportados pelo processo de exportação contínua. Use **Shift + Enter** para executar esta célula.

7. Na próxima célula, insira e execute o seguinte para exibir o esquema que o Spark criou para os arquivos JSON:

   ```scala
   jsonData.printSchema
   ```

    O esquema para cada tipo de telemetria é diferente. O exemplo a seguir é o esquema que é gerado para solicitações da Web (dados armazenados `Requests` no subdiretório):

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

8. Use o seguinte para registrar o dataframe como uma tabela temporária e executar uma consulta nos dados:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city is not null limit 10").show()
   ```

    Essa consulta retorna as informações de cidade dos primeiros 20 registros em que Context. Location. City não é nulo.

   > [!NOTE]  
   > A estrutura de contexto está presente em toda a telemetria registrada por Application Insights. O elemento City não pode ser preenchido em seus logs. Use o esquema para identificar outros elementos que você pode consultar que podem conter dados para seus logs.
   >
   >

    Essa consulta retorna informações semelhantes ao seguinte texto:

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

Para obter mais exemplos de como usar Apache Spark para trabalhar com dados e serviços no Azure, consulte os seguintes documentos:

* [Apache Spark com BI: Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para analisar a temperatura de edifício usando dados de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

Para obter informações sobre como criar e executar aplicativos Spark, consulte os seguintes documentos:

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando o Livy](apache-spark-livy-rest-interface.md)
