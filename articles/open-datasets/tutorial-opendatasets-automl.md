---
title: 'Tutorial: Enriquecer um modelo de aprendizado de máquina automatizado'
titleSuffix: Azure Open Datasets
description: Saiba como aproveitar a conveniência dos conjuntos de informações abertos do Azure junto com o poder do serviço de Azure Machine Learning para criar um modelo de regressão para prever preços de Tarifa de táxi de NYC.
services: open-datasets
ms.service: open-datasets
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 05/02/2019
ms.openlocfilehash: 963e4f7e9db638450a89dd4ae0091019fc58e2a4
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359439"
---
# <a name="tutorial-build-a-regression-model-with-automated-machine-learning-and-open-datasets"></a>Tutorial: Criar um modelo de regressão com o Machine Learning automatizado e os conjuntos de valores abertos

Neste tutorial, você aproveita a conveniência dos conjuntos de valores abertos do Azure junto com o poder do serviço de Azure Machine Learning para criar um modelo de regressão para prever preços de Tarifa de táxi de NYC. Baixe facilmente os dados de táxi, feriado e clima disponíveis publicamente e configure um experimento de aprendizado de máquina automatizado usando o serviço Azure Machine Learning. Esse processo aceita dados de treinamento e definições de configuração e itera automaticamente por meio de combinações de métodos diferentes de normalização/padronização de recursos, modelos e configurações de hiperparâmetro para chegar ao melhor modelo.

Neste tutorial, você aprende as seguintes tarefas:

- Configurar uma área de trabalho do serviço do Azure Machine Learning
- Configurar um ambiente local do Python
- Acessar, transformar e unir dados usando o Azure Open Data Sets
- Treinar um modelo de regressão de Machine Learning automatizado
- Calcular a precisão do modelo

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer os seguintes pré-requisitos.

* Um espaço de trabalho de serviço do Azure Machine Learning
* Um ambiente Python 3,6

### <a name="create-a-workspace"></a>Criar uma área de trabalho

Siga as [instruções](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace#portal) para criar um espaço de trabalho por meio do portal do Azure, se você ainda não tiver um. Após a criação, anote o nome do espaço de trabalho, o nome do grupo de recursos e a ID da assinatura.

### <a name="create-a-python-environment"></a>Criar um ambiente do Python

Este exemplo usa um ambiente Anaconda com notebooks Jupyter, mas você pode executar esse código em qualquer ambiente 3.6. x e com qualquer editor de texto ou IDE. Use as etapas a seguir para criar um novo ambiente de desenvolvimento.

1. Se você ainda não o tiver, [Baixe](https://www.anaconda.com/distribution/) e instale o Anaconda e escolha **versão do Python 3,7**.
1. Abra um prompt do Anaconda e crie um novo ambiente. Levará vários minutos para criar o ambiente enquanto os componentes e pacotes são baixados.
    ```
    conda create -n tutorialenv python=3.6.5
    ```
1. Ative o ambiente.
    ```
    conda activate tutorialenv
    ```
1. Habilitar kernels IPython específicos do ambiente.
    ```
    conda install notebook ipykernel
    ```
1. Crie o kernel.
    ```
    ipython kernel install --user
    ```
1. Instale os pacotes necessários para este tutorial. Esses pacotes são grandes e levarão de 5-10 minutos para serem instalados.
    ```
    pip install azureml-sdk[automl] azureml-opendatasets
    ```
1. Inicie um kernel do notebook do seu ambiente.
    ```
    jupyter notebook
    ```

Depois de concluir essas etapas, clone o [repositório de notebook Open DataSets](https://github.com/Azure/OpenDatasetsNotebooks) e abra o bloco de anotações **tutoriais/taxi-automl/01-tutorial-opendatasets-automl. ipynb** para executá-lo.

## <a name="download-and-prepare-data"></a>Baixar e preparar dados

Importe os pacotes necessários. O pacote Open DataSets contém uma classe que representa cada fonte de`NycTlcGreen` dados (por exemplo) para filtrar facilmente os parâmetros de data antes do download.


```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Comece criando um dataframe para manter os dados de táxi. Ao trabalhar em um ambiente não Spark, os conjuntos de dados abertos só permitem baixar um mês de data por vez com determinadas classes a serem `MemoryError` evitadas com grandes DataSets. Para baixar um ano de dados de táxi, busque iterativamente um mês por vez e antes de acrescentá-lo aos `green_taxi_df` registros 2000 de amostra aleatória de cada mês para evitar o inflamento do dataframe. Em seguida, visualize os dados.

>[!NOTE]
> Os conjuntos de dados abertos têm classes de espelhamento para trabalhar em ambientes Spark onde o tamanho dos dados e a memória não são uma preocupação.

```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2016", "%m/%d/%Y")
end = datetime.strptime("1/31/2016", "%m/%d/%Y")

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
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20 17:38:28</td>
      <td>2016-01-20 17:46:33</td>
      <td>1</td>
      <td>0,98</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73,921715</td>
      <td>40,766682</td>
      <td>-73,916908</td>
      <td>...</td>
      <td>2.0</td>
      <td>7.0</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0,3</td>
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
      <td>3, 8</td>
      <td>Nenhuma</td>
      <td>Nenhum</td>
      <td>-73,979973</td>
      <td>40,677071</td>
      <td>-73,934349</td>
      <td>...</td>
      <td>2.0</td>
      <td>11,5</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12,8</td>
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
      <td>Nenhum</td>
      <td>-73,863045</td>
      <td>40,882923</td>
      <td>-73,839836</td>
      <td>...</td>
      <td>2.0</td>
      <td>12.5</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04 17:50:03</td>
      <td>2016-01-04 18:03:43</td>
      <td>1</td>
      <td>2,87</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,977730</td>
      <td>40,684647</td>
      <td>-73,931259</td>
      <td>...</td>
      <td>1.0</td>
      <td>12.0</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13 08:48:20</td>
      <td>2016-01-13 08:52:16</td>
      <td>1</td>
      <td>0,50</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,942589</td>
      <td>40,841423</td>
      <td>-73,943672</td>
      <td>...</td>
      <td>2.0</td>
      <td>4.5</td>
      <td>0.0</td>
      <td>0,5</td>
      <td>0,3</td>
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
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,830894</td>
      <td>40,759434</td>
      <td>-73,842422</td>
      <td>...</td>
      <td>2.0</td>
      <td>10.5</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12,3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>646563</th>
      <td>2</td>
      <td>2016-01-14 00:45:30</td>
      <td>2016-01-14 00:54:16</td>
      <td>1</td>
      <td>1,93</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,927109</td>
      <td>40,762848</td>
      <td>-73,909302</td>
      <td>...</td>
      <td>1.0</td>
      <td>8.5</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>9,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>438204</th>
      <td>1</td>
      <td>2016-01-09 14:25:02</td>
      <td>2016-01-09 14:32:48</td>
      <td>2</td>
      <td>0,80</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73,881195</td>
      <td>40,741779</td>
      <td>-73,872086</td>
      <td>...</td>
      <td>2.0</td>
      <td>6.5</td>
      <td>0.0</td>
      <td>0,5</td>
      <td>0,3</td>
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
      <td>1, 4</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,954376</td>
      <td>40,805729</td>
      <td>-73,939117</td>
      <td>...</td>
      <td>1.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>1.5</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>11,3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>269105</th>
      <td>2</td>
      <td>2016-01-24 20:46:50</td>
      <td>2016-01-24 21:04:03</td>
      <td>6</td>
      <td>2,82</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73.845200</td>
      <td>40,722134</td>
      <td>-73,810638</td>
      <td>...</td>
      <td>1.0</td>
      <td>13.0</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>16,3</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
<p>10 linhas × 23 colunas</p>
</div>



Agora que os dados iniciais estão carregados, defina uma função para criar vários recursos baseados em tempo do campo de retirada DateTime. Isso criará novos campos para o número do mês, o dia do mês, o dia da semana e a hora do dia e permitirá que o modelo seja fatorado em sazonalidade com base no tempo. A função também adiciona um recurso estático para o código do país para unir dados de feriados. Use a `apply()` função no dataframe para aplicar iterativamente a `build_time_features()` função a cada linha nos dados de táxi.


```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour
    country_code = "US"

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day, country_code))


green_taxi_df[["month_num", "day_of_month", "day_of_week", "hour_of_day", "country_code"]
              ] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
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
      <th>TRIPTYPE</th>
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
      <td>0,98</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,921715</td>
      <td>40,766682</td>
      <td>-73,916908</td>
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
      <td>3, 8</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73,979973</td>
      <td>40,677071</td>
      <td>-73,934349</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12,8</td>
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
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,863045</td>
      <td>40,882923</td>
      <td>-73,839836</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13,8</td>
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
      <td>2,87</td>
      <td>Nenhum</td>
      <td>Nenhum</td>
      <td>-73,977730</td>
      <td>40,684647</td>
      <td>-73,931259</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13,8</td>
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
      <td>0,50</td>
      <td>Nenhuma</td>
      <td>Nenhum</td>
      <td>-73,942589</td>
      <td>40,841423</td>
      <td>-73,943672</td>
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
      <td>Nenhum</td>
      <td>-73,830894</td>
      <td>40,759434</td>
      <td>-73,842422</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12,3</td>
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
      <td>1,93</td>
      <td>Nenhuma</td>
      <td>Nenhuma</td>
      <td>-73,927109</td>
      <td>40,762848</td>
      <td>-73,909302</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>9,8</td>
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
      <td>0,80</td>
      <td>Nenhuma</td>
      <td>Nenhum</td>
      <td>-73,881195</td>
      <td>40,741779</td>
      <td>-73,872086</td>
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
      <td>1, 4</td>
      <td>Nenhum</td>
      <td>Nenhuma</td>
      <td>-73,954376</td>
      <td>40,805729</td>
      <td>-73,939117</td>
      <td>...</td>
      <td>1.5</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>11,3</td>
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
      <td>2,82</td>
      <td>Nenhuma</td>
      <td>Nenhum</td>
      <td>-73.845200</td>
      <td>40,722134</td>
      <td>-73,810638</td>
      <td>...</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>16,3</td>
      <td>1.0</td>
      <td>1</td>
      <td>24</td>
      <td>6</td>
      <td>20</td>
      <td>EUA</td>
    </tr>
  </tbody>
</table>
<p>10 linhas × 28 colunas</p>
</div>

Remova algumas das colunas que você não precisará para modelagem ou criação de recursos adicionais. Renomeie o campo hora para a hora de retirada e, além disso, `pandas.Series.dt.normalize`converta o tempo de meia-noite usando. Você faz isso em todos os recursos de tempo para que o componente DateTime possa ser usado posteriormente como uma chave ao unir conjuntos de datas em um nível diário de granularidade.

```python
columns_to_remove = ["lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                     ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df = green_taxi_df.rename(
    columns={"lpepPickupDatetime": "datetime"})
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
      <td>0,98</td>
      <td>-73,921715</td>
      <td>40,766682</td>
      <td>-73,916908</td>
      <td>40,761257</td>
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
      <td>3, 8</td>
      <td>-73,979973</td>
      <td>40,677071</td>
      <td>-73,934349</td>
      <td>40,671654</td>
      <td>12,8</td>
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
      <td>-73,863045</td>
      <td>40,882923</td>
      <td>-73,839836</td>
      <td>40,868336</td>
      <td>13,8</td>
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
      <td>2,87</td>
      <td>-73,977730</td>
      <td>40,684647</td>
      <td>-73,931259</td>
      <td>40,694248</td>
      <td>13,8</td>
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
      <td>0,50</td>
      <td>-73,942589</td>
      <td>40,841423</td>
      <td>-73,943672</td>
      <td>40,834396</td>
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

### <a name="enrich-with-holiday-data"></a>Enriquecer com dados de feriados

Agora que você tem dados de táxi baixados e quase preparados, adicione dados de feriados como recursos adicionais. Os recursos específicos do feriado auxiliarão na precisão do modelo, pois os principais feriados são ocasiões em que a demanda de táxi aumenta drasticamente e o fornecimento se torna limitado. O conjunto de datas de feriados é relativamente pequeno, portanto, busque o conjunto `PublicHolidays` completo usando o construtor de classe sem parâmetros para filtragem. Visualize os dados para verificar o formato.

```python
from azureml.opendatasets import PublicHolidays
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
      <td>Dia do ano novo</td>
      <td>Nenhum</td>
      <td>AL</td>
      <td>Dia do ano novo</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40689</th>
      <td>Argélia</td>
      <td>Dia do ano novo</td>
      <td>Nenhuma</td>
      <td>DZ</td>
      <td>Dia do ano novo</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40690</th>
      <td>Andorra</td>
      <td>Dia do ano novo</td>
      <td>Nenhum</td>
      <td>AD</td>
      <td>Dia do ano novo</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40691</th>
      <td>Angola</td>
      <td>Dia do ano novo</td>
      <td>Nenhum</td>
      <td>AO</td>
      <td>Dia do ano novo</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40692</th>
      <td>Argentina</td>
      <td>Dia do ano novo</td>
      <td>Nenhuma</td>
      <td>AR</td>
      <td>Dia do ano novo</td>
      <td>2008-01-01</td>
    </tr>
  </tbody>
</table>
</div>



`countryRegionCode` Renomeie `date` as colunas e para que correspondam aos respectivos nomes de campo dos dados de táxi e também Normalize o tempo para que ele possa ser usado como uma chave. Em seguida, junte os dados de feriado com os dados de táxi executando uma junção à esquerda usando a `merge()` função pandas. Isso preservará todos os registros `green_taxi_df`de, mas adicionará dados de feriado onde existirem para `datetime` o `country_code`correspondente e, que nesse caso é `"US"`sempre. Visualize os dados para verificar se eles foram mesclados corretamente.

```python
holidays_df = holidays_df.rename(
    columns={"countryRegionCode": "country_code", "date": "datetime"})
holidays_df["datetime"] = holidays_df["datetime"].dt.normalize()
holidays_df.pop("countryOrRegion")
holidays_df.pop("holidayName")

taxi_holidays_df = pd.merge(green_taxi_df, holidays_df, how="left", on=[
                            "datetime", "country_code"])
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
      <td>0,98</td>
      <td>-73,921715</td>
      <td>40,766682</td>
      <td>-73,916908</td>
      <td>40,761257</td>
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
      <td>3, 8</td>
      <td>-73,979973</td>
      <td>40,677071</td>
      <td>-73,934349</td>
      <td>40,671654</td>
      <td>12,8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>EUA</td>
      <td>True</td>
      <td>Dia do ano novo</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>2.44</td>
      <td>-73,863045</td>
      <td>40,882923</td>
      <td>-73,839836</td>
      <td>40,868336</td>
      <td>13,8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>EUA</td>
      <td>True</td>
      <td>Dia do ano novo</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2016-01-04</td>
      <td>1</td>
      <td>2,87</td>
      <td>-73,977730</td>
      <td>40,684647</td>
      <td>-73,931259</td>
      <td>40,694248</td>
      <td>13,8</td>
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
      <td>0,50</td>
      <td>-73,942589</td>
      <td>40,841423</td>
      <td>-73,943672</td>
      <td>40,834396</td>
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

### <a name="enrich-with-weather-data"></a>Enriquecer com dados meteorológicos

Agora você acrescenta dados meteorológicos de superfície de NOAA aos dados de táxi e feriado. Use uma abordagem semelhante para buscar os dados meteorológicos baixando um mês de cada vez iterativamente. Além disso, especifique `cols` o parâmetro com uma matriz de cadeias de caracteres para filtrar as colunas que você deseja baixar. Trata-se de um conjunto de dados muito grande que contém o clima da superfície de tempo de todo o mundo, portanto, antes de acrescentar cada mês, filtre os campos `query()` lat/long para perto de NYC usando a função no dataframe. Isso garantirá que `weather_df` o não fique muito grande.

```python
from azureml.opendatasets import NoaaIsdWeather

weather_df = pd.DataFrame([])
start = datetime.strptime("1/1/2016", "%m/%d/%Y")
end = datetime.strptime("1/31/2016", "%m/%d/%Y")

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
      <td>40,85</td>
      <td>0.0</td>
      <td>-74, 61</td>
      <td>2016-01-01 00:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754980</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.7</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74, 61</td>
      <td>2016-01-01 01:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754981</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.7</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74, 61</td>
      <td>2016-01-01 02:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754982</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.1</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74, 61</td>
      <td>2016-01-01 03:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754983</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.6</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74, 61</td>
      <td>2016-01-01 04:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754984</th>
      <td>94741</td>
      <td>24,0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40,85</td>
      <td>5.0</td>
      <td>-74, 61</td>
      <td>2016-01-01 04:59:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754985</th>
      <td>94741</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40,85</td>
      <td>NaN</td>
      <td>-74, 61</td>
      <td>2016-01-01 04:59:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754986</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.6</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74, 61</td>
      <td>2016-01-01 05:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754987</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74, 61</td>
      <td>2016-01-01 06:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754988</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74, 61</td>
      <td>2016-01-01 07:51:00</td>
      <td>725025</td>
    </tr>
  </tbody>
</table>
</div>

Novamente, `pandas.Series.dt.normalize` chame `datetime` no campo nos dados meteorológicos para que ele corresponda à chave de `taxi_holidays_df`tempo no. Exclua as colunas desnecessárias e filtre os registros em que a temperatura `NaN`está.

Em seguida, agrupe os dados meteorológicos para que você tenha valores meteorológicos agregados diariamente. Defina um dictname `aggregations` para definir como agregar cada campo em um nível diário. Para `snowDepth` `precipTime` e `temperature` levar`precipDepth` a média e obter o máximo diário. Use a `groupby()` função junto com as agregações para agrupar os dados. Visualize os dados para garantir que haja um registro por dia.

```python
weather_df["datetime"] = weather_df["datetime"].dt.normalize()
weather_df.pop("usaf")
weather_df.pop("wban")
weather_df.pop("longitude")
weather_df.pop("latitude")

# filter out NaN
weather_df = weather_df.query("temperature==temperature")

# group by datetime
aggregations = {"snowDepth": "mean", "precipTime": "max",
                "temperature": "mean", "precipDepth": "max"}
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
      <td>5,197345</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-02</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>2,567857</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-03</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>3,846429</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-04</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>0,123894</td>
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
      <td>-0,896396</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-07</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>3,180645</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-08</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>4,384091</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-09</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>6,710274</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>2016-01-10</th>
      <td>NaN</td>
      <td>24,0</td>
      <td>10,943655</td>
      <td>254,0</td>
    </tr>
  </tbody>
</table>
</div>

> [!NOTE]
> Os exemplos neste tutorial mesclam dados usando as funções do pandas e as agregações personalizadas, mas o SDK de conjuntos de dados abertos tem classes projetadas para mesclar e enriquecer facilmente os conjuntos. Consulte o [bloco de anotações](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) para obter exemplos de código desses padrões de design.

### <a name="cleanse-data"></a>Limpar dados

Mescle os dados de táxi e feriado que você preparou com os novos dados meteorológicos. Desta vez, você só precisa `datetime` da chave e, novamente, executar uma junção à esquerda dos dados. Execute a `describe()` função no novo dataframe para ver as estatísticas de Resumo de cada campo.

```python
taxi_holidays_weather_df = pd.merge(
    taxi_holidays_df, weather_df_grouped, how="left", on=["datetime"])
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
      <td>24000, 0</td>
      <td>24000, 0</td>
      <td>24000, 0</td>
      <td>24000, 0</td>
      <td>24000, 0</td>
      <td>24000, 0</td>
      <td>24000, 0</td>
      <td>24000, 0</td>
      <td>24000, 0</td>
      <td>24000, 0</td>
      <td>24000, 0</td>
      <td>24000, 0</td>
      <td>1671, 0</td>
      <td>24000, 0</td>
      <td>24000, 0</td>
      <td>24000, 0</td>
    </tr>
    <tr>
      <th>média</th>
      <td>1,786583</td>
      <td>6,576208</td>
      <td>1,582588</td>
      <td>20.505491</td>
      <td>84,936413</td>
      <td>-36,232825</td>
      <td>21,723144</td>
      <td>7,863018</td>
      <td>6,500000</td>
      <td>15,113708</td>
      <td>3,240250</td>
      <td>13,664125</td>
      <td>11,764141</td>
      <td>13,258875</td>
      <td>13,903524</td>
      <td>1056,644458</td>
    </tr>
    <tr>
      <th>padrão</th>
      <td>0,409728</td>
      <td>9, 86857</td>
      <td>2,418177</td>
      <td>108,847821</td>
      <td>70,678506</td>
      <td>37,650276</td>
      <td>19,104384</td>
      <td>10,648766</td>
      <td>3,452124</td>
      <td>8,485155</td>
      <td>1,956895</td>
      <td>6,650676</td>
      <td>15,651884</td>
      <td>10,339720</td>
      <td>9,474396</td>
      <td>2815.592754</td>
    </tr>
    <tr>
      <th>mín.</th>
      <td>1, 0</td>
      <td>-60, 0</td>
      <td>-1, 0</td>
      <td>-74,179482</td>
      <td>0.000000</td>
      <td>-74,190704</td>
      <td>0.000000</td>
      <td>-52.800000</td>
      <td>1, 0</td>
      <td>1, 0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>3, 0</td>
      <td>1, 0</td>
      <td>-13,379464</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2, 0</td>
      <td>1, 0</td>
      <td>0,330000</td>
      <td>-73,946680</td>
      <td>40,717712</td>
      <td>-73,945429</td>
      <td>1,770000</td>
      <td>1, 0</td>
      <td>3,750000</td>
      <td>8, 0</td>
      <td>2, 0</td>
      <td>9, 0</td>
      <td>3, 0</td>
      <td>1, 0</td>
      <td>6,620773</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2, 0</td>
      <td>4, 0</td>
      <td>0,830000</td>
      <td>1,500000</td>
      <td>40,814129</td>
      <td>0,500000</td>
      <td>21,495000</td>
      <td>2, 0</td>
      <td>6,500000</td>
      <td>15, 0</td>
      <td>3, 0</td>
      <td>15, 0</td>
      <td>4,428571</td>
      <td>6, 0</td>
      <td>13, 90753</td>
      <td>10, 0</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2, 0</td>
      <td>9, 0</td>
      <td>1,870000</td>
      <td>89, 0</td>
      <td>129, 0</td>
      <td>1, 0</td>
      <td>40,746146</td>
      <td>11,300000</td>
      <td>9,250000</td>
      <td>22, 0</td>
      <td>5, 0</td>
      <td>19, 0</td>
      <td>12,722222</td>
      <td>24, 0</td>
      <td>22,944737</td>
      <td>132, 0</td>
    </tr>
    <tr>
      <th>máx.</th>
      <td>2, 0</td>
      <td>460, 0</td>
      <td>51,950000</td>
      <td>265, 0</td>
      <td>265, 0</td>
      <td>6, 0</td>
      <td>58,600000</td>
      <td>498, 0</td>
      <td>12, 0</td>
      <td>30, 0</td>
      <td>6, 0</td>
      <td>23, 0</td>
      <td>67, 90909</td>
      <td>24, 0</td>
      <td>31,303665</td>
      <td>9999, 0</td>
    </tr>
  </tbody>
</table>
</div>

Nas estatísticas de resumo, você verá que há vários campos que têm exceções ou valores que reduzirão a precisão do modelo. Primeiro filtre os campos lat/long para que estejam dentro dos mesmos limites usados para filtrar dados meteorológicos. O `tripDistance` campo tem alguns dados inválidos, pois o valor mínimo é negativo. O `passengerCount` campo tem dados inválidos também, sendo que o valor máximo é 210 passageiros. Por fim, o `totalAmount` campo tem valores negativos, o que não faz sentido no contexto de nosso modelo.

Filtre essas anomalias usando funções de consulta e, em seguida, remova as últimas colunas desnecessárias para treinamento.

```python
final_df = taxi_holidays_weather_df.query(
    "pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query(
    "pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>0 and tripDistance<75")
final_df = final_df.query("passengerCount>0 and passengerCount<100")
final_df = final_df.query("totalAmount>0")

columns_to_remove_for_training = ["datetime", "pickupLongitude",
                                  "pickupLatitude", "dropoffLongitude", "dropoffLatitude", "country_code"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Chame `describe()` novamente nos dados para garantir que a limpeza funcione conforme o esperado. Agora você tem um conjunto preparado e limpo de táxi, feriado e dados meteorológicos a serem usados para o treinamento do modelo de aprendizado de máquina.

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
      <td>11765, 0</td>
      <td>11765, 0</td>
      <td>11765, 0</td>
      <td>11765, 0</td>
      <td>11765, 0</td>
      <td>11765, 0</td>
      <td>11765, 0</td>
      <td>11765, 0</td>
      <td>1490, 0</td>
      <td>11765, 0</td>
      <td>11765, 0</td>
      <td>11765, 0</td>
    </tr>
    <tr>
      <th>média</th>
      <td>1,786910</td>
      <td>1,343476</td>
      <td>2,848488</td>
      <td>14,689039</td>
      <td>3,499788</td>
      <td>14,948916</td>
      <td>3,234254</td>
      <td>13,647344</td>
      <td>12,508581</td>
      <td>11.855929</td>
      <td>10,301433</td>
      <td>208,432384</td>
    </tr>
    <tr>
      <th>padrão</th>
      <td>0,409508</td>
      <td>1, 1232</td>
      <td>2,895960</td>
      <td>10,289832</td>
      <td>1,707865</td>
      <td>8,442438</td>
      <td>1,958477</td>
      <td>6,640280</td>
      <td>16,203195</td>
      <td>10,125701</td>
      <td>8,553512</td>
      <td>1284,892832</td>
    </tr>
    <tr>
      <th>mín.</th>
      <td>1, 0</td>
      <td>1, 0</td>
      <td>0, 10000</td>
      <td>3,300000</td>
      <td>1, 0</td>
      <td>1, 0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>3, 0</td>
      <td>1, 0</td>
      <td>-13,379464</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2, 0</td>
      <td>1, 0</td>
      <td>1, 70000</td>
      <td>8,160000</td>
      <td>2, 0</td>
      <td>8, 0</td>
      <td>2, 0</td>
      <td>9, 0</td>
      <td>3, 0</td>
      <td>1, 0</td>
      <td>3,504580</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2, 0</td>
      <td>1, 0</td>
      <td>1,900000</td>
      <td>11,300000</td>
      <td>3, 0</td>
      <td>15, 0</td>
      <td>3, 0</td>
      <td>15, 0</td>
      <td>4,250000</td>
      <td>6, 0</td>
      <td>10,168182</td>
      <td>3, 0</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2, 0</td>
      <td>1, 0</td>
      <td>3,550000</td>
      <td>17.800000</td>
      <td>5, 0</td>
      <td>22, 0</td>
      <td>5, 0</td>
      <td>19, 0</td>
      <td>15,647059</td>
      <td>24, 0</td>
      <td>16,966923</td>
      <td>41, 0</td>
    </tr>
    <tr>
      <th>máx.</th>
      <td>2, 0</td>
      <td>6, 0</td>
      <td>51,950000</td>
      <td>150.300000</td>
      <td>6, 0</td>
      <td>30, 0</td>
      <td>6, 0</td>
      <td>23, 0</td>
      <td>67, 90909</td>
      <td>24, 0</td>
      <td>26,524107</td>
      <td>9999, 0</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="train-a-model"></a>Preparar um modelo

Agora você usa os dados preparados para treinar um modelo de aprendizado de máquina automatizado. Comece dividindo `final_df` os recursos (valores X) e rótulos (valor y), que para esse modelo é o custo de Tarifa de táxi.

```python
y_df = final_df.pop("totalAmount")
x_df = final_df
```

Agora você divide os dados em conjuntos de treinamento e teste usando a `train_test_split()` função `scikit-learn` na biblioteca. O `test_size` parâmetro determina a percentagem de dados para alocar ao teste. O `random_state` parâmetro define uma semente para o gerador de números aleatórios, para que suas divisões de treinamento de treino sejam determinísticas.


```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(
    x_df, y_df, test_size=0.2, random_state=222)
```

### <a name="load-workspace-and-configure-experiment"></a>Carregar espaço de trabalho e configurar o experimento

Carregue seu espaço de trabalho de serviço `get()` do Azure Machine Learning usando a função com suas informações de assinatura e de espaço de trabalho. Crie um experimento dentro de seu espaço de trabalho para armazenar e monitorar as execuções de modelo.


```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment

workspace = Workspace.get(subscription_id="<your-subscription-id>",
                          name="<your-workspace-name>", resource_group="<your-resource-group>")
experiment = Experiment(workspace, "opendatasets-ml")
```

Crie um objeto de configuração para o experimento usando `AutoMLConfig` a classe. Você anexa os dados de treinamento e especifica também as configurações e parâmetros que controlam o processo de treinamento. Os parâmetros têm as seguintes finalidades:

* `task`: o tipo de experimento a ser executado.
* `X`: recursos de treinamento.
* `y`: rótulos de treinamento.
* `iterations`: número de iterações a serem executadas. Cada iteração tenta combinações de métodos diferentes de normalização/padronização de recursos e modelos diferentes usando várias configurações de hiperparâmetro.
* `primary_metric`: métrica primária a ser otimizada durante o treinamento do modelo. O modelo de melhor ajuste será escolhido com base nessa métrica.
* `preprocess`: controla se o experimento pode pré-processar os dados de entrada (manipulando dados ausentes, convertendo texto em numérico, etc.)
* `n_cross_validations`: Número de divisões de validação cruzada a serem executadas quando os dados de validação não forem especificados.


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

Envie o experimento para treinamento. Depois de enviar o experimento, o processo é iterado por meio de diferentes algoritmos de aprendizado de máquina e configurações de hiperparâmetro, respeitando suas restrições definidas. Ele escolhe o modelo de melhor ajuste, otimizando a métrica de precisão definida. Passe o `automl_config` objeto para o experimento. Defina a saída como `True` para exibir o andamento durante o experimento.

Depois de enviar o experimento, você verá a saída ao vivo para o processo de treinamento. Para cada iteração, você vê o método de modelo de normalização/padronização de recursos, a duração da execução e a precisão do treinamento. O campo `BEST` controla a melhor pontuação de treinamento em execução com base em seu tipo de métrica.

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

### <a name="retrieve-the-fitted-model"></a>Recuperar o modelo ajustado

No final de todas as iterações de treinamento, o processo de aprendizado de máquina automatizado cria um algoritmo Ensemble de todas as execuções individuais, seja com bagging ou empilhamento. Recupere o Ensemble ajustado para a variável `fitted_model`e a melhor execução da variável. `best_run`

```python
best_run, fitted_model = training_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="test-model-accuracy"></a>Precisão do modelo de teste

Use o modelo ajustado Ensemble para executar previsões no conjunto de teste para prever as tarifas de táxi. A função `predict()` usa o modelo ajustado e prevê os valores de y, custo de Tarifa de táxi, para `X_test` o conjunto de espaço.


```python
y_predict = fitted_model.predict(X_test.values)
```

Calcule o erro de raiz quadrada média dos resultados. Use o `y_test` dataframe e converta-o em uma `y_actual` lista para comparar com os valores previstos. A função `mean_squared_error` usa duas matrizes de valores e calcula o erro de quadrado médio entre eles. Pegar a raiz quadrada do resultado resulta em um erro nas mesmas unidades que a variável y, custo. Ele indica aproximadamente até onde as previsões de Tarifa de táxi são provenientes das tarifas reais, enquanto aumentam muito os erros.


```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```




    4.178568987067901



Execute o código a seguir para calcular o erro de porcentagem absoluta média (mape) usando `y_actual` os `y_predict` conjuntos de valores completo e. Essa métrica calcula uma diferença absoluta entre cada valor previsto e real e soma todas as diferenças. Em seguida, ele expressa essa soma como uma porcentagem do total dos valores reais.


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

Considerando que você usou uma amostra razoavelmente pequena de dados em relação ao conjunto completo (n = 11748), a precisão do modelo é razoavelmente alta em 85%, com RMSE em aproximadamente +-$4 erro ao prever o preço de Tarifa de táxi. Como uma próxima etapa em potencial para melhorar a precisão, volte para a segunda célula deste bloco de anotações e aumente o tamanho da amostra de 2.000 registros por mês e execute o experimento inteiro novamente para treinar novamente o modelo com mais dados.

## <a name="clean-up-resources"></a>Limpar recursos

Se você não planeja usar os recursos que criou, exclua-os, para não incorrer em nenhum encargo.

1. No portal do Azure, selecione **Grupos de recursos** na extremidade esquerda.
1. Na lista, selecione o grupo de recursos que criou.
1. Selecione **Eliminar grupo de recursos**.
1. Insira o nome do grupo de recursos. Em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

* Consulte os [blocos de anotações](https://github.com/Azure/OpenDatasetsNotebooks) do Azure Open DataSets para obter mais exemplos de código.
* Siga o [instruções](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train) para obter mais informações sobre o aprendizado de máquina automatizado no serviço Azure Machine Learning.
