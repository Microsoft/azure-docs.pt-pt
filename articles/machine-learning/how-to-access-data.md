---
title: Acessar dados nos serviços de armazenamento do Azure
titleSuffix: Azure Machine Learning
description: Saiba como usar os repositórios de armazenamento para se conectar com segurança aos serviços de armazenamento do Azure durante o treinamento com o Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: ylxiong
author: YLXiong1125
ms.reviewer: nibaccam
ms.date: 12/10/2019
ms.custom: seodec18
ms.openlocfilehash: ac6ef6341013ca13d5a9f27be8897365c1c2155d
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540948"
---
# <a name="access-data-in-azure-storage-services"></a>Acessar dados nos serviços de armazenamento do Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, saiba como acessar facilmente seus dados nos serviços de armazenamento do Azure por meio de repositórios de Azure Machine Learning. Os armazenamentos de dados são usados para armazenar informações de conexão, como sua ID de assinatura e autorização de token. Ao usar armazenamentos de dados, você pode acessar seu armazenamento sem precisar codificar informações de conexão em seus scripts. 

Você pode criar repositórios de armazenamento a partir [dessas soluções de armazenamento do Azure](#matrix). Para soluções de armazenamento sem suporte e para salvar o custo de egresso de dados durante experimentos de Machine Learning, recomendamos que você [mova seus dados](#move) para soluções de armazenamento do Azure com suporte. 

## <a name="prerequisites"></a>Pré-requisitos
Precisa de:
- Uma subscrição do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

- Uma conta de armazenamento do Azure com um [contêiner de blob do](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) Azure ou [compartilhamento de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- O [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou acesso ao [Azure Machine Learning Studio](https://ml.azure.com/).

- Uma área de trabalho do Azure Machine Learning.
  
  [Crie um espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md) ou use um existente por meio do SDK do Python:

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Criar e registrar repositórios de armazenamento

Ao registrar uma solução de armazenamento do Azure como um repositório de armazenamento, você cria automaticamente esse repositório de armazenamento em um espaço de trabalho específico. Você pode criar e registrar repositórios de armazenamento em um espaço de trabalho usando o SDK do Python ou o Azure Machine Learning Studio.

### <a name="python-sdk"></a>Python SDK

Todos os métodos de registro estão na classe [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) e têm o formulário `register_azure_*`.

Você pode encontrar as informações necessárias para preencher o método `register()` usando o [portal do Azure](https://portal.azure.com):

1. Selecione **contas de armazenamento** no painel esquerdo e escolha a conta de armazenamento que você deseja registrar. 
2. Para obter informações como o nome da conta, o contêiner e o nome do compartilhamento de arquivos, vá para a página **visão geral** . Para obter informações de autenticação, como chave de conta ou token SAS, acesse **chaves de acesso** no painel **configurações** . 

> [!IMPORTANT]
> Se sua conta de armazenamento estiver em uma rede virtual, haverá suporte apenas para a criação de um armazenamento de BLOBs do Azure. Para conceder ao seu espaço de trabalho acesso à sua conta de armazenamento, defina o parâmetro `grant_workspace_access` como `True`.

Os exemplos a seguir mostram como registrar um contêiner de BLOBs do Azure, um compartilhamento de arquivos do Azure e dados do SQL do Azure como um datastore.

#### <a name="blob-container"></a>Contentor de blobs

Para registrar um contêiner de blob do Azure como um repositório de armazenamento, use [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

O código a seguir cria e registra o repositório de armazenamento de `blob_datastore_name` no espaço de trabalho `ws`. Esse repositório de armazenamento acessa o contêiner de blob `my-container-name` na conta de armazenamento de `my-account-name`, usando a chave de conta fornecida.

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

#### <a name="file-share"></a>Partilha de ficheiros

Para registrar um compartilhamento de arquivos do Azure como um repositório de armazenamento, use [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

O código a seguir cria e registra o repositório de armazenamento de `file_datastore_name` no espaço de trabalho `ws`. Esse repositório de armazenamento acessa o compartilhamento de arquivos `my-fileshare-name` na conta de armazenamento de `my-account-name`, usando a chave de conta fornecida.

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                 datastore_name=file_datastore_name, 
                                                 file_share_name=file_share_name, 
                                                 account_name=account_name,
                                                 account_key=account_key)
```

#### <a name="sql-data"></a>Dados SQL

Para um armazenamento de dados SQL do Azure, use [register_azure_sql_database ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-sql-database-workspace--datastore-name--server-name--database-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--endpoint-none--overwrite-false--username-none--password-none-) para registrar um repositório de dados de credencial conectado a um banco de dados SQL do Azure com autenticação SQL ou permissões de entidade de serviço. 

Para registrar-se por meio da autenticação do SQL:

```python
sql_datastore_name="azsqlsdksql"
server_name=os.getenv("SQL_SERVERNAME", "<my-server-name>") # Name of the Azure SQL server
database_name=os.getenv("SQL_DATBASENAME", "<my-database-name>") # Name of the Azure SQL database
username=os.getenv("SQL_USER_NAME", "<my-sql-user-name>") # Username of the database user to access the database
password=os.getenv("SQL_USER_PASSWORD", "<my-sql-user-password>") # Password of the database user to access the database

sql_datastore = Datastore.register_azure_sql_database(workspace=ws,
                                                  datastore_name=sql_datastore_name,
                                                  server_name=server_name,
                                                  database_name=database_name,
                                                  username=username,
                                                  password=password)

```

Para registrar-se por meio de uma entidade de serviço:

```python 
sql_datastore_name="azsqlsdksp"
server_name=os.getenv("SQL_SERVERNAME", "<my-server-name>") # Name of the SQL server
database_name=os.getenv("SQL_DATBASENAME", "<my-database-name>") # Name of the SQL database
client_id=os.getenv("SQL_CLIENTNAME", "<my-client-id>") # Client ID of the service principal with permissions to access the database
client_secret=os.getenv("SQL_CLIENTSECRET", "<my-client-secret>") # Secret of the service principal
tenant_id=os.getenv("SQL_TENANTID", "<my-tenant-id>") # Tenant ID of the service principal

sql_datastore = Datastore.register_azure_sql_database(workspace=ws,
                                                      datastore_name=sql_datastore_name,
                                                      server_name=server_name,
                                                      database_name=database_name,
                                                      client_id=client_id,
                                                      client_secret=client_secret,
                                                      tenant_id=tenant_id)
```

#### <a name="storage-guidance"></a>Orientações de armazenamento

Recomendamos um contêiner de blob do Azure. Tanto o armazenamento Standard quanto o Premium estão disponíveis para BLOBs. Embora o armazenamento Premium seja mais caro, suas velocidades de taxa de transferência mais rápidas podem melhorar a velocidade de suas execuções de treinamento, especialmente se você treinar um grande conjunto de grandes. Para obter informações sobre o custo de contas de armazenamento, consulte a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning studio 

Crie um novo repositório de armazenamento em algumas etapas no Azure Machine Learning Studio:

1. Entre no [Azure Machine Learning Studio](https://ml.azure.com/).
1. Selecione **repositórios de armazenamento** no painel esquerdo em **gerenciar**.
1. Selecione **+ novo repositório de armazenamento**.
1. Preencha o formulário para um novo repositório de armazenamento. O formulário se atualiza de forma inteligente com base em suas seleções para tipo de armazenamento e tipo de autenticação do Azure.
  
Você pode encontrar as informações necessárias para preencher o formulário na [portal do Azure](https://portal.azure.com). Selecione **contas de armazenamento** no painel esquerdo e escolha a conta de armazenamento que você deseja registrar. A página **visão geral** fornece informações como o nome da conta, o contêiner e o nome do compartilhamento de arquivos. Para itens de autenticação, como chave de conta ou token SAS, vá para **chaves de conta** no painel **configurações** .

O exemplo a seguir demonstra a aparência do formulário quando você cria um repositório de armazenamento de BLOBs do Azure: 
    
![Formulário para um novo repositório de armazenamento](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Obter repositórios de armazenamento do seu espaço de trabalho

Para obter um repositório de armazenamento específico registrado no espaço de trabalho atual, use o método estático [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) na classe `Datastore`:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Para obter a lista de armazenamentos de dados registrados com um determinado espaço de trabalho, você pode usar a propriedade [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) em um objeto de espaço de trabalho:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Quando você cria um espaço de trabalho, um contêiner de BLOBs do Azure e um compartilhamento de arquivos do Azure são automaticamente registrados no espaço de trabalho. Eles são nomeados `workspaceblobstore` e `workspacefilestore`, respectivamente. Eles armazenam as informações de conexão para o contêiner de BLOB e o compartilhamento de arquivos que são provisionados na conta de armazenamento anexada ao espaço de trabalho. O contêiner de `workspaceblobstore` é definido como o repositório de armazenamento padrão.

Para obter o armazenamento de datastore padrão do espaço de trabalho, use esta linha:

```Python
datastore = ws.get_default_datastore()
```

Para definir um repositório de armazenamento padrão diferente para o espaço de trabalho atual, use o método [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) no objeto de espaço de trabalho:

```Python
# Define the default datastore for the current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Carregar e transferir dados

Os métodos [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) e [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) descritos nos exemplos a seguir são específicos do e operam de forma idêntica para as classes [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) e [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) .

### <a name="upload"></a>Carregar

Carregue um diretório ou arquivos individuais no repositório de armazenamento usando o SDK do Python:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

O parâmetro `target_path` especifica o local no compartilhamento de arquivos (ou contêiner de BLOB) a ser carregado. O padrão é `None`, de modo que os dados são carregados para a raiz. Se `overwrite=True`, todos os dados existentes em `target_path` serão substituídos.

Você também pode carregar uma lista de arquivos individuais no repositório de armazenamento por meio do método `upload_files()`.

### <a name="download"></a>Transferir

Baixar dados de um repositório de armazenamento para o sistema de arquivos local:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

O parâmetro `target_path` é o local do diretório local para o qual baixar os dados. Para especificar um caminho para a pasta na partilha de ficheiros (ou contentor de BLOBs) para transferir, fornecer esse caminho em `prefix`. Se `prefix` for `None`, todo o conteúdo do seu compartilhamento de arquivos (ou contêiner de BLOB) será baixado.

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Acesse seus dados durante o treinamento

> [!IMPORTANT]
> Agora, é recomendável usar [Azure Machine Learning conjuntos](how-to-create-register-datasets.md) de dados para acessar seu dado no treinamento. Os conjuntos de dados fornecem funções que carregam dados tabulares em um data frame do pandas ou do Spark. Os conjuntos de dados também fornecem a capacidade de baixar ou montar arquivos de qualquer formato do armazenamento de BLOBs do Azure, arquivos do Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, banco de dados SQL do Azure e banco de dados do Azure para PostgreSQL. [Saiba mais sobre como treinar com conjuntos de](how-to-train-with-datasets.md)os.

A tabela a seguir lista os métodos que dizem ao destino de computação como usar os repositórios de armazenamento durante as execuções: 

Aparência|Método|Descrição|
----|-----|--------
Montar| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Use para montar o repositório de armazenamento no destino de computação. Quando o armazenamento de datastore é montado, todos os arquivos de seu armazenamento de datafica acessíveis para seu destino de computação.
Transferir|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Use para baixar o conteúdo do seu repositório de armazenamento para o local especificado por `path_on_compute`. <br><br> Esse download ocorre antes da execução.
Carregar|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Use para carregar um arquivo do local especificado por `path_on_compute` para seu repositório de armazenamento. <br><br> Esse carregamento ocorre após a execução.

Para fazer referência a uma pasta ou arquivo específico em seu repositório de armazenamento e disponibilizá-lo no destino de computação, use o método de [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) do repositório de armazenamento:

```Python
# To mount the full contents in your storage to the compute target
datastore.as_mount()

# To download the contents of only the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Qualquer `datastore` ou objeto de `datastore.path` especificado é resolvido para um nome de variável de ambiente do formato `"$AZUREML_DATAREFERENCE_XXXX"`. O valor desse nome representa o caminho de montagem/download no destino de computação. O caminho do repositório de armazenamento no destino de computação pode não ser o mesmo que o caminho de execução para o script de treinamento.

### <a name="examples"></a>Exemplos 

É recomendável usar a classe [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) para acessar dados durante o treinamento. 

A variável `script_params` é um dicionário que contém parâmetros para `entry_script`. Use-o para passar um armazenamento de dados e descrever como os dados são disponibilizados no destino de computação. Saiba mais no [tutorial de ponta a ponta](tutorial-train-models-with-aml.md).

```Python
from azureml.train.estimator import Estimator

# Notice that '/' is in front, which indicates the absolute path
script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Você também pode transmitir uma lista de armazenamentos de dados para o parâmetro `inputs` do construtor de `Estimator` para montar ou copiar dados de/para seu datastore. Este exemplo de código:
* Baixa todo o conteúdo em `datastore1` para o destino de computação antes da execução do script de treinamento `train.py`.
* Baixa a pasta `'./foo'` em `datastore2` para o destino de computação antes que `train.py` seja executado.
* Carrega o arquivo de `'./bar.pkl'` do destino de computação para `datastore3` depois que o script tiver sido executado.

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
Se preferir usar um objeto `RunConfig` para treinamento, você precisará configurar um objeto [`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) . 

O código a seguir mostra como trabalhar com um objeto `DataReference` em um pipeline de estimativa. Para obter o exemplo completo, consulte [este bloco de anotações](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-estimatorstep.ipynb).

```Python
from azureml.core import Datastore
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

def_blob_store = Datastore(ws, "workspaceblobstore")

input_data = DataReference(
       datastore=def_blob_store,
       data_reference_name="input_data",
       path_on_datastore="20newsgroups/20news.pkl")

output = PipelineData("output", datastore=def_blob_store)
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>Matriz de computação e armazenamento de datastore

Atualmente, os armazenamentos de dados dão suporte ao armazenamento de informações de conexão para os serviços de armazenamento listados na matriz a seguir. Essa matriz exibe as funcionalidades de acesso a dados disponíveis para os diferentes destinos de computação e cenários de armazenamento de Datastore. [Saiba mais sobre os destinos de computação para Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Computação|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Local|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|N/A         |N/A                                                                         |
| Computação do Machine Learning do Azure |[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [pipelines de Machine Learning](concept-ml-pipelines.md)|[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [pipelines de Machine Learning](concept-ml-pipelines.md)|N/A         |N/A                                                                         |
| Virtual Machines               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| Azure HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| Transferência de dados                  |[Pipelines de Machine Learning](concept-ml-pipelines.md)                                               |N/A                                           |[Pipelines de Machine Learning](concept-ml-pipelines.md)            |[Pipelines de Machine Learning](concept-ml-pipelines.md)                                                                            |
| Azure Databricks                     |[Pipelines de Machine Learning](concept-ml-pipelines.md)                                              |N/A                                           |[Pipelines de Machine Learning](concept-ml-pipelines.md)             |N/A                                                                         |
| Azure Batch                    |[Pipelines de Machine Learning](concept-ml-pipelines.md)                                               |N/A                                           |N/A         |N/A                                                                         |
| Azure Data Lake Analytics       |N/A                                           |N/A                                           |[Pipelines de Machine Learning](concept-ml-pipelines.md)             |N/A                                                                         |

> [!NOTE]
> Pode haver cenários em que processos de dados grandes altamente iterativos são executados mais rapidamente usando `as_download()` em vez de `as_mount()`. Você pode validar isso experimentalmente.

### <a name="accessing-source-code-during-training"></a>Acessando o código-fonte durante o treinamento

O armazenamento de BLOBs do Azure tem velocidades de taxa de transferência mais altas que um compartilhamento de arquivos do Azure e será dimensionado para um grande número de trabalhos iniciados em paralelo. Por esse motivo, é recomendável configurar suas execuções para usar o armazenamento de BLOBs para transferir arquivos de código-fonte.

O exemplo de código a seguir especifica na configuração de execução qual repositório de armazenamento de blob usar para transferências de código-fonte:

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Acessar dados durante a Pontuação

Azure Machine Learning fornece várias maneiras de usar seus modelos para pontuação. Alguns desses métodos não fornecem acesso a repositórios de armazenamento. Use a tabela a seguir para entender quais métodos permitem que você acesse os repositórios de armazenamento durante a Pontuação:

| Método | Acesso ao repositório de armazenamento | Descrição |
| ----- | :-----: | ----- |
| [Previsão de lote](how-to-run-batch-predictions.md) | ✔ | Faça previsões em grandes quantidades de dados de forma assíncrona. |
| [Serviço Web](how-to-deploy-and-where.md) | &nbsp; | Implantar modelos como um serviço Web. |
| [Módulo Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Implantar modelos para IoT Edge dispositivos. |

Para situações em que o SDK não fornece acesso aos armazenamentos de dados, você poderá criar código personalizado usando o SDK do Azure relevante para acessar os dados. Por exemplo, o [SDK do armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python) é uma biblioteca de cliente que você pode usar para acessar dados armazenados em BLOBs ou arquivos.

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>Mover dados para soluções de armazenamento do Azure com suporte

Azure Machine Learning dá suporte ao acesso a dados do armazenamento de BLOBs do Azure, arquivos do Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, banco de dados SQL do Azure e banco de dados do Azure para PostgreSQL. Se você estiver usando um armazenamento sem suporte, recomendamos que você mova seus dados para soluções de armazenamento do Azure com suporte usando Azure Data Factory. Mover dados para o armazenamento com suporte pode ajudá-lo a salvar os custos de egresso de dados durante experimentos de aprendizado de máquina. 

Azure Data Factory fornece transferência de dados eficiente e resiliente com mais de 80 conectores predefinidos sem custo adicional. Esses conectores incluem serviços de dados do Azure, fontes de dados locais, Amazon S3 e redshift e Google BigQuery. [Siga o guia passo a passo para mover seus dados usando Azure data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

## <a name="next-steps"></a>Passos seguintes

* [Preparar um modelo](how-to-train-ml-models.md)
* [Implementar um modelo](how-to-deploy-and-where.md)
