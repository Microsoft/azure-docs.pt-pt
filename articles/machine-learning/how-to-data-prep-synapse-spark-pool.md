---
title: Preparação de dados com piscinas Spark (pré-visualização)
titleSuffix: Azure Machine Learning
description: Saiba como anexar piscinas spark para preparação de dados com Azure Synapse e Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 87e03b6aee122c5a26d4388ca8b570aa6cdf7b55
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662890"
---
# <a name="attach-synapse-spark-pools-for-data-preparation-with-azure-synapse-preview"></a>Anexar piscinas de faíscas de sinapse para preparação de dados com Azure Synapse (pré-visualização)

Neste artigo, você aprende a anexar e lançar uma piscina Apache Spark apoiada pela [Azure Synapse](/synapse-analytics/overview-what-is.md) para a preparação de dados. 

>[!IMPORTANT]
> A Azure Machine Learning e a integração Azure Synapse estão em pré-visualização. As capacidades apresentadas neste artigo empregam o `azureml-synapse` pacote que contém funcionalidades [experimentais](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental) de pré-visualização que podem mudar a qualquer momento.

## <a name="azure-machine-learning-and-azure-synapse-integration-preview"></a>Azure Machine Learning e Azure Synapse integração (pré-visualização)

A integração do Azure Synapse com a Azure Machine Learning (pré-visualização) permite-lhe anexar uma piscina Apache Spark apoiada pela Azure Synapse para a exploração e preparação interativas de dados. Com esta integração, você pode ter um computamento dedicado para a preparação de dados em escala, tudo dentro do mesmo caderno Python que você usa para treinar seus modelos de machine learning.

## <a name="prerequisites"></a>Pré-requisitos

* Criar um espaço de trabalho para [aprendizagem automática Azure.](how-to-manage-workspace.md?tabs=python)

* [Criar um espaço de trabalho sinapse no portal Azure](../synapse-analytics/quickstart-create-workspace.md).

* [Crie a piscina Apache Spark utilizando o portal Azure, ferramentas web ou o Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* [Instale o Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) que inclui o `azureml-synapse` pacote (pré-visualização). 
    * Também pode instalá-lo por si mesmo, mas só é compatível com as versões SDK 1.20 ou superiores. 
        ```python
        pip install azureml-synapse
        ```

## <a name="link-machine-learning-workspace-and-synapse-assets"></a>Link machine learning workspace e ativos de Sinapse

Antes de poder anexar um pool de Faíscas Synapse para a preparação de dados, o seu espaço de trabalho Azure Machine Learning deve estar ligado ao seu espaço de trabalho Azure Synapse. 

Você pode ligar o seu espaço de trabalho ML e o espaço de trabalho Synapse através do [Python SDK](#link-sdk) ou do [estúdio Azure Machine Learning](#link-studio). 

> [!IMPORTANT]
> Para ligar ao espaço de trabalho da Sinapse com sucesso, deve ser-lhe concedido o papel de **Proprietário** do espaço de trabalho synapse. Verifique o seu acesso no [portal Azure](https://ms.portal.azure.com/).
>
> Se não é **proprietário** do espaço de trabalho synapse, mas pretende utilizar um serviço ligado existente, consulte [obter um serviço ligado existente](#get-an-existing-linked-service).


<a name="link-sdk"></a>
### <a name="link-workspaces-with-the-python-sdk"></a>Link espaços de trabalho com o Python SDK

O seguinte código emprega as [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice?preserve-view=true&view=azure-ml-py) classes e as classes [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration?preserve-view=true&view=azure-ml-py) para, 

* Ligue o seu espaço de trabalho de aprendizagem automática, `ws` com o seu espaço de trabalho Azure Synapse. 
* Registe o seu espaço de trabalho synapse com a Azure Machine Learning como um serviço ligado.

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```
> [!IMPORTANT] 
> Uma identidade gerida, `system_assigned_identity_principal_id` é criada para cada serviço ligado. Esta identidade gerida deve ser concedida ao papel de **Administrador de Faíscas De sinapse Apache** do espaço de trabalho synapse antes de iniciar a sua sessão de Sinapse. [Atribua o papel de Administrador de Faíscas Apache Synapse à identidade gerida no Estúdio Synapse](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md).
>
> Para encontrar o `system_assigned_identity_principal_id` serviço de ligação específica, utilize `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` .

<a name="link-studio"></a>
### <a name="link-workspaces-via-studio"></a>Link espaços de trabalho via estúdio

Ligue o seu espaço de trabalho de aprendizagem automática e o espaço de trabalho da Synapse através do estúdio Azure Machine Learning com os seguintes passos: 

1. Inscreva-se no [estúdio Azure Machine Learning.](https://ml.azure.com/)
1. Selecione **Serviços Ligados** na secção **Gerir** o painel esquerdo.
1. Selecione **Adicionar integração**.
1. Na forma de **espaço de trabalho Link,** povoar os campos

   |Campo| Descrição    
   |---|---
   |Nome| Forneça um nome para o seu serviço ligado. Este nome é o que será usado para fazer referência a este serviço particular ligado.
   |Nome da subscrição | Selecione o nome da sua subscrição que está associada ao seu espaço de trabalho de aprendizagem automática. 
   |Espaço de trabalho sinapse | Selecione o espaço de trabalho Synapse a que pretende ligar. 
   
1. Selecione **Seguinte** para abrir o formulário **Select Spark pools (opcional).** Neste formulário, você seleciona qual piscina synapse Spark para anexar ao seu espaço de trabalho

1. Selecione **Seguinte** para abrir o formulário **'Rever'** e verificar as suas seleções. 
1. Selecione **Criar** para completar o processo de criação de serviços ligado.

## <a name="get-an-existing-linked-service"></a>Obtenha um serviço ligado existente

Para recuperar e utilizar um serviço ligado existente requer permissões **de Utilizador ou Contribuinte** para o espaço de trabalho Synapse.

Este exemplo recupera um serviço ligado existente, `synapselink1` a partir do espaço de `ws` trabalho, com o [`get()`](/python/api/azureml-core/azureml.core.linkedservice?preserve-view=true&view=azure-ml-py#get-workspace--name-) método.
```python
linked_service = LinkedService.get(ws, 'synapselink1')
```

### <a name="manage-linked-services"></a>Gerir serviços ligados

Para desvincular os seus espaços de trabalho, use o `unregister()` método

``` python
linked_service.unregister()
```

Veja todos os serviços ligados associados ao seu espaço de trabalho de aprendizagem automática. 

```python
LinkedService.list(ws)
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Anexar a piscina Synapse Spark como um cálculo

Uma vez ligados os seus espaços de trabalho, anexe um pool Synapse Spark como um recurso de computação dedicado para as suas tarefas de preparação de dados. 

Você pode anexar piscinas Synapse Spark através,
* Azure Machine Learning studio
* [Modelos do Azure Resource Manager (ARM)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* O Python SDK 

Siga estes passos para anexar uma piscina Synapse Spark utilizando o estúdio. 

1. Inscreva-se no [estúdio Azure Machine Learning.](https://ml.azure.com/)
1. Selecione **Serviços Ligados** na secção **Gerir** o painel esquerdo.
1. Selecione o seu espaço de trabalho Synapse.
1. Selecione **piscinas de faísca anexa** na parte superior esquerda. 
1. **Selecione Anexar**. 
1. Selecione a sua piscina Synapse Spark da lista e forneça um nome.  
    1. Esta lista identifica as piscinas Synapse Spark disponíveis que podem ser anexadas ao seu cálculo. 
    1. Para criar uma nova piscina Synapse Spark, consulte [a piscina Create Apache Spark com o Estúdio Synapse](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
1. **Selecione Anexar Selecionado**. 


Também pode utilizar o **Python SDK** para anexar uma piscina de faíscas synapse. 

O código de seguimento, 
1. Configura a SinapseCompute com,

   1. O LinkedService, `linked_service` que criou ou recuperou no passo anterior. 
   1. O tipo de meta de cálculo que pretende anexar, `SynapseSpark`
   1. O nome da piscina Synapse Spark. Isto deve corresponder a uma piscina Apache Spark existente que está no seu espaço de trabalho Synapse.
   
1. Cria um ComputeTarget de aprendizagem automática passando, 
   1. O espaço de trabalho de aprendizagem automática que você quer usar, `ws`
   1. O nome que gostaria de referir ao cálculo dentro do espaço de trabalho de aprendizagem automática. 
   1. O attach_configuration que especificou ao configurar o seu SynapseCompute.
       1. A chamada para ComputeTarget.attach() é assíncronea, por isso a amostra bloqueia até que a chamada esteja concluída.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name="<Synapse Spark pool name>") #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name='<Synapse Spark pool alias in Azure ML>', 
                                       attach_configuration=attach_config
                                      )

synapse_compute.wait_for_completion()
```

Verifique se a piscina Synapse Spark está anexada.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-preparation-tasks"></a>Lançar pool de faíscas synapse para tarefas de preparação de dados

Pode especificar um [ambiente de aprendizagem automática Azure](concept-environments.md) para utilizar durante a sessão de Sinapse. Apenas as dependências da Conda especificadas no ambiente produzirão efeito. A imagem do Docker não é suportada.

>[!WARNING]
>  As dependências de Python especificadas no ambiente As dependências conda não são suportadas nas piscinas de Faíscas de Sinapse. Atualmente, apenas as versões pitão fixas são suportadas. Verifique a sua versão Python incluindo  `sys.version_info` no seu script.

O seguinte código cria o ambiente, que instala a `myenv` `azureml-core` versão 1.20.0 e `numpy` a versão 1.17.0 antes do início da sessão. Em seguida, pode incluir este ambiente na sua declaração de sessão de Sinapse. `start`

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Para iniciar a preparação de dados com o pool Synapse Spark, especifique o nome da piscina Synapse Spark e forneça o seu ID de subscrição, o grupo de recursos de espaço de trabalho de aprendizagem automática, o nome do espaço de trabalho de machine learning e qual o ambiente a utilizar durante a sessão de Sinapse. 

> [!IMPORTANT]
> Para continuar a utilizar o pool Synapse Spark, deve indicar qual o recurso computacional a utilizar ao longo das suas tarefas de preparação de dados `%synapse` para linhas únicas de código e `%%synapse` para várias linhas. 

```python
%synapse start -c SynapseSparkPoolAlias -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName -e myenv
```

Após o início da sessão, pode verificar os metadados da sessão.

```python
%synapse meta
```

## <a name="load-data-from-storage"></a>Carregar dados do armazenamento

Assim que a sessão de Sinapse Spark começar, leia os dados que pretende preparar. O carregamento de dados é suportado para o armazenamento de Azure Blob e para as Gerações 1 e 2 de Armazenamento do Lago de Dados Azure.

Existem duas formas de carregar dados destes serviços de armazenamento: 

* Carregue diretamente os dados do armazenamento utilizando o seu caminho Hadoop Distributed Files System (HDFS).

* Leia os dados de um conjunto de dados de [aprendizagem automática Azure](how-to-create-register-datasets.md)existente .

Para aceder a estes serviços de armazenamento, precisa de permissões **de Storage Blob Data Reader.** Se planeia reescrevê-los nestes serviços de armazenamento, precisa de permissões **de Colaborador de Dados blob** de armazenamento. [Saiba mais sobre permissões e funções de armazenamento.](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>Carregar dados com o caminho do Sistema de Ficheiros Distribuídos Hadoop (HDFS)

Para carregar e ler dados a partir do armazenamento com a trajetória HDFS correspondente, é necessário ter as suas credenciais de autenticação de acesso a dados prontamente disponíveis. Estas credenciais diferem consoante o seu tipo de armazenamento.  

O código que se segue demonstra como ler dados de um **armazenamento Azure Blob** num dataframe spark com a sua assinatura de acesso partilhado (SAS) ou a chave de acesso. 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "sas token")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

O código que se segue demonstra como ler dados da **Azure Data Lake Storage Generation 1 (ADLS Gen 1)** com as suas principais credenciais de serviço. 

```python

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
https://login.microsoftonline.com/<tenant id>/oauth2/token)

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

O código que se segue demonstra como ler dados da **Azure Data Lake Storage Generation 2 (ADLS Gen 2)** com as suas principais credenciais de serviço. 

```python
# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
https://login.microsoftonline.com/<tenant id>/oauth2/token)


df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>Ler em dados de conjuntos de dados registados

Também pode obter um conjunto de dados registado existente no seu espaço de trabalho e realizar a preparação de dados sobre o mesmo, convertendo-os num data-dado de faísca.  

O exemplo a seguir obtém um Separador Registado, `blob_dset` que faz referência a ficheiros no armazenamento de bolhas e converte-o num dataframe de faíscas. Quando converter os seus conjuntos de dados num dataframe de faíscas, pode alavancar `pyspark` as bibliotecas de exploração e preparação de dados.  

``` python

%%synapse
from azureml.core import Workspace, Dataset

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-preparation-tasks"></a>Executar tarefas de preparação de dados

Depois de ter recuperado e explorado os seus dados, pode realizar tarefas de preparação de dados.

O seguinte código, expande-se sobre o exemplo HDFS na secção anterior e filtra os dados no dataframe da faísca, `df` com base na coluna **Survivor** e grupos que listam por **Idade**

```python
%%synapse
from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>Guardar dados para armazenamento e parar a sessão de faíscas

Assim que a sua exploração e preparação de dados estiver concluída, guarde os seus dados preparados para posterior utilização na sua conta de armazenamento no Azure.

No exemplo seguinte, os dados preparados são reensitados para o armazenamento da Azure Blob e substituem o ficheiro original `Titanic.csv` no `training_data` diretório. Para voltar a escrever para o armazenamento, precisa de permissões **de Colaborador de Dados Blob de Armazenamento.** [Saiba mais sobre permissões e funções de armazenamento.](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

Quando tiver concluído a preparação de dados e tiver guardado os seus dados preparados para armazenamento, pare de utilizar o seu pool Synapse Spark com o seguinte comando.

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>Criar conjunto de dados para representar dados preparados

Quando estiver pronto para consumir os seus dados preparados para a formação de modelos, ligue-se ao seu armazenamento com uma loja de [dados Azure Machine Learning](how-to-access-data.md)e especifique quais os ficheiros que pretende utilizar com um [conjunto de dados de aprendizagem de máquinas Azure](how-to-create-register-datasets.md).

O seguinte exemplo de código,

* Assumiu que já criou uma datastore que se conecta ao serviço de armazenamento onde guardou os seus dados preparados.  
* Obtém a datastore `mydatastore` existente, a partir do espaço de trabalho, `ws` com o método get().
* Cria um [Dataset de Ficheiros,](how-to-create-register-datasets.md#filedataset) `train_ds` que faz referência aos ficheiros de dados preparados localizados no `training_data` diretório em `mydatastore` .  
* Cria a variável `input1` , que pode ser usada posteriormente para disponibilizar os ficheiros de dados do conjunto de `train_ds` dados para um alvo de computação.

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```

## <a name="example-notebook"></a>Bloco de notas de exemplo

Consulte este [caderno final para](../synapse-analytics/overview-what-is.md) um exemplo de código detalhado de como realizar a preparação de dados e a formação de modelos a partir de um único caderno com Azure Synapse e Azure Machine Learning.

## <a name="next-steps"></a>Passos seguintes

* [Treine um modelo.](how-to-set-up-training-targets.md)
* [Treine com conjunto de dados de aprendizagem automática Azure](how-to-train-with-datasets.md)
* [Criar um conjunto de dados de aprendizagem automática Azure](how-to-create-register-datasets.md).

