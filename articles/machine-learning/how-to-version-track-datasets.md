---
title: Controle de versão do conjunto de
titleSuffix: Azure Machine Learning
description: Saiba como obter a melhor versão de seus conjuntos de informações e como o controle de versão funciona com pipelines do Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: sihhu
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.custom: ''
ms.openlocfilehash: f70c826d839be1de82f3f21cf615f261a2193358
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75536437"
---
# <a name="version-and-track-datasets-in-experiments"></a>Conjuntos de testes de versão e acompanhamento em experimentos
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a controlar a versão e o acompanhamento de conjuntos de Azure Machine Learning para reprodução. O controle de versão do conjunto de dados é uma maneira de marcar o estado de seus dados de forma que você possa aplicar uma versão específica do conjunto para experimentos futuros.

Cenários típicos de controle de versão:

* Quando novos dados estão disponíveis para novo treinamento
* Quando você estiver aplicando uma preparação de dados ou abordagens de engenharia de recursos diferentes

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, você precisa de:

- [SDK do Azure Machine Learning para Python instalado](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). Este SDK inclui o pacote de conjuntos de linhas do [azureml](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py) .
    
- Um [espaço de trabalho Azure Machine Learning](concept-workspace.md). Recupere um existente executando o código a seguir ou [crie um novo espaço de trabalho](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- Um [conjunto de Azure Machine Learning](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Registrar e recuperar versões do conjunto de registros

Ao registrar um conjunto de registros, você pode fazer a versão, reutilizá-lo e compartilhá-lo entre experimentos e colegas. Você pode registrar vários conjuntos de registros com o mesmo nome e recuperar uma versão específica por nome e número de versão.

### <a name="register-a-dataset-version"></a>Registrar uma versão do conjunto de registros

O código a seguir registra uma nova versão do conjunto de `titanic_ds` DataSet definindo o parâmetro `create_new_version` como `True`. Se não houver nenhum conjunto de `titanic_ds` existente registrado com o espaço de trabalho, o código criará um novo conjunto de um com o nome `titanic_ds` e definirá sua versão como 1.

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

Quando você cria uma versão de conjunto de dados, você *não* está criando uma cópia extra do dado com o espaço de trabalho. Uma vez que os conjuntos de dados são referências a eles em seu serviço de armazenamento, você tem uma única fonte de verdade, gerenciada pelo seu serviço de armazenamento.

>[!IMPORTANT]
> Se os dados referenciados pelo DataSet forem substituídos ou excluídos, a chamada de uma versão específica do DataSet *não* reverterá a alteração.

Quando você carrega dados de um DataSet, o conteúdo de dados atual referenciado pelo DataSet é sempre carregado. Se você quiser certificar-se de que cada versão do conjunto de dados seja reproduzível, recomendamos que você não modifique o conteúdo de dados referenciado pela versão do DataSet. Quando novos dados chegam, salve novos arquivos de dados em uma pasta de dados separada e, em seguida, crie uma nova versão de conjunto de dados a fim de incluí-los na nova pasta.

A imagem e o código de exemplo a seguir mostram a maneira recomendada para estruturar suas pastas de dados e criar versões de conjuntos de dados que fazem referência a essas pastas:

![Estrutura de pastas](./media/how-to-version-track-datasets/folder-image.png)

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

Você pode usar um conjunto de dados como entrada e saída de cada etapa Machine Learning pipeline. Quando você executa novamente os pipelines, a saída de cada etapa de pipeline é registrada como uma nova versão do conjunto de resultados.

Como Machine Learning pipelines preenchem a saída de cada etapa em uma nova pasta toda vez que o pipeline é reproduzido, os conjuntos de resultados de saída com versão são reproduzíveis.

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

## <a name="track-datasets-in-experiments"></a>Acompanhar conjuntos de os testes em experimentos

Para cada experimento de Machine Learning, você pode facilmente rastrear os conjuntos de dados usados como entrada por meio do objeto experimento `Run`.

O código a seguir usa o método [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) para controlar quais conjuntos de dados de entrada foram usados com a execução do experimento:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

Você também pode encontrar o `input_datasets` de experimentos usando [Azure Machine Learning Studio (clássico)](https://ml.azure.com/). 

A imagem a seguir mostra onde encontrar o conjunto de dados de entrada de um experimento em Azure Machine Learning Studio (clássico). Para este exemplo, vá para o painel **experimentos** e abra a guia **Propriedades** para uma execução específica de seu experimento, `keras-mnist`.

![Conjuntos de dados de entrada](./media/how-to-version-track-datasets/input-datasets.png)

Use o código a seguir para registrar modelos com conjuntos de valores:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Após o registro, você pode ver a lista de modelos registrados com o conjunto de registros usando Python ou [Azure Machine Learning Studio (clássico)](https://ml.azure.com/). A exibição a seguir é do painel **conjuntos de valores** em **ativos**. Selecione o conjunto de um e selecione a guia **modelos** para obter uma lista dos modelos registrados com o conjunto de um. 

![Modelos de conjuntos de dados de entrada](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>Passos seguintes

* [Treinar com conjuntos de os](how-to-train-with-datasets.md)
* [Mais notebooks de conjunto de anotações de exemplo](https://aka.ms/dataset-tutorial)
