---
title: Ligue-se aos serviços de armazenamento azure
titleSuffix: Azure Machine Learning
description: Saiba como usar as lojas de dados para se conectar de forma segura aos serviços de armazenamento Azure durante o treino com o Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: f5a7605a1fa68c3a600c77ded762722990d7a514
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231208"
---
# <a name="connect-to-azure-storage-services"></a>Ligue-se aos serviços de armazenamento azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprenda a ligar-se aos serviços de armazenamento Azure através de lojas de dados Azure Machine Learning. As lojas de dados armazenam informações de ligação, como o seu ID de subscrição e autorização simbólica no seu [Cofre chave](https://azure.microsoft.com/services/key-vault/) associado ao espaço de trabalho, para que possa aceder de forma segura ao seu armazenamento sem ter de os codificar nos seus scripts. Para entender onde as lojas de dados se encaixam no fluxo de trabalho global de acesso a dados da Azure Machine Learning, consulte o artigo de dados de [acesso seguro.](concept-data.md#data-workflow)

Pode criar lojas de dados a partir [destas soluções de armazenamento Azure.](#matrix) Para soluções de armazenamento não suportadas e para economizar custos de egress de dados durante experiências de aprendizagem automática, recomendamos que [mova os seus dados](#move) para soluções de armazenamento Azure suportadas. 

## <a name="prerequisites"></a>Pré-requisitos

Precisa de:
- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

- Uma conta de armazenamento Azure com um [recipiente de blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) ou [partilha de ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- O [Azure Machine Learning SDK para Python,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou acesso ao [estúdio Azure Machine Learning.](https://ml.azure.com/)

- Uma área de trabalho do Azure Machine Learning.
  
  Ou cria um espaço de [trabalho azure machine learning](how-to-manage-workspace.md) ou usa um existente através do Python SDK. Importe `Workspace` `Datastore` a e a classe e `config.json` carregue as `from_config()`suas informações de subscrição a partir do ficheiro utilizando a função . Isto procura o ficheiro JSON no diretório atual por padrão, mas também pode especificar `from_config(path="your/file/path")`um parâmetro de percurso para apontar para o ficheiro usando .

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Tipos de serviços de armazenamento de dados suportados

Atualmente, as lojas de dados suportam armazenar informações de ligação aos serviços de armazenamento listados na seguinte matriz.

| Tipo&nbsp;de armazenamento | Tipo&nbsp;de autenticação | [Estúdio&nbsp;azure machine&nbsp;learning](https://ml.azure.com/) | [Pitão&nbsp;&nbsp;de&nbsp; aprendizagem de máquina sdk azure](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [ClI&nbsp;&nbsp;de aprendizagem automática azure](reference-azure-machine-learning-cli.md) | [API&nbsp;&nbsp;de&nbsp; aprendizagem automática azure](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Armazenamento&nbsp;Azure&nbsp;Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Chave de conta <br> Ficha SAS | ✓ | ✓ | ✓ |✓
[Partilha&nbsp;de&nbsp;ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Chave de conta <br> Ficha SAS | ✓ | ✓ | ✓ |✓
[Armazenamento&nbsp;de&nbsp;lagos&nbsp;azure data gen 1](https://docs.microsoft.com/azure/data-lake-store/)| Service principal (Principal de serviço)| ✓ | ✓ | ✓ |✓
[Armazenamento&nbsp;de&nbsp;lagos&nbsp;azure data gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Service principal (Principal de serviço)| ✓ | ✓ | ✓ |✓
[Base&nbsp;de Dados&nbsp;Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| Autenticação do SQL <br>Service principal (Principal de serviço)| ✓ | ✓ | ✓ |✓
[Azure&nbsp;PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | Autenticação do SQL| ✓ | ✓ | ✓ |✓
[Base&nbsp;de&nbsp;&nbsp;Dados Azure para MySQL](https://docs.microsoft.com/azure/mysql/overview) | Autenticação do SQL|  | ✓* | ✓* |✓*
[Sistema de&nbsp;&nbsp;Ficheiros de Tijolos de Dados](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| Sem autenticação | | ✓** | ✓ ** |✓** 

*MySQL só é suportado para o pipeline [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). <br>
**Databricks só é suportado para pipeline [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)

### <a name="storage-guidance"></a>Orientações de armazenamento

Recomendamos a criação de uma loja de dados para um [recipiente Azure Blob.](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) Tanto o armazenamento standard como o premium estão disponíveis para bolhas. Embora o armazenamento premium seja mais caro, as suas velocidades de produção mais rápidas podem melhorar a velocidade dos seus treinos, especialmente se treinar contra um grande conjunto de dados. Para obter informações sobre o custo das contas de armazenamento, consulte a calculadora de [preços Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[O Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) foi construído em cima do armazenamento da Blob Azure e projetado para a análise de big data da empresa. Uma parte fundamental do Data Lake Storage Gen2 é a adição de um [espaço hierárquico](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) ao armazenamento blob. O espaço hierárquico organiza objetos/ficheiros numa hierarquia de diretórios para um acesso eficiente de dados.

Quando se cria um espaço de trabalho, um recipiente de blob Azure e uma partilha de ficheiros Azure estão automaticamente registados no espaço de trabalho. São nomeados `workspaceblobstore` `workspacefilestore`e, respectivamente. `workspaceblobstore`é usado para armazenar artefactos do espaço de trabalho e os seus registos de experiências de aprendizagem automática. `workspacefilestore`é utilizado para armazenar cadernos e scripts R autorizados por instância de [cálculo](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files). O `workspaceblobstore` recipiente é definido como o datastore padrão.

> [!IMPORTANT]
> O designer de Aprendizagem automática Azure (pré-visualização) criará uma loja de dados chamada **azureml_globaldatasets** automaticamente quando abrir uma amostra na página inicial do designer. Esta loja de dados contém apenas conjuntos de dados de amostras. Por favor, **não** utilize esta loja de dados para qualquer acesso confidencial a dados.
> ![Loja de dados criada automaticamente para conjuntos de dados de amostras de designers](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Criar e registar lojas de dados

Ao registar uma solução de armazenamento Azure como uma loja de dados, cria-se e regista automaticamente essa loja de dados num espaço de trabalho específico. Pode criar e registar lojas de dados num espaço de trabalho utilizando o estúdio [Python SDK](#python-sdk) ou [Azure Machine Learning.](#azure-machine-learning-studio)

>[!IMPORTANT]
> Como parte do processo inicial de criação e registo da loja de dados, o Azure Machine Learning valida que o serviço de armazenamento subjacente existe e que o utilizador fornecido principal (nome de utilizador, diretor de serviço ou token SAS) tem acesso a esse armazenamento. No entanto, para as lojas de dados Azure Data Lake Storage [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) Gen [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) 1 e 2, esta validação acontece mais tarde, quando métodos de acesso a dados como ou são chamados. 
<br><br>
Após a criação de datastore, esta validação é realizada apenas para métodos que requerem acesso ao recipiente de armazenamento subjacente, **e não** cada vez que objetos de datastore são recuperados. Por exemplo, a validação acontece se quiser descarregar ficheiros a partir da sua loja de dados; mas se apenas quiser alterar a sua loja de dados padrão, então a validação não acontece.

### <a name="python-sdk"></a>SDK Python

Todos os métodos [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) de registo estão na aula e têm o formulário. `register_azure_*`
> [!IMPORTANT]
> Se a sua conta de armazenamento estiver numa rede virtual, apenas é suportada a criação de lojas de dados **através do SDK.**

Pode encontrar a informação de `register_azure_*()` que necessita para preencher o método no [portal Azure.](https://portal.azure.com)

* Se planeia utilizar uma chave de conta ou um token SAS para autenticação, selecione Contas de **Armazenamento** no painel esquerdo e escolha a conta de armazenamento que pretende registar. 
  * A página **'Overview'** fornece informações como o nome da conta, o contentor e o nome da partilha de ficheiros. 
      1. Para obter as teclas da conta, aceda às **teclas de acesso** no painel **Definições.** 
      1. Para tokens SAS, aceda a **assinaturas** de acesso partilhado no painel **Definições.**

* Se planeia utilizar um princípio de serviço para autenticação, vá aos **registos** da sua App e selecione qual a aplicação que pretende utilizar. 
    * A sua página **de visão geral** correspondente conterá informações necessárias, como id do inquilino e iD do cliente.

Os exemplos seguintes mostram como registar um contentor de blob Azure, uma partilha de ficheiros Azure, e Azure Data Lake Storage Generation 2 como uma loja de dados. Os parâmetros fornecidos nestes exemplos são os **parâmetros necessários** para criar e registar uma loja de dados. 

Para criar lojas de dados para outros serviços de armazenamento e ver parâmetros opcionais para estes métodos, consulte a [documentação de referência para os métodos `register_azure_*` aplicáveis.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods)

#### <a name="blob-container"></a>Contentor de blobs

Para registar um recipiente de blob Azure como uma loja de dados, utilize [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

O seguinte código cria `blob_datastore_name` e regista `ws` a loja de dados no espaço de trabalho. Esta loja de `my-container-name` dados acede `my-account-name` ao recipiente blob na conta de armazenamento, utilizando a chave de acesso à conta fornecida.

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
Se o seu recipiente de bolhas estiver `skip_validation=True` em [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) rede virtual, inclua o parâmetro no seu método. 

#### <a name="file-share"></a>Partilha de ficheiros

Para registar uma partilha de ficheiros [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)Azure como uma loja de dados, utilize . 

O seguinte código cria `file_datastore_name` e regista `ws` a loja de dados no espaço de trabalho. Esta loja de `my-fileshare-name` dados acede `my-account-name` à parte do ficheiro na conta de armazenamento, utilizando a chave de acesso à conta fornecida.

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
Se a sua quota de ficheiro estiver `skip_validation=True` na [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) rede virtual, inclua o parâmetro no seu método. 

#### <a name="azure-data-lake-storage-generation-2"></a>Geração de Armazenamento de Lagos De Dados Azure 2

Para uma loja de dados Azure Data Lake Storage Generation 2 (ADLS Gen 2), utilize [register_azure_data_lake_gen2()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) para registar uma loja de dados credencial ligada a um armazenamento Azure DataLake Gen 2 com [permissões principais](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)de serviço. Para utilizar o seu diretor de serviço, é necessário registar a [sua aplicação](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) e conceder ao diretor de serviço acesso ao *Storage Blob Data Owner.* Saiba mais sobre o controlo de [acesso criado para ADLS Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Para utilizar o seu diretor de serviço, é necessário registar a [sua aplicação](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) e conceder ao diretor de serviço o acesso certo aos dados. Saiba mais sobre o controlo de [acesso criado para ADLS Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

O seguinte código cria `adlsgen2_datastore_name` e regista `ws` a loja de dados no espaço de trabalho. Esta loja de dados `test` acede `account_name` ao sistema de ficheiros na conta de armazenamento, utilizando as credenciais principais do serviço fornecidas.

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

* Para itens de autenticação, como chave de conta ou ficha SAS, aceda às **teclas de acesso** no painel **Definições.** 

* Para itens principais de serviço como, ID de inquilino e ID do cliente, vá às **suas inscrições** da App e selecione qual a aplicação que pretende utilizar. A sua página **de visão geral** correspondente conterá estes itens. 

O exemplo que se segue demonstra como é o formulário quando se cria uma loja de dados Azure blob: 
    
![Formulário para uma nova datastore](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Obtenha lojas de dados do seu espaço de trabalho

Para obter uma loja de dados específica registada [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) no espaço `Datastore` de trabalho atual, utilize o método estático na classe:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Para obter a lista de datastores registadas com [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) um determinado espaço de trabalho, pode utilizar a propriedade num objeto de espaço de trabalho:

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
Também pode alterar a loja de dados predefinida com o seguinte código. Esta capacidade só é suportada através do SDK. 

```Python
 ws.set_default_datastore(new_default_datastore)
```
<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Enviar e descarregar dados

Os [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) métodos e métodos descritos nos seguintes exemplos são específicos e funcionam de forma idêntica para as classes [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) e [AzureFileDatastore.](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)

> [!NOTE]
> O upload para as lojas de dados AzureDataLakeGen2 não é suportado neste momento.

### <a name="upload"></a>Carregar

Faça upload de um diretório ou de ficheiros individuais para a loja de dados utilizando o Python SDK:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

O `target_path` parâmetro especifica a localização na partilha de ficheiros (ou recipiente de bolha) para carregar. Não se `None`incorre para, para que os dados sejam carregados para raiz. Se, `overwrite=True`os dados `target_path` existentes forem substituídos.

Também pode fazer o upload de uma lista `upload_files()` de ficheiros individuais para a loja de dados através do método.

### <a name="download"></a>Transferência

Descarregue os dados de uma loja de dados para o seu sistema de ficheiros local:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

O `target_path` parâmetro é a localização do diretório local para descarregar os dados para. Para especificar um caminho para a pasta na partilha de ficheiros (ou recipiente blob) para descarregar, forneça esse caminho para `prefix`. Se `prefix` `None`estiver, todos os conteúdos da sua partilha de ficheiros (ou recipiente blob) serão descarregados.

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Aceda aos seus dados durante o treino

Para interagir com dados nas suas datastores ou embalar os seus dados num objeto consumível para tarefas de aprendizagem automática, como treinar, criar um conjunto de [dados de Aprendizagem automática Azure](how-to-create-register-datasets.md). Os conjuntos de dados fornecem funções que carregam dados tabular em pandas ou Spark DataFrame. Os conjuntos de dados também fornecem a capacidade de descarregar ou montar ficheiros de qualquer formato a partir do armazenamento Azure Blob, Ficheiros Azure, Armazenamento de Lagos De dados Azure Gen1, Armazenamento de Lagos Azure, Base de Dados Azure SQL e Base de Dados Azure para PostgreSQL. [Saiba mais sobre como treinar com conjuntos](how-to-train-with-datasets.md)de dados.

### <a name="accessing-source-code-during-training"></a>Acesso ao código fonte durante o treino

O armazenamento da Azure Blob tem velocidades de produção mais elevadas do que uma quota de ficheiros Azure e irá escalar para um grande número de postos de trabalho iniciados em paralelo. Por esta razão, recomendamos configurar as suas correções para utilizar o armazenamento Blob para transferir ficheiros de código fonte.

O exemplo de código que se segue especifica na configuração de execução que blob datastore usar para transferências de código fonte.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Dados de acesso durante a pontuação

O Azure Machine Learning fornece várias formas de utilizar os seus modelos para pontuar. Alguns destes métodos não fornecem acesso a lojas de dados. Utilize a tabela seguinte para entender quais os métodos que lhe permitem aceder às lojas de dados durante a pontuação:

| Método | Acesso a datastore | Descrição |
| ----- | :-----: | ----- |
| [Predição de lote](how-to-use-parallel-run-step.md) | ✔ | Faça previsões sobre grandes quantidades de dados assincronicamente. |
| [Serviço web](how-to-deploy-and-where.md) | &nbsp; | Implementar modelos como um serviço web. |
| [Módulo Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Implementar modelos para dispositivos IoT Edge. |

Para situações em que o SDK não fornece acesso a lojas de dados, poderá ser capaz de criar código personalizado utilizando o SDK Azure relevante para aceder aos dados. Por exemplo, o [Azure Storage SDK para Python](https://github.com/Azure/azure-storage-python) é uma biblioteca de clientes que pode usar para aceder a dados armazenados em blobs ou ficheiros.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Mova dados para soluções de armazenamento Azure suportadas

O Azure Machine Learning suporta o acesso a dados do armazenamento Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database e Azure Database para PostgreSQL. Se estiver a utilizar armazenamento não suportado, recomendamos que desloque os seus dados para soluções de armazenamento Azure suportadas utilizando a [Azure Data Factory e estes passos.](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool) Mover dados para armazenamento suportado pode ajudá-lo a economizar custos de egress durante experiências de aprendizagem automática. 

A Azure Data Factory fornece transferência de dados eficiente e resiliente com mais de 80 conectores pré-construídos sem custos adicionais. Estes conectores incluem serviços de dados Azure, fontes de dados no local, Amazon S3 e Redshift, e Google BigQuery.

## <a name="next-steps"></a>Passos seguintes

* [Criar um conjunto de dados de aprendizagem automática Azure](how-to-create-register-datasets.md)
* [Preparar um modelo](how-to-train-ml-models.md)
* [Implementar um modelo](how-to-deploy-and-where.md)
