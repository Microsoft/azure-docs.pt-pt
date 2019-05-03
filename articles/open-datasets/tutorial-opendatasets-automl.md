---
title: 'Tutorial: Enriqueça um modelo de aprendizagem automática automatizada'
titleSuffix: Azure Open Datasets
description: Saiba como aproveitar a conveniência de conjuntos de dados aberto do Azure, juntamente com o poder do serviço Azure Machine Learning para criar um modelo de regressão para prever preços de Europeia NYC táxis.
services: open-datasets
ms.service: open-datasets
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 05/02/2019
ms.openlocfilehash: e753793b5da59d09a21991831046a43899c62ef2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026824"
---
# <a name="tutorial-build-a-regression-model-with-automated-machine-learning-and-open-datasets"></a>Tutorial: Criar um modelo de regressão com aprendizagem automática e conjuntos de dados aberto

Neste tutorial, aproveita a conveniência de conjuntos de dados aberto do Azure, juntamente com o poder do serviço Azure Machine Learning para criar um modelo de regressão para prever preços de Europeia NYC táxis. Facilmente transferir táxis publicamente disponíveis, feriado e sobreviver aos dados e configurar uma automatizada experimentação do machine learning com o serviço Azure Machine Learning. Este processo aceita as definições de configuração e dados de treinamento e automaticamente itera através de combinações de métodos de normalização/padronização de funcionalidades diferente, modelos e as definições de hiper-parâmetros para deparar-se com o melhor modelo.

Neste tutorial, vai aprender as seguintes tarefas:

- Configurar uma área de trabalho do serviço do Azure Machine Learning
- Configurar um ambiente local do Python
- Acesso, transformação e associar dados utilizando conjuntos de dados aberto do Azure
- Preparar uma modelo de regressão de aprendizagem automática
- Calcular a precisão do modelo

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer os seguintes pré-requisitos.

* Uma área de trabalho do serviço do Azure Machine Learning
* Um ambiente do Python 3.6

### <a name="create-a-workspace"></a>Criar uma área de trabalho

Siga os [instruções](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace#portal) para criar uma área de trabalho através do portal do Azure, se ainda não tiver uma. Após a criação, tome nota do seu nome de área de trabalho, o nome do grupo de recursos e o ID de subscrição.

### <a name="create-a-python-environment"></a>Criar um ambiente de Python

Este exemplo utiliza um ambiente de Anaconda com blocos de notas do Jupyter, mas pode executar esse código em qualquer ambiente 3.6.x e com qualquer editor de texto ou IDE. Utilize os seguintes passos para criar um novo ambiente de desenvolvimento.

1. Se ainda não o tiver, [baixe](https://www.anaconda.com/distribution/) instalar Anaconda e escolha **versão Python 3.7**.
1. Abra uma linha de Anaconda e criar um novo ambiente. Irá demorar alguns minutos a criar o ambiente, enquanto os componentes e os pacotes são transferidos.
    ```
    conda create -n tutorialenv python=3.6.5
    ```
1. Ative o ambiente.
    ```
    conda activate tutorialenv
    ```
1. Ative kernels de IPython específicos do ambiente.
    ```
    conda install notebook ipykernel
    ```
1. Crie o kernel.
    ```
    ipython kernel install --user
    ```
1. Instale os pacotes que precisa para este tutorial. Esses pacotes são grandes e irão demorar 5 a 10 minutos para instalar.
    ```
    pip install azureml-sdk[automl] azureml-contrib-opendatasets
    ```
1. Inicie um kernel de bloco de notas do seu ambiente.
    ```
    jupyter notebook
    ```

Depois de concluir estes passos, clone o [repositório de bloco de notas de conjuntos de dados abra](https://github.com/Azure/OpenDatasetsNotebooks) e abra o **tutorials/taxi-automl/01-tutorial-opendatasets-automl.ipynb** bloco de notas para executá-lo.

## <a name="download-and-prepare-data"></a>Transferir e preparar dados

Importe os pacotes necessários. O pacote de conjuntos de dados aberto contém uma classe que representa cada origem de dados (`NycTlcGreen` por exemplo) para filtrar facilmente os parâmetros de data antes de transferir.


```python
from azureml.contrib.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Comece por criar um pacote de dados para armazenar os dados de táxis. Ao trabalhar num ambiente de não-Spark, conjuntos de dados aberto só permite baixar um mês de dados cada vez com determinadas classes para evitar `MemoryError` com grandes conjuntos de dados. Para transferir um ano de dados de táxis, iterativamente buscar um mês, ao mesmo tempo e antes de anexando-o para `green_taxi_df` amostras aleatórias dos registos de 2000 por mês, para evitar bloating o pacote de dados. Em seguida, visualize os dados.

>[!NOTE]
> Conjuntos de dados aberto tem classes para trabalhar em ambientes de Spark em que o tamanho de dados e de memória não são uma preocupação de espelhamento.

```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2016","%m/%d/%Y")
end = datetime.strptime("1/31/2016","%m/%d/%Y")

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
      <th>paymentType</th>
      <th>fareAmount</th>
      <th>extra</th>
      <th>mtaTax</th>
      <th>improvementSurcharge</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>tripType</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20 17:38:28</td>
      <td>2016-01-20 17:46:33</td>
      <td>1</td>
      <td>0.98</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.921715</td>
      <td>40.766682</td>
      <td>-73.916908</td>
      <td>...</td>
      <td>2.0</td>
      <td>7.0</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>8.8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01 21:53:28</td>
      <td>2016-01-02 00:00:00</td>
      <td>1</td>
      <td>3.08</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.979973</td>
      <td>40.677071</td>
      <td>-73.934349</td>
      <td>...</td>
      <td>2.0</td>
      <td>11.5</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12.8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01 00:50:23</td>
      <td>2016-01-01 01:05:37</td>
      <td>1</td>
      <td>2.44</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.863045</td>
      <td>40.882923</td>
      <td>-73.839836</td>
      <td>...</td>
      <td>2.0</td>
      <td>12.5</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13.8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04 17:50:03</td>
      <td>2016-01-04 18:03:43</td>
      <td>1</td>
      <td>2.87</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.977730</td>
      <td>40.684647</td>
      <td>-73.931259</td>
      <td>...</td>
      <td>1.0</td>
      <td>12.0</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13.8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13 08:48:20</td>
      <td>2016-01-13 08:52:16</td>
      <td>1</td>
      <td>0.50</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.942589</td>
      <td>40.841423</td>
      <td>-73.943672</td>
      <td>...</td>
      <td>2.0</td>
      <td>4.5</td>
      <td>0.0</td>
      <td>0,5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>5.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1049431</th>
      <td>2</td>
      <td>2016-01-29 17:16:18</td>
      <td>2016-01-29 17:27:52</td>
      <td>1</td>
      <td>2.25</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.830894</td>
      <td>40.759434</td>
      <td>-73.842422</td>
      <td>...</td>
      <td>2.0</td>
      <td>10.5</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>646563</th>
      <td>2</td>
      <td>2016-01-14 00:45:30</td>
      <td>2016-01-14 00:54:16</td>
      <td>1</td>
      <td>1.93</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.927109</td>
      <td>40.762848</td>
      <td>-73.909302</td>
      <td>...</td>
      <td>1.0</td>
      <td>8.5</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>9.8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>438204</th>
      <td>1</td>
      <td>2016-01-09 14:25:02</td>
      <td>2016-01-09 14:32:48</td>
      <td>2</td>
      <td>0.80</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.881195</td>
      <td>40.741779</td>
      <td>-73.872086</td>
      <td>...</td>
      <td>2.0</td>
      <td>6.5</td>
      <td>0.0</td>
      <td>0,5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>7.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>303784</th>
      <td>2</td>
      <td>2016-01-25 18:13:47</td>
      <td>2016-01-25 18:23:50</td>
      <td>1</td>
      <td>1.04</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.954376</td>
      <td>40.805729</td>
      <td>-73.939117</td>
      <td>...</td>
      <td>1.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0.3</td>
      <td>1.5</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>11.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>269105</th>
      <td>2</td>
      <td>2016-01-24 20:46:50</td>
      <td>2016-01-24 21:04:03</td>
      <td>6</td>
      <td>2.82</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.845200</td>
      <td>40.722134</td>
      <td>-73.810638</td>
      <td>...</td>
      <td>1.0</td>
      <td>13.0</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0.3</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>16.3</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
<p>10 linhas de colunas de 23 ×</p>
</div>



Agora que os dados iniciais são carregados, defina uma função para criar vários recursos baseados no tempo do campo datetime de recolha. Isto irá criar novos campos para o número do mês, dia do mês, dia da semana e hora do dia e permitirá que o modelo de sazonalidade baseados no tempo. A função também adiciona um recurso estático para o código de país associar dados de feriado. Utilize o `apply()` função sobre o pacote de dados para aplicar iterativamente a `build_time_features()` função para cada linha dos dados de táxis.


```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour
    country_code = "US"

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day, country_code))

green_taxi_df[["month_num", "day_of_month","day_of_week", "hour_of_day", "country_code"]] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
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
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>tripType</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>country_code</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20 17:38:28</td>
      <td>2016-01-20 17:46:33</td>
      <td>1</td>
      <td>0.98</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.921715</td>
      <td>40.766682</td>
      <td>-73.916908</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>8.8</td>
      <td>1.0</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>EUA</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01 21:53:28</td>
      <td>2016-01-02 00:00:00</td>
      <td>1</td>
      <td>3.08</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.979973</td>
      <td>40.677071</td>
      <td>-73.934349</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12.8</td>
      <td>1.0</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>EUA</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01 00:50:23</td>
      <td>2016-01-01 01:05:37</td>
      <td>1</td>
      <td>2.44</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.863045</td>
      <td>40.882923</td>
      <td>-73.839836</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13.8</td>
      <td>1.0</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>EUA</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04 17:50:03</td>
      <td>2016-01-04 18:03:43</td>
      <td>1</td>
      <td>2.87</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.977730</td>
      <td>40.684647</td>
      <td>-73.931259</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13.8</td>
      <td>1.0</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>17</td>
      <td>EUA</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13 08:48:20</td>
      <td>2016-01-13 08:52:16</td>
      <td>1</td>
      <td>0.50</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.942589</td>
      <td>40.841423</td>
      <td>-73.943672</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>5.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>EUA</td>
    </tr>
    <tr>
      <th>1049431</th>
      <td>2</td>
      <td>2016-01-29 17:16:18</td>
      <td>2016-01-29 17:27:52</td>
      <td>1</td>
      <td>2.25</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.830894</td>
      <td>40.759434</td>
      <td>-73.842422</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>29</td>
      <td>4</td>
      <td>17</td>
      <td>EUA</td>
    </tr>
    <tr>
      <th>646563</th>
      <td>2</td>
      <td>2016-01-14 00:45:30</td>
      <td>2016-01-14 00:54:16</td>
      <td>1</td>
      <td>1.93</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.927109</td>
      <td>40.762848</td>
      <td>-73.909302</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>9.8</td>
      <td>1.0</td>
      <td>1</td>
      <td>14</td>
      <td>3</td>
      <td>0</td>
      <td>EUA</td>
    </tr>
    <tr>
      <th>438204</th>
      <td>1</td>
      <td>2016-01-09 14:25:02</td>
      <td>2016-01-09 14:32:48</td>
      <td>2</td>
      <td>0.80</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.881195</td>
      <td>40.741779</td>
      <td>-73.872086</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>7.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>9</td>
      <td>5</td>
      <td>14</td>
      <td>EUA</td>
    </tr>
    <tr>
      <th>303784</th>
      <td>2</td>
      <td>2016-01-25 18:13:47</td>
      <td>2016-01-25 18:23:50</td>
      <td>1</td>
      <td>1.04</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.954376</td>
      <td>40.805729</td>
      <td>-73.939117</td>
      <td>...</td>
      <td>1.5</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>11.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>25</td>
      <td>0</td>
      <td>18</td>
      <td>EUA</td>
    </tr>
    <tr>
      <th>269105</th>
      <td>2</td>
      <td>2016-01-24 20:46:50</td>
      <td>2016-01-24 21:04:03</td>
      <td>6</td>
      <td>2.82</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.845200</td>
      <td>40.722134</td>
      <td>-73.810638</td>
      <td>...</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>16.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>24</td>
      <td>6</td>
      <td>20</td>
      <td>EUA</td>
    </tr>
  </tbody>
</table>
<p>10 linhas de colunas de 28 ×</p>
</div>

Remova algumas das colunas que não precisará de modelagem ou criação de funcionalidades adicionais. Mudar o nome do campo de tempo durante a recolha e, além disso, converter a hora para a utilização de meia-noite `pandas.Series.dt.normalize`. Fazê-lo ao tempo de todos os recursos para que o componente de datetime pode ser posterior usados como chave ao associar os conjuntos de dados em conjunto, um nível de granularidade de diário.

```python
columns_to_remove = ["lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                    ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df = green_taxi_df.rename(columns={"lpepPickupDatetime": "datetime"})
green_taxi_df["datetime"] = green_taxi_df["datetime"].dt.normalize()
green_taxi_df.head(5)
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
      <th>datetime</th>
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
      <th>country_code</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20</td>
      <td>1</td>
      <td>0.98</td>
      <td>-73.921715</td>
      <td>40.766682</td>
      <td>-73.916908</td>
      <td>40.761257</td>
      <td>8.8</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>EUA</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>3.08</td>
      <td>-73.979973</td>
      <td>40.677071</td>
      <td>-73.934349</td>
      <td>40.671654</td>
      <td>12.8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>EUA</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>2.44</td>
      <td>-73.863045</td>
      <td>40.882923</td>
      <td>-73.839836</td>
      <td>40.868336</td>
      <td>13.8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>EUA</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04</td>
      <td>1</td>
      <td>2.87</td>
      <td>-73.977730</td>
      <td>40.684647</td>
      <td>-73.931259</td>
      <td>40.694248</td>
      <td>13.8</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>17</td>
      <td>EUA</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13</td>
      <td>1</td>
      <td>0.50</td>
      <td>-73.942589</td>
      <td>40.841423</td>
      <td>-73.943672</td>
      <td>40.834396</td>
      <td>5.3</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>EUA</td>
    </tr>
  </tbody>
</table>
</div>

### <a name="enrich-with-holiday-data"></a>Enriqueça com dados de feriado

Agora que tem dados de táxis transferido e aproximadamente preparado, adicione dados de feriado como funcionalidades adicionais. Funcionalidades específicas do feriado irão ajudar a precisão do modelo, assim como nos principais feriados vezes onde táxis a procura aumenta drasticamente e o fornecimento se torna limitado. O conjunto de dados do feriado é relativamente pequeno, então, obter o conjunto completo utilizando a `PublicHolidays` construtor da classe sem parâmetros para filtragem. Pré-visualize os dados para verificar o formato.

```python
from azureml.contrib.opendatasets import PublicHolidays
# call default constructor to download full dataset
holidays_df = PublicHolidays().to_pandas_dataframe()
holidays_df.head(5)
```

    ActivityStarted, to_pandas_dataframe
    Looking for parquet files...
    Reading them into Pandas dataframe...
    Reading Processed/part-00000-tid-1353805596865908763-9ee4e95b-0d55-4292-addd-a0e19d7c32cb-3559-c000.snappy.parquet under container holidaydatacontainer
    Done.
    ActivityCompleted: Activity=to_pandas_dataframe, HowEnded=Success, Duration=1799.89 [ms]

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
      <th>countryOrRegion</th>
      <th>holidayName</th>
      <th>isPaidTimeOff</th>
      <th>countryRegionCode</th>
      <th>normalizeHolidayName</th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>40688</th>
      <td>Albânia</td>
      <td>Dia de ano novo</td>
      <td>Nenhuma</td>
      <td>AL</td>
      <td>Dia de ano novo</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40689</th>
      <td>Argélia</td>
      <td>Dia de ano novo</td>
      <td>Nenhuma</td>
      <td>DZ</td>
      <td>Dia de ano novo</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40690</th>
      <td>Andorra</td>
      <td>Dia de ano novo</td>
      <td>Nenhuma</td>
      <td>AD</td>
      <td>Dia de ano novo</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40691</th>
      <td>Angola</td>
      <td>Dia de ano novo</td>
      <td>Nenhuma</td>
      <td>AO</td>
      <td>Dia de ano novo</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40692</th>
      <td>Argentina</td>
      <td>Dia de ano novo</td>
      <td>Nenhuma</td>
      <td>AR</td>
      <td>Dia de ano novo</td>
      <td>2008-01-01</td>
    </tr>
  </tbody>
</table>
</div>



Mudar o nome da `countryRegionCode` e `date` colunas para corresponder aos nomes de campo de dados de táxis e normalizar também o tempo para que possa ser utilizada como uma chave. Em seguida, Junte-se os dados de datas festivas com os dados de táxis efetuando uma associação de esquerda com os Pandas `merge()` função. Isso preservará todos os registos da `green_taxi_df`, mas adicionar nos dados de feriado, onde ele existe para o controle correspondente `datetime` e `country_code`, que neste caso é sempre `"US"`. Pré-visualize os dados para verificar que eles foram mesclados corretamente.

```python
holidays_df = holidays_df.rename(columns={"countryRegionCode": "country_code", "date": "datetime"})
holidays_df["datetime"] = holidays_df["datetime"].dt.normalize()
holidays_df.pop("countryOrRegion")
holidays_df.pop("holidayName")

taxi_holidays_df = pd.merge(green_taxi_df, holidays_df, how="left", on=["datetime", "country_code"])
taxi_holidays_df.head(5)
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
      <th>datetime</th>
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
      <th>country_code</th>
      <th>isPaidTimeOff</th>
      <th>normalizeHolidayName</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2016-01-20</td>
      <td>1</td>
      <td>0.98</td>
      <td>-73.921715</td>
      <td>40.766682</td>
      <td>-73.916908</td>
      <td>40.761257</td>
      <td>8.8</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>EUA</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>3.08</td>
      <td>-73.979973</td>
      <td>40.677071</td>
      <td>-73.934349</td>
      <td>40.671654</td>
      <td>12.8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>EUA</td>
      <td>Verdadeiro</td>
      <td>Dia de ano novo</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>2.44</td>
      <td>-73.863045</td>
      <td>40.882923</td>
      <td>-73.839836</td>
      <td>40.868336</td>
      <td>13.8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>EUA</td>
      <td>Verdadeiro</td>
      <td>Dia de ano novo</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2016-01-04</td>
      <td>1</td>
      <td>2.87</td>
      <td>-73.977730</td>
      <td>40.684647</td>
      <td>-73.931259</td>
      <td>40.694248</td>
      <td>13.8</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>17</td>
      <td>EUA</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>2016-01-13</td>
      <td>1</td>
      <td>0.50</td>
      <td>-73.942589</td>
      <td>40.841423</td>
      <td>-73.943672</td>
      <td>40.834396</td>
      <td>5.3</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>EUA</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

### <a name="enrich-with-weather-data"></a>Enriqueça com dados de Meteorologia

Agora acrescentar dados de superfície de meteorologia da NOAA para os dados de táxis e boas festas. Utilize uma abordagem semelhante para obter os dados de Meteorologia baixando um mês num momento iterativamente. Além disso, especificar o `cols` parâmetro com uma matriz de cadeias de caracteres para filtrar as colunas que pretende transferir. Este é um conjunto de dados muito grande, que contém dados meteorológicos de superfície da todo o mundo, por isso, antes de acrescentar todos os meses, filtrar os campos de lat e longos para uma quase NYC usando o `query()` função sobre o pacote de dados. Isto irá garantir o `weather_df` não ficar demasiado grande.

```python
from azureml.contrib.opendatasets import NoaaIsdWeather

weather_df = pd.DataFrame([])
start = datetime.strptime("1/1/2016","%m/%d/%Y")
end = datetime.strptime("1/31/2016","%m/%d/%Y")

for sample_month in range(12):
    tmp_df = NoaaIsdWeather(cols=["temperature", "precipTime", "precipDepth", "snowDepth"], start_date=start + relativedelta(months=sample_month), end_date=end + relativedelta(months=sample_month))\
        .to_pandas_dataframe()
    print("--weather downloaded--")

    # filter out coordinates not in NYC to conserve memory
    tmp_df = tmp_df.query("latitude>=40.53 and latitude<=40.88")
    tmp_df = tmp_df.query("longitude>=-74.09 and longitude<=-73.72")
    print("--filtered coordinates--")
    weather_df = weather_df.append(tmp_df)

weather_df.head(10)
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
      <th>wban</th>
      <th>precipTime</th>
      <th>snowDepth</th>
      <th>temperatura</th>
      <th>Latitude</th>
      <th>precipDepth</th>
      <th>Longitude</th>
      <th>datetime</th>
      <th>usaf</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1754979</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>7.2</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 00:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754980</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.7</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 01:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754981</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.7</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 02:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754982</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.1</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 03:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754983</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.6</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 04:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754984</th>
      <td>94741</td>
      <td>24.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40.85</td>
      <td>5.0</td>
      <td>-74.061</td>
      <td>2016-01-01 04:59:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754985</th>
      <td>94741</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40.85</td>
      <td>NaN</td>
      <td>-74.061</td>
      <td>2016-01-01 04:59:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754986</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.6</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 05:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754987</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 06:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754988</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 07:51:00</td>
      <td>725025</td>
    </tr>
  </tbody>
</table>
</div>

Chamar novamente `pandas.Series.dt.normalize` sobre o `datetime` campo nos dados de Meteorologia para que ela corresponda à chave de tempo no `taxi_holidays_df`. Eliminar as colunas desnecessárias e filtrar os registos em que a temperatura for `NaN`.

Em seguida agrupe os dados de Meteorologia para que tem diariamente Meteorologia valores agregados. Definir um dict `aggregations` para definir como agregar cada campo a um nível de diário. Para `snowDepth` e `temperature` tirar a média e para `precipTime` e `precipDepth` tirar diária máximo. Utilize o `groupby()` função juntamente com as agregações para agrupar os dados. Pré-visualize os dados para garantir que existe um registo por dia.

```python
weather_df["datetime"] = weather_df["datetime"].dt.normalize()
weather_df.pop("usaf")
weather_df.pop("wban")
weather_df.pop("longitude")
weather_df.pop("latitude")

# filter out NaN
weather_df = weather_df.query("temperature==temperature")

# group by datetime
aggregations = {"snowDepth": "mean", "precipTime": "max", "temperature": "mean", "precipDepth": "max"}
weather_df_grouped = weather_df.groupby("datetime").agg(aggregations)
weather_df_grouped.head(10)
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
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>temperatura</th>
      <th>precipDepth</th>
    </tr>
    <tr>
      <th>datetime</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2016-01-01</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>5.197345</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-02</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>2.567857</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-03</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>3.846429</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-04</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>0.123894</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-05</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>-7.206250</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-06</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>-0.896396</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-07</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>3.180645</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-08</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>4.384091</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-09</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>6.710274</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>2016-01-10</th>
      <td>NaN</td>
      <td>24.0</td>
      <td>10.943655</td>
      <td>254.0</td>
    </tr>
  </tbody>
</table>
</div>

> [!NOTE]
> Os exemplos neste tutorial unir dados através de funções de Pandas e agregações personalizadas, mas o SDK de conjuntos de dados aberto tem classes projetadas para intercalar e enriquecer os conjuntos de dados facilmente. Consulte a [bloco de notas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) para obter exemplos de código desses padrões de design.

### <a name="cleanse-data"></a>Limpar dados

Intercale os dados táxis e boas festas que preparou com os novos dados de Meteorologia. Desta vez, só precisa o `datetime` da chave e executar novamente uma associação à esquerda dos dados. Execute o `describe()` função sobre o novo pacote de dados para ver estatísticas de resumo para cada campo.

```python
taxi_holidays_weather_df = pd.merge(taxi_holidays_df, weather_df_grouped, how="left", on=["datetime"])
taxi_holidays_weather_df.describe()
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
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>temperatura</th>
      <th>precipDepth</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>1671.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
    </tr>
    <tr>
      <th>média</th>
      <td>1.786583</td>
      <td>6.576208</td>
      <td>1.582588</td>
      <td>20.505491</td>
      <td>84.936413</td>
      <td>-36.232825</td>
      <td>21.723144</td>
      <td>7.863018</td>
      <td>6.500000</td>
      <td>15.113708</td>
      <td>3.240250</td>
      <td>13.664125</td>
      <td>11.764141</td>
      <td>13.258875</td>
      <td>13.903524</td>
      <td>1056.644458</td>
    </tr>
    <tr>
      <th>std</th>
      <td>0.409728</td>
      <td>9.086857</td>
      <td>2.418177</td>
      <td>108.847821</td>
      <td>70.678506</td>
      <td>37.650276</td>
      <td>19.104384</td>
      <td>10.648766</td>
      <td>3.452124</td>
      <td>8.485155</td>
      <td>1.956895</td>
      <td>6.650676</td>
      <td>15.651884</td>
      <td>10.339720</td>
      <td>9.474396</td>
      <td>2815.592754</td>
    </tr>
    <tr>
      <th>min.</th>
      <td>1.000000</td>
      <td>-60.000000</td>
      <td>-1.000000</td>
      <td>-74.179482</td>
      <td>0.000000</td>
      <td>-74.190704</td>
      <td>0.000000</td>
      <td>-52.800000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>3.000000</td>
      <td>1.000000</td>
      <td>-13.379464</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2.000000</td>
      <td>1.000000</td>
      <td>0.330000</td>
      <td>-73.946680</td>
      <td>40.717712</td>
      <td>-73.945429</td>
      <td>1.770000</td>
      <td>1.000000</td>
      <td>3.750000</td>
      <td>8.000000</td>
      <td>2.000000</td>
      <td>9.000000</td>
      <td>3.000000</td>
      <td>1.000000</td>
      <td>6.620773</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2.000000</td>
      <td>4.000000</td>
      <td>0.830000</td>
      <td>1.500000</td>
      <td>40.814129</td>
      <td>0.500000</td>
      <td>21.495000</td>
      <td>2.000000</td>
      <td>6.500000</td>
      <td>15.000000</td>
      <td>3.000000</td>
      <td>15.000000</td>
      <td>4.428571</td>
      <td>6.000000</td>
      <td>13.090753</td>
      <td>10.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2.000000</td>
      <td>9.000000</td>
      <td>1.870000</td>
      <td>89.000000</td>
      <td>129.000000</td>
      <td>1.000000</td>
      <td>40.746146</td>
      <td>11.300000</td>
      <td>9.250000</td>
      <td>22.000000</td>
      <td>5.000000</td>
      <td>19.000000</td>
      <td>12.722222</td>
      <td>24.000000</td>
      <td>22.944737</td>
      <td>132.000000</td>
    </tr>
    <tr>
      <th>máx.</th>
      <td>2.000000</td>
      <td>460.000000</td>
      <td>51.950000</td>
      <td>265.000000</td>
      <td>265.000000</td>
      <td>6.000000</td>
      <td>58.600000</td>
      <td>498.000000</td>
      <td>12.000000</td>
      <td>30.000000</td>
      <td>6.000000</td>
      <td>23.000000</td>
      <td>67.090909</td>
      <td>24.000000</td>
      <td>31.303665</td>
      <td>9999.000000</td>
    </tr>
  </tbody>
</table>
</div>

As estatísticas de resumo, ver que existem vários campos que têm valores atípicos ou valores que irão reduzir a precisão do modelo. Em primeiro lugar, filtre os campos de lat e longos dentro dos limites mesmo que é utilizado para filtrar dados meteorológicos. O `tripDistance` campo tem alguns dados ruins, porque o valor mínimo é negativo. O `passengerCount` campo tem dados incorretos, com o valor máximo que está a ser 210 passageiros. Por último, o `totalAmount` campo tem valores negativos, que não fazem sentido no contexto de nosso modelo.

Filtrar essas anomalias com as funções de consulta e, em seguida, remova algumas colunas desnecessárias para treinamento.

```python
final_df = taxi_holidays_weather_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>0 and tripDistance<75")
final_df = final_df.query("passengerCount>0 and passengerCount<100")
final_df = final_df.query("totalAmount>0")

columns_to_remove_for_training = ["datetime", "pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude", "country_code"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Chamar `describe()` novamente nos dados para garantir a limpeza funcionou como esperado. Agora tem um conjunto preparado e limpo de táxis feriado e dados meteorológicos a utilizar para a preparação de modelos de aprendizagem automática.

```python
final_df.describe()
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
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>temperatura</th>
      <th>precipDepth</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>1490.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
    </tr>
    <tr>
      <th>média</th>
      <td>1.786910</td>
      <td>1.343476</td>
      <td>2.848488</td>
      <td>14.689039</td>
      <td>3.499788</td>
      <td>14.948916</td>
      <td>3.234254</td>
      <td>13.647344</td>
      <td>12.508581</td>
      <td>11.855929</td>
      <td>10.301433</td>
      <td>208.432384</td>
    </tr>
    <tr>
      <th>std</th>
      <td>0.409508</td>
      <td>1.001232</td>
      <td>2.895960</td>
      <td>10.289832</td>
      <td>1.707865</td>
      <td>8.442438</td>
      <td>1.958477</td>
      <td>6.640280</td>
      <td>16.203195</td>
      <td>10.125701</td>
      <td>8.553512</td>
      <td>1284.892832</td>
    </tr>
    <tr>
      <th>min.</th>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>0.010000</td>
      <td>3.300000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>3.000000</td>
      <td>1.000000</td>
      <td>-13.379464</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2.000000</td>
      <td>1.000000</td>
      <td>1.070000</td>
      <td>8.160000</td>
      <td>2.000000</td>
      <td>8.000000</td>
      <td>2.000000</td>
      <td>9.000000</td>
      <td>3.000000</td>
      <td>1.000000</td>
      <td>3.504580</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2.000000</td>
      <td>1.000000</td>
      <td>1.900000</td>
      <td>11.300000</td>
      <td>3.000000</td>
      <td>15.000000</td>
      <td>3.000000</td>
      <td>15.000000</td>
      <td>4.250000</td>
      <td>6.000000</td>
      <td>10.168182</td>
      <td>3.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2.000000</td>
      <td>1.000000</td>
      <td>3.550000</td>
      <td>17.800000</td>
      <td>5.000000</td>
      <td>22.000000</td>
      <td>5.000000</td>
      <td>19.000000</td>
      <td>15.647059</td>
      <td>24.000000</td>
      <td>16.966923</td>
      <td>41.000000</td>
    </tr>
    <tr>
      <th>máx.</th>
      <td>2.000000</td>
      <td>6.000000</td>
      <td>51.950000</td>
      <td>150.300000</td>
      <td>6.000000</td>
      <td>30.000000</td>
      <td>6.000000</td>
      <td>23.000000</td>
      <td>67.090909</td>
      <td>24.000000</td>
      <td>26.524107</td>
      <td>9999.000000</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="train-a-model"></a>Preparar um modelo

Agora, utilizar os dados preparados para formar uma modelo de aprendizagem automatizada. Iniciar, dividindo `final_df` nos recursos de (X valores) e as etiquetas (valor de y), que, para este modelo, é o custo de Europeia táxis.

```python
y_df = final_df.pop("totalAmount")
x_df = final_df
```

Agora, dividir os dados em treinamento e teste conjuntos com o `train_test_split()` funcionar o `scikit-learn` biblioteca. O `test_size` parâmetro determina a percentagem de dados para alocar ao teste. O `random_state` parâmetro define uma semente para o gerador de números aleatórios, de modo a que as divisões de treinar-testar são determinísticos.


```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=222)
```

### <a name="load-workspace-and-configure-experiment"></a>Área de trabalho de carga e configurar experimentação

Carregar sua através de área de trabalho de serviço do Azure Machine Learning a `get()` função com as suas informações de subscrição e a área de trabalho. Crie uma experimentação na sua área de trabalho para armazenar e monitorizar as execuções de modelo.


```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment

workspace = Workspace.get(subscription_id="<your-subscription-id>", name="<your-workspace-name>", resource_group="<your-resource-group>")
experiment = Experiment(workspace, "opendatasets-ml")
```

Criar um objeto de configuração para a experimentação, utilizando o `AutoMLConfig` classe. Anexar os seus dados de treinamento e, além disso, especificar as definições e parâmetros que controlam o processo de treinamento. Os parâmetros de ter os seguintes fins:

* `task`: o tipo de experimentação para execução.
* `X`: recursos de treinamento.
* `y`: etiquetas de treinamento.
* `iterations`: número de iterações para executar. Cada iteração tenta combinações de métodos de normalização/padronização de funcionalidades diferente e modelos diferentes com várias definições de hiper-parâmetros.
* `primary_metric`: métrica primária para otimizar durante a preparação de modelos. Melhor ajuste modelo será escolhido com base nesta métrica.
* `preprocess`: controla se a experimentação pode pré-processar os dados de entrada (processamento de dados em falta, converter o texto em numérico, etc.)
* `n_cross_validations`: Número de divisões de validação cruzada a efetuar quando os dados de validação não for especificados.


```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task="regression",
                             X=X_train.values,
                             y=y_train.values.flatten(),
                             iterations=20,
                             primary_metric="spearman_correlation",
                             preprocess=True,
                             n_cross_validations=5
                            )
```

### <a name="submit-experiment"></a>Submeter a experimentação

Submeta a experimentação para treinamento. Depois de submeter a experimentação, o processo faz a iteração por meio de diferente de machine learning algoritmos e as definições de hiper-parâmetros, respeitando as restrições definidas pelo. Ele escolhe o modelo mais adequado ao otimizar a métrica de precisão definidos. Passar o `automl_config` objeto para a experimentação. Definir a saída `True` para ver o progresso durante a experimentação.

Depois de submeter a experimentação, pode ver a resultado em direto para o processo de treinamento. Para cada iteração, verá o método de normalização/padronização de tipo e o recurso de modelo, a duração de execução e a precisão de treinamento. O campo `BEST` controla o melhor com score de treinamento com base em seu tipo de métrica.

```python
training_run = experiment.submit(automl_config, show_output=True)
```

    Running on local machine
    Parent Run ID: AutoML_5c35f2a7-e479-4e7f-a131-ed4cb51e29d1
    Current status: DatasetEvaluation. Gathering dataset statistics.
    Current status: FeaturesGeneration. Generating features for the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: ModelSelection. Beginning model selection.

    ****************************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ****************************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler RandomForest                      0:00:07       0.9081    0.9081
             1   StandardScalerWrapper DecisionTree             0:00:05       0.9121    0.9121
             2   StandardScalerWrapper LightGBM                 0:00:04       0.9318    0.9318
             3   StandardScalerWrapper LightGBM                 0:00:07       0.9286    0.9318
             4   MaxAbsScaler LightGBM                          0:00:05       0.9246    0.9318
             5   MaxAbsScaler LightGBM                          0:00:05       0.9199    0.9318
             6   MaxAbsScaler RandomForest                      0:00:07       0.9327    0.9327
             7   StandardScalerWrapper ElasticNet               0:00:04       0.9371    0.9371
             8   MaxAbsScaler LightGBM                          0:00:05       0.9327    0.9371
             9   MaxAbsScaler SGD                               0:00:04       0.9077    0.9371
            10   MaxAbsScaler LightGBM                          0:00:04       0.9340    0.9371
            11   StandardScalerWrapper LightGBM                 0:00:04       0.8301    0.9371
            12   MaxAbsScaler DecisionTree                      0:00:05       0.9214    0.9371
            13   StandardScalerWrapper DecisionTree             0:00:04       0.9201    0.9371
            14   MaxAbsScaler DecisionTree                      0:00:05       0.9179    0.9371
            15   MaxAbsScaler ExtremeRandomTrees                0:00:05       0.9052    0.9371
            16   StandardScalerWrapper DecisionTree             0:00:04       0.9282    0.9371
            17   StandardScalerWrapper ElasticNet               0:00:04       0.9319    0.9371
            18   VotingEnsemble                                 0:00:16       0.9380    0.9380
            19   StackEnsemble                                  0:00:17       0.9376    0.9380

### <a name="retrieve-the-fitted-model"></a>Obter o modelo ajustado

No final de todas as iterações de treinamento, a processo de aprendizagem automática cria um algoritmo de ensemble a partir de todas as execuções individuais, seja com bagging ou empilhamento. Obter o ensemble ajustada na variável `fitted_model`e a melhor execução individual na variável `best_run`.

```python
best_run, fitted_model = training_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="test-model-accuracy"></a>Testar a precisão do modelo

Utilize o modelo de ensemble ajustada para executar previsões no conjunto de dados de teste para prever fares táxis. A função `predict()` usa o modelo de ajustada e prevê os valores de y, táxis Europeia custo, para o `X_test` conjunto de dados.


```python
y_predict = fitted_model.predict(X_test.values)
```

Calcule a média erros ao quadrado a raiz dos resultados. Utilize o `y_test` pacote de dados e convertê-lo a uma lista `y_actual` a comparar com os valores previstos. A função `mean_squared_error` usa duas matrizes de valores e calcula o média erro ao quadrado entre eles. Levando a raiz quadrada da resultado oferece um erro na mesma unidade como a variável y, o custo. Aproximadamente indica a distância as previsões de Europeia táxis são dos fares reais, ao mesmo tempo bastante weighting erros grandes.


```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```




    4.178568987067901



Execute o seguinte código para calcular erro percentagem absoluto mean (MAPE) utilizando o completo `y_actual` e `y_predict` conjuntos de dados. Esta métrica calcula uma diferença absoluta entre cada valor previsto e real e somas todas as diferenças. Em seguida, expressa que a soma como percentagem do total dos valores reais.


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
    0.14923619644924357

    Model Accuracy:
    0.8507638035507564

Considerando que usamos uma bastante pequena amostra dos dados em relação ao conjunto de dados completo (n = 11748), precisão do modelo é bastante alto em 85%, com RMSE em torno de erro + - US $4.00 na previsão do preço de Europeia táxis. Como passo seguinte potencial para melhorar a precisão, voltar para a segunda célula deste bloco de notas e aumentar o tamanho da amostra de 2000 registos por mês e executar toda a experimentar novamente para voltar a preparar o modelo com mais dados.

## <a name="clean-up-resources"></a>Limpar recursos

Se não planeja usar os recursos que criou, eliminá-los, para que não existem custos.

1. No portal do Azure, selecione **Grupos de recursos** na extremidade esquerda.
1. Na lista, selecione o grupo de recursos que criou.
1. Selecione **Eliminar grupo de recursos**.
1. Introduza o nome do grupo de recursos. Em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

* Ver os conjuntos de dados aberto do Azure [blocos de notas](https://github.com/Azure/OpenDatasetsNotebooks) para obter mais exemplos de código.
* Siga os [procedimentos](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train) para obter mais informações sobre automatizada aprendizagem automática no serviço Azure Machine Learning.
