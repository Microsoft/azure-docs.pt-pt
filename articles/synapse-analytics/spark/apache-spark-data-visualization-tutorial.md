---
title: Visualizar dados com o Apache Spark
description: Crie visualizações de dados ricas utilizando cadernos Apache Spark e Azure Synapse Analytics
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 10/20/2020
ms.author: midesa
ms.openlocfilehash: 56af49b6fa862c93822293056752182b534ac442
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942266"
---
# <a name="analyze-data-with-apache-spark"></a>Analisar dados com Apache Spark

Neste tutorial, você aprenderá a realizar análises exploratórias de dados usando Azure Open Datasets e Apache Spark. Em seguida, pode visualizar os resultados num caderno do Estúdio Synapse em Azure Synapse Analytics.

Em particular, vamos analisar o conjunto de dados [de táxis de Nova Iorque (NYC).](https://azure.microsoft.com/en-us/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) Os dados estão disponíveis através de Azure Open Datasets. Este subconjunto do conjunto de dados contém informações sobre viagens de táxi amarelas: informações sobre cada viagem, o tempo e locais de início e fim, o custo e outros atributos interessantes.
  
## <a name="before-you-begin"></a>Antes de começar
Crie uma Piscina de Faíscas Apache seguindo o [tutorial de piscina Create a Apache Spark.](../articles/../quickstart-create-apache-spark-pool-studio.md) 

## <a name="download-and-prepare-the-data"></a>Baixar e preparar os dados
1. Crie um caderno utilizando o núcleo PySpark. Para obter instruções, consulte [Criar um caderno](../quickstart-apache-spark-notebook.md#create-a-notebook). 
   
   > [!Note]
   > Por causa do núcleo PySpark, não precisa de criar nenhum contexto explicitamente. O contexto Spark é automaticamente criado para si quando executar a primeira célula de código.

2. Neste tutorial, vamos usar várias bibliotecas diferentes para nos ajudar a visualizar o conjunto de dados. Para fazer esta análise, importar as seguintes bibliotecas: 

   ```python
   import matplotlib.pyplot as plt
   import seaborn as sns
   import pandas as pd
   ```

3. Como os dados brutos estão num formato Parquet, pode utilizar o contexto Spark para puxar o ficheiro para a memória como um DataFrame diretamente. Crie um DataFrame de faísca recuperando os dados através da API open Datasets. Aqui, usamos o *esquema* do DataFrame de Faísca em propriedades de leitura para inferir os tipos de dados e esquemas.

   ```python
   from azureml.opendatasets import NycTlcYellow
   from datetime import datetime
   from dateutil import parser

   end_date = parser.parse('2018-06-06')
   start_date = parser.parse('2018-05-01')
   nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
   df = nyc_tlc.to_spark_dataframe()
   ```

4. Depois de lidos os dados, vamos querer fazer alguma filtragem inicial para limpar o conjunto de dados. Podemos remover colunas não importadas e adicionar colunas que extraam informações importantes. Além disso, filtraremos anomalias dentro do conjunto de dados.

   ```python
   # Filter the dataset 
   from pyspark.sql.functions import *

   filtered_df = df.select('vendorID', 'passengerCount', 'tripDistance','paymentType', 'fareAmount', 'tipAmount'\
                                   , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                   , dayofweek('tpepPickupDateTime').alias('day_of_week')\
                                   , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month'))\
                               .filter((df.passengerCount > 0)\
                                   & (df.tipAmount >= 0)\
                                   & (df.fareAmount >= 1) & (df.fareAmount <= 250)\
                                   & (df.tripDistance > 0) & (df.tripDistance <= 200))

   filtered_df.createOrReplaceTempView("taxi_dataset")
   ```

## <a name="analyze-data"></a>Analisar dados
Como analista de dados, tem uma vasta gama de ferramentas disponíveis para o ajudar a extrair informações dos dados. Nesta parte do tutorial, vamos percorrer algumas ferramentas úteis disponíveis nos cadernos Azure Synapse Analytics. Nesta análise, queremos entender os fatores que produzem dicas de táxi mais elevadas para o nosso período selecionado.

### <a name="apache-spark-sql-magic"></a>Magia Apache Spark SQL 
Primeiro, faremos análises exploratórias de dados por Apache Spark SQL e comandos mágicos com o caderno Azure Synapse. Depois de termos a nossa consulta, visualizaremos os resultados usando a ```chart options``` capacidade incorporada. 

1. Dentro do seu caderno, crie uma nova célula e copie o seguinte código. Ao utilizar esta consulta, queremos entender como os valores médios das gorjetas mudaram durante o período que selecionamos. Esta consulta também nos ajudará a identificar outros insights úteis, incluindo o valor mínimo/máximo de gorjeta por dia e o valor médio da tarifa.
   
   ```sql
   %%sql
   SELECT 
       day_of_month
       , MIN(tipAmount) AS minTipAmount
       , MAX(tipAmount) AS maxTipAmount
       , AVG(tipAmount) AS avgTipAmount
       , AVG(fareAmount) as fareAmount
   FROM taxi_dataset 
   GROUP BY day_of_month
   ORDER BY day_of_month ASC
   ```

2. Depois de terminarmos a nossa consulta, podemos visualizar os resultados mudando para a vista do gráfico. Este exemplo cria um gráfico de linha especificando o ```day_of_month``` campo como a chave e como o ```avgTipAmount``` valor. Depois de ter feito as seleções, **selecione Aplicar** para atualizar a sua tabela. 
   
## <a name="visualize-data"></a>Visualizar os dados
Além das opções de gráficos de cadernos incorporados, pode utilizar bibliotecas de código aberto populares para criar as suas próprias visualizações. Nos seguintes exemplos, usaremos Seaborn e Matplotlib. Estas são comumente usadas bibliotecas Python para visualização de dados. 

> [!Note]
> Por padrão, cada piscina Apache Spark em Azure Synapse Analytics contém um conjunto de bibliotecas comumente usadas e padrão. Pode ver a lista completa de bibliotecas na documentação de execução do [Azure Synapse.](../spark/apache-spark-version-support.md) Além disso, para disponibilizar código de terceiros ou construído localmente para as suas aplicações, pode [instalar uma biblioteca](../spark/apache-spark-azure-portal-add-libraries.md) numa das suas piscinas Spark.

1. Para tornar o desenvolvimento mais fácil e menos dispendioso, vamos reduzir o conjunto de dados. Usaremos a capacidade de amostragem de Apache Spark incorporada. Além disso, tanto Seaborn como Matplotlib requerem uma matriz de DataFrame ou NumPy pandas. Para obter um DataFrame pandas, use o ```toPandas()``` comando para converter o DataFrame.

   ```python
   # To make development easier, faster, and less expensive, downsample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   # The charting package needs a Pandas DataFrame or NumPy array to do the conversion
   sampled_taxi_pd_df = sampled_taxi_df.toPandas()
   ```

1. Queremos compreender a distribuição de dicas no nosso conjunto de dados. Usaremos Matplotlib para criar um histograma que mostre a distribuição da gorjeta e contagem. Com base na distribuição, podemos ver que as dicas são desviadas para valores inferiores ou iguais a $10.

   ```python
   # Look at a histogram of tips by count by using Matplotlib

   ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
   ax1.set_title('Tip amount distribution')
   ax1.set_xlabel('Tip Amount ($)')
   ax1.set_ylabel('Counts')
   plt.suptitle('')
   plt.show()
   ```

   ![Histograma de gorjetas.](./media/apache-spark-machine-learning-mllib-notebook/histogram.png)

1. Em seguida, queremos entender a relação entre as dicas para uma determinada viagem e o dia da semana. Use Seaborn para criar um enredo de caixa que resume as tendências para cada dia da semana. 

   ```python
   # View the distribution of tips by day of week using Seaborn
   ax = sns.boxplot(x="day_of_week", y="tipAmount",data=sampled_taxi_pd_df, showfliers = False)
   ax.set_title('Tip amount distribution per day')
   ax.set_xlabel('Day of Week')
   ax.set_ylabel('Tip Amount ($)')
   plt.show()

   ```
   ![Gráfico que mostra a distribuição de dicas por dia.](./media/apache-spark-data-viz/data-analyst-tutorial-per-day.png)

4. Outra hipótese nossa pode ser que haja uma relação positiva entre o número de passageiros e o valor total da gorjeta de táxi. Para verificar esta relação, execute o seguinte código para gerar um enredo de caixa que ilustra a distribuição de dicas para cada contagem de passageiros. 
   
   ```python
   # How many passengers tipped by various amounts 
   ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
   ax2.set_title('Tip amount by Passenger count')
   ax2.set_xlabel('Passenger count')
   ax2.set_ylabel('Tip Amount ($)')
   ax2.set_ylim(0,30)
   plt.suptitle('')
   plt.show()
   ```
   ![Gráfico que mostra um enredo de bigode de caixa.](./media/apache-spark-machine-learning-mllib-notebook/box-whisker-plot.png)

1. Por último, queremos entender a relação entre o valor da tarifa e o valor da gorjeta. Com base nos resultados, podemos ver que há várias observações onde as pessoas não dão gorjeta. No entanto, também vemos uma relação positiva entre os montantes globais da tarifa e das gorjetas.
   
   ```python
   # Look at the relationship between fare and tip amounts

   ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
   ax.set_title('Tip amount by Fare amount')
   ax.set_xlabel('Fare Amount ($)')
   ax.set_ylabel('Tip Amount ($)')
   plt.axis([-2, 80, -2, 20])
   plt.suptitle('')
   plt.show()
   ```
   ![Lote de inclinação.](./media/apache-spark-machine-learning-mllib-notebook/scatter.png)

## <a name="shut-down-the-spark-instance"></a>Desligue o exemplo da Faísca

Depois de terminar a execução da aplicação, desligue o caderno para libertar os recursos. Feche o separador ou selecione **Sessão final** do painel de estado na parte inferior do caderno.

## <a name="see-also"></a>Veja também

- [Visão geral: Apache Spark on Azure Synapse Analytics](apache-spark-overview.md)
- [Construa um modelo de aprendizagem automática com o Apache SparkML](../spark/apache-spark-machine-learning-mllib-notebook.md)

## <a name="next-steps"></a>Próximos passos

- [Azure Synapse Analytics](../index.yml)
- [Documentação oficial da Faísca Apache Spark](https://spark.apache.org/docs/latest/)