---
title: Criar experimentações de ML automatizadas
titleSuffix: Azure Machine Learning service
description: Aprendizagem automática escolhe um algoritmo para e gera um modelo pronto para implantação. Saiba as opções que pode utilizar para configurar automatizada experimentações de machine learning.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 9c003ebaed645fcdefb379eb100220ccc2207d82
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202982"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurar automatizadas de ML de experimentações no Python

Neste guia, saiba como definir várias definições de configuração de seu automatizada experimentações de machine learning com o [do Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Aprendizagem automática escolhe um algoritmo e hiperparâmetros para e gera um modelo pronto para implantação. Existem várias opções que pode utilizar para configurar automatizada experimentações de machine learning.

Para ver exemplos de um automatizada experimentações de machine learning, consulte [Tutorial: Preparar um modelo de classificação automatizada de machine Learning](tutorial-auto-train-models.md) ou [formar modelos com aprendizagem automática na cloud](how-to-auto-train-remote.md).

Opções de configuração disponíveis no automatizada de machine learning:

* Selecione o tipo de experimentação: Classificação, regressão ou previsão de série temporal
* Origem de dados, formatos e obtenção de dados
* Escolher o destino de computação: local ou remoto
* Aprendizagem automatizada as definições de experimentação
* Executar uma automatizada experimentação do machine learning
* Explorar métricas de modelo
* Registe e implemente o modelo

Se preferir uma experiência sem código, também pode [criar sua automatizada experimentações de machine learning no portal do Azure](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Selecione o tipo de experimentação

Antes de começar a sua experimentação, deve determinar o tipo de problema de aprendizado de máquina que está a resolver. Automatizada de machine learning suporta tipos de tarefa de classificação, regressão e previsão.

Automatizada de machine learning suporta os seguintes algoritmos durante a automação e o processo de otimização. Como um utilizador, não é necessário para especificar o algoritmo. Embora os algoritmos DNN estejam disponíveis durante o treinamento, ML automatizado não criar modelos DNN.

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
[Classificador DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[Regressor foi DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Regressor foi DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Classificador DNN Linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regressor foi linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regressor foi linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[A classificação bayesiana Ingênua](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[Descendente gradação stochastic gradient (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

Utilize o `task` parâmetro no `AutoMLConfig` construtor para especificar o tipo de experimentação.

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

Se estiver a utilizar uma computação remota para executar a sua experimentação, a obtenção de dados deverá ser colada num script de python separado `get_data()`. Este script é executado na computação remota onde é executada a automatizada experimentação do machine learning. `get_data` Elimina a necessidade de obter os dados durante a transmissão para cada iteração. Sem `get_data`, sua experimentação irão falhar quando é executado no cálculo remoto.

Eis um exemplo de `get_data`:

```python
%%writefile $project_folder/get_data.py
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import LabelEncoder
def get_data(): # Burning man 2016 data
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    le = LabelEncoder()
    le.fit(df["Label"].values)
    y = le.transform(df["Label"].values)
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    return { "X" : df, "y" : y }
```

No seu `AutoMLConfig` objeto, especifique o `data_script` parâmetro e forneça o caminho para o `get_data` ficheiro de script semelhante à abaixo:

```python
automl_config = AutoMLConfig(****, data_script=project_folder + "/get_data.py", **** )
```

`get_data` pode retornar o script:

Chave | Tipo | Mutuamente exclusivo com    | Descrição
---|---|---|---
X | Pandas Dataframe ou matriz de Numpy | data_train, etiqueta, colunas |  Todas as funcionalidades para treinar com
Y | Pandas Dataframe ou matriz de Numpy |   label   | Etiqueta de dados para preparar com. Para classificação, devem ser uma matriz de inteiros.
X_valid | Pandas Dataframe ou matriz de Numpy   | data_train, etiqueta | _Opcional_ dados de que forma o conjunto de validação de recursos. Se não for especificado, X é dividida entre train e validar
y_valid |   Pandas Dataframe ou matriz de Numpy | data_train, etiqueta | _Opcional_ os dados da etiqueta para validar com. Se não for especificado, y é dividida entre train e validar
sample_weight | Pandas Dataframe ou matriz de Numpy |   data_train, etiqueta, colunas| _Opcional_ um valor de peso para cada exemplo. Utilize quando quiser atribuir diferentes pesos para os seus pontos de dados
sample_weight_valid | Pandas Dataframe ou matriz de Numpy | data_train, etiqueta, colunas |    _Opcional_ um valor de peso para cada exemplo de validação. Se não for especificado, sample_weight é dividida entre train e validar
data_train |    Pandas Dataframe |  X, y, X_valid, y_valid |    Todos os dados (funcionalidades + etiqueta) para treinar com
label | cadeia  | X, y, X_valid, y_valid |  A coluna data_train representa a etiqueta
Colunas | matriz de cadeias de caracteres  ||  _Opcional_ lista aprovada de colunas a utilizar para funcionalidades
cv_splits_indices   | Matriz de números inteiros ||  _Opcional_ lista de índices para dividir os dados para cruzada validação

## <a name="train-and-validation-data"></a>Educar e a validação de dados

Pode especificar train separado e validação definida por meio de get_data() ou diretamente no `AutoMLConfig` método.

### <a name="k-folds-cross-validation"></a>K subconjuntos de validação cruzada

Utilize `n_cross_validations` definição para especificar o número de cruzada validações. O conjunto de dados de treinamento será dividido aleatoriamente em `n_cross_validations` subconjuntos de tamanho igual. Durante cada validação cruzada redondo, um dos subconjuntos será utilizado para a validação do modelo com base em com os subconjuntos restantes. Este processo repete-se para `n_cross_validations` Arredonda por excesso até que cada subconjuntos de validação é utilizado uma vez como conjunto de validação. As pontuações média em todos os `n_cross_validations` rodadas serão reportadas e o modelo correspondente será reestruturar os em todo o conjunto de dados de preparação.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo entre validação (amostragem aleatória de secundárias repetida)

Utilizar `validation_size` para especificar a percentagem do conjunto de dados de treinamento que deve ser utilizado para validação e use `n_cross_validations` para especificar o número de cruzada validações. Durante cada uma validação round, um subconjunto de tamanho de cruzada `validation_size` serão selecionados aleatoriamente para a validação do modelo com base em com os dados restantes. Por fim, a média pontua em todos os `n_cross_validations` rodadas serão reportadas e o modelo correspondente será reestruturar os em todo o conjunto de dados de preparação. Monte Carlo não é suportada para previsão de séries de tempo.

### <a name="custom-validation-dataset"></a>Conjunto de dados de validação personalizada

Utilize o conjunto de dados de validação personalizada se dividir aleatório não é aceitável, normalmente, os dados de séries de tempo ou dados desequilibrados. Pode especificar o seu próprio conjunto de dados de validação. O modelo será avaliado contra o conjunto de dados de validação especificado, em vez de um conjunto de dados aleatório.

## <a name="compute-to-run-experiment"></a>Computação para executar a experimentação

Em seguida, determine onde será preparado o modelo. Uma experimentação de preparação de aprendizagem automática, pode executar as seguintes opções de computação:
*   Seu computador local, como uma área de trabalho local ou computador portátil – em geral, quando tem pequeno conjunto de dados e estiver na fase de exploração.
*   Um computador remoto na cloud – [Machine Learning geridos de computação no Azure](concept-compute-target.md#amlcompute) é um serviço gerido que permite a capacidade preparar modelos de machine learning em clusters de máquinas virtuais do Azure.

Consulte a [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) por exemplo o destinos de computação de blocos de notas com o local e remoto.

*   Um cluster do Azure Databricks na sua subscrição do Azure. Pode encontrar mais detalhes aqui – [cluster do programa de configuração do Azure Databricks para o ML automatizada](how-to-configure-environment.md#azure-databricks)

Consulte a [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) como blocos de notas com o Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configurar as definições de experimentação

Existem várias opções que pode utilizar para configurar seu automatizada experimentação do machine learning. Esses parâmetros são definidos pela Instanciação de um `AutoMLConfig` objeto. Consulte a [AutoMLConfig classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) para obter uma lista completa de parâmetros.

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

Os três diferentes `task` valores de parâmetro determinam a lista de algoritmos para aplicar.  Utilize o `whitelist` ou `blacklist` parâmetros para modificar ainda mais iterações com os algoritmos disponíveis para incluir ou excluir. A lista de modelos suportados pode ser encontrada na [SupportedAlgorithms classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py).

### <a name="primary-metric"></a>Métrica primária
A métrica primária; conforme mostrado nos exemplos acima determina a métrica a ser utilizado durante a preparação de modelos para otimização. A métrica primária, que pode selecionar é determinada pelo tipo de tarefa que escolher. Segue-se uma lista de métricas disponíveis.

|Classificação | Regressão | Previsão de série temporal
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-preprocessing--featurization"></a>O pré-processamento de dados & featurization

Em cada automatizada experimentação do machine learning, os seus dados estão [automaticamente dimensionado e normalizados](concept-automated-ml.md#preprocess) para ajudar a algoritmos de bom desempenho.  No entanto, também pode ativar adicional pré-processamento/featurization, como em falta imputation de valores, codificação de mensagens em fila e transformações. [Saiba mais sobre o que featurization está incluído](how-to-create-portal-experiments.md#preprocess).

Para ativar este featurization, especifique `"preprocess": True` para o [ `AutoMLConfig` classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

### <a name="time-series-forecasting"></a>Previsão de série temporal
Para o tipo de tarefa de previsão de série de tempo tem parâmetros adicionais para definir.
1. time_column_name - este é um parâmetro necessário que define o nome da coluna na série de data/hora que contém seu treinamento dados.
1. max_horizon - isso define o período de tempo que pretende prever horizontalmente com base na periodicidade dos dados de treinamento. Por exemplo se tiver dados de treinamento com detalhamento de tempo diária, até que ponto por definir em dias em que quer que o modelo para treinar para.
1. grain_column_names - isso define o nome de colunas que contêm dados de séries de tempo individuais em seus dados de treinamento. Por exemplo, se a previsão de vendas de uma determinada marca pela loja, definiria colunas de arquivo e marca como as colunas de intervalo de agregação.

Ver exemplo dessas definições de que está a ser utilizadas abaixo, o exemplo de bloco de notas está disponível [aqui](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

## <a name="run-experiment"></a>Execute experimentação

ML automatizada terá de criar uma `Experiment` objeto, que é um determinado objeto num `Workspace` utilizada para executar experimentações.

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
Existem algumas opções pode definir para concluir a sua experimentação.
1. Não existem critérios - se não definir qualquer sair parâmetros que a experimentação vai continuar até que nenhum progresso adicional em sua métrica primária.
1. Número de iterações - lhe define o número de iterações de experimentação para execução. Pode opcional adicionar iteration_timeout_minutes para definir um limite de tempo em minutos por cada iteração.
1. Saia do após um período de tempo - usando experiment_timeout_minutes nas definições do que pode definir o período de tempo em minutos uma experimentação deve continuar em execução.
1. Sair quando for alcançada uma pontuação - usando experiment_exit_score que pode optar por executar a experimentação, depois de uma classificação com base em sua métrica principal foi atingida.

### <a name="explore-model-metrics"></a>Explorar métricas de modelo

Pode ver os resultados de treinamento num widget ou inline, se estiver num bloco de notas. Ver [controlar e avaliar modelos](how-to-track-experiments.md#view-run-details) para obter mais detalhes.

## <a name="understand-automated-ml-models"></a>Compreender os modelos de ML automatizados

Qualquer modelo produzido através de ML automatizada inclui os seguintes passos:
+ Automatizada de engenharia de funcionalidades (se pré-processar = True)
+ Dimensionamento/normalização e o algoritmo com valores hypermeter

Podemos torná-lo transparente para obter essas informações a partir da saída fitted_model do ML automatizada.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Engenharia de funcionalidades automatizadas

Ver a lista de pré-processamento e [automatizada de engenharia de funcionalidades](concept-automated-ml.md#preprocess) que acontece quando pré-processar = True.

Considere este exemplo:
+ Há 4 recursos de entrada: Um (numéricos), B (numéricos), C (numéricos), 1!d (DateTime)
+ Funcionalidade numérico C é ignorada porque é uma coluna de ID com todos os valores exclusivos
+ Recursos numéricos A e B tenham valores em falta e, por conseguinte, são imputed pela média
+ O recurso de DateTime D é caracterizadas em 11 diferentes funcionalidades de engenharia

Utilize estes 2 APIs no primeiro passo do modelo ajustado para saber mais.  Ver [este bloco de notas do exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` devolve uma lista de nomes das funcionalidades de engenharia.

  Utilização:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Esta lista inclui todos os nomes de recursos de engenharia.

  >[!Note]
  >Utilizar 'timeseriestransformer' para tarefa = "previsão", utilize outra datatransformer para a tarefa "regressão" ou "classificação".

+ API 2: `get_featurization_summary()` devolve featurization resumo para todas as funcionalidades de entrada.

  Utilização:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Utilizar 'timeseriestransformer' para tarefa = "previsão", utilize outra datatransformer para a tarefa "regressão" ou "classificação".

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
   |TypeDetected|Tipo de dados detetado da funcionalidade de entrada.|
   |Removido|Indica se a funcionalidade de entrada foi removida ou utilizada.|
   |EngineeringFeatureCount|Número de funcionalidades geradas por meio de transformações de engenharia de funcionalidade automatizada.|
   |Transformações|Lista de transformações aplicadas a recursos para gerar a engenharia de funcionalidades de entrada.|

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>Dimensionamento/normalização e o algoritmo com valores hypermeter:

Para compreender os valores de normalização/dimensionamento e o algoritmo/hiper-parâmetros para um pipeline, utilize fitted_model.steps. [Saiba mais sobre dimensionamento/normalização](concept-automated-ml.md#preprocess). Eis uma saída de exemplo:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Para obter mais detalhes, utilize esta função auxiliar mostrada na [este bloco de notas do exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

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
        else:
            pprint(step[1].get_params())
            print()

print_model(fitted_model)
```

Segue-se a saída de exemplo para um pipeline com um algoritmo específico (LogisticRegression com RobustScalar, neste caso).

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

## <a name="explain-the-model-interpretability"></a>Explicar o modelo (interpretability)

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

É possível visualizar o gráfico de importância de funcionalidade na sua área de trabalho no portal do Azure. O gráfico também é mostrado ao utilizar o widget de Jupyter num bloco de notas. Para saber mais sobre os gráficos consulte o [artigo de blocos de notas do serviço de exemplo do Azure Machine Learning.](samples-notebooks.md)

```Python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![gráfico de importância de funcionalidade](./media/how-to-configure-auto-train/feature-importance.png)

Para obter mais informações sobre como explicações de modelo e a importância de recurso podem ser ativadas em outras áreas do SDK fora da aprendizagem automática, consulte a [conceito](machine-learning-interpretability-explainability.md) artigo sobre interpretability.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [como e onde implementar um modelo](how-to-deploy-and-where.md).

Saiba mais sobre [como preparar um modelo de regressão automatizada machine Learning](tutorial-auto-train-models.md) ou [automatizada de como treinar a utilizar o machine learning num recurso remoto](how-to-auto-train-remote.md).
