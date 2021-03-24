---
title: Discussão de dados com piscinas Apache Spark (pré-visualização)
titleSuffix: Azure Machine Learning
description: Saiba como anexar e lançar piscinas Apache Spark para discussão de dados com a Azure Synapse Analytics e Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 9ced4da7f71a0499e538e499644d89240611f1ea
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104956218"
---
# <a name="attach-apache-spark-pools-powered-by-azure-synapse-analytics-for-data-wrangling-preview"></a>Anexar piscinas Apache Spark (alimentadas por Azure Synapse Analytics) para a luta de dados (pré-visualização)

Neste artigo, você aprende a anexar e lançar uma piscina Apache Spark alimentada pela [Azure Synapse Analytics](/synapse-analytics/overview-what-is.md) para a luta de dados em escala. 

Este artigo contém orientações para a realização de tarefas de conflito de dados interativamente dentro de uma sessão de Sinapse dedicada num caderno Jupyter. Se preferir utilizar os oleodutos Azure Machine Learning, consulte [Como utilizar o Apache Spark (alimentado pela Azure Synapse Analytics) no seu pipeline de aprendizagem automática (pré-visualização)](how-to-use-synapsesparkstep.md).

>[!IMPORTANT]
> A integração Azure Machine Learning e Azure Synapse Analytics está em pré-visualização. As capacidades apresentadas neste artigo empregam o `azureml-synapse` pacote que contém funcionalidades [experimentais](/python/api/overview/azure/ml/#stable-vs-experimental) de pré-visualização que podem mudar a qualquer momento.

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration-preview"></a>Azure Machine Learning e Azure Synapse Analytics integração (pré-visualização)

A integração Azure Synapse Analytics com Azure Machine Learning (pré-visualização) permite-lhe anexar uma piscina Apache Spark apoiada pela Azure Synapse para a exploração e preparação interativa de dados. Com esta integração, você pode ter um computamento dedicado para a disputa de dados em escala, tudo dentro do mesmo caderno Python que você usa para treinar seus modelos de machine learning.

## <a name="prerequisites"></a>Pré-requisitos

* Criar um espaço de trabalho para [aprendizagem automática Azure.](how-to-manage-workspace.md?tabs=python)

* [Crie um espaço de trabalho Azure Synapse Analytics no portal Azure](../synapse-analytics/quickstart-create-workspace.md).

* [Crie a piscina Apache Spark utilizando o portal Azure, ferramentas web ou o Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* [Configure o seu ambiente de desenvolvimento](how-to-configure-environment.md) para instalar o Azure Machine Learning SDK, ou use uma instância de cálculo [Azure Machine Learning](concept-compute-instance.md#create) com o SDK já instalado. 

* Instale a `azureml-synapse` embalagem (pré-visualização) com o seguinte código:

  ```python
  pip install azureml-synapse
  ```

* [Link Azure Machine Learning espaço de trabalho e espaço de trabalho Azure Synapse Analytics](how-to-link-synapse-ml-workspaces.md).

## <a name="get-an-existing-linked-service"></a>Obtenha um serviço ligado existente
Antes de poder anexar um computamento dedicado para a luta de dados, tem de ter um espaço de trabalho ML ligado a um espaço de trabalho Azure Synapse Analytics, este é referido como um serviço ligado. 

Para recuperar e utilizar um serviço ligado existente requer permissões **de Utilizador ou Colaborador** para o espaço de trabalho Azure Synapse Analytics.

Veja todos os serviços ligados associados ao seu espaço de trabalho de aprendizagem automática. 

```python
LinkedService.list(ws)
```

Este exemplo recupera um serviço ligado existente, `synapselink1` a partir do espaço de `ws` trabalho, com o [`get()`](/python/api/azureml-core/azureml.core.linkedservice#get-workspace--name-) método.
```python
linked_service = LinkedService.get(ws, 'synapselink1')
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Anexar a piscina Synapse Spark como um cálculo

Assim que recuperar o serviço ligado, anexe um pool Synapse Apache Spark como um recurso de computação dedicado para as suas tarefas de trabalho de trabalho de trabalho. 

Você pode anexar piscinas Apache Spark através,
* Azure Machine Learning studio
* [Modelos do Azure Resource Manager (ARM)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* O Python SDK 

### <a name="attach-a-pool-via-the-studio"></a>Anexar uma piscina através do estúdio
Siga estes passos: 

1. Inscreva-se no [estúdio Azure Machine Learning.](https://ml.azure.com/)
1. Selecione **Serviços Ligados** na secção **Gerir** o painel esquerdo.
1. Selecione o seu espaço de trabalho Synapse.
1. Selecione **piscinas de faísca anexa** na parte superior esquerda. 
1. **Selecione Anexar**. 
1. Selecione a sua piscina Apache Spark da lista e forneça um nome.  
    1. Esta lista identifica as piscinas Synapse Spark disponíveis que podem ser anexadas ao seu cálculo. 
    1. Para criar uma nova piscina Synapse Spark, consulte [a piscina Create Apache Spark com o Estúdio Synapse](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
1. **Selecione Anexar Selecionado**. 

### <a name="attach-a-pool-with-the-python-sdk"></a>Anexe uma piscina com o Python SDK

Você também pode empregar o **Python SDK** para anexar uma piscina Apache Spark. 

O código de seguimento, 
1. Configura a SinapseCompute com,

   1. O LinkedService, `linked_service` que criou ou recuperou no passo anterior. 
   1. O tipo de meta de cálculo que pretende anexar, `SynapseSpark`
   1. O nome da piscina Apache Spark. Isto deve corresponder a uma piscina Apache Spark existente que está no seu espaço de trabalho Azure Synapse Analytics.
   
1. Cria um ComputeTarget de aprendizagem automática passando, 
   1. O espaço de trabalho de aprendizagem automática que você quer usar, `ws`
   1. O nome que gostaria de referir ao computo dentro do espaço de trabalho Azure Machine Learning. 
   1. O attach_configuration que especificou ao configurar o seu Synapse Compute.
       1. A chamada para ComputeTarget.attach() é assíncronea, por isso a amostra bloqueia até que a chamada esteja concluída.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name="<Synapse Spark pool name>") #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name="<Synapse Spark pool alias in Azure ML>", 
                                       attach_configuration=attach_config
                                      )

synapse_compute.wait_for_completion()
```

Verifique se a piscina Apache Spark está anexada.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-preparation-tasks"></a>Lançar pool de faíscas synapse para tarefas de preparação de dados

Para iniciar a preparação de dados com a piscina Apache Spark, especifique o nome da piscina Apache Spark:

> [!IMPORTANT]
> Para continuar a utilizar o conjunto Apache Spark, deve indicar qual o recurso computacional a utilizar ao longo das suas tarefas de trabalho de trabalho `%synapse` para linhas únicas de código e `%%synapse` para várias linhas. 

```python
%synapse start -c SynapseSparkPoolAlias
```

Após o início da sessão, pode verificar os metadados da sessão.

```python
%synapse meta
```

Pode especificar um [ambiente de aprendizagem automática Azure](concept-environments.md) para utilizar durante a sua sessão de Faísca Apache. Apenas as dependências da Conda especificadas no ambiente produzirão efeito. A imagem do Docker não é suportada.

>[!WARNING]
>  As dependências de Python especificadas no ambiente As dependências conda não são suportadas em piscinas Apache Spark. Atualmente, apenas as versões pitão fixas são suportadas. Verifique a sua versão Python incluindo  `sys.version_info` no seu script.

O seguinte código cria o ambiente, que instala a `myenv` `azureml-core` versão 1.20.0 e `numpy` a versão 1.17.0 antes do início da sessão. Em seguida, pode incluir este ambiente na sua declaração de sessão apache `start` Spark.

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Para iniciar a preparação de dados com a piscina Apache Spark e o seu ambiente personalizado, especifique o nome da piscina Apache Spark e qual o ambiente a utilizar durante a sessão Apache Spark. Além disso, você pode fornecer o seu ID de subscrição, o grupo de recursos de espaço de trabalho de aprendizagem automática, e o nome do espaço de trabalho de machine learning.

```python
%synapse start -c SynapseSparkPoolAlias -e myenv -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName
```
## <a name="load-data-from-storage"></a>Carregar dados do armazenamento

Assim que a sessão apache spark começar, leia os dados que deseja preparar. O carregamento de dados é suportado para o armazenamento de Azure Blob e para as Gerações 1 e 2 de Armazenamento do Lago de Dados Azure.

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
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "<sas token>")

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

O exemplo seguinte autentica-se no espaço de trabalho, obtém um Separadors Registado, `blob_dset` que faz referência a ficheiros no armazenamento de bolhas e converte-o num data-dado de faísca. Quando converter os seus conjuntos de dados num dataframe de faíscas, pode alavancar `pyspark` as bibliotecas de exploração e preparação de dados.  

``` python

%%synapse
from azureml.core import Workspace, Dataset

subscription_id = "<enter your subscription ID>"
resource_group = "<enter your resource group>"
workspace_name = "<enter your workspace name>"

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-wrangling-tasks"></a>Executar tarefas de conflito de dados

Depois de ter recuperado e explorado os seus dados, pode realizar tarefas de luta de dados.

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

Quando tiver concluído a preparação de dados e tiver guardado os seus dados preparados para armazenamento, pare de utilizar a sua piscina Apache Spark com o seguinte comando.

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

Consulte este [caderno final para](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb) um exemplo de código detalhado de como realizar a preparação de dados e a formação de modelos a partir de um único caderno com Azure Synapse Analytics e Azure Machine Learning.

## <a name="next-steps"></a>Passos seguintes

* [Treine um modelo.](how-to-set-up-training-targets.md)
* [Treine com conjunto de dados de aprendizagem automática Azure](how-to-train-with-datasets.md)
