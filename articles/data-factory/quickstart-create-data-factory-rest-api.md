---
title: Criar uma fábrica de dados do Azure com a API REST | Microsoft Docs
description: Crie uma fábrica de dados do Azure para copiar dados de uma localização no armazenamento de Blobs do Azure para outra localização.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: jingwang
ms.openlocfilehash: c3a7c089bcc6dca2379c84330d3b937b9c7f1167
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57450300"
---
# <a name="quickstart-create-an-azure-data-factory-and-pipeline-by-using-the-rest-api"></a>Início rápido: Criar uma fábrica de dados e um pipeline do Azure com a API REST

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](quickstart-create-data-factory-rest-api.md)

O Azure Data Factory é um serviço de integração de dados baseado na cloud que lhe permite criar fluxos de trabalho condicionados por dados na cloud para orquestrar e automatizar o movimento e a transformação de dados. Com o Azure Data Factory, poderá criar e agendar fluxos de trabalho condicionados por dados (denominados pipelines) que são capazes de ingerir dados provenientes de diferentes arquivos de dados, processar/transformar os dados através de serviços de computação, tais como o Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning, e publicar os dados de saída em arquivos de dados como o Azure SQL Data Warehouse para serem consumidos por aplicações de business intelligence (BI).

Este início rápido descreve como utilizar a API REST para criar uma fábrica de dados do Azure. O pipeline nesta fábrica de dados copia dados de uma localização para outra localização num armazenamento de blobs do Azure.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Subscrição do Azure**. Se não tiver uma subscrição, pode criar uma conta de [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* **Conta de Armazenamento do Azure**. Utilize o armazenamento de blobs como arquivo de dados de **origem** e de **sink**. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md) para obter os passos para criar uma.
* Crie um **contentor de blobs** no Armazenamento de Blobs, crie uma **pasta** de entrada no contentor e carregue alguns ficheiros para a pasta. Pode utilizar ferramentas como o [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para ligar ao Armazenamento de Blobs do Azure, criar contentores de blobs, carregar o ficheiro de entrada e verificar o ficheiro de saída.
* Instale o **Azure PowerShell**. Siga as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-Az-ps). Este início rápido utiliza o PowerShell para invocar chamadas à API REST.
* **Crie uma aplicação no Azure Active Directory** ao seguir [estas instruções](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Tome nota dos seguintes valores que vai utilizar em passos posteriores: **ID da aplicação**, **chave de autenticação** e **ID de inquilino**. Atribua a aplicação à função "**Contribuidor**".

## <a name="set-global-variables"></a>Definir variáveis globais

1. Inicie o **Azure PowerShell**. Mantenha o Azure PowerShell aberto até ao fim deste início rápido. Se o fechar e reabrir, terá de executar os comandos novamente.

    Execute o comando seguinte e introduza o nome de utilizador e a palavra-passe que utiliza para iniciar sessão no Portal do Azure:
    
    ```powershell
    Connect-AzAccount
    ```
    Execute o comando seguinte para ver todas as subscrições desta conta:

    ```powershell
    Get-AzSubscription
    ```
    Execute o comando seguinte para selecionar a subscrição com a qual pretende trabalhar. Substitua **SubscriptionId** pelo ID da sua subscrição do Azure:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. Execute os seguintes comandos depois de substituir os marcadores de posição pelos seus próprios valores para definir as variáveis globais a utilizar em passos posteriores.

    ```powershell
    $tenantID = "<your tenant ID>"
    $appId = "<your application ID>"
    $authKey = "<your authentication key for the application>"
    $subsId = "<your subscription ID to create the factory>"
    $resourceGroup = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $apiVersion = "2018-06-01"
    ```

## <a name="authenticate-with-azure-ad"></a>Autenticar com o Azure AD

Execute os seguintes comandos para autenticar com o Azure Active Directory (AAD):

```powershell
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]"https://login.microsoftonline.com/${tenantId}"
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($appId, $authKey)
$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Execute os seguintes comandos para criar uma fábrica de dados:

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}?api-version=${apiVersion}"
$body = @"
{
    "name": "$dataFactoryName",
    "location": "East US",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Tenha em atenção os seguintes pontos:

* O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro seguinte, altere o nome e tente novamente.

    ```
    Data factory name "ADFv2QuickStartDataFactory" is not available.
    ```
* Para obter uma lista de regiões do Azure em que a fábrica de dados está atualmente disponível, selecione as regiões que lhe interessam, na página seguinte e, em seguida, expanda **Analytics** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras regiões.

Segue-se a resposta de exemplo:

```json
{
    "name": "<dataFactoryName>",
    "tags": {
    },
    "properties":  {
        "provisioningState":  "Succeeded",
        "loggingStorageAccountKey":  "**********",
        "createTime":  "2017-09-14T06:22:59.9106216Z",
        "version":  "2018-06-01"
    },
    "identity":  {
        "type":  "SystemAssigned",
        "principalId":  "<service principal ID>",
        "tenantId":  "<tenant ID>"
    },
    "id":  "dataFactoryName",
    "type":  "Microsoft.DataFactory/factories",
    "location":  "East US"
}
```

## <a name="create-linked-services"></a>Criar serviços ligados

Os serviços ligados são criados numa fábrica de dados para ligar os seus arquivos de dados e serviços de computação a essa fábrica de dados. Neste início rápido, só precisa de criar um serviço ligado do Armazenamento do Azure como origem de cópia e arquivo sink, com o nome "AzureStorageLinkedService" no exemplo.

Execute os seguintes comandos para criar um serviço ligado com o nome **AzureStorageLinkedService**:

Substitua &lt;accountName&gt; e &lt;accountKey&gt; pelo nome e chave da sua conta de armazenamento do Azure antes de executar os comandos.

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/linkedservices/AzureStorageLinkedService?api-version=${apiVersion}"
$body = @"
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                "type": "SecureString"
            }
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Segue-se o resultado do exemplo:

```json
{
    "id":  "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/linkedservices/AzureStorageLinkedService",
    "name":  "AzureStorageLinkedService",
    "properties":  {
        "type":  "AzureStorage",
        "typeProperties":  {
            "connectionString":  "@{value=**********; type=SecureString}"
        }
    },
    "etag":  "0000c552-0000-0000-0000-59b1459c0000"
}
```

## <a name="create-datasets"></a>Criar conjuntos de dados

Defina um conjunto de dados que represente os dados a copiar de uma origem para um sink. Neste exemplo, este conjunto de dados de Blobs refere-se ao serviço ligado do Armazenamento do Azure que criou no passo anterior. O conjunto de dados assume um parâmetro cujo valor é definido numa atividade que consome o conjunto de dados. O parâmetro é utilizado para criar o "folderPath" que aponta para a localização onde os dados residem ou estão armazenados.

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/datasets/BlobDataset?api-version=${apiVersion}"
$body = @"
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": {
                "value": "@{dataset().path}",
                "type": "Expression"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Segue-se o resultado do exemplo:

```json
{
    "id":  "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/datasets/BlobDataset",
    "name":  "BlobDataset",
    "properties":  {
        "type":  "AzureBlob",
        "typeProperties":  {
            "folderPath":  "@{value=@{dataset().path}; type=Expression}"
        },
        "linkedServiceName":  {
            "referenceName":  "AzureStorageLinkedService",
            "type":  "LinkedServiceReference"
        },
        "parameters":  {
            "path":  "@{type=String}"
        }
    },
    "etag":  "0000c752-0000-0000-0000-59b1459d0000"
}
```

## <a name="create-pipeline"></a>Criar pipeline

Neste exemplo, este pipeline contém uma atividade e assume dois parâmetros: o caminho do blob de entrada e o caminho do blob de saída. Os valores destes parâmetros são definidos quando o pipeline é acionado/executado. A atividade de cópia refere-se ao mesmo conjunto de dados de blobs criado no passo anterior como entrada e saída. Quando o conjunto de dados é utilizado como conjunto de dados de entrada, o caminho de entrada é especificado. Da mesma forma, quando o conjunto de dados é utilizado como conjunto de dados de saída, o caminho de saída é especificado.

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelines/Adfv2QuickStartPipeline?api-version=${apiVersion}"
$body = @"
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                    "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Segue-se o resultado do exemplo:

```json
{
    "id":  "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/pipelines/Adfv2QuickStartPipeline",
    "name":  "Adfv2QuickStartPipeline",
    "properties":  {
        "activities":  [
            "@{name=CopyFromBlobToBlob; type=Copy; inputs=System.Object[]; outputs=System.Object[]; typeProperties=}"
        ],
        "parameters":  {
            "inputPath":  "@{type=String}",
            "outputPath":  "@{type=String}"
        }
    },
    "etag":  "0000c852-0000-0000-0000-59b1459e0000"
}
```

## <a name="create-pipeline-run"></a>Criar execução de pipeline

Neste passo, defina os valores dos parâmetros **inputPath** e **outputPath** especificados no pipeline com os valores reais dos caminhos dos blobs de origem e de sink e acione uma execução de pipeline. O ID da execução de pipeline devolvido no corpo da resposta é utilizado em monitorizações da API posteriores.

Substitua o valor de **inputPath** e **outputPath** pelos caminhos dos blobs de origem e de sink para copiar dados entre os mesmos antes de guardar o ficheiro.


```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelines/Adfv2QuickStartPipeline/createRun?api-version=${apiVersion}"
$body = @"
{
    "inputPath": "<the path to existing blob(s) to copy data from, e.g. containername/path>",
    "outputPath": "<the blob path to copy data to, e.g. containername/path>"
}
"@
$response = Invoke-RestMethod -Method POST -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
$runId = $response.runId
```

Segue-se o resultado do exemplo:

```json
{
    "runId":  "2f26be35-c112-43fa-9eaa-8ba93ea57881"
}
```

## <a name="monitor-pipeline"></a>Monitorizar o pipeline

1. Execute o script seguinte para verificar continuamente o estado de execução do pipeline até que este termine de copiar os dados.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```

    Segue-se o resultado do exemplo:

    ```json
    {
        "key":  "000000000-0000-0000-0000-00000000000",
        "timestamp":  "2017-09-07T13:12:39.5561795Z",
        "runId":  "000000000-0000-0000-0000-000000000000",
        "dataFactoryName":  "<dataFactoryName>",
        "pipelineName":  "Adfv2QuickStartPipeline",
        "parameters":  [
            "inputPath: <inputBlobPath>",
            "outputPath: <outputBlobPath>"
        ],
        "parametersCount":  2,
        "parameterNames":  [
            "inputPath",
            "outputPath"
        ],
        "parameterNamesCount":  2,
        "parameterValues":  [
            "<inputBlobPath>",
            "<outputBlobPath>"
        ],
        "parameterValuesCount":  2,
        "runStart":  "2017-09-07T13:12:00.3710792Z",
        "runEnd":  "2017-09-07T13:12:39.5561795Z",
        "durationInMs":  39185,
        "status":  "Succeeded",
        "message":  ""
    }
    ```

2. Execute o script seguinte para obter os detalhes da execução da atividade de cópia, como, por exemplo, o tamanho dos dados lidos/escritos.

    ```PowerShell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

    Segue-se o resultado do exemplo:

    ```json
    {
        "value":  [
            {
                "id":  "000000000-0000-0000-0000-00000000000",
                "timestamp":  "2017-09-07T13:12:38.4780542Z",
                "pipelineRunId":  "000000000-0000-00000-0000-0000000000000",
                "pipelineName":  "Adfv2QuickStartPipeline",
                "status":  "Succeeded",
                "failureType":  "",
                "linkedServiceName":  "",
                "activityName":  "CopyFromBlobToBlob",
                "activityType":  "Copy",
                "activityStart":  "2017-09-07T13:12:02.3299261Z",
                "activityEnd":  "2017-09-07T13:12:38.4780542Z",
                "duration":  36148,
                "input":  "@{source=; sink=}",
                "output":  "@{dataRead=331452208; dataWritten=331452208; copyDuration=22; throughput=14712.9; errors=System.Object[]; effectiveIntegrationRuntime=DefaultIntegrationRuntime (West US); usedDataIntegrationUnits=2; billedDuration=22}",
                "error":  "@{errorCode=; message=; failureType=; target=CopyFromBlobToBlob}"
            }
        ]
    }
    ```

## <a name="verify-the-output"></a>Verificar a saída

Utilize o explorador do Armazenamento do Azure para verificar se o blob ou blobs são copiados de "inputBlobPath"para "outputBlobPath", conforme especificou quando criou uma execução de pipeline.

## <a name="clean-up-resources"></a>Limpar recursos
Pode limpar os recursos que criou no Guia de Introdução de duas formas. Pode eliminar o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md), que inclui todos os recursos no grupo de recursos. Se quiser manter os outros recursos intactos, elimine apenas a fábrica de dados que criou neste tutorial.

Execute o seguinte comando para eliminar todo o grupo de recursos:
```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

Execute o seguinte comando para eliminar apenas a fábrica de dados:

```powershell
Remove-AzDataFactoryV2 -Name "<NameOfYourDataFactory>" -ResourceGroupName "<NameOfResourceGroup>"
```

## <a name="next-steps"></a>Passos Seguintes
O pipeline neste exemplo copia dados de uma localização para outra localização num armazenamento de blobs do Azure. Leia os [tutoriais](tutorial-copy-data-dot-net.md) para saber como utilizar o Data Factory em mais cenários.
