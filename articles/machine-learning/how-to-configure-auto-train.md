---
title: Criar experimentações de ML automatizado
titleSuffix: Azure Machine Learning
description: Machine learning automatizado escolhe um algoritmo para si e gera um modelo pronto para ser implantado. Aprenda as opções que pode utilizar para configurar experiências automatizadas de aprendizagem automática.
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: 11f52df683b7a85a8ad5163bbede33d786897452
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257271"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurar experimentações do ML automatizado no Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste guia, aprenda a definir várias configurações de configuração das suas experiências automatizadas de aprendizagem automática com o [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). O machine learning automatizado escolhe um algoritmo e hiperparâmetros para si e gera um modelo pronto para ser implantado. Existem várias opções que pode utilizar para configurar experiências automatizadas de aprendizagem automática de máquinas.

Para ver exemplos de experiências automatizadas de aprendizagem automática de máquinas, consulte [Tutorial: Treine um modelo](tutorial-auto-train-models.md) de classificação com machine learning automatizado ou [modelos de comboio com machine learning automatizado na nuvem](how-to-auto-train-remote.md).

Opções de configuração disponíveis em machine learning automatizado:

* Selecione o seu tipo de experiência: Classificação, Regressão ou Previsão da Série de Tempo
* Fonte de dados, formatos e recolha de dados
* Escolha o seu alvo computacional: local ou remoto
* Configurações automatizadas de experiências de aprendizagem automática de máquinas
* Executar uma experiência automatizada de aprendizagem automática de máquinas
* Explore as métricas do modelo
* Registar e implementar modelo

Se preferir uma experiência sem código, também pode [criar as suas experiências automatizadas](how-to-use-automated-ml-for-ml-models.md)de aprendizagem automática em estúdio de Machine Learning.

## <a name="select-your-experiment-type"></a>Selecionar o tipo de experimentação

Antes de começar a sua experiência, deve determinar o tipo de problema de aprendizagem automática que está a resolver. O machine learning automatizado suporta tipos de tarefas de classificação, regressão e previsão. Saiba mais sobre os tipos de [tarefas.](how-to-define-task-type.md)

A aprendizagem automática de máquinas suporta os seguintes algoritmos durante o processo de automação e afinação. Como utilizador, não há necessidade de especificar o algoritmo.

> [!NOTE]
> Se planeia exportar os seus modelos auto ML criados para um [modelo ONNX](concept-onnx.md), apenas os algoritmos indicados com um * são capazes de ser convertidos para o formato ONNX. Saiba mais sobre [a conversão de modelos para ONNX](concept-automated-ml.md#use-with-onnx). <br> <br> Note ainda que o ONNX apenas suporta tarefas de classificação e regressão neste momento. 

Classificação | Regressão | Previsão de Série Temporal
|-- |-- |--
[Regressão Logística](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Rede Elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Gbm leve](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Gbm leve](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Gbm leve](https://lightgbm.readthedocs.io/en/latest/index.html)
[Reforço do Gradiente](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Reforço do Gradiente](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Reforço do Gradiente](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Árvore de Decisão](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Árvore de Decisão](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Árvore de Decisão](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Vizinhos Mais Próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K Vizinhos Mais Próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K Vizinhos Mais Próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Laço LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Classificação do vetor de suporte (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Descida do Gradiente Estocástico (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[Descida do Gradiente Estocástico (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Floresta Aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Floresta Aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Floresta Aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classe DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier) |[DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Classificador Linear DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regresso linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor) |[Regresso linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Baías Ingénuas](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Regresso linear rápido](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Descida do Gradiente Estocástico (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* |[Gradiente Declive Online Regressor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Profeta](https://facebook.github.io/prophet/docs/quick_start.html)
|[Perceptron Classifier médio](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||PrevisãoCN
|[Classe Linear SVM](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)* ||

Utilize `task` o parâmetro `AutoMLConfig` no construtor para especificar o seu tipo de experiência.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Fonte e formato de dados

O machine learning automatizado suporta dados que residem no seu ambiente de trabalho local ou na nuvem, como o Armazenamento De Blob Azure. Os dados podem ser lidos num **DataFrame pandas** ou num Conjunto de Dados de **Aprendizagem automática Azure .**  [Saiba mais sobre conjuntos](how-to-create-register-datasets.md)de dados .

Requisitos para os dados de formação:
- Os dados devem estar na forma tabular.
- O valor a prever, coluna de alvo, deve estar nos dados.

Os seguintes exemplos de código demonstram como armazenar os dados nestes formatos.

* Conjunto de Dados Tabular

  ```python
  from azureml.core.dataset import Dataset
  from azureml.opendatasets import Diabetes
  
  tabular_dataset = Diabetes.get_tabular_dataset()
  train_dataset, test_dataset = tabular_dataset.random_split(percentage=0.1, seed=42)
  label = "Y"
  ```

* Quadro de dados pandas

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Recolha dados para executar experiência em computação remota

Para execuções remotas, os dados de treino devem ser acessíveis a partir da computação remota. A [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) classe no SDK expõe a funcionalidade a:

* transferir facilmente dados de ficheiros estáticos ou fontes de URL para o seu espaço de trabalho
* disponibilizar os seus dados para treinar scripts ao executar em recursos de computação em nuvem

Veja o [como](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) usar a `Dataset` classe para montar dados para o seu alvo de cálculo.

## <a name="train-and-validation-data"></a>Dados de comboio e validação

Pode especificar conjuntos separados de comboios e validação diretamente no `AutoMLConfig` construtor.

### <a name="k-folds-cross-validation"></a>Validação Cruzada K-Folds

Utilize `n_cross_validations` a definição para especificar o número de validações cruzadas. O conjunto de dados de `n_cross_validations` treino será dividido aleatoriamente em dobras de tamanho igual. Durante cada ronda de validação cruzada, uma das dobras será utilizada para validação do modelo treinado nas restantes dobras. Este processo repete-se para `n_cross_validations` rondas até que cada dobra seja usada uma vez como conjunto de validação. As pontuações `n_cross_validations` médias em todas as rondas serão reportadas, e o modelo correspondente será retreinado em todo o conjunto de dados de treino.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Validação cruzada de Monte Carlo (Sub-amostragem aleatória repetida)

Utilize `validation_size` para especificar a percentagem do conjunto de dados `n_cross_validations` de formação que deve ser utilizado para validação e utilizar para especificar o número de validações cruzadas. Durante cada ronda de validação `validation_size` cruzada, um subconjunto de tamanho será selecionado aleatoriamente para validação do modelo treinado nos restantes dados. Finalmente, as pontuações `n_cross_validations` médias em todas as rondas serão reportadas, e o modelo correspondente será retreinado em todo o conjunto de dados de treino. Monte Carlo não é apoiado para a previsão de séries temporais.

### <a name="custom-validation-dataset"></a>Conjunto de dados de validação personalizada

Utilize um conjunto de dados de validação personalizado se a divisão aleatória não for aceitável, normalmente dados de séries de tempo ou dados desequilibrados. Pode especificar o seu próprio conjunto de dados de validação. O modelo será avaliado em função do conjunto de dados de validação especificado em vez de conjunto de dados aleatório.

## <a name="compute-to-run-experiment"></a>Computação para executar a experimentação

Em seguida, determine onde o modelo será treinado. Uma experiência automatizada de formação em machine learning pode ser executada nas seguintes opções de cálculo:
* A sua máquina local, como um ambiente de trabalho local ou portátil – Geralmente quando se tem um pequeno conjunto de dados e ainda se encontra em fase de exploração.
* Uma máquina remota na nuvem – [Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute) é um serviço gerido que permite a capacidade de treinar modelos de aprendizagem automática em clusters de máquinas virtuais Azure.

  Consulte este [site gitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) por exemplo de cadernos com alvos de computação local e remota.

* Um cluster Azure Databricks na sua subscrição Azure. Pode encontrar mais detalhes aqui - [Configurar cluster de databricks Azure para ML automatizado](how-to-configure-environment.md#azure-databricks)

  Consulte este [site gitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) por exemplo de cadernos com Tijolos de Dados Azure.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configure as definições da sua experiência

Existem várias opções que pode utilizar para configurar a sua experiência automatizada de aprendizagem automática de máquinas. Estes parâmetros são definidos instantaneamente um `AutoMLConfig` objeto. Consulte a [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) para obter uma lista completa de parâmetros.

Alguns exemplos incluem:

1. Experiência de classificação utilizando AUC ponderada como a métrica primária com intervalos de tempo de experimentação definidos para 30 minutos e 2 dobras de validação cruzada.

   ```python
       automl_classifier=AutoMLConfig(
       task='classification',
       primary_metric='AUC_weighted',
       experiment_timeout_minutes=30,
       blacklist_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
2. Abaixo está um exemplo de uma experiência de regressão definida para terminar após 60 minutos com cinco dobras cruzadas de validação.

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      whitelist_models=['KNN'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```

Os três `task` valores diferentes do parâmetro `forecasting`(o terceiro tipo `regression` de tarefa é , e usa um conjunto de algoritmos semelhante si semelhante como tarefas) determinam a lista de modelos a aplicar. Utilize `whitelist` os `blacklist` ou parâmetros para modificar ainda mais as iterações com os modelos disponíveis para incluir ou excluir. A lista de modelos suportados pode ser encontrada na [Classe SupportEdModels](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) para ([Classificação,](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification) [Previsão](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)e [Regressão).](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)

Para ajudar a evitar falhas no tempo limite da `experiment_timeout_minutes` experiência, o serviço de validação automatizada ml exigirá que seja definido para um mínimo de 15 minutos, ou 60 minutos se a sua linha por tamanho da coluna exceder 10 milhões.

### <a name="primary-metric"></a>Métrica Primária
A métrica primária determina a métrica a ser usada durante o treino do modelo para otimização. As métricas disponíveis que pode selecionar são determinadas pelo tipo de tarefa que escolher, e a tabela seguinte mostra métricas primárias válidas para cada tipo de tarefa.

|Classificação | Regressão | Previsão de Série Temporal
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Conheça as definições específicas destas métricas em [Compreender os resultados automatizados de aprendizagem automática.](how-to-understand-automated-ml.md)

### <a name="data-featurization"></a>Característica de dados

Em todas as experiências automatizadas de aprendizagem automática de máquinas, os seus dados são [automaticamente dimensionados e normalizados](concept-automated-ml.md#preprocess) para ajudar *certos* algoritmos sensíveis a funcionalidades que estão em escalas diferentes.  No entanto, também pode permitir uma funcionalidade adicional, como a imputação de valores em falta, codificação e transformações. [Saiba mais sobre o que está incluído.](how-to-use-automated-ml-for-ml-models.md#featurization)

Ao configurar as suas experiências, `featurization`pode ativar a definição avançada . A tabela seguinte mostra as definições aceites para a caracterização na [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig).

|Configuração de Características | Descrição |
| ------------- | ------------- |
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Indica que deve ser utilizado um passo de caracterização personalizado. [Aprenda a personalizar a caracterização.](how-to-configure-auto-train.md#customize-feature-engineering)|
|`"featurization": 'off'`| Indica que o passo de caracterização não deve ser feito automaticamente.|
|`"featurization": 'auto'`| Indica que, como parte do pré-processamento, os [guarda-costas de dados e os passos](how-to-use-automated-ml-for-ml-models.md#advanced-featurization-options) de caracterização são executados automaticamente.|

> [!NOTE]
> Os passos automatizados de funcionalidade de aprendizagem automática (normalização de recursos, manuseamento de dados em falta, conversão de texto em numérico, etc.) tornam-se parte do modelo subjacente. Ao utilizar o modelo para previsões, os mesmos passos de caracterização aplicados durante o treino são aplicados automaticamente aos seus dados de entrada.

### <a name="time-series-forecasting"></a>Previsão de Série Temporal
A tarefa `forecasting` da série de tempo requer parâmetros adicionais no objeto de configuração:

1. `time_column_name`: Parâmetro necessário que defina o nome da coluna nos seus dados de treino contendo uma série de tempo válida.
1. `max_horizon`: Define o tempo que pretende prever com base na periodicidade dos dados de formação. Por exemplo, se tiver dados de treino com grãos de horário diário, define até que ponto em dias quer que o modelo treine.
1. `grain_column_names`: Define o nome das colunas que contêm dados individuais de séries temporais nos seus dados de formação. Por exemplo, se estiver a prever vendas de uma determinada marca por loja, definirá as colunas de loja e marca como colunas de cereais. Serão criadas séries e previsões separadas para cada grão/agrupamento. 

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

### <a name="ensemble-configuration"></a><a name="ensemble"></a>Configuração do conjunto

Os modelos do Conjunto são ativados por padrão, e aparecem como as iterações de execução final numa corrida automatizada de aprendizagem automática. Atualmente, os métodos de conjunto suportados são o voto e o empilhamento. A votação é implementada como voto suave usando médias ponderadas, e a implementação do empilhamento está usando uma implementação de duas camadas, onde a primeira camada tem os mesmos modelos que o conjunto de votação, e o modelo de segunda camada é usado para encontrar a combinação ideal dos modelos a partir da primeira camada. Se estiver a utilizar modelos ONNX **ou** tiver uma explicabilidade de modelo, o empilhamento será desativado e apenas o voto será utilizado.

Existem múltiplos argumentos predefinidos `kwargs` que `AutoMLConfig` podem ser fornecidos como num objeto para alterar o comportamento padrão do conjunto de pilhas.

* `stack_meta_learner_type`: o meta-aprendiz é um modelo treinado na saída dos modelos heterogéneos individuais. Os meta-aprendizes `LogisticRegression` predefinidos destinam-se a tarefas de classificação (ou `LogisticRegressionCV` se a validação cruzada estiver ativada) e `ElasticNet` a tarefas de regressão/previsão (ou `ElasticNetCV` se a validação cruzada estiver ativada). Este parâmetro pode ser uma das `LogisticRegression`seguintes `ElasticNet`cordas: `LightGBMRegressor`. `LinearRegression` `LogisticRegressionCV` `LightGBMClassifier`, , `ElasticNetCV`, ou .
* `stack_meta_learner_train_percentage`: especifica a proporção do conjunto de formação (ao escolher o tipo de treino de comboio e validação) a reservar para a formação do meta-aprendiz. O valor `0.2`predefinido é .
* `stack_meta_learner_kwargs`: parâmetros opcionais para passar para o inicializador do meta-aprendiz. Estes parâmetros e parâmetros espelham os parâmetros e os tipos de parâmetros do construtor de modelos correspondentes, e são encaminhados para o construtor do modelo.

O código que se segue mostra um `AutoMLConfig` exemplo de especificar o comportamento do conjunto personalizado num objeto.

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

O treino do conjunto é ativado por padrão, `enable_voting_ensemble` `enable_stack_ensemble` mas pode ser desativado utilizando os parâmetros e booleanos.

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

## <a name="run-experiment"></a>Executar experiência

Para ml automatizado, `Experiment` você cria um objeto, `Workspace` que é um objeto nomeado em um uso usado para executar experiências.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Submeta a experimentação para executar e gerar um modelo. Passe `AutoMLConfig` o `submit` método para gerar o modelo.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>As dependências são instaladas pela primeira vez numa nova máquina.  Pode demorar até 10 minutos até que a saída seja mostrada.
>A `show_output` `True` definição para resultados na saída mostrada na consola.

### <a name="exit-criteria"></a>Critérios de Saída
Existem algumas opções que pode definir para terminar a sua experiência.
1. Sem critérios: Se não definir quaisquer parâmetros de saída, a experiência continuará até que não sejam feitos mais progressos na sua métrica primária.
1. Saída após um período `experiment_timeout_minutes` de tempo: A utilização das definições permite-lhe definir quanto tempo em minutos deve continuar a decorrer uma experiência.
1. Saída após uma pontuação ter `experiment_exit_score` sido alcançada: A utilização completará a experiência depois de ter sido atingida uma pontuação métrica primária.

### <a name="explore-model-metrics"></a>Explore as métricas do modelo

Pode ver os resultados do seu treino num widget ou inline se estiver num caderno. Consulte [o Track e avalie os modelos](how-to-track-experiments.md#view-run-details) para obter mais detalhes.

## <a name="understand-automated-ml-models"></a>Compreender modelos ML automatizados

Qualquer modelo produzido com ML automatizado inclui os seguintes passos:
+ Engenharia automática de `"featurization": 'auto'`recursos (se)
+ Escala/Normalização e algoritmo com valores hiperparâmetros

Tornamos transparente obter esta informação a partir da fitted_model saída a partir de ML automatizado.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Engenharia automatizada de recursos

Consulte a lista de [pré-processamento](concept-automated-ml.md#preprocess) e `"featurization": 'auto'`engenharia automática de recursos que acontece quando .

Considere este exemplo:
+ Existem quatro funcionalidades de entrada: A (Numérico), B (Numérico), C (Numérico), D (DataTime)
+ A característica numérica C é abandonada porque é uma coluna de ID com todos os valores únicos
+ Características numéricas A e B têm valores em falta e, portanto, são imputadas pela média
+ A funcionalidade DateTime D está incluída em 11 funcionalidades diferentes de engenharia

Utilize estas 2 APIs no primeiro passo do modelo equipado para entender mais.  Consulte [este caderno de amostras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` devolve uma lista de nomes de funcionalidades projetados.

  Utilização:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Esta lista inclui todos os nomes de funcionalidades projetados.

  >[!Note]
  >Utilize o "timeseriestransformer" para a tarefa='forecasting', então utilize o "datatransformer" para a tarefa de "regressão" ou "classificação".

+ API 2: `get_featurization_summary()` resumo de retorna para todas as funcionalidades de entrada.

  Utilização:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Utilize o "timeseriestransformer" para a tarefa='forecasting', então utilize o "datatransformer" para a tarefa de "regressão" ou "classificação".

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
   |RawFeatureName|Característica de entrada/nome da coluna a partir do conjunto de dados fornecido.|
   |TipoDetectado|Detetado tipo de dados da função de entrada.|
   |Caiu|Indica se a função de entrada foi largada ou utilizada.|
   |EngineeringFeatureCount|Número de funcionalidades geradas através de transformações automatizadas de engenharia de recursos.|
   |Transformações|Lista de transformações aplicadas às funcionalidades de entrada para gerar funcionalidades projetadas.|
   
### <a name="customize-feature-engineering"></a>Personalizar engenharia de recursos
Para personalizar a engenharia `"featurization": FeaturizationConfig`de funcionalidades, especifique .

A personalização suportada inclui:

|Personalização|Definição|
|--|--|
|Atualização de propósito da coluna|Sobrepor o tipo de função para a coluna especificada.|
|Atualização do parâmetro do transformador |Atualizar os parâmetros para o transformador especificado. Atualmente suporta imputer (médio, mais frequente & mediano) e HashOneHotEncoder.|
|Colunas de gotete |Colunas a deixar de ser característica.|
|Transformadores de blocos| Os transformadores de blocos a utilizar no processo de caracterização.|

Crie o objeto FeaturizationConfig utilizando chamadas API:
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

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Escala/Normalização e algoritmo com valores hiperparâmetros:

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

Os modelos produzidos com ML automatizado têm todos objetos de invólucro que espelham a funcionalidade da sua classe de origem de código aberto. A maioria dos objetos de invólucro `predict_proba()` de modelo de classificação devolvidos por ML automatizado implementam a função, que aceita uma amostra de dados de matriz matriz matriz matriz ou escassa das suas características (valores X), e devolve uma matriz n-dimensional de cada amostra e a sua respetiva probabilidade de classe.

Assumindo que recuperou o melhor modelo de execução e equipado utilizando as mesmas chamadas de cima, pode ligar `predict_proba()` diretamente do modelo equipado, fornecendo uma `X_test` amostra no formato apropriado, dependendo do tipo de modelo.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Se o modelo subjacente `predict_proba()` não suportar a função ou o formato estiver incorreto, será lançada uma exceção específica para a classe do modelo. Consulte os docs de referência [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) e [XGBoost,](https://xgboost.readthedocs.io/en/latest/python/python_api.html) por exemplo, de como esta função é implementada para diferentes tipos de modelos.

<a name="explain"></a>

## <a name="model-interpretability"></a>Capacidade de interpretação do modelo

A interpretação do modelo permite-lhe compreender porque é que os seus modelos fizeram previsões e os valores de importância da característica subjacente. O SDK inclui vários pacotes para permitir funcionalidades de interpretação de modelos, tanto no tempo de treino como de inferência, para modelos locais e implantados.

Veja como obter amostras [de](how-to-machine-learning-interpretability-automl.md) código sobre como ativar funcionalidades de interpretação especificamente dentro de experiências automatizadas de aprendizagem automática de máquinas.

Para obter informações gerais sobre como as explicações do modelo e a importância da funcionalidade podem ser ativadas noutras áreas do SDK fora da aprendizagem automática de máquinas, consulte [o](how-to-machine-learning-interpretability.md) artigo conceptual sobre a interpretação.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [como e onde implementar um modelo.](how-to-deploy-and-where.md)

Saiba mais sobre como treinar um modelo de [regressão com machine learning automatizado](tutorial-auto-train-models.md) ou como treinar utilizando machine learning automatizado num recurso [remoto](how-to-auto-train-remote.md).
