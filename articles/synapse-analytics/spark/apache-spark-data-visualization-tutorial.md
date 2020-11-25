---
title: Visualizar dados com Apache Spark
description: Criar visualizações de dados ricas usando Os Cadernos analíticos Apache Spark e Azure Synapse
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 10/20/2020
ms.author: midesa
ms.openlocfilehash: 820dd8b607f5ec2fdc44d25063e0a941f76237ad
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95920169"
---
# <a name="analyze-data-with-apache-spark"></a>Analisar dados com Apache Spark

Neste tutorial, você aprenderá a realizar análises exploratórias de dados usando Azure Open Datasets e Apache Spark e, em seguida, visualizando os resultados em um Azure Synapse Studio Notebook.

Em particular, vamos analisar o conjunto de dados [de táxis de Nova Iorque (NYC).](https://azure.microsoft.com/en-us/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) Os dados estão disponíveis através de Azure Open Datasets. Este subconjunto do conjunto de dados contém informações sobre viagens de táxi amarelas, incluindo informações sobre cada viagem, o início e o tempo e locais finais, o custo e outros atributos interessantes.
  
## <a name="before-you-begin"></a>Before you begin
- Crie uma Piscina de Faíscas Apache seguindo o [tutorial de piscina Create a Apache Spark](../articles/../quickstart-create-apache-spark-pool-studio.md) 

## <a name="download-and-prepare-the-data"></a>Baixar e preparar os dados
1. Crie um caderno utilizando o núcleo PySpark. Para obter instruções, consulte [Criar um Caderno](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook). 
   
> [!Note]
> 
> Por causa do núcleo PySpark, não precisa de criar quaisquer contextos explicitamente. O contexto Spark é automaticamente criado para si quando executar a primeira célula de código.
>

2. Neste tutorial, usaremos várias bibliotecas diferentes para nos ajudar a visualizar o conjunto de dados. Para fazer esta análise, teremos de importar as seguintes bibliotecas: 

```python
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
```

3. Como os dados brutos estão num formato Parquet, pode utilizar o contexto Spark para puxar o ficheiro para a memória como um dataframe diretamente. Crie um dataframe Spark recuperando os dados através da API open Datasets. Aqui, usaremos o esquema de dataframe spark em propriedades *de leitura* para inferir os tipos de dados e esquemas.

```python
from azureml.opendatasets import NycTlcYellow
from datetime import datetime
from dateutil import parser

end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
df = nyc_tlc.to_spark_dataframe()
```

4. Uma vez lidos os dados, vamos querer fazer alguma filtragem inicial para limpar o conjunto de dados. Podemos remover colunas não importadas e adicionar colunas adicionais que extraem informações importantes. Além disso, também filtraremos anomalias dentro do conjunto de dados.

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
 Como analista de dados, tem uma vasta gama de ferramentas disponíveis para o ajudar a extrair informações dos dados. Nesta parte do tutorial, vamos percorrer algumas ferramentas úteis disponíveis nos Cadernos Azure Synapse Analytics. Nesta análise, queremos entender os fatores que produzem dicas de táxi mais elevadas para o nosso período selecionado.

###  <a name="apache-spark-sql-magic"></a>Magia Apache Spark SQL 
Em primeiro lugar, realizaremos análises exploratórias de dados por Apache Spark SQL e comandos mágicos com o Caderno Synapse. Assim que tivermos a nossa consulta, visualizaremos os resultados usando a ```chart options``` capacidade incorporada. 

1. Dentro do seu caderno, crie uma nova célula e copie o código abaixo. Usando esta consulta, queremos entender como os valores médios de gorjeta mudaram ao longo do período que selecionamos. Esta consulta também nos ajudará a identificar outros insights úteis, incluindo o valor mínimo/máximo de gorjeta por dia e o valor médio da tarifa.
   
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

2. Uma vez terminada a nossa consulta, podemos visualizar os resultados mudando para a vista do **gráfico.** Neste exemplo, criaremos um **gráfico de linha** especificando o campo como a ```day_of_month``` **chave** e como ```avgTipAmount``` o **valor**. Depois de ter feito as seleções, clique em **Aplicar** para atualizar a sua tabela. 
   
## <a name="visualize-data"></a>Visualizar os dados
Além das opções de gráficos de notebook incorporados, também pode usar bibliotecas de código aberto populares para criar as suas próprias visualizações. Nos exemplos seguintes, usaremos Seaborn e Matplotlib que são comumente usados bibliotecas Python para visualização de dados. 

> [!Note]
> 
> Por padrão, cada piscina Apache Spark Azure Synapse Analytics contém um conjunto de bibliotecas comumente usadas e padrão. Pode ver a lista completa de bibliotecas no Tempo de [Execução Azure Synapse.](../spark/apache-spark-version-support.md) documentação. Além disso, para disponibilizar código de terceiros ou construído localmente para as suas aplicações, pode [instalar uma biblioteca](../spark/apache-spark-azure-portal-add-libraries.md) numa das suas Piscinas de Faísca (pré-visualização).
>

1. Para tornar o desenvolvimento mais fácil e menos dispendioso, vamos reduzir a amostra do conjunto de dados. Usaremos a capacidade de amostragem de Apache Spark incorporada. Além disso, tanto Seaborn como Matplotlib requerem um dataframe pandas ou uma matriz numpy. Para obter um dataframe pandas, usaremos o comando para converter o ```toPandas()``` nosso dataframe.

```python
# To make development easier, faster and less expensive down sample for now
sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()
```

1. Em primeiro lugar, queremos compreender a distribuição de dicas no nosso conjunto de dados. Usaremos Matplotlib para criar um histograma mostrando a distribuição da quantidade de gorjeta e contagem. Com base na distribuição, podemos ver que as dicas são desviadas para valores inferiores ou iguais a $10.

```python
# Look at tips by count histogram using Matplotlib

ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()
```

![Histograma de Dicas](./media/apache-spark-machine-learning-mllib-notebook/histogram.png)

1. Em seguida, queremos entender a relação entre as dicas para uma determinada viagem e o dia da semana. Usaremos Seaborn para criar um enredo de caixa que ressoe as tendências para cada dia da semana. 

```python
# View the distribution of tips by day of week using Seaborn
ax = sns.boxplot(x="day_of_week", y="tipAmount",data=sampled_taxi_pd_df, showfliers = False)
ax.set_title('Tip amount distribution per day')
ax.set_xlabel('Day of Week')
ax.set_ylabel('Tip Amount ($)')
plt.show()

```
![Distribuição de dicas por dia](./media/apache-spark-data-viz/data-analyst-tutorial-per-day.png)

4. Agora, outra hipótese que temos pode ser que haja uma relação positiva entre o número de passageiros e o valor total da gorjeta de táxi. Para verificar esta relação, executaremos o seguinte código para gerar um enredo de caixa ilustrando a distribuição de dicas para cada contagem de passageiros. 
   
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
![Lote de Whisker de caixa](./media/apache-spark-machine-learning-mllib-notebook/box-whisker-plot.png)

1. Por último, queremos entender a relação entre o valor da gorjeta. Com base nos resultados, podemos ver que há várias observações onde as pessoas não dão gorjeta. No entanto, também vemos uma relação positiva entre os montantes globais da tarifa e das gorjetas.
   
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
![Lote de dispersão](./media/apache-spark-machine-learning-mllib-notebook/scatter.png)

## <a name="shut-down-the-spark-instance"></a>Desligue o exemplo da Faísca

Depois de ter terminado a execução da aplicação, desligue o portátil para libertar os recursos fechando o separador ou selecione **Sessão final** do painel de estado na parte inferior do portátil.

## <a name="see-also"></a>Ver também

- [Visão geral: Apache Spark on Azure Synapse Analytics](apache-spark-overview.md)
- [Construa um modelo de aprendizagem automática com o Apache SparkML](../spark/apache-spark-machine-learning-mllib-notebook.md)

## <a name="next-steps"></a>Passos seguintes

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentação oficial da Faísca Apache Spark](https://spark.apache.org/docs/latest/)