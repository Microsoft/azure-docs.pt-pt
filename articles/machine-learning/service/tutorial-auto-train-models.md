---
title: 'Tutorial de regressão: ML automatizado'
titleSuffix: Azure Machine Learning
description: Saiba como gerar um modelo de aprendizado de máquina usando o aprendizado de máquina automatizado. Azure Machine Learning pode executar o pré-processamento de dados, a seleção de algoritmos e a seleção de hiperparâmetro de forma automatizada para você. Em seguida, o modelo final é implantado com Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 11/04/2019
ms.openlocfilehash: 5e7d897b3a845580d7830e2cf816417f2282dd27
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74271869"
---
# <a name="tutorial-use-automated-machine-learning-to-predict-taxi-fares"></a>Tutorial: usar o Machine Learning automatizado para prever as tarifas de táxi
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste tutorial, você usa o aprendizado de máquina automatizado no Azure Machine Learning para criar um modelo de regressão para prever preços de Tarifa de táxi de NYC. Esse processo aceita dados de treinamento e definições de configuração e itera automaticamente por meio de combinações de métodos diferentes de normalização/padronização de recursos, modelos e configurações de hiperparâmetro para chegar ao melhor modelo.

![Diagrama de fluxo](./media/tutorial-auto-train-models/flow2.png)

Neste tutorial, você aprende as seguintes tarefas:

> [!div class="checklist"]
> * Baixar, transformar e limpar dados usando o Azure Open Data Sets
> * Treinar um modelo de regressão de Machine Learning automatizado
> * Calcular a precisão do modelo

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga](https://aka.ms/AMLFree) do Azure Machine Learning hoje.

## <a name="prerequisites"></a>Pré-requisitos

* Conclua o [tutorial de instalação](tutorial-1st-experiment-sdk-setup.md) se você ainda não tiver um espaço de trabalho Azure Machine Learning ou uma máquina virtual do notebook.
* Depois de concluir o tutorial de instalação, abra o bloco de anotações **tutoriais/regression-Automated-ml. ipynb** usando o mesmo servidor de bloco de anotações.

Este tutorial também está disponível no [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) se você quiser executá-lo em seu próprio [ambiente local](how-to-configure-environment.md#local). Execute `pip install azureml-sdk[automl] azureml-opendatasets azureml-widgets` para obter os pacotes necessários.

## <a name="download-and-prepare-data"></a>Baixar e preparar dados

Importe os pacotes necessários. O pacote Open DataSets contém uma classe que representa cada fonte de dados (`NycTlcGreen`, por exemplo) para filtrar facilmente os parâmetros de data antes do download.

```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Comece criando um dataframe para manter os dados de táxi. Ao trabalhar em um ambiente não Spark, os conjuntos de dados abertos só permitem o download de um mês de dado por vez, com determinadas classes para evitar `MemoryError` com grandes conjuntos.

Para baixar dados de táxi, busque iterativamente um mês por vez e antes de acrescentá-lo a `green_taxi_df` os registros 2.000 de amostra aleatória de cada mês para evitar o inflamento do dataframe. Em seguida, visualize os dados.


```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2015","%m/%d/%Y")
end = datetime.strptime("1/31/2015","%m/%d/%Y")

for sample_month in range(12):
    temp_df_green = NycTlcGreen(start + relativedelta(months=sample_month), end + relativedelta(months=sample_month)) \
        .to_pandas_dataframe()
    green_taxi_df = green_taxi_df.append(temp_df_green.sample(2000))

green_taxi_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th: só de-de-type {vertical-align: intermédia;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>lpepPickupDatetime</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>...</th>
      <th>pagamentotype</th>
      <th>fareAmount</th>
      <th>extra</th>
      <th>mtaTax</th>
      <th>improvementSurcharge</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>TRIPTYPE</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>131969</th>
      <td>2</td>
      <td>2015-01-11 05:34:44</td>
      <td>2015-01-11 05:45:03</td>
      <td>3</td>
      <td>4,84</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,88</td>
      <td>40,84</td>
      <td>-73,94</td>
      <td>...</td>
      <td>2</td>
      <td>15, 0</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0, 0</td>
      <td>0, 0</td>
      <td>Nan</td>
      <td>16,30</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1129817</th>
      <td>2</td>
      <td>2015-01-20 16:26:29</td>
      <td>2015-01-20 16:30:26</td>
      <td>1</td>
      <td>0,69</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,96</td>
      <td>40,81</td>
      <td>-73,96</td>
      <td>...</td>
      <td>2</td>
      <td>4.50</td>
      <td>1,00</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0, 0</td>
      <td>0, 0</td>
      <td>Nan</td>
      <td>6,30</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1278620</th>
      <td>2</td>
      <td>2015-01-01 05:58:10</td>
      <td>2015-01-01 06:00:55</td>
      <td>1</td>
      <td>0,45</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,92</td>
      <td>40,76</td>
      <td>-73,91</td>
      <td>...</td>
      <td>2</td>
      <td>4, 0</td>
      <td>0, 0</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0, 0</td>
      <td>0, 0</td>
      <td>Nan</td>
      <td>4,80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>348430</th>
      <td>2</td>
      <td>2015-01-17 02:20:50</td>
      <td>2015-01-17 02:41:38</td>
      <td>1</td>
      <td>0, 0</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,81</td>
      <td>40,70</td>
      <td>-73,82</td>
      <td>...</td>
      <td>2</td>
      <td>12,50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0, 0</td>
      <td>0, 0</td>
      <td>Nan</td>
      <td>13,80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1269627</th>
      <td>1</td>
      <td>2015-01-01 05:04:10</td>
      <td>2015-01-01 05:06:23</td>
      <td>1</td>
      <td>0,50</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,92</td>
      <td>40,76</td>
      <td>-73,92</td>
      <td>...</td>
      <td>2</td>
      <td>4, 0</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0</td>
      <td>0, 0</td>
      <td>0, 0</td>
      <td>Nan</td>
      <td>5,00</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>811755</th>
      <td>1</td>
      <td>2015-01-04 19:57:51</td>
      <td>2015-01-04 20:05:45</td>
      <td>2</td>
      <td>1,10</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,96</td>
      <td>40,72</td>
      <td>-73,95</td>
      <td>...</td>
      <td>2</td>
      <td>6,50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0, 0</td>
      <td>0, 0</td>
      <td>Nan</td>
      <td>7,80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>737281</th>
      <td>1</td>
      <td>2015-01-03 12:27:31</td>
      <td>2015-01-03 12:33:52</td>
      <td>1</td>
      <td>0,90</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,88</td>
      <td>40,76</td>
      <td>-73,87</td>
      <td>...</td>
      <td>2</td>
      <td>6,00</td>
      <td>0, 0</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0, 0</td>
      <td>0, 0</td>
      <td>Nan</td>
      <td>6,80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>113951</th>
      <td>1</td>
      <td>2015-01-09 23:25:51</td>
      <td>2015-01-09 23:39:52</td>
      <td>1</td>
      <td>3,30</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,96</td>
      <td>40,72</td>
      <td>-73,91</td>
      <td>...</td>
      <td>2</td>
      <td>12,50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0, 0</td>
      <td>0, 0</td>
      <td>Nan</td>
      <td>13,80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>150436</th>
      <td>2</td>
      <td>2015-01-11 17:15:14</td>
      <td>2015-01-11 17:22:57</td>
      <td>1</td>
      <td>1,19</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,94</td>
      <td>40,71</td>
      <td>-73,95</td>
      <td>...</td>
      <td>1</td>
      <td>7, 0</td>
      <td>0, 0</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>1.75</td>
      <td>0, 0</td>
      <td>Nan</td>
      <td>9,55</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>432136</th>
      <td>2</td>
      <td>2015-01-22 23:16:33</td>
      <td>2015-01-22 23:20:13</td>
      <td>1</td>
      <td>0,65</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,94</td>
      <td>40,71</td>
      <td>-73,94</td>
      <td>...</td>
      <td>2</td>
      <td>5,00</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0, 0</td>
      <td>0, 0</td>
      <td>Nan</td>
      <td>6,30</td>
      <td>1,00</td>
    </tr>
  </tbody>
</table>
<p>10 linhas × 23 colunas</p>
</div>


Agora que os dados iniciais estão carregados, defina uma função para criar vários recursos baseados em tempo do campo de retirada DateTime. Isso criará novos campos para o número do mês, o dia do mês, o dia da semana e a hora do dia e permitirá que o modelo seja fatorado em sazonalidade com base no tempo. Use a função `apply()` no dataframe para aplicar iterativamente a função `build_time_features()` a cada linha nos dados de táxi.

```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day))

green_taxi_df[["month_num", "day_of_month","day_of_week", "hour_of_day"]] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
green_taxi_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th: só de-de-type {vertical-align: intermédia;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>lpepPickupDatetime</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>...</th>
      <th>improvementSurcharge</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>TRIPTYPE</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>131969</th>
      <td>2</td>
      <td>2015-01-11 05:34:44</td>
      <td>2015-01-11 05:45:03</td>
      <td>3</td>
      <td>4,84</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,88</td>
      <td>40,84</td>
      <td>-73,94</td>
      <td>...</td>
      <td>0,3</td>
      <td>0, 0</td>
      <td>0, 0</td>
      <td>Nan</td>
      <td>16,30</td>
      <td>1,00</td>
      <td>1</td>
      <td>11</td>
      <td>6</td>
      <td>5</td>
    </tr>
    <tr>
      <th>1129817</th>
      <td>2</td>
      <td>2015-01-20 16:26:29</td>
      <td>2015-01-20 16:30:26</td>
      <td>1</td>
      <td>0,69</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,96</td>
      <td>40,81</td>
      <td>-73,96</td>
      <td>...</td>
      <td>0,3</td>
      <td>0, 0</td>
      <td>0, 0</td>
      <td>Nan</td>
      <td>6,30</td>
      <td>1,00</td>
      <td>1</td>
      <td>20</td>
      <td>1</td>
      <td>16</td>
    </tr>
    <tr>
      <th>1278620</th>
      <td>2</td>
      <td>2015-01-01 05:58:10</td>
      <td>2015-01-01 06:00:55</td>
      <td>1</td>
      <td>0,45</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,92</td>
      <td>40,76</td>
      <td>-73,91</td>
      <td>...</td>
      <td>0,3</td>
      <td>0, 0</td>
      <td>0, 0</td>
      <td>Nan</td>
      <td>4,80</td>
      <td>1,00</td>
      <td>1</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
    </tr>
    <tr>
      <th>348430</th>
      <td>2</td>
      <td>2015-01-17 02:20:50</td>
      <td>2015-01-17 02:41:38</td>
      <td>1</td>
      <td>0, 0</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,81</td>
      <td>40,70</td>
      <td>-73,82</td>
      <td>...</td>
      <td>0,3</td>
      <td>0, 0</td>
      <td>0, 0</td>
      <td>Nan</td>
      <td>13,80</td>
      <td>1,00</td>
      <td>1</td>
      <td>17</td>
      <td>5</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1269627</th>
      <td>1</td>
      <td>2015-01-01 05:04:10</td>
      <td>2015-01-01 05:06:23</td>
      <td>1</td>
      <td>0,50</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,92</td>
      <td>40,76</td>
      <td>-73,92</td>
      <td>...</td>
      <td>0</td>
      <td>0, 0</td>
      <td>0, 0</td>
      <td>Nan</td>
      <td>5,00</td>
      <td>1,00</td>
      <td>1</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
    </tr>
    <tr>
      <th>811755</th>
      <td>1</td>
      <td>2015-01-04 19:57:51</td>
      <td>2015-01-04 20:05:45</td>
      <td>2</td>
      <td>1,10</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,96</td>
      <td>40,72</td>
      <td>-73,95</td>
      <td>...</td>
      <td>0,3</td>
      <td>0, 0</td>
      <td>0, 0</td>
      <td>Nan</td>
      <td>7,80</td>
      <td>1,00</td>
      <td>1</td>
      <td>4</td>
      <td>6</td>
      <td>19</td>
    </tr>
    <tr>
      <th>737281</th>
      <td>1</td>
      <td>2015-01-03 12:27:31</td>
      <td>2015-01-03 12:33:52</td>
      <td>1</td>
      <td>0,90</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,88</td>
      <td>40,76</td>
      <td>-73,87</td>
      <td>...</td>
      <td>0,3</td>
      <td>0, 0</td>
      <td>0, 0</td>
      <td>Nan</td>
      <td>6,80</td>
      <td>1,00</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
      <td>12</td>
    </tr>
    <tr>
      <th>113951</th>
      <td>1</td>
      <td>2015-01-09 23:25:51</td>
      <td>2015-01-09 23:39:52</td>
      <td>1</td>
      <td>3,30</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,96</td>
      <td>40,72</td>
      <td>-73,91</td>
      <td>...</td>
      <td>0,3</td>
      <td>0, 0</td>
      <td>0, 0</td>
      <td>Nan</td>
      <td>13,80</td>
      <td>1,00</td>
      <td>1</td>
      <td>9</td>
      <td>4</td>
      <td>23</td>
    </tr>
    <tr>
      <th>150436</th>
      <td>2</td>
      <td>2015-01-11 17:15:14</td>
      <td>2015-01-11 17:22:57</td>
      <td>1</td>
      <td>1,19</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,94</td>
      <td>40,71</td>
      <td>-73,95</td>
      <td>...</td>
      <td>0,3</td>
      <td>1.75</td>
      <td>0, 0</td>
      <td>Nan</td>
      <td>9,55</td>
      <td>1,00</td>
      <td>1</td>
      <td>11</td>
      <td>6</td>
      <td>17</td>
    </tr>
    <tr>
      <th>432136</th>
      <td>2</td>
      <td>2015-01-22 23:16:33</td>
      <td>2015-01-22 23:20:13</td>
      <td>1</td>
      <td>0,65</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,94</td>
      <td>40,71</td>
      <td>-73,94</td>
      <td>...</td>
      <td>0,3</td>
      <td>0, 0</td>
      <td>0, 0</td>
      <td>Nan</td>
      <td>6,30</td>
      <td>1,00</td>
      <td>1</td>
      <td>22</td>
      <td>3</td>
      <td>23</td>
    </tr>
  </tbody>
</table>
<p>10 linhas × 27 colunas</p>
</div>

Remova algumas das colunas que você não precisará para treinamento ou criação de recursos adicionais.

```python
columns_to_remove = ["lpepPickupDatetime", "lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                    ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df.head(5)
```

### <a name="cleanse-data"></a>Limpar dados

Execute a função `describe()` no novo dataframe para ver as estatísticas de Resumo de cada campo.

```python
green_taxi_df.describe()
```

<div>
<style scoped> .dataframe tbody tr th: só de-de-type {vertical-align: intermédia;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>48000, 0</td>
      <td>48000, 0</td>
      <td>48000, 0</td>
      <td>48000, 0</td>
      <td>48000, 0</td>
      <td>48000, 0</td>
      <td>48000, 0</td>
      <td>48000, 0</td>
      <td>48000, 0</td>
      <td>48000, 0</td>
      <td>48000, 0</td>
      <td>48000, 0</td>
    </tr>
    <tr>
      <th>média</th>
      <td>1,78</td>
      <td>1,37</td>
      <td>2,87</td>
      <td>-73,83</td>
      <td>40,69</td>
      <td>-73,84</td>
      <td>40,70</td>
      <td>14,75</td>
      <td>6,50</td>
      <td>15,13</td>
      <td>3,27</td>
      <td>13,52</td>
    </tr>
    <tr>
      <th>padrão</th>
      <td>0.41</td>
      <td>1, 4</td>
      <td>2,93</td>
      <td>2,76</td>
      <td>1,52</td>
      <td>2.61</td>
      <td>1,44</td>
      <td>12, 8</td>
      <td>3.45</td>
      <td>8,45</td>
      <td>1,95</td>
      <td>6,83</td>
    </tr>
    <tr>
      <th>min.</th>
      <td>1,00</td>
      <td>0, 0</td>
      <td>0, 0</td>
      <td>-74,66</td>
      <td>0, 0</td>
      <td>-74,66</td>
      <td>0, 0</td>
      <td>-300, 0</td>
      <td>1,00</td>
      <td>1,00</td>
      <td>0, 0</td>
      <td>0, 0</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2, 0</td>
      <td>1,00</td>
      <td>1, 6</td>
      <td>-73,96</td>
      <td>40,70</td>
      <td>-73,97</td>
      <td>40,70</td>
      <td>7,80</td>
      <td>3.75</td>
      <td>8,00</td>
      <td>2, 0</td>
      <td>9, 0</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2, 0</td>
      <td>1,00</td>
      <td>1,90</td>
      <td>-73,94</td>
      <td>40,75</td>
      <td>-73,94</td>
      <td>40,75</td>
      <td>11,30</td>
      <td>6,50</td>
      <td>15, 0</td>
      <td>Digite 3,00</td>
      <td>15, 0</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2, 0</td>
      <td>1,00</td>
      <td>3,60</td>
      <td>-73,92</td>
      <td>40,80</td>
      <td>-73,91</td>
      <td>40,79</td>
      <td>17,80</td>
      <td>9,25</td>
      <td>22, 0</td>
      <td>5,00</td>
      <td>19, 0</td>
    </tr>
    <tr>
      <th>maximizar</th>
      <td>2, 0</td>
      <td>9, 0</td>
      <td>97,57</td>
      <td>0, 0</td>
      <td>41,93</td>
      <td>0, 0</td>
      <td>41,94</td>
      <td>450, 0</td>
      <td>12, 0</td>
      <td>30, 0</td>
      <td>6,00</td>
      <td>23, 0</td>
    </tr>
  </tbody>
</table>
</div>


Nas estatísticas de resumo, você verá que há vários campos que têm exceções ou valores que reduzirão a precisão do modelo. Primeiro filtre os campos lat/long para dentro dos limites da área Manhattan. Isso filtrará viagens ou viagens de táxi mais longos que são exceções em relação à sua relação com outros recursos.

Além disso, filtre o campo `tripDistance` para que seja maior que zero, mas menos de 31 milhas (a distância Haversine entre os dois pares lat/long). Isso elimina grandes viagens de exceção que têm custo inconsistente de viagens.

Por fim, o campo `totalAmount` tem valores negativos para as tarifas de táxi, que não fazem sentido no contexto de nosso modelo, e o campo `passengerCount` tem dados inválidos com os valores mínimos zero.

Filtre essas anomalias usando funções de consulta e, em seguida, remova as últimas colunas desnecessárias para treinamento.


```python
final_df = green_taxi_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>=0.25 and tripDistance<31")
final_df = final_df.query("passengerCount>0 and totalAmount>0")

columns_to_remove_for_training = ["pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Chame `describe()` novamente nos dados para garantir que a limpeza funcione conforme o esperado. Agora você tem um conjunto preparado e limpo de táxi, feriado e dados meteorológicos a serem usados para o treinamento do modelo de aprendizado de máquina.

```python
final_df.describe()
```

## <a name="configure-workspace"></a>Configurar a área de trabalho

Crie um objeto de área de trabalho a partir da área de trabalho existente. Um [espaço de trabalho](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) é uma classe que aceita suas informações de assinatura e recursos do Azure. Ele também cria um recurso de nuvem para monitorar e rastrear suas execuções de modelo. `Workspace.from_config()` lê o arquivo **config. JSON** e carrega os detalhes de autenticação em um objeto chamado `ws`. `ws` é utilizado em todo o restante código neste tutorial.

```python
from azureml.core.workspace import Workspace
ws = Workspace.from_config()
```

## <a name="split-the-data-into-train-and-test-sets"></a>Dividir os dados em conjuntos de treinamento e teste

Divida os dados em conjuntos de treinamento e teste usando a função `train_test_split` na biblioteca `scikit-learn`. Essa função separa os dados no conjunto de dados x (**recursos**) para treinamento de modelo e o conjunto de dados y (**valores a prever**) para teste.

O `test_size` parâmetro determina a percentagem de dados para alocar ao teste. O parâmetro `random_state` define uma semente para o gerador aleatório, de forma que suas divisões de treinamento de treino sejam determinísticas.

```python
from sklearn.model_selection import train_test_split

y_df = final_df.pop("totalAmount")
x_df = final_df

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=223)
```

A finalidade desta etapa é ter pontos de dados para testar o modelo concluído que não foi usado para treinar o modelo, a fim de medir a precisão real.

Em outras palavras, um modelo bem treinado deve ser capaz de fazer previsões com precisão dos dados que ele ainda não viu. Agora você tem dados preparados para treinar automaticamente um modelo de aprendizado de máquina.

## <a name="automatically-train-a-model"></a>Preparar automaticamente um modelo

Para treinar um modelo automaticamente, execute as seguintes etapas:
1. Defina as configurações para a execução do experimento. Anexe seus dados de treinamento à configuração e modifique as configurações que controlam o processo de treinamento.
1. Envie o experimento para o ajuste do modelo. Depois de enviar o experimento, o processo é iterado por meio de diferentes algoritmos de aprendizado de máquina e configurações de hiperparâmetro, respeitando suas restrições definidas. Ele escolhe o modelo de melhor ajuste, otimizando uma métrica de precisão.

### <a name="define-training-settings"></a>Definir configurações de treinamento

Defina o parâmetro experimento e as configurações de modelo para treinamento. Ver a lista completa dos [definições](how-to-configure-auto-train.md). O envio do experimento com essas configurações padrão levará aproximadamente 5-20 min, mas se você quiser um tempo de execução menor, reduza o parâmetro `experiment_timeout_minutes`.

|Propriedade| Valor neste tutorial |Descrição|
|----|----|---|
|**iteration_timeout_minutes**|2|Limite de tempo em minutos para cada iteração. Reduza esse valor para diminuir o tempo de execução total.|
|**experiment_timeout_minutes**|20|Quantidade máxima de tempo em minutos que todas as iterações combinadas podem levar antes que o experimento seja encerrado.|
|**enable_early_stopping**|Verdadeiro|Sinalizador para enble encerramento antecipado se a pontuação não estiver melhorando em curto prazo.|
|**primary_metric**| spearman_correlation | Métrica que pretende otimizar. O modelo de melhor ajuste será escolhido com base nessa métrica.|
|**personalização**| Automático | Usando **auto**, o experimento pode pré-processar os dados de entrada (manipulando dados ausentes, convertendo texto em numérico, etc.)|
|**Verbosidade**| logging.INFO | Controla o nível de registo.|
|**n_cross_validations**|5|Número de divisões de validação cruzada a serem executadas quando os dados de validação não forem especificados.|

```python
import logging

automl_settings = {
    "iteration_timeout_minutes": 2,
    "experiment_timeout_minutes": 20,
    "enable_early_stopping": True,
    "primary_metric": 'spearman_correlation',
    "featurization": 'auto',
    "verbosity": logging.INFO,
    "n_cross_validations": 5
}
```

Use as configurações de treinamento definidas como um parâmetro `**kwargs` para um objeto `AutoMLConfig`. Além disso, especifique os dados de treinamento e o tipo de modelo, que é `regression` nesse caso.

```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             X=x_train.values,
                             y=y_train.values.flatten(),
                             **automl_settings)
```

> [!NOTE]
> As etapas de pré-processamento automatizado do Machine Learning (normalização de recursos, manipulação de dados ausentes, conversão de texto em numeric, etc.) tornam-se parte do modelo subjacente. Ao usar o modelo para previsões, as mesmas etapas de pré-processamento aplicadas durante o treinamento são aplicadas aos dados de entrada automaticamente.

### <a name="train-the-automatic-regression-model"></a>Preparar o modelo de regressão automática

Crie um objeto de experimento em seu espaço de trabalho. Um experimento atua como um contêiner para suas execuções individuais. Passe o objeto `automl_config` definido para o experimento e defina a saída como `True` para exibir o andamento durante a execução.

Depois de iniciar o experimento, a saída mostrava as atualizações ativas conforme o experimento é executado. Para cada iteração, você vê o tipo de modelo, a duração da execução e a precisão do treinamento. O campo `BEST` controla a melhor pontuação de treinamento em execução com base em seu tipo de métrica.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, "taxi-experiment")
local_run = experiment.submit(automl_config, show_output=True)
```

    Running on local machine
    Parent Run ID: AutoML_1766cdf7-56cf-4b28-a340-c4aeee15b12b
    Current status: DatasetFeaturization. Beginning to featurize the dataset.
    Current status: DatasetEvaluation. Gathering dataset statistics.
    Current status: FeaturesGeneration. Generating features for the dataset.
    Current status: DatasetFeaturizationCompleted. Completed featurizing the dataset.
    Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
    Current status: ModelSelection. Beginning model selection.

    ****************************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ****************************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   StandardScalerWrapper RandomForest             0:00:16       0.8746    0.8746
             1   MinMaxScaler RandomForest                      0:00:15       0.9468    0.9468
             2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.9303    0.9468
             3   StandardScalerWrapper LightGBM                 0:00:10       0.9424    0.9468
             4   RobustScaler DecisionTree                      0:00:09       0.9449    0.9468
             5   StandardScalerWrapper LassoLars                0:00:09       0.9440    0.9468
             6   StandardScalerWrapper LightGBM                 0:00:10       0.9282    0.9468
             7   StandardScalerWrapper RandomForest             0:00:12       0.8946    0.9468
             8   StandardScalerWrapper LassoLars                0:00:16       0.9439    0.9468
             9   MinMaxScaler ExtremeRandomTrees                0:00:35       0.9199    0.9468
            10   RobustScaler ExtremeRandomTrees                0:00:19       0.9411    0.9468
            11   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9077    0.9468
            12   StandardScalerWrapper LassoLars                0:00:15       0.9433    0.9468
            13   MinMaxScaler ExtremeRandomTrees                0:00:14       0.9186    0.9468
            14   RobustScaler RandomForest                      0:00:10       0.8810    0.9468
            15   StandardScalerWrapper LassoLars                0:00:55       0.9433    0.9468
            16   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9026    0.9468
            17   StandardScalerWrapper RandomForest             0:00:13       0.9140    0.9468
            18   VotingEnsemble                                 0:00:23       0.9471    0.9471
            19   StackEnsemble                                  0:00:27       0.9463    0.9471

## <a name="explore-the-results"></a>Explorar os resultados

Explore os resultados do treinamento automático com um [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). O widget permite que você veja um grafo e uma tabela de todas as iterações de execução individuais, juntamente com metadados e métricas de precisão de treinamento. Além disso, você pode filtrar em métricas de precisão diferentes da métrica primária com o seletor de lista suspensa.

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![detalhes de execução do widget do Jupyter](./media/tutorial-auto-train-models/automl-dash-output.png)
![diagrama de Jupyter do widget](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="retrieve-the-best-model"></a>Obter o melhor modelo

Selecione o melhor modelo de suas iterações. A função `get_output` retorna a melhor execução e o modelo ajustado para a última invocação de ajuste. Usando as sobrecargas em `get_output`, você pode recuperar o melhor modelo de execução e ajustada para qualquer métrica registrada ou uma iteração específica.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

### <a name="test-the-best-model-accuracy"></a>A maior precisão do modelo de teste

Use o melhor modelo para executar previsões no conjunto de dados de teste para prever tarifas de táxi. A função `predict` usa o melhor modelo e prevê os valores de y, **custo de viagem**, do conjunto de dados de `x_test`. Imprimir os primeiros 10 prevista valores a partir de custos `y_predict`.

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Calcule a `root mean squared error` dos resultados. Converta o `y_test` dataframe em uma lista para comparar com os valores previstos. A função `mean_squared_error` usa duas matrizes de valores e calcula o erro de quadrado médio entre elas. Pegar a raiz quadrada do resultado resulta em um erro nas mesmas unidades que a variável y, **custo**. Indica aproximadamente até onde as previsões de Tarifa de táxi são provenientes das tarifas reais.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Execute o código a seguir para calcular o erro de porcentagem absoluta média (MAPE) usando os conjuntos de dados Full `y_actual` e `y_predict`. Essa métrica calcula uma diferença absoluta entre cada valor previsto e real e soma todas as diferenças. Em seguida, ele expressa essa soma como uma porcentagem do total dos valores reais.

```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

    Model MAPE:
    0.14353867606052823

    Model Accuracy:
    0.8564613239394718


Das duas métricas de precisão da previsão, você vê que o modelo é muito bom na previsão de passagens de táxi dos recursos do conjunto de dados, normalmente dentro de +-$4 e aproximadamente 15% de erro.

A processo de desenvolvimento do modelo de aprendizagem tradicional é altamente com muitos recursos e requer investimento de dados de conhecimento e a hora de domínio significativo para executar e comparar os resultados de dezenas de modelos. Usar o Machine Learning automatizado é uma ótima maneira de testar rapidamente vários modelos diferentes para seu cenário.

## <a name="clean-up-resources"></a>Limpar recursos

Não conclua esta seção se você planeja executar outros tutoriais de Azure Machine Learning.

### <a name="stop-the-notebook-vm"></a>Parar a VM do notebook

[!INCLUDE [aml-stop-server](../../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Excluir tudo

Se você não planeja usar os recursos que criou, exclua-os, para não incorrer em nenhum encargo.

1. No portal do Azure, selecione **Grupos de recursos** na extremidade esquerda.
1. Na lista, selecione o grupo de recursos que criou.
1. Selecione **Eliminar grupo de recursos**.
1. Insira o nome do grupo de recursos. Em seguida, selecione **Eliminar**.

Você também pode manter o grupo de recursos, mas excluir um único espaço de trabalho. Exiba as propriedades do espaço de trabalho e selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial de aprendizado de máquina automatizado, você fez as seguintes tarefas:

> [!div class="checklist"]
> * Configurou um espaço de trabalho e dados preparados para um experimento.
> * Treinado com o uso de um modelo de regressão automatizado localmente com parâmetros personalizados.
> * Resultados de treinamento explorados e revisados.

[Implementar o seu modelo](tutorial-deploy-models-with-aml.md) com o Azure Machine Learning.
