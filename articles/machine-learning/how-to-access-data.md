---
title: Conecte-se aos serviços de armazenamento em Azure
titleSuffix: Azure Machine Learning
description: Saiba como utilizar as lojas de dados para ligar de forma segura aos serviços de armazenamento da Azure durante o treino com a Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 11/03/2020
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: 78b7bab204a08b474ea3c5cf5c2f7735c019a9c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519933"
---
# <a name="connect-to-storage-services-on-azure"></a>Conecte-se aos serviços de armazenamento em Azure

Neste artigo, aprenda a ligar-se aos serviços de armazenamento de dados em Azure com as lojas de dados Azure Machine Learning e a [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro).

As datas conectam-se de forma segura ao seu serviço de armazenamento no Azure sem colocar em risco as suas credenciais de autenticação e a integridade da sua fonte de dados original. Armazenam informações de conexão, como o ID de subscrição e a autorização simbólica no [cofre](https://azure.microsoft.com/services/key-vault/) chave que está associado ao espaço de trabalho, para que possa aceder de forma segura ao seu armazenamento sem ter de os codificar nos scripts. Pode criar datastores que se liguem a [estas soluções de armazenamento Azure](#matrix).

Para entender onde as datas-tores se encaixam no fluxo de trabalho global de acesso a dados da Azure Machine Learning, consulte o artigo [de dados de acesso seguro.](concept-data.md#data-workflow)

Para uma experiência de baixo código, consulte como utilizar o [estúdio Azure Machine Learning para criar e registar datastores.](how-to-connect-data-ui.md#create-datastores)

>[!TIP]
> Este artigo pressupõe que pretende ligar-se ao seu serviço de armazenamento com credenciais de autenticação baseadas em credenciais, como um principal de serviço ou um token de assinatura de acesso partilhado (SAS). Tenha em mente que, se as credenciais forem registadas nas datastores, todos os utilizadores com função de *leitor* de espaço de trabalho são capazes de recuperar estas credenciais. [Saiba mais sobre o papel do *workspace Reader.*](how-to-assign-roles.md#default-roles) <br><br>Se isso for uma preocupação, aprenda a [ligar-se aos serviços de armazenamento com acesso à identidade.](how-to-identity-based-data-access.md) <br><br>Esta capacidade é uma funcionalidade de pré-visualização [experimental](/python/api/overview/azure/ml/#stable-vs-experimental) e pode mudar a qualquer momento. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

- Uma conta de armazenamento Azure com um [tipo de armazenamento suportado](#matrix).

- [O Azure Machine Learning SDK para Python.](/python/api/overview/azure/ml/intro)

- Uma área de trabalho do Azure Machine Learning.
  
  Ou [cria um espaço de trabalho de aprendizagem automática Azure](how-to-manage-workspace.md) ou utiliza um existente através do Python SDK. 

    Importe a `Workspace` classe e `Datastore` carregue as suas informações de subscrição do ficheiro `config.json` utilizando a função `from_config()` . Isto procura o ficheiro JSON no diretório atual por predefinição, mas também pode especificar um parâmetro de caminho para apontar para o ficheiro usando `from_config(path="your/file/path")` .

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

    Quando cria um espaço de trabalho, um recipiente de blob Azure e uma partilha de ficheiros Azure são automaticamente registados como datastores para o espaço de trabalho. São nomeados `workspaceblobstore` `workspacefilestore` e, respectivamente. É `workspaceblobstore` usado para armazenar artefactos do espaço de trabalho e seus registos de experiências de aprendizagem automática. Também é definido como a **datastore padrão** e não pode ser eliminado do espaço de trabalho. O `workspacefilestore` é usado para armazenar cadernos e scripts R autorizados através de instância [computacional](./concept-compute-instance.md#accessing-files).
    
    > [!NOTE]
    > O designer de Machine Learning Azure criará uma loja de dados com o nome **azureml_globaldatasets** automaticamente quando abrir uma amostra na página inicial do designer. Esta loja de dados contém apenas conjuntos de dados de amostra. Não  utilize esta loja de dados para qualquer acesso confidencial aos dados.

<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Tipos de serviços de armazenamento de dados suportados

As datas-lojas suportam atualmente o armazenamento de informações de ligação aos serviços de armazenamento listados na seguinte matriz. 

> [!TIP]
> **Para soluções de armazenamento não suportadas**, e para poupar o custo da saída de dados durante as experiências de ML, [mova os seus dados](#move) para uma solução de armazenamento Azure suportada. 

| Tipo de armazenamento &nbsp; | Tipo de autenticação &nbsp; | [Estúdio Azure &nbsp; Machine &nbsp; Learning](https://ml.azure.com/) | [Azure &nbsp; Máquina &nbsp; aprendendo Python &nbsp; SDK](/python/api/overview/azure/ml/intro) |  [Azure &nbsp; Machine &nbsp; Learning CLI](reference-azure-machine-learning-cli.md) | [Azure &nbsp; Machine &nbsp; Learning &nbsp; Rest API](/rest/api/azureml/) | Código VS
---|---|---|---|---|---|---
[Armazenamento Azure &nbsp; Blob &nbsp;](../storage/blobs/storage-blobs-overview.md)| Chave da conta <br> Token de SAS | ✓ | ✓ | ✓ |✓ |✓
[Azure &nbsp; File &nbsp; Share](../storage/files/storage-files-introduction.md)| Chave da conta <br> Token de SAS | ✓ | ✓ | ✓ |✓|✓
[Azure &nbsp; Data Lake Storage Gen &nbsp; &nbsp; 1](../data-lake-store/index.yml)| Service principal (Principal de serviço)| ✓ | ✓ | ✓ |✓|
[Azure &nbsp; Data Lake Storage Gen &nbsp; &nbsp; 2](../storage/blobs/data-lake-storage-introduction.md)| Service principal (Principal de serviço)| ✓ | ✓ | ✓ |✓|
[&nbsp;Base de Dados Azure SQL &nbsp;](../azure-sql/database/sql-database-paas-overview.md)| Autenticação do SQL <br>Service principal (Principal de serviço)| ✓ | ✓ | ✓ |✓|
[Azure &nbsp; PostgreSQL](../postgresql/overview.md) | Autenticação do SQL| ✓ | ✓ | ✓ |✓|
[Base de Dados Azure &nbsp; &nbsp; para &nbsp; MySQL](../mysql/overview.md) | Autenticação do SQL|  | ✓* | ✓* |✓*|
[Sistema de Ficheiros databricks &nbsp; &nbsp;](/azure/databricks/data/databricks-file-system)| Sem autenticação | | ✓** | ✓ ** |✓** |

\* MySQL é suportado apenas para pipeline [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep)<br />
\*\* Databricks é suportado apenas para pipeline [DatabricksStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep)


### <a name="storage-guidance"></a>Orientações de armazenamento

Recomendamos a criação de uma loja de dados para um [recipiente Azure Blob](../storage/blobs/storage-blobs-introduction.md). Tanto o armazenamento standard como o armazenamento premium estão disponíveis para bolhas. Embora o armazenamento premium seja mais caro, as suas velocidades de produção mais rápidas podem melhorar a velocidade dos seus treinos, especialmente se treinar contra um grande conjunto de dados. Para obter informações sobre o custo das contas de armazenamento, consulte a [calculadora de preços da Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[A azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) é construída em cima do armazenamento Azure Blob e projetada para análise de big data da empresa. Uma parte fundamental do Data Lake Storage Gen2 é a adição de um [espaço hierárquico](../storage/blobs/data-lake-storage-namespace.md) de nomes para o armazenamento blob. O espaço hierárquico organiza objetos/ficheiros numa hierarquia de diretórios para um acesso eficiente aos dados.

## <a name="storage-access-and-permissions"></a>Acesso ao armazenamento e permissões

Para garantir uma ligação segura ao seu serviço de armazenamento Azure, o Azure Machine Learning requer que tenha permissão para aceder ao recipiente de armazenamento de dados correspondente. Este acesso depende das credenciais de autenticação utilizadas para registar a datastore. 

### <a name="virtual-network"></a>Rede virtual 

Por predefinição, o Azure Machine Learning não consegue comunicar com uma conta de armazenamento que está por trás de uma firewall ou dentro de uma rede virtual. Se a sua conta de armazenamento de dados estiver numa **rede virtual,** são necessários passos de configuração adicionais para garantir que o Azure Machine Learning tenha acesso aos seus dados. 

> [!NOTE]
> Esta orientação aplica-se também às [datas-lojas criadas com acesso de dados baseados na identidade (pré-visualização)](how-to-identity-based-data-access.md). 

**Para os utilizadores do Python SDK**, aceder aos seus dados através do seu script de treino num alvo de computação, o alvo do cálculo precisa de estar dentro da mesma rede virtual e sub-rede do armazenamento.  

**Para os utilizadores de estúdios Azure Machine Learning,** várias funcionalidades dependem da capacidade de ler dados a partir de um conjunto de dados; tais como pré-visualizações de conjuntos de dados, perfis e aprendizagem automática de máquinas. Para que estas funcionalidades funcionem com armazenamento por detrás de redes virtuais, utilize um [espaço de trabalho gerido identidade no estúdio](how-to-enable-studio-virtual-network.md) para permitir que a Azure Machine Learning aceda à conta de armazenamento de fora da rede virtual. 

O Azure Machine Learning pode receber pedidos de clientes fora da rede virtual. Para garantir que a entidade que solicita dados do serviço é segura, [crie o Azure Private Link para o seu espaço de trabalho.](how-to-configure-private-link.md)

### <a name="access-validation"></a>Validação de acesso

**Como parte do processo inicial de criação e registo de datastore, o** Azure Machine Learning valida automaticamente que o serviço de armazenamento subjacente existe e o utilizador fornecido principal (nome de utilizador, principal de serviço ou token SAS) tem acesso ao armazenamento especificado.

**Após a criação da datastore,** esta validação é realizada apenas para métodos que requerem acesso ao recipiente de armazenamento subjacente, **e não** cada vez que os objetos da datas são recuperados. Por exemplo, a validação acontece se pretender descarregar ficheiros da sua datastore; mas se apenas quiser alterar a sua datastore padrão, então a validação não acontece.

Para autenticar o seu acesso ao serviço de armazenamento subjacente, pode fornecer a sua chave de conta, fichas de acesso partilhado (SAS) ou principal de serviço no método correspondente `register_azure_*()` do tipo de loja de dados que pretende criar. A [matriz do tipo de armazenamento](#matrix) lista os tipos de autenticação suportados que correspondem a cada tipo de datastore.

Pode encontrar a chave de conta, o token SAS e informações principais do serviço no seu [portal Azure.](https://portal.azure.com)

* Se pretender utilizar uma chave de conta ou um sinal SAS para autenticação, selecione **Contas de Armazenamento** no painel esquerdo e escolha a conta de armazenamento que pretende registar. 
  * A **página 'Vista Geral'** fornece informações como o nome da conta, o contentor e o nome da partilha de ficheiros. 
      1. Para obter as teclas de conta, aceda às **teclas de acesso** no painel **de definições.** 
      1. Para fichas SAS, aceda a **assinaturas de acesso compartilhadas** no painel **de Definições.**

* Se planeia utilizar um principal de serviço para autenticação, vá às **inscrições da** sua App e selecione qual a aplicação que pretende utilizar. 
    * A sua página **geral** correspondente conterá informações necessárias, como iD do inquilino e identificação do cliente.

> [!IMPORTANT]
> * Se precisar de alterar as suas teclas de acesso para uma conta de Armazenamento Azure (chave de conta ou token SAS), certifique-se de sincronizar as novas credenciais com o seu espaço de trabalho e as lojas de dados ligadas a ela. Saiba como [sincronizar as suas credenciais atualizadas.](how-to-change-storage-access-key.md) 
### <a name="permissions"></a>Permissões

Para o recipiente de blob Azure e o armazenamento do Azure Data Lake Gen 2, certifique-se de que as suas credenciais de autenticação têm acesso **ao Leitor de Dados Blob de Armazenamento.** Saiba mais sobre [o Storage Blob Data Reader](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Uma conta SAS não tem permissões. 
* Para **o acesso à leitura** de dados, as suas credenciais de autenticação devem ter um mínimo de lista e ler permissões para contentores e objetos. 

* Para que os dados **escrevam acesso,** escreva e adicione permissões também são necessárias.

<a name="python"></a>

## <a name="create-and-register-datastores"></a>Criar e registar datastores

Quando regista uma solução de armazenamento Azure como uma datastore, cria e regista automaticamente essa datastore para um espaço de trabalho específico. Reveja a secção [de permissões de acesso ao armazenamento &](#storage-access-and-permissions) para obter orientação sobre cenários de rede virtuais e onde encontrar as credenciais de autenticação necessárias. 

Dentro desta secção encontram-se exemplos de como criar e registar uma loja de dados através do Python SDK para os seguintes tipos de armazenamento. Os parâmetros fornecidos nestes exemplos são os **parâmetros necessários** para criar e registar uma datastore.

* [Recipiente de bolhas Azure](#azure-blob-container)
* [Azure partilha de ficheiros](#azure-file-share)
* [Azure Data Lake Storage Generation 2](#azure-data-lake-storage-generation-2)

 Para criar datastores para outros serviços de armazenamento suportados, consulte a [documentação de referência para os `register_azure_*` métodos aplicáveis.](/python/api/azureml-core/azureml.core.datastore.datastore#methods)

Se preferir uma experiência de código baixo, consulte [Connect to data with Azure Machine Learning studio](how-to-connect-data-ui.md).
>[!IMPORTANT]
> Se não registar e re-registar uma loja de dados com o mesmo nome, e falhar, o Cofre da Chave Azure para o seu espaço de trabalho pode não ter a eliminação suave ativada. Por predefinição, a eliminação suave está ativada para a instância do cofre de chaves criada pelo seu espaço de trabalho, mas pode não ser ativada se utilizar um cofre-chave existente ou se tiver um espaço de trabalho criado antes de outubro de 2020. Para obter informações sobre como permitir a eliminação suave, consulte [Ativar o Soft Delete para obter um cofre de chaves existente]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault)."

> [!NOTE]
> O nome datastore deve consistir apenas em letras minúsculas, dígitos e sublinhados. 

### <a name="azure-blob-container"></a>Recipiente de bolhas Azure

Para registar um recipiente de bolhas Azure como uma loja de dados, utilize [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

O código a seguir cria e regista a `blob_datastore_name` datastore para o `ws` espaço de trabalho. Esta loja de dados acede ao `my-container-name` recipiente blob na `my-account-name` conta de armazenamento, utilizando a chave de acesso à conta fornecida. Reveja a secção [de permissões de acesso ao armazenamento &](#storage-access-and-permissions) para obter orientação sobre cenários de rede virtuais e onde encontrar as credenciais de autenticação necessárias. 

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

### <a name="azure-file-share"></a>Partilha de ficheiros do Azure

Para registar uma partilha de ficheiros Azure como uma datastore, utilize [`register_azure_file_share()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) . 

O código a seguir cria e regista a `file_datastore_name` datastore para o `ws` espaço de trabalho. Esta loja de dados acede à parte do `my-fileshare-name` ficheiro na conta de `my-account-name` armazenamento, utilizando a chave de acesso à conta fornecida. Reveja a secção [de permissões de acesso ao armazenamento &](#storage-access-and-permissions) para obter orientação sobre cenários de rede virtuais e onde encontrar as credenciais de autenticação necessárias. 

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

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Generation 2

Para uma data-loja de dados Azure Data Lake Storage Generation 2 (ADLS Gen 2), utilize [register_azure_data_lake_gen2()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) para registar uma loja de dados credenciais ligada a um armazenamento Azure DataLake Gen 2 com [permissões principais](../active-directory/develop/howto-create-service-principal-portal.md)de serviço.  

Para utilizar o seu principal serviço, precisa de registar a [sua aplicação](../active-directory/develop/app-objects-and-service-principals.md) e conceder ao serviço o acesso principal aos dados através do controlo de acesso baseado em funções (Azure RBAC) ou das listas de controlo de acesso (ACL). Saiba mais sobre [o controlo de acesso configurado para a ADLS Gen 2](../storage/blobs/data-lake-storage-access-control-model.md). 

O código a seguir cria e regista a `adlsgen2_datastore_name` datastore para o `ws` espaço de trabalho. Esta loja de dados acede ao sistema de ficheiros `test` na `account_name` conta de armazenamento, utilizando as credenciais principais do serviço fornecido. Reveja a secção [de permissões de acesso ao armazenamento &](#storage-access-and-permissions) para obter orientação sobre cenários de rede virtuais e onde encontrar as credenciais de autenticação necessárias. 

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



## <a name="create-datastores-with-other-azure-tools"></a>Criar lojas de dados com outras ferramentas Azure
Além de criar datastores com o Python SDK e o estúdio, também pode usar modelos de Gestor de Recursos Azure ou a extensão do Código VS de Aprendizagem de Máquinas Azure. 

<a name="arm"></a>
### <a name="azure-resource-manager"></a>Azure Resource Manager

Há uma série de modelos [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-datastore-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) que podem ser usados para criar datastores.

Para obter informações sobre a utilização destes modelos, consulte [utilize um modelo de Gestor de Recursos Azure para criar um espaço de trabalho para a aprendizagem de máquinas Azure](how-to-create-workspace-template.md).

### <a name="vs-code-extension"></a>Extensão do Código VS

Se preferir criar e gerir as datas usando a extensão do Código VS de Aprendizagem de Máquinas Azure, visite a [gestão de recursos do Código VS como orientar](how-to-manage-resources-vscode.md#datastores) para saber mais.
<a name="train"></a>
## <a name="use-data-in-your-datastores"></a>Utilize dados nas suas datastores

Depois de criar uma loja de dados, [crie um conjunto de dados Azure Machine Learning](how-to-create-register-datasets.md) para interagir com os seus dados. Os conjuntos de dados embalam os seus dados num objeto consumível avaliado preguiçosamente para tarefas de aprendizagem automática, como o treino. 

Com conjuntos de dados, pode [descarregar ou montar](how-to-train-with-datasets.md#mount-vs-download) ficheiros de qualquer formato dos serviços de armazenamento Azure para formação de modelos num alvo de computação. [Saiba mais sobre como treinar modelos ML com conjuntos de dados.](how-to-train-with-datasets.md)

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Obtenha datastores do seu espaço de trabalho

Para obter uma loja de dados específica registada no espaço de trabalho atual, utilize o [`get()`](/python/api/azureml-core/azureml.core.datastore%28class%29#get-workspace--datastore-name-) método estático na `Datastore` classe:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Para obter a lista de datastores registadas com um determinado espaço de trabalho, pode utilizar a [`datastores`](/python/api/azureml-core/azureml.core.workspace%28class%29#datastores) propriedade num objeto de espaço de trabalho:

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

## <a name="access-data-during-scoring"></a>Aceder aos dados durante a pontuação

A Azure Machine Learning fornece várias formas de usar os seus modelos para pontuar. Alguns destes métodos não fornecem acesso a datastores. Utilize a seguinte tabela para entender quais os métodos que lhe permitem aceder às datastores durante a pontuação:

| Método | Acesso à loja de dados | Description |
| ----- | :-----: | ----- |
| [Predição de lote](./tutorial-pipeline-batch-scoring-classification.md) | ✔ | Faça previsões sobre grandes quantidades de dados assíncroneamente. |
| [Serviço Web](how-to-deploy-and-where.md) | &nbsp; | Implementar modelos como um serviço web. |
| [Módulo Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Implementar modelos para dispositivos IoT Edge. |

Para situações em que o SDK não fornece acesso às datastores, poderá ser capaz de criar código personalizado utilizando o Azure SDK relevante para aceder aos dados. Por exemplo, o [Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python) é uma biblioteca de clientes que pode utilizar para aceder a dados armazenados em bolhas ou ficheiros.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Mover dados para soluções de armazenamento suportadas da Azure

A Azure Machine Learning suporta o acesso a dados a partir do armazenamento de Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database e Azure Database for PostgreSQL. Se estiver a utilizar um armazenamento não suportado, recomendamos que mova os seus dados para soluções de armazenamento suportadas da Azure utilizando [a Azure Data Factory e estes passos](../data-factory/quickstart-create-data-factory-copy-data-tool.md). Mover dados para armazenamento suportado pode ajudá-lo a poupar custos de saída de dados durante experiências de aprendizagem automática. 

A Azure Data Factory fornece transferência de dados eficiente e resiliente com mais de 80 conectores pré-construídos sem custos adicionais. Estes conectores incluem serviços de dados Azure, fontes de dados no local, Amazon S3 e Redshift, e Google BigQuery.

## <a name="next-steps"></a>Passos seguintes

* [Criar um conjunto de dados de aprendizagem de máquinas Azure](how-to-create-register-datasets.md)
* [Preparar um modelo](how-to-set-up-training-targets.md)
* [Implementar um modelo](how-to-deploy-and-where.md)