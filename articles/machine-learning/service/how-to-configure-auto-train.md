---
title: Criar experimentos de ML automatizados
titleSuffix: Azure Machine Learning service
description: Aprendizagem automática escolhe um algoritmo para e gera um modelo pronto para implantação. Saiba as opções que pode utilizar para configurar automatizada experimentações de machine learning.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: 5dee966f8664bc14d81004e625ad9632066ffcb2
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742311"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurar experimentos de ML automatizados em Python

Neste guia, saiba como definir várias definições de configuração de seus experimentos de aprendizado de máquina automatizados com o [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Aprendizagem automática escolhe um algoritmo e hiperparâmetros para e gera um modelo pronto para implantação. Existem várias opções que pode utilizar para configurar automatizada experimentações de machine learning.

Para exibir exemplos de experimentos de aprendizado de máquina automatizados, consulte [tutorial: Treine um modelo de classificação com aprendizado](tutorial-auto-train-models.md) de máquina automatizado ou [modelos de treinamento com o aprendizado de máquina automatizado na nuvem](how-to-auto-train-remote.md).

Opções de configuração disponíveis no automatizada de machine learning:

* Selecione seu tipo de experimento: Previsão de classificação, regressão ou série temporal
* Origem de dados, formatos e obtenção de dados
* Escolher o destino de computação: local ou remoto
* Aprendizagem automatizada as definições de experimentação
* Executar uma automatizada experimentação do machine learning
* Explorar métricas de modelo
* Registe e implemente o modelo

Se preferir uma experiência sem código, você também poderá [criar experiências automatizadas de aprendizado de máquina no portal do Azure](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Selecione o tipo de experimentação

Antes de começar a sua experimentação, deve determinar o tipo de problema de aprendizado de máquina que está a resolver. Automatizada de machine learning suporta tipos de tarefa de classificação, regressão e previsão.

Automatizada de machine learning suporta os seguintes algoritmos durante a automação e o processo de otimização. Como um utilizador, não é necessário para especificar o algoritmo.

Classificação | Regressão | Previsão de série temporal
|-- |-- |--
[Regressão logística](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Net elástico](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Net elástico](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Aumentam a gradação](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Aumentam a gradação](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Aumentam a gradação](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Árvore de decisão](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Árvore de decisão](https://scikit-learn.org/stable/modules/tree.html#regression)|[Árvore de decisão](https://scikit-learn.org/stable/modules/tree.html#regression)
[K mais próximo de vizinhos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K mais próximo de vizinhos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K mais próximo de vizinhos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Classificação de Vetores de suporte de C (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Descendente gradação stochastic gradient (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Descendente gradação stochastic gradient (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Árvores extremamente aleatório](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Árvores extremamente aleatório](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Árvores extremamente aleatório](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classificador DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[Regressor DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Regressor DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Classificador linear DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regressor linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regressor linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[A classificação bayesiana Ingênua](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[Descendente gradação stochastic gradient (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

Use o `task` parâmetro `AutoMLConfig` no construtor para especificar o tipo de experimento.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

## <a name="data-source-and-format"></a>Origem de dados e de formato
Aprendizagem automática suporta os dados que residem no ambiente de trabalho local ou na cloud, como o armazenamento de Blobs do Azure. Os dados podem ser lidos em scikit-saiba formatos de dados suportados. Pode ler os dados em:
* Matrizes de Numpy X (recursos) e y (variável de destino ou também conhecido como etiqueta)
* Pandas dataframe

Exemplos:

*   Matrizes de Numpy

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data
    y_digits = digits.target
    ```

*   Pandas dataframe

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    y = df["Label"]
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Obter dados para executar a experimentação em computação remota

Para execuções remotas, você precisa tornar os dados acessíveis da computação remota. Isso pode ser feito por meio do carregamento dos dados no repositório de armazenamento.

Aqui está um exemplo de como `datastore`usar:

```python
    import pandas as pd
    from sklearn import datasets

    data_train = datasets.load_digits()

    pd.DataFrame(data_train.data[100:,:]).to_csv("data/X_train.csv", index=False)
    pd.DataFrame(data_train.target[100:]).to_csv("data/y_train.csv", index=False)

    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)
```

### <a name="define-dprep-references"></a>Definir referências de dprep

Defina X e y como referência de dprep, que será passado para o objeto de `AutoMLConfig` aprendizado de máquina automatizado semelhante ao seguinte:

```python

    X = dprep.auto_read_file(path=ds.path('digitsdata/X_train.csv'))
    y = dprep.auto_read_file(path=ds.path('digitsdata/y_train.csv'))


    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 path = project_folder,
                                 run_configuration=conda_run_config,
                                 X = X,
                                 y = y,
                                 **automl_settings
                                )
```

## <a name="train-and-validation-data"></a>Educar e a validação de dados

Você pode especificar o treinamento separado e a validação definidos diretamente `AutoMLConfig` no método.

### <a name="k-folds-cross-validation"></a>K subconjuntos de validação cruzada

Utilize `n_cross_validations` definição para especificar o número de cruzada validações. O conjunto de dados de treinamento será dividido aleatoriamente em `n_cross_validations` subconjuntos de tamanho igual. Durante cada validação cruzada redondo, um dos subconjuntos será utilizado para a validação do modelo com base em com os subconjuntos restantes. Este processo repete-se para `n_cross_validations` Arredonda por excesso até que cada subconjuntos de validação é utilizado uma vez como conjunto de validação. As pontuações média em todos os `n_cross_validations` rodadas serão reportadas e o modelo correspondente será reestruturar os em todo o conjunto de dados de preparação.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Validação cruzada Monte Carlo (subamostra aleatória repetida)

Utilizar `validation_size` para especificar a percentagem do conjunto de dados de treinamento que deve ser utilizado para validação e use `n_cross_validations` para especificar o número de cruzada validações. Durante cada uma validação round, um subconjunto de tamanho de cruzada `validation_size` serão selecionados aleatoriamente para a validação do modelo com base em com os dados restantes. Por fim, a média pontua em todos os `n_cross_validations` rodadas serão reportadas e o modelo correspondente será reestruturar os em todo o conjunto de dados de preparação. Monte Carlo não tem suporte para previsão de série temporal.

### <a name="custom-validation-dataset"></a>Conjunto de dados de validação personalizada

Use o conjunto de dados de validação personalizada se a divisão aleatória não for aceitável, geralmente de data de série temporal ou dados desequilibrados. Pode especificar o seu próprio conjunto de dados de validação. O modelo será avaliado contra o conjunto de dados de validação especificado, em vez de um conjunto de dados aleatório.

## <a name="compute-to-run-experiment"></a>Computação para executar a experimentação

Em seguida, determine onde será preparado o modelo. Uma experimentação de preparação de aprendizagem automática, pode executar as seguintes opções de computação:
*   Seu computador local, como uma área de trabalho local ou computador portátil – em geral, quando tem pequeno conjunto de dados e estiver na fase de exploração.
*   Um computador remoto na cloud – [Machine Learning geridos de computação no Azure](concept-compute-target.md#amlcompute) é um serviço gerido que permite a capacidade preparar modelos de machine learning em clusters de máquinas virtuais do Azure.

Consulte o [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) para ver os notebooks de exemplo com destinos de computação locais e remotos.

*   Um cluster Azure Databricks na sua assinatura do Azure. Você pode encontrar mais detalhes aqui- [configurar Azure Databricks cluster para o ml automatizado](how-to-configure-environment.md#azure-databricks)

Consulte o [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) para ver os notebooks de exemplo com Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configurar as definições de experimentação

Existem várias opções que pode utilizar para configurar seu automatizada experimentação do machine learning. Esses parâmetros são definidos pela Instanciação de um `AutoMLConfig` objeto. Consulte a [classe AutoMLConfig](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) para obter uma lista completa de parâmetros.

Alguns exemplos incluem:

1.  Experimentação de classificação usando AUC ponderada como a métrica primária com tempo máximo de 12 000 segundos por iteração, com a experimentação para terminar depois de 50 iterações e 2 subconjuntos de validação cruzada.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        blacklist_models='XGBoostClassifier',
        X=X,
        y=y,
        n_cross_validations=2)
    ```
2.  Segue-se um exemplo de um conjunto de experimentação de regressão para terminar após 100 iterações, com cada iteração duração até 600 segundos, com 5 validação cruzada subconjuntos.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        X=X,
        y=y,
        n_cross_validations=5)
    ```

Os três valores `task` de parâmetro diferentes determinam a lista de algoritmos a serem aplicados.  Use os `whitelist` parâmetros `blacklist` ou para modificar ainda mais as iterações com os algoritmos disponíveis para incluir ou excluir. A lista de modelos com suporte pode ser encontrada na [classe SupportedAlgorithms](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py).

### <a name="primary-metric"></a>Métrica primária
A métrica primária; conforme mostrado nos exemplos acima, determina a métrica a ser usada durante o treinamento do modelo para otimização. A métrica primária que você pode selecionar é determinada pelo tipo de tarefa que você escolher. Abaixo está uma lista de métricas disponíveis.

|Classificação | Regressão | Previsão de série temporal
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-preprocessing--featurization"></a>Pré-processamento de dados & personalização

Em todos os experimentos de aprendizado de máquina automatizados, seus dados são [dimensionados e normalizados automaticamente](concept-automated-ml.md#preprocess) para ajudar os algoritmos a funcionar bem.  No entanto, você também pode habilitar o pré-processamento/personalização adicional, como valores ausentes de imputação, codificação e transformações. [Saiba mais sobre o que o personalização está incluído](how-to-create-portal-experiments.md#preprocess).

Para habilitar esse personalização, especifique `"preprocess": True` para a [ `AutoMLConfig` classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

### <a name="time-series-forecasting"></a>Previsão de série temporal
Para o tipo de tarefa previsão de série temporal, você tem parâmetros adicionais para definir.
1. time_column_name-este é um parâmetro necessário que define o nome da coluna nos dados de treinamento que contém a série de data/hora.
1. max_horizon-define o período de tempo que você deseja prever com base na periodicidade dos dados de treinamento. Por exemplo, se você tiver dados de treinamento com refinamentos diários de tempo, defina a distância em dias que deseja que o modelo treine.
1. grain_column_names-define o nome das colunas que contêm dados de série temporal individuais em seus dados de treinamento. Por exemplo, se você estiver prevendo as vendas de uma determinada marca por loja, você definirá as colunas de armazenamento e marca como suas colunas de refinamento.

Veja o exemplo dessas configurações que estão sendo usadas abaixo. o exemplo de notebook está disponível [aqui](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

```python
# Setting Store and Brand as grains for training.
grain_column_names = ['Store', 'Brand']
nseries = data.groupby(grain_column_names).ngroups

# View the number of time series data with defined grains
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'grain_column_names': grain_column_names,
    'drop_column_names': ['logQuantity'],
    'max_horizon': n_test_periods
}

automl_config = AutoMLConfig(task='forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble"></a>Configuração do Ensemble

Os modelos de Ensemble são habilitados por padrão e aparecem como as iterações de execução final em uma execução de Machine Learning automatizada. Atualmente, os métodos Ensemble com suporte são votação e empilhamento. A votação é implementada como votação suave usando médias ponderadas e a implementação de empilhamento está usando uma implementação de 2 camadas, em que a primeira camada tem os mesmos modelos que o Ensemble de votação, e o segundo modelo de camada é usado para encontrar a combinação ideal do modelos da primeira camada. Se você estiver usando modelos ONNX **ou** tiver a explicação de modelo habilitado, o empilhamento será desabilitado e somente a votação será utilizada.

Há vários argumentos padrão que podem ser fornecidos como `kwargs` em um `AutoMLConfig` objeto para alterar o comportamento padrão da pilha Ensemble.

* `stack_meta_learner_type`: o meta-aprendiz é um modelo treinado na saída dos modelos heterogêneos individuais. Os meta-aprendizs padrão `LogisticRegression` são para tarefas de classificação `LogisticRegressionCV` (ou se a validação cruzada estiver `ElasticNet` habilitada) e para tarefas de regressão `ElasticNetCV` /previsão (ou se a validação cruzada estiver habilitada). Esse parâmetro pode ser uma das seguintes cadeias de `LogisticRegression`caracteres `LogisticRegressionCV`: `LightGBMClassifier`, `ElasticNet` `ElasticNetCV` `LightGBMRegressor`,,,, `LinearRegression`ou.
* `stack_meta_learner_train_percentage`: especifica a proporção do conjunto de treinamento (ao escolher treinamento e tipo de validação) a ser reservado para treinar o meta-aprendiz. O valor padrão `0.2`é.
* `stack_meta_learner_kwargs`: parâmetros opcionais a serem passados para o inicializador do meta-aprendiz. Esses parâmetros e tipos de parâmetro espelham aqueles do construtor de modelo correspondente e são encaminhados para o construtor de modelo.

O código a seguir mostra um exemplo de especificação do comportamento Ensemble personalizado `AutoMLConfig` em um objeto.

```python
ensemble_settings = {
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        iterations=20,
        X=X_train,
        y=y_train,
        n_cross_validations=5,
        **ensemble_settings
        )
```

O treinamento do Ensemble é habilitado por padrão, mas pode ser desabilitado usando `enable_voting_ensemble` os `enable_stack_ensemble` parâmetros Boolianos e.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        iterations=20,
        X=X_train,
        y=y_train,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>Execute experimentação

Para o ml automatizado, `Experiment` você cria um objeto, que é um objeto `Workspace` nomeado em um usado para executar experimentos.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Submeta a experimentação para executar e gerar um modelo. Passar o `AutoMLConfig` para o `submit` método para gerar o modelo.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>As dependências são instaladas pela primeira vez numa máquina nova.  Pode demorar até 10 minutos antes de saída é mostrada.
>A definição `show_output` para `True` resulta na saída a ser apresentada na consola.

### <a name="exit-criteria"></a>Critérios de saída
Há algumas opções que você pode definir para concluir o experimento.
1. Sem critérios – se você não definir nenhum parâmetro de saída, o experimento continuará até que nenhum progresso adicional seja feito em sua métrica primária.
1. Número de iterações – você define o número de iterações para a execução do experimento. Você pode adicionar iteration_timeout_minutes opcionalmente para definir um limite de tempo em minutos por cada iteração.
1. Sair após um período de tempo – usando experiment_timeout_minutes em suas configurações, você pode definir por quanto tempo em minutos um experimento continuará em execução.
1. Sair depois que uma pontuação for atingida-usando experiment_exit_score, você poderá optar por concluir o experimento depois que uma pontuação baseada em sua métrica primária for atingida.

### <a name="explore-model-metrics"></a>Explorar métricas de modelo

Você pode exibir os resultados de treinamento em um widget ou embutido se estiver em um bloco de anotações. Ver [controlar e avaliar modelos](how-to-track-experiments.md#view-run-details) para obter mais detalhes.

## <a name="understand-automated-ml-models"></a>Entender os modelos de ML automatizados

Qualquer modelo produzido usando o ML automatizado inclui as seguintes etapas:
+ Engenharia automatizada de recursos (se pré-processar = true)
+ Dimensionamento/normalização e algoritmo com valores de hipermedidor

Tornamos transparente para obter essas informações da saída do fitted_model do ML automatizado.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Engenharia automatizada de recursos

Consulte a lista de pré-processamento e [engenharia de recursos automatizada](concept-automated-ml.md#preprocess) que ocorre quando pré-processamento = verdadeiro.

Considere este exemplo:
+ Há quatro recursos de entrada: A (numérico), B (numérico), C (numérico), D (DateTime)
+ O recurso numérico C é Descartado porque é uma coluna de ID com todos os valores exclusivos
+ Os recursos numéricos A e B têm valores ausentes e, portanto, são imputados por média
+ O recurso DateTime D é destacados em 11 recursos de engenharia diferentes

Use essas duas APIs na primeira etapa do modelo ajustado para entender mais.  Consulte [este bloco de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` retorna uma lista de nomes de recursos com engenharia.

  Utilização:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Essa lista inclui todos os nomes de recursos de engenharia.

  >[!Note]
  >Use ' timeseriestransformer ' para a tarefa = ' previsão ', caso contrário use ' transtransformer ' para a tarefa ' regressão ' ou ' classificação '.

+ API 2: `get_featurization_summary()` retorna o resumo de personalização para todos os recursos de entrada.

  Utilização:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Use ' timeseriestransformer ' para a tarefa = ' previsão ', caso contrário use ' transtransformer ' para a tarefa ' regressão ' ou ' classificação '.

  Saída:
  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   Em que:

   |Output|Definição|
   |----|--------|
   |RawFeatureName|Nome de recurso/coluna de entrada do conjunto de dados fornecido.|
   |TypeDetected|Tipo de dados detectado do recurso de entrada.|
   |Passou|Indica se o recurso de entrada foi descartado ou usado.|
   |EngineeringFeatureCount|Número de recursos gerados por meio de transformações automatizadas de engenharia de recursos.|
   |Transformações|Lista de transformações aplicadas aos recursos de entrada para gerar recursos de engenharia.|

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>Dimensionamento/normalização e algoritmo com valores de hipermedidor:

Para entender os valores de dimensionamento/normalização e de algoritmo/hiperparâmetro para um pipeline, use fitted_model. Steps. [Saiba mais sobre dimensionamento/normalização](concept-automated-ml.md#preprocess). Eis uma saída de exemplo:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Para obter mais detalhes, use essa função auxiliar mostrada neste [bloco de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

```python
from pprint import pprint


def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()


print_model(fitted_model)
```

Veja a seguir um exemplo de saída para um pipeline usando um algoritmo específico (LogisticRegression com RobustScalar, neste caso).

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

<a name="explain"></a>

## <a name="explain-the-model-interpretability"></a>Explicar o modelo (interpretabilidade)

Aprendizagem automática permite-lhe compreender a importância de funcionalidade.  Durante o processo de treinamento, pode obter a importância de recurso global para o modelo.  Para cenários de classificação, também pode obter a importância de recurso de nível de classe.  Tem de fornecer um conjunto de dados de validação (X_valid) para obter a importância de funcionalidade.

Existem duas formas de gerar a importância de funcionalidade.

*   Depois de uma experimentação estiver concluída, pode usar `explain_model` método em qualquer iteração.

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        explain_model(fitted_model, X_train, X_test)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

*   Para ver a importância de funcionalidade para todas as iterações, defina `model_explainability` sinalizador para `True` no AutoMLConfig.

    ```python
    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 primary_metric = 'AUC_weighted',
                                 max_time_sec = 12000,
                                 iterations = 10,
                                 verbosity = logging.INFO,
                                 X = X_train,
                                 y = y_train,
                                 X_valid = X_test,
                                 y_valid = y_test,
                                 model_explainability=True,
                                 path=project_folder)
    ```

    Quando tiver terminado, pode utilizar o método retrieve_model_explanation para recuperar a importância de funcionalidade para uma iteração específica.

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        retrieve_model_explanation(best_run)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

É possível visualizar o gráfico de importância de funcionalidade na sua área de trabalho no portal do Azure. Exiba a URL usando o objeto de execução:

```
automl_run.get_portal_url()
```

É possível visualizar o gráfico de importância de funcionalidade na sua área de trabalho no portal do Azure. O gráfico também é mostrado ao usar o `RunDetails` [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) em um bloco de anotações. Para saber mais sobre os gráficos, consulte [entender os resultados automatizados do Machine Learning](how-to-understand-automated-ml.md).

```Python
from azureml.widgets import RunDetails
RunDetails(automl_run).show()
```

![gráfico de importância de funcionalidade](./media/how-to-configure-auto-train/feature-importance.png)

Para obter mais informações sobre como as explicações de modelo e a importância do recurso podem ser habilitadas em outras áreas do SDK fora do aprendizado de máquina automatizado, consulte o artigo [conceito](machine-learning-interpretability-explainability.md) sobre interpretação.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [como e onde implementar um modelo](how-to-deploy-and-where.md).

Saiba mais sobre [como treinar um modelo de regressão com o aprendizado de máquina automatizado](tutorial-auto-train-models.md) ou [como treinar usando o aprendizado de máquina automatizado em um recurso remoto](how-to-auto-train-remote.md).
