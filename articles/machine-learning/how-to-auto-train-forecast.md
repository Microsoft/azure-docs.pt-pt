---
title: Treina automaticamente um modelo de previsão da série de tempo
titleSuffix: Azure Machine Learning
description: Aprenda a usar a Azure Machine Learning para treinar um modelo de regressão de previsão de séries temporizadas utilizando machine learning automatizado.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.date: 08/20/2020
ms.openlocfilehash: f423ae957d11248b16a180e22647d6566157b7be
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782843"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Treina automaticamente um modelo de previsão da série de tempo
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a configurar e treinar um modelo de regressão de previsão de séries temporizadas utilizando machine learning automatizado, AutoML, no [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py). 

Para uma experiência de baixo código, consulte o [Tutorial: Previsão de procura com machine learning automatizado](tutorial-automated-ml-forecast.md) para um exemplo de previsão de séries temporizadas usando machine learning automatizado no estúdio [Azure Machine Learning](https://ml.azure.com/).

Ao contrário dos métodos clássicos das séries de tempo, em ML automatizado, os valores das séries de tempo anteriores são "apostados" para se tornarem dimensões adicionais para o regressor juntamente com outros preditores. Esta abordagem incorpora múltiplas variáveis contextuais e a sua relação entre si durante o treino. Uma vez que vários fatores podem influenciar uma previsão, este método alinha-se bem com cenários de previsão do mundo real. Por exemplo, ao prever vendas, interações de tendências históricas, taxa de câmbio e preço impulsionam conjuntamente o resultado das vendas. 

Os seguintes exemplos mostram-lhe como:

* Preparar dados para modelação de séries de tempo
* Configure parâmetros específicos da série de tempo num [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) objeto
* Executar previsões com dados da série de tempo

## <a name="prerequisites"></a>Pré-requisitos

Para este artigo que precisa, 

* Uma área de trabalho do Azure Machine Learning. Para criar o espaço de trabalho, consulte Criar um espaço de trabalho para [aprendizagem de máquinas Azure.](how-to-manage-workspace.md)

* Este artigo assume uma familiaridade básica com a criação de uma experiência automatizada de aprendizagem automática de máquinas. Siga o [tutorial](tutorial-auto-train-models.md) ou [como](how-to-configure-auto-train.md) ver os padrões básicos de design de experiências automatizadas de aprendizagem automática.

## <a name="preparing-data"></a> Preparar dados

A diferença mais importante entre um tipo de tarefa de regressão de previsão e um tipo de tarefa de regressão dentro do AutoML é incluir uma funcionalidade nos seus dados que representa uma série de tempo válida. Uma série de tempo regular tem uma frequência bem definida e consistente e tem um valor em cada ponto de amostra num período de tempo contínuo. 

Considere a seguinte imagem de um `sample.csv` ficheiro.
Este conjunto de dados é de dados de vendas diárias para uma empresa que tem duas lojas diferentes, A e B. 

Além disso, existem funcionalidades para

 *  `week_of_year`: permite que o modelo detete sazonalidade semanal.
* `day_datetime`: representa uma série de tempo limpo com frequência diária.
* `sales_quantity`: a coluna-alvo para as previsões em execução. 

```output
day_datetime,store,sales_quantity,week_of_year
9/3/2018,A,2000,36
9/3/2018,B,600,36
9/4/2018,A,2300,36
9/4/2018,B,550,36
9/5/2018,A,2100,36
9/5/2018,B,650,36
9/6/2018,A,2400,36
9/6/2018,B,700,36
9/7/2018,A,2450,36
9/7/2018,B,650,36
```


Leia os dados num dataframe do Pandas e, em seguida, utilize a `to_datetime` função para garantir que a série de tempos é um `datetime` tipo.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

Neste caso, os dados já estão classificados ascendendo pelo campo de tempo `day_datetime` . No entanto, ao configurar uma experiência, certifique-se de que a coluna de tempo desejada é ordenada de forma ascendente para construir uma série de tempo válida. 

O seguinte código, 
* Assume que os dados contêm 1.000 registos, e faz uma divisão determinística nos dados para criar conjuntos de dados de treino e teste. 
* Identifica a coluna do rótulo como `sales_quantity` .
* Separa o campo de etiquetas de `test_data` formação do `test_target` conjunto.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!IMPORTANT]
> Ao treinar um modelo para a previsão de valores futuros, certifique-se de que todas as funcionalidades utilizadas no treino podem ser utilizadas ao executar previsões para o seu horizonte pretendido. Por exemplo, ao criar uma previsão da procura, incluindo uma característica do preço atual das ações, poderia aumentar maciçamente a precisão da formação. No entanto, se pretender prever com um horizonte longo, poderá não ser capaz de prever com precisão os valores de ações futuros correspondentes a futuros pontos de série sonora, e a precisão do modelo poderá sofrer.

<a name="config"></a>

## <a name="training-and-validation-data"></a>Dados de formação e validação

Pode especificar conjuntos de comboios e validação separados diretamente no `AutoMLConfig` objeto.   Saiba mais sobre o [AutoMLConfig](#configure-experiment).

Para a previsão de séries de tempo, a **Validação da Cruz de Origem Rolante (ROCV)** é automaticamente utilizada quando passa os dados de treino e validação em conjunto, e define o número de dobras de validação cruzada com o `n_cross_validations` parâmetro no seu `AutoMLConfig` . ROCV divide a série em dados de treino e validação usando um ponto de tempo de origem. Deslizar a origem no tempo gera as dobras de validação cruzada. Esta estratégia preserva a integridade dos dados das séries temporítem e elimina o risco de fuga de dados

![texto alternativo](./media/how-to-auto-train-forecast/ROCV.svg)

Para outras opções de validação cruzada e divisão de dados, consulte [as divisões de dados de configuração e a validação cruzada em AutoML](how-to-configure-cross-validation-data-splits.md).


```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```

Saiba mais sobre como a AutoML aplica validação cruzada para [evitar modelos de sobreajustamento](concept-manage-ml-pitfalls.md#prevent-over-fitting).

## <a name="configure-experiment"></a>Experiência de configuração
O [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) objeto define as configurações e dados necessários para uma tarefa automatizada de aprendizagem automática de máquinas. A configuração para um modelo de previsão é semelhante à configuração de um modelo de regressão padrão, mas existem certos passos de apadragem e opções de configuração especificamente para dados da série de tempo. 

### <a name="featurization-steps"></a>Etapas de exibição

Em todas as experiências automatizadas de aprendizagem automática de máquinas, as técnicas automáticas de escala e normalização são aplicadas aos seus dados por padrão. Estas técnicas são tipos de **caracterização** que ajudam *certos* algoritmos que são sensíveis a características em escalas diferentes. Saiba mais sobre os passos de exibição padrão na [Exibição em AutoML](how-to-configure-auto-features.md#automatic-featurization)

No entanto, os seguintes passos são executados apenas para `forecasting` tipos de tarefas:

* Detetar a frequência da amostra de séries temporárias (por exemplo, hora, dia, semanalmente) e criar novos registos para pontos de tempo ausentes para tornar a série contínua.
* Imputar valores em falta no alvo (via preenchimento de reencaminhamento) e colunas de características (utilizando valores medianos de coluna)
* Criar funcionalidades baseadas em identificadores de séries de tempo para permitir efeitos fixos em diferentes séries
* Criar funcionalidades baseadas no tempo para ajudar na aprendizagem de padrões sazonais
* Codificar variáveis categóricas para quantidades numéricas

Para obter um resumo das características que são criadas como resultado destes passos, consulte [a transparência da Participação](how-to-configure-auto-features.md#featurization-transparency)

> [!NOTE]
> As etapas automatizadas de aprendizagem automática (normalização de recurso, manuseamento de dados em falta, conversão de texto em numérico, etc.) tornam-se parte do modelo subjacente. Ao utilizar o modelo para previsões, aplicam-se automaticamente os mesmos passos de apresentação aplicados durante o treino.

#### <a name="customize-featurization"></a>Personalizar a exibição

Também tem a opção de personalizar as definições de exibição para garantir que os dados e funcionalidades que são utilizados para treinar o seu modelo ML resultam em previsões relevantes. 

As personalizações suportadas para `forecasting` tarefas incluem:

|Personalização|Definição|
|--|--|
|**Atualização do propósito da coluna**|Substitua o tipo de função detetada automaticamente para a coluna especificada.|
|**Atualização do parâmetro do transformador** |Atualize os parâmetros para o transformador especificado. Atualmente suporta *o Imputer* (fill_value e mediano).|
|**Colunas de queda** |Especifica as colunas a deixar de serem apresentando.|

Para personalizar as ações com o SDK, especifique `"featurization": FeaturizationConfig` no seu `AutoMLConfig` objeto. Saiba mais sobre [as apresentações personalizadas.](how-to-configure-auto-features.md#customize-featurization)

```python
featurization_config = FeaturizationConfig()
# `logQuantity` is a leaky feature, so we remove it.
featurization_config.drop_columns = ['logQuantitity']
# Force the CPWVOL5 feature to be of numeric type.
featurization_config.add_column_purpose('CPWVOL5', 'Numeric')
# Fill missing values in the target column, Quantity, with zeroes.
featurization_config.add_transformer_params('Imputer', ['Quantity'], {"strategy": "constant", "fill_value": 0})
# Fill mising values in the `INCOME` column with median value.
featurization_config.add_transformer_params('Imputer', ['INCOME'], {"strategy": "median"})
```

Se estiver a usar o estúdio Azure Machine Learning para a sua experiência, consulte o [artigo de como fazer.](how-to-use-automated-ml-for-ml-models.md#customize-featurization)

### <a name="configuration-settings"></a>Definições de configuração

À semelhança de um problema de regressão, define-se parâmetros de treino padrão como tipo de tarefa, número de iterações, dados de treino e número de validações cruzadas. Para as tarefas de previsão, há parâmetros adicionais que devem ser definidos que afetam a experiência. 

A tabela a seguir resume estes parâmetros adicionais. Consulte a [documentação de referência](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) para padrões de design de sintaxe.

| Nome do parâmetro &nbsp; | Descrição | Obrigatório |
|-------|-------|-------|
|`time_column_name`|Utilizado para especificar a coluna de datas nos dados de entrada utilizados para a construção da série de tempo e inferir a sua frequência.|✓|
|`forecast_horizon`|Define quantos períodos para a frente gostaria de prever. O horizonte está em unidades da frequência da série-tempo. As unidades baseiam-se no intervalo de tempo dos seus dados de treino, por exemplo, mensais, semanais que o meteorologista deve prever.|✓|
|`enable_dnn`|[Ativar as NNNs de Previsão]().||
|`time_series_id_column_names`|O(s) nome da coluna(s) usado para identificar exclusivamente as séries de tempo em dados que têm várias linhas com a mesma marca de tempo. Se os identificadores de séries temporquiçantes não forem definidos, presume-se que o conjunto de dados seja uma série de tempo. Para saber mais sobre séries únicas, consulte o [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).||
|`target_lags`|Número de linhas para atrasar os valores-alvo com base na frequência dos dados. O lag é representado como uma lista ou inteiro único. O lag deve ser usado quando a relação entre as variáveis independentes e a variável dependente não corresponde ou correlaciona por defeito. ||
|`target_rolling_window_size`|*n* períodos históricos a utilizar para gerar valores previstos, <= tamanho do conjunto de treino. Se omitido, *n* é o tamanho completo do conjunto de treino. Especifique este parâmetro quando apenas pretende considerar uma certa quantidade de história ao treinar o modelo. Saiba mais sobre [a agregação de janelas de rolamento de alvos](#target-rolling-window-aggregation).||


O seguinte código, 
* Cria o `time-series settings` como um objeto dicionário. 
* Define o `time_column_name` campo no conjunto de `day_datetime` dados. 
* Define o `time_series_id_column_names` parâmetro para `"store"` . Isto garante a criação **de dois grupos separados de séries de tempo** para os dados; um para a loja A e B.
* Define `forecast_horizon` o a 50 para prever para todo o conjunto de testes. 
* Define uma janela de previsão para 10 períodos com `target_rolling_window_size`
* Especifica um único lag nos valores-alvo durante dois períodos à frente com o `target_lags` parâmetro. 
* Define `target_lags` a definição "auto" recomendada, que irá detetar automaticamente este valor para si.

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "time_series_id_column_names": ["store"],
    "forecast_horizon": 50,
    "target_lags": "auto",
    "target_rolling_window_size": 10,
}
```

Estes `time_series_settings` são então passados para o seu objeto padrão `AutoMLConfig` juntamente com o tipo de `forecasting` tarefa, métrica primária, critérios de saída e dados de treino. 

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

## <a name="optional-configurations"></a>Configurações opcionais

Estão disponíveis configurações opcionais adicionais para tarefas de previsão, tais como permitir a aprendizagem profunda e especificar uma agregação de janelas de rolamento de alvo. 

### <a name="enable-deep-learning"></a>Permitir a aprendizagem profunda

> [!NOTE]
> O suporte do DNN para a previsão em Machine Learning automatizado está em **pré-visualização** e não é suportado para execuções locais.

Também pode aproveitar a aprendizagem profunda com redes neuronais profundas, DNNs, para melhorar as pontuações do seu modelo. A aprendizagem profunda automatizada da ML permite a previsão de dados univariados e multivariados de séries de tempo.

Os modelos de aprendizagem profunda têm três capacidades intrínsecas:
1. Eles podem aprender com mapeamentos arbitrários de entradas para saídas
1. Suportam múltiplas entradas e saídas
1. Podem extrair automaticamente padrões em dados de entrada que se estendem por longas sequências. 

Para permitir uma aprendizagem profunda, coloque o `enable_dnn=True` objeto no `AutoMLConfig` objeto.

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **time_series_settings)
```
Para ativar a DNN para uma experiência AutoML criada no estúdio Azure Machine Learning, consulte as definições do [tipo de tarefa no estúdio como fazê-lo](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment).

A ML automatizada fornece aos utilizadores séries temporais nativas e modelos de aprendizagem profunda como parte do sistema de recomendação. 


Modelos| Descrição | Benefícios
----|----|---
Profeta (Pré-visualização)|O profeta trabalha melhor com séries temporéticas que têm fortes efeitos sazonais e várias estações de dados históricos. Para alavancar este modelo, instale-o localmente `pip install fbprophet` utilizando. | A precisão & rápida, robusta para fora, dados em falta e mudanças dramáticas nas suas séries de tempo.
Auto-ARIMA (Pré-visualização)|A média móvel integrada autorregressiva (ARIMA) tem um melhor desempenho, quando os dados estão estacionários. Isto significa que as suas propriedades estatísticas como a média e a variação são constantes em todo o conjunto. Por exemplo, se atirares uma moeda, então a probabilidade de receberes cabeças é de 50%, independentemente de virares hoje, amanhã ou no próximo ano.| Ótimo para séries univariadas, uma vez que os valores passados são usados para prever os valores futuros.
PrevisãoTCN (pré-visualização)| A ForecastTCN é um modelo de rede neural projetado para enfrentar as tarefas de previsão mais exigentes, capturando tendências locais e globais não lineares nos seus dados, bem como relações entre séries de tempo.|Capaz de alavancar tendências complexas nos seus dados e facilmente escalar para o maior dos conjuntos de dados.


Consulte o [caderno de previsão de produção de bebidas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) para obter um código detalhado que utiliza dnns.


### <a name="target-rolling-window-aggregation"></a>Agregação da janela de rolamento do alvo
Muitas vezes, a melhor informação que um meteorologista pode ter é o valor recente do alvo.  As agregações de janelas de rolamento de alvo permitem adicionar uma agregação rolante dos valores de dados como funcionalidades. Gerar e utilizar estas funcionalidades adicionais como dados contextuais extra ajuda na precisão do modelo do comboio.

Por exemplo, digamos que quer prever a procura de energia. É melhor adicionar uma característica de janela rolante de três dias para ter em conta as alterações térmicas dos espaços aquecidos. Neste exemplo, crie esta janela colocando `target_rolling_window_size= 3` no `AutoMLConfig` construtor. 

A tabela mostra a engenharia de características resultantes que ocorre quando a agregação da janela é aplicada. As colunas para **mínimo, máximo** e **soma** são geradas numa janela deslizante de três com base nas definições definidas. Cada linha tem uma nova característica calculada, no caso do timetamp para 8 de setembro de 2017 4:00am os valores máximos, mínimos e somas são calculados usando os **valores** da procura para 8 de setembro de 2017 1:00AM - 3:00AM. Esta janela de três turnos ao longo para povoar dados para as restantes linhas.

![texto alternativo](./media/how-to-auto-train-forecast/target-roll.svg)


Ver um exemplo de código Python aproveitando a [função agregado de janelas de rolamento do alvo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).

## <a name="run-the-experiment"></a>Executar a experimentação 

Quando tiver o `AutoMLConfig` seu objeto pronto, pode submeter a experiência. Depois de terminar o modelo, recupere a melhor iteração de corrida.

```python
ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

## <a name="forecasting-with-best-model"></a>Previsão com melhor modelo

Utilize a melhor iteração do modelo para prever valores para o conjunto de dados de teste.

A `forecast()` função permite especificações de quando as previsões devem começar, ao contrário das `predict()` , que são normalmente usadas para tarefas de classificação e regressão.

No exemplo seguinte, substitui-se primeiro todos os valores `y_pred` em `NaN` . A origem prevista será no final dos dados de formação neste caso. No entanto, se substituísse apenas a segunda metade com `y_pred` `NaN` , a função deixaria os valores numéricos no primeiro semestre sem modificar, mas previu os `NaN` valores na segunda parte. A função devolve tanto os valores previstos como as características alinhadas.

Também pode utilizar o `forecast_destination` parâmetro na `forecast()` função para prever valores até uma data especificada.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Calcular o erro quadrado da média de raiz (RMSE) entre os `actual_labels` valores reais e os valores previstos em `predict_labels` .

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Agora que a precisão global do modelo foi determinada, o próximo passo mais realista é usar o modelo para prever valores futuros desconhecidos. 

Forneça um conjunto de dados no mesmo formato que o conjunto de `test_data` testes, mas com datas futuras, e o conjunto de previsão resultante é os valores previstos para cada etapa da série de tempo. Assumindo que os últimos registos da série de tempos no conjunto de dados foram para 12/31/2018. Para prever a procura para o dia seguinte (ou quantos períodos precisa prever, <= `forecast_horizon` ), crie um recorde de séries de tempo única para cada loja para 01/01/2019.

```output
day_datetime,store,week_of_year
01/01/2019,A,1
01/01/2019,A,1
```

Repita os passos necessários para carregar estes dados futuros num dataframe e, em seguida, corra `best_run.predict(test_data)` para prever valores futuros.

> [!NOTE]
> Não se pode prever valores para um número de períodos superiores aos do `forecast_horizon` . O modelo deve ser re-treinado com um horizonte maior para prever valores futuros para além do horizonte atual.


## <a name="example-notebooks"></a>Blocos de notas de exemplo
Consulte os [cadernos de amostras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) de previsão para exemplos de código detalhados de configuração avançada de previsão, incluindo:

* [deteção e caracterização de férias](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [validação cruzada de origem rolante](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [atrasos configuráveis](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [funcionalidades agregadas de janelas rolantes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

## <a name="next-steps"></a>Passos seguintes

* Siga o [tutorial](tutorial-auto-train-models.md) para aprender a criar experiências com machine learning automatizado.
* Consulte o [Azure Machine Learning SDK para](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) documentação de referência python.
