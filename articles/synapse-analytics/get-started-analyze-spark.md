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
ms.date: 03/24/2021
ms.openlocfilehash: 0becbbdb68f75072e10a51f5a2eae95291b9ed77
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108337"
---
# <a name="analyze-with-apache-spark"></a>Analise com Faísca Apache

Neste tutorial, você vai aprender os passos básicos para carregar e analisar dados com Apache Spark para Azure Synapse.

## <a name="create-a-serverless-apache-spark-pool"></a>Crie uma piscina Apache Spark sem servidor

1. No Synapse Studio, no painel do lado esquerdo, **selecione Gerir**  >  **as piscinas Apache Spark**.
1. Selecione **Novo** 
1. Para **o nome da piscina Apache Spark** **insi spark1**.
1. Para **o tamanho do nó,** **introduza Pequeno**.
1. Para **o número de nós** Definir o mínimo para 3 e o máximo a 3
1. Selecione **Rever + criar** > **Criar**. A tua piscina Apache Spark estará pronta em alguns segundos.

## <a name="understanding-serverless-apache-spark-pools"></a>Compreender piscinas Apache Spark sem servidor

Uma piscina spark sem servidor é uma forma de indicar como um utilizador quer trabalhar com a Spark. Quando você começar a usar uma piscina, uma sessão de faísca é criada se necessário. A piscina controla quantos recursos spark serão utilizados por essa sessão e quanto tempo a sessão durará muito antes de parar automaticamente. Você paga pelos recursos de faísca usados durante a sessão, não para a própria piscina. Desta forma, uma piscina spark permite-lhe trabalhar com a Spark, sem ter que se preocupar em gerir clusters. Isto é semelhante ao funcionamento de uma piscina SQL sem servidor.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analise os dados do táxi de NYC no armazenamento de bolhas usando a Spark

1. No Estúdio Synapse ir para o centro **de Desenvolvimento**
2. Crie um novo Caderno com o idioma padrão definido para **PySpark (Python)**.
3. Crie uma nova célula de código e cole o seguinte código nessa célula.
    ```py
    %%pyspark
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    df = data.to_spark_dataframe()
    # Display 10 rows
    display(df.limit(10))
    ```
1. No caderno, no **menu Attach,** escolha a piscina **Spark1** sem servidor que criamos anteriormente.
1. **Selecione Executar** na célula. A Sinapse iniciará uma nova sessão de Faíscas para executar esta célula, se necessário. Se for necessária uma nova sessão de Faísca, inicialmente levará cerca de dois segundos a ser criada. 
1. Se quiser apenas ver o esquema do dataframe executar uma célula com o seguinte código:

    ```py
    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Carregue os dados do táxi de NYC na base de dados Spark nyctaxi

Os dados estão disponíveis através do **dataframe dados denominados.** Coloque-o numa base de dados spark chamada **nyctaxi.**

1. Adicione um novo ao caderno e, em seguida, introduza o seguinte código:

    ```py
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analise os dados do Táxi de NYC usando Spark e cadernos

1. Volte para o seu caderno.
1. Crie uma nova célula de código e introduza o seguinte código. 

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Corre o telemóvel para mostrar os dados do Táxi da NYC que carregámos na base **de dados do Nyctaxi** Spark.
1. Crie uma nova célula de código e introduza o seguinte código. Vamos analisar estes dados e guardar os resultados numa tabela chamada **nyctaxi.passengercountstats**.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistance) as SumTripDistance,
          AVG(TripDistance) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistance > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. Nos resultados da célula, selecione **Chart** para ver os dados visualizados.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Analisar dados com piscina SQL dedicada](get-started-analyze-sql-pool.md)
