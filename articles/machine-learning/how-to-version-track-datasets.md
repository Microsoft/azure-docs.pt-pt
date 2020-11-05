---
title: Versão do conjunto de dados
titleSuffix: Azure Machine Learning
description: Saiba como melhor ver os seus conjuntos de dados e como a versão funciona com os pipelines de machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: b4dc222ed0fc350b680d2696c1faa16d44b84a02
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93358342"
---
# <a name="version-and-track-datasets-in-experiments"></a>Conjuntos de dados de versão e rastreio em experiências


Neste artigo, você vai aprender a ver e rastrear conjuntos de dados de Aprendizagem de Máquinas Azure para reprodutibilidade. A versão do Dataset é uma forma de reservar o estado dos seus dados para que possa aplicar uma versão específica do conjunto de dados para experiências futuras.

Cenários típicos de ver versão:

* Quando novos dados estão disponíveis para reciclagem
* Quando se está a aplicar diferentes abordagens de preparação de dados ou de engenharia de recursos

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, você precisa:

- [Azure Machine Learning SDK para Python instalado](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py). Este SDK inclui o pacote [de conjuntos de dados azureml.](/python/api/azureml-core/azureml.core.dataset?preserve-view=true&view=azure-ml-py)
    
- Um [espaço de trabalho de aprendizagem automática Azure.](concept-workspace.md) Recupere um existente executando o seguinte código ou [crie um novo espaço de trabalho](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- Um [conjunto de dados de aprendizagem de máquinas Azure](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Registar e recuperar versões de conjunto de dados

Ao registar um conjunto de dados, pode ver, reutilizar e partilhá-lo através de experiências e com colegas. Pode registar vários conjuntos de dados com o mesmo nome e recuperar uma versão específica pelo nome e número de versão.

### <a name="register-a-dataset-version"></a>Registar uma versão do conjunto de dados

O código que se segue regista uma nova versão do conjunto de `titanic_ds` dados, definindo o `create_new_version` parâmetro para `True` . Se não houver um conjunto de dados existente `titanic_ds` registado no espaço de trabalho, o código cria um novo conjunto de dados com o nome e define a sua versão para `titanic_ds` 1.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>Recuperar um conjunto de dados pelo nome

Por predefinição, o método [get_by_name](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-by-name-workspace--name--version--latest--) na `Dataset` classe devolve a versão mais recente do conjunto de dados registado no espaço de trabalho. 

O seguinte código obtém a versão 1 do conjunto de `titanic_ds` dados.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Ver versão das melhores práticas

Quando cria uma versão do conjunto de dados, *não* está a criar uma cópia extra de dados com o espaço de trabalho. Como os conjuntos de dados são referências aos dados do seu serviço de armazenamento, tem uma única fonte de verdade, gerida pelo seu serviço de armazenamento.

>[!IMPORTANT]
> Se os dados referenciados pelo seu conjunto de dados forem substituídos ou eliminados, a chamada de uma versão específica do conjunto de dados *não* reverte a alteração.

Quando carrega dados a partir de um conjunto de dados, o conteúdo de dados atual referenciado pelo conjunto de dados é sempre carregado. Se pretender certificar-se de que cada versão do conjunto de dados é reprodutível, recomendamos que não modifique o conteúdo de dados referenciado pela versão do conjunto de dados. Quando novos dados chegam, guarde novos ficheiros de dados numa pasta de dados separada e, em seguida, crie uma nova versão do conjunto de dados para incluir dados dessa nova pasta.

Os seguintes códigos de imagem e amostra mostram a forma recomendada de estruturar as suas pastas de dados e de criar versões de conjunto de dados que referenciam essas pastas:

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

Pode utilizar um conjunto de dados como entrada e saída de cada passo do pipeline Machine Learning. Ao reencandidatura dos gasodutos, a saída de cada etapa do gasoduto é registada como uma nova versão do conjunto de dados.

Como os gasodutos Machine Learning povoam a saída de cada passo numa nova pasta sempre que o gasoduto se reproduz, os conjuntos de dados de saída versados são reprodutíveis. Saiba mais sobre [conjuntos de dados em oleodutos.](how-to-create-your-first-pipeline.md#steps)

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

Para cada experiência de Machine Learning, pode rastrear facilmente os conjuntos de dados utilizados como entrada através do objeto de `Run` experiência.

O seguinte código utiliza o [`get_details()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-details--) método para rastrear quais os conjuntos de dados de entrada utilizados com a execução da experiência:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

Também pode encontrar as `input_datasets` experiências através da utilização https://ml.azure.com/ . 

A imagem a seguir mostra onde encontrar o conjunto de dados de entrada de uma experiência no estúdio Azure Machine Learning. Para este exemplo, vá ao painel **de Experiências** e abra o **separador Propriedades** para uma execução específica da sua `keras-mnist` experiência, .

![Conjuntos de dados de entrada](./media/how-to-version-track-datasets/input-datasets.png)

Utilize o seguinte código para registar modelos com conjuntos de dados:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Após a inscrição, pode ver a lista de modelos registados com o conjunto de dados utilizando python ou ir a https://ml.azure.com/ .

A seguinte vista é do painel **de conjuntos de dados** em **Ativos**. Selecione o conjunto de dados e, em seguida, selecione o **separador Modelos** para uma lista dos modelos que estão registados no conjunto de dados. 

![Modelos de conjuntos de dados de entrada](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>Próximos passos

* [Preparar com conjuntos de dados](how-to-train-with-datasets.md)
* [Mais cadernos de conjuntos de dados de amostra](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)