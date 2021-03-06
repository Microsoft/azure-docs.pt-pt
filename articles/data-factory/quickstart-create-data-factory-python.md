---
title: 'Quickstart: Criar uma fábrica de dados Azure usando Python'
description: Utilize uma fábrica de dados para copiar dados de um local no armazenamento da Azure Blob para outro local.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.devlang: python
ms.topic: quickstart
ms.date: 04/12/2021
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 534b5b3aca86cc2f6d7ee2d703939420f80abb8e
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365098"
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

* [Python 3.6+](https://www.python.org/downloads/).

* [Uma conta de Armazenamento Azure.](../storage/common/storage-account-create.md)

* [Explorador de Armazenamento Azure](https://storageexplorer.com/) (opcional).

* [Uma aplicação no Azure Ative Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Crie a aplicação seguindo os passos deste link e atribua a aplicação à função  **Contribuinte** seguindo as instruções no mesmo artigo. Tome nota dos seguintes valores, como mostrado no artigo a utilizar em etapas posteriores: **ID de aplicação (id principal de serviço abaixo), chave de autenticação (segredo do cliente abaixo) e ID do inquilino.**

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

    O [Python SDK para data factory](https://github.com/Azure/azure-sdk-for-python) suporta Python 2.7 e 3.6+.

4. Para instalar o pacote Python para autenticação de identidade Azure, executar o seguinte comando:

    ```python
    pip install azure-identity
    ```
    > [!NOTE] 
    > O pacote "identidade azul" pode ter conflitos com "azure-cli" em algumas dependências comuns. Se encontrar algum problema de autenticação, remova "azure-cli" e suas dependências, ou utilize uma máquina limpa sem instalar o pacote "azure-cli" para fazê-lo funcionar.
    > Para nuvens soberanas, você deve usar as constantes específicas da nuvem apropriada.  Consulte a [Connect to all regions using Azure libraries for Python Multi-cloud | Microsoft Docs para instruções para se conectar com Python em nuvens soberanas.](https://docs.microsoft.com/azure/developer/python/azure-sdk-sovereign-domain)
    
    
## <a name="create-a-data-factory-client"></a>Criar um cliente de fábrica de dados

  
1. Crie um ficheiro denominado **datafactory.py**. Adicione as seguintes instruções para adicionar referências aos espaços de nomes.

    ```python
    from azure.identity import ClientSecretCredential 
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
        subscription_id = '<subscription ID>'

        # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
        rg_name = '<resource group>'

        # The data factory name. It must be globally unique.
        df_name = '<factory name>'

        # Specify your Active Directory client ID, client secret, and tenant ID
        credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
        
        # Specify following for Soverign Clouds, import right cloud constant and then use it to connect.
        # from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD as CLOUD
        # credentials = DefaultAzureCredential(authority=CLOUD.endpoints.active_directory, tenant_id=tenant_id)
        
        resource_client = ResourceManagementClient(credentials, subscription_id)
        adf_client = DataFactoryManagementClient(credentials, subscription_id)

        rg_params = {'location':'westus'}
        df_params = {'location':'westus'}
    ```

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Adicione o código seguinte ao método **Main** que cria uma **fábrica de dados**. Se o grupo de recursos já existir, comente a primeira instrução `create_or_update`.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a data factory
    df_resource = Factory(location='westus')
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
    ls_name = 'storageLinkedService001'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString(value='DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;EndpointSuffix=<suffix>')

    ls_azure_storage = LinkedServiceResource(properties=AzureStorageLinkedService(connection_string=storage_string)) 
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
    blob_path = '<container>/<folder path>'
    blob_filename = '<file name>'
    ds_azure_blob = DatasetResource(properties=AzureBlobDataset(
        linked_service_name=ds_ls, folder_path=blob_path, file_name=blob_filename)) 
    ds = adf_client.datasets.create_or_update(
        rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Criar um conjunto de dados para o Blob do Azure de sink

Adicione o código seguinte ao método Main, que cria um conjunto de dados de blobs do Azure. Para obter mais informações sobre estas propriedades do conjunto de dados do Blob do Azure, consulte o artigo [Conector de blobs do Azure](connector-azure-blob-storage.md#dataset-properties).

Defina um conjunto de dados que represente os dados de origem no Blob do Azure. Este conjunto de dados do Blob refere-se ao serviço ligado de Armazenamento do Azure que criou no passo anterior.

```python
    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = '<container>/<folder path>'
    dsOut_azure_blob = DatasetResource(properties=AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath))
    dsOut = adf_client.datasets.create_or_update(
        rg_name, df_name, dsOut_name, dsOut_azure_blob)
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
    
    #Note1: To pass parameters to the pipeline, add them to the json string params_for_pipeline shown below in the format { “ParameterName1” : “ParameterValue1” } for each of the parameters needed in the pipeline.
    #Note2: To pass parameters to a dataflow, create a pipeline parameter to hold the parameter name/value, and then consume the pipeline parameter in the dataflow parameter in the format @pipeline().parameters.parametername.
    
    p_name = 'copyPipeline'
    params_for_pipeline = {}

    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)
```

## <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline

Adicione o código seguinte ao método **Main** que **aciona uma execução de pipeline**.

```python
    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})
```

## <a name="monitor-a-pipeline-run"></a>Monitorizar uma execução de pipeline

Para monitorizar a execução do pipeline, adicione o código seguinte ao método **Main**:

```python
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

```

Agora, adicione a instrução seguinte para invocar o método **main** quando o programa é executado:

```python
# Start the main method
main()
```

## <a name="full-script"></a>Script completo

Eis o código de Python completo:

```python
from azure.identity import ClientSecretCredential 
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


def main():

    # Azure subscription ID
    subscription_id = '<subscription ID>'

    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The data factory name. It must be globally unique.
    df_name = '<factory name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)

    rg_params = {'location':'westus'}
    df_params = {'location':'westus'}
 
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    # Create a data factory
    df_resource = Factory(location='westus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)

    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService001'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString(value='DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;EndpointSuffix=<suffix>')

    ls_azure_storage = LinkedServiceResource(properties=AzureStorageLinkedService(connection_string=storage_string)) 
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)

    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path = '<container>/<folder path>'
    blob_filename = '<file name>'
    ds_azure_blob = DatasetResource(properties=AzureBlobDataset(
        linked_service_name=ds_ls, folder_path=blob_path, file_name=blob_filename))
    ds = adf_client.datasets.create_or_update(
        rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)

    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = '<container>/<folder path>'
    dsOut_azure_blob = DatasetResource(properties=AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath))
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

```console
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
