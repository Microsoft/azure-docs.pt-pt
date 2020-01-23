---
title: Acessar dados nos serviços de armazenamento do Azure
titleSuffix: Azure Machine Learning
description: Saiba como usar os repositórios de armazenamento para se conectar com segurança aos serviços de armazenamento do Azure durante o treinamento com o Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 01/15/2020
ms.custom: seodec18
ms.openlocfilehash: 6867862c130bf6f0b7cc34098064f6ce6eec282b
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543500"
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
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Tipos de serviço de armazenamento de dados com suporte

Atualmente, os armazenamentos de dados dão suporte ao armazenamento de informações de conexão para os serviços de armazenamento listados na matriz a seguir. Neste momento, não há suporte para o Azure data warehouse. 

| Tipo de&nbsp;de armazenamento | Tipo de&nbsp;de autenticação | [Azure&nbsp;Machine&nbsp;Learning Studio](https://ml.azure.com/) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; SDK do Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [CLI do Azure&nbsp;Machine&nbsp;Learning](reference-azure-machine-learning-cli.md) | [API REST do Azure&nbsp;Machine&nbsp;Learning&nbsp;](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Armazenamento de&nbsp;blob do Azure&nbsp;](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Chave de conta <br> Token de SAS | ✓ | ✓ | ✓ |✓
[Compartilhamento de&nbsp;de arquivos do&nbsp;do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Chave de conta <br> Token de SAS | ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;1](https://docs.microsoft.com/azure/data-lake-store/)| Principal de serviço| ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Principal de serviço| ✓ | ✓ | ✓ |✓
Banco de dados SQL&nbsp;do Azure&nbsp;| Autenticação do SQL <br>Principal de serviço| ✓ | ✓ | ✓ |✓
PostgreSQL&nbsp;do Azure | Autenticação do SQL| ✓ | ✓ | ✓ |✓
&nbsp;do banco de dados&nbsp;do Azure para&nbsp;MySQL | Autenticação do SQL|  | ✓ | ✓ |✓
Databricks&nbsp;sistema de&nbsp;de arquivos| Sem autenticação | | ✓* | ✓ * |✓* 

\* somente com suporte em cenários de destino de computação local

### <a name="storage-guidance"></a>Orientações de armazenamento

É recomendável criar um repositório de armazenamento para um contêiner de blob do Azure.  
Tanto o armazenamento Standard quanto o Premium estão disponíveis para BLOBs. Embora o armazenamento Premium seja mais caro, suas velocidades de taxa de transferência mais rápidas podem melhorar a velocidade de suas execuções de treinamento, especialmente se você treinar um grande conjunto de grandes. Para obter informações sobre o custo de contas de armazenamento, consulte a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

Quando você cria um espaço de trabalho, um contêiner de BLOBs do Azure e um compartilhamento de arquivos do Azure são automaticamente registrados no espaço de trabalho. Eles são nomeados `workspaceblobstore` e `workspacefilestore`, respectivamente. Eles armazenam as informações de conexão para o contêiner de BLOB e o compartilhamento de arquivos que são provisionados na conta de armazenamento anexada ao espaço de trabalho. O contêiner de `workspaceblobstore` é definido como o repositório de armazenamento padrão.

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Criar e registrar repositórios de armazenamento

Ao registrar uma solução de armazenamento do Azure como um repositório de armazenamento, você cria e registra automaticamente esse repositório de armazenamento em um espaço de trabalho específico. Você pode criar e registrar repositórios de armazenamento em um espaço de trabalho usando o SDK do Python ou o Azure Machine Learning Studio.

>[!IMPORTANT]
> Como parte do processo de criação e registro do repositório de armazenamento atual, o Azure Machine Learning valida que a entidade de segurança fornecida pelo usuário (nome de usuário, entidade de serviço ou token SAS) tem acesso ao serviço de armazenamento subjacente. 
<br>
No entanto, para Azure Data Lake Storage armazenamentos de dados Gen 1 e 2, essa validação ocorrerá mais tarde quando métodos de acesso a data como [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) ou [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) forem chamados. 

### <a name="python-sdk"></a>Python SDK

Todos os métodos de registro estão na classe [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) e têm o formulário `register_azure_*`.

Você pode encontrar as informações necessárias para preencher o método `register()` usando o [portal do Azure](https://portal.azure.com):

1. Selecione **contas de armazenamento** no painel esquerdo e escolha a conta de armazenamento que você deseja registrar. 
2. Para obter informações como o nome da conta, o contêiner e o nome do compartilhamento de arquivos, vá para a página **visão geral** . Para obter informações de autenticação, como chave de conta ou token SAS, acesse **chaves de acesso** no painel **configurações** . 

> [!IMPORTANT]
> Se sua conta de armazenamento estiver em uma rede virtual, haverá suporte apenas para a criação de um armazenamento de BLOBs do Azure. Para conceder ao seu espaço de trabalho acesso à sua conta de armazenamento, defina o parâmetro `grant_workspace_access` como `True`.

Os exemplos a seguir mostram como registrar um contêiner de blob do Azure, um compartilhamento de arquivos do Azure e Azure Data Lake Storage a geração 2 como um repositório de armazenamento. Para outros serviços de armazenamento, consulte a [documentação de referência para os métodos de `register_azure_*`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods).

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

#### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage geração 2

Para um repositório de armazenamento de Azure Data Lake Storage geração 2 (ADLS Gen 2), use [register_azure_data_lake_gen2 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) para registrar um repositório de armazenamento de credenciais conectado a um armazenamento do Azure datalake Gen 2 com permissões de entidade de serviço. Saiba mais sobre o [controle de acesso configurado para o ADLS Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

O código a seguir cria e registra o repositório de armazenamento de `adlsgen2_datastore_name` no espaço de trabalho `ws`. Esse repositório de armazenamento acessa o sistema de arquivos `test` na conta de armazenamento de `account_name`, usando as credenciais de entidade de serviço fornecidas.

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```

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

Para interagir com os dados em seus armazenamentos de dados ou para empacotar seus dados em um objeto consumível para tarefas de aprendizado de máquina, como treinamento, [crie um conjunto de Azure Machine Learning](how-to-create-register-datasets.md). Os conjuntos de dados fornecem funções que carregam dados tabulares em um data frame do pandas ou do Spark. Os conjuntos de dados também fornecem a capacidade de baixar ou montar arquivos de qualquer formato do armazenamento de BLOBs do Azure, arquivos do Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, banco de dados SQL do Azure e banco de dados do Azure para PostgreSQL. [Saiba mais sobre como treinar com conjuntos de](how-to-train-with-datasets.md)os.

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
| [Previsão de lote](how-to-use-parallel-run-step.md) | ✔ | Faça previsões em grandes quantidades de dados de forma assíncrona. |
| [Serviço Web](how-to-deploy-and-where.md) | &nbsp; | Implantar modelos como um serviço Web. |
| [Módulo Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Implantar modelos para IoT Edge dispositivos. |

Para situações em que o SDK não fornece acesso aos armazenamentos de dados, você poderá criar código personalizado usando o SDK do Azure relevante para acessar os dados. Por exemplo, o [SDK do armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python) é uma biblioteca de cliente que você pode usar para acessar dados armazenados em BLOBs ou arquivos.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Mover dados para soluções de armazenamento do Azure com suporte

Azure Machine Learning dá suporte ao acesso a dados do armazenamento de BLOBs do Azure, arquivos do Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, banco de dados SQL do Azure e banco de dados do Azure para PostgreSQL. Se você estiver usando um armazenamento sem suporte, recomendamos que você mova seus dados para soluções de armazenamento do Azure com suporte usando [Azure data Factory e essas etapas](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool). Mover dados para o armazenamento com suporte pode ajudá-lo a salvar os custos de egresso de dados durante experimentos de aprendizado de máquina. 

Azure Data Factory fornece transferência de dados eficiente e resiliente com mais de 80 conectores predefinidos sem custo adicional. Esses conectores incluem serviços de dados do Azure, fontes de dados locais, Amazon S3 e redshift e Google BigQuery.

## <a name="next-steps"></a>Passos seguintes

* [Criar um conjunto de informações do Azure Machine Learning](how-to-create-register-datasets.md)
* [Preparar um modelo](how-to-train-ml-models.md)
* [Implementar um modelo](how-to-deploy-and-where.md)
