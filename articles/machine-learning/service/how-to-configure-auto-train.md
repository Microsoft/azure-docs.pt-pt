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
ms.date: 01/08/2019
ms.custom: seodec18
ms.openlocfilehash: 79d013e5836555547cbf254bb25c06add0a717e1
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295390"
---
# <a name="configure-automated-machine-learning-experiments"></a>Configurar automatizada experimentações de machine learning

Aprendizagem automática escolhe um algoritmo e hiperparâmetros para e gera um modelo pronto para implantação. Existem várias opções que pode utilizar para configurar automatizada experimentações de machine learning. Neste guia, saiba como definir várias definições de configuração.

Para ver exemplos de um automatizada experimentações de machine learning, consulte [Tutorial: Preparar um modelo de classificação automatizada de machine Learning](tutorial-auto-train-models.md) ou [formar modelos com aprendizagem automática na cloud](how-to-auto-train-remote.md).

Opções de configuração disponíveis no automatizada de machine learning:

* Selecione o tipo de experimentação: Classificação, regressão ou de previsão
* Origem de dados, formatos e obtenção de dados
* Escolher o destino de computação: local ou remoto
* Aprendizagem automatizada as definições de experimentação
* Executar uma automatizada experimentação do machine learning
* Explorar métricas de modelo
* Registe e implemente o modelo

## <a name="select-your-experiment-type"></a>Selecione o tipo de experimentação
Antes de começar a sua experimentação, deve determinar o tipo de problema de aprendizado de máquina que está a resolver. Automatizada de machine learning suporta tipos de tarefa de classificação, regressão e previsão.

Enquanto as capacidades de aprendizagem de máquina automáticos estão disponíveis em geral, **previsão ainda está em pré-visualização pública.**

Automatizada de machine learning suporta os seguintes algoritmos durante a automação e o processo de otimização. Como um utilizador, não é necessário para especificar o algoritmo.

Classificação | Regressão | Previsão
|-- |-- |--
[Regressão logística](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Net elástico](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Net elástico](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Descendente gradação stochastic gradient (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[A classificação bayesiana Ingênua](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[Aumentam a gradação](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Aumentam a gradação](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Classificação de Vetores de suporte de C (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Árvore de decisão](https://scikit-learn.org/stable/modules/tree.html#regression)|[Árvore de decisão](https://scikit-learn.org/stable/modules/tree.html#regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[K mais próximo de vizinhos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K mais próximo de vizinhos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[K mais próximo de vizinhos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Árvore de decisão](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Descendente gradação stochastic gradient (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Descendente gradação stochastic gradient (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Árvores extremamente aleatório](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Árvores extremamente aleatório](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Árvores extremamente aleatório](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Aumentam a gradação](https://scikit-learn.org/stable/modules/ensemble.html#classification)|
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|


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
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
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

Chave | Tipo |    Mutuamente exclusivo com | Descrição
---|---|---|---
X | Pandas Dataframe ou matriz de Numpy | data_train, etiqueta, colunas |  Todas as funcionalidades para treinar com
Y | Pandas Dataframe ou matriz de Numpy |   label   | Etiqueta de dados para preparar com. Para classificação, devem ser uma matriz de inteiros.
X_valid | Pandas Dataframe ou matriz de Numpy   | data_train, etiqueta | _Opcional_ todas as funcionalidades para validar com. Se não for especificado, X é dividida entre train e validar
y_valid |   Pandas Dataframe ou matriz de Numpy | data_train, etiqueta | _Opcional_ os dados da etiqueta para validar com. Se não for especificado, y é dividida entre train e validar
sample_weight | Pandas Dataframe ou matriz de Numpy |   data_train, etiqueta, colunas| _Opcional_ um valor de peso para cada exemplo. Utilize quando quiser atribuir diferentes pesos para os seus pontos de dados
sample_weight_valid | Pandas Dataframe ou matriz de Numpy | data_train, etiqueta, colunas |    _Opcional_ um valor de peso para cada exemplo de validação. Se não for especificado, sample_weight é dividida entre train e validar
data_train |    Pandas Dataframe |  X, y, X_valid, y_valid |    Todos os dados (funcionalidades + etiqueta) para treinar com
label | cadeia  | X, y, X_valid, y_valid |  A coluna data_train representa a etiqueta
Colunas | matriz de cadeias de caracteres  ||  _Opcional_ lista aprovada de colunas a utilizar para funcionalidades
cv_splits_indices   | Matriz de números inteiros ||  _Opcional_ lista de índices para dividir os dados para cruzada validação

### <a name="load-and-prepare-data-using-dataprep-sdk"></a>Carregar e preparar dados com o SDK de DataPrep
Automatizada experimentações de machine learning suporta o carregamento de dados e transformações com o SDK de dataprep. Com o SDK fornece a capacidade de

>* Carregar a partir de muitos tipos de ficheiro com a inferência de tipos de parâmetro (codificação, separador, cabeçalhos) de análise
>* Conversão de tipo usando inferência durante o carregamento de ficheiro
>* Suporte de ligação para o MS SQL Server e o armazenamento do Azure Data Lake
>* Adicionar coluna utilização de uma expressão
>* Impute valores em falta
>* Derivar coluna por exemplo
>* Filtragem
>* Transformações de Python personalizadas

Para saber mais sobre os dados de preparação sdk fazer referência a [como preparar dados para modelar artigo](how-to-load-data.md).
Segue-se um exemplo de carregamento de dados com o sdk de preparação de dados.
```python
# The data referenced here was pulled from `sklearn.datasets.load_digits()`.
simple_example_data_root = 'https://dprepdata.blob.core.windows.net/automl-notebook-data/'
X = dprep.auto_read_file(simple_example_data_root + 'X.csv').skip(1)  # Remove the header row.
# You can use `auto_read_file` which intelligently figures out delimiters and datatypes of a file.

# Here we read a comma delimited file and convert all columns to integers.
y = dprep.read_csv(simple_example_data_root + 'y.csv').to_long(dprep.ColumnSelector(term='.*', use_regex = True))
```

## <a name="train-and-validation-data"></a>Educar e a validação de dados

Pode especificar train separado e validação definida por meio de get_data() ou diretamente no `AutoMLConfig` método.

## <a name="cross-validation-split-options"></a>Opções de divisão de validação cruzada

### <a name="k-folds-cross-validation"></a>K subconjuntos de validação cruzada

Utilize `n_cross_validations` definição para especificar o número de cruzada validações. O conjunto de dados de treinamento será dividido aleatoriamente em `n_cross_validations` subconjuntos de tamanho igual. Durante cada validação cruzada redondo, um dos subconjuntos será utilizado para a validação do modelo com base em com os subconjuntos restantes. Este processo repete-se para `n_cross_validations` Arredonda por excesso até que cada subconjuntos de validação é utilizado uma vez como conjunto de validação. As pontuações média em todos os `n_cross_validations` rodadas serão reportadas e o modelo correspondente será reestruturar os em todo o conjunto de dados de preparação.

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>Monte Carlo (também conhecido como validação cruzada Amostragem de secundárias aleatória repetida)

Utilizar `validation_size` para especificar a percentagem do conjunto de dados de treinamento que deve ser utilizado para validação e use `n_cross_validations` para especificar o número de cruzada validações. Durante cada uma validação round, um subconjunto de tamanho de cruzada `validation_size` serão selecionados aleatoriamente para a validação do modelo com base em com os dados restantes. Por fim, a média pontua em todos os `n_cross_validations` rodadas serão reportadas e o modelo correspondente será reestruturar os em todo o conjunto de dados de preparação.

### <a name="custom-validation-dataset"></a>Conjunto de dados de validação personalizada

Utilize o conjunto de dados de validação personalizada se dividir aleatório não é aceitável (normalmente de tempo os dados de série ou desequilibrado). Pode especificar o seu próprio conjunto de dados de validação. O modelo será avaliado contra o conjunto de dados de validação especificado, em vez de um conjunto de dados aleatório.

## <a name="compute-to-run-experiment"></a>Computação para executar a experimentação

Em seguida, determine onde será preparado o modelo. Uma experimentação de preparação de aprendizagem automática, pode executar as seguintes opções de computação:
*   Seu computador local, como uma área de trabalho local ou computador portátil – em geral, quando tem pequeno conjunto de dados e estiver na fase de exploração.
*   Um computador remoto na cloud – [Machine Learning geridos de computação no Azure](concept-azure-machine-learning-architecture.md#managed-and-unmanaged-compute-targets) é um serviço gerido que permite a capacidade preparar modelos de machine learning em clusters de máquinas virtuais do Azure.

Consulte a [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/automl) por exemplo o destinos de computação de blocos de notas com o local e remoto.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configurar as definições de experimentação

Existem várias opções que pode utilizar para configurar seu automatizada experimentação do machine learning. Esses parâmetros são definidos pela Instanciação de um `AutoMLConfig` objeto.

Alguns exemplos incluem:

1.  Experimentação de classificação usando AUC ponderada como a métrica primária com tempo máximo de 12 000 segundos por iteração, com a experimentação para terminar depois de 50 iterações e 2 subconjuntos de validação cruzada.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
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
        primary_metric='r2_score',
        X=X,
        y=y,
        n_cross_validations=5)
    ```

Existem três diferentes `task` valores de parâmetros, que determinam a lista de algoritmos para aplicar.  Utilize o `whitelist` ou `blacklist` parâmetros para modificar ainda mais iterações com os algoritmos disponíveis para incluir ou excluir.
* Classificação
    * LogisticRegression
    * SGD
    * MultinomialNaiveBayes
    * BernoulliNaiveBayes
    * SVM
    * LinearSVM
    * KNN
    * DecisionTree
    * RandomForest
    * ExtremeRandomTrees
    * LightGBM
    * GradientBoosting
    * TensorFlowDNN
    * TensorFlowLinearClassifier
* Regressão
    * ElasticNet
    * GradientBoosting
    * DecisionTree
    * KNN
    * LassoLars
    * SGD 
    * RandomForest
    * ExtremeRandomTree
    * LightGBM
    * TensorFlowLinearRegressor
    * TensorFlowDNN
* Previsão
    * ElasticNet
    * GradientBoosting
    * DecisionTree
    * KNN
    * LassoLars
    * SGD 
    * RandomForest
    * ExtremeRandomTree
    * LightGBM
    * TensorFlowLinearRegressor
    * TensorFlowDNN

Consulte a [AutoMLConfig classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) para obter uma lista completa de parâmetros.  

## <a name="data-pre-processing-and-featurization"></a>Processamento prévio de dados e featurization

Se usar `preprocess=True`, os seguintes passos de pré-processamento de dados são efetuados automaticamente para:
1.  Remova cardinalidade elevada ou nenhum recurso de variância
    * Remova funcionalidades sem informações úteis de conjuntos de formação e validação. Estes incluem recursos com todos os valores em falta, mesmo valor em todas as linhas ou com uma cardinalidade muito elevada (por exemplo, hashes, IDs ou GUIDs).
1.  Imputation de valor em falta
    *   De funcionalidades numéricas, impute valores em falta com a média dos valores na coluna.
    *   Para obter recursos categóricos, impute valores em falta com o valor mais frequente.
1.  Gerar recursos adicionais
    * Para as funcionalidades de DateTime: Ano, mês, dia, dia da semana, dia do ano, trimestre, semana do ano, hora, minuto, segundo.
    * Para as funcionalidades de texto: Frequência de prazo com base em vectorizer de contagem de unigram, bi-grams e chamamos-gram, do word.
1.  Transformações e codificações
    * Recursos de numérico com poucos valores exclusivos, transformados em funcionalidades categóricas.
    * Consoante a cardinalidade dos recursos categóricos, execute etiqueta codificação ou (hash) frequente de uma codificação.

## <a name="run-experiment"></a>Execute experimentação

Submeta a experimentação para executar e gerar um modelo. Passar o `AutoMLConfig` para o `submit` método para gerar o modelo.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>As dependências são instaladas pela primeira vez numa máquina nova.  Pode demorar até 10 minutos antes de saída é mostrada.
>A definição `show_output` para `True` resulta na saída a ser apresentada na consola.


## <a name="explore-model-metrics"></a>Explorar métricas de modelo
Pode ver os resultados num widget ou inline, se estiver num bloco de notas. Ver [controlar e avaliar modelos](how-to-track-experiments.md#view-run-details) para obter mais detalhes.


### <a name="classification-metrics"></a>Métricas de classificação
As métricas seguintes são salvas em cada iteração de uma tarefa de classificação.

|Métrica primária|Descrição|Cálculo|Parâmetros adicionais
--|--|--|--|
AUC_Macro| AUC é a área sob a curva de característica do Recetor operacional. Macro é a média aritmética de AUC para cada classe.  | [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | média = "macro"|
AUC_Micro| AUC é a área sob a curva de característica do Recetor operacional. Micro é computada globalmente ao combinar os positivos verdadeiros e falsos positivos de cada classe| [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | média = "micro"|
AUC_Weighted  | AUC é a área sob a curva de característica do Recetor operacional. Ponderada é a média aritmética de classificação para cada classe, ponderada pelo número de instâncias verdadeiros em cada classe| [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|média = "ponderado"
accuracy|A precisão é a porcentagem de etiquetas previstas que correspondam exatamente com as etiquetas verdadeiras. |[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Nenhuma|
average_precision_score_macro|Precisão média resume uma curva de recolhimento de precisão como a média ponderada de precisions alcançados em cada limite, com o aumento de Lembre-se de que o limiar anterior utilizado como o peso. Macro é a média aritmética da pontuação média de precisão de cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|média = "macro"|
average_precision_score_micro|Precisão média resume uma curva de recolhimento de precisão como a média ponderada de precisions alcançados em cada limite, com o aumento de Lembre-se de que o limiar anterior utilizado como o peso. Micro é computada globalmente com o combinam os positivos verdadeiros e falsos positivos em cada limite|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|média = "micro"|
average_precision_score_weighted|Precisão média resume uma curva de recolhimento de precisão como a média ponderada de precisions alcançados em cada limite, com o aumento de Lembre-se de que o limiar anterior utilizado como o peso. Ponderada é a média aritmética da pontuação média de precisão para cada classe, ponderada pelo número de instâncias verdadeiros em cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|média = "ponderado"|
balanced_accuracy|Precisão equilibrada é a média aritmética de remoção para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média = "macro"|
f1_score_macro|F1 pontuação é a média harmônica de precisão e lembre-se. Macro é a média aritmética de pontuação F1 para cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|média = "macro"|
f1_score_micro|F1 pontuação é a média harmônica de precisão e lembre-se. Micro é computada globalmente por contagem de total positivos verdadeiros, falsos negativos e falsos positivos|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|média = "micro"|
f1_score_weighted|F1 pontuação é a média harmônica de precisão e lembre-se. Média ponderada pela frequência de classe de pontuação F1 para cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|média = "ponderado"|
log_loss|Esta é a função de perda utilizada regressão logística de (multinomial) e extensões do mesmo como redes neurais, definidas como a probabilidade de registo de negativa das etiquetas verdadeiras tendo em conta as previsões de um classificador probabilístico. Para obter um exemplo único com true Etiquetar yt na {0,1} e estimado probabilidade yp esse yt = 1, a perda de log é – P de registo (yt&#124;yp) =-(yt log(yp) + (1 - yt) registo (1 - yp))|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Nenhuma|
norm_macro_recall|Normalizado Macro Lembre-se de é lembrar de Macro normalizados de modo a que o desempenho aleatório tem uma pontuação igual a 0 e desempenho perfeito tem uma pontuação de 1. Isto é conseguido ao norm_macro_recall: = (recall_score_macro - R) /(1-R), em que o R é o valor esperado de recall_score_macro para predições aleatórias (ou seja, R = 0,5 para classificação binária) e R=(1/C) para problemas de classificação de classe C|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|média = "macro" e, em seguida, (recall_score_macro - R) /(1-R), em que o R é o valor esperado de recall_score_macro para predições aleatórias (ou seja, R = 0,5 para classificação binária) e R=(1/C) para problemas de classificação de classe C|
precision_score_macro|Precisão é a percentagem de elementos identificados como uma determinada classe, que, na verdade, estão nessa classe. Macro é a média aritmética de precisão para cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|média = "macro"|
precision_score_micro|Precisão é a percentagem de elementos identificados como uma determinada classe, que, na verdade, estão nessa classe. Micro é computada globalmente por contagem de total positivos verdadeiros e falsos positivos|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|média = "micro"|
precision_score_weighted|Precisão é a percentagem de elementos identificados como uma determinada classe, que, na verdade, estão nessa classe. Ponderada é a média aritmética de precisão para cada classe, ponderado por número de instâncias verdadeiros em cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|média = "ponderado"|
recall_score_macro|Lembre-se é a percentagem de elementos, na verdade, numa determinada classe, que estão identificadas corretamente. Macro é a média aritmética de remoção para cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média = "macro"|
recall_score_micro|Lembre-se é a percentagem de elementos, na verdade, numa determinada classe, que estão identificadas corretamente. Micro é computada globalmente por contagem de total positivos verdadeiros, falsos negativos|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média = "micro"|
recall_score_weighted|Lembre-se é a percentagem de elementos, na verdade, numa determinada classe, que estão identificadas corretamente. Ponderada é a média aritmética de remoção para cada classe, ponderado por número de instâncias verdadeiros em cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média = "ponderado"|
weighted_accuracy|Precisão ponderada é precisão em que o peso para cada exemplo é igual a proporção de instâncias verdadeiras na classe de verdadeiro esse exemplo|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight é um vetor igual a proporção dessa classe para cada elemento no destino|

### <a name="regression-and-forecasting-metrics"></a>Regressão e métricas de previsão
As métricas seguintes são salvas em cada iteração para uma regressão ou a tarefa de previsão.

|Métrica primária|Descrição|Cálculo|Parâmetros adicionais
--|--|--|--|
explained_variance|Variância explicada é a proporção ao qual um modelo matemático contas para a variação de um determinado conjunto de dados. É que a percentagem de diminuir a variação dos dados originais para a variância dos erros. Quando a média dos erros for 0, é igual a variância explicada.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Nenhuma|
r2_score|R2 é o coeficiente de determinação ou a redução de percentagem de erros ao quadrado, em comparação comparada um modelo de linha de base que produz a média. Quando a média dos erros for 0, é igual a variância explicada.|[Cálculo](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Nenhuma|
spearman_correlation|Correlação de Spearman é uma medida nonparametric de monotonicidade da relação entre dois conjuntos de dados. Ao contrário de correlação Pearson, a correlação de Spearman não partem do princípio de que ambos os conjuntos de dados normalmente são distribuídos. Como outros coeficientes de correlação, isso varia entre -1 e + 1 com 0 indicando sem correlação. Correlações de -1 ou + 1 implicam uma relação de monotónica exata. Correlações positivas implicam que como x aumenta, assim como o y. Correlações negativas implicam que como x aumenta, y diminui.|[Cálculo](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Nenhuma|
mean_absolute_error|Significa erro absoluto é o valor esperado de valor absoluto de diferença entre o destino e a predição|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Nenhuma|
normalized_mean_absolute_error|Erro de absoluto mean normalizado é mean erro absoluto dividida pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Divida por intervalo dos dados|
median_absolute_error|Erro de absoluto mediano é o valor mediano de todas as diferenças absolutas entre o destino e a predição. Esta perda é robusta para valores atípicos.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Nenhuma|
normalized_median_absolute_error|Erro de absoluto mediano normalizado é mediano erro absoluto dividido pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Divida por intervalo dos dados|
root_mean_squared_error|Raiz significar um erro ao quadrado é a raiz quadrada da diferença ao quadrado esperada entre o destino e a predição|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Nenhuma|
normalized_root_mean_squared_error|Raiz normalizado significar um erro ao quadrado é raiz mean erros dividido pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Divida por intervalo dos dados|
root_mean_squared_log_error|Raiz significa de erro do registo ao quadrado é a raiz quadrada do erro logarítmica ao quadrado esperado|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Nenhuma|
normalized_root_mean_squared_log_error|Erro de registo ao quadrado mean normalizado raiz é dividido pelo intervalo dos dados de erro do registo ao quadrado médio de raiz|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Divida por intervalo dos dados|

## <a name="explain-the-model"></a>Explicar o modelo

Enquanto as capacidades de aprendizagem de máquina automáticos estão disponíveis em geral, **a modelo explainability funcionalidade ainda está em pré-visualização pública.**

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

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![gráfico de importância de funcionalidade](./media/how-to-configure-auto-train/feature-importance.png)

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [como e onde implementar um modelo](how-to-deploy-and-where.md).

Saiba mais sobre [como preparar um modelo de regressão automatizada machine Learning](tutorial-auto-train-models.md) ou [automatizada de como treinar a utilizar o machine learning num recurso remoto](how-to-auto-train-remote.md).
