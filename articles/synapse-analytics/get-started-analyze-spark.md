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
ms.date: 12/31/2020
ms.openlocfilehash: 7410fcbc890780281763a91f33525562e9de1853
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182490"
---
# <a name="analyze-with-apache-spark"></a>Analise com Faísca Apache

Neste tutorial, você vai aprender os passos básicos para carregar e analisar dados com Apache Spark para Azure Synapse.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analise os dados do táxi de NYC no armazenamento de bolhas usando a Spark

1. No centro **de dados,** clique no **+** botão para **Adicionar um novo recurso** e, em seguida, clique em >> **galeria Browse**. 
1. Encontre **a Comissão de Táxis & Limousine da NYC - registos amarelos de viagem** de táxi e clique nele. 
1. Na parte inferior da página prima **Continuar,** em **seguida, adicionar conjunto de dados**. 
1. Após um momento, no centro de **dados** em **Linked**, clique à direita no **Azure Blob Storage >> Sample Datasets >> nyc_tlc_yellow** e selecione **Novo caderno**, em seguida, **Carregue para Quadro de Dados**.
1. Isto criará um novo Caderno com o seguinte código:
    ```

    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    df = data.to_spark_dataframe()
    # Display 10 rows
    display(df.limit(10))
    ```
1. No caderno, no **menu Attach,** escolha a piscina **Spark1** sem servidor que criamos anteriormente.
1. Selecione **Executar** na célula
1. Se quiser apenas ver o esquema do dataframe executar uma célula com o seguinte código:
    ```

    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Carregue os dados do táxi de NYC na base de dados Spark nyctaxi

Os dados estão disponíveis numa tabela em **SQLPOOL1**. Coloque-o numa base de dados spark chamada **nyctaxi.**

1. No Estúdio Synapse, vá ao centro **de Desenvolvimento.**
1. Selecione **+**  >  **Caderno**.
1. Em cima do caderno, desaperte o **valor do Anexo** ao **Spark1**.
1. Na primeira célula de código do novo caderno e, em seguida, introduza o seguinte código:


    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```


1. Execute o script. Pode levar 2 a 3 minutos.
1. No centro **de dados,** no **separador Espaço de Trabalho,** clique com o botão direito **bases de dados** e, em seguida, selecione **Refresh**. Deverá agora ver a base de dados **nyctaxi (Spark)** na lista.


## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analise os dados do Táxi de NYC usando Spark e cadernos

1. Volte para o seu caderno.
1. Crie uma nova célula de código e introduza o seguinte código. 


   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Corre o telemóvel para mostrar os dados do Táxi da NYC que carregámos na base **de dados do Nyctaxi** Spark.
1. Crie uma nova célula de código e introduza o seguinte código. Em seguida, executar a célula para fazer a mesma análise que fizemos anteriormente com a piscina SQL **SQL 21** dedicada. Este código guarda e exibe os resultados da análise numa tabela chamada **nyctaxi.passengercountstats**.


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

Anteriormente copiamos dados da tabela de piscinas **SQL SQL 22 dedicada SQLPOOL1.dbo.Trip** para a mesa Spark **nyctaxi.trip**. Em seguida, agregou os dados na tabela Spark **nyctaxi.passengercountstats**. Agora você vai copiar os dados de **nyctaxi.passengercountstats** em uma mesa de bilhar SQL dedicada chamada **SQLPOOL1.dbo.PassengerCountStats**.

1. Crie uma nova célula de código e introduza o seguinte código. Coloque a cela no seu caderno. Copia a mesa spark agregada de volta para a mesa de bilhar SQL dedicada.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Analisar dados com piscina SQL sem servidor](get-started-analyze-sql-on-demand.md)
