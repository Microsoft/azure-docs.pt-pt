---
title: Ligar aos serviços de armazenamento Azure
titleSuffix: Azure Machine Learning
description: Saiba como utilizar as lojas de dados para ligar de forma segura aos serviços de armazenamento da Azure durante o treino com a Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 07/08/2020
ms.custom: seodec18, tracking-python
ms.openlocfilehash: 194864d223d908cc2d8b1d7f14efe81e16bbd058
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87031512"
---
# <a name="connect-to-azure-storage-services"></a>Ligar aos serviços de armazenamento Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, saiba como **ligar-se aos serviços de armazenamento Azure através das lojas de dados Azure Machine Learning**. As datas armazenam informações de conexão, como o ID de subscrição e a autorização simbólica no cofre de [chaves](https://azure.microsoft.com/services/key-vault/) associado ao espaço de trabalho, para que possa aceder de forma segura ao seu armazenamento sem ter de os codificar nos scripts. 

**Para soluções de armazenamento não suportadas**, e para poupar o custo da saída de dados durante as experiências de ML, [mova os seus dados](#move) para uma solução de armazenamento Azure suportada.  Pode criar datastores a partir [destas soluções de armazenamento Azure](#matrix). 

Para entender onde as datas-tores se encaixam no fluxo de trabalho global de acesso a dados da Azure Machine Learning, consulte o artigo [de dados de acesso seguro.](concept-data.md#data-workflow)

## <a name="prerequisites"></a>Pré-requisitos

Precisará:
- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

- Uma conta de armazenamento Azure com um [recipiente de blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) ou [uma partilha de ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- O [Azure Machine Learning SDK para Python,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou acesso ao [estúdio Azure Machine Learning.](https://ml.azure.com/)

- Uma área de trabalho do Azure Machine Learning.
  
  Ou [cria um espaço de trabalho de aprendizagem automática Azure](how-to-manage-workspace.md) ou utiliza um existente através do Python SDK. Importe a `Workspace` classe e `Datastore` carregue as suas informações de subscrição do ficheiro `config.json` utilizando a função `from_config()` . Isto procura o ficheiro JSON no diretório atual por predefinição, mas também pode especificar um parâmetro de caminho para apontar para o ficheiro usando `from_config(path="your/file/path")` .

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Tipos de serviços de armazenamento de dados suportados

As datas-lojas suportam atualmente o armazenamento de informações de ligação aos serviços de armazenamento listados na seguinte matriz.

| Tipo de armazenamento &nbsp; | Tipo de autenticação &nbsp; | [Estúdio Azure &nbsp; Machine &nbsp; Learning](https://ml.azure.com/) | [Azure &nbsp; Máquina &nbsp; aprendendo Python &nbsp; SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Azure &nbsp; Machine &nbsp; Learning CLI](reference-azure-machine-learning-cli.md) | [Azure &nbsp; Machine &nbsp; Learning &nbsp; Rest API](https://docs.microsoft.com/rest/api/azureml/) | Código VS
---|---|---|---|---|---|---
[Armazenamento Azure &nbsp; Blob &nbsp;](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Chave da conta <br> Token de SAS | ✓ | ✓ | ✓ |✓ |✓
[Azure &nbsp; File &nbsp; Share](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Chave da conta <br> Token de SAS | ✓ | ✓ | ✓ |✓|✓
[Azure &nbsp; Data Lake Storage Gen &nbsp; &nbsp; 1](https://docs.microsoft.com/azure/data-lake-store/)| Service principal (Principal de serviço)| ✓ | ✓ | ✓ |✓|
[Azure &nbsp; Data Lake Storage Gen &nbsp; &nbsp; 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Service principal (Principal de serviço)| ✓ | ✓ | ✓ |✓|
[&nbsp;Base de Dados Azure SQL &nbsp;](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| Autenticação do SQL <br>Service principal (Principal de serviço)| ✓ | ✓ | ✓ |✓|
[Azure &nbsp; PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | Autenticação do SQL| ✓ | ✓ | ✓ |✓|
[Base de Dados Azure &nbsp; &nbsp; para &nbsp; MySQL](https://docs.microsoft.com/azure/mysql/overview) | Autenticação do SQL|  | ✓* | ✓* |✓*|
[Sistema de Ficheiros databricks &nbsp; &nbsp;](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| Sem autenticação | | ✓** | ✓ ** |✓** |

*MySQL só é suportado para o pipeline [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). <br>
**Databricks é suportado apenas para pipeline [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)

### <a name="storage-guidance"></a>Orientações de armazenamento

Recomendamos a criação de uma loja de dados para um [recipiente Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Tanto o armazenamento standard como o armazenamento premium estão disponíveis para bolhas. Embora o armazenamento premium seja mais caro, as suas velocidades de produção mais rápidas podem melhorar a velocidade dos seus treinos, especialmente se treinar contra um grande conjunto de dados. Para obter informações sobre o custo das contas de armazenamento, consulte a [calculadora de preços da Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[A azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) é construída em cima do armazenamento Azure Blob e projetada para análise de big data da empresa. Uma parte fundamental do Data Lake Storage Gen2 é a adição de um [espaço hierárquico](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) de nomes para o armazenamento blob. O espaço hierárquico organiza objetos/ficheiros numa hierarquia de diretórios para um acesso eficiente aos dados.

Quando cria um espaço de trabalho, um recipiente de blob Azure e uma partilha de ficheiros Azure são automaticamente registados no espaço de trabalho. São nomeados `workspaceblobstore` `workspacefilestore` e, respectivamente. `workspaceblobstore`é usado para armazenar artefactos do espaço de trabalho e seus registos de experiências de aprendizagem automática. `workspacefilestore`é usado para armazenar cadernos e scripts R autorizados através de [instância computacional](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files). O `workspaceblobstore` recipiente é definido como a datastore predefinido e não pode ser eliminado do espaço de trabalho.

> [!IMPORTANT]
> O designer de Machine Learning (pré-visualização) criará uma datastore com o nome **azureml_globaldatasets** automaticamente quando abrir uma amostra na página inicial do designer. Esta loja de dados contém apenas conjuntos de dados de amostra. Não **do not** utilize esta loja de dados para qualquer acesso confidencial aos dados.
> ![Datastore criado automaticamente para conjuntos de dados de amostras de designers](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Criar e registar datastores

Quando regista uma solução de armazenamento Azure como uma datastore, cria e regista automaticamente essa datastore para um espaço de trabalho específico. Pode criar e registar datastores para um espaço de trabalho utilizando o estúdio [Python SDK](#python-sdk) ou [Azure Machine Learning](#azure-machine-learning-studio).

>[!IMPORTANT]
> Como parte do processo inicial de criação e registo de datastore, a Azure Machine Learning valida que o serviço de armazenamento subjacente existe e que o utilizador fornecido principal (nome de utilizador, principal de serviço ou ficha SAS) tem acesso a esse armazenamento. No entanto, para a Azure Data Lake Storage Gen 1 e 2 datastores, esta validação acontece mais tarde, quando métodos de acesso a dados como [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) ou [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) são chamados. 
<br><br>
Após a criação da datastore, esta validação é realizada apenas para métodos que requerem acesso ao recipiente de armazenamento subjacente, **e não** cada vez que os objetos da loja de dados são recuperados. Por exemplo, a validação acontece se pretender descarregar ficheiros da sua datastore; mas se apenas quiser alterar a sua datastore padrão, então a validação não acontece.

### <a name="python-sdk"></a>SDK Python

Todos os métodos de registo estão na [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) classe e têm o `register_azure_*` formulário.

> [!IMPORTANT]
> Se planeia criar uma loja de dados para contas de armazenamento que se encontram numa rede virtual, consulte os dados do Access numa secção [de rede virtual.](#access-data-in-a-virtual-network)

Pode encontrar a informação necessária para preencher o `register_azure_*()` método no [portal Azure](https://portal.azure.com).

* O nome datastore deve consistir apenas em letras minúsculas, dígitos e sublinhados. 

* Se pretender utilizar uma chave de conta ou um sinal SAS para autenticação, selecione **Contas de Armazenamento** no painel esquerdo e escolha a conta de armazenamento que pretende registar. 
  * A **página 'Vista Geral'** fornece informações como o nome da conta, o contentor e o nome da partilha de ficheiros. 
      1. Para obter as teclas de conta, aceda às **teclas de acesso** no painel **de definições.** 
      1. Para fichas SAS, aceda a **assinaturas de acesso compartilhadas** no painel **de Definições.**

* Se planeia utilizar um principal de serviço para autenticação, vá às **inscrições da** sua App e selecione qual a aplicação que pretende utilizar. 
    * A sua página **geral** correspondente conterá informações necessárias, como iD do inquilino e identificação do cliente.

> [!IMPORTANT]
> Por razões de segurança, poderá ter de alterar as suas chaves de acesso para uma conta de Armazenamento Azure (chave de conta ou ficha SAS). Ao fazê-lo, certifique-se de sincronizar as novas credenciais com o seu espaço de trabalho e as datas-tores que lhe estão ligadas. Saiba como sincronizar as suas credenciais atualizadas com [estes passos.](how-to-change-storage-access-key.md) 

Os exemplos a seguir mostram como registar um recipiente de blob Azure, uma partilha de ficheiros Azure, e Azure Data Lake Storage Generation 2 como uma datastore. Os parâmetros fornecidos nestes exemplos são os **parâmetros necessários** para criar e registar uma datastore. 

Para criar datastores para outros serviços de armazenamento e ver parâmetros opcionais para estes métodos, consulte a [documentação de referência para os `register_azure_*` métodos aplicáveis.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods)

#### <a name="blob-container"></a>Contentor de blobs

Para registar um recipiente de bolhas Azure como uma loja de dados, utilize [`register_azure_blob_container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

O código a seguir cria e regista a `blob_datastore_name` datastore para o `ws` espaço de trabalho. Esta loja de dados acede ao `my-container-name` recipiente blob na `my-account-name` conta de armazenamento, utilizando a chave de acesso à conta fornecida.

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```
Se o seu recipiente de bolhas estiver na rede virtual, inclua o parâmetro `skip_validation=True` no seu [`register_azure_blob_container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) método. 

#### <a name="file-share"></a>Partilha de ficheiros

Para registar uma partilha de ficheiros Azure como uma datastore, utilize [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) . 

O código a seguir cria e regista a `file_datastore_name` datastore para o `ws` espaço de trabalho. Esta loja de dados acede à parte do `my-fileshare-name` ficheiro na conta de `my-account-name` armazenamento, utilizando a chave de acesso à conta fornecida.

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```
Se a sua partilha de ficheiros estiver na rede virtual, inclua o parâmetro `skip_validation=True` no seu [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) método. 

#### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Generation 2

Para uma data-loja de dados Azure Data Lake Storage Generation 2 (ADLS Gen 2), utilize [register_azure_data_lake_gen2()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) para registar uma loja de dados credenciais ligada a um armazenamento Azure DataLake Gen 2 com [permissões principais](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)de serviço. Para utilizar o seu principal de serviço, tem de registar a [sua candidatura](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) e conceder ao titular do serviço o acesso do Leitor *de Dados blob de armazenamento.* Saiba mais sobre [o controlo de acesso configurado para a ADLS Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

O código a seguir cria e regista a `adlsgen2_datastore_name` datastore para o `ws` espaço de trabalho. Esta loja de dados acede ao sistema de ficheiros `test` na `account_name` conta de armazenamento, utilizando as credenciais principais do serviço fornecido.

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

Crie uma nova loja de dados em alguns passos no estúdio Azure Machine Learning:

> [!IMPORTANT]
> Se a sua conta de armazenamento de dados estiver numa rede virtual, são necessários passos de configuração adicionais para garantir que o estúdio tem acesso aos seus dados. Consulte [o isolamento da rede & privacidade](how-to-enable-virtual-network.md#machine-learning-studio) para garantir que são aplicadas as etapas de configuração adequadas. 

1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com/).
1. Selecione **Datastores** no painel esquerdo em **Manage**.
1. Selecione **+ Nova loja de dados**.
1. Preencha o formulário para uma nova datastore. O formulário atualiza-se inteligentemente com base nas suas seleções para o tipo de armazenamento Eszure e tipo de autenticação.
  
Pode encontrar a informação de que necessita para preencher o formulário no [portal Azure](https://portal.azure.com). Selecione **Contas de Armazenamento** no painel esquerdo e escolha a conta de armazenamento que pretende registar. A **página 'Vista Geral'** fornece informações como o nome da conta, o contentor e o nome da partilha de ficheiros. 

* Para itens de autenticação, como chave de conta ou ficha SAS, aceda às **teclas de acesso** no painel **de Definições.** 

* Para itens principais de serviço como, ID do inquilino e ID do cliente, vá às suas **inscrições da App** e selecione qual a aplicação que pretende utilizar. A sua página **geral** correspondente conterá estes itens. 

> [!IMPORTANT]
> Por razões de segurança, poderá ter de alterar as suas chaves de acesso para uma conta de Armazenamento Azure (chave de conta ou ficha SAS). Ao fazê-lo, certifique-se de sincronizar as novas credenciais com o seu espaço de trabalho e as datas-tores que lhe estão ligadas. Saiba como sincronizar as suas credenciais atualizadas com [estes passos.](how-to-change-storage-access-key.md) 

O exemplo a seguir demonstra como é a forma quando cria uma loja **de dados azure blob**: 
    
![Formulário para uma nova datastore](media/how-to-access-data/new-datastore-form.png)

### <a name="vs-code"></a>Código VS

Pode criar e gerir as datas-tores utilizando a extensão do Código VS de Aprendizagem de Máquinas Azure. Visite o [guia de gestão de recursos do Código VS para](how-to-manage-resources-vscode.md#datastores) saber mais.

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Obtenha datastores do seu espaço de trabalho

Para obter uma loja de dados específica registada no espaço de trabalho atual, utilize o [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) método estático na `Datastore` classe:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Para obter a lista de datastores registadas com um determinado espaço de trabalho, pode utilizar a [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) propriedade num objeto de espaço de trabalho:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Para obter a loja de dados predefinido do espaço de trabalho, utilize esta linha:

```Python
datastore = ws.get_default_datastore()
```
Também pode alterar a loja de dados predefinido com o seguinte código. Esta capacidade só é suportada através do SDK. 

```Python
 ws.set_default_datastore(new_default_datastore)
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Carregar e transferir dados

Os [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) métodos e métodos descritos nos seguintes exemplos são específicos e funcionam de forma idêntica para as classes [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) e [AzureFileDatastore.](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)

> [!NOTE]
> O upload para as datas do AzureDataLakeGen2 não é suportado neste momento.

### <a name="upload"></a>Carregar

Faça o upload de um diretório ou de ficheiros individuais para a loja de dados utilizando o Python SDK:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

O `target_path` parâmetro especifica a localização na partilha de ficheiros (ou recipiente blob) para carregar. É por `None` defeito, para que os dados são carregados para raiz. Se, `overwrite=True` quaisquer dados existentes `target_path` forem substituídos.

Também pode fazer o upload de uma lista de ficheiros individuais para a datastore através do `upload_files()` método.

### <a name="download"></a>Download

Descarregue os dados de uma loja de dados para o seu sistema de ficheiros local:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

O `target_path` parâmetro é a localização do diretório local para descarregar os dados para. Para especificar um caminho para a pasta na partilha de ficheiros (ou recipiente blob) para descarregar, forneça esse caminho para `prefix` . Se `prefix` for , todo o conteúdo da sua partilha de `None` ficheiros (ou recipiente blob) será descarregado.

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Aceda aos seus dados durante o treino

Para interagir com os dados nas suas datastores ou para embalar os seus dados num objeto consumível para tarefas de aprendizagem automática, como formação, [criar um conjunto de dados de aprendizagem automática Azure](how-to-create-register-datasets.md). Os conjuntos de dados fornecem funções que carregam dados tabulares num panda ou Nodilho de Dados. Os conjuntos de dados também fornecem a capacidade de descarregar ou montar ficheiros de qualquer formato a partir do armazenamento Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database e Azure Database for PostgreSQL. [Saiba mais sobre como treinar com conjuntos de dados.](how-to-train-with-datasets.md)

### <a name="accessing-source-code-during-training"></a>Aceder ao código fonte durante o treino

O armazenamento Azure Blob tem velocidades de produção mais altas do que uma partilha de ficheiros Azure e irá escalar para um grande número de empregos iniciados em paralelo. Por esta razão, recomendamos configurar as suas corridas para utilizar o armazenamento Blob para transferir ficheiros de código fonte.

O exemplo de código a seguir especifica na configuração de execução que blob datastore para usar para transferências de código fonte.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Aceder aos dados durante a pontuação

A Azure Machine Learning fornece várias formas de usar os seus modelos para pontuar. Alguns destes métodos não fornecem acesso a datastores. Utilize a seguinte tabela para entender quais os métodos que lhe permitem aceder às datastores durante a pontuação:

| Método | Acesso à loja de dados | Descrição |
| ----- | :-----: | ----- |
| [Predição de lote](how-to-use-parallel-run-step.md) | ✔ | Faça previsões sobre grandes quantidades de dados assíncroneamente. |
| [Serviço Web](how-to-deploy-and-where.md) | &nbsp; | Implementar modelos como um serviço web. |
| [Módulo Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Implementar modelos para dispositivos IoT Edge. |

Para situações em que o SDK não fornece acesso às datastores, poderá ser capaz de criar código personalizado utilizando o Azure SDK relevante para aceder aos dados. Por exemplo, o [Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python) é uma biblioteca de clientes que pode utilizar para aceder a dados armazenados em bolhas ou ficheiros.


## <a name="access-data-in-a-virtual-network"></a>Aceder a dados numa rede virtual

Se o seu armazenamento estiver por detrás de uma rede virtual, deve realizar etapas de configuração adicionais para que o seu espaço de trabalho e datastore acedam aos seus dados. Para obter mais informações sobre como utilizar datastores e conjuntos de dados numa rede virtual, consulte [o isolamento da Rede durante o treino & inferência com redes virtuais privadas](how-to-enable-virtual-network.md#use-datastores-and-datasets).

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Mover dados para soluções de armazenamento suportadas da Azure

A Azure Machine Learning suporta o acesso a dados a partir do armazenamento de Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database e Azure Database for PostgreSQL. Se estiver a utilizar um armazenamento não suportado, recomendamos que mova os seus dados para soluções de armazenamento suportadas da Azure utilizando [a Azure Data Factory e estes passos](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool). Mover dados para armazenamento suportado pode ajudá-lo a poupar custos de saída de dados durante experiências de aprendizagem automática. 

A Azure Data Factory fornece transferência de dados eficiente e resiliente com mais de 80 conectores pré-construídos sem custos adicionais. Estes conectores incluem serviços de dados Azure, fontes de dados no local, Amazon S3 e Redshift, e Google BigQuery.

## <a name="next-steps"></a>Passos seguintes

* [Criar um conjunto de dados de aprendizagem de máquinas Azure](how-to-create-register-datasets.md)
* [Preparar um modelo](how-to-train-ml-models.md)
* [Implementar um modelo](how-to-deploy-and-where.md)
