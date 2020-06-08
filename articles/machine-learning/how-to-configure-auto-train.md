---
title: Criar experimentações de ML automatizado
titleSuffix: Azure Machine Learning
description: Machine learning automatizado escolhe um algoritmo para si e gera um modelo pronto para ser implantado. Aprenda as opções que pode usar para configurar experiências automatizadas de aprendizagem automática de máquinas.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 05/20/2020
ms.custom: seodec18
ms.openlocfilehash: 2db6134d49ccd73c32339fe51814d915ae88604a
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2020
ms.locfileid: "84484492"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurar experimentações do ML automatizado no Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste guia, aprenda a definir várias configurações das suas experiências automatizadas de aprendizagem automática de máquinas com o [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). A aprendizagem automática de máquinas escolhe um algoritmo e hiperparímetros para si e gera um modelo pronto para ser implantado. Existem várias opções que pode usar para configurar experiências automatizadas de aprendizagem automática de máquinas.

Para ver exemplos de experiências automatizadas de aprendizagem automática de máquinas, consulte [Tutorial: Treine um modelo de classificação com machine learning automatizado](tutorial-auto-train-models.md) ou [modelos de comboio com aprendizagem automática de máquinas na nuvem.](how-to-auto-train-remote.md)

Opções de configuração disponíveis em machine learning automatizado:

* Selecione o seu tipo de experiência: Classificação, Regressão ou Previsão de Séries de Tempo
* Fonte de dados, formatos e recolha de dados
* Escolha o seu alvo de cálculo: local ou remoto
* Definições automatizadas de experiências de aprendizagem de máquinas
* Executar uma experiência automatizada de aprendizagem automática
* Explore as métricas do modelo
* Registar e implementar modelo

Se preferir uma experiência sem código, também pode [criar as suas experiências automatizadas de aprendizagem automática de máquinas no estúdio Azure Machine Learning.](how-to-use-automated-ml-for-ml-models.md)

## <a name="select-your-experiment-type"></a>Selecionar o tipo de experimentação

Antes de começar a sua experiência, deve determinar o tipo de problema de aprendizagem automática que está a resolver. A aprendizagem automática de máquinas suporta tipos de tarefas de classificação, regressão e previsão. Saiba mais sobre [tipos de tarefas.](how-to-define-task-type.md)

A aprendizagem automática de máquinas suporta os seguintes algoritmos durante o processo de automatização e afinação. Como utilizador, não há necessidade de especificar o algoritmo.

> [!NOTE]
> Se planeia exportar os seus modelos auto ML criados para um [modelo ONNX,](concept-onnx.md)apenas os algoritmos indicados com um * são capazes de ser convertidos para o formato ONNX. Saiba mais sobre [a conversão de modelos para ONNX](concept-automated-ml.md#use-with-onnx). <br> <br> Note-se também que o ONNX apenas suporta tarefas de classificação e regressão neste momento. 

Classificação | Regressão | Previsão de Série Temporal
|-- |-- |--
[Regressão Logística](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Rede Elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[GBM leve](https://lightgbm.readthedocs.io/en/latest/index.html)* |[GBM leve](https://lightgbm.readthedocs.io/en/latest/index.html)*|[GBM leve](https://lightgbm.readthedocs.io/en/latest/index.html)
[Aumento do gradiente](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Aumento do gradiente](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Aumento do gradiente](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Árvore da Decisão](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Árvore da Decisão](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Árvore da Decisão](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Vizinhos Mais Próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K Vizinhos Mais Próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K Vizinhos Mais Próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Laço LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Classificação do vetor de suporte (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Descida estocástica do gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[Descida estocástica do gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Floresta Aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Floresta Aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Floresta Aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classificador DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier) |[DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Classificador Linear DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Linear Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor) |[Linear Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Baías Ingénuas](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Rápido Linear Regressor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Descida estocástica do gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* |[Regressor de Descida de Gradiente Online](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Profeta](https://facebook.github.io/prophet/docs/quick_start.html)
|[Classificador Perceptron Médio](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||PrevisãoTCN
|[Classificador Linear SVM](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)* ||

Utilize o `task` parâmetro no construtor para `AutoMLConfig` especificar o seu tipo de experiência.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Fonte de dados e formato

A aprendizagem automática de máquinas suporta dados que residem no seu ambiente de trabalho local ou na nuvem, como o Azure Blob Storage. Os dados podem ser lidos num **DataFrame pandas** ou num **Separador de Aprendizagem de Máquinas Azure.**  [Saiba mais sobre conjuntos de dados.](how-to-create-register-datasets.md)

Requisitos para dados de formação:
- Os dados devem estar em forma tabular.
- O valor a prever, coluna-alvo, deve estar nos dados.

Os seguintes exemplos de código demonstram como armazenar os dados nestes formatos.

* Conjunto de Dados Tabular

  ```python
  from azureml.core.dataset import Dataset
  from azureml.opendatasets import Diabetes
  
  tabular_dataset = Diabetes.get_tabular_dataset()
  train_dataset, test_dataset = tabular_dataset.random_split(percentage=0.1, seed=42)
  label = "Y"
  ```

* Dataframe de pandas

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Recolher dados para executar experiência em computação remota

Para execuções remotas, os dados de treino devem estar acessíveis a partir do cálculo remoto. A classe [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) no SDK expõe a funcionalidade a:

* transfira facilmente dados de ficheiros estáticos ou fontes de URL para o seu espaço de trabalho
* disponibilize os seus dados para a formação de scripts quando estiver em execução em recursos de computação em nuvem

Consulte o [como fazer](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) para um exemplo de utilização da classe para `Dataset` montar dados no seu alvo de computação.

## <a name="train-and-validation-data"></a>Dados de treino e validação

Pode especificar conjuntos de comboios e validação separados diretamente no `AutoMLConfig` construtor.

### <a name="k-folds-cross-validation"></a>Validação cruzada K-Folds

Utilize `n_cross_validations` a definição para especificar o número de validações cruzadas. O conjunto de dados de treino será dividido aleatoriamente em `n_cross_validations` dobras de tamanho igual. Durante cada ronda de validação cruzada, uma das dobras será utilizada para validação do modelo treinado nas restantes dobras. Este processo repete-se para `n_cross_validations` as rondas até que cada dobra seja utilizada uma vez como conjunto de validação. As pontuações médias em todas as `n_cross_validations` rondas serão reportadas, e o modelo correspondente será retreinado em todo o conjunto de dados de treino.

Saiba mais sobre como a autoML aplica validação cruzada para [evitar modelos de sobreajustamento](concept-manage-ml-pitfalls.md#prevent-over-fitting).
### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Validação cruzada de Monte Carlo (Sub-Amostragem Aleatória Repetida)

Utilize `validation_size` para especificar a percentagem do conjunto de dados de formação que deve ser utilizado para validação, e use `n_cross_validations` para especificar o número de validações cruzadas. Durante cada ronda de validação cruzada, será selecionado aleatoriamente um subconjunto de tamanho `validation_size` para validação do modelo treinado nos restantes dados. Finalmente, as pontuações médias em todas as `n_cross_validations` rondas serão reportadas, e o modelo correspondente será retreinado em todo o conjunto de dados de treino. Monte Carlo não é apoiado para a previsão de séries de tempo.

### <a name="custom-validation-dataset"></a>Conjunto de dados de validação personalizada

Utilize conjunto de dados de validação personalizada se a divisão aleatória não for aceitável, normalmente dados de séries de tempo ou dados desequilibrados. Pode especificar o seu próprio conjunto de dados de validação. O modelo será avaliado com o conjunto de dados de validação especificado em vez de conjunto de dados aleatórios.

## <a name="compute-to-run-experiment"></a>Computação para executar a experimentação

Em seguida, determinar onde o modelo será treinado. Uma experiência automatizada de treinamento de machine learning pode ser executada com as seguintes opções de computação:
* A sua máquina local, como um ambiente de trabalho local ou um portátil – Geralmente quando tem um conjunto de dados pequeno e ainda está na fase de exploração.
* Uma máquina remota na nuvem – [Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute) é um serviço gerido que permite treinar modelos de machine learning em clusters de máquinas virtuais Azure. 

  Consulte este [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) para ver exemplos de cadernos com alvos de computação local e remota.

* Um cluster Azure Databricks na sua subscrição Azure. Pode encontrar mais detalhes aqui - [Configurar o cluster Azure Databricks para ML automatizado](how-to-configure-environment.md#azure-databricks)

  Consulte este [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) para ver exemplos de cadernos com Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configure as definições da sua experiência

Existem várias opções que pode usar para configurar a sua experiência automatizada de aprendizagem automática de máquinas. Estes parâmetros são definidos através da instantânea instantânea de um `AutoMLConfig` objeto. Consulte a [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) para obter uma lista completa de parâmetros.

Alguns exemplos incluem:

1. Experiência de classificação utilizando AUC ponderado como a métrica primária com minutos de tempo de experiência definidos para 30 minutos e 2 dobras de validação cruzada.

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

Os três valores de `task` parâmetros diferentes (o terceiro tipo de tarefa é `forecasting` , e usa um conjunto de algoritmos semelhante como `regression` tarefas) determinam a lista de modelos a aplicar. Utilize os `whitelist` parâmetros ou `blacklist` parâmetros para modificar ainda mais as iterações com os modelos disponíveis para incluir ou excluir. A lista de modelos suportados pode ser encontrada na [Classe DeModels Suportados](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) para ([Classificação,](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification) [Previsão](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)e [Regressão).](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)

Para evitar falhas no tempo de experiência, o serviço de validação automatizado da ML exigirá que `experiment_timeout_minutes` seja definido para um mínimo de 15 minutos, ou 60 minutos se o tamanho da sua linha por coluna exceder 10 milhões.

### <a name="primary-metric"></a>Métrica Primária
A métrica primária determina a métrica a ser usada durante o treino do modelo para otimização. As métricas disponíveis que pode selecionar são determinadas pelo tipo de tarefa que escolher, e a tabela seguinte apresenta métricas primárias válidas para cada tipo de tarefa.

|Classificação | Regressão | Previsão de Série Temporal
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Conheça as definições específicas destas métricas na Compreensão dos [resultados automatizados de aprendizagem automática de máquinas.](how-to-understand-automated-ml.md)

### <a name="data-featurization"></a>A exibição de dados

Em todas as experiências automatizadas de machine learning, os seus dados são [automaticamente dimensionados e normalizados](how-to-configure-auto-features.md#) para ajudar *certos* algoritmos sensíveis a funcionalidades que estão em escalas diferentes.  No entanto, também pode permitir a acoplamento adicional, como a imputação de valores em falta, codificação e transformação.

Ao configurar as suas experiências no `AutoMLConfig` seu objeto, pode ativar/desativar a definição `featurization` . A tabela a seguir mostra as definições aceites para a participação na [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig).

|Configuração de exibição | Descrição |
| ------------- | ------------- |
|`"featurization": 'auto'`| Indica que, como parte do pré-processamento, [os guarda-dados e as etapas de exibição](how-to-configure-auto-features.md#featurization) são executados automaticamente. **Definição predefinida**|
|`"featurization": 'off'`| Indica que o passo de exibição não deve ser feito automaticamente.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Indica que deve ser utilizado um passo de aposição personalizado. [Saiba como personalizar a caracterização.](how-to-configure-auto-features.md#customize-featurization)|

> [!NOTE]
> As etapas automatizadas de aprendizagem automática (normalização de recurso, manuseamento de dados em falta, conversão de texto em numérico, etc.) tornam-se parte do modelo subjacente. Ao utilizar o modelo para previsões, aplicam-se automaticamente os mesmos passos de apresentação aplicados durante o treino.

### <a name="time-series-forecasting"></a>Previsão de Série Temporal
A tarefa da série de tempo `forecasting` requer parâmetros adicionais no objeto de configuração:

1. `time_column_name`: Parâmetro requerido que define o nome da coluna nos seus dados de treino contendo uma série de tempo válida.
1. `max_horizon`: Define o tempo que pretende prever com base na periodicidade dos dados de formação. Por exemplo, se tiver dados de treino com grãos diários, define a distância em dias para a qual pretende que o modelo treine.
1. `grain_column_names`: Define o nome das colunas que contêm dados individuais das séries hortênsias nos seus dados de treino. Por exemplo, se estiver a prever vendas de uma determinada marca por loja, definirá as colunas de loja e marca como as suas colunas de cereais. Serão criadas séries e previsões separadas para cada grão/agrupamento. 

Por exemplo, as definições utilizadas abaixo, consulte o [caderno de amostras](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

Os modelos conjunto são ativados por padrão, e aparecem como as iterações de execução final numa corrida automática de aprendizagem automática. Os métodos de agrupamento atualmente suportados estão a votar e a empilhar. A votação é implementada como voto suave usando médias ponderadas, e a implementação do empilhamento está usando uma implementação de duas camadas, onde a primeira camada tem os mesmos modelos que o conjunto de votação, e o modelo de segunda camada é usado para encontrar a combinação ideal dos modelos a partir da primeira camada. Se estiver a utilizar modelos ONNX **ou** tiver a explicabilidade do modelo ativada, o empilhamento será desativado e apenas será utilizado o voto.

Existem múltiplos argumentos padrão que podem ser fornecidos como `kwargs` num objeto para alterar o comportamento do conjunto `AutoMLConfig` padrão.

* `ensemble_download_models_timeout_sec`: Durante a geração do modelo **VoteIngEnsemble** e **StackEnsemble,** são descarregados vários modelos equipados das anteriores corridas infantis. Se encontrar este erro: `AutoMLEnsembleException: Could not find any models for running ensembling` , poderá ter de dar mais tempo para que os modelos sejam descarregados. O valor predefinido é de 300 segundos para o descarregamento destes modelos em paralelo e não existe um limite máximo de tempo. Configure este parâmetro com um valor superior a 300 segundos, se for necessário mais tempo. 

  > [!NOTE]
  >  Se o tempo limite for atingido e houver modelos descarregados, então o encantante prossegue com o maior número de modelos que descarregou. Não é necessário que todos os modelos precisem de ser descarregados para terminar dentro desse intervalo.

Os seguintes parâmetros aplicam-se apenas aos modelos **StackEnsemble:** 

* `stack_meta_learner_type`: o meta-aprendiz é um modelo treinado na saída dos modelos heterogéneos individuais. Os meta-aprendizes predefinidos são `LogisticRegression` para tarefas de classificação (ou `LogisticRegressionCV` se a validação cruzada estiver ativada) e para `ElasticNet` tarefas de regressão/previsão (ou `ElasticNetCV` se a validação cruzada estiver ativada). Este parâmetro pode ser uma das seguintes cordas: `LogisticRegression` , , , , , , , ou `LogisticRegressionCV` `LightGBMClassifier` `ElasticNet` `ElasticNetCV` `LightGBMRegressor` `LinearRegression` .

* `stack_meta_learner_train_percentage`: especifica a proporção do conjunto de formação (na escolha do tipo de formação de comboio e validação) a reservar para a formação do metaapresador. O valor predefinido é `0.2` . 

* `stack_meta_learner_kwargs`: parâmetros opcionais para passar para o inicializador do metaacosador. Estes parâmetros e tipos de parâmetros espelham os parâmetros e os tipos de parâmetros do construtor de modelos correspondentes, e são encaminhados para o construtor do modelo.

O código que se segue mostra um exemplo de especificação do comportamento do conjunto personalizado num `AutoMLConfig` objeto.

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
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

O treino do conjunto é ativado por padrão, mas pode ser desativado utilizando os `enable_voting_ensemble` parâmetros e `enable_stack_ensemble` booleanos.

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

## <a name="run-experiment"></a>Experiência de execução

Para ml automatizado, você cria um `Experiment` objeto, que é um objeto nomeado em um `Workspace` usado para executar experiências.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Submeta a experimentação para executar e gerar um modelo. Passe o `AutoMLConfig` método para gerar o `submit` modelo.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>As dependências são instaladas pela primeira vez numa nova máquina.  Pode levar até 10 minutos antes de ser mostrada a saída.
>Definição `show_output` para `True` resultados na saída mostrada na consola.

### <a name="exit-criteria"></a><a name="exit"></a>Critérios de saída

Há algumas opções que pode definir para terminar a sua experiência.
1. Sem critérios: Se não definir quaisquer parâmetros de saída, a experiência continuará até que não sejam feitos mais progressos na sua métrica primária.
1. Saída após um período de tempo: A utilização `experiment_timeout_minutes` nas suas definições permite-lhe definir quanto tempo em minutos deve continuar a decorrer uma experiência.
1. Saída após a conclusão de uma pontuação: A utilização `experiment_exit_score` completará a experiência depois de ter sido alcançada uma pontuação métrica primária.

### <a name="explore-model-metrics"></a>Explore as métricas do modelo

Pode ver os resultados do seu treino num widget ou inline se estiver num caderno. Consulte [Track e avalie os modelos](how-to-track-experiments.md#view-run-details) para obter mais detalhes.

Para mais detalhes sobre como descarregar ou registar um modelo de implementação para um serviço web, consulte [como e onde implementar um modelo.](how-to-deploy-and-where.md)

## <a name="understand-automated-ml-models"></a>Compreender modelos ML automatizados

Qualquer modelo produzido com ml automatizado inclui os seguintes passos:
+ Engenharia de recursos automatizados `"featurization": 'auto'` (se)
+ Escala/Normalização e algoritmo com valores de hiperparímetro

Tornamos transparente obter esta informação da fitted_model produção de ML automatizado.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Engenharia de recursos automatizados

Consulte a lista de engenharia de recursos pré-processamento e [automatizado]() que acontece quando `"featurization": 'auto'` .

Considere este exemplo:
+ Existem quatro características de entrada: A (Numérico), B (numérico), C (numérico), D (DataTime)
+ Recurso numérico C é largado porque é uma coluna de ID com todos os valores únicos
+ Características numéricas A e B têm valores em falta e, portanto, são imputadas pela média
+ A função DateTime D é apresentada em 11 diferentes funcionalidades de engenharia

Utilize estes 2 APIs no primeiro passo do modelo equipado para entender mais.  Consulte [este caderno de amostras.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)

+ API 1: `get_engineered_feature_names()` devolve uma lista de nomes de recursos projetados.

  Utilização:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Esta lista inclui todos os nomes de recursos projetados.

  >[!Note]
  >Utilize o "timeseriestransformer" para a tarefa='previsão', então utilize o "adaptador de dados" para a tarefa de "regressão" ou "classificação".

+ API 2: `get_featurization_summary()` retorna o resumo da exibição para todas as funcionalidades de entrada.

  Utilização:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Utilize o "timeseriestransformer" para a tarefa='previsão', então utilize o "adaptador de dados" para a tarefa de "regressão" ou "classificação".

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
   |RawFeatureName|Denominação/nome da coluna a partir do conjunto de dados fornecido.|
   |TipoDetectado|Tipo de dados detetado da função de entrada.|
   |Caiu|Indica se a função de entrada foi largada ou utilizada.|
   |EngenhariaFeatureCount|Número de funcionalidades geradas através de transformações automatizadas de engenharia de recursos.|
   |Transformações|Lista de transformações aplicadas às funcionalidades de entrada para gerar funcionalidades de engenharia.|
### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Escala/Normalização e algoritmo com valores de hiperparímetro:

Para compreender os valores de escala/normalização e algoritmo/hiperparímetro para um pipeline, utilize fitted_model.passos. [Saiba mais sobre escala/normalização.]() Eis uma saída de exemplo:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Para obter mais detalhes, utilize esta função de ajudante: 

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


print_model(model)
```

A seguinte saída da amostra é para um oleoduto usando um algoritmo específico (LogísticaRegression com RobustScalar, neste caso).

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

### <a name="predict-class-probability"></a>Prever probabilidade de classe

Os modelos produzidos com ML automatizado têm todos objetos de invólucro que espelham a funcionalidade da sua classe de origem aberta. A maioria dos objetos de invólucro de modelo de classificação devolvidos por ML automatizado implementam a `predict_proba()` função, que aceita uma amostra de dados matrizes semelhantes a matrizes ou escassas das suas funcionalidades (valores X), e devolve uma matriz n-dimensional de cada amostra e sua respetiva probabilidade de classe.

Assumindo que recuperou o melhor modelo de execução e equipado utilizando as mesmas chamadas de cima, pode ligar `predict_proba()` diretamente do modelo equipado, fornecendo uma `X_test` amostra no formato adequado, dependendo do tipo de modelo.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Se o modelo subjacente não suportar a `predict_proba()` função ou o formato estiver incorreto, será lançada uma exceção específica da classe do modelo. Consulte os docs de referência [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) e [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) para exemplos de como esta função é implementada para diferentes tipos de modelos.

<a name="explain"></a>

## <a name="model-interpretability"></a>Capacidade de interpretação do modelo

A interpretação do modelo permite-lhe compreender porque é que os seus modelos fizeram previsões e os valores de importância subjacentes. O SDK inclui vários pacotes para permitir funcionalidades de interpretação de modelos, tanto no tempo de treino como no tempo de inferência, para modelos locais e implantados.

Consulte o [como fazer amostras de](how-to-machine-learning-interpretability-automl.md) código sobre como permitir funcionalidades de interpretação especificamente dentro de experiências automatizadas de aprendizagem automática de máquinas.

Para obter informações gerais sobre como as explicações dos modelos e a importância do recurso podem ser ativadas noutras áreas do SDK fora da aprendizagem automática de máquinas, consulte o artigo [de conceito](how-to-machine-learning-interpretability.md) sobre interpretação.

## <a name="next-steps"></a>Próximos passos

+ Saiba mais sobre [como e onde implementar um modelo.](how-to-deploy-and-where.md)

+ Saiba mais sobre [como treinar um modelo de regressão com machine learning automatizado](tutorial-auto-train-models.md) ou como treinar utilizando [aprendizagem automática de máquinas num recurso remoto.](how-to-auto-train-remote.md)
+ Saiba como treinar vários modelos com autoML no [Acelerador de Soluções de Muitos Modelos.](https://aka.ms/many-models)
