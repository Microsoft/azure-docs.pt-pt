---
title: Acessar dados nos serviços de armazenamento do Azure
titleSuffix: Azure Machine Learning
description: Saiba como usar os armazenamentos de armazenamento para acessar os serviços de repositório do Azure durante o treinamento com o Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 08/2/2019
ms.custom: seodec18
ms.openlocfilehash: ecd1212385473e33d05f38f67db5708bff060daa
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71218238"
---
# <a name="access-data-in-azure-storage-services"></a>Acessar dados nos serviços de armazenamento do Azure

 Neste artigo, saiba como acessar facilmente seus dados nos serviços de armazenamento do Azure por meio de repositórios de Azure Machine Learning. Os armazenamentos de dados são usados para armazenar informações de conexão, como sua ID de assinatura e autorização de token, para acessar seu armazenamento sem ter que embutir em código essas informações em seus scripts.

Este "como" mostra exemplos das seguintes tarefas:
* [Registrar repositórios de armazenamento](#access)
* [Obter repositórios de armazenamento do espaço de trabalho](#get)
* [Carregar e baixar dados usando armazenamentos](#up-and-down)
* [Acessar dados durante o treinamento](#train)

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar arquivos de dados, primeiro tem um [área de trabalho](concept-workspace.md).

Comece por qualquer um [criar uma nova área de trabalho](how-to-manage-workspace.md) ou obtenção de um já existente:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="register-datastores"></a>Registrar repositórios de armazenamento

Todos os métodos de registro estão na [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) classe e têm o formato register_azure_ *.

Os exemplos a seguir mostram que você deve registrar um contêiner de blob do Azure ou um compartilhamento de arquivos do Azure como um repositório de armazenamento.

+ Para um **armazenamento de contêiner de blob do Azure**, use[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                      datastore_name='your datastore name', 
                                                      container_name='your azure blob container name',
                                                      account_name='your storage account name', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
  ```

+ Para um **repositório de armazenamento de arquivos do Azure**, use [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). Por exemplo: 
  ```Python
  datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                  datastore_name='your datastore name', 
                                                  file_share_name='your file share name',
                                                  account_name='your storage account name', 
                                                  account_key='your storage account key',
                                                  create_if_not_exists=True)
  ```

####  <a name="storage-guidance"></a>Orientações de armazenamento

Recomendamos o contêiner de blob do Azure. Tanto o armazenamento Standard quanto o Premium estão disponíveis para BLOBs. Embora sejam mais caros, sugerimos o armazenamento Premium devido a velocidades de taxa de transferência mais rápidas que podem melhorar a velocidade de suas execuções de treinamento, especialmente se você treinar em um grande conjunto de dados. Consulte a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) para obter informações de custo da conta de armazenamento.

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Obter repositórios de armazenamento do seu espaço de trabalho

Para obter um repositório de armazenamento específico registrado no espaço de trabalho atual, [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) use o método estático na classe de repositório de armazenamento:

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Para obter a lista de armazenamentos de dados registrados com um determinado espaço de trabalho, você `datastores` pode usar a propriedade em um objeto de espaço de trabalho:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Quando você cria um espaço de trabalho, um contêiner de BLOBs do Azure e um compartilhamento de arquivos do `workspaceblobstore` Azure `workspacefilestore` são registrados no espaço de trabalho chamado e, respectivamente. Eles armazenam as informações de conexão do contêiner de BLOB e o compartilhamento de arquivos que é provisionado na conta de armazenamento anexada ao espaço de trabalho. O `workspaceblobstore` é definido como o repositório de armazenamento padrão.

Para obter o arquivo de dados do espaço de trabalho predefinido:

```Python
datastore = ws.get_default_datastore()
```

Para definir um repositório de armazenamento padrão diferente para o espaço de trabalho [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) atual, use o método no objeto de espaço de trabalho:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Carregar & Baixar dados
Os [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) métodos [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) e descritos nos exemplos a seguir são específicos para e operam de forma idêntica para as classes [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) e [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) .

### <a name="upload"></a>Carregar

 Carregar um diretório ou arquivos individuais para o arquivo de dados com o SDK de Python.

Para carregar um diretório para um arquivo de dados `datastore`:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

O `target_path` parâmetro especifica o local no compartilhamento de arquivos (ou contêiner de BLOB) a ser carregado. Assume como predefinição `None`, caso em que os dados são carregados para a raiz. Quando `overwrite=True` qualquer dado existente em `target_path` é substituído.

Ou carregue uma lista de arquivos individuais no repositório de armazenamento por meio `upload_files()` do método.

### <a name="download"></a>Transferência

Da mesma forma, transferir dados de um arquivo de dados para o sistema de ficheiros local.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

O `target_path` parâmetro é o local do diretório local para o qual baixar os dados. Para especificar um caminho para a pasta na partilha de ficheiros (ou contentor de BLOBs) para transferir, fornecer esse caminho em `prefix`. Se `prefix` é `None`, todo o conteúdo da sua partilha de ficheiros (ou o contentor de BLOBs) que irá ser descarregado.

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Acesse seus dados durante o treinamento

> [!IMPORTANT]
> Usar [Azure Machine Learning conjuntos de dados (versão prévia)](how-to-create-register-datasets.md) é a nova maneira recomendada de acessar os seus dados no treinamento. Os conjuntos de dados fornecem funções que carregam dados tabulares em pandas ou Spark dataframe e a capacidade de baixar ou montar arquivos de qualquer formato do blob do Azure, arquivo do Azure, Azure Data Lake Gen 1, Azure Data Lake Gen 2, Azure SQL, PostgreSQL do Azure. Saiba mais sobre [como treinar com conjuntos de](how-to-train-with-datasets.md)os.

A tabela a seguir lista os métodos que dizem ao destino de computação como usar os repositórios de armazenamento durante as execuções. 

Aparência|Método|Descrição|
----|-----|--------
Montar| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Use para montar o repositório de armazenamento no destino de computação.
Transferência|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Use para baixar o conteúdo do seu repositório de armazenamento para o local especificado `path_on_compute`por. <br> Esse download ocorre antes da execução.
Carregar|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Use para carregar um arquivo do local especificado pelo `path_on_compute` para seu repositório de armazenamento. <br> Esse carregamento ocorre após a execução.

Para fazer referência a uma pasta ou arquivo específico em seu repositório de armazenamento e disponibilizá-lo no destino de computação, [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) use o método de armazenamento de Datastore.

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Qualquer `datastore` objeto `datastore.path` ou é resolvido para um nome de variável de ambiente do formato `"$AZUREML_DATAREFERENCE_XXXX"`, cujo valor representa o caminho de montagem/download na computação de destino. O caminho do repositório de armazenamento na computação de destino pode não ser o mesmo que o caminho de execução para o script de treinamento.

### <a name="examples"></a>Exemplos 

Os exemplos de código a seguir são específicos [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) para a classe para acessar dados durante o treinamento. 

`script_params`é um dicionário que contém parâmetros para o entry_script. Você pode usá-lo para passar um armazenamento de dados e descrever como os dados devem ser disponibilizados no destino de computação. Saiba mais em nosso [tutorial](tutorial-train-models-with-aml.md)de ponta a ponta.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Você também pode passar uma lista de armazenamentos de dados para o parâmetro do `inputs` Construtor do estimador para montar ou copiar data de/para seus repositórios. Este exemplo de código:
* Baixa todo o conteúdo em `datastore1` para o destino de computação antes de o `train.py` script de treinamento ser executado
* Baixa a pasta `'./foo'` no `datastore2` destino de computação antes `train.py` da execução
* Carrega o arquivo `'./bar.pkl'` do destino de computação para o `datastore3` após a execução do script

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```

### <a name="compute-and-datastore-matrix"></a>Matriz de computação e armazenamento de datastore

Atualmente, os armazenamentos de dados dão suporte ao armazenamento de informações de conexão para os serviços de armazenamento listados na matriz a seguir. Essa matriz exibe as funcionalidades de acesso a dados disponíveis para os diferentes destinos de computação e cenários de armazenamento de Datastore. Saiba mais sobre os [destinos de computação para Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Computação|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Localização|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|N/A         |N/A                                                                         |
| Computação do Azure Machine Learning |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|N/A         |N/A                                                                         |
| Máquinas virtuais               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| Transferência de dados                  |[Pipelines de ml&nbsp;](concept-ml-pipelines.md)                                               |N/A                                           |[Pipelines de ml&nbsp;](concept-ml-pipelines.md)            |[Pipelines de ml&nbsp;](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[Pipelines de ml&nbsp;](concept-ml-pipelines.md)                                              |N/A                                           |[Pipelines de ml&nbsp;](concept-ml-pipelines.md)             |N/A                                                                         |
| Azure Batch                    |[Pipelines de ml&nbsp;](concept-ml-pipelines.md)                                               |N/A                                           |N/D         |N/A                                                                         |
| Azure datalake Analytics       |N/A                                           |N/A                                           |[Pipelines de ml&nbsp;](concept-ml-pipelines.md)             |N/A                                                                         |

> [!NOTE]
> Pode haver cenários em que processos altamente iterativos de dados grandes são executados mais `as_download()` rapidamente usando `as_mount()`em vez de; isso pode ser validado experimentalmente.

### <a name="accessing-source-code-during-training"></a>Acessando o código-fonte durante o treinamento

O armazenamento de BLOBs do Azure tem velocidades de taxa de transferência maiores que o compartilhamento de arquivos do Azure e será dimensionado para um grande número de trabalhos iniciados em paralelo Por esse motivo, é recomendável configurar suas execuções para usar o armazenamento de BLOBs para transferir arquivos de código-fonte.

O exemplo de código a seguir especifica na configuração de execução qual repositório de armazenamento de blob usar para transferências de código-fonte.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Acessar dados durante a Pontuação

Azure Machine Learning fornece várias maneiras de usar seus modelos para pontuação. Alguns desses métodos não fornecem acesso a repositórios de armazenamento. Use a tabela a seguir para entender quais métodos permitem que você acesse os repositórios de armazenamento durante a Pontuação:

| Método | Acesso ao repositório de armazenamento | Descrição |
| ----- | :-----: | ----- |
| [Previsão de lote](how-to-run-batch-predictions.md) | ✔ | Faça previsões em grandes quantidades de dados de forma assíncrona. |
| [Serviço Web](how-to-deploy-and-where.md) | &nbsp; | Implantar modelo (s) como um serviço Web. |
| [Módulo IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Implantar modelo (s) para IoT Edge dispositivos. |

Para situações em que o SDK não fornece acesso a repositórios de dados, você poderá criar um código personalizado usando o SDK do Azure relevante para acessar o dado. Por exemplo, usando o [SDK de armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python) para acessar dados armazenados em BLOBs.


## <a name="next-steps"></a>Passos seguintes

* [Preparar um modelo](how-to-train-ml-models.md)

* [Implementar um modelo](how-to-deploy-and-where.md)
