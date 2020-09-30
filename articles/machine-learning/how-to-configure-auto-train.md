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
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperfq1
ms.openlocfilehash: 895bdfde80dfe10bef4b6aad12236db60e2c427b
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91533150"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurar experimentações do ML automatizado no Python


Neste guia, aprenda a definir várias configurações das suas experiências automatizadas de aprendizagem automática de máquinas com o [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true). A aprendizagem automática de máquinas escolhe um algoritmo e hiperparímetros para si e gera um modelo pronto para ser implantado. Existem várias opções que pode usar para configurar experiências automatizadas de aprendizagem automática de máquinas.

Para ver exemplos de experiências automatizadas de aprendizagem automática de máquinas, consulte [Tutorial: Treine um modelo de classificação com machine learning automatizado](tutorial-auto-train-models.md) ou [modelos de comboio com aprendizagem automática de máquinas na nuvem.](how-to-auto-train-remote.md)

Opções de configuração disponíveis em machine learning automatizado:

* Selecione o seu tipo de experiência: Classificação, Regressão ou Previsão de Séries de Tempo
* Fonte de dados, formatos e recolha de dados
* Escolha o seu alvo de cálculo: local ou remoto
* Definições automatizadas de experiências de aprendizagem de máquinas
* Executar uma experimentação de machine learning automatizado
* Explore as métricas do modelo
* Registar e implementar modelo

Se preferir uma experiência sem código, também pode [criar as suas experiências automatizadas de aprendizagem automática de máquinas no estúdio Azure Machine Learning.](how-to-use-automated-ml-for-ml-models.md)

## <a name="prerequisites"></a>Pré-requisitos

Para este artigo que precisa, 
* Uma área de trabalho do Azure Machine Learning. Para criar o espaço de trabalho, consulte Criar um espaço de trabalho para [aprendizagem de máquinas Azure.](how-to-manage-workspace.md)

* A Azure Machine Learning Python SDK instalada.
    Para instalar o SDK pode, 
    * Crie uma instância computacional, que instala automaticamente o SDK e está pré-configurada para fluxos de trabalho ML. Veja [o que é um azure machine learning por exemplo para](concept-compute-instance.md#managing-a-compute-instance) mais informações. 

    * [Instale o SDK por si mesmo.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) Apenas certifique-se de incluir o `automl` extra. 

## <a name="select-your-experiment-type"></a>Selecionar o tipo de experimentação

Antes de começar a sua experiência, deve determinar o tipo de problema de aprendizagem automática que está a resolver. A aprendizagem automática de máquinas suporta tipos de tarefas `classification` `regression` de, e `forecasting` . Saiba mais sobre [tipos de tarefas.](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast)

O código que se segue utiliza o `task` parâmetro no construtor para `AutoMLConfig` especificar o tipo de experiência como `classification` .

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Fonte de dados e formato

A aprendizagem automática de máquinas suporta dados que residem no seu ambiente de trabalho local ou na nuvem, como o Azure Blob Storage. Os dados podem ser lidos num **DataFrame pandas** ou num **Separador de Aprendizagem de Máquinas Azure.** [Saiba mais sobre conjuntos de dados.](how-to-create-register-datasets.md)

Requisitos para dados de formação:
- Os dados devem estar em forma tabular.
- O valor a prever, coluna-alvo, deve estar nos dados.

**Para experiências remotas,** os dados de treino devem estar acessíveis a partir do cálculo remoto. A AutoML só aceita [TabularDatasets de Aprendizagem de Máquinas Azure](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) ao trabalhar num computação remota. 

Os conjuntos de dados Azure Machine Learning expõem a funcionalidade a:

* Transfira facilmente dados de ficheiros estáticos ou fontes de URL para o seu espaço de trabalho.
* Disponibilize os seus dados para os scripts de formação quando estiver em execução em recursos de computação em nuvem. Veja [como treinar com conjuntos](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) de dados para um exemplo de utilização da classe para montar `Dataset` dados no seu alvo de computação remota.

O código a seguir cria um SeparadorSet a partir de um url web. Consulte [Criar um Separador de Dados](how-to-create-register-datasets.md#create-a-tabulardataset) para obter exemplos de código sobre como criar conjuntos de dados de outras fontes, como ficheiros locais e datastores.

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**Para experiências locais de computação,** recomendamos dataframes pandas para tempos de processamento mais rápidos.

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>Dados de treino, validação e teste

Pode especificar conjuntos de **treino e validação separados** diretamente no `AutoMLConfig` construtor. Saiba mais sobre [como configurar as divisões de dados e validação cruzada](how-to-configure-cross-validation-data-splits.md) para as suas experiências AutoML. 

Se não especificar explicitamente um `validation_data` ou `n_cross_validation` parâmetro, o AutoML aplica técnicas predefinidos para determinar como a validação é realizada. Esta determinação depende do número de linhas no conjunto de dados atribuído ao seu `training_data` parâmetro. 

|Tamanho dos &nbsp; dados de formação &nbsp;| Técnica de validação |
|---|-----|
|**Maior &nbsp; que &nbsp; 20.000 &nbsp; linhas**| É aplicada a divisão de dados de comboio/validação. O padrão é tomar 10% do conjunto de dados de formação inicial como o conjunto de validação. Por sua vez, este conjunto de validação é usado para o cálculo de métricas.
|**Menores &nbsp; de &nbsp; 20.000 &nbsp; filas**| Aplica-se uma abordagem de validação cruzada. O número predefinido de dobras depende do número de linhas. <br> **Se o conjunto de dados for inferior a 1.000 linhas,** são utilizadas 10 dobras. <br> **Se as linhas estiverem entre 1.000 e 20.000,** então são usadas três dobras.

Neste momento, precisa de fornecer os seus **próprios dados de teste** para avaliação de modelos. Para obter um exemplo de código de trazer os seus próprios dados de teste para avaliação de modelos consulte a secção de **Teste** [deste caderno Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb).

## <a name="compute-to-run-experiment"></a>Computação para executar a experimentação

Em seguida, determinar onde o modelo será treinado. Uma experiência automatizada de treinamento de machine learning pode ser executada nas seguintes opções de computação. Aprenda os prós e contras das opções [de computação local e remota.](concept-automated-ml.md#local-remote) 

* A sua máquina **local,** como um ambiente de trabalho local ou um portátil – Geralmente quando tem um pequeno conjunto de dados e ainda está na fase de exploração. Consulte [este caderno](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb) para obter um exemplo de computação local. 
 
* Uma máquina **remota** na nuvem – [Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute) é um serviço gerido que permite treinar modelos de machine learning em clusters de máquinas virtuais Azure. 

    Consulte [este caderno](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) para obter um exemplo remoto utilizando o Azure Machine Learning Managed Compute. 

* Um **cluster Azure Databricks** na sua subscrição Azure. Pode encontrar mais detalhes aqui - [Configurar o cluster Azure Databricks para ML Automatizado](how-to-configure-environment.md#aml-databricks). Consulte este [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) para ver exemplos de cadernos com Azure Databricks.

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
       blocked_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
1. O exemplo a seguir é uma experiência de regressão definida para terminar após 60 minutos com cinco dobras cruzadas de validação.

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      allowed_models=['KNN'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```


1. As tarefas de previsão requerem uma configuração adicional, consulte o [auto-comboio um](how-to-auto-train-forecast.md) artigo de modelo de previsão da série de tempo para obter mais detalhes. 

    ```python
    time_series_settings = {
        'time_column_name': time_column_name,
        'time_series_id_column_names': time_series_id_column_names,
        'drop_column_names': ['logQuantity'],
        'forecast_horizon': n_test_periods
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
    
### <a name="supported-models"></a>Modelos suportados

A aprendizagem automática de máquinas tenta diferentes modelos e algoritmos durante o processo de automatização e afinação. Como utilizador, não há necessidade de especificar o algoritmo. 

Os três `task` valores de parâmetros diferentes determinam a lista de algoritmos, ou modelos, a aplicar. Utilize os `allowed_models` parâmetros ou `blocked_models` parâmetros para modificar ainda mais as iterações com os modelos disponíveis para incluir ou excluir. 

A tabela seguinte resume os modelos suportados por tipo de tarefa. 

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
[Classificador Perceptron Médio](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest&preserve-view=true)|[Regressor de Descida de Gradiente Online](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest&preserve-view=true) |[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Baías Ingénuas](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Rápido Linear Regressor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest&preserve-view=true)|[Profeta](https://facebook.github.io/prophet/docs/quick_start.html)
[Descida estocástica do gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||PrevisãoTCN
|[Classificador Linear SVM](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest&preserve-view=true)*||

### <a name="primary-metric"></a>Métrica Primária
O `primary metric` parâmetro determina a métrica a ser usada durante o treino do modelo para otimização. As métricas disponíveis que pode selecionar são determinadas pelo tipo de tarefa que escolher, e a tabela seguinte apresenta métricas primárias válidas para cada tipo de tarefa.

Conheça as definições específicas destas métricas na Compreensão dos [resultados automatizados de aprendizagem automática de máquinas.](how-to-understand-automated-ml.md)

|Classificação | Regressão | Previsão de Série Temporal
|--|--|--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-featurization"></a>A exibição de dados

Em todas as experiências automatizadas de machine learning, os seus dados são automaticamente dimensionados e normalizados para ajudar *certos* algoritmos sensíveis a funcionalidades que estão em escalas diferentes. Esta escala e normalização é referida como caracterização. Consulte [a Exibição em AutoML](how-to-configure-auto-features.md#) para obter mais detalhes e exemplos de código. 

Ao configurar as suas experiências no `AutoMLConfig` seu objeto, pode ativar/desativar a definição `featurization` . A tabela a seguir mostra as definições aceites para a exibição no [objeto AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

|Configuração de exibição | Descrição |
| ------------- | ------------- |
|`"featurization": 'auto'`| Indica que, como parte do pré-processamento, [os guarda-dados e as etapas de exibição](how-to-configure-auto-features.md#featurization) são executados automaticamente. **Definição padrão**.|
|`"featurization": 'off'`| Indica que o passo de exibição não deve ser feito automaticamente.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Indica que deve ser utilizado um passo de aposição personalizado. [Saiba como personalizar a caracterização.](how-to-configure-auto-features.md#customize-featurization)|

> [!NOTE]
> As etapas automatizadas de aprendizagem automática (normalização de recurso, manuseamento de dados em falta, conversão de texto em numérico, etc.) tornam-se parte do modelo subjacente. Ao utilizar o modelo para previsões, aplicam-se automaticamente os mesmos passos de apresentação aplicados durante o treino.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a>Configuração do conjunto

Os modelos conjuntos são ativados por padrão e aparecem como as iterações de execução final numa execução AutoML. Atualmente **votingEnsemble** e **StackEnsemble** são suportados. 

O voto implementa o voto suave que utiliza médias ponderadas. A implementação do empilhamento usa uma implementação de duas camadas, onde a primeira camada tem os mesmos modelos que o conjunto de votação, e o modelo de segunda camada é usado para encontrar a combinação ideal dos modelos a partir da primeira camada. 

Se estiver a utilizar modelos ONNX **ou** tiver a explicabilidade do modelo ativada, o empilhamento é desativado e apenas o voto é utilizado.

O treino do conjunto pode ser desativado utilizando os `enable_voting_ensemble` parâmetros e `enable_stack_ensemble` booleanos.

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

Para alterar o comportamento do conjunto padrão, existem múltiplos argumentos padrão que podem ser fornecidos como `kwargs` num `AutoMLConfig` objeto.

> [!IMPORTANT]
>  Os parâmetros a seguir não são parâmetros explícitos da classe AutoMLConfig. 

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

 <a name="exit"></a> 

### <a name="exit-criteria"></a>Critérios de saída

Há algumas opções que pode definir para terminar a sua experiência.

|Critérios| descrição
|----|----
Sem &nbsp; critérios | Se não definir quaisquer parâmetros de saída, a experiência continua até que não haja mais progressos na sua métrica primária.
Depois de &nbsp; um longo período de &nbsp; &nbsp; &nbsp; tempo| Utilize `experiment_timeout_minutes` nas suas definições para definir quanto tempo, em minutos, a sua experiência deve continuar a decorrer. <br><br> Para evitar falhas no tempo de experiência, há um mínimo de 15 minutos, ou 60 minutos se o tamanho da sua linha por coluna exceder 10 milhões.
Uma &nbsp; pontuação &nbsp; foi &nbsp; &nbsp; alcançada| A utilização completa a experiência depois de ter `experiment_exit_score` sido alcançada uma pontuação métrica primária especificada.

## <a name="explore-models-and-metrics"></a>Explore modelos e métricas

Pode ver os resultados do seu treino num widget ou inline se estiver num caderno. Consulte [Track e avalie os modelos](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs) para obter mais detalhes.

Consulte [os resultados automatizados de aprendizagem automática](how-to-understand-automated-ml.md) de máquinas para definições e exemplos dos gráficos de desempenho e métricas fornecidas para cada execução. 

Para obter um resumo de exibição e entender que características foram adicionadas a um determinado modelo, consulte [a transparência da Participação.](how-to-configure-auto-features.md#featurization-transparency) 

## <a name="register-and-deploy-models"></a>Registar e implantar modelos

Para mais detalhes sobre como descarregar ou registar um modelo de implementação para um serviço web, consulte [como e onde implementar um modelo.](how-to-deploy-and-where.md)

<a name="explain"></a>

## <a name="model-interpretability"></a>Capacidade de interpretação do modelo

A interpretação do modelo permite-lhe compreender porque é que os seus modelos fizeram previsões e os valores de importância subjacentes. O SDK inclui vários pacotes para permitir funcionalidades de interpretação de modelos, tanto no tempo de treino como no tempo de inferência, para modelos locais e implantados.

Consulte o [como fazer amostras de](how-to-machine-learning-interpretability-automl.md) código sobre como permitir funcionalidades de interpretação especificamente dentro de experiências automatizadas de aprendizagem automática de máquinas.

Para obter informações gerais sobre como as explicações dos modelos e a importância do recurso podem ser ativadas noutras áreas do SDK fora da aprendizagem automática de máquinas, consulte o artigo [de conceito](how-to-machine-learning-interpretability.md) sobre interpretação.

> [!NOTE]
> O modelo ForecastTCN não é atualmente suportado pelo Cliente explicação. Este modelo não devolverá um dashboard de explicação se for devolvido como o melhor modelo, e não suporta explicações a pedido.

## <a name="next-steps"></a>Próximos passos

+ Saiba mais sobre [como e onde implementar um modelo.](how-to-deploy-and-where.md)

+ Saiba mais sobre [como treinar um modelo de regressão com machine learning automatizado](tutorial-auto-train-models.md) ou como treinar utilizando [aprendizagem automática de máquinas num recurso remoto.](how-to-auto-train-remote.md)

+ Saiba como treinar vários modelos com AutoML no [Acelerador de Soluções de Muitos Modelos.](https://aka.ms/many-models)
