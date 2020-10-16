---
title: Analisar registos de Insight de Aplicação com Spark - Azure HDInsight
description: Aprenda a exportar registos de Insight de aplicação para armazenamento de bolhas e, em seguida, analise os registos com Spark on HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 1ddf2b6879d8d33f99281daba6fb1040e24a37af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86078804"
---
# <a name="analyze-application-insights-telemetry-logs-with-apache-spark-on-hdinsight"></a>Analisar registos de telemetria de Insights de Aplicação com Faísca Apache em HDInsight

Aprenda a usar [o Apache Spark](https://spark.apache.org/) em HDInsight para analisar os dados de telemetria do Insight de Aplicação.

[Visual Studio Application Insights](../../azure-monitor/app/app-insights-overview.md) é um serviço de análise que monitoriza as suas aplicações web. Os dados de telemetria gerados pela Application Insights podem ser exportados para o Azure Storage. Uma vez que os dados estão no Azure Storage, o HDInsight pode ser usado para analisá-los.

## <a name="prerequisites"></a>Pré-requisitos

* Uma aplicação configurada para utilizar insights de aplicação.

* Familiaridade com a criação de um cluster HDInsight baseado em Linux. Para obter mais informações, consulte [Create Apache Spark on HDInsight](apache-spark-jupyter-spark-sql.md).

* Um navegador web.

Foram utilizados os seguintes recursos para desenvolver e testar este documento:

* Os dados de telemetria do Application Insights foram gerados utilizando uma [ aplicação webNode.js configurada para utilizar o Application Insights](../../azure-monitor/app/nodejs.md).

* Uma faísca baseada em Linux na versão 3.5 do cluster HDInsight foi usada para analisar os dados.

## <a name="architecture-and-planning"></a>Arquitetura e planeamento

O seguinte diagrama ilustra a arquitetura de serviço deste exemplo:

![Dados que fluem de Insights de Aplicação para armazenamento de bolhas, em seguida, Faísca](./media/apache-spark-analyze-application-insight-logs/application-insights.png)

### <a name="azure-storage"></a>Storage do Azure

Os Insights de Aplicação podem ser configurados para exportar continuamente informações de telemetria para bolhas. O HDInsight pode então ler os dados armazenados nas bolhas. No entanto, existem alguns requisitos que deve seguir:

* **Localização**: Se a Conta de Armazenamento e o HDInsight estiverem em locais diferentes, poderá aumentar a latência. Aumenta também os custos, uma vez que os encargos de saída são aplicados aos dados que se deslocam entre regiões.

    > [!WARNING]  
    > A utilização de uma Conta de Armazenamento num local diferente do HDInsight não é suportada.

* **Tipo blob**: HDInsight suporta apenas bolhas de bloco. O Application Insights não pode utilizar blobs de bloco, por isso deve funcionar por defeito com o HDInsight.

Para obter informações sobre a adição de armazenamento a um cluster existente, consulte o documento [de contas de armazenamento adicional.](../hdinsight-hadoop-add-storage.md)

### <a name="data-schema"></a>Esquema de dados

A Application Insights fornece informações sobre [modelos de dados](../../azure-monitor/app/export-data-model.md) de exportação para o formato de dados de telemetria exportados para bolhas. Os passos deste documento utilizam o Spark SQL para trabalhar com os dados. O Spark SQL pode gerar automaticamente um esquema para a estrutura de dados JSON registada pela Application Insights.

## <a name="export-telemetry-data"></a>Dados de telemetria de exportação

Siga os passos na [Configuração exportação contínua](../../azure-monitor/app/export-telemetry.md) para configurar os seus Insights de Aplicação para exportar informações de telemetria para uma bolha de armazenamento Azure.

## <a name="configure-hdinsight-to-access-the-data"></a>Configure HDInsight para aceder aos dados

Se estiver a criar um cluster HDInsight, adicione a conta de armazenamento durante a criação do cluster.

Para adicionar a Conta de Armazenamento Azure a um cluster existente, utilize as informações no documento [Adicionar Contas de Armazenamento adicionais.](../hdinsight-hadoop-add-storage.md)

## <a name="analyze-the-data-pyspark"></a>Analise os dados: PySpark

1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net/jupyter` até onde CLUSTERNAME é o nome do seu cluster.

2. No canto superior direito da página Jupyter, selecione **New**, e depois **PySpark**. Abre um novo separador de navegador contendo um Jupyter Notebook baseado em Python.

3. No primeiro campo (chamado **de célula)** na página, introduza o seguinte texto:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Este código configura a Spark para aceder novamente à estrutura do diretório para os dados de entrada. A telemetria Application Insights é registada numa estrutura de diretório semelhante à `/{telemetry type}/YYYY-MM-DD/{##}/` .

4. Utilize **o SHIFT+ENTER** para executar o código. No lado esquerdo da célula, aparece um \* ' entre os suportes para indicar que o código desta célula está a ser executado. Uma vez concluído, o ' \* ' altera-se para um número, e a saída semelhante ao seguinte texto é exibida abaixo da célula:

    ```output
    Creating SparkContext as 'sc'

    ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
    3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

    Creating HiveContext as 'sqlContext'
    SparkContext and HiveContext created. Executing user code ...
    ```

5. Uma nova célula é criada abaixo da primeira. Introduza o seguinte texto na nova célula. Substitua `CONTAINER` e pelo nome da conta de armazenamento `STORAGEACCOUNT` Azure e nome do recipiente blob que contém dados de Insights de Aplicação.

   ```python
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Utilize **o SHIFT+ENTER** para executar esta célula. Vê um resultado semelhante ao seguinte texto:

    ```output
    Found 1 items
    drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
    ```

    O caminho de wasbs devolvido é a localização dos dados de telemetria application Insights. Mude a `hdfs dfs -ls` linha na célula para utilizar o caminho das vespas devolvida e, em seguida, use o **SHIFT+ENTER** para executar novamente a célula. Desta vez, os resultados devem apresentar os diretórios que contêm dados de telemetria.

   > [!NOTE]  
   > Para o restante dos passos nesta secção, o `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` diretório foi usado. A sua estrutura de diretório pode ser diferente.

6. Na célula seguinte, introduza o seguinte código: `WASB_PATH` Substitua-o pelo caminho do passo anterior.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Este código cria um conjunto de dados a partir dos ficheiros JSON exportados pelo processo de exportação contínua. Utilize **o SHIFT+ENTER** para executar esta célula.
7. Na célula seguinte, insira e corra o seguinte para ver o esquema que Spark criou para os ficheiros JSON:

   ```python
   jsonData.printSchema()
   ```

    O esquema para cada tipo de telemetria é diferente. Segue-se o esquema gerado para pedidos web (dados armazenados na `Requests` subdiretória):

    ```output
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
    ```

8. Utilize o seguinte para registar o dataframe como tabela temporária e executar uma consulta com os dados:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    Esta consulta devolve a informação da cidade para os 20 melhores registos onde context.location.city não é nulo.

   > [!NOTE]  
   > A estrutura de contexto está presente em toda a telemetria registada pela Application Insights. O elemento da cidade pode não estar povoado nos seus troncos. Utilize o esquema para identificar outros elementos que possa consultar que possam conter dados para os seus registos.

    Esta consulta devolve informações semelhantes ao seguinte texto:

    ```output
    +---------+
    |     city|
    +---------+
    | Bellevue|
    |  Redmond|
    |  Seattle|
    |Charlotte|
    ...
    +---------+
    ```

## <a name="analyze-the-data-scala"></a>Analisar os dados: Scala

1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net/jupyter` até onde CLUSTERNAME é o nome do seu cluster.

2. No canto superior direito da página Jupyter, selecione **New**, e depois **Scala**. Aparece um novo separador de navegador contendo um Jupyter Notebook baseado em Scala.

3. No primeiro campo (chamado **de célula)** na página, introduza o seguinte texto:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Este código configura a Spark para aceder novamente à estrutura do diretório para os dados de entrada. A telemetria Application Insights é registada numa estrutura de diretório semelhante a `/{telemetry type}/YYYY-MM-DD/{##}/` .

4. Utilize **o SHIFT+ENTER** para executar o código. No lado esquerdo da célula, aparece um \* ' entre os suportes para indicar que o código desta célula está a ser executado. Uma vez concluído, o ' \* ' altera-se para um número, e a saída semelhante ao seguinte texto é exibida abaixo da célula:

    ```output
    Creating SparkContext as 'sc'

    ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
    3    application_1468969497124_0001    spark    idle    Link    Link    ✔

    Creating HiveContext as 'sqlContext'
    SparkContext and HiveContext created. Executing user code ...
    ```

5. Uma nova célula é criada abaixo da primeira. Introduza o seguinte texto na nova célula. Substitua `CONTAINER` e pelo nome da conta de armazenamento `STORAGEACCOUNT` Azure e nome do recipiente blob que contém registos de Insights de Aplicação.

   ```scala
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Utilize **o SHIFT+ENTER** para executar esta célula. Vê um resultado semelhante ao seguinte texto:

    ```output
    Found 1 items
    drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
    ```

    O caminho de wasbs devolvido é a localização dos dados de telemetria application Insights. Mude a `hdfs dfs -ls` linha na célula para utilizar o caminho das vespas devolvida e, em seguida, use o **SHIFT+ENTER** para executar novamente a célula. Desta vez, os resultados devem apresentar os diretórios que contêm dados de telemetria.

   > [!NOTE]  
   > Para o restante dos passos nesta secção, o `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` diretório foi usado. Este diretório pode não existir a menos que os seus dados de telemetria sejam para uma aplicação web.

6. Na célula seguinte, introduza o seguinte código: `WASB\_PATH` Substitua-o pelo caminho do passo anterior.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Este código cria um conjunto de dados a partir dos ficheiros JSON exportados pelo processo de exportação contínua. Utilize **o SHIFT+ENTER** para executar esta célula.

7. Na célula seguinte, insira e corra o seguinte para ver o esquema que Spark criou para os ficheiros JSON:

   ```scala
   jsonData.printSchema
   ```

    O esquema para cada tipo de telemetria é diferente. Segue-se o esquema gerado para pedidos web (dados armazenados na `Requests` subdiretória):

    ```output
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
    ```

8. Utilize o seguinte para registar o dataframe como tabela temporária e executar uma consulta com os dados:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city isn't null limit 10").show()
   ```

    Esta consulta devolve a informação da cidade para os 20 melhores registos onde context.location.city não é nulo.

   > [!NOTE]  
   > A estrutura de contexto está presente em toda a telemetria registada pela Application Insights. O elemento da cidade pode não estar povoado nos seus troncos. Utilize o esquema para identificar outros elementos que possa consultar que possam conter dados para os seus registos.

    Esta consulta devolve informações semelhantes ao seguinte texto:

    ```output
    +---------+
    |     city|
    +---------+
    | Bellevue|
    |  Redmond|
    |  Seattle|
    |Charlotte|
    ...
    +---------+
    ```

## <a name="next-steps"></a>Passos seguintes

Para obter mais exemplos de utilização da Apache Spark para trabalhar com dados e serviços em Azure, consulte os seguintes documentos:

* [Apache Spark com BI: Realizar análise de dados interativas usando Spark in HDInsight com ferramentas BI](apache-spark-use-bi-tools.md)
* [Faísca Apache com Machine Learning: Use faísca em HDInsight para analisar a temperatura do edifício usando dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Faísca Apache com Machine Learning: Use faísca em HDInsight para prever os resultados da inspeção alimentar](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de registo do site usando Apache Spark em HDInsight](apache-spark-custom-library-website-log-analysis.md)

Para obter informações sobre a criação e execução de aplicações Spark, consulte os seguintes documentos:

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando Livy](apache-spark-livy-rest-interface.md)
