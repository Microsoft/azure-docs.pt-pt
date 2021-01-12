---
title: 'Quickstart: Começa a analisar com a Spark'
description: Neste tutorial, você aprenderá a analisar dados com Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 3b5f5d64498922e9fc35942ff4570d801aa6c516
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118885"
---
# <a name="analyze-with-apache-spark"></a>Analise com Faísca Apache

Neste tutorial, você vai aprender os passos básicos para carregar e analisar dados com Apache Spark para Azure Synapse.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analise os dados do táxi de NYC no armazenamento de bolhas usando a Spark

1. No centro **de dados,** selecione **Adicionar um novo recurso** (mais botão acima do **Linked)**> **galeria Browse**.
1. Selecione **NYC Taxi & Limousine Commission - registos amarelos da viagem de táxi**.
1. Na parte inferior da página, selecione **Continue**  >  **Adicionar conjunto de dados**.
1. No centro **de dados** em **Linked**, clique à direita **Azure Blob Storage**, e, em seguida, selecione **conjuntos de dados de amostra**  >  **nyc_tlc_yellow**.
1. Selecione **novo caderno** para criar um novo caderno com o seguinte código:

    ```py
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```

1. No **menu Anexar ao** menu no caderno, escolha uma piscina spark sem servidor.
1. **Selecione Executar** na célula.
1. Se quiser ver apenas o esquema do dataframe, execute uma célula com o seguinte código:

    ```py
    data_df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Carregue os dados do táxi de NYC na base de dados Spark nyctaxi

Os dados estão disponíveis numa tabela em **SQLPOOL1**. Coloque-o numa base de dados spark chamada **nyctaxi.**

1. No Estúdio Synapse, vá ao centro **de Desenvolvimento.**
1. Selecione **+**  >  **Caderno**.
1. Em cima do caderno, desaperte o **valor do Anexo** ao **Spark1**.
1. **Selecione Adicionar código** para adicionar uma célula de código de caderno e, em seguida, introduzir o seguinte texto:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. **Selecione Executar** na célula.
1. No centro **de dados,** clique à direita bases de **dados** e, em seguida, selecione **Refresh**. Devia ver estas bases de dados:

    - **SQLPOOL1 (SQL)**
    - **nyctaxi (Faísca)**

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analise os dados do Táxi de NYC usando Spark e cadernos

1. Volte para o seu caderno.
1. Crie uma nova célula de código e introduza o seguinte texto.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Faça o telemóvel para mostrar os dados do Táxi de NYC que carregou na base **de dados nyctaxi** Spark.
1. Execute o seguinte código para fazer a mesma análise que fez anteriormente com a piscina **SQL SQLQ1** dedicada. Este código guarda e exibe os resultados da análise numa tabela denominada **nyctaxi.passengercountstats**.

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

## <a name="load-data-from-a-spark-table-into-a-dedicated-sql-pool-table"></a>Carregue os dados de uma tabela Spark numa mesa de bilhar SQL dedicada

Anteriormente copiou dados da tabela de piscinas **SQL.dbo.Trip** para a mesa Spark **nyctaxi.trip**. Em seguida, agregou os dados na tabela Spark **nyctaxi.passengercountstats**. Agora você vai copiar os dados de **nyctaxi.passengercountstats** em uma mesa de bilhar SQL dedicada chamada **SQLPOOL1.dbo.PassengerCountStats**.

Coloque a seguinte célula no seu caderno. Copia a mesa spark agregada de volta para a mesa de bilhar SQL dedicada.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Analisar dados com piscina SQL sem servidor](get-started-analyze-sql-on-demand.md)
