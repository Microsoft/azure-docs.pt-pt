---
title: 'Quickstart: Criar uma fábrica de dados Azure usando Python'
description: Utilize uma fábrica de dados para copiar dados de um local no armazenamento da Azure Blob para outro local.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.devlang: python
ms.topic: quickstart
ms.date: 01/22/2018
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 1ebeae8e16acf4ca2b82270cc6ec40151794ad58
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497338"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-python"></a>Quickstart: Create a data factory and pipeline using Python (Guia de Início Rápido: Criar uma fábrica de dados e um pipeline com o Python)

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](quickstart-create-data-factory-python.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste arranque rápido, cria-se uma fábrica de dados utilizando o Python. O gasoduto nesta fábrica de dados copia dados de uma pasta para outra pasta no armazenamento Azure Blob.

A Azure Data Factory é um serviço de integração de dados baseado na nuvem que permite criar fluxos de trabalho baseados em dados para orquestrar e automatizar movimentos de dados e transformação de dados. Utilizando a Azure Data Factory, pode criar e agendar fluxos de trabalho baseados em dados, chamados oleodutos.

Os oleodutos podem ingerir dados de lojas de dados diferentes. Os oleodutos processam ou transformam dados utilizando serviços de computação como Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning. Os oleodutos publicam dados de produção em lojas de dados como a Azure Synapse Analytics para aplicações de business intelligence (BI).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Python 3.4+](https://www.python.org/downloads/).

* [Uma conta de Armazenamento Azure.](../storage/common/storage-account-create.md)

* [Explorador de Armazenamento Azure](https://storageexplorer.com/) (opcional).

* [Uma aplicação no Azure Ative Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Tome nota dos seguintes valores a utilizar em etapas posteriores: **ID de aplicação,** **chave de autenticação** e **identificação do inquilino.** Atribua a aplicação à **função Contribuinte** seguindo as instruções no mesmo artigo.

## <a name="create-and-upload-an-input-file"></a>Criar e carregar um ficheiro de entrada

1. Inicie o Bloco de notas. Copie o texto seguinte e guarde-o como **input.txt** no disco.

    ```text
    John|Doe
    Jane|Doe
    ```
2.  Utilize ferramentas como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/) para criar o contentor **adfv2tutorial** e a pasta **entrada** no mesmo. Em seguida, carregue o ficheiro **input.txt** para a pasta **entrada**.

## <a name="install-the-python-package"></a>Instalar o pacote do Python

1. Abra um terminal ou uma linha de comandos com privilégios de administrador. 
2. Em primeiro lugar, instale o pacote do Python para recursos de gestão do Azure:

    ```python
    pip install azure-mgmt-resource
    ```
3. Para instalar o pacote do Python para o Data Factory, execute o seguinte comando:

    ```python
    pip install azure-mgmt-datafactory
    ```

    O [Python SDK para data factory](https://github.com/Azure/azure-sdk-for-python) suporta Python 2.7, 3.3, 3.4, 3.5, 3.6 e 3.7.

## <a name="create-a-data-factory-client"></a>Criar um cliente de fábrica de dados

1. Crie um ficheiro denominado **datafactory.py**. Adicione as seguintes instruções para adicionar referências aos espaços de nomes.

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.datafactory import DataFactoryManagementClient
    from azure.mgmt.datafactory.models import *
    from datetime import datetime, timedelta
    import time
    ```
2. Adicione as seguintes funções que imprimem informações.

    ```python
    def print_item(group):
        """Print an Azure object instance."""
        print("\tName: {}".format(group.name))
        print("\tId: {}".format(group.id))
        if hasattr(group, 'location'):
            print("\tLocation: {}".format(group.location))
        if hasattr(group, 'tags'):
            print("\tTags: {}".format(group.tags))
        if hasattr(group, 'properties'):
            print_properties(group.properties)

    def print_properties(props):
        """Print a ResourceGroup properties instance."""
        if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
            print("\tProperties:")
            print("\t\tProvisioning State: {}".format(props.provisioning_state))
        print("\n\n")

    def print_activity_run_details(activity_run):
        """Print activity run details."""
        print("\n\tActivity run details\n")
        print("\tActivity run status: {}".format(activity_run.status))
        if activity_run.status == 'Succeeded':
            print("\tNumber of bytes read: {}".format(activity_run.output['dataRead']))
            print("\tNumber of bytes written: {}".format(activity_run.output['dataWritten']))
            print("\tCopy duration: {}".format(activity_run.output['copyDuration']))
        else:
            print("\tErrors: {}".format(activity_run.error['message']))
    ```
3. Adicione o código seguinte ao método **Main** que cria uma instância da classe DataFactoryManagementClient. Utilize este objeto para criar uma fábrica de dados, um serviço ligado, conjuntos de dados e um pipeline. Também pode utilizar este objeto para monitorizar os detalhes de execução do pipeline. Defina a variável **subscription_id** como o ID da sua subscrição do Azure. Para obter uma lista de regiões do Azure em que o Data Factory está atualmente disponível, selecione as regiões que lhe interessam na página seguinte e, em seguida, expanda **Analytics** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras regiões.

    ```python
    def main():

        # Azure subscription ID
        subscription_id = '<Specify your Azure Subscription ID>'

        # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
        rg_name = 'ADFTutorialResourceGroup'

        # The data factory name. It must be globally unique.
        df_name = '<Specify a name for the data factory. It must be globally unique>'

        # Specify your Active Directory client ID, client secret, and tenant ID
        credentials = ServicePrincipalCredentials(client_id='<Active Directory application/client ID>', secret='<client secret>', tenant='<Active Directory tenant ID>')
        resource_client = ResourceManagementClient(credentials, subscription_id)
        adf_client = DataFactoryManagementClient(credentials, subscription_id)

        rg_params = {'location':'eastus'}
        df_params = {'location':'eastus'}
    ```

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Adicione o código seguinte ao método **Main** que cria uma **fábrica de dados**. Se o grupo de recursos já existir, comente a primeira instrução `create_or_update`.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a data factory
    df_resource = Factory(location='eastus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)
```

## <a name="create-a-linked-service"></a>Criar um serviço ligado

Adicione o código seguinte ao método **Main** que cria um **serviço ligado do Armazenamento do Azure**.

Os serviços ligados são criados numa fábrica de dados para ligar os seus arquivos de dados e serviços de computação a essa fábrica de dados. Neste início rápido, só precisa de criar um serviço ligado do Armazenamento do Azure como origem de cópia e arquivo sink, com o nome "AzureStorageLinkedService" no exemplo. Substitua `<storageaccountname>` e `<storageaccountkey>` pelo nome e pela chave da sua conta de Armazenamento do Azure.

```python
    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString(value='DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<storageaccountkey>')

    ls_azure_storage = AzureStorageLinkedService(connection_string=storage_string)
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)
```
## <a name="create-datasets"></a>Criar conjuntos de dados

Nesta secção, pode criar dois conjuntos de dados: um para a origem e outro para o sink.

### <a name="create-a-dataset-for-source-azure-blob"></a>Criar um conjunto de dados para o Blob do Azure de origem

Adicione o código seguinte ao método Main, que cria um conjunto de dados de blobs do Azure. Para obter mais informações sobre estas propriedades do conjunto de dados do Blob do Azure, consulte o artigo [Conector de blobs do Azure](connector-azure-blob-storage.md#dataset-properties).

Defina um conjunto de dados que represente os dados de origem no Blob do Azure. Este conjunto de dados do Blob refere-se ao serviço ligado de Armazenamento do Azure que criou no passo anterior.

```python
    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path= 'adfv2tutorial/input'
    blob_filename = 'input.txt'
    ds_azure_blob= AzureBlobDataset(linked_service_name=ds_ls, folder_path=blob_path, file_name = blob_filename)
    ds = adf_client.datasets.create_or_update(rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Criar um conjunto de dados para o Blob do Azure de sink

Adicione o código seguinte ao método Main, que cria um conjunto de dados de blobs do Azure. Para obter mais informações sobre estas propriedades do conjunto de dados do Blob do Azure, consulte o artigo [Conector de blobs do Azure](connector-azure-blob-storage.md#dataset-properties).

Defina um conjunto de dados que represente os dados de origem no Blob do Azure. Este conjunto de dados do Blob refere-se ao serviço ligado de Armazenamento do Azure que criou no passo anterior.

```python
    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = 'adfv2tutorial/output'
    dsOut_azure_blob = AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath)
    dsOut = adf_client.datasets.create_or_update(rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)
```

## <a name="create-a-pipeline"></a>Criar um pipeline

Adicione o código seguinte ao método **Main** que cria um **pipeline com uma atividade de cópia**.

```python
    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

    #Create a pipeline with the copy activity
    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)
```

## <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline

Adicione o código seguinte ao método **Main** que **aciona uma execução de pipeline**.

```python
    #Create a pipeline run.
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})
```

## <a name="monitor-a-pipeline-run"></a>Monitorizar uma execução de pipeline

Para monitorizar a execução do pipeline, adicione o código seguinte ao método **Main**:

```python
    #Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])

```

Agora, adicione a instrução seguinte para invocar o método **main** quando o programa é executado:

```python
# Start the main method
main()
```

## <a name="full-script"></a>Script completo

Eis o código de Python completo:

```python
from azure.common.credentials import ServicePrincipalCredentials
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.datafactory import DataFactoryManagementClient
from azure.mgmt.datafactory.models import *
from datetime import datetime, timedelta
import time


def print_item(group):
    """Print an Azure object instance."""
    print("\tName: {}".format(group.name))
    print("\tId: {}".format(group.id))
    if hasattr(group, 'location'):
        print("\tLocation: {}".format(group.location))
    if hasattr(group, 'tags'):
        print("\tTags: {}".format(group.tags))
    if hasattr(group, 'properties'):
        print_properties(group.properties)
    print("\n")


def print_properties(props):
    """Print a ResourceGroup properties instance."""
    if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
        print("\tProperties:")
        print("\t\tProvisioning State: {}".format(props.provisioning_state))
    print("\n")


def print_activity_run_details(activity_run):
    """Print activity run details."""
    print("\n\tActivity run details\n")
    print("\tActivity run status: {}".format(activity_run.status))
    if activity_run.status == 'Succeeded':
        print("\tNumber of bytes read: {}".format(
            activity_run.output['dataRead']))
        print("\tNumber of bytes written: {}".format(
            activity_run.output['dataWritten']))
        print("\tCopy duration: {}".format(
            activity_run.output['copyDuration']))
    else:
        print("\tErrors: {}".format(activity_run.error['message']))


def main():

    # Azure subscription ID
    subscription_id = '<your Azure subscription ID>'

    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<Azure resource group name>'

    # The data factory name. It must be globally unique.
    df_name = '<Your data factory name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ServicePrincipalCredentials(
        client_id='<Active Directory client ID>', secret='<client secret>', tenant='<tenant ID>')
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)

    rg_params = {'location': 'eastus'}
    df_params = {'location': 'eastus'}

    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    # Create a data factory
    df_resource = Factory(location='eastus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)

    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService'

    # Specify the name and key of your Azure Storage account
    storage_string = SecureString(
        value='DefaultEndpointsProtocol=https;AccountName=<storage account name>;AccountKey=<storage account key>')

    ls_azure_storage = AzureStorageLinkedService(
        connection_string=storage_string)
    ls = adf_client.linked_services.create_or_update(
        rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)

    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path = 'adfv2tutorial/input'
    blob_filename = 'input.txt'
    ds_azure_blob = AzureBlobDataset(
        linked_service_name=ds_ls, folder_path=blob_path, file_name=blob_filename)
    ds = adf_client.datasets.create_or_update(
        rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)

    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = 'adfv2tutorial/output'
    dsOut_azure_blob = AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath)
    dsOut = adf_client.datasets.create_or_update(
        rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)

    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name, inputs=[dsin_ref], outputs=[
                                 dsOut_ref], source=blob_source, sink=blob_sink)

    # Create a pipeline with the copy activity
    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(
        activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)

    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})

    # Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(
        rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])


# Start the main method
main()
```

## <a name="run-the-code"></a>Executar o código

Crie e inicie a aplicação e, em seguida, verifique a execução de pipeline.

A consola imprime o progresso da criação da fábrica de dados, o serviço ligado, os conjuntos de dados, o pipeline e a execução de pipeline. Aguarde até ver os detalhes da execução da atividade de cópia com o tamanho dos dados lidos/escritos. Em seguida, utilize ferramentas como o [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para verificar se os blobs são copiados para "outputBlobPath" a partir de "inputBlobPath", conforme especificou nas variáveis.

Segue-se o resultado do exemplo:

```json
Name: <data factory name>
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>
Location: eastus
Tags: {}

Name: storageLinkedService
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/linkedservices/storageLinkedService

Name: ds_in
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_in

Name: ds_out
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_out

Name: copyPipeline
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/pipelines/copyPipeline

Pipeline run status: Succeeded
Datetime with no tzinfo will be considered UTC.
Datetime with no tzinfo will be considered UTC.

Activity run details

Activity run status: Succeeded
Number of bytes read: 18
Number of bytes written: 18
Copy duration: 4
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para eliminar a fábrica de dados, adicione o código abaixo ao programa:

```python
adf_client.factories.delete(rg_name, df_name)
```

## <a name="next-steps"></a>Passos seguintes

O pipeline neste exemplo copia dados de uma localização para outra localização num armazenamento de blobs do Azure. Leia os [tutoriais](tutorial-copy-data-dot-net.md) para saber como utilizar o Data Factory em mais cenários.
