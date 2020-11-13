---
title: 'Quickstart: Começa a analisar com a Spark'
description: Neste tutorial, você vai aprender a analisar dados com Apache Spark
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: a33b702209bf2724098b7a0db907b1b7237ceccf
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592622"
---
# <a name="analyze-with-apache-spark"></a>Analise com Faísca Apache

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analise os dados do táxi de NYC no armazenamento de bolhas usando a Spark

Neste tutorial, você vai aprender os passos básicos para carregar e analisar dados com Apache Spark para Azure Synapse.

1. No centro **de dados,** clique em **Adicionar um novo recurso** (mais botão acima do **Linked** )  >> **Browse Samples**. 
1. Encontre **a Comissão de Táxis & Limousine da NYC - registos amarelos de viagem** de táxi e clique nele. 
1. Na parte inferior da página prima **Continue** e depois adicione **conjunto de dados**. 
1. Agora no centro **de dados** sob o clique direito **ligado** no **Azure Blob Storage >> Sample Datasets >> nyc_tlc_yellow** e selecione Novo **caderno**
1. Isto criará um novo Caderno com o seguinte código:
    ```
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```
1. No caderno, escolha uma piscina de faísca sem servidor no **menu Attach**
1. Selecione **Executar** na célula
1. Se quiser apenas ver o esquema do dataframe executar uma célula com o seguinte código:
    ```
    data_df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Carregue os dados do táxi de NYC na base de dados Spark nyctaxi

Temos dados disponíveis numa tabela em **SQLPOOL1.** Coloque-o numa base de dados spark chamada **nyctaxi.**

1. No Estúdio Synapse, vá ao centro **de Desenvolvimento.**
1. Selecione **+**  >  **Caderno**.
1. Em cima do caderno, desaperte o **valor do Anexo** ao **Spark1**.
1. **Selecione Adicionar código** para adicionar uma célula de código de caderno e, em seguida, colar o seguinte texto:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Aceda ao centro **de dados,** clique com o botão direito **bases de dados** e, em seguida, selecione **Refresh**. Devia ver estas bases de dados:

    - **SQLPOOL1** (piscina SQL dedicada)
    - **nyctaxi** (piscina Apache Spark sem servidor)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analise os dados do Táxi de NYC usando Spark e cadernos

1. Volte para o seu caderno.
1. Crie uma nova célula de código e introduza o seguinte texto. Em seguida, executar o telemóvel para mostrar os dados do táxi nyc que carregamos na base de dados **nyctaxi** Spark.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Executar o seguinte código para fazer a mesma análise que fizemos anteriormente com a piscina **SQL SQLQ1** dedicada. Este código guarda os resultados da análise numa tabela chamada **nyctaxi.passengercountstats** e visualiza os resultados.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. Nos resultados da célula, selecione **Chart** para ver os dados visualizados.

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>Personalize a visualização de dados com Spark e cadernos

Pode controlar como os gráficos são renderizados usando cadernos. O seguinte código mostra um exemplo simples. Utiliza as populares bibliotecas **matplotlib** e **seaborn.** O código torna o mesmo tipo de gráfico de linha que as consultas SQL que corremos anteriormente.

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```



## <a name="load-data-from-a-spark-table-into-a-dedicated-sql-pool-table"></a>Carregue os dados de uma tabela Spark numa mesa de bilhar SQL dedicada

Anteriormente copiamos dados da tabela de piscinas **SQL SQL 22 dedicada SQLPOOL1.dbo.Trip** para a mesa Spark **nyctaxi.trip**. Depois, usando a Spark, agregamos os dados na tabela Spark **nyctaxi.passengercountstats.** Agora vamos copiar os dados de **nyctaxi.passengercountstats** em uma mesa de bilhar SQL dedicada chamada **SQLPOOL1.dbo.PassengerCountStats**.

Coloque a seguinte célula no seu caderno. Copia a mesa spark agregada de volta para a mesa de bilhar SQL dedicada.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Analisar dados com piscina SQL sem servidor](get-started-analyze-sql-on-demand.md)


