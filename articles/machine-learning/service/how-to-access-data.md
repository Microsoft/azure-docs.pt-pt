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
ms.openlocfilehash: f489abeab0e1374d2d40ade79c4eb55fd633b909
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443288"
---
# <a name="access-data-from-your-datastores"></a>Aceder a dados a partir de seus arquivos de dados
Neste artigo, irá aprender diferentes formas de aceder e interagir com os seus dados em fluxos de trabalho do Azure Machine Learning por meio de arquivos de dados.

Nesta explicação de procedimento mostra exemplos para as seguintes tarefas: 
* [Escolha um arquivo de dados](#access)
* [Obter um arquivo de dados](#get)
* [Carregar e transferir dados para arquivos de dados](#upload-and-download-data)
* Arquivo de dados de acesso durante o treinamento

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar arquivos de dados, é necessário um [área de trabalho](concept-azure-machine-learning-architecture.md#workspace) primeiro. 

Comece por qualquer um [criar uma nova área de trabalho](quickstart-create-workspace-with-python.md) ou obtenção de um já existente:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

Ou, [seguir este guia de introdução do Python](quickstart-create-workspace-with-python.md) para utilizar o SDK para criar a sua área de trabalho e começar a utilizar.

<a name="access"></a>

## <a name="choose-a-datastore"></a>Escolha um arquivo de dados

Pode utilizar o arquivo de dados padrão ou coloque as suas.

### <a name="use-the-default-datastore-in-your-workspace"></a>Utilizar o arquivo de dados padrão na sua área de trabalho

Não é necessário para criar ou configurar uma conta de armazenamento, uma vez que cada área de trabalho tem um arquivo de dados padrão. Pode usar a que o arquivo de dados imediatamente, pois já está registado na área de trabalho. 

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

## <a name="get-data-in-your-datastore"></a>Obter dados no seu arquivo de dados

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

## <a name="upload-and-download-data"></a>Carregar e transferir dados
O [ `upload()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) e [ `download()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) métodos descritos nos exemplos seguintes são específicos e operar de maneira idêntica para o [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) e [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) classes.

### <a name="upload"></a>Carregar

 Carregar um diretório ou arquivos individuais para o arquivo de dados com o SDK de Python.

Para carregar um diretório para um arquivo de dados `ds`:

```Python
import azureml.data
from azureml.data import AzureFileDatastore, AzureBlobDatastore

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

## <a name="access-datastores-during-training"></a>Acesso a arquivos de dados durante o treinamento
Pode acessar um arquivo de dados durante um treinamento ser executado (por exemplo, para dados de treinamento ou validação) num destino de computação remota através do SDK de Python com o [ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) classe.

Existem várias formas de tornar o seu arquivo de dados disponíveis na computação remota.

Forma|Método|Descrição
----|-----|--------
Montar| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Utilize para montar um arquivo de dados na computação remota.
Transferência|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Utilize para transferir dados da localização especificada pela `path_on_compute` no seu arquivo de dados para a computação remoto.
Carregar|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Utilizar para carregar dados para a raiz do seu arquivo de dados a partir da localização especificada pela `path_on_compute`.

```Python
import azureml.data
from azureml.data import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

### <a name="reference-filesfolders"></a>Ficheiros/pastas de referência
Para fazer referência a uma pasta específica ou um ficheiro no seu arquivo de dados, utilize o arquivo de dados [ `path()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) função.

```Python
#download the contents of the `./bar` directory from the datastore 
ds.path('./bar').as_download()
```


### <a name="examples"></a>Exemplos 

Qualquer `ds` ou `ds.path` objeto é resolvido para um nome de variável de ambiente do formato `"$AZUREML_DATAREFERENCE_XXXX"` cujo valor representa o caminho de montagem/transferir na computação remoto. O caminho de arquivo de dados na computação remoto não pode ser o mesmo que o caminho de execução para o script.

Para aceder ao seu arquivo de dados durante o treinamento, transmita-o para o script de treinamento como um argumento da linha de comandos através de `script_params` partir do [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py')
```
`as_mount()` é o modo predefinido para um arquivo de dados, para que poderia também diretamente passar `ds` para o `'--data_dir'` argumento.

Ou passar uma lista de arquivos de dados para o construtor de Calculadora `inputs` parâmetro montar ou copiar de/para o seu datastore(s). Este exemplo de código:
* Transfere todo o conteúdo no arquivo de dados `ds1` para a computação remota antes do script de treinamento `train.py` é executado
* A pasta de downloads `'./foo'` no arquivo de dados `ds2` para a computação remota antes de `train.py` é executado
* Carrega o ficheiro `'./bar.pkl'` de computação remota até o arquivo de dados `d3` após a execução do seu script

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```


## <a name="next-steps"></a>Passos Seguintes

* [Preparar um modelo](how-to-train-ml-models.md)

* [Implementar um modelo](how-to-deploy-and-where.md)
