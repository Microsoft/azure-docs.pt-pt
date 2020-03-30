---
title: Analisar registos de insight de aplicação com faísca - Azure HDInsight
description: Aprenda a exportar registos de insight de aplicação para armazenamento de bolhas e, em seguida, analise os registos com Spark no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 6fd7682f56fbe446904a4acdb39e78525f2523a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435245"
---
# <a name="analyze-application-insights-telemetry-logs-with-apache-spark-on-hdinsight"></a>Analise os registos de telemetria de Insights de Aplicação com Apache Spark no HDInsight

Saiba como usar [o Apache Spark](https://spark.apache.org/) no HDInsight para analisar os dados de telemetria do Insight da Aplicação.

[Visual Studio Application Insights](../../azure-monitor/app/app-insights-overview.md) é um serviço de análise que monitoriza as suas aplicações web. Os dados de telemetria gerados pela Application Insights podem ser exportados para o Armazenamento Azure. Uma vez que os dados estão no Armazenamento Azure, o HDInsight pode ser usado para analisá-los.

## <a name="prerequisites"></a>Pré-requisitos

* Uma aplicação que está configurada para utilizar insights de aplicação.

* Familiaridade com a criação de um cluster HDInsight baseado em Linux. Para mais informações, consulte [Create Apache Spark no HDInsight](apache-spark-jupyter-spark-sql.md).

* Um navegador web.

Foram utilizados os seguintes recursos no desenvolvimento e teste deste documento:

* Os dados de telemetria da Application Insights foram gerados através de uma [aplicação web Node.js configurada para utilizar insights](../../azure-monitor/app/nodejs.md)de aplicação .

* Uma Spark baseada em Linux na versão 3.5 do cluster HDInsight foi usada para analisar os dados.

## <a name="architecture-and-planning"></a>Arquitetura e planeamento

O diagrama seguinte ilustra a arquitetura de serviço deste exemplo:

![Dados que fluem de Application Insights para armazenamento de bolhas, em seguida, Spark](./media/apache-spark-analyze-application-insight-logs/application-insights.png)

### <a name="azure-storage"></a>Storage do Azure

Os insights de aplicação podem ser configurados para exportar continuamente informações de telemetria para bolhas. O HDInsight pode então ler os dados armazenados nas bolhas. No entanto, existem alguns requisitos que deve seguir:

* **Localização**: Se a Conta de Armazenamento e o HDInsight estiverem em locais diferentes, pode aumentar a latência. Aumenta também os custos, uma vez que os encargos com a saída são aplicados aos dados que se deslocam entre regiões.

    > [!WARNING]  
    > A utilização de uma Conta de Armazenamento num local diferente do HDInsight não é suportada.

* **Tipo de bolha:** HDInsight suporta apenas bolhas de bloco. Aplicação Insights predefinidos para usar bolhas de bloco, por isso deve funcionar por padrão com hDInsight.

Para obter informações sobre a adição de armazenamento a um cluster existente, consulte o documento de contas de [armazenamento adicionais.](../hdinsight-hadoop-add-storage.md)

### <a name="data-schema"></a>Esquema de dados

Aplicação Insights fornece informações sobre modelos de dados de [exportação](../../azure-monitor/app/export-data-model.md) para o formato de dados de telemetria exportado para bolhas. Os passos neste documento utilizam a Spark SQL para trabalhar com os dados. O Spark SQL pode gerar automaticamente um esquema para a estrutura de dados da JSON registada por Application Insights.

## <a name="export-telemetry-data"></a>Dados de telemetria de exportação

Siga os passos na [Configure Continuous Export](../../azure-monitor/app/export-telemetry.md) para configurar os insights da sua aplicação para exportar informações de telemetria para uma bolha de armazenamento Azure.

## <a name="configure-hdinsight-to-access-the-data"></a>Configure o HDInsight para aceder aos dados

Se estiver a criar um cluster HDInsight, adicione a conta de armazenamento durante a criação do cluster.

Para adicionar a Conta de Armazenamento Azure a um cluster existente, utilize a informação no documento [Adicionar Contas de Armazenamento adicionais.](../hdinsight-hadoop-add-storage.md)

## <a name="analyze-the-data-pyspark"></a>Analise os dados: PySpark

1. A partir de um `https://CLUSTERNAME.azurehdinsight.net/jupyter` navegador web, navegue até onde clusterNAME é o nome do seu cluster.

2. No canto superior direito da página Jupyter, selecione **New**, e depois **PySpark**. Abre-se um novo separador de navegador contendo um Jupyter Notebook baseado em Python.

3. No primeiro campo (chamado **de célula)** na página, introduza o seguinte texto:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Este código confunde a Spark para aceder recursivamente à estrutura do diretório para os dados de entrada. Aplicação Insights telemetria é registrado a `/{telemetry type}/YYYY-MM-DD/{##}/`uma estrutura de diretório semelhante à .

4. Utilize **shift+ENTER** para executar o código. No lado esquerdo da célula,\*aparece um ' entre os suportes para indicar que o código nesta célula está a ser executado. Uma vez concluído,\*as alterações de um número e a saída semelhante ao seguinte texto são apresentadas abaixo da célula:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...

5. Uma nova célula é criada abaixo da primeira. Introduza o seguinte texto na nova célula. `CONTAINER` Substitua `STORAGEACCOUNT` e com o nome da conta De armazenamento Azure e o nome do recipiente blob que contém dados de Informações de Aplicação.

   ```python
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Utilize **SHIFT+ENTER** para executar esta célula. Vê um resultado semelhante ao seguinte texto:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    O caminho das vespas devolvido é a localização dos dados de telemetria da Application Insights. Mude `hdfs dfs -ls` a linha da célula para utilizar o caminho das vespas devolvido e, em seguida, use **SHIFT+ENTER** para executar novamente a célula. Desta vez, os resultados devem apresentar os diretórios que contenham dados de telemetria.

   > [!NOTE]  
   > Durante o resto dos passos `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` nesta secção, o diretório foi usado. A sua estrutura de diretório pode ser diferente.

6. Na célula seguinte, introduza o `WASB_PATH` seguinte código: Substitua pelo caminho do passo anterior.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Este código cria um quadro de dados dos ficheiros JSON exportados pelo processo de exportação contínua. Utilize **SHIFT+ENTER** para executar esta célula.
7. Na célula seguinte, introduza e corra o seguinte para ver o esquema que a Spark criou para os ficheiros JSON:

   ```python
   jsonData.printSchema()
   ```

    O esquema para cada tipo de telemetria é diferente. O exemplo seguinte é o esquema que é gerado para pedidos `Requests` web (dados armazenados no subdiretório):

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

8. Utilize o seguinte para registar o quadro de dados como uma tabela temporária e fazer uma consulta contra os dados:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    Esta consulta devolve a informação da cidade para os 20 melhores registos onde o contexto.local.cidade não é nulo.

   > [!NOTE]  
   > A estrutura de contexto está presente em toda a telemetria registada pela Application Insights. O elemento da cidade pode não ser povoado nos seus troncos. Utilize o esquema para identificar outros elementos que pode consultar que possam conter dados para os seus registos.

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

## <a name="analyze-the-data-scala"></a>Analisar os dados: Scala

1. A partir de um `https://CLUSTERNAME.azurehdinsight.net/jupyter` navegador web, navegue até onde clusterNAME é o nome do seu cluster.

2. No canto superior direito da página Jupyter, selecione **New**, e depois **Scala**. Aparece um novo separador de navegador contendo um Caderno Jupyter baseado em Scala.

3. No primeiro campo (chamado **de célula)** na página, introduza o seguinte texto:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Este código confunde a Spark para aceder recursivamente à estrutura do diretório para os dados de entrada. Aplicação Insights telemetria é registrado `/{telemetry type}/YYYY-MM-DD/{##}/`a uma estrutura de diretório semelhante a .

4. Utilize **shift+ENTER** para executar o código. No lado esquerdo da célula,\*aparece um ' entre os suportes para indicar que o código nesta célula está a ser executado. Uma vez concluído,\*as alterações de um número e a saída semelhante ao seguinte texto são apresentadas abaixo da célula:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...

5. Uma nova célula é criada abaixo da primeira. Introduza o seguinte texto na nova célula. `CONTAINER` Substitua `STORAGEACCOUNT` e com o nome da conta De armazenamento Azure e o nome do recipiente blob que contém registos de Informações de Aplicação.

   ```scala
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Utilize **SHIFT+ENTER** para executar esta célula. Vê um resultado semelhante ao seguinte texto:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    O caminho das vespas devolvido é a localização dos dados de telemetria da Application Insights. Mude `hdfs dfs -ls` a linha da célula para utilizar o caminho das vespas devolvido e, em seguida, use **SHIFT+ENTER** para executar novamente a célula. Desta vez, os resultados devem apresentar os diretórios que contenham dados de telemetria.

   > [!NOTE]  
   > Durante o resto dos passos `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` nesta secção, o diretório foi usado. Este diretório pode não existir a menos que os seus dados de telemetria sejam para uma aplicação web.

6. Na célula seguinte, introduza o `WASB\_PATH` seguinte código: Substitua pelo caminho do passo anterior.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Este código cria um quadro de dados dos ficheiros JSON exportados pelo processo de exportação contínua. Utilize **SHIFT+ENTER** para executar esta célula.

7. Na célula seguinte, introduza e corra o seguinte para ver o esquema que a Spark criou para os ficheiros JSON:

   ```scala
   jsonData.printSchema
   ```

    O esquema para cada tipo de telemetria é diferente. O exemplo seguinte é o esquema que é gerado para pedidos `Requests` web (dados armazenados no subdiretório):

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

8. Utilize o seguinte para registar o quadro de dados como uma tabela temporária e fazer uma consulta contra os dados:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city isn't null limit 10").show()
   ```

    Esta consulta devolve a informação da cidade para os 20 melhores registos onde o contexto.local.cidade não é nulo.

   > [!NOTE]  
   > A estrutura de contexto está presente em toda a telemetria registada pela Application Insights. O elemento da cidade pode não ser povoado nos seus troncos. Utilize o esquema para identificar outros elementos que pode consultar que possam conter dados para os seus registos.

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

## <a name="next-steps"></a>Passos seguintes

Para mais exemplos de utilização da Apache Spark para trabalhar com dados e serviços no Azure, consulte os seguintes documentos:

* [Apache Spark com BI: Realizar análise interativa de dados utilizando spark in HDInsight com ferramentas BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para analisar a temperatura do edifício utilizando dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para prever resultados da inspeção alimentar](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark em HDInsight](apache-spark-custom-library-website-log-analysis.md)

Para obter informações sobre a criação e execução de aplicações Spark, consulte os seguintes documentos:

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando Livy](apache-spark-livy-rest-interface.md)
