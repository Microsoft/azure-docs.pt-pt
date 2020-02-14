---
title: Criar experimentos de ML automatizados
titleSuffix: Azure Machine Learning
description: Aprendizagem automática escolhe um algoritmo para e gera um modelo pronto para implantação. Saiba as opções que pode utilizar para configurar automatizada experimentações de machine learning.
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: b7f837c56214d2d01d0f119e0107a095bcfd782b
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198774"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurar experimentos de ML automatizados em Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste guia, aprenda a definir várias configurações de configuração das suas experiências automatizadas de aprendizagem automática com o [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Aprendizagem automática escolhe um algoritmo e hiperparâmetros para e gera um modelo pronto para implantação. Existem várias opções que pode utilizar para configurar automatizada experimentações de machine learning.

Para ver exemplos de experiências automatizadas de aprendizagem automática de máquinas, consulte [Tutorial: Treine um modelo](tutorial-auto-train-models.md) de classificação com machine learning automatizado ou [modelos de comboio com machine learning automatizado na nuvem](how-to-auto-train-remote.md).

Opções de configuração disponíveis no automatizada de machine learning:

* Selecione o seu tipo de experiência: Classificação, Regressão ou Previsão da Série de Tempo
* Origem de dados, formatos e obtenção de dados
* Escolher o destino de computação: local ou remoto
* Aprendizagem automatizada as definições de experimentação
* Executar uma automatizada experimentação do machine learning
* Explorar métricas de modelo
* Registe e implemente o modelo

Se preferir uma experiência sem código, também pode [criar as suas experiências automatizadas](how-to-create-portal-experiments.md)de aprendizagem automática em estúdio de Machine Learning.

## <a name="select-your-experiment-type"></a>Selecione o tipo de experimentação

Antes de começar a sua experimentação, deve determinar o tipo de problema de aprendizado de máquina que está a resolver. O machine learning automatizado suporta tipos de tarefas de classificação, regressão e previsão. Saiba mais sobre os tipos de [tarefas.](how-to-define-task-type.md)

Automatizada de machine learning suporta os seguintes algoritmos durante a automação e o processo de otimização. Como um utilizador, não é necessário para especificar o algoritmo.

Classificação | Regressão | Previsão de séries temporais
|-- |-- |--
[Regressão Logística](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Rede Elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Rede Elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Gbm leve](https://lightgbm.readthedocs.io/en/latest/index.html)|[Gbm leve](https://lightgbm.readthedocs.io/en/latest/index.html)|[Gbm leve](https://lightgbm.readthedocs.io/en/latest/index.html)
[Reforço do Gradiente](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Reforço do Gradiente](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Reforço do Gradiente](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Árvore de Decisão](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Árvore de Decisão](https://scikit-learn.org/stable/modules/tree.html#regression)|[Árvore de Decisão](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Vizinhos Mais Próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Vizinhos Mais Próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Vizinhos Mais Próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[Laço LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[Laço LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Classificação do vetor de suporte (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Descida do Gradiente Estocástico (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Descida do Gradiente Estocástico (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Floresta Aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Floresta Aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Floresta Aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classe DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Classificador Linear DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regresso linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regresso linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Baías Ingénuas](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[Regresso linear rápido](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Descida do Gradiente Estocástico (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[Gradiente Declive Online Regressor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Profeta](https://facebook.github.io/prophet/docs/quick_start.html)
|[Perceptron Classifier médio](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||PrevisãoCN
|[Classe Linear SVM](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)||

Utilize o parâmetro `task` no construtor `AutoMLConfig` para especificar o seu tipo de experiência.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Origem de dados e de formato

Aprendizagem automática suporta os dados que residem no ambiente de trabalho local ou na cloud, como o armazenamento de Blobs do Azure. Os dados podem ser lidos num **DataFrame pandas** ou num Conjunto de Dados de **Aprendizagem automática Azure .**  [Saiba mais sobre conjuntos](how-to-create-register-datasets.md)de dados .

Requisitos para os dados de formação:
- Os dados devem estar na forma tabular.
- O valor a prever, coluna de destino, deve estar nos dados.

Os exemplos de código a seguir demonstram como armazenar os dados nesses formatos.

* TabularDataset
  ```python
  from azureml.core.dataset import Dataset
  from azureml.opendatasets import Diabetes
  
  tabular_dataset = Diabetes.get_tabular_dataset()
  train_dataset, test_dataset = tabular_dataset.random_split(percentage=0.1, seed=42)
  label = "Y"
  ```

* Pandas dataframe

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split

    df = pd.read_csv("your-local-file.csv")
    train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
    label = "label-col-name"
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Obter dados para executar a experimentação em computação remota

Para execuções remotas, os dados de treinamento devem ser acessíveis a partir da computação remota. A [classe`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) no SDK expõe a funcionalidade a:

* Transfira facilmente dados de arquivos estáticos ou de fontes de URL para seu espaço de trabalho
* disponibilizar seus dados para scripts de treinamento ao executar recursos de computação em nuvem

Veja o [como](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) usar a classe `Dataset` para montar dados para o seu alvo de cálculo.

## <a name="train-and-validation-data"></a>Educar e a validação de dados

Pode especificar conjuntos separados de comboios e validação diretamente no `AutoMLConfig` construtor.

### <a name="k-folds-cross-validation"></a>K subconjuntos de validação cruzada

Utilize `n_cross_validations` definição para especificar o número de validações cruzadas. O conjunto de dados de treino será dividido aleatoriamente em `n_cross_validations` dobras de tamanho igual. Durante cada validação cruzada redondo, um dos subconjuntos será utilizado para a validação do modelo com base em com os subconjuntos restantes. Este processo repete-se durante `n_cross_validations` rondas até que cada dobra seja usada uma vez como conjunto de validação. As pontuações médias em todas as `n_cross_validations` rondas serão reportadas, e o modelo correspondente será retreinado em todo o conjunto de dados de treino.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Validação cruzada Monte Carlo (subamostra aleatória repetida)

Utilize `validation_size` para especificar a percentagem do conjunto de dados de formação que deve ser utilizado para validação e utilize `n_cross_validations` para especificar o número de validações cruzadas. Durante cada ronda de validação cruzada, um subconjunto de tamanho `validation_size` será selecionado aleatoriamente para validação do modelo treinado nos restantes dados. Finalmente, as pontuações médias em todas as `n_cross_validations` rondas serão reportadas, e o modelo correspondente será retreinado em todo o conjunto de dados de treino. Monte Carlo não tem suporte para previsão de série temporal.

### <a name="custom-validation-dataset"></a>Conjunto de dados de validação personalizada

Use o conjunto de dados de validação personalizada se a divisão aleatória não for aceitável, geralmente de data de série temporal ou dados desequilibrados. Pode especificar o seu próprio conjunto de dados de validação. O modelo será avaliado contra o conjunto de dados de validação especificado, em vez de um conjunto de dados aleatório.

## <a name="compute-to-run-experiment"></a>Computação para executar a experimentação

Em seguida, determine onde será preparado o modelo. Uma experimentação de preparação de aprendizagem automática, pode executar as seguintes opções de computação:
*   Seu computador local, como uma área de trabalho local ou computador portátil – em geral, quando tem pequeno conjunto de dados e estiver na fase de exploração.
*   Uma máquina remota na nuvem – [Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute) é um serviço gerido que permite a capacidade de treinar modelos de aprendizagem automática em clusters de máquinas virtuais Azure.

    Consulte este [site gitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) por exemplo de cadernos com alvos de computação local e remota.

*   Um cluster Azure Databricks na sua assinatura do Azure. Pode encontrar mais detalhes aqui - [Configurar cluster de databricks Azure para ML automatizado](how-to-configure-environment.md#azure-databricks)

    Consulte este [site gitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) por exemplo de cadernos com Tijolos de Dados Azure.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configurar as definições de experimentação

Existem várias opções que pode utilizar para configurar seu automatizada experimentação do machine learning. Estes parâmetros são definidos instantaneamente um objeto `AutoMLConfig`. Consulte a [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) para obter uma lista completa de parâmetros.

Alguns exemplos incluem:

1.  Teste de classificação usando AUC ponderado como a métrica primária com minutos de tempo limite de experimento definido como 30 minutos e 2 dobras de validação cruzada.

    ```python
    automl_classifier=AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        blacklist_models='XGBoostClassifier',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=2)
    ```
2.  Abaixo está um exemplo de uma experiência de regressão definida para terminar após 60 minutos com cinco dobras cruzadas de validação.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        experiment_timeout_minutes=60,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5)
    ```

Os três diferentes valores de parâmetro sétero `task` (o terceiro tipo de tarefa é `forecasting`, e usa um conjunto de algoritmos semelhante si `regression` tarefas) determinam a lista de modelos a aplicar. Utilize os parâmetros `whitelist` ou `blacklist` para modificar ainda mais as iterações com os modelos disponíveis para incluir ou excluir. A lista de modelos suportados pode ser encontrada na [Classe SupportEdModels](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) para ([Classificação,](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification) [Previsão](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)e [Regressão).](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)

O serivce de validação automatizado do ML exigirá que `experiment_timeout_minutes` seja fixado para um tempo mínimo de 15 minutos, a fim de ajudar a evitar falhas no tempo de tempo.

### <a name="primary-metric"></a>Métrica primária
A métrica primária determina a métrica a ser usada durante o treinamento do modelo para otimização. As métricas disponíveis que você pode selecionar são determinadas pelo tipo de tarefa que você escolher e a tabela a seguir mostra métricas primárias válidas para cada tipo de tarefa.

|Classificação | Regressão | Previsão de séries temporais
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Conheça as definições específicas destas métricas em [Compreender os resultados automatizados de aprendizagem automática.](how-to-understand-automated-ml.md)

### <a name="data-featurization"></a>Característica de dados

Em todas as experiências automatizadas de aprendizagem automática de máquinas, os seus dados são [automaticamente dimensionados e normalizados](concept-automated-ml.md#preprocess) para ajudar *certos* algoritmos sensíveis a funcionalidades que estão em escalas diferentes.  No entanto, também pode permitir uma funcionalidade adicional, como a imputação de valores em falta, codificação e transformações. [Saiba mais sobre o que está incluído.](how-to-create-portal-experiments.md#featurization)

Ao configurar as suas experiências, pode ativar a configuração avançada `featurization`. A tabela seguinte mostra as configurações aceites para a caracterização na [classe`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

|Configuração de Características | Descrição |
| ------------- | ------------- |
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Indica que deve ser utilizado um passo de caracterização personalizado. [Aprenda a personalizar a caracterização.](how-to-configure-auto-train.md#customize-feature-engineering)|
|`"featurization": 'off'`| Indica que o passo de caracterização não deve ser feito automaticamente.|
|`"featurization": 'auto'`| Indica que, como parte do pré-processamento, os [guarda-costas de dados e os passos](how-to-create-portal-experiments.md#advanced-featurization-options) de caracterização são executados automaticamente.|

> [!NOTE]
> Os passos automatizados de funcionalidade de aprendizagem automática (normalização de recursos, manuseamento de dados em falta, conversão de texto em numérico, etc.) tornam-se parte do modelo subjacente. Ao utilizar o modelo para previsões, os mesmos passos de caracterização aplicados durante o treino são aplicados automaticamente aos seus dados de entrada.

### <a name="time-series-forecasting"></a>Previsão de séries temporais
A série de tempo `forecasting` tarefa requer parâmetros adicionais no objeto de configuração:

1. `time_column_name`: Parâmetro necessário que define o nome da coluna nos seus dados de treino contendo uma série de tempo válida.
1. `max_horizon`: Define o tempo que pretende prever com base na periodicidade dos dados de formação. Por exemplo, se você tiver dados de treinamento com refinamentos diários de tempo, defina a distância em dias que deseja que o modelo treine.
1. `grain_column_names`: Define o nome das colunas que contêm dados individuais de séries temporais nos seus dados de formação. Por exemplo, se você estiver prevendo as vendas de uma determinada marca por loja, você definirá as colunas de armazenamento e marca como suas colunas de refinamento. As diversas séries temporais e as previsões serão criadas para cada granulação/agrupamento. 

Para exemplo das definições utilizadas abaixo, consulte o [caderno da amostra](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

automl_config = AutoMLConfig(task = 'forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=20,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble"></a>Configuração do conjunto

Os modelos de Ensemble são habilitados por padrão e aparecem como as iterações de execução final em uma execução de Machine Learning automatizada. Atualmente, os métodos Ensemble com suporte são votação e empilhamento. A votação é implementada como voto suave usando médias ponderadas, e a implementação do empilhamento está usando uma implementação de duas camadas, onde a primeira camada tem os mesmos modelos que o conjunto de votação, e o modelo de segunda camada é usado para encontrar a combinação ideal de os modelos da primeira camada. Se estiver a utilizar modelos ONNX **ou** tiver uma explicabilidade de modelo, o empilhamento será desativado e apenas o voto será utilizado.

Existem múltiplos argumentos predefinidos que podem ser fornecidos como `kwargs` num objeto `AutoMLConfig` para alterar o comportamento padrão do conjunto de pilhas.

* `stack_meta_learner_type`: o meta-aprendiz é um modelo treinado na saída dos modelos heterogéneos individuais. Os meta-aprendizes predefinidos são `LogisticRegression` para tarefas de classificação (ou `LogisticRegressionCV` se a validação cruzada estiver ativada) e `ElasticNet` para tarefas de regressão/previsão (ou `ElasticNetCV` se a validação cruzada estiver ativada). Este parâmetro pode ser uma das seguintes cordas: `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor`ou `LinearRegression`.
* `stack_meta_learner_train_percentage`: especifica a proporção do conjunto de formação (ao escolher o tipo de treino de comboio e validação) a reservar para a formação do meta-aprendiz. O valor predefinido é `0.2`.
* `stack_meta_learner_kwargs`: parâmetros opcionais para passar para o inicializador do meta-aprendiz. Estes parâmetros e parâmetros espelham os parâmetros e os tipos de parâmetros do construtor de modelos correspondentes, e são encaminhados para o construtor do modelo.

O código que se segue mostra um exemplo de especificar o comportamento do conjunto personalizado num objeto `AutoMLConfig`.

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
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

O treino do conjunto é ativado por padrão, mas pode ser desativado utilizando os parâmetros `enable_voting_ensemble` e `enable_stack_ensemble` boolean.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>Execute experimentação

Para ml automatizado, cria-se um objeto `Experiment`, que é um objeto nomeado numa `Workspace` usada para executar experiências.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Submeta a experimentação para executar e gerar um modelo. Passe a `AutoMLConfig` para o método `submit` para gerar o modelo.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>As dependências são instaladas pela primeira vez numa máquina nova.  Pode demorar até 10 minutos antes de saída é mostrada.
>A definição `show_output` para `True` resulta na exibição da saída na consola.

### <a name="exit-criteria"></a>Critérios de saída
Há algumas opções que você pode definir para encerrar seu experimento.
1. Nenhum critério: se você não definir nenhum parâmetro de saída, o experimento continuará até que nenhum progresso adicional seja feito em sua métrica primária.
1. Saída após um período de tempo: Utilizar `experiment_timeout_minutes` nas suas definições permite definir quanto tempo em minutos deve uma experiência continuar em execução.
1. Saída após uma pontuação alcançada: Usando `experiment_exit_score` completará a experiência depois de ter sido atingida uma pontuação métrica primária.

### <a name="explore-model-metrics"></a>Explorar métricas de modelo

Você pode exibir os resultados de treinamento em um widget ou embutido se estiver em um bloco de anotações. Consulte [o Track e avalie os modelos](how-to-track-experiments.md#view-run-details) para obter mais detalhes.

## <a name="understand-automated-ml-models"></a>Entender os modelos de ML automatizados

Qualquer modelo produzido usando o ML automatizado inclui as seguintes etapas:
+ Engenharia automática de recursos (se `"featurization": 'auto'`)
+ Dimensionamento/normalização e algoritmo com valores de hiperparâmetro

Tornamos transparente obter esta informação a partir da fitted_model saída a partir de ML automatizado.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Engenharia automatizada de recursos

Consulte a lista de pré-processamento e [engenharia automática](concept-automated-ml.md#preprocess) de recursos que acontece quando `"featurization": 'auto'`.

Considere este exemplo:
+ Existem quatro funcionalidades de entrada: A (Numérico), B (Numérico), C (Numérico), D (DataTime)
+ O recurso numérico C é Descartado porque é uma coluna de ID com todos os valores exclusivos
+ Os recursos numéricos A e B têm valores ausentes e, portanto, são imputadosdos pela média
+ O recurso DateTime D é destacados em 11 recursos de engenharia diferentes

Use essas duas APIs na primeira etapa do modelo ajustado para entender mais.  Consulte [este caderno de amostras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` devolve uma lista de nomes de funcionalidades projetados.

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

+ API 2: `get_featurization_summary()` retorna resumo de características para todas as funcionalidades de entrada.

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

   |Saída|Definição|
   |----|--------|
   |RawFeatureName|Nome de recurso/coluna de entrada do conjunto de dados fornecido.|
   |TypeDetected|Tipo de dados detectado do recurso de entrada.|
   |Passou|Indica se o recurso de entrada foi descartado ou usado.|
   |EngineeringFeatureCount|Número de recursos gerados por meio de transformações automatizadas de engenharia de recursos.|
   |Transformações|Lista de transformações aplicadas aos recursos de entrada para gerar recursos de engenharia.|
   
### <a name="customize-feature-engineering"></a>Personalizar a engenharia de recursos
Para personalizar a engenharia de funcionalidades, especifique `"featurization": FeaturizationConfig`.

A personalização com suporte inclui:

|Personalização|Definição|
|--|--|
|Atualização de finalidade de coluna|Substituir o tipo de recurso para a coluna especificada.|
|Atualização de parâmetro do transformador |Atualize os parâmetros para o transformador especificado. Atualmente, dá suporte a imputer (média & mediana, mais frequente) e HashOneHotEncoder.|
|Remover colunas |As colunas a serem descartadas são destacados.|
|Bloquear transformadores| Bloqueie os transformadores a serem usados no processo personalização.|

Crie o objeto FeaturizationConfig usando chamadas à API:
```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Dimensionamento/normalização e algoritmo com valores de hiperparâmetro:

Para compreender os valores de escala/normalização e algoritmo/hiperparâmetro para um gasoduto, utilize fitted_model.passos. [Saiba mais sobre escala/normalização](concept-automated-ml.md#preprocess). Eis uma saída de exemplo:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Para obter mais detalhes, utilize esta função de ajudante mostrada [neste caderno de amostras](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

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

A seguinte saída de amostra é para um pipeline usando um algoritmo específico (LogisticRegression with RobustScalar, neste caso).

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

### <a name="predict-class-probability"></a>Prever a probabilidade da classe

Os modelos produzidos com ML automatizado têm todos objetos de invólucro que espelham a funcionalidade da sua classe de origem de código aberto. A maioria dos objetos de invólucro de modelo de classificação devolvidos por ML automatizado implementam a função `predict_proba()`, que aceita uma amostra de dados de matriz matriz matriz esparsa ou escassa das suas características (valores X), e devolve uma matriz n-dimensional de cada amostra e a sua respetiva probabilidade de classe.

Assumindo que recuperou o melhor modelo de execução e equipado utilizando as mesmas chamadas de cima, pode ligar `predict_proba()` diretamente do modelo equipado, fornecendo uma amostra `X_test` no formato apropriado, dependendo do tipo de modelo.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Se o modelo subjacente não suportar a função `predict_proba()` ou o formato estiver incorreto, será lançada uma exceção específica para a classe do modelo. Consulte os docs de referência [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) e [XGBoost,](https://xgboost.readthedocs.io/en/latest/python/python_api.html) por exemplo, de como esta função é implementada para diferentes tipos de modelos.

<a name="explain"></a>

## <a name="model-interpretability"></a>Capacidade de interpretação do modelo

A interpretação de modelo permite que você entenda por que seus modelos fizeram previsões e os valores subjacentes de importância do recurso. O SDK inclui vários pacotes para habilitar recursos de interpretação de modelo, tanto no tempo de inferência quanto no treinamento, para modelos locais e implantados.

Veja como obter amostras [de](how-to-machine-learning-interpretability-automl.md) código sobre como ativar funcionalidades de interpretação especificamente dentro de experiências automatizadas de aprendizagem automática de máquinas.

Para obter informações gerais sobre como as explicações do modelo e a importância da funcionalidade podem ser ativadas noutras áreas do SDK fora da aprendizagem automática de máquinas, consulte [o](how-to-machine-learning-interpretability.md) artigo conceptual sobre a interpretação.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [como e onde implementar um modelo.](how-to-deploy-and-where.md)

Saiba mais sobre como treinar um modelo de [regressão com machine learning automatizado](tutorial-auto-train-models.md) ou como treinar utilizando machine learning automatizado num recurso [remoto](how-to-auto-train-remote.md).
