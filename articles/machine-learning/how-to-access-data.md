---
title: Dados de acesso nos serviços de armazenamento do Azure
titleSuffix: Azure Machine Learning
description: Saiba como usar as lojas de dados para se conectar de forma segura aos serviços de armazenamento Azure durante o treino com o Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: keli19
author: likebupt
ms.reviewer: nibaccam
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: 5527d58f140074279d6b9feac13803f29eb0bda5
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396191"
---
# <a name="access-data-in-azure-storage-services"></a>Dados de acesso nos serviços de armazenamento do Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprenda a aceder facilmente aos seus dados nos serviços de Armazenamento Azure através de lojas de dados de Machine Learning. As lojas de dados são usadas para armazenar informações de ligação, como o seu ID de subscrição e autorização simbólica. Quando utiliza lojas de dados, pode aceder ao seu armazenamento sem ter de obter informações de ligação a código sinuoso nos seus scripts. 

Pode criar lojas de dados a partir [destas soluções de Armazenamento Azure.](#matrix) Para soluções de armazenamento não suportadas e para poupar custos de egress de dados durante experiências de aprendizagem automática, recomendamos que [mova os seus dados](#move) para soluções de armazenamento azure suportadas. 

## <a name="prerequisites"></a>Pré-requisitos
Precisa de:
- Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

- Uma conta de armazenamento Azure com um [recipiente de blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) ou [partilha de ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- O [Azure Machine Learning SDK para Python,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou acesso ao [estúdio Azure Machine Learning.](https://ml.azure.com/)

- Uma área de trabalho do Azure Machine Learning.
  
  Ou cria um espaço de [trabalho azure machine learning](how-to-manage-workspace.md) ou usa um existente através do Python SDK. Importe a classe `Workspace` e `Datastore` e carregue as suas informações de subscrição a partir do ficheiro `config.json` utilizando a função `from_config()`. Isto procura o ficheiro JSON no diretório atual por padrão, mas também pode especificar um parâmetro de percurso para apontar para o ficheiro usando `from_config(path="your/file/path")`.

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Tipos de serviços de armazenamento de dados suportados

Atualmente, as lojas de dados suportam armazenar informações de ligação aos serviços de armazenamento listados na seguinte matriz. Neste momento, o armazém Azure Data não é suportado. 

| Tipo&nbsp;de armazenamento | Tipo&nbsp;de autenticação | [Estúdio Azure&nbsp;Machine&nbsp;Learning](https://ml.azure.com/) | [Máquina de&nbsp;&nbsp;máquina azure &nbsp; SDK Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Máquina&nbsp;Azure&nbsp;Aprendizagem CLI](reference-azure-machine-learning-cli.md) | [Máquina de&nbsp;&nbsp;Azure  Aprendizagem&nbsp; API de descanso](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Armazenamento de&nbsp;de azure&nbsp;](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Chave de conta <br> Ficha SAS | ✓ | ✓ | ✓ |✓
[Azure&nbsp;File&nbsp;Share](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Chave de conta <br> Ficha SAS | ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;1](https://docs.microsoft.com/azure/data-lake-store/)| Diretor de serviço| ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Diretor de serviço| ✓ | ✓ | ✓ |✓
Base de dados de&nbsp;Azure&nbsp;SQL| Autenticação do SQL <br>Diretor de serviço| ✓ | ✓ | ✓ |✓
Azure&nbsp;PostgreSQL | Autenticação do SQL| ✓ | ✓ | ✓ |✓
&nbsp;de Base de Dados&nbsp;Azure para&nbsp;MySQL | Autenticação do SQL|  | ✓* | ✓* |✓*
Sistema de&nbsp;de de ficheiros&nbsp;de tijolos de dados| Sem autenticação | | ✓** | ✓ ** |✓** 

*MySQL só é suportado para pipeline [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). <br> \** Databricks é suportado apenas para pipeline [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)

### <a name="storage-guidance"></a>Orientações de armazenamento

Recomendamos a criação de uma loja de dados para um recipiente de blob Azure.  
Tanto o armazenamento standard como o premium estão disponíveis para bolhas. Embora o armazenamento premium seja mais caro, as suas velocidades de produção mais rápidas podem melhorar a velocidade dos seus treinos, especialmente se treinar contra um grande conjunto de dados. Para obter informações sobre o custo das contas de armazenamento, consulte a calculadora de [preços Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

Quando se cria um espaço de trabalho, um recipiente de blob Azure e uma partilha de ficheiros Azure estão automaticamente registados no espaço de trabalho. São nomeados `workspaceblobstore` e `workspacefilestore`, respectivamente. Armazenam as informações de ligação para o recipiente blob e a parte de ficheiro que estão aprovisionadas na conta de armazenamento anexada ao espaço de trabalho. O recipiente `workspaceblobstore` é definido como o datastore padrão.

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Criar e registar lojas de dados

Ao registar uma solução de Armazenamento Azure como uma loja de dados, cria-se e regista automaticamente essa loja de dados num espaço de trabalho específico. Pode criar e registar lojas de dados num espaço de trabalho utilizando o estúdio Python SDK ou Azure Machine Learning.

>[!IMPORTANT]
> Como parte do atual processo de criação e registo da loja de dados, o Azure Machine Learning valida que o utilizador fornecido principal (nome de utilizador, diretor de serviço ou token SAS) tem acesso ao serviço de armazenamento subjacente. 
<br><br>
No entanto, para as lojas de dados Azure Data Lake Storage Gen 1 e 2, esta validação ocorre mais tarde quando são chamados métodos de acesso a dados como [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) ou [`from_delimited_files()`.](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) 

### <a name="python-sdk"></a>SDK Python

Todos os métodos de registo estão na classe [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) e têm o formulário `register_azure_*`.

Pode encontrar a informação de que necessita para povoar o método `register()` no [portal Azure.](https://portal.azure.com)
Selecione Contas de **Armazenamento** no painel esquerdo e escolha a conta de armazenamento que pretende registar. A página **'Overview'** fornece informações como o nome da conta, o contentor e o nome da partilha de ficheiros. 

* Para itens de autenticação, como chave de conta ou ficha SAS, vá para **As Teclas de Conta** no painel **Definições.** 

* Para itens principais de serviço como, ID de inquilino e ID do cliente, vá às **suas inscrições** da App e selecione qual a aplicação que pretende utilizar. A sua página **de visão geral** correspondente conterá estes itens.

> [!IMPORTANT]
> Se a sua conta de armazenamento estiver numa rede virtual, apenas é suportada a criação de blob, File share, ADLS Gen 1 e ADLS Gen 2 **através do SDK.** Para conceder o acesso ao seu espaço de trabalho na sua conta de armazenamento, defina o parâmetro `grant_workspace_access` para `True`.

Os exemplos seguintes mostram como registar um contentor de blob Azure, uma partilha de ficheiros Azure, e Azure Data Lake Storage Generation 2 como uma loja de dados. Para outros serviços de armazenamento, consulte a [documentação de referência para os métodos `register_azure_*`.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods)

#### <a name="blob-container"></a>Contentor de blobs

Para registar um recipiente de blob Azure como uma loja de dados, utilize [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

O código seguinte cria e regista a `blob_datastore_name` loja de dados para o espaço de trabalho `ws`. Esta loja de dados acede ao recipiente `my-container-name` blob na conta de armazenamento `my-account-name`, utilizando a chave de conta fornecida.

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

Para registar uma partilha de ficheiros Azure como uma loja de dados, utilize [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

O código seguinte cria e regista a `file_datastore_name` loja de dados para o espaço de trabalho `ws`. Esta loja de dados acede à `my-fileshare-name` partilha de ficheiros na conta de armazenamento `my-account-name`, utilizando a chave de conta fornecida.

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

#### <a name="azure-data-lake-storage-generation-2"></a>Geração de Armazenamento de Lagos De Dados Azure 2

Para uma loja de dados Azure Data Lake Storage Generation 2 (ADLS Gen 2), utilize [register_azure_data_lake_gen2()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) para registar uma loja de dados credencial ligada a um armazenamento Azure DataLake Gen 2 com [permissões principais](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)de serviço. Para utilizar o seu principal de serviço, é necessário registar a [sua aplicação](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) e definir atribuições de funções ao acesso ao Leitor e aos Dados. Saiba mais sobre o controlo de [acesso criado para ADLS Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

O código seguinte cria e regista a `adlsgen2_datastore_name` loja de dados para o espaço de trabalho `ws`. Esta loja de dados acede ao sistema de ficheiros `test` na conta de armazenamento `account_name`, utilizando as credenciais principais do serviço prestados.

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

Crie uma nova datastore em alguns passos no estúdio Azure Machine Learning:

> [!IMPORTANT]
> Se a sua conta de armazenamento estiver numa rede virtual, apenas é suportada a criação de lojas de dados [através do SDK.](#python-sdk) 

1. Inscreva-se no [estúdio Azure Machine Learning.](https://ml.azure.com/)
1. Selecione **Datastores** no painel esquerdo em **Manage**.
1. Selecione **+ Nova loja**de dados .
1. Preencha o formulário para uma nova loja de dados. O formulário atualiza-se inteligentemente com base nas suas seleções para o tipo de armazenamento Azure e tipo de autenticação.
  
Pode encontrar a informação de que necessita para preencher o formulário no [portal Azure.](https://portal.azure.com) Selecione Contas de **Armazenamento** no painel esquerdo e escolha a conta de armazenamento que pretende registar. A página **'Overview'** fornece informações como o nome da conta, o contentor e o nome da partilha de ficheiros. 

* Para itens de autenticação, como chave de conta ou ficha SAS, vá para **As Teclas de Conta** no painel **Definições.** 

* Para itens principais de serviço como, ID de inquilino e ID do cliente, vá às **suas inscrições** da App e selecione qual a aplicação que pretende utilizar. A sua página **de visão geral** correspondente conterá estes itens. 

O exemplo que se segue demonstra como é o formulário quando se cria uma loja de dados Azure blob: 
    
![Formulário para uma nova datastore](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Obtenha lojas de dados do seu espaço de trabalho

> [!IMPORTANT]
> O designer de Aprendizagem automática Azure (pré-visualização) criará uma loja de dados chamada **azureml_globaldatasets** automaticamente quando abrir uma amostra na página inicial do designer. Esta loja de dados contém apenas conjuntos de dados de amostras. Por favor, **não** utilize esta loja de dados para qualquer acesso confidencial a dados!
> ![loja de dados criada automaticamente para conjuntos de dados de amostras de design](media/how-to-access-data/datastore-designer-sample.png)

Para obter uma loja de dados específica registada no espaço de trabalho atual, utilize o [método](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-)`get()`estático na classe `Datastore`:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Para obter a lista de datastores registadas com um determinado espaço de trabalho, pode utilizar a [propriedade`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) num objeto de espaço de trabalho:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Para obter a loja de dados padrão do espaço de trabalho, use esta linha:

```Python
datastore = ws.get_default_datastore()
```

Para definir uma loja de dados padrão diferente para o espaço de trabalho atual, utilize o método [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) no objeto espaço de trabalho:

```Python
# Define the default datastore for the current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Carregar e transferir dados

Os métodos [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) e [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) descritos nos seguintes exemplos são específicos e funcionam de forma idêntica para as classes [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) e [AzureFileDatastore.](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)

### <a name="upload"></a>Carregar

Faça upload de um diretório ou de ficheiros individuais para a loja de dados utilizando o Python SDK:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

O parâmetro `target_path` especifica a localização na partilha de ficheiros (ou recipiente de bolha) para carregar. Não se aplica a `None`, pelo que os dados são carregados para raiz. Se `overwrite=True`, os dados existentes no `target_path` são substituídos.

Também pode fazer o upload de uma lista de ficheiros individuais para a loja de dados através do método `upload_files()`.

### <a name="download"></a>Transferência

Descarregue os dados de uma loja de dados para o seu sistema de ficheiros local:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

O parâmetro `target_path` é a localização do diretório local para descarregar os dados. Para especificar um caminho para a pasta na partilha de ficheiros (ou recipiente blob) para descarregar, forneça esse caminho para `prefix`. Se `prefix` for `None`, todos os conteúdos da sua partilha de ficheiros (ou recipiente blob) serão descarregados.

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Aceda aos seus dados durante o treino

Para interagir com dados nas suas datastores ou embalar os seus dados num objeto consumível para tarefas de aprendizagem automática, como treinar, criar um conjunto de [dados de Aprendizagem automática Azure](how-to-create-register-datasets.md). Os conjuntos de dados fornecem funções que carregam dados tabular em pandas ou Spark DataFrame. Os conjuntos de dados também fornecem a capacidade de descarregar ou montar ficheiros de qualquer formato a partir do armazenamento Azure Blob, Ficheiros Azure, Armazenamento de Lagos De dados Azure Gen1, Armazenamento de Lagos Azure, Base de Dados Azure SQL e Base de Dados Azure para PostgreSQL. [Saiba mais sobre como treinar com conjuntos](how-to-train-with-datasets.md)de dados.

### <a name="accessing-source-code-during-training"></a>Acesso ao código fonte durante o treino

O armazenamento da Azure Blob tem velocidades de produção mais elevadas do que uma quota de ficheiros Azure e irá escalar para um grande número de postos de trabalho iniciados em paralelo. Por esta razão, recomendamos configurar as suas correções para utilizar o armazenamento Blob para transferir ficheiros de código fonte.

O exemplo de código que se segue especifica na configuração de execução que blob datastore a utilizar para transferências de código fonte:

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Dados de acesso durante a pontuação

O Azure Machine Learning fornece várias formas de utilizar os seus modelos para pontuar. Alguns destes métodos não fornecem acesso a lojas de dados. Utilize a tabela seguinte para entender quais os métodos que lhe permitem aceder às lojas de dados durante a pontuação:

| Método | Acesso a datastore | Descrição |
| ----- | :-----: | ----- |
| [Previsão do lote](how-to-use-parallel-run-step.md) | ✔ | Faça previsões sobre grandes quantidades de dados assincronicamente. |
| [Serviço web](how-to-deploy-and-where.md) | &nbsp; | Implementar modelos como um serviço web. |
| [Módulo Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Implementar modelos para dispositivos IoT Edge. |

Para situações em que o SDK não fornece acesso a lojas de dados, poderá ser capaz de criar código personalizado utilizando o SDK Azure relevante para aceder aos dados. Por exemplo, o [Azure Storage SDK para Python](https://github.com/Azure/azure-storage-python) é uma biblioteca de clientes que pode usar para aceder a dados armazenados em blobs ou ficheiros.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Mova dados para soluções de armazenamento azure suportadas

O Azure Machine Learning suporta o acesso a dados do armazenamento Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database e Azure Database para PostgreSQL. Se estiver a utilizar armazenamento não suportado, recomendamos que desloque os seus dados para soluções de armazenamento Azure suportadas utilizando a [Azure Data Factory e estes passos.](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool) Mover dados para armazenamento suportado pode ajudá-lo a economizar custos de egress durante experiências de aprendizagem automática. 

A Azure Data Factory fornece transferência de dados eficiente e resiliente com mais de 80 conectores pré-construídos sem custos adicionais. Estes conectores incluem serviços de dados Azure, fontes de dados no local, Amazon S3 e Redshift, e Google BigQuery.

## <a name="next-steps"></a>Passos seguintes

* [Criar um conjunto de dados de aprendizagem automática Azure](how-to-create-register-datasets.md)
* [Treinar um modelo](how-to-train-ml-models.md)
* [Implementar um modelo](how-to-deploy-and-where.md)
