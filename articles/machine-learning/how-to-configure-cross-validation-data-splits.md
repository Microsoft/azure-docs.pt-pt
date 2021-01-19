---
title: Divisões de dados e validação cruzada na aprendizagem automática de máquinas
titleSuffix: Azure Machine Learning
description: Saiba como configurar divisões de conjuntos de dados e validação cruzada para experiências automatizadas de aprendizagem automática de máquinas
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 06/16/2020
ms.openlocfilehash: 8e749e5f6ea6bcf76a1b4f143bce03ceb41cbb07
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2021
ms.locfileid: "98573297"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>Configurar divisões de dados e validação cruzada no machine learning automatizado

Neste artigo, aprende-se as diferentes opções para configurar divisões de dados de treino/validação e validação cruzada para a sua aprendizagem automática de máquinas, ML automatizado, experiências.

No Azure Machine Learning, quando utiliza ML automatizado para construir vários modelos ML, cada corrida infantil precisa de validar o modelo relacionado calculando as métricas de qualidade para esse modelo, como precisão ou AUC ponderado. Estas métricas são calculadas comparando as previsões feitas com cada modelo com rótulos reais de observações anteriores nos dados de validação. [Saiba mais sobre como as métricas são calculadas com base no tipo de validação.](#metric-calculation-for-cross-validation-in-machine-learning) 

Experiências automatizadas de ML realizam validação de modelos automaticamente. As seguintes secções descrevem como pode personalizar ainda mais as definições de validação com o [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py). 

Para obter uma experiência de baixo código ou sem código, consulte [Criar as suas experiências automatizadas de aprendizagem automática de máquinas no estúdio Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md). 

> [!NOTE]
> O estúdio suporta atualmente divisões de dados de formação/validação e opções de validação cruzada, mas não suporta especificar ficheiros de dados individuais para o seu conjunto de validação. 

## <a name="prerequisites"></a>Pré-requisitos

Para este artigo que precisa,

* Uma área de trabalho do Azure Machine Learning. Para criar o espaço de trabalho, consulte Criar um espaço de trabalho para [aprendizagem de máquinas Azure.](how-to-manage-workspace.md)

* Familiaridade com a criação de uma experiência automatizada de aprendizagem automática com o Azure Machine Learning SDK. Siga o [tutorial](tutorial-auto-train-models.md) ou [como](how-to-configure-auto-train.md) ver os padrões fundamentais de design de experiências de aprendizagem automática de máquinas.

* A compreensão dos dados de comboio/validação divide-se e validação cruzada como conceitos de machine learning. Para uma explicação de alto nível,

    * [Sobre conjuntos de comboios, validação e testes em machine learning](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [Compreender validação cruzada na aprendizagem automática](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd) 

## <a name="default-data-splits-and-cross-validation-in-machine-learning"></a>Os dados predefinidos dividem-se e validam-se cruzadamente na aprendizagem automática

Utilize o objeto [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?preserve-view=true&view=azure-ml-py) para definir as definições de experiência e treino. No seguinte corte de código, note que apenas os parâmetros necessários são definidos, que são os parâmetros para `n_cross_validation` ou `validation_ data` **não** estão incluídos.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

Se não especificar explicitamente um `validation_data` ou `n_cross_validation` parâmetro, o AutoML aplica técnicas predefinidas dependendo do número de linhas no conjunto de dados `training_data` único fornecido:

|Tamanho dos &nbsp; dados de formação &nbsp;| Técnica de validação |
|---|-----|
|**Maior &nbsp; que &nbsp; 20.000 &nbsp; linhas**| É aplicada a divisão de dados de comboio/validação. O padrão é tomar 10% do conjunto de dados de formação inicial como o conjunto de validação. Por sua vez, este conjunto de validação é usado para o cálculo de métricas.
|**Menores &nbsp; de &nbsp; 20.000 &nbsp; filas**| Aplica-se uma abordagem de validação cruzada. O número predefinido de dobras depende do número de linhas. <br> **Se o conjunto de dados for inferior a 1.000 linhas,** são utilizadas 10 dobras. <br> **Se as linhas estiverem entre 1.000 e 20.000,** então são usadas três dobras.

## <a name="provide-validation-data"></a>Fornecer dados de validação

Neste caso, pode começar com um único ficheiro de dados e dividi-lo em conjuntos de treino e validação ou pode fornecer um ficheiro de dados separado para o conjunto de validação. De qualquer forma, o `validation_data` parâmetro do seu objeto atribui quais os `AutoMLConfig` dados a utilizar como o seu conjunto de validação. Este parâmetro apenas aceita conjuntos de dados sob a forma de um conjunto de [dados de aprendizagem de máquinas Azure](how-to-create-register-datasets.md) ou de pandas.   

O exemplo de código que se segue define explicitamente qual a parte dos dados fornecidos `dataset` a utilizar para treino e validação.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>Fornecer tamanho de conjunto de validação

Neste caso, apenas um conjunto de dados é fornecido para a experiência. Ou seja, o `validation_data` parâmetro **não** é especificado, e o conjunto de dados fornecido é atribuído ao  `training_data` parâmetro.  No seu `AutoMLConfig` objeto, pode definir o `validation_size` parâmetro para reter uma parte dos dados de treino para validação. Isto significa que o conjunto de validação será dividido por AutoML a partir do `training_data` fornecido inicial. Este valor deve ser entre 0,0 e 1.0 não inclusivo (por exemplo, 0,2 significa que 20% dos dados são guardados para dados de validação).

Consulte o seguinte exemplo de código:

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="set-the-number-of-cross-validations"></a>Definir o número de validações cruzadas

Para realizar a validação cruzada, inclua o `n_cross_validations` parâmetro e desa cota-o para um valor. Este parâmetro define quantas validações cruzadas devem ser executadas, com base no mesmo número de dobras.

No código seguinte, são definidas cinco dobras para validação cruzada. Assim, cinco treinos diferentes, cada treino utilizando 4/5 dos dados, e cada validação usando 1/5 dos dados com uma dobra de retenção diferente de cada vez.

Como resultado, as métricas são calculadas com a média das cinco métricas de validação.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>Especificar dobras de dados de validação cruzada personalizadas

Também pode fornecer as suas próprias dobras de dados de validação cruzada (CV). Este é considerado um cenário mais avançado porque está a especificar quais colunas dividir e usar para validação.  Inclua colunas de divisão cv personalizadas nos seus dados de treino e especifique quais colunas ao povoar os nomes das colunas no `cv_split_column_names` parâmetro. Cada coluna representa uma divisão de validação cruzada, e é preenchida com valores inteiros 1 ou 0-- onde 1 indica que a linha deve ser usada para o treino e 0 indica que a linha deve ser usada para validação.

O seguinte código snippet contém dados de marketing bancário com duas colunas de cv divididas 'cv1' e 'cv2'.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> Para utilizar `cv_split_column_names` `training_data` `label_column_name` e, por favor, atualize a sua versão Azure Machine Learning Python SDK 1.6.0 ou mais tarde. Para versões SDK anteriores, consulte a utilização `cv_splits_indices` , mas note que é utilizado apenas com a entrada de conjunto de `X` `y` dados. 


## <a name="metric-calculation-for-cross-validation-in-machine-learning"></a>Cálculo métrico para validação cruzada na aprendizagem automática

Quando a validação cruzada de K-fold ou Monte Carlo é utilizada, as métricas são calculadas em cada dobra de validação e, em seguida, agregadas. A operação de agregação é uma média para métricas escalar e uma soma para gráficos. As métricas calculadas durante a validação cruzada baseiam-se em todas as dobras e, portanto, em todas as amostras do conjunto de treino. [Saiba mais sobre métricas na aprendizagem automática de máquinas.](how-to-understand-automated-ml.md)

Quando um conjunto de validação personalizado ou um conjunto de validação selecionado automaticamente são utilizados, as métricas de avaliação do modelo são calculadas a partir apenas desse conjunto de validação, e não dos dados de treino.

## <a name="next-steps"></a>Passos seguintes

* [Evitar dados desequilibrados e sobremontagem](concept-manage-ml-pitfalls.md).
* [Tutorial: Utilize machine learning automatizado para prever tarifas de táxi - Secção de dados divididos](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets).
* Como [treinar automaticamente um modelo de previsão de séries tempor assual .](how-to-auto-train-forecast.md)
