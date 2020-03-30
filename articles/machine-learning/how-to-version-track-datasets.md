---
title: Versão de dataset
titleSuffix: Azure Machine Learning
description: Saiba como ver melhor os seus conjuntos de dados e como a versão funciona com pipelines de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.custom: ''
ms.openlocfilehash: acbd2e3ba756255cbc69ae8a7b7ad62d7a1c1c5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528477"
---
# <a name="version-and-track-datasets-in-experiments"></a>Versão e conjuntos de dados de rastreio em experiências
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprenderá a versão e rastreará conjuntos de dados do Azure Machine Learning para a reprodutibilidade. A versão dataset é uma forma de marcar o estado dos seus dados para que possa aplicar uma versão específica do conjunto de dados para futuras experiências.

Cenários de versão típicos:

* Quando novos dados estiverem disponíveis para reconversão
* Quando estiver a aplicar diferentes abordagens de preparação de dados ou de engenharia de recursos

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, você precisa:

- [Azure Machine Learning SDK para Python instalado](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). Este SDK inclui o pacote [de conjuntos de dados em azureml.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py)
    
- Um [espaço de trabalho azure machine learning.](concept-workspace.md) Recupere um existente executando o seguinte código, ou [crie um novo espaço](how-to-manage-workspace.md)de trabalho .

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- Um conjunto de [dados de aprendizagem automática azure](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Registar e recuperar versões de conjunto de dados

Ao registar um conjunto de dados, pode versão, reutilizar e partilhá-lo em experiências e colegas. Pode registar vários conjuntos de dados com o mesmo nome e recuperar uma versão específica por nome e número de versão.

### <a name="register-a-dataset-version"></a>Registar uma versão dataset

O código seguinte regista uma `titanic_ds` nova versão do `create_new_version` conjunto `True`de dados, definindo o parâmetro para . Se não houver um `titanic_ds` conjunto de dados existente registado no espaço de trabalho, o código cria um novo conjunto de dados com o nome `titanic_ds` e define a sua versão para 1.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```
Também pode registar uma nova versão de um conjunto de dados em 

### <a name="retrieve-a-dataset-by-name"></a>Recuperar um conjunto de dados pelo nome

Por predefinição, o método `Dataset` [get_by_name()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) da classe devolve a versão mais recente do conjunto de dados registado no espaço de trabalho. 

O código seguinte obtém `titanic_ds` a versão 1 do conjunto de dados.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Verete as melhores práticas

Quando cria uma versão dataset, *não* está a criar uma cópia extra de dados com o espaço de trabalho. Como os conjuntos de dados são referências aos dados do seu serviço de armazenamento, tem uma única fonte de verdade, gerida pelo seu serviço de armazenamento.

>[!IMPORTANT]
> Se os dados referenciados pelo seu conjunto de dados forem substituídos ou eliminados, a chamada para uma versão específica do conjunto de dados *não* reverte a alteração.

Quando carrega dados a partir de um conjunto de dados, o conteúdo de dados atual referenciado pelo conjunto de dados é sempre carregado. Se pretender certificar-se de que cada versão do conjunto de dados é reprodutível, recomendamos que não modifique o conteúdo de dados referenciado pela versão dataset. Quando entrarem novos dados, guarde novos ficheiros de dados numa pasta de dados separada e, em seguida, crie uma nova versão dataset para incluir dados dessa nova pasta.

O seguinte código de imagem e amostra mostra a forma recomendada de estruturar as suas pastas de dados e de criar versões de conjunto de dados que referenciam essas pastas:

![Estrutura de pasta](./media/how-to-version-track-datasets/folder-image.png)

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

## <a name="version-a-pipeline-output-dataset"></a>Versão um conjunto de dados de saída de gasoduto

Pode utilizar um conjunto de dados como entrada e saída de cada passo do gasoduto Machine Learning. Quando reexecuta os gasodutos, a saída de cada passo do gasoduto é registada como uma nova versão dataset.

Uma vez que os gasodutos de Machine Learning povoam a saída de cada passo para uma nova pasta sempre que o pipeline regere, os conjuntos de dados de saída versão são reprodutíveis. Saiba mais sobre conjuntos de [dados em pipelines.](how-to-create-your-first-pipeline.md#steps)

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>Rastrear conjuntos de dados em experiências

Para cada experiência de Machine Learning, pode rastrear facilmente os `Run` conjuntos de dados utilizados como entrada através do objeto de experiência.

O seguinte código [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) utiliza o método para rastrear quais os conjuntos de dados de entrada utilizados com a execução da experiência:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

Também pode encontrar `input_datasets` as experiências https://ml.azure.com/utilizando . 

A imagem que se segue mostra onde encontrar o conjunto de dados de entrada de uma experiência no estúdio Azure Machine Learning. Para este exemplo, vá ao painel de **experiências** e abra o `keras-mnist`separador **Propriedades** para uma execução específica da sua experiência, .

![Conjuntos de dados de entrada](./media/how-to-version-track-datasets/input-datasets.png)

Utilize o seguinte código para registar modelos com conjuntos de dados:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Após o registo, pode ver a lista de modelos registados com o conjunto de dados utilizando python ou ir para https://ml.azure.com/.

A seguinte vista é do painel **datasets** em **Ativos**. Selecione o conjunto de dados e, em seguida, selecione o separador **Modelos** para uma lista dos modelos registados com o conjunto de dados. 

![Modelos de conjuntos de dados de entrada](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>Passos seguintes

* [Preparar com conjuntos de dados](how-to-train-with-datasets.md)
* [Mais cadernos de conjuntos de dados de amostra](https://aka.ms/dataset-tutorial)
