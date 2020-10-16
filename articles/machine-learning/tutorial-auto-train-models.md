---
title: 'Tutorial de regressão: ML automatizado'
titleSuffix: Azure Machine Learning
description: Neste tutorial, aprende-se a gerar um modelo de aprendizagem automática utilizando machine learning automatizado. O Azure Machine Learning pode realizar a pré-processamento de dados, a seleção de algoritmos e a seleção de hiperparímetros de forma automatizada para si.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.date: 08/14/2020
ms.custom: devx-track-python
ms.openlocfilehash: cf6616dcc3935946ad4a7213263bb20281d25354
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90896787"
---
# <a name="tutorial-use-automated-machine-learning-to-predict-taxi-fares"></a>Tutorial: Use machine learning automatizado para prever tarifas de táxi


Neste tutorial, você usa machine learning automatizado em Azure Machine Learning para criar um modelo de regressão para prever os preços das tarifas de táxi de NYC. Este processo aceita definições de dados de formação e configuração, e itera automaticamente através de combinações de diferentes métodos de normalização/normalização de funcionalidades, modelos e configurações de hiperparímetro para chegar ao melhor modelo.

![Diagrama de fluxo](./media/tutorial-auto-train-models/flow2.png)

Neste tutorial aprende-se as seguintes tarefas:

> [!div class="checklist"]
> * Descarregue, transforme e limpe dados usando datasets Azure Open
> * Treine um modelo automatizado de regressão de aprendizagem automática
> * Calcular a precisão do modelo

Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga](https://aka.ms/AMLFree) do Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

* Complete o tutorial de [configuração](tutorial-1st-experiment-sdk-setup.md) se ainda não tiver um espaço de trabalho de Aprendizagem automática Azure ou uma máquina virtual de caderno.
* Depois de completar o tutorial de configuração, abra os *tutoriais/regression-automl-nyc-taxi-data/regression-automated-ml.ipynb* usando o mesmo servidor de caderno.

Este tutorial também está disponível no [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) se desejar executá-lo no seu próprio [ambiente local.](how-to-configure-environment.md#local) Corra `pip install azureml-sdk[automl] azureml-opendatasets azureml-widgets` para obter os pacotes necessários.

## <a name="download-and-prepare-data"></a>Baixar e preparar dados

Importe as embalagens necessárias. O pacote Open Datasets contém uma classe que representa cada fonte de dados `NycTlcGreen` (por exemplo) para filtrar facilmente os parâmetros da data antes de descarregar.

```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Comece por criar um dataframe para reter os dados do táxi. Ao trabalhar num ambiente não-Spark, o Open Datasets apenas permite o descarregamento de um mês de dados de cada vez com determinadas classes para evitar `MemoryError` com grandes conjuntos de dados.

Para descarregar dados de táxi, buscar iterativamente um mês de cada vez, e antes de apingi-lo para `green_taxi_df` amostrar aleatoriamente 2.000 registos de cada mês para evitar inchar o dataframe. Em seguida, pré-visualizar os dados.


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

|vendorID| lpepPickupDatetime|  lpepDropoffDatetime|    passageiroso| tripDistance|   puLocationId|   doLocationId|   pickupLongitude|    pickupAtitude| dropoffLongitude    |...|   tipo de pagamento |fareAmount |extra| mtaTax| melhoriaSSurcharge|   tipAmount|  portagensAmont|    ehailFee|   totalAmonto|    tripType|
|----|----|----|----|----|----|---|--|---|---|---|----|----|----|--|---|----|-----|----|----|----|----|---|
|131969|2|2015-01-11 05:34:44|2015-01-11 05:45:03|3|4.84|Nenhum|Nenhum|-73.88|40.84|-73.94|...|2|15.00|0.50|0.50|0.3|0.00|0.00|nan|16.30|1,00
|1129817|2|2015-01-20 16:26:29|2015-01-20 16:30:26|1|0.69|Nenhum|Nenhum|-73.96|40.81|-73.96|...|2|4.50|1,00|0.50|0.3|0.00|0.00|nan|6.30|1,00
|1278620|2|2015-01-01 05:58:10|2015-01-01 06:00:55|1|0,45|Nenhum|Nenhum|-73.92|40.76|-73.91|...|2|4.00|0.00|0.50|0.3|0.00|0.00|nan|4,80|1,00
|348430|2|2015-01-17 02:20:50|2015-01-17 02:41:38|1|0.00|Nenhum|Nenhum|-73.81|40.70|-73.82|...|2|12.50|0.50|0.50|0.3|0.00|0.00|nan|13.80|1,00
1269627|1|2015-01-01 05:04:10|2015-01-01 05:06:23|1|0.50|Nenhum|Nenhum|-73.92|40.76|-73.92|...|2|4.00|0.50|0.50|0|0.00|0.00|nan|5.00|1,00
|811755|1|2015-01-04 19:57:51|2015-01-04 20:05:45|2|1.10|Nenhum|Nenhum|-73.96|40.72|-73.95|...|2|6.50|0.50|0.50|0.3|0.00|0.00|nan|7.80|1,00
|737281|1|2015-01-03 12:27:31|2015-01-03 12:33:52|1|0.90|Nenhum|Nenhum|-73.88|40.76|-73.87|...|2|6.00|0.00|0.50|0.3|0.00|0.00|nan|6.80|1,00
|113951|1|2015-01-09 23:25:51|2015-01-09 23:39:52|1|3.30|Nenhum|Nenhum|-73.96|40.72|-73.91|...|2|12.50|0.50|0.50|0.3|0.00|0.00|nan|13.80|1,00
|150436|2|2015-01-11 17:15:14|2015-01-11 17:22:57|1|1.19|Nenhum|Nenhum|-73.94|40.71|-73.95|...|1|7.00|0.00|0.50|0.3|1,75|0.00|nan|9.55|1,00
|432136|2|2015-01-22 23:16:33 2015-01-22 23:20:13 1 0.65|Nenhum|Nenhum|-73.94|40.71|-73.94|...|2|5.00|0.50|0.50|0.3|0.00|0.00|nan|6.30|1,00

Agora que os dados iniciais são carregados, defina uma função para criar várias funcionalidades baseadas no tempo de recolha a partir do campo de data de recolha. Isto criará novos campos para o número do mês, dia do mês, dia da semana e hora do dia, e permitirá que o modelo considere a sazonalidade baseada no tempo. Utilize a `apply()` função no quadro de dados para aplicar iterativamente `build_time_features()` a função a cada linha nos dados do táxi.

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

|vendorID| lpepPickupDatetime|  lpepDropoffDatetime|    passageiroso| tripDistance|   puLocationId|   doLocationId|   pickupLongitude|    pickupAtitude| dropoffLongitude    |...|   tipo de pagamento|fareAmount  |extra| mtaTax| melhoriaSSurcharge|   tipAmount|  portagensAmont|    ehailFee|   totalAmonto|tripType|month_num|day_of_month|day_of_week|hour_of_day
|----|----|----|----|----|----|---|--|---|---|---|----|----|----|--|---|----|-----|----|----|----|----|---|----|----|----
|131969|2|2015-01-11 05:34:44|2015-01-11 05:45:03|3|4.84|Nenhum|Nenhum|-73.88|40.84|-73.94|...|2|15.00|0.50|0.50|0.3|0.00|0.00|nan|16.30|1,00|1|11|6|5
|1129817|2|2015-01-20 16:26:29|2015-01-20 16:30:26|1|0.69|Nenhum|Nenhum|-73.96|40.81|-73.96|...|2|4.50|1,00|0.50|0.3|0.00|0.00|nan|6.30|1,00|1|20|1|16
|1278620|2|2015-01-01 05:58:10|2015-01-01 06:00:55|1|0,45|Nenhum|Nenhum|-73.92|40.76|-73.91|...|2|4.00|0.00|0.50|0.3|0.00|0.00|nan|4,80|1,00|1|1|3|5
|348430|2|2015-01-17 02:20:50|2015-01-17 02:41:38|1|0.00|Nenhum|Nenhum|-73.81|40.70|-73.82|...|2|12.50|0.50|0.50|0.3|0.00|0.00|nan|13.80|1,00|1|17|5|2
1269627|1|2015-01-01 05:04:10|2015-01-01 05:06:23|1|0.50|Nenhum|Nenhum|-73.92|40.76|-73.92|...|2|4.00|0.50|0.50|0|0.00|0.00|nan|5.00|1,00|1|1|3|5
|811755|1|2015-01-04 19:57:51|2015-01-04 20:05:45|2|1.10|Nenhum|Nenhum|-73.96|40.72|-73.95|...|2|6.50|0.50|0.50|0.3|0.00|0.00|nan|7.80|1,00|1|4|6|19
|737281|1|2015-01-03 12:27:31|2015-01-03 12:33:52|1|0.90|Nenhum|Nenhum|-73.88|40.76|-73.87|...|2|6.00|0.00|0.50|0.3|0.00|0.00|nan|6.80|1,00|1|3|5|12
|113951|1|2015-01-09 23:25:51|2015-01-09 23:39:52|1|3.30|Nenhum|Nenhum|-73.96|40.72|-73.91|...|2|12.50|0.50|0.50|0.3|0.00|0.00|nan|13.80|1,00|1|9|4|23
|150436|2|2015-01-11 17:15:14|2015-01-11 17:22:57|1|1.19|Nenhum|Nenhum|-73.94|40.71|-73.95|...|1|7.00|0.00|0.50|0.3|1,75|0.00|nan|9.55|1,00|1|11|6|17
|432136|2|2015-01-22 23:16:33 2015-01-22 23:20:13 1 0.65|Nenhum|Nenhum|-73.94|40.71|-73.94|...|2|5.00|0.50|0.50|0.3|0.00|0.00|nan|6.30|1,00|1|22|3|23

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

Executar a `describe()` função no novo dataframe para ver estatísticas sumárias para cada campo.

```python
green_taxi_df.describe()
```

|vendorID|passageiroso|tripDistance|pickupLongitude|pickupAtitude|dropoffLongitude|dropoffAtitude|  totalAmonto|month_num day_of_month|day_of_week|hour_of_day
|----|----|---|---|----|---|---|---|---|---|---|---
|count|48000.00|48000.00|48000.00|48000.00|48000.00|48000.00|48000.00|48000.00|48000.00|48000.00|48000.00|48000.00
|mean|1.78|1.37|2.87|-73.83|40.69|-73.84|40.70|14.75|6.50|15.13|3.27|13.52
|std|0.41|1.04|2,93|2.76|1,52|2.61|1.44|12.08|3.45|8.45|1.95|6.83
|min|1,00|0.00|0.00|-74.66|0.00|-74.66|0.00|-300.00|1,00|1,00|0.00|0.00
|25%|2.00|1,00|1.06|-73.96|40.70|-73.97|40.70|7.80|3,75|8.00|2.00|9.00
|50%|2.00|1,00|1.90|-73.94|40.75|-73.94|40.75|11.30|6.50|15.00|3,00|15.00
|75%|2.00|1,00|3,60|-73.92|40.80|-73.91|40.79|17.80|9.25|22.00|5.00|19.00
|max|2.00|9.00|97.57|0.00|41.93|0.00|41.94|450.00|12.00|30.00|6.00|23.00


A partir das estatísticas resumidas, vê-se que existem vários campos que têm valores ou valores que reduzirão a precisão do modelo. Primeiro filtrar os campos lat/long para estar dentro dos limites da área de Manhattan. Isto irá filtrar viagens de táxi mais longas ou que sejam mais distantes em relação à sua relação com outras características.

Além disso, filtrar o `tripDistance` campo para ser superior a zero, mas a menos de 31 milhas (a distância haversine entre os dois pares lat/long). Isto elimina viagens longas e fora do comum que têm custos inconsistentes de viagem.

Por último, o `totalAmount` campo tem valores negativos para as tarifas de táxi, que não fazem sentido no contexto do nosso modelo, e o `passengerCount` campo tem dados negativos com os valores mínimos a serem nulos.

Filtra estas anomalias utilizando funções de consulta e, em seguida, remova as últimas colunas desnecessárias para o treino.


```python
final_df = green_taxi_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>=0.25 and tripDistance<31")
final_df = final_df.query("passengerCount>0 and totalAmount>0")

columns_to_remove_for_training = ["pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Ligue `describe()` novamente sobre os dados para garantir que a limpeza funcionou como esperado. Você agora tem um conjunto preparado e limpo de dados de táxi, férias e tempo para usar para o treino de modelo de machine learning.

```python
final_df.describe()
```

## <a name="configure-workspace"></a>Configurar a área de trabalho

Crie um objeto de área de trabalho a partir da área de trabalho existente. Um [Espaço de Trabalho](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) é uma classe que aceita a sua subscrição Azure e informações de recursos. Também cria um recurso em nuvem para monitorizar e rastrear as suas execuções de modelos. `Workspace.from_config()` lê o ficheiro **config.js** e carrega os dados de autenticação num objeto chamado `ws` . `ws` é utilizado em todo o restante código neste tutorial.

```python
from azureml.core.workspace import Workspace
ws = Workspace.from_config()
```

## <a name="split-the-data-into-train-and-test-sets"></a>Divida os dados em conjuntos de comboios e testes

Divida os dados em conjuntos de treino e teste utilizando a `train_test_split` função na `scikit-learn` biblioteca. Esta função segrega os dados no conjunto de dados x **(características)** para a formação de modelos e o conjunto de dados y **(valores a prever)** para testes.

O `test_size` parâmetro determina a percentagem de dados a atribuir aos testes. O `random_state` parâmetro define uma semente para o gerador aleatório, de modo que as suas fraturas de teste de comboio são determinísticas.

```python
from sklearn.model_selection import train_test_split

x_train, x_test = train_test_split(final_df, test_size=0.2, random_state=223)
```

O objetivo deste passo é ter pontos de dados para testar o modelo acabado que não tenha sido usado para treinar o modelo, de forma a medir a verdadeira precisão.

Por outras palavras, um modelo bem treinado deve ser capaz de fazer previsões com precisão a partir de dados que ainda não viu. Tem agora dados preparados para o treino automático de um modelo de aprendizagem automática.

## <a name="automatically-train-a-model"></a>Treine automaticamente um modelo

Para treinar automaticamente um modelo, tome os seguintes passos:
1. Defina as definições para a execução da experiência. Anexe os seus dados de treino à configuração e modifique as definições que controlam o processo de treino.
1. Submeta a experiência para afinação de modelos. Após a submissão da experiência, o processo itera através de diferentes algoritmos de aprendizagem automática e configurações de hiperparímetro, aderindo aos seus constrangimentos definidos. Escolhe o modelo mais adequado otimizando uma métrica de precisão.

### <a name="define-training-settings"></a>Definir definições de formação

Defina o parâmetro de experiência e as definições do modelo para o treino. Ver a lista completa de [definições](how-to-configure-auto-train.md). Submeter a experiência com estas definições predefinidos levará aproximadamente 5-20 min, mas se quiser um tempo de funcionamento mais curto, reduza o `experiment_timeout_hours` parâmetro.

|Propriedade| Valor neste tutorial |Descrição|
|----|----|---|
|**iteration_timeout_minutes**|2|Limite de tempo em minutos para cada iteração. Reduza este valor para diminuir o tempo de execução total.|
|**experiment_timeout_hours**|0.3|O tempo máximo em horas que todas as iterações combinadas podem demorar antes do fim da experiência.|
|**enable_early_stopping**|Verdadeiro|Bandeira para permitir a rescisão antecipada se a pontuação não melhorar a curto prazo.|
|**primary_metric**| spearman_correlation | Métrica que pretende otimizar. O modelo mais adequado será escolhido com base nesta métrica.|
|**adisco**| Automático | Utilizando **o auto,** a experiência pode pré-processar os dados de entrada (manusear dados em falta, converter texto em numérico, etc.)|
|**verbosidade**| logging.INFO | Controla o nível de registo.|
|**n_cross_validations**|5|Número de divisões de validação cruzada para efetuar quando os dados de validação não são especificados.|

```python
import logging

automl_settings = {
    "iteration_timeout_minutes": 2,
    "experiment_timeout_hours": 0.3,
    "enable_early_stopping": True,
    "primary_metric": 'spearman_correlation',
    "featurization": 'auto',
    "verbosity": logging.INFO,
    "n_cross_validations": 5
}
```

Utilize as definições de treino definidas como `**kwargs` parâmetro para um `AutoMLConfig` objeto. Além disso, especifique os seus dados de treino e o tipo de modelo, que é `regression` neste caso.

```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             training_data=x_train,
                             label_column_name="totalAmount",
                             **automl_settings)
```

> [!NOTE]
> As etapas de pré-processamento de aprendizagem automática automatizadas (normalização de recurso, manuseamento de dados em falta, conversão de texto em numérico, etc.) tornam-se parte do modelo subjacente. Ao utilizar o modelo para previsões, as mesmas etapas de pré-processamento aplicadas durante o treino são aplicadas automaticamente aos seus dados de entrada.

### <a name="train-the-automatic-regression-model"></a>Treine o modelo de regressão automática

Crie um objeto de experiência no seu espaço de trabalho. Uma experiência funciona como um recipiente para as suas corridas individuais. Passe o objeto definido `automl_config` para a experiência, e desaje a saída `True` para ver o progresso durante a execução.

Depois de iniciar a experiência, a saída mostrou atualizações ao vivo à medida que a experiência decorre. Para cada iteração, vê-se o tipo de modelo, a duração da execução e a precisão do treino. O campo `BEST` acompanha a melhor pontuação de treino de corrida com base no seu tipo métrico.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, "taxi-experiment")
local_run = experiment.submit(automl_config, show_output=True)
```

```output
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
```

## <a name="explore-the-results"></a>Explorar os resultados

Explore os resultados do treino automático com um [widget Jupyter.](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true) O widget permite-lhe ver um gráfico e tabela de todas as iterações individuais de execução, juntamente com métricas de precisão de treino e metadados. Além disso, pode filtrar em diferentes métricas de precisão do que a sua métrica primária com o seletor de dropdown.

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Jupyter widget detalhes ](./media/tutorial-auto-train-models/automl-dash-output.png)
 ![ jupyter widget plot](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="retrieve-the-best-model"></a>Obter o melhor modelo

Selecione o melhor modelo das suas iterações. A `get_output` função devolve a melhor execução e o modelo equipado para a última invocação de ajuste. Ao utilizar as `get_output` sobrecargas, pode recuperar o melhor modelo de funcionaamento e equipado para qualquer métrica registada ou uma iteração específica.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

### <a name="test-the-best-model-accuracy"></a>Teste a melhor precisão do modelo

Use o melhor modelo para executar previsões no conjunto de dados de teste para prever tarifas de táxi. A função `predict` utiliza o melhor modelo e prevê os valores do y, custo da **viagem,** a partir do conjunto de `x_test` dados. Imprima os primeiros 10 valores de custo previstos de `y_predict` .

```python
y_test = x_test.pop("totalAmount")

y_predict = fitted_model.predict(x_test)
print(y_predict[:10])
```

Calcular os `root mean squared error` resultados. Converta o `y_test` quadro de dados numa lista para comparar com os valores previstos. A função `mean_squared_error` requer dois conjuntos de valores e calcula o erro quadrado médio entre eles. Tomar a raiz quadrada do resultado dá um erro nas mesmas unidades que a variável y, **custo**. Indica aproximadamente a distância entre as previsões das tarifas de táxi das tarifas reais.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Executar o seguinte código para calcular o erro de percentagem absoluta média (MAPE) utilizando os `y_actual` `y_predict` conjuntos completos e de dados. Esta métrica calcula uma diferença absoluta entre cada valor previsto e real e resume todas as diferenças. Em seguida, expressa essa soma como uma por cento do total dos valores reais.

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

```output
Model MAPE:
0.14353867606052823

Model Accuracy:
0.8564613239394718
```


A partir das duas métricas de precisão de previsão, você vê que o modelo é bastante bom na previsão de tarifas de táxi a partir das características do conjunto de dados, tipicamente dentro de +- $4,00, e aproximadamente 15% de erro.

O processo tradicional de desenvolvimento de modelos de aprendizagem automática é altamente intensivo em recursos, e requer conhecimentos significativos de domínio e investimento no tempo para executar e comparar os resultados de dezenas de modelos. Usar machine learning automatizado é uma ótima maneira de testar rapidamente muitos modelos diferentes para o seu cenário.

## <a name="clean-up-resources"></a>Limpar os recursos

Não preencha esta secção se planeia executar outros tutoriais de Aprendizagem automática Azure.

### <a name="stop-the-compute-instance"></a>Pare a instância computacional

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Apagar tudo

Se não planeia usar os recursos que criou, elimine-os, para não incorrer em quaisquer encargos.

1. No portal do Azure, selecione **Grupos de recursos** na extremidade esquerda.
1. Na lista, selecione o grupo de recursos que criou.
1. Selecione **Eliminar grupo de recursos**.
1. Insira o nome do grupo de recursos. Em seguida, selecione **Eliminar**.

Também pode manter o grupo de recursos, mas eliminar um único espaço de trabalho. Mostrar as propriedades do espaço de trabalho e selecionar **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial automatizado de aprendizagem automática, fez as seguintes tarefas:

> [!div class="checklist"]
> * Configurar um espaço de trabalho e preparar dados para uma experiência.
> * Treinado usando um modelo de regressão automatizado localmente com parâmetros personalizados.
> * Explorou e reviu os resultados da formação.

[Implemente o seu modelo](tutorial-deploy-models-with-aml.md) com Azure Machine Learning.
