---
title: Versão do conjunto de dados
titleSuffix: Azure Machine Learning
description: Saiba como ver conjuntos de dados de machine learning e como a versão funciona com os pipelines de machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: d72d2d094e220bd4e460cfca6b422f0609c083af
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880072"
---
# <a name="version-and-track-azure-machine-learning-datasets"></a>Versão e rastreio de conjuntos de dados de aprendizagem automática Azure

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

## <a name="version-an-ml-pipeline-output-dataset"></a>Versão um conjunto de dados de saída de gasoduto ML

Pode utilizar um conjunto de dados como entrada e saída de cada passo do [gasoduto ML.](concept-ml-pipelines.md) Ao reencandidatura dos gasodutos, a saída de cada etapa do gasoduto é registada como uma nova versão do conjunto de dados.

Os gasodutos ML povoam a saída de cada passo numa nova pasta sempre que o gasoduto se reensala. Este comportamento permite que os conjuntos de dados de saída versados sejam reprodutíveis. Saiba mais sobre [conjuntos de dados em oleodutos.](./how-to-create-machine-learning-pipelines.md#steps)

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

## <a name="track-data-in-your-experiments"></a>Acompanhe os dados nas suas experiências

O Azure Machine Learning rastreia os seus dados ao longo da sua experiência como conjuntos de dados de entrada e saída.  

Seguem-se os cenários em que os seus dados são rastreados como um **conjunto de dados de entrada**. 

* Como `DatasetConsumptionConfig` objeto através do `inputs` ou parâmetro do seu objeto ao submeter a `arguments` `ScriptRunConfig` experiência. 

* Quando métodos como, get_by_name() ou get_by_id() são chamados no seu script. Para este cenário, o nome atribuído ao conjunto de dados quando o registou no espaço de trabalho é o nome apresentado. 

Seguem-se os cenários em que os seus dados são rastreados como um **conjunto de dados de saída**.  

* Passe um `OutputFileDatasetConfig` objeto através do ou parâmetro ao submeter uma `outputs` `arguments` experiência. `OutputFileDatasetConfig` os objetos também podem ser usados para persistir dados entre os passos do gasoduto. Consulte [os dados de movimento entre os passos do gasoduto ML.](how-to-move-data-in-out-of-pipelines.md)
  
* Registe um conjunto de dados no seu script. Para este cenário, o nome atribuído ao conjunto de dados quando o registou no espaço de trabalho é o nome apresentado. No exemplo seguinte, `training_ds` é o nome que seria exibido.

    ```Python
   training_ds = unregistered_ds.register(workspace = workspace,
                                     name = 'training_ds',
                                     description = 'training data'
                                     )
    ```

* Submeta a execução da criança com um conjunto de dados não registado no script. Isto resulta num conjunto de dados guardado anónimo.

### <a name="trace-datasets-in-experiment-runs"></a>Trace datasets em execuções experimentais

Para cada experiência de Machine Learning, pode rastrear facilmente os conjuntos de dados utilizados como entrada com o objeto da `Run` experiência.

O seguinte código utiliza o [`get_details()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-details--) método para rastrear quais os conjuntos de dados de entrada utilizados com a execução da experiência:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

Você também pode encontrar o `input_datasets` de experiências usando o [estúdio Azure Machine Learning](). 

A imagem a seguir mostra onde encontrar o conjunto de dados de entrada de uma experiência no estúdio Azure Machine Learning. Para este exemplo, vá ao painel **de Experiências** e abra o **separador Propriedades** para uma execução específica da sua `keras-mnist` experiência, .

![Conjuntos de dados de entrada](./media/how-to-version-track-datasets/input-datasets.png)

Utilize o seguinte código para registar modelos com conjuntos de dados:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Após a inscrição, pode ver a lista de modelos registados com o conjunto de dados utilizando python ou ir ao [estúdio.](https://ml.azure.com/)

A seguinte vista é do painel **de conjuntos de dados** em **Ativos**. Selecione o conjunto de dados e, em seguida, selecione o **separador Modelos** para uma lista dos modelos que estão registados no conjunto de dados. 

![Modelos de conjuntos de dados de entrada](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>Próximos passos

* [Preparar com conjuntos de dados](how-to-train-with-datasets.md)
* [Mais cadernos de conjuntos de dados de amostra](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)