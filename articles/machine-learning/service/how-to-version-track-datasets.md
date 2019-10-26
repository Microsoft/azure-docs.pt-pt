---
title: Controle de versão do conjunto de
titleSuffix: Azure Machine Learning service
description: Conheça a prática recomendada para a versão de seus conjuntos de valores e como o controle de versão funciona com pipelines do Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: sihhu
ms.reviewer: nibaccam
ms.date: 10/25/2019
ms.custom: ''
ms.openlocfilehash: a361800623796cb3bc26a47c4f8f532503836124
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72902005"
---
# <a name="version-and-track-datasets-in-experiments"></a>Conjuntos de testes de versão e acompanhamento em experimentos

Neste "como", você aprende a controlar a versão e o acompanhamento de conjuntos de Azure Machine Learning para reprodução. O controle de versão do conjunto de dados é uma maneira de indicar o estado de seus dados, de modo que você pode aplicar uma versão específica do DataSet para experimentos futuros.

Cenários típicos a serem considerados para controle de versão:

* Quando novos dados estão disponíveis para novo treinamento.
* Ao aplicar as abordagens de preparação de dados ou de engenharia de recursos diferentes.

## <a name="prerequisites"></a>Pré-requisitos

Para este "como", você precisa:

- O [SDK do Azure Machine Learning para Python instalado](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), que inclui o pacote de conjuntos de linhas do [azureml](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py) .
    
- Um [espaço de trabalho Azure Machine Learning](concept-workspace.md). Recupere um existente com o código a seguir ou [crie um novo espaço de trabalho](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- Um [conjunto de Azure Machine Learning](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Registrar e recuperar versões do conjunto de registros

O registro de um conjunto de registros permite que você proscreva, reutilize e compartilhe entre experimentos e colegas. Você pode registrar vários conjuntos de registros com o mesmo nome e recuperar uma versão específica por nome e número de versão.

### <a name="register-a-dataset-version"></a>Registrar uma versão do conjunto de registros

O código a seguir registra uma nova versão do conjunto de `titanic_ds`, definindo o parâmetro `create_new_version` como `True`. Se não houver nenhum `titanic_ds` registrado com o espaço de trabalho, ele criará um novo conjunto de um com o nome `titanic_ds` e definirá sua versão como 1.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>Recuperar um conjunto de um DataSet por nome

Por padrão, o método [get_by_name ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) na classe `Dataset` retorna a versão mais recente do conjunto de informações registrado com o espaço de trabalho. 

O código a seguir obtém a versão 1 do conjunto de `titanic_ds` DataSet.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Prática recomendada de controle de versão

Ao criar uma versão de conjunto de dados, você **não** está criando uma cópia extra de data com o espaço de trabalho. DataSets são referências aos dados em seu serviço de armazenamento, portanto, você tem apenas uma única fonte de verdade gerenciada pelo seu serviço de armazenamento. 

>[!IMPORTANT]
> Se os dados referenciados pelo DataSet forem substituídos ou excluídos, chamar uma versão específica do DataSet não poderá reverter a alteração. 

Ao carregar dados de um DataSet, ele sempre carregará o conteúdo de dados atual referenciado pelo DataSet. Se você quiser garantir o reprodução de cada versão do conjunto de dados, recomendamos que você não modifique o conteúdo do dado referenciado pela versão do DataSet. Quando novos dados chegam, salve os novos arquivos de dados em uma pasta de dados separada e crie uma nova versão de conjunto de dados para incluir a data dessa nova pasta de dados.

A imagem e o código de exemplo a seguir mostram a maneira recomendada para estruturar suas pastas de dados e criar versões de conjuntos de dado que fazem referência a essas pastas.

![Estrutura de pastas](media/how-to-version-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-a-pipeline-output-dataset"></a>Versão um conjunto de uma saída de pipeline

Você pode usar o conjunto de dados como entrada e saída de cada etapa de pipeline do ML (aprendizado de máquina). Quando você executa novamente os pipelines, a saída de cada etapa de pipeline será registrada como uma nova versão do conjunto de resultados. 

Como os pipelines ML preenchem a saída de cada etapa em uma nova pasta toda vez que o pipeline é executado novamente, os conjuntos de resultados de saída com versão são reproduzíveis.

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>Acompanhar conjuntos de os testes em experimentos

Para cada experimento de aprendizado de máquina, você pode facilmente rastrear os conjuntos de dados usados como entrada por meio do objeto de `Run` do modelo registrado.

Use o código a seguir para registrar modelos com conjuntos de valores.

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Após o registro, você poderá ver a lista de modelos registrados com o conjunto de registros usando o Python ou a [página de aterrissagem do espaço de trabalho](https://ml.azure.com/).

O código a seguir usa o método [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) para controlar quais conjuntos de dados de entrada foram usados com essa execução de experimento.

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
train_dataset = inputs[0]['dataset']

# list the files referenced by train_dataset
train_dataset.to_path()
```

Você também pode encontrar o `input_datasets` de experimentos usando a [página de aterrissagem do espaço de trabalho (versão prévia)](https://ml.azure.com/). 

A imagem a seguir mostra onde encontrar o conjunto de dados de entrada de um experimento na página de aterrissagem do espaço de trabalho. Para este exemplo, navegue até o painel **experimentos** e abra a guia **Propriedades** para uma execução específica de seu experimento, `keras-mnist`. 

![Conjuntos de dados de entrada](media/how-to-version-datasets/input-datasets.png)

Você também pode encontrar os modelos que usaram seu conjunto de seus conjuntos de espaço de trabalho. A exibição a seguir é da folha datasets em ativos. Selecione o conjunto de um e navegue até a guia modelos para obter uma lista dos modelos usando esse conjunto de tais. 

![Modelos de conjuntos de dados de entrada](media/how-to-version-datasets/dataset-models.png)

## <a name="next-steps"></a>Passos seguintes

* [Treine com conjuntos de valores](how-to-train-with-datasets.md).
* [Mais blocos de anotações de exemplo de conjuntos de](https://aka.ms/dataset-tutorial)informações.
