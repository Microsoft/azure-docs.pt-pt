---
title: Auto-train um modelo de previsão de série de tempo
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o Azure Machine Learning para treinar um modelo de regressão de previsão de séries temportivas utilizando machine learning automatizado.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 859f8a9c2bf644461c8945255de9f925b4e943f4
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251855"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Auto-train um modelo de previsão de série de tempo
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a treinar um modelo de regressão de previsão de séries temportivas utilizando machine learning automatizado em Azure Machine Learning. Configurar um modelo de previsão é semelhante à configuração de um modelo de regressão padrão utilizando machine learning automatizado, mas existem certas opções de configuração e passos de pré-processamento para trabalhar com dados da série de tempo. Os seguintes exemplos mostram-lhe como:

* Preparar dados para modelação de séries temporais
* Configure parâmetros específicos da série de tempo num objeto [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)
* Executar previsões com dados da série de tempo

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Pode utilizar ml automatizado para combinar técnicas e abordagens e obter uma previsão recomendada e de alta qualidade da série de tempo. Uma experiência automatizada em séries temporais é tratada como um problema de regressão multivariada. Os valores das séries de tempo passadas são "apostados" para se tornarem dimensões adicionais para o regresso, juntamente com outros preditores.

Esta abordagem, ao contrário dos métodos clássicos da série de tempo, tem uma vantagem de incorporar naturalmente múltiplas variáveis contextuais e sua relação entre si durante o treino. Nas aplicações de previsão do mundo real, vários fatores podem influenciar uma previsão. Por exemplo, ao prever vendas, as interações das tendências históricas, da taxa de câmbio e do preço impulsionam conjuntamente o resultado das vendas. Um outro benefício é que todas as inovações recentes nos modelos de regressão se aplicam imediatamente à previsão.

Pode [configurar](#config) até onde deve ser o futuro a previsão (o horizonte de previsão), bem como os lags e muito mais. O ML automatizado aprende um modelo único, mas muitas vezes ramificado internamente para todos os itens nos horizontes de dataset e previsão. Mais dados estão assim disponíveis para estimar os parâmetros dos modelos e a generalização para séries invisíveis torna-se possível.

As funcionalidades extraídas dos dados de treino desempenham um papel fundamental. E, ml automatizado executa passos padrão de pré-processamento e gera funcionalidades adicionais da série de tempo para capturar efeitos sazonais e maximizar a precisão preditiva.

## <a name="time-series-and-deep-learning-models"></a>Séries temporais e modelos de Aprendizagem Profunda


O ML automatizado fornece aos utilizadores modelos de time-series e deep learning nativos como parte do sistema de recomendação. Estes alunos incluem:
+ Profeta
+ Auto-ARIMA
+ PrevisãoCN

A aprendizagem profunda automatizada do ML permite a previsão de dados de séries de tempo univariadae multivariada.

Os modelos de aprendizagem profunda têm três capacidades intrínsecas:
1. Eles podem aprender com mapeamentos arbitrários de inputs a saídas
1. Suportam várias inputs e saídas
1. Podem extrair automaticamente padrões em dados de entrada que se estendem por longas sequências

Dados maiores, modelos de aprendizagem profunda, como o ForecastTCN da Microsoft, podem melhorar as pontuações do modelo resultante. 

Os alunos de séries de horárionativo também são fornecidos como parte do ML automatizado. O profeta trabalha melhor com séries temporais que têm fortes efeitos sazonais e várias estações de dados históricos. O profeta é preciso e rápido, robusto para outliers, dados em falta e mudanças dramáticas na sua série de tempo. 

AutoRegressive Integrated Moving Average (ARIMA) é um método estatístico popular para a previsão de séries de tempo. Esta técnica de previsão é comumente utilizada em cenários de previsão de curto prazo onde os dados mostram evidências de tendências como ciclos, que podem ser imprevisíveis e difíceis de modelar ou prever. A Auto-ARIMA transforma os seus dados em dados estacionários para receber resultados consistentes e fiáveis.

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Machine Learning. Para criar o espaço de trabalho, consulte Criar um espaço de [trabalho azure machine learning](how-to-manage-workspace.md).
* Este artigo assume a familiaridade básica com a criação de uma experiência automatizada de aprendizagem automática de máquinas. Siga o [tutorial](tutorial-auto-train-models.md) ou [como](how-to-configure-auto-train.md) ver os padrões básicos de design de experiências automatizadas de aprendizagem automática.

## <a name="preparing-data"></a>Preparação de dados

A diferença mais importante entre um tipo de tarefa de regressão de previsão e o tipo de tarefa de regressão dentro do machine learning automatizado é incluir uma funcionalidade nos seus dados que representa uma série de tempo válida. Uma série de tempo regular tem uma frequência bem definida e consistente e tem um valor em cada ponto de amostra num período de tempo contínuo. Considere a seguinte foto de um ficheiro `sample.csv`.

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

Este conjunto de dados é um simples exemplo de dados de vendas diárias para uma empresa que tem duas lojas diferentes, A e B. Além disso, existe uma funcionalidade para `week_of_year` que permitirá ao modelo detetar sazonalidade semanal. O campo `day_datetime` representa uma série de tempo limpo com frequência diária, e o campo `sales_quantity` é a coluna alvo para as previsões de execução. Leia os dados num quadro de dados do Pandas e, em seguida, use a função `to_datetime` para garantir que a série de horários é um tipo `datetime`.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

Neste caso, os dados já estão classificados ascendentes pelo campo de tempo `day_datetime`. No entanto, ao configurar uma experiência, certifique-se de que a coluna de tempo desejada é ordenada em forma ascendente para construir uma série de tempo válida. Assuma que os dados contêm 1.000 registos, e faça uma divisão determinística nos dados para criar conjuntos de dados de treino e teste. Identifique o nome da coluna da etiqueta e coloque-o no rótulo. Neste exemplo, o rótulo será `sales_quantity`. Em seguida, separe o campo de etiquetas de `test_data` para formar o conjunto de `test_target`.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> Ao treinar um modelo para a previsão de valores futuros, certifique-se de que todas as funcionalidades utilizadas no treino podem ser usadas ao executar previsões para o seu horizonte pretendido. Por exemplo, ao criar uma previsão de procura, incluindo uma característica para o preço atual das ações poderia aumentar maciçamente a precisão da formação. No entanto, se pretender prever com um horizonte longo, poderá não ser capaz de prever com precisão os valores de stock futuros correspondentes a futuros pontos da série temporal, e a precisão do modelo poderá sofrer.

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>Configurar e executar experiência

Para as tarefas de previsão, o machine learning automatizado utiliza passos de pré-processamento e estimativa específicos dos dados da série temporal. Serão executadas as seguintes etapas de pré-processamento:

* Detete a frequência da amostra da série de tempo (por exemplo, de hora em hora, diariamente, semanalmente) e crie novos registos para pontos de tempo ausentes para tornar a série contínua.
* Impute valores em falta no alvo (através de colunas de preenchimento para a frente) e colunas de características (utilizando valores médios de coluna)
* Criar funcionalidades à base de cereais para permitir efeitos fixos em diferentes séries
* Criar funcionalidades baseadas no tempo para ajudar na aprendizagem de padrões sazonais
* Codificar variáveis categóricas a quantidades numéricas

O [](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py)`AutoMLConfig`objeto define as definições e dados necessários para uma tarefa automatizada de aprendizagem automática de máquinas. Semelhante a um problema de regressão, define-se parâmetros de treino padrão como tipo de tarefa, número de iterações, dados de treino e número de validações cruzadas. Para as tarefas de previsão, existem parâmetros adicionais que devem ser definidos que afetam a experiência. A tabela seguinte explica cada parâmetro e a sua utilização.

| Param | Descrição | Necessário |
|-------|-------|-------|
|`time_column_name`|Utilizada para especificar a coluna data nos dados de entrada utilizados para a construção da série de tempo e inferindo a sua frequência.|✓|
|`grain_column_names`|Nomes ou grupos de séries individuais nos dados de entrada. Se o grão não for definido, presume-se que o conjunto de dados é uma série de tempo.||
|`max_horizon`|Define o horizonte de previsão máximo desejado em unidades de frequência de série seleção. As unidades baseiam-se no intervalo de tempo dos seus dados de treino, por exemplo, mensalmente, semanalmente, que o meteorologista deve prever.|✓|
|`target_lags`|Número de linhas para atrasar os valores-alvo com base na frequência dos dados. O lag é representado como uma lista ou único inteiro. O lag deve ser usado quando a relação entre as variáveis independentes e a variável dependente não corresponde ou correlaciona por padrão. Por exemplo, ao tentar prever a procura de um produto, a procura em qualquer mês pode depender do preço de mercadorias específicas 3 meses antes. Neste exemplo, pode querer atrasar o alvo (procura) negativamente por 3 meses para que o modelo esteja a treinar na relação correta.||
|`target_rolling_window_size`|*n* períodos históricos a utilizar para gerar valores previstos, <= tamanho do conjunto de treino. Se omitida, *n* é o tamanho completo do conjunto de treino. Especifique este parâmetro quando só quiser considerar uma certa quantidade de história ao treinar o modelo.||
|`enable_dnn`|Ativar DNNs de previsão.||

Consulte a [documentação](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) de referência para mais informações.

Crie as definições da série de tempo como um objeto de dicionário. Detete a `time_column_name` para o campo `day_datetime` no conjunto de dados. Definir o parâmetro `grain_column_names` para garantir a criação de **dois grupos separados de séries temporais** para os dados; um para a loja A e B. Por último, detete te `max_horizon` para 50 para prever para todo o conjunto de testes. Estabeleça uma janela de previsão para 10 períodos com `target_rolling_window_size`, e especifique um único atraso nos valores-alvo durante dois períodos à frente com o parâmetro `target_lags`. Recomenda-se definir `max_horizon`, `target_rolling_window_size` e `target_lags` para "auto" que detete automaticamente estes valores para si. No exemplo abaixo, foram utilizadas definições "auto" para estes parâmetros. 

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": "auto",
    "target_lags": "auto",
    "target_rolling_window_size": "auto",
    "preprocess": True,
}
```

> [!NOTE]
> Os passos automatizados de pré-processamento de aprendizagem automática (normalização de recursos, manuseamento de dados em falta, conversão de texto para numérico, etc.) tornam-se parte do modelo subjacente. Ao utilizar o modelo para previsões, as mesmas etapas de pré-processamento aplicadas durante o treino são aplicadas automaticamente aos seus dados de entrada.

Ao definir o `grain_column_names` no código acima, a AutoML criará dois grupos de séries de tempo separadas, também conhecidos como várias séries de tempo. Se nenhum grão for definido, a AutoML assumirá que o conjunto de dados é uma série de tempo única. Para saber mais sobre as séries de tempo únicas, consulte o [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

Agora crie um objeto padrão `AutoMLConfig`, especificando o tipo de tarefa `forecasting`, e submeta a experiência. Depois do modelo terminar, recupere a melhor iteração de execução.

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

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

Consulte os cadernos de [amostras de previsão](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) para obter exemplos de código detalhados de configuração de previsão avançada, incluindo:

* [deteção e caracterização de férias](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [validação cruzada de origem rolante](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [lags configuráveis](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [características agregados de janela sonantes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>Configure uma experiência de previsão de dnn

> [!NOTE]
> O suporte do DNN para previsão em Machine Learning Automatizado está em Pré-visualização e não é suportado para corridas locais.

Para alavancar os DNNs para previsão, terá de definir o parâmetro `enable_dnn` no AutoMLConfig. 

Recomendamos a utilização de um cluster AML Compute com GPU SKUs e pelo menos dois nós como alvo da computação. Para permitir tempo suficiente para que o treino de DNN termine, recomendamos que o tempo de experimentação seja no mínimo um par de horas.
Para obter mais informações sobre os tamanhos de computação AML e VM que incluam GPU's, consulte a documentação da [AML Compute](how-to-set-up-training-targets.md#amlcompute) e a [documentação otimizada de tamanhos de máquinavirtual da GPU.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)

Consulte o caderno de previsão de produção de [bebidas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) para obter um exemplo de código detalhado que alavanca os DNNs.

### <a name="view-feature-engineering-summary"></a>Ver resumo de engenharia de recursos

Para tipos de tarefas de série seleção em machine learning automatizado, pode visualizar detalhes do processo de engenharia de funcionalidades. O seguinte código mostra cada característica bruta juntamente com os seguintes atributos:

* Nome de recurso cru
* Número de características projetadas formadas a partir desta característica bruta
* Tipo detetado
* Se a funcionalidade foi abandonada
* Lista de transformações de recursos para a característica bruta

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Previsão com melhor modelo

Utilize a melhor iteração do modelo para prever valores para o conjunto de dados de teste.

```python
predict_labels = fitted_model.predict(test_data)
actual_labels = test_labels.flatten()
```

Em alternativa, pode utilizar a função `forecast()` em vez de `predict()`, o que permitirá especificações de quando as previsões devem começar. No exemplo seguinte, substitui-se primeiro todos os valores em `y_pred` por `NaN`. A origem prevista será no final dos dados de formação neste caso, como seria normalmente quando se utiliza`predict()`. No entanto, se substituísse apenas a segunda metade do `y_pred` por `NaN`, a função deixaria os valores numéricos na primeira parte inalterados, mas previa os valores `NaN` na segunda parte. A função devolve tanto os valores previstos como as características alinhadas.

Também pode utilizar o parâmetro `forecast_destination` na função `forecast()` para prever valores até uma data especificada.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Calcular a RMSE (erro quadrado da média raiz) entre os valores reais `actual_labels` e os valores previstos em `predict_labels`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Agora que a precisão global do modelo foi determinada, o próximo passo mais realista é usar o modelo para prever valores futuros desconhecidos. Forneça um conjunto de dados no mesmo formato que o conjunto de teste `test_data` mas com datas futuras, e o conjunto de previsão resultante são os valores previstos para cada passo da série temporal. Assuma que os últimos registos da série de tempo no conjunto de dados foram para 12/31/2018. Para prever a procura para o dia seguinte (ou quantos períodos for necessário prever, <= `max_horizon`), criar um único recorde de séries de tempo para cada loja para 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Repita os passos necessários para carregar estes dados futuros para um quadro de dados e, em seguida, executar `best_run.predict(test_data)` para prever valores futuros.

> [!NOTE]
> Não se podem prever valores para um número superior ao `max_horizon`. O modelo deve ser retreinado com um horizonte maior para prever valores futuros para além do horizonte atual.

## <a name="next-steps"></a>Passos seguintes

* Siga o [tutorial](tutorial-auto-train-models.md) para aprender a criar experiências com machine learning automatizado.
* Consulte o [Azure Machine Learning SDK para obter](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) documentação de referência python.
