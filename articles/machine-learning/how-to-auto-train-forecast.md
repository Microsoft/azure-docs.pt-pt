---
title: Treina automaticamente um modelo de previsão da série de tempo
titleSuffix: Azure Machine Learning
description: Aprenda a usar a Azure Machine Learning para treinar um modelo de regressão de previsão de séries temporizadas utilizando machine learning automatizado.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: how-to
ms.date: 03/09/2020
ms.openlocfilehash: 6ef21eb0bbd941af30af203f395a833a1ee32b44
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434703"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Treina automaticamente um modelo de previsão da série de tempo
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a configurar e a treinar um modelo de regressão de previsão de séries temporizadas utilizando aprendizagem automática de máquinas no [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py). 

Para uma experiência de baixo código, consulte o [Tutorial: Previsão de procura com machine learning automatizado](tutorial-automated-ml-forecast.md) para um exemplo de previsão de séries temporizadas usando machine learning automatizado no estúdio [Azure Machine Learning](https://ml.azure.com/).

Configurar um modelo de previsão é semelhante à criação de um modelo de regressão padrão utilizando machine learning automatizado, mas existem certas opções de configuração e passos de pré-processamento para trabalhar com dados da série de tempo. 

Por exemplo, pode [configurar](#config) até que ponto a previsão deve estender-se no futuro (o horizonte de previsão), bem como os atrasos e muito mais. ML automatizado aprende um modelo único, mas muitas vezes ramificado internamente para todos os itens nos horizontes de dataset e previsão. Mais dados estão, assim, disponíveis para estimar os parâmetros do modelo e a generalização para séries invisíveis torna-se possível.

Os seguintes exemplos mostram-lhe como:

* Preparar dados para modelação de séries de tempo
* Configure parâmetros específicos da série de tempo num [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) objeto
* Executar previsões com dados da série de tempo

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Ao contrário dos métodos clássicos das séries de tempo, em ml automatizados os valores das séries de tempo passadas são "apostados" para se tornarem dimensões adicionais para o regressor juntamente com outros preditores. Esta abordagem incorpora múltiplas variáveis contextuais e a sua relação entre si durante o treino. Uma vez que vários fatores podem influenciar uma previsão, este método alinha-se bem com cenários de previsão do mundo real. Por exemplo, ao prever vendas, interações de tendências históricas, taxa de câmbio e preço impulsionam conjuntamente o resultado das vendas. 

As características extraídas dos dados de treino desempenham um papel crítico. E, ml automatizado executa etapas de pré-processamento padrão e gera funcionalidades adicionais de séries de tempo para capturar efeitos sazonais e maximizar a precisão preditiva

## <a name="time-series-and-deep-learning-models"></a>Séries de tempo e modelos de aprendizagem profunda

A aprendizagem profunda automatizada da ML permite a previsão de dados univariados e multivariados de séries de tempo.

Os modelos de aprendizagem profunda têm três capacidades intrínsecas:
1. Eles podem aprender com mapeamentos arbitrários de entradas para saídas
1. Suportam múltiplas entradas e saídas
1. Podem extrair automaticamente padrões em dados de entrada que se estendem por longas sequências

Dados maiores, modelos de aprendizagem profunda, como o ForecastTCN da Microsoft, podem melhorar as pontuações do modelo resultante. Aprenda a configurar a [sua experiência para uma aprendizagem profunda.](#configure-a-dnn-enable-forecasting-experiment)

A ML automatizada fornece aos utilizadores séries temporais nativas e modelos de aprendizagem profunda como parte do sistema de recomendação. 

Modelos| Description | Benefícios
----|----|---
Profeta (Pré-visualização)|O profeta trabalha melhor com séries temporéticas que têm fortes efeitos sazonais e várias estações de dados históricos. Para alavancar este modelo, instale-o localmente `pip install fbprophet` utilizando. | A precisão & rápida, robusta para fora, dados em falta e mudanças dramáticas nas suas séries de tempo.
Auto-ARIMA (Pré-visualização)|A Média Móvel Integrada AutoRegressiva (ARIMA) tem um melhor desempenho, quando os dados estão estacionários. Isto significa que as suas propriedades estatísticas como a média e a variação são constantes em todo o conjunto. Por exemplo, se atirares uma moeda, então a probabilidade de receberes cabeças é de 50%, independentemente de virares hoje, amanhã ou no próximo ano.| Ótimo para séries univariadas, uma vez que os valores passados são usados para prever os valores futuros.
PrevisãoTCN (pré-visualização)| A ForecastTCN é um modelo de rede neural projetado para enfrentar as tarefas de previsão mais exigentes, capturando tendências locais e globais não lineares nos seus dados, bem como relações entre séries de tempo.|Capaz de alavancar tendências complexas nos seus dados e facilmente escalar para o maior dos conjuntos de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Machine Learning. Para criar o espaço de trabalho, consulte Criar um espaço de trabalho para [aprendizagem de máquinas Azure.](how-to-manage-workspace.md)
* Este artigo assume uma familiaridade básica com a criação de uma experiência automatizada de aprendizagem automática de máquinas. Siga o [tutorial](tutorial-auto-train-models.md) ou [como](how-to-configure-auto-train.md) ver os padrões básicos de design de experiências automatizadas de aprendizagem automática.

## <a name="preparing-data"></a> Preparar dados

A diferença mais importante entre um tipo de tarefa de regressão de previsão e tipo de tarefa de regressão no machine learning automatizado é incluir uma funcionalidade nos seus dados que representa uma série de tempo válida. Uma série de tempo regular tem uma frequência bem definida e consistente e tem um valor em cada ponto de amostra num período de tempo contínuo. Considere a seguinte imagem de um `sample.csv` ficheiro.

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

Este conjunto de dados é um exemplo simples de dados de vendas diárias para uma empresa que tem duas lojas diferentes, A e B. Além disso, existe uma funcionalidade para `week_of_year` que permita ao modelo detetar sazonalidade semanal. O campo `day_datetime` representa uma série de tempo limpo com frequência diária, e o campo é a `sales_quantity` coluna-alvo para as previsões de execução. Leia os dados num dataframe do Pandas e, em seguida, utilize a `to_datetime` função para garantir que a série de tempos é um `datetime` tipo.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

Neste caso, os dados já estão classificados ascendendo pelo campo de tempo `day_datetime` . No entanto, ao configurar uma experiência, certifique-se de que a coluna de tempo desejada é ordenada de forma ascendente para construir uma série de tempo válida. Assuma que os dados contêm 1.000 registos, e faça uma divisão determinística nos dados para criar conjuntos de dados de treino e teste. Identifique o nome da coluna do rótulo e coloque-o na etiqueta. Neste exemplo, o rótulo `sales_quantity` será. Em seguida, separe o campo de `test_data` etiquetas para formar o `test_target` conjunto.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> Ao treinar um modelo para a previsão de valores futuros, certifique-se de que todas as funcionalidades utilizadas no treino podem ser utilizadas ao executar previsões para o seu horizonte pretendido. Por exemplo, ao criar uma previsão da procura, incluindo uma característica do preço atual das ações, poderia aumentar maciçamente a precisão da formação. No entanto, se pretender prever com um horizonte longo, poderá não ser capaz de prever com precisão os valores de ações futuros correspondentes a futuros pontos de série sonora, e a precisão do modelo poderá sofrer.

<a name="config"></a>

## <a name="train-and-validation-data"></a>Dados de treino e validação
Pode especificar conjuntos de comboios e validação separados diretamente no `AutoMLConfig` construtor.

### <a name="rolling-origin-cross-validation"></a>Validação da Cruz de Origem Rolante
Para a previsão de séries de tempo rolling origin cross validation (ROCV) é usado para dividir séries de tempo de uma forma temporalmente consistente. ROCV divide a série em dados de treino e validação usando um ponto de tempo de origem. Deslizar a origem no tempo gera as dobras de validação cruzada.  

![texto alternativo](./media/how-to-auto-train-forecast/ROCV.svg)

Esta estratégia preservará a integridade dos dados das séries temporítem e eliminará o risco de fuga de dados. O ROCV é automaticamente utilizado para a previsão de tarefas, transmitindo os dados de formação e validação em conjunto e definindo o número de dobras de validação cruzada utilizando `n_cross_validations` . Saiba mais sobre como a ML automóvel aplica validação cruzada para [evitar modelos de sobreajustamento](concept-manage-ml-pitfalls.md#prevent-over-fitting).

```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```
Saiba mais sobre o [AutoMLConfig](#configure-and-run-experiment).

## <a name="configure-and-run-experiment"></a>Configurar e executar experiência

Para as tarefas de previsão, a aprendizagem automática de máquinas utiliza etapas de pré-processamento e estimativa específicas dos dados da série de tempo. Serão executadas as seguintes etapas de pré-processamento:

* Detetar a frequência da amostra de séries temporárias (por exemplo, hora, dia, semanalmente) e criar novos registos para pontos de tempo ausentes para tornar a série contínua.
* Imputar valores em falta no alvo (via preenchimento de reencaminhamento) e colunas de características (utilizando valores medianos de coluna)
* Criar funcionalidades baseadas em cereais para permitir efeitos fixos em diferentes séries
* Criar funcionalidades baseadas no tempo para ajudar na aprendizagem de padrões sazonais
* Codificar variáveis categóricas para quantidades numéricas

O [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) objeto define as configurações e dados necessários para uma tarefa automatizada de aprendizagem automática de máquinas. À semelhança de um problema de regressão, define-se parâmetros de treino padrão como tipo de tarefa, número de iterações, dados de treino e número de validações cruzadas. Para as tarefas de previsão, há parâmetros adicionais que devem ser definidos que afetam a experiência. A tabela seguinte explica cada parâmetro e a sua utilização.

| Nome do parâmetro &nbsp; | Description | Necessário |
|-------|-------|-------|
|`time_column_name`|Utilizado para especificar a coluna de datas nos dados de entrada utilizados para a construção da série de tempo e inferir a sua frequência.|✓|
|`grain_column_names`|Nome(s) que define grupos de séries individuais nos dados de entrada. Se o grão não for definido, presume-se que o conjunto de dados é uma série temporal.||
|`max_horizon`|Define o horizonte de previsão máximo desejado em unidades de frequência de série sonora. As unidades baseiam-se no intervalo de tempo dos seus dados de treino, por exemplo, mensais, semanais que o meteorologista deve prever.|✓|
|`target_lags`|Número de linhas para atrasar os valores-alvo com base na frequência dos dados. O lag é representado como uma lista ou inteiro único. O lag deve ser usado quando a relação entre as variáveis independentes e a variável dependente não corresponde ou correlaciona por defeito. Por exemplo, ao tentar prever a procura de um produto, a procura em qualquer mês pode depender do preço de mercadorias específicas 3 meses antes. Neste exemplo, pode querer atrasar negativamente o alvo (procura) em 3 meses para que o modelo esteja a treinar sobre a relação correta.||
|`target_rolling_window_size`|*n* períodos históricos a utilizar para gerar valores previstos, <= tamanho do conjunto de treino. Se omitido, *n* é o tamanho completo do conjunto de treino. Especifique este parâmetro quando apenas pretende considerar uma certa quantidade de história ao treinar o modelo.||
|`enable_dnn`|Ativar as DNNs de Previsão.||

Consulte a [documentação de referência](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) para obter mais informações.

Crie as definições da série de tempo como um objeto dicionário. Desa parte `time_column_name` para o campo no conjunto de `day_datetime` dados. Defina o `grain_column_names` parâmetro para garantir que são **criados dois grupos separados de séries de tempo** para os dados; um para a loja A e B. Por último, defina o `max_horizon` 50 para prever para todo o conjunto de testes. Desaponte uma janela de previsão para 10 períodos com `target_rolling_window_size` , e especifique um único lag nos valores-alvo para dois períodos à frente com o `target_lags` parâmetro. Recomenda-se a definição `max_horizon` `target_rolling_window_size` e `target_lags` "auto" que detete automaticamente estes valores para si. No exemplo abaixo, foram utilizadas definições "auto" para estes parâmetros. 

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
> As etapas de pré-processamento de aprendizagem automática automatizadas (normalização de recurso, manuseamento de dados em falta, conversão de texto em numérico, etc.) tornam-se parte do modelo subjacente. Ao utilizar o modelo para previsões, as mesmas etapas de pré-processamento aplicadas durante o treino são aplicadas automaticamente aos seus dados de entrada.

Ao definir o `grain_column_names` corte de código acima, o AutoML criará dois grupos separados de séries de tempo, também conhecidos como séries de tempo múltiplas. Se nenhum grão for definido, a AutoML assumirá que o conjunto de dados é uma única série de tempo. Para saber mais sobre séries únicas, consulte o [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

Agora crie um `AutoMLConfig` objeto padrão, especificando o `forecasting` tipo de tarefa, e submeta a experiência. Depois de terminar o modelo, recupere a melhor iteração de corrida.

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

Consulte os [cadernos de amostras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) de previsão para exemplos de código detalhados de configuração avançada de previsão, incluindo:

* [deteção e caracterização de férias](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [validação cruzada de origem rolante](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [atrasos configuráveis](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [funcionalidades agregadas de janelas rolantes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>Configurar um DNN permitir a experiência de previsão

> [!NOTE]
> O suporte do DNN para a previsão em Machine Learning automatizado está em Pré-visualização e não é suportado para execuções locais.

Para aproveitar os DNNs para a previsão, terá de definir o `enable_dnn` parâmetro no AutoMLConfig para ser verdadeiro. 

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **time_series_settings)
```
Saiba mais sobre [o AutoMLConfig](#configure-and-run-experiment).

Em alternativa, pode selecionar a `Enable deep learning` opção no estúdio.
![texto alternativo](./media/how-to-auto-train-forecast/enable_dnn.png)

Recomendamos a utilização de um cluster AML Compute com SKUs GPU e pelo menos dois nós como alvo de computação. Para permitir a conclusão do treino de DNN, recomendamos definir o tempo limite da experiência para um mínimo de algumas horas.
Para obter mais informações sobre os tamanhos de computação AML e VM que incluem GPU's, consulte a [documentação AML Compute](how-to-set-up-training-targets.md#amlcompute) e [a documentação de tamanhos de máquinas virtuais otimizadas da GPU.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)

Consulte o [caderno de previsão de produção de bebidas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) para obter um código detalhado que utiliza dnns.

### <a name="target-rolling-window-aggregation"></a>Agregação da janela de rolamento do alvo
Muitas vezes, a melhor informação que um meteorologista pode ter é o valor recente do alvo. Criar estatísticas cumulativas do alvo pode aumentar a precisão das suas previsões. As agregações de janelas de rolamento de alvos permitem adicionar uma agregação rolante dos valores de dados como funcionalidades. Para ativar as janelas de rolamento de alvos, ajuste `target_rolling_window_size` o tamanho da janela de inteiros desejado. 

Um exemplo disso pode ser visto na previsão da procura de energia. Pode adicionar uma característica de janela rolante de três dias para explicar as alterações térmicas dos espaços aquecidos. No exemplo abaixo, criamos esta janela de tamanho três, colocando `target_rolling_window_size=3` no `AutoMLConfig` construtor. A tabela mostra a engenharia de características que ocorre quando a agregação da janela é aplicada. As colunas para mínimo, máximo e soma são geradas numa janela deslizante de três com base nas definições definidas. Cada linha tem uma nova característica calculada, no caso do carimbo de hora para 8 de setembro de 2017 4:00am os valores máximos, mínimos e somas são calculados usando os valores da procura para 8 de setembro de 2017 1:00AM - 3:00AM. Esta janela de três turnos ao longo para povoar dados para as restantes linhas.

![texto alternativo](./media/how-to-auto-train-forecast/target-roll.svg)

Gerar e utilizar estas funcionalidades adicionais como dados contextuais extra ajuda na precisão do modelo do comboio.

Ver um exemplo de código Python aproveitando a [função agregado de janelas de rolamento do alvo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).

### <a name="view-feature-engineering-summary"></a>Ver resumo de engenharia de recursos

Para tipos de tarefas de séries tempotarefas em machine learning automatizado, pode ver detalhes do processo de engenharia de recursos. O seguinte código mostra cada característica em bruto juntamente com os seguintes atributos:

* Nome de recurso cru
* Número de recursos projetados formados a partir desta característica bruta
* Tipo detetado
* Se a funcionalidade foi abandonada
* Lista de transformações de recursos para a característica bruta

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Previsão com melhor modelo

Utilize a melhor iteração do modelo para prever valores para o conjunto de dados de teste.

A `forecast()` função deve ser utilizada em vez de `predict()` , isto permitirá especificações de quando as previsões devem começar. No exemplo seguinte, substitui-se primeiro todos os valores `y_pred` em `NaN` . A origem prevista será no final dos dados de formação neste caso, como seria normalmente quando se utiliza `predict()` . No entanto, se substituísse apenas a segunda metade com `y_pred` `NaN` , a função deixaria os valores numéricos no primeiro semestre sem modificar, mas previu os `NaN` valores na segunda parte. A função devolve tanto os valores previstos como as características alinhadas.

Também pode utilizar o `forecast_destination` parâmetro na `forecast()` função para prever valores até uma data especificada.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Calcular RMSE (erro quadrado de raiz) entre os `actual_labels` valores reais e os valores previstos em `predict_labels` .

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Agora que a precisão global do modelo foi determinada, o próximo passo mais realista é usar o modelo para prever valores futuros desconhecidos. Forneça um conjunto de dados no mesmo formato que o conjunto de `test_data` testes, mas com datas futuras, e o conjunto de previsão resultante é os valores previstos para cada etapa da série de tempo. Assumindo que os últimos registos da série de tempos no conjunto de dados foram para 12/31/2018. Para prever a procura para o dia seguinte (ou quantos períodos precisa prever, <= `max_horizon` ), crie um recorde de séries de tempo única para cada loja para 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Repita os passos necessários para carregar estes dados futuros num dataframe e, em seguida, corra `best_run.predict(test_data)` para prever valores futuros.

> [!NOTE]
> Não se pode prever valores para um número de períodos superiores aos do `max_horizon` . O modelo deve ser re-treinado com um horizonte maior para prever valores futuros para além do horizonte atual.

## <a name="next-steps"></a>Passos seguintes

* Siga o [tutorial](tutorial-auto-train-models.md) para aprender a criar experiências com machine learning automatizado.
* Consulte o [Azure Machine Learning SDK para](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) documentação de referência python.
