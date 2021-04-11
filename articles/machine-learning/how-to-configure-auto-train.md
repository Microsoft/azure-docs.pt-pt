---
title: Criar experimentações de ML automatizado
titleSuffix: Azure Machine Learning
description: Saiba como definir fontes de dados, computas e configurações para as suas experiências automatizadas de aprendizagem automática de máquinas.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperf-fy21q1, automl
ms.openlocfilehash: 755386bfa36b18796eccec0020efe9136e0215cd
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106068154"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurar experimentações do ML automatizado no Python


Neste guia, aprenda a definir várias configurações das suas experiências automatizadas de aprendizagem automática de máquinas com o [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro). A aprendizagem automática de máquinas escolhe um algoritmo e hiperparímetros para si e gera um modelo pronto para ser implantado. Existem várias opções que pode usar para configurar experiências automatizadas de aprendizagem automática de máquinas.

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
    * Crie uma instância computacional, que instala automaticamente o SDK e está pré-configurada para fluxos de trabalho ML. Consulte [Criar e gerir uma instância de cálculo de Aprendizagem automática Azure](how-to-create-manage-compute-instance.md) para obter mais informações. 

    * [Instale `automl` o pacote por si mesmo,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment)que inclui a [instalação predefinição](/python/api/overview/azure/ml/install#default-install) do SDK.
    
    > [!WARNING]
    > Python 3.8 não é compatível com `automl` . 

## <a name="select-your-experiment-type"></a>Selecionar o tipo de experimentação

Antes de começar a sua experiência, deve determinar o tipo de problema de aprendizagem automática que está a resolver. A aprendizagem automática de máquinas suporta tipos de tarefas `classification` `regression` de, e `forecasting` . Saiba mais sobre [tipos de tarefas.](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast)

O código que se segue utiliza o `task` parâmetro no construtor para `AutoMLConfig` especificar o tipo de experiência como `classification` .

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Origem de dados e formato

O machine learning automatizado suporta os dados que residem no ambiente de trabalho local ou na cloud, como o Armazenamento de Blobs do Azure. Os dados podem ser lidos num **DataFrame pandas** ou num **Separador de Aprendizagem de Máquinas Azure.** [Saiba mais sobre os conjuntos de dados](how-to-create-register-datasets.md).

Requisitos para a formação de dados em machine learning:
- Os dados devem estar em forma tabular.
- O valor a prever, coluna-alvo, deve estar nos dados.

**Para experiências remotas,** os dados de treino devem estar acessíveis a partir do cálculo remoto. O AutoML só aceita [TabularDatasets do Azure Machine Learning](/python/api/azureml-core/azureml.data.tabulardataset) ao trabalhar em computação remota. 

Os conjuntos de dados do Azure Machine Learning expõem a funcionalidade para:

* Transfira facilmente dados de ficheiros estáticos ou fontes de URL para o seu espaço de trabalho.
* Tornar os dados disponíveis para os scripts de preparação ao executar os recursos de computação na cloud. Veja [como treinar com conjuntos](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) de dados para um exemplo de utilização da classe para montar `Dataset` dados no seu alvo de computação remota.

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

Pode especificar dados de **formação separados e conjuntos de dados de validação** diretamente no `AutoMLConfig` construtor. Saiba mais sobre [como configurar as divisões de dados e validação cruzada](how-to-configure-cross-validation-data-splits.md) para as suas experiências AutoML. 

Se não especificar explicitamente um `validation_data` ou `n_cross_validation` parâmetro, o ML automatizado aplica técnicas predefinidos para determinar como a validação é realizada. Esta determinação depende do número de linhas no conjunto de dados atribuído ao seu `training_data` parâmetro. 

|Tamanho dos &nbsp; dados de formação &nbsp;| Técnica de validação |
|---|-----|
|**Maior &nbsp; que &nbsp; 20.000 &nbsp; linhas**| É aplicada a divisão de dados de comboio/validação. O padrão é tomar 10% do conjunto de dados de formação inicial como o conjunto de validação. Por sua vez, este conjunto de validação é usado para o cálculo de métricas.
|**Menores &nbsp; de &nbsp; 20.000 &nbsp; filas**| Aplica-se uma abordagem de validação cruzada. O número predefinido de dobras depende do número de linhas. <br> **Se o conjunto de dados for inferior a 1.000 linhas,** são utilizadas 10 dobras. <br> **Se as linhas estiverem entre 1.000 e 20.000,** então são usadas três dobras.

Neste momento, precisa de fornecer os seus **próprios dados de teste** para avaliação de modelos. Para obter um exemplo de código de trazer os seus próprios dados de teste para avaliação de modelos consulte a secção de **Teste** [deste caderno Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb).

## <a name="compute-to-run-experiment"></a>Computação para executar a experimentação

Em seguida, determinar onde o modelo será treinado. Pode executar uma experimentação de preparação de machine learning automatizado nas seguintes opções de computação. Conheça as [vantagens e desvantagens das opções de computação local e remota](concept-automated-ml.md#local-remote). 

* A sua máquina **local,** como um ambiente de trabalho local ou um portátil – Geralmente quando tem um pequeno conjunto de dados e ainda está na fase de exploração. Veja [este bloco de notas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb) para obter um exemplo de computação local. 
 
* Uma máquina **remota** na nuvem – [Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute) é um serviço gerido que permite treinar modelos de machine learning em clusters de máquinas virtuais Azure. 

    Veja [este bloco de notas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) para obter um exemplo remoto que utiliza o Azure Machine Learning Managed Compute. 

* Um **cluster Azure Databricks** na sua subscrição Azure. Pode encontrar mais detalhes na [Configuração de um cluster Azure Databricks para ML automatizado.](how-to-configure-databricks-automl-environment.md) Consulte este [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) para ver exemplos de cadernos com Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configure as definições da sua experiência

Existem várias opções que pode usar para configurar a sua experiência automatizada de aprendizagem automática de máquinas. Estes parâmetros são definidos através da instantânea instantânea de um `AutoMLConfig` objeto. Consulte a [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) para obter uma lista completa de parâmetros.

Alguns exemplos incluem:

1. Experiência de classificação utilizando AUC ponderado como a métrica primária com minutos de tempo de experiência definidos para 30 minutos e 2 dobras de validação cruzada.

   ```python
       automl_classifier=AutoMLConfig(task='classification',
                                      primary_metric='AUC_weighted',
                                      experiment_timeout_minutes=30,
                                      blocked_models=['XGBoostClassifier'],
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=2)
   ```
1. O exemplo a seguir é uma experiência de regressão definida para terminar após 60 minutos com cinco dobras cruzadas de validação.

   ```python
      automl_regressor = AutoMLConfig(task='regression',
                                      experiment_timeout_minutes=60,
                                      allowed_models=['KNN'],
                                      primary_metric='r2_score',
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=5)
   ```


1. As tarefas de previsão requerem configuração extra, consulte o [Autotrain um](how-to-auto-train-forecast.md) artigo de modelo de previsão da série de tempo para obter mais detalhes. 

    ```python
    time_series_settings = {
        'time_column_name': time_column_name,
        'time_series_id_column_names': time_series_id_column_names,
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
[Classificador Perceptron Médio](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)|[Regressor de Descida de Gradiente Online](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) |[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Baías Ingénuas](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Rápido Linear Regressor](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)|[Profeta](https://facebook.github.io/prophet/docs/quick_start.html)
[Descida estocástica do gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||PrevisãoTCN
|[Classificador Linear SVM](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)*||

### <a name="primary-metric"></a>Métrica Primária
O `primary metric` parâmetro determina a métrica a ser usada durante o treino do modelo para otimização. As métricas disponíveis que pode selecionar são determinadas pelo tipo de tarefa que escolher, e a tabela seguinte apresenta métricas primárias válidas para cada tipo de tarefa.

Escolher uma métrica primária para aprendizagem automática de máquinas para otimizar depende de muitos fatores. Recomendamos que a sua principal consideração seja escolher uma métrica que melhor represente as suas necessidades de negócio. Em seguida, considere se a métrica é adequada para o seu perfil de conjunto de dados (tamanho de dados, gama, distribuição de classes, etc.).

Conheça as definições específicas destas métricas na Compreensão dos [resultados automatizados de aprendizagem automática de máquinas.](how-to-understand-automated-ml.md)

|Classificação | Regressão | Previsão de Série Temporal
|--|--|--
|`accuracy`| `spearman_correlation` | `spearman_correlation`
|`AUC_weighted` | `normalized_root_mean_squared_error` | `normalized_root_mean_squared_error`
|`average_precision_score_weighted` | `r2_score` | `r2_score`
|`norm_macro_recall` | `normalized_mean_absolute_error` | `normalized_mean_absolute_error`
|`precision_score_weighted` |

### <a name="primary-metrics-for-classification-scenarios"></a>Métricas primárias para cenários de classificação 

As métricas limiares postais, `accuracy` `average_precision_score_weighted` como, `norm_macro_recall` , e podem `precision_score_weighted` não otimizar tão bem para conjuntos de dados que são pequenos, têm uma inclinação de classe muito grande (desequilíbrio de classe), ou quando o valor métrico esperado é muito próximo de 0,0 ou 1.0. Nesses casos, `AUC_weighted` pode ser uma escolha melhor para a métrica primária. Após a conclusão automatizada da aprendizagem automática de máquinas, pode escolher o modelo vencedor com base na métrica mais adequada às necessidades do seu negócio.

| Metric | Casos de uso de exemplo |
| ------ | ------- |
| `accuracy` | Classificação de imagem, análise de sentimento, previsão de Churn |
| `AUC_weighted` | Deteção de fraudes, classificação de imagem, deteção de anomalias/deteção de spam |
| `average_precision_score_weighted` | Análise de sentimentos |
| `norm_macro_recall` | Previsão de churn |
| `precision_score_weighted` |  |

### <a name="primary-metrics-for-regression-scenarios"></a>Métricas primárias para cenários de regressão

Métricas como `r2_score` e podem representar melhor a qualidade do modelo quando a escala do `spearman_correlation` valor-a-prever cobre muitas ordens de magnitude. Por exemplo, a estimativa salarial, onde muitas pessoas têm um salário de 20 mil a 100 mil dólares, mas a escala vai muito alta com alguns salários na faixa dos 100 milhões de dólares. 

`normalized_mean_absolute_error` e trataria um erro de previsão de `normalized_root_mean_squared_error` 20 mil dólares o mesmo para um trabalhador com um salário de 30 mil dólares como um trabalhador a ganhar 20 milhões de dólares. Embora, na realidade, prever apenas 20 mil dólares de desconto de um salário de 20 milhões de dólares é muito próximo (uma pequena diferença relativa de 0,1%), enquanto 20 mil dólares de desconto de 30 mil dólares não é próximo (uma grande diferença relativa de 67%). `normalized_mean_absolute_error` e `normalized_root_mean_squared_error` são úteis quando os valores a prever estão numa escala semelhante.

| Metric | Casos de uso de exemplo |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | Previsão de preços (casa/produto/ponta), Previsão da pontuação da revisão |
| `r2_score` | Atraso da companhia aérea, estimativa salarial, tempo de resolução de bugs |
| `normalized_mean_absolute_error` |  |

### <a name="primary-metrics-for-time-series-forecasting-scenarios"></a>Métricas primárias para cenários de previsão de séries de tempo

Consulte notas de regressão, acima.

| Metric | Casos de uso de exemplo |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | Previsão de preços (previsão), otimização de inventário, previsão da procura |
| `r2_score` | Previsão de preços (previsão), otimização de inventário, previsão da procura |
| `normalized_mean_absolute_error` | |

### <a name="data-featurization"></a>A exibição de dados

Em todas as experiências automatizadas de machine learning, os seus dados são automaticamente dimensionados e normalizados para ajudar *certos* algoritmos sensíveis a funcionalidades que estão em escalas diferentes. Esta escala e normalização é referida como caracterização. Consulte [a Exibição em AutoML](how-to-configure-auto-features.md#) para obter mais detalhes e exemplos de código. 

Ao configurar as suas experiências no `AutoMLConfig` seu objeto, pode ativar/desativar a definição `featurization` . A tabela a seguir mostra as definições aceites para a exibição no [objeto AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

|Configuração de exibição | Description |
| ------------- | ------------- |
|`"featurization": 'auto'`| Indica que, como parte do pré-processamento, [os guarda-dados e as etapas de exibição](how-to-configure-auto-features.md#featurization) são executados automaticamente. **Definição padrão**.|
|`"featurization": 'off'`| Indica que o passo de exibição não deve ser feito automaticamente.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Indica que deve ser utilizado um passo de aposição personalizado. [Saiba como personalizar a caracterização.](how-to-configure-auto-features.md#customize-featurization)|

> [!NOTE]
> As etapas automatizadas de aprendizagem automática (normalização de recurso, manuseamento de dados em falta, conversão de texto em numérico, etc.) tornam-se parte do modelo subjacente. Ao utilizar o modelo para previsões, aplicam-se automaticamente os mesmos passos de apresentação aplicados durante o treino.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a>Configuração do conjunto

Os modelos conjuntos são ativados por padrão e aparecem como as iterações de execução final numa execução AutoML. Atualmente **votingEnsemble** e **StackEnsemble** são suportados. 

O voto implementa o voto suave, que utiliza médias ponderadas. A implementação do empilhamento usa uma implementação de duas camadas, onde a primeira camada tem os mesmos modelos que o conjunto de votação, e o modelo de segunda camada é usado para encontrar a combinação ideal dos modelos a partir da primeira camada. 

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

* `stack_meta_learner_train_percentage`: especifica a proporção do conjunto de formação (na escolha do tipo de formação de comboio e validação) a reservar para a formação do metaapresador. O valor predefinido é `0.2`. 

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

<a name="exit"></a> 

### <a name="exit-criteria"></a>Critérios de saída

Existem algumas opções que pode definir no seu AutoMLConfig para terminar a sua experiência.

|Critérios| descrição
|----|----
Sem &nbsp; critérios | Se não definir quaisquer parâmetros de saída, a experiência continua até que não haja mais progressos na sua métrica primária.
Depois de &nbsp; um longo período de &nbsp; &nbsp; &nbsp; tempo| Utilize `experiment_timeout_minutes` nas suas definições para definir quanto tempo, em minutos, a sua experiência deve continuar a decorrer. <br><br> Para evitar falhas no tempo de experiência, há um mínimo de 15 minutos, ou 60 minutos se o tamanho da sua linha por coluna exceder 10 milhões.
Uma &nbsp; pontuação &nbsp; foi &nbsp; &nbsp; alcançada| A utilização completa a experiência depois de ter `experiment_exit_score` sido alcançada uma pontuação métrica primária especificada.

## <a name="run-experiment"></a>Experiência de execução

Para ml automatizado, você cria um `Experiment` objeto, que é um objeto nomeado em um `Workspace` usado para executar experiências.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'Tutorial-automl'
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

### <a name="multiple-child-runs-on-clusters"></a>Várias crianças correm em aglomerados

As corridas automatizadas de crianças experimentadas ml podem ser realizadas em um cluster que já está executando outra experiência. No entanto, o tempo depende de quantos nós o cluster tem, e se esses nós estão disponíveis para executar uma experiência diferente.

Cada nó no cluster atua como uma máquina virtual individual (VM) que pode realizar uma única corrida de treino; para ml automatizado isto significa uma corrida de crianças. Se todos os nós estiverem ocupados, a nova experiência está na fila. Mas se houver nós livres, a nova experiência será executada em paralelo nos nós/VMs disponíveis.

Para ajudar a gerir as corridas de crianças e quando podem ser realizadas, recomendamos que crie um cluster dedicado por experiência, e que combine o número `max_concurrent_iterations` da sua experiência com o número de nós no cluster. Desta forma, utilize todos os nós do agrupamento ao mesmo tempo com o número de corridas/iterações simultâneas que pretende.

Configure  `max_concurrent_iterations` no seu `AutoMLConfig` objeto. Se não estiver configurado, por defeito só é permitida uma execução/iteração de uma criança em simultâneo por experiência.  

## <a name="explore-models-and-metrics"></a>Explore modelos e métricas

A ML automatizada oferece opções para monitorizar e avaliar os resultados da sua formação. 

* Pode ver os resultados do seu treino num widget ou inline se estiver num caderno. Veja [como monitorizar ml automatizado corre](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs) para mais detalhes.

* Para definições e exemplos dos gráficos de desempenho e métricas fornecidas para cada execução, consulte [os resultados automatizados da experiência de aprendizagem automática](how-to-understand-automated-ml.md) de máquinas . 

* Para obter um resumo de exibição e entender que características foram adicionadas a um determinado modelo, consulte [a transparência da Participação.](how-to-configure-auto-features.md#featurization-transparency) 

Pode ver os hiperparímetros, as técnicas de escala e normalização e algoritmo aplicado a uma ml automática específica com a seguinte solução de código personalizado. 

O seguinte define o método personalizado, `print_model()` que imprime os hiperparímetros de cada passo do gasoduto de treino automatizado ML.
 
```python
from pprint import pprint

def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0]+ ' - ')
        elif hasattr(step[1], '_base_learners') and hasattr(step[1], '_meta_learner'):
            print("\nMeta Learner")
            pprint(step[1]._meta_learner)
            print()
            for estimator in step[1]._base_learners:
                print_model(estimator[1], estimator[0]+ ' - ')
        else:
            pprint(step[1].get_params())
            print()   
```

Para uma corrida local ou remota que foi submetida e treinada a partir do mesmo caderno de experiências, você pode passar no melhor modelo usando o `get_output()` método. 

```python
best_run, fitted_model = run.get_output()
print(best_run)
         
print_model(fitted_model)
```

A seguinte saída indica que:
 
* A técnica StandardScalerWrapper foi utilizada para escalar e normalizar os dados antes do treino.

* O algoritmo XGBoostClassifier foi identificado como o melhor executado, e também mostra os valores do hiperparímetro. 

```python
StandardScalerWrapper
{'class_name': 'StandardScaler',
 'copy': True,
 'module_name': 'sklearn.preprocessing.data',
 'with_mean': False,
 'with_std': False}

XGBoostClassifier
{'base_score': 0.5,
 'booster': 'gbtree',
 'colsample_bylevel': 1,
 'colsample_bynode': 1,
 'colsample_bytree': 0.6,
 'eta': 0.4,
 'gamma': 0,
 'learning_rate': 0.1,
 'max_delta_step': 0,
 'max_depth': 8,
 'max_leaves': 0,
 'min_child_weight': 1,
 'missing': nan,
 'n_estimators': 400,
 'n_jobs': 1,
 'nthread': None,
 'objective': 'multi:softprob',
 'random_state': 0,
 'reg_alpha': 0,
 'reg_lambda': 1.6666666666666667,
 'scale_pos_weight': 1,
 'seed': None,
 'silent': None,
 'subsample': 0.8,
 'tree_method': 'auto',
 'verbose': -10,
 'verbosity': 1}
```

Para uma corrida existente a partir de uma experiência diferente no seu espaço de trabalho, obtenha o ID de execução específico que você quer explorar e passar isso para o `print_model()` método. 

```python
from azureml.train.automl.run import AutoMLRun

ws = Workspace.from_config()
experiment = ws.experiments['automl-classification']
automl_run = AutoMLRun(experiment, run_id = 'AutoML_xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx')

automl_run
best_run, model_from_aml = automl_run.get_output()

print_model(model_from_aml)

```
> [!NOTE]
> Os algoritmos que a ML automatizada emprega têm aleatoriedade inerente que pode causar uma ligeira variação na pontuação final das métricas de um modelo recomendado, como precisão. A ML automatizada também realiza operações em dados como divisão de ensaios de comboio, divisão de validação de comboios ou validação cruzada quando necessário. Portanto, se executar uma experiência com as mesmas configurações e métrica primária várias vezes, provavelmente verá variação em cada experiência métricas pontuadas devido a estes fatores. 

## <a name="register-and-deploy-models"></a>Registar e implantar modelos

Pode registar um modelo, para que possa voltar para uso posterior. 

Para registar um modelo a partir de uma execução automática de ML, utilize o [`register_model()`](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) método. 

```Python

best_run, fitted_model = run.get_output()
print(fitted_model.steps)

model_name = best_run.properties['model_name']
description = 'AutoML forecast example'
tags = None

model = remote_run.register_model(model_name = model_name, 
                                  description = description, 
                                  tags = tags)
```


Para obter detalhes sobre como criar uma configuração de implementação e implementar um modelo registado num serviço web, consulte [como e onde implementar um modelo.](how-to-deploy-and-where.md?tabs=python#define-a-deployment-configuration)

> [!TIP]
> Para modelos registados, a implementação de um clique está disponível através do [estúdio Azure Machine Learning](https://ml.azure.com). Veja [como implementar modelos registados a partir do estúdio.](how-to-use-automated-ml-for-ml-models.md#deploy-your-model) 
<a name="explain"></a>

## <a name="model-interpretability"></a>Capacidade de interpretação do modelo

A interpretação do modelo permite-lhe compreender porque é que os seus modelos fizeram previsões e os valores de importância subjacentes. O SDK inclui vários pacotes para permitir funcionalidades de interpretação de modelos, tanto no tempo de treino como no tempo de inferência, para modelos locais e implantados.

Consulte o [como fazer amostras de](how-to-machine-learning-interpretability-automl.md) código sobre como permitir funcionalidades de interpretação especificamente dentro de experiências automatizadas de aprendizagem automática de máquinas.

Para obter informações gerais sobre como as explicações dos modelos e a importância do recurso podem ser ativadas noutras áreas do SDK fora da aprendizagem automática de máquinas, consulte o artigo [de conceito](how-to-machine-learning-interpretability.md) sobre interpretação.

> [!NOTE]
> O modelo ForecastTCN não é atualmente suportado pelo Cliente explicação. Este modelo não devolverá um dashboard de explicação se for devolvido como o melhor modelo, e não suporta explicações a pedido.

## <a name="next-steps"></a>Passos seguintes

+ Saiba mais sobre [como e onde implementar um modelo.](how-to-deploy-and-where.md)

+ Saiba mais sobre [como treinar um modelo de regressão com machine learning automatizado](tutorial-auto-train-models.md) ou como treinar utilizando [aprendizagem automática de máquinas num recurso remoto.](how-to-auto-train-remote.md)

+ Saiba como treinar vários modelos com AutoML no [Acelerador de Soluções de Muitos Modelos.](https://aka.ms/many-models)

+ [Experiências automatizadas de ML de resolução de problemas.](how-to-troubleshoot-auto-ml.md) 
