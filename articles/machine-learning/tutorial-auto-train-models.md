---
title: 'Tutorial de regressão: ML automatizado'
titleSuffix: Azure Machine Learning
description: Neste tutorial, aprende-se a gerar um modelo de aprendizagem automática utilizando machine learning automatizado. O Azure Machine Learning pode realizar o pré-processamento de dados, a seleção de algoritmos e a seleção de hiperparâmetros de forma automatizada para si.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.openlocfilehash: 75e61ea3f4fa6c2b346f912a9effd66ad94e7e93
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77116444"
---
# <a name="tutorial-use-automated-machine-learning-to-predict-taxi-fares"></a>Tutorial: Use machine learning automatizado para prever tarifas de táxi
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste tutorial, você usa machine learning automatizado em Azure Machine Learning para criar um modelo de regressão para prever os preços das tarifas de táxi de NYC. Este processo aceita configurações de dados de formação e configuração, e automaticamente iterates através de combinações de diferentes métodos de normalização/normalização de funcionalidades, modelos e configurações de hiperparâmetros para chegar ao melhor modelo.

![Diagrama de fluxo](./media/tutorial-auto-train-models/flow2.png)

Neste tutorial aprende-se as seguintes tarefas:

> [!div class="checklist"]
> * Descarregue, transforme e limpe dados usando conjuntos de dados abertos do Azure
> * Treine um modelo automatizado de regressão de aprendizagem automática
> * Calcular a precisão do modelo

Se não tiver uma subscrição Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga](https://aka.ms/AMLFree) do Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

* Complete o tutorial de [configuração](tutorial-1st-experiment-sdk-setup.md) se ainda não tiver um espaço de trabalho Azure Machine Learning ou uma máquina virtual de caderno.
* Depois de concluir o tutorial de configuração, abra os *tutoriais/regressão-automl-nyc-taxi-data/regressão-automatizado-ml.ipynb* com o mesmo servidor de caderno.

Este tutorial também está disponível no [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) se você deseja executá-lo em seu próprio [ambiente local](how-to-configure-environment.md#local). Faça `pip install azureml-sdk[automl] azureml-opendatasets azureml-widgets` para obter os pacotes necessários.

## <a name="download-and-prepare-data"></a>Descarregue e prepare dados

Importar os pacotes necessários. O pacote Open Datasets contém uma classe que representa cada fonte de dados (`NycTlcGreen` por exemplo) para filtrar facilmente os parâmetros da data antes de ser descarregado.

```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Comece por criar um dataframe para guardar os dados do táxi. Quando se trabalha num ambiente não-Spark, o Open Datasets apenas permite o download de um mês de dados de cada vez com certas classes para evitar `MemoryError` com grandes conjuntos de dados.

Para descarregar dados de táxi, iterativamente buscar um mês de cada vez, e antes de os anexar para `green_taxi_df` amostras aleatórias de 2.000 registos de cada mês para evitar inchar o dataframe. Em seguida, pré-visualizar os dados.


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
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

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
      <th>fornecedorID</th>
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
      <th>pagamentoTipo</th>
      <th>fareAmount</th>
      <th>extra</th>
      <th>mtaTax</th>
      <th>melhoriaA sobretaxa</th>
      <th>tipAmount</th>
      <th>portagensAmount</th>
      <th>ehailFee</th>
      <th>totalMontante</th>
      <th>tripTypeType</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>131969</th>
      <td>2</td>
      <td>2015-01-11 05:34:44</td>
      <td>2015-01-11 05:45:03</td>
      <td>3</td>
      <td>4.84</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.88</td>
      <td>40.84</td>
      <td>-73.94</td>
      <td>...</td>
      <td>2</td>
      <td>15.00</td>
      <td>0.50</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>16.30</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1129817</th>
      <td>2</td>
      <td>2015-01-20 16:26:29</td>
      <td>2015-01-20 16:30:26</td>
      <td>1</td>
      <td>0.69</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.96</td>
      <td>40.81</td>
      <td>-73.96</td>
      <td>...</td>
      <td>2</td>
      <td>4.50</td>
      <td>1,00</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>6.30</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1278620</th>
      <td>2</td>
      <td>2015-01-01 05:58:10</td>
      <td>2015-01-01 06:00:55</td>
      <td>1</td>
      <td>0.45</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.92</td>
      <td>40.76</td>
      <td>-73.91</td>
      <td>...</td>
      <td>2</td>
      <td>4.00</td>
      <td>0.00</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>4.80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>348430</th>
      <td>2</td>
      <td>2015-01-17 02:20:50</td>
      <td>2015-01-17 02:41:38</td>
      <td>1</td>
      <td>0.00</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.81</td>
      <td>40.70</td>
      <td>-73.82</td>
      <td>...</td>
      <td>2</td>
      <td>12.50</td>
      <td>0.50</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>13.80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1269627</th>
      <td>1</td>
      <td>2015-01-01 05:04:10</td>
      <td>2015-01-01 05:06:23</td>
      <td>1</td>
      <td>0.50</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.92</td>
      <td>40.76</td>
      <td>-73.92</td>
      <td>...</td>
      <td>2</td>
      <td>4.00</td>
      <td>0.50</td>
      <td>0.50</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>5,00</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>811755</th>
      <td>1</td>
      <td>2015-01-04 19:57:51</td>
      <td>2015-01-04 20:05:45</td>
      <td>2</td>
      <td>1.10</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.96</td>
      <td>40.72</td>
      <td>-73.95</td>
      <td>...</td>
      <td>2</td>
      <td>6,50</td>
      <td>0.50</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>7.80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>737281</th>
      <td>1</td>
      <td>2015-01-03 12:27:31</td>
      <td>2015-01-03 12:33:52</td>
      <td>1</td>
      <td>0.90</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.88</td>
      <td>40.76</td>
      <td>-73.87</td>
      <td>...</td>
      <td>2</td>
      <td>6.00</td>
      <td>0.00</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>6.80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>113951</th>
      <td>1</td>
      <td>2015-01-09 23:25:51</td>
      <td>2015-01-09 23:39:52</td>
      <td>1</td>
      <td>3.30</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.96</td>
      <td>40.72</td>
      <td>-73.91</td>
      <td>...</td>
      <td>2</td>
      <td>12.50</td>
      <td>0.50</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>13.80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>150436</th>
      <td>2</td>
      <td>2015-01-11 17:15:14</td>
      <td>2015-01-11 17:22:57</td>
      <td>1</td>
      <td>1.19</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.94</td>
      <td>40.71</td>
      <td>-73.95</td>
      <td>...</td>
      <td>1</td>
      <td>7.00</td>
      <td>0.00</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>1.75</td>
      <td>0.00</td>
      <td>nan</td>
      <td>9.55</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>432136</th>
      <td>2</td>
      <td>2015-01-22 23:16:33</td>
      <td>2015-01-22 23:20:13</td>
      <td>1</td>
      <td>0.65</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.94</td>
      <td>40.71</td>
      <td>-73.94</td>
      <td>...</td>
      <td>2</td>
      <td>5,00</td>
      <td>0.50</td>
      <td>0.50</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>6.30</td>
      <td>1,00</td>
    </tr>
  </tbody>
</table>
<p>10 linhas × 23 colunas</p>
</div>


Agora que os dados iniciais são carregados, defina uma função para criar várias funcionalidades baseadas no tempo a partir do campo de data de recolha. Isto criará novos campos para o número mensal, dia do mês, dia da semana e hora do dia, e permitirá que o modelo factorem na sazonalidade baseada no tempo. Utilize a função `apply()` no quadro de dados para aplicar iterativamente a função `build_time_features()` a cada linha nos dados do táxi.

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
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

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
      <th>fornecedorID</th>
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
      <th>melhoriaA sobretaxa</th>
      <th>tipAmount</th>
      <th>portagensAmount</th>
      <th>ehailFee</th>
      <th>totalMontante</th>
      <th>tripTypeType</th>
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
      <td>4.84</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.88</td>
      <td>40.84</td>
      <td>-73.94</td>
      <td>...</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>16.30</td>
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
      <td>0.69</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.96</td>
      <td>40.81</td>
      <td>-73.96</td>
      <td>...</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>6.30</td>
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
      <td>0.45</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.92</td>
      <td>40.76</td>
      <td>-73.91</td>
      <td>...</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>4.80</td>
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
      <td>0.00</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.81</td>
      <td>40.70</td>
      <td>-73.82</td>
      <td>...</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>13.80</td>
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
      <td>0.50</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.92</td>
      <td>40.76</td>
      <td>-73.92</td>
      <td>...</td>
      <td>0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
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
      <td>1.10</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.96</td>
      <td>40.72</td>
      <td>-73.95</td>
      <td>...</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>7.80</td>
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
      <td>0.90</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.88</td>
      <td>40.76</td>
      <td>-73.87</td>
      <td>...</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>6.80</td>
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
      <td>3.30</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.96</td>
      <td>40.72</td>
      <td>-73.91</td>
      <td>...</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>13.80</td>
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
      <td>1.19</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.94</td>
      <td>40.71</td>
      <td>-73.95</td>
      <td>...</td>
      <td>0.3</td>
      <td>1.75</td>
      <td>0.00</td>
      <td>nan</td>
      <td>9.55</td>
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
      <td>0.65</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.94</td>
      <td>40.71</td>
      <td>-73.94</td>
      <td>...</td>
      <td>0.3</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>6.30</td>
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

Remova algumas das colunas que não necessitará para treino ou construção de recursos adicionais.

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

Executar a função `describe()` no novo quadro de dados para ver estatísticas sumárias para cada campo.

```python
green_taxi_df.describe()
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

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
      <th>fornecedorID</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalMontante</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
    </tr>
    <tr>
      <th>média</th>
      <td>1.78</td>
      <td>1.37</td>
      <td>2.87</td>
      <td>-73.83</td>
      <td>40.69</td>
      <td>-73.84</td>
      <td>40.70</td>
      <td>14.75</td>
      <td>6,50</td>
      <td>15.13</td>
      <td>3.27</td>
      <td>13.52</td>
    </tr>
    <tr>
      <th>std</th>
      <td>0.41</td>
      <td>1.04</td>
      <td>2.93</td>
      <td>2.76</td>
      <td>1.52</td>
      <td>2.61</td>
      <td>1.44</td>
      <td>12.08</td>
      <td>3.45</td>
      <td>8.45</td>
      <td>1.95</td>
      <td>6.83</td>
    </tr>
    <tr>
      <th>mín</th>
      <td>1,00</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>-74.66</td>
      <td>0.00</td>
      <td>-74.66</td>
      <td>0.00</td>
      <td>-300.00</td>
      <td>1,00</td>
      <td>1,00</td>
      <td>0.00</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2.00</td>
      <td>1,00</td>
      <td>1.06</td>
      <td>-73.96</td>
      <td>40.70</td>
      <td>-73.97</td>
      <td>40.70</td>
      <td>7.80</td>
      <td>3.75</td>
      <td>8.00</td>
      <td>2.00</td>
      <td>9, 0</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2.00</td>
      <td>1,00</td>
      <td>1.90</td>
      <td>-73.94</td>
      <td>40.75</td>
      <td>-73.94</td>
      <td>40.75</td>
      <td>11.30</td>
      <td>6,50</td>
      <td>15.00</td>
      <td>Digite 3,00</td>
      <td>15.00</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2.00</td>
      <td>1,00</td>
      <td>3.60</td>
      <td>-73.92</td>
      <td>40.80</td>
      <td>-73.91</td>
      <td>40.79</td>
      <td>17.80</td>
      <td>9.25</td>
      <td>22.00</td>
      <td>5,00</td>
      <td>19.00</td>
    </tr>
    <tr>
      <th>máx</th>
      <td>2.00</td>
      <td>9, 0</td>
      <td>97.57</td>
      <td>0.00</td>
      <td>41.93</td>
      <td>0.00</td>
      <td>41.94</td>
      <td>450.00</td>
      <td>12.00</td>
      <td>30.00</td>
      <td>6.00</td>
      <td>23.00</td>
    </tr>
  </tbody>
</table>
</div>


A partir das estatísticas sumárias, vê-se que existem vários campos que têm valores ou valores mais distantes que reduzirão a precisão do modelo. Primeiro filtrar os campos de lat/longo para estar dentro dos limites da área de Manhattan. Isto irá filtrar viagens de táxi mais longas ou viagens que são mais estranhas em relação à sua relação com outras características.

Além disso, filtrar o campo `tripDistance` ser superior a zero, mas menos de 31 milhas (a distância haversina entre os dois pares lat/longo). Isto elimina viagens longas e mais estranhas que têm um custo de viagem inconsistente.

Por último, o campo `totalAmount` tem valores negativos para as tarifas de táxi, o que não faz sentido no contexto do nosso modelo, e o campo `passengerCount` tem dados negativos, com os valores mínimos a serem nulos.

Filtrar estas anomalias utilizando funções de consulta e, em seguida, remover as últimas colunas desnecessárias para o treino.


```python
final_df = green_taxi_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>=0.25 and tripDistance<31")
final_df = final_df.query("passengerCount>0 and totalAmount>0")

columns_to_remove_for_training = ["pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Ligue `describe()` novamente nos dados para garantir que a limpeza funcionou como esperado. Tem agora um conjunto preparado e limpo de dados de táxi, férias e meteorologia para usar para a formação de modelos de aprendizagem automática.

```python
final_df.describe()
```

## <a name="configure-workspace"></a>Configurar a área de trabalho

Crie um objeto de área de trabalho a partir da área de trabalho existente. Um [Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) é uma classe que aceita a sua informação de subscrição e recursos Azure. Também cria um recurso em nuvem para monitorizar e rastrear as execuções do seu modelo. `Workspace.from_config()` lê o ficheiro **config.json** e carrega os detalhes da autenticação num objeto chamado `ws`. `ws` é utilizado em todo o restante código neste tutorial.

```python
from azureml.core.workspace import Workspace
ws = Workspace.from_config()
```

## <a name="split-the-data-into-train-and-test-sets"></a>Divida os dados em conjuntos de comboios e testes

Divida os dados em conjuntos de treino e teste utilizando a função `train_test_split` na biblioteca `scikit-learn`. Esta função segrega os dados no conjunto de dados x **(características)** para treino de modelos e o conjunto de dados y **(valores a prever**) para testes.

O parâmetro `test_size` determina a percentagem de dados a atribuir aos testes. O parâmetro `random_state` define uma semente para o gerador aleatório, de modo que as suas divisões de teste de comboio são determinísticas.

```python
from sklearn.model_selection import train_test_split

y_df = final_df.pop("totalAmount")
x_df = final_df

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=223)
```

O objetivo deste passo é ter pontos de dados para testar o modelo acabado que não foi usado para treinar o modelo, de forma a medir a verdadeira precisão.

Por outras palavras, um modelo bem treinado deve ser capaz de fazer previsões com precisão a partir de dados que ainda não viu. Agora tem dados preparados para treinar automaticamente um modelo de aprendizagem automática.

## <a name="automatically-train-a-model"></a>Preparar automaticamente um modelo

Para treinar automaticamente um modelo, dê os seguintes passos:
1. Defina as definições para a execução da experiência. Fixe os seus dados de treino à configuração e modifique as definições que controlam o processo de treino.
1. Submeta a experiência para afinação do modelo. Depois de submeter a experiência, o processo iterates através de diferentes algoritmos de aprendizagem automática e configurações de hiperparâmetros, aderindo aos seus constrangimentos definidos. Escolhe o modelo mais adequado otimizando uma métrica de precisão.

### <a name="define-training-settings"></a>Definir definições de formação

Defina as definições de parâmetros de experimentação e modelo para treino. Ver a lista completa de [definições](how-to-configure-auto-train.md). Submeter a experiência com estas definições predefinidas demorará aproximadamente 5-20 min, mas se quiser um prazo de execução mais curto, reduza o parâmetro `experiment_timeout_minutes`.

|Propriedade| Valor neste tutorial |Descrição|
|----|----|---|
|**iteration_timeout_minutes**|2|Limite de tempo em minutos para cada iteração. Reduza este valor para diminuir o tempo total de execução.|
|**experiment_timeout_minutes**|20|O máximo de tempo em minutos que todas as iterações combinadas podem levar antes do fim da experiência.|
|**enable_early_stopping**|Verdadeiro|Bandeira para permitir a rescisão antecipada se a pontuação não melhorar a curto prazo.|
|**primary_metric**| spearman_correlation | Métrica que pretende otimizar. O modelo mais adequado será escolhido com base nesta métrica.|
|**caracterização**| Automático | Utilizando **o automóvel,** a experiência pode pré-processar os dados de entrada (manuseando dados em falta, convertendo texto em numérico, etc.)|
|**verbosidade**| logging.INFO | Controla o nível de registo.|
|**n_cross_validations**|5|O número de divisões de validação cruzada para executar quando os dados de validação não são especificados.|

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

Utilize as definições de treino definidas como parâmetro `**kwargs` para um objeto `AutoMLConfig`. Além disso, especifique os seus dados de formação e o tipo de modelo, que é `regression` neste caso.

```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             X=x_train.values,
                             y=y_train.values.flatten(),
                             **automl_settings)
```

> [!NOTE]
> Os passos automatizados de pré-processamento de aprendizagem automática (normalização de recursos, manuseamento de dados em falta, conversão de texto para numérico, etc.) tornam-se parte do modelo subjacente. Ao utilizar o modelo para previsões, as mesmas etapas de pré-processamento aplicadas durante o treino são aplicadas automaticamente aos seus dados de entrada.

### <a name="train-the-automatic-regression-model"></a>Preparar o modelo de regressão automática

Crie um objeto de experiência no seu espaço de trabalho. Uma experiência funciona como um recipiente para as suas corridas individuais. Passe o `automl_config` definido oobjeto à experiência, e desloque a saída para `True` para ver o progresso durante a execução.

Depois de iniciar a experiência, a saída mostrou atualizações ao vivo à medida que a experiência corre. Para cada iteração, você vê o tipo de modelo, a duração do percurso e a precisão do treino. O campo `BEST` rastreia a melhor pontuação de treino de corrida com base no seu tipo métrico.

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

Explore os resultados do treino automático com um [widget Jupyter.](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) O widget permite-lhe ver um gráfico e tabela de todas as iterações individuais de execução, juntamente com métricas de precisão de treino e metadados. Além disso, pode filtrar métricas de precisão diferentes da sua métrica primária com o seletor de queda.

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![widget de widget de Jupyter detalhes](./media/tutorial-auto-train-models/automl-dash-output.png)
![plano de widget Jupyter](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="retrieve-the-best-model"></a>Obter o melhor modelo

Selecione o melhor modelo das suas iterações. A função `get_output` devolve o melhor funcionamento e o modelo equipado para a última invocação. Ao utilizar as sobrecargas no `get_output`, pode recuperar o melhor modelo de corrida e equipado para qualquer métrica ou iteração específica.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

### <a name="test-the-best-model-accuracy"></a>A maior precisão do modelo de teste

Use o melhor modelo para executar previsões no conjunto de dados de teste para prever tarifas de táxi. A função `predict` utiliza o melhor modelo e prevê os valores de y, custo de **viagem,** a partir do conjunto de dados `x_test`. Imprima os primeiros 10 valores de custo previstos a partir de `y_predict`.

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Calcular a `root mean squared error` dos resultados. Converta o quadro de dados `y_test` numa lista para comparar com os valores previstos. A função `mean_squared_error` pega em duas matrizes de valores e calcula o erro quadrado médio entre eles. Tomar a raiz quadrada do resultado dá um erro nas mesmas unidades que a variável y, **custo**. Indica aproximadamente até onde estão as previsões de tarifas de táxi das tarifas reais.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Executar o seguinte código para calcular erro de percentagem absoluta (MAPE) utilizando os conjuntos de dados completos `y_actual` e `y_predict`. Esta métrica calcula uma diferença absoluta entre cada valor previsto e real e resume todas as diferenças. Em seguida, expressa essa soma como uma por cento do total dos valores reais.

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


A partir das duas métricas de precisão de previsão, vê-se que o modelo é bastante bom na previsão de tarifas de táxi a partir das funcionalidades do conjunto de dados, tipicamente dentro de +- $4,00, e aproximadamente 15% de erro.

A processo de desenvolvimento do modelo de aprendizagem tradicional é altamente com muitos recursos e requer investimento de dados de conhecimento e a hora de domínio significativo para executar e comparar os resultados de dezenas de modelos. Usar machine learning automatizado é uma ótima maneira de testar rapidamente muitos modelos diferentes para o seu cenário.

## <a name="clean-up-resources"></a>Limpar recursos

Não complete esta secção se planeia executar outros tutoriais de Aprendizagem automática Azure.

### <a name="stop-the-compute-instance"></a>Pare a instância computacional

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Apagar tudo

Se não planeia usar os recursos que criou, apague-os, para não incorrer em quaisquer acusações.

1. No portal do Azure, selecione **Grupos de recursos** na extremidade esquerda.
1. Na lista, selecione o grupo de recursos que criou.
1. Selecione **Eliminar grupo de recursos**.
1. Insira o nome do grupo de recursos. Em seguida, selecione **Eliminar**.

Também pode manter o grupo de recursos, mas eliminar um único espaço de trabalho. Mostrar as propriedades do espaço de trabalho e selecionar **Apagar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial automatizado de aprendizagem automática, fez as seguintes tarefas:

> [!div class="checklist"]
> * Configurou um espaço de trabalho e preparou dados para uma experiência.
> * Treinado utilizando um modelo de regressão automatizada localmente com parâmetros personalizados.
> * Explorou e reviu os resultados do treino.

[Implemente o seu modelo](tutorial-deploy-models-with-aml.md) com a Azure Machine Learning.
