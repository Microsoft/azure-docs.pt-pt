---
title: Aceder a dados em arquivos de dados / blobs para formação
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar arquivos de dados para armazenamento de dados de BLOBs de acesso durante o treinamento com o serviço Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 02/25/2019
ms.custom: seodec18
ms.openlocfilehash: 85910e2f422ea45b2468f20b4ff9425f64ca3cbe
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793419"
---
# <a name="access-data-from-your-datastores"></a>Aceder a dados a partir de seus arquivos de dados

 No serviço do Azure Machine Learning, arquivos de dados são de computação independentemente da localização de mecanismos para o armazenamento de acesso sem a necessidade de alterações ao seu código de origem. Se escrever código de treinamento para ter um caminho como um parâmetro ou fornece um arquivo de dados diretamente para um avaliador que esteja, fluxos de trabalho do Azure Machine Learning Certifique-se seus locais de arquivo de dados estão acessíveis e disponíveis para o contexto de computação.

Nesta explicação de procedimento mostra exemplos das seguintes tarefas:
* [Escolha um arquivo de dados](#access)
* [Obter dados](#get)
* [Carregar e transferir dados para arquivos de dados](#up-and-down)
* [Arquivo de dados de acesso durante o treinamento](#train)

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar arquivos de dados, primeiro tem um [área de trabalho](concept-azure-machine-learning-architecture.md#workspace).

Comece por qualquer um [criar uma nova área de trabalho](setup-create-workspace.md#sdk) ou obtenção de um já existente:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>Escolha um arquivo de dados

Pode utilizar o arquivo de dados padrão ou coloque as suas.

### <a name="use-the-default-datastore-in-your-workspace"></a>Utilizar o arquivo de dados padrão na sua área de trabalho

 Cada área de trabalho tem um arquivo de dados predefinido registado, que pode utilizar imediatamente.

Para obter o arquivo de dados do espaço de trabalho predefinido:

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>Registar o seu próprio arquivo de dados com a área de trabalho

Se tiver de armazenamento do Azure existente, pode registá-lo como um arquivo de dados na sua área de trabalho.   Todos os métodos de Registro são sobre o [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) de classe e ter o formulário register_azure_ *. 

Os exemplos seguintes mostram-lhe para registar um contentor de Blobs do Azure ou uma partilha de ficheiros do Azure como um arquivo de dados.

+ Para uma **o arquivo de dados do Azure Blob contentor**, utilize [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ Para uma **o arquivo de dados do Azure ficheiro partilha**, utilize [ `register_azure_file_share()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). Por exemplo: 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           container_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>Localizar e definir os arquivos de dados

Para obter um arquivo de dados especificado registado na área de trabalho atual, utilize [ `get()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) :

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Para obter uma lista de todos os arquivos de dados numa área de trabalho específica, utilize este código:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

Para definir um arquivo de dados predefinido diferente para a área de trabalho atual, utilize [ `set_default_datastore()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-):

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Carregar e transferir dados
O [ `upload()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) e [ `download()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) métodos descritos nos exemplos seguintes são específicos e operar de maneira idêntica para o [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) e [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) classes.

### <a name="upload"></a>Carregar

 Carregar um diretório ou arquivos individuais para o arquivo de dados com o SDK de Python.

Para carregar um diretório para um arquivo de dados `ds`:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```

`target_path` Especifica a localização na partilha de ficheiros (ou contentor de BLOBs) para carregar. Assume como predefinição `None`, caso em que os dados são carregados para a raiz. `overwrite=True` irá substituir quaisquer dados existentes em `target_path`.

Ou carregue uma lista de arquivos individuais para o arquivo de dados por meio do arquivo de dados `upload_files()` método.

### <a name="download"></a>Transferência
Da mesma forma, transferir dados de um arquivo de dados para o sistema de ficheiros local.

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```

`target_path` é a localização do diretório local para transferir os dados. Para especificar um caminho para a pasta na partilha de ficheiros (ou contentor de BLOBs) para transferir, fornecer esse caminho em `prefix`. Se `prefix` é `None`, todo o conteúdo da sua partilha de ficheiros (ou o contentor de BLOBs) que irá ser descarregado.

<a name="train"></a>
## <a name="access-datastores-during-training"></a>Acesso a arquivos de dados durante o treinamento

Depois de disponibilizar o arquivo de dados no destino de computação, pode acessá-lo durante execuções de preparação (por exemplo, dados de treinamento ou validação), simplesmente passando o caminho a ele como um parâmetro no seu script de treinamento.

A tabela seguinte lista os [ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) métodos que informam ao destino de computação como usar o arquivo de dados durante execuções.

Forma|Método|Descrição|
----|-----|--------
Montar| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Utilize para montar o arquivo de dados no destino de computação.
Transferência|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Utilize para transferir o conteúdo do seu arquivo de dados para a localização especificada pela `path_on_compute`. <br> Para o contexto de treinamento ser executado, este download acontece antes da execução.
Carregar|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Utilizar para carregar um ficheiro da localização especificada pela `path_on_compute` para seu arquivo de dados. <br> Para o contexto de treinamento ser executado, este carregamento acontece após a sua execução.

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Para fazer referência a uma pasta específica ou um ficheiro no seu arquivo de dados e disponibilizá-la no destino de computação, utilize o arquivo de dados [ `path()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) função.

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> Qualquer `ds` ou `ds.path` objeto é resolvido para um nome de variável de ambiente do formato `"$AZUREML_DATAREFERENCE_XXXX"` cujo valor representa o caminho de montagem/transferir na computação de destino. O caminho de arquivo de dados na computação de destino não pode ser o mesmo que o caminho de execução para o script de treinamento.

### <a name="compute-context-and-datastore-type-matrix"></a>Matriz de tipo de contexto e o arquivo de dados de computação

A matriz seguinte apresenta as funcionalidades de acesso de dados disponíveis para os cenários de contexto e o arquivo de dados de computação diferentes. O termo "Pipeline" nesta matriz se refere à capacidade de usar arquivos de dados como entrada ou saída na [do Azure Machine Learning Pipelines](https://docs.microsoft.com/azure/machine-learning/service/concept-ml-pipelines).

||Computação local|Computação do Azure Machine Learning|Transferência de Dados|Databricks|HDInsight|Azure Batch|Azure DataLake Analytics|Virtual Machines|
-|--|-----------|----------|---------|-----|--------------|---------|---------|
|AzureBlobDatastore|[`as_download()`] [`as_upload()`]|[`as_mount()`]<br> [`as_download()`] [`as_upload()`] <br> Pipeline|Pipeline|Pipeline|[`as_download()`] <br> [`as_upload()`]|Pipeline||[`as_download()`] <br> [`as_upload()`]|
|AzureFileDatastore|[`as_download()`] [`as_upload()`]|[`as_mount()`]<br> [`as_download()`] [`as_upload()`] Pipeline |||[`as_download()`] [`as_upload()`]|||[`as_download()`] [`as_upload()`]|
|AzureDataLakeDatastore|||Pipeline|Pipeline|||Pipeline||
|AzureDataLakeGen2Datastore|||Pipeline||||||
|AzureDataPostgresSqlDatastore|||Pipeline||||||
|AzureSqlDatabaseDataDatastore|||Pipeline||||||


> [!NOTE]
> Poderão existir cenários em que altamente iterativo, processos de dados de grande dimensão executar com o mais rápido [`as_download()`] em vez de [`as_mount()`]; isso experimentalmente, pode ser validado.

### <a name="examples"></a>Exemplos 

Os seguintes exemplos de código são específicos para o [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe para aceder ao seu arquivo de dados durante o treinamento.

Este código cria um avaliador que esteja usando o script de formação `train.py`, do diretório de origem indicado, usando os parâmetros definidos no `script_params`, tudo no destino de computação especificado.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Também pode passar uma lista de arquivos de dados para o construtor de Calculadora `inputs` parâmetro montar ou copiar de/para o seu datastore(s). Este exemplo de código:
* Transfere todo o conteúdo no arquivo de dados `ds1` para o destino de computação antes do script de treinamento `train.py` é executado
* A pasta de downloads `'./foo'` no arquivo de dados `ds2` para o destino de computação antes de `train.py` é executado
* Carrega o ficheiro `'./bar.pkl'` proveniente do destino de computação até o arquivo de dados `ds3` após a execução do seu script

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="next-steps"></a>Passos Seguintes

* [Preparar um modelo](how-to-train-ml-models.md)

* [Implementar um modelo](how-to-deploy-and-where.md)
