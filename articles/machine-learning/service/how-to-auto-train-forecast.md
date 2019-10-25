---
title: Treinar automaticamente um modelo de previsão de série temporal
titleSuffix: Azure Machine Learning
description: Saiba como usar Azure Machine Learning para treinar um modelo de regressão de previsão de série temporal usando o aprendizado de máquina automatizado.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 06/20/2019
ms.openlocfilehash: 3cec6ee9368b1d9d1f2c9a627108aaf41c6da3c3
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819860"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Treinar automaticamente um modelo de previsão de série temporal

Neste artigo, você aprenderá a treinar um modelo de regressão de previsão de série temporal usando o aprendizado de máquina automatizado no Azure Machine Learning. Configurar um modelo de previsão é semelhante a configurar um modelo de regressão padrão usando o Machine Learning automatizado, mas algumas opções de configuração e etapas de pré-processamento existem para trabalhar com dados de série temporal. Os exemplos a seguir mostram como:

* Preparar dados para a modelagem de série temporal
* Configurar parâmetros de série temporal específicos em um objeto [`AutoMLConfig`](/python/api/azureml-train-automl/azureml.train.automl.automlconfig)
* Executar previsões com dados de série temporal

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Você pode usar o ML automatizado para combinar técnicas e abordagens e obter uma previsão de série temporal de alta qualidade e recomendada. Um experimento de série temporal automatizado é tratado como um problema de regressão multivariado. Os valores de série temporal anteriores são "dinamizados" para se tornarem dimensões adicionais para o regressor junto com outros previsões.

Essa abordagem, ao contrário dos métodos de série temporal clássica, tem uma vantagem de incorporar naturalmente várias variáveis contextuais e sua relação entre si durante o treinamento. Em aplicativos de previsão do mundo real, vários fatores podem influenciar uma previsão. Por exemplo, ao prever as vendas, as interações de tendências históricas, a taxa de câmbio e o preço, todos orientam o resultado das vendas. Um benefício adicional é que todas as inovações recentes nos modelos de regressão se aplicam imediatamente à previsão.

Você pode [Configurar](#config) o quanto no futuro a previsão deve estender (o horizonte de previsão), bem como um retardo e muito mais. O ML automatizado aprende um modelo único, mas geralmente ramificado internamente para todos os itens no conjunto de e horizontes de previsão. Por isso, mais dados estão disponíveis para estimar os parâmetros de modelo e a generalização para uma série não vista se torna possível.

Os recursos extraídos dos dados de treinamento desempenham uma função crítica. E o ML automatizado executa etapas de pré-processamento padrão e gera recursos adicionais de série temporal para capturar efeitos sazonais e maximizar a precisão preditiva.

## <a name="time-series-and-deep-learning-models"></a>Modelos de série temporal e aprendizado profundo


O ML automatizado fornece aos usuários os modelos de série temporal nativa e de aprendizado profundo como parte do sistema de recomendação. Esses aprendizes incluem:
+ Prophet
+ ARIMA automático
+ ForecastTCN

O profundo aprendizado automatizado do ML permite prever dados de série temporal monovariável e MultiVariable.

Os modelos de aprendizado profundo têm três capbailities intrínsecos:
1. Eles podem aprender com mapeamentos arbitrários de entradas para saídas
1. Eles dão suporte a várias entradas e saídas
1. Eles podem extrair automaticamente padrões em dados de entrada que se estendem por longas sequências

Considerando dados maiores, modelos de aprendizado profundo, como Microsoft ' ForecasTCN ', podem melhorar as pontuações do modelo resultante. 

Os aprendizes da série temporal nativa também são fornecidos como parte do ML automatizado. O Prophet funciona melhor com a série temporal que tem efeitos sazonais fortes e várias estações de dados históricos. O Prophet é preciso & rápido, robusto a exceções, dados ausentes e alterações consideráveis na sua série temporal. 

A média de movimentação integrada de regressão automática (ARIMA) é um método estatístico popular para a previsão de série temporal. Essa técnica de previsão é normalmente usada em cenários de previsão de curto prazo, em que os dados mostram evidências de tendências, como ciclos, que podem ser imprevisíveis e difíceis de modelar ou prever. O ARIMA transforma seus dados em dados estáticos para receber resultados consistentes e confiáveis.

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Machine Learning. Para criar o espaço de trabalho, consulte [criar um Azure Machine Learning espaço de trabalho](how-to-manage-workspace.md).
* Este artigo pressupõe familiaridade básica com a configuração de um experimento de aprendizado de máquina automatizado. Siga o [tutorial](tutorial-auto-train-models.md) ou [instruções](how-to-configure-auto-train.md) para ver os padrões básicos de design de experimento de aprendizado automático de máquina.

## <a name="preparing-data"></a>Preparando dados

A diferença mais importante entre um tipo de tarefa de regressão de previsão e o tipo de tarefa de regressão no Machine Learning automatizado é incluir um recurso em seus dados que representa uma série temporal válida. Uma série temporal regular tem uma frequência bem definida e consistente e tem um valor em cada ponto de exemplo em um período de tempo contínuo. Considere o seguinte instantâneo de um arquivo `sample.csv`.

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

Esse conjunto de dados é um exemplo simples de dados de vendas diários para uma empresa que tem duas lojas diferentes, A e B. Além disso, há um recurso para `week_of_year` que permitirá que o modelo detecte sazonalidade semanalmente. O campo `day_datetime` representa uma série temporal limpa com frequência diária e o campo `sales_quantity` é a coluna de destino para executar previsões. Leia os dados em um dataframe do pandas e use a função `to_datetime` para garantir que a série temporal seja um tipo de `datetime`.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

Nesse caso, os dados já estão classificados em ordem crescente pelo campo de tempo `day_datetime`. No entanto, ao configurar um experimento, verifique se a coluna de tempo desejada está classificada em ordem crescente para criar uma série temporal válida. Suponha que os dados contenham 1.000 registros e criem uma divisão determinística nos dados para criar conjuntos de dados de treinamento e teste. Identifique o nome da coluna do rótulo e defina-o como rótulo. Neste exemplo, o rótulo será `sales_quantity`. Em seguida, separe o campo de rótulo de `test_data` para formar o conjunto de `test_target`.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> Ao treinar um modelo para prever valores futuros, verifique se todos os recursos usados no treinamento podem ser usados ao executar previsões para o horizonte pretendido. Por exemplo, ao criar uma previsão de demanda, incluir um recurso para o preço de estoque atual poderia aumentar imensamente a precisão de treinamento. No entanto, se você pretende prever com um horizonte longo, talvez não seja possível prever com precisão valores de ações futuros correspondentes aos pontos de série temporal futuros, e a precisão do modelo pode ser afetada.

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>Configurar e executar experimento

Para tarefas de previsão, o Machine Learning automatizado usa etapas de pré-processamento e estimativa específicas para dados de série temporal. As seguintes etapas de pré-processamento serão executadas:

* Detecte a frequência de exemplo de série temporal (por exemplo, a cada hora, diariamente, semanalmente) e crie novos registros para pontos de tempo ausentes para tornar a série contínua.
* Imputar valores ausentes no destino (via avanço) e colunas de recurso (usando valores de coluna mediana)
* Criar recursos baseados em granulares para habilitar efeitos fixos em diferentes séries
* Crie recursos baseados em tempo para auxiliar no aprendizado de padrões sazonais
* Codificar variáveis categóricas em quantidades numéricas

O objeto `AutoMLConfig` define as configurações e os dados necessários para uma tarefa de aprendizado de máquina automatizada. Semelhante a um problema de regressão, você define parâmetros de treinamento padrão, como tipo de tarefa, número de iterações, dados de treinamento e número de validações cruzadas. Para tarefas de previsão, há parâmetros adicionais que devem ser definidos para afetar o experimento. A tabela a seguir explica cada parâmetro e seu uso.

| Param | Descrição | Obrigatório |
|-------|-------|-------|
|`time_column_name`|Usado para especificar a coluna datetime nos dados de entrada usados para criar a série temporal e inferir sua frequência.|✓|
|`grain_column_names`|Nome (s) definindo grupos de séries individuais nos dados de entrada. Se a granulação não for definida, o conjunto de dados será considerado uma série temporal.||
|`max_horizon`|Define o horizonte de previsão máximo desejado em unidades de frequência de série temporal. As unidades são baseadas no intervalo de tempo de seus dados de treinamento, por exemplo, mensalmente, semanalmente que o previsão deve prever.|✓|
|`target_lags`|Número de linhas para atrasar os valores de destino com base na frequência dos dados. Isso é representado como uma lista ou um único inteiro. O retardo deve ser usado quando a relação entre as variáveis independentes e a variável dependente não corresponder ou correlacionar por padrão. Por exemplo, ao tentar prever a demanda de um produto, a demanda em qualquer mês pode depender do preço de mercadorias específicas de 3 meses antes. Neste exemplo, você pode querer atrasar o destino (demanda) negativamente por 3 meses para que o modelo seja treinamento na relação correta.||
|`target_rolling_window_size`|*n* períodos históricos a serem usados para gerar valores previstos, < = tamanho do conjunto de treinamento. Se omitido, *n* será o tamanho completo do conjunto de treinamento. Especifique esse parâmetro quando desejar apenas considerar uma determinada quantidade de histórico ao treinar o modelo.||

Consulte a [documentação de referência](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) para obter mais informações.

Crie as configurações de série temporal como um objeto Dictionary. Defina o `time_column_name` para o campo `day_datetime` no conjunto de dados. Defina o parâmetro `grain_column_names` para garantir que **dois grupos de série temporal separados** sejam criados para os dados; uma para a loja A e B. por fim, defina o `max_horizon` como 50 para prever o conjunto de teste inteiro. Defina uma janela de previsão como 10 períodos com `target_rolling_window_size`e especifique um único retardo nos valores de destino para 2 períodos à frente com o parâmetro `target_lags`.

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10,
    "preprocess": True,
}
```

> [!NOTE]
> As etapas de pré-processamento automatizado do Machine Learning (normalização de recursos, manipulação de dados ausentes, conversão de texto em numeric, etc.) tornam-se parte do modelo subjacente. Ao usar o modelo para previsões, as mesmas etapas de pré-processamento aplicadas durante o treinamento são aplicadas aos dados de entrada automaticamente.

Ao definir o `grain_column_names` no trecho de código acima, o AutoML criará dois grupos de série temporal separados, também conhecidos como várias séries temporais. Se nenhum detalhamento for definido, AutoML assumirá que o conjunto de data é uma única série temporal. Para saber mais sobre a série temporal única, consulte o [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

Agora, crie um objeto `AutoMLConfig` padrão, especificando o tipo de tarefa `forecasting` e envie o experimento. Após a conclusão do modelo, recupere a melhor iteração de execução.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
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

Consulte o [notebook de demanda de energia](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) para obter exemplos de código detalhado de configuração de previsão avançada, incluindo:

* detecção de feriados e personalização
* validação cruzada de origem sem interrupção
* retardo configurável
* recursos agregados de janela sem interrupção

### <a name="view-feature-engineering-summary"></a>Exibir Resumo de engenharia de recursos

Para os tipos de tarefa de série temporal no Machine Learning automatizado, você pode exibir detalhes do processo de engenharia de recursos. O código a seguir mostra cada recurso bruto junto com os seguintes atributos:

* Nome do recurso bruto
* Número de recursos de engenharia formados fora deste recurso bruto
* Tipo detectado
* Se o recurso foi descartado
* Lista de transformações de recursos para o recurso bruto

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Prevendo com o melhor modelo

Use a melhor iteração de modelo para prever valores para o conjunto de dados de teste.

```python
predict_labels = fitted_model.predict(test_data)
actual_labels = test_labels.flatten()
```

Como alternativa, você pode usar a função `forecast()` em vez de `predict()`, que permitirá especificações de quando as previsões devem ser iniciadas. No exemplo a seguir, você primeiro substitui todos os valores em `y_pred` por `NaN`. A origem da previsão estará no final dos dados de treinamento nesse caso, como normalmente seria ao usar `predict()`. No entanto, se você substituiu apenas a segunda metade de `y_pred` por `NaN`, a função deixaria os valores numéricos na primeira metade sem modificações, mas preverá os valores de `NaN` na segunda metade. A função retorna os valores previstos e os recursos alinhados.

Você também pode usar o parâmetro `forecast_destination` na função `forecast()` para prever valores até uma data especificada.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Calcule RMSE (erro ao quadrado da média raiz) entre o `actual_labels` valores reais e os valores previstos em `predict_labels`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_lables, predict_labels))
rmse
```

Agora que a precisão geral do modelo foi determinada, a próxima etapa realista é usar o modelo para prever valores futuros desconhecidos. Basta fornecer um conjunto de dados no mesmo formato que o conjunto de teste `test_data` mas com DateTimes futuros e o conjunto de previsão resultante é os valores previstos para cada etapa da série temporal. Suponha que os últimos registros de série temporal no conjunto de dados eram de 12/31/2018. Para prever a demanda para o dia seguinte (ou quantos períodos forem necessários para prever, < = `max_horizon`), crie um único registro de série temporal para cada loja para 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Repita as etapas necessárias para carregar esses dados futuros em um dataframe e, em seguida, execute `best_run.predict(test_data)` para prever valores futuros.

> [!NOTE]
> Os valores não podem ser previstos para o número de períodos maiores que o `max_horizon`. O modelo deve ser treinado novamente com um horizonte maior para prever valores futuros além do horizonte atual.

## <a name="next-steps"></a>Passos seguintes

* Siga o [tutorial](tutorial-auto-train-models.md) para aprender a criar experimentos com o Machine Learning automatizado.
* Exiba a documentação de referência do [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) .
