---
title: Criar uma fábrica de dados Azure utilizando a REST API
description: Crie um oleoduto de fábrica de dados Azure para copiar dados de um local no armazenamento de Azure Blob para outro local.
author: linda33wj
ms.service: data-factory
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 01/18/2021
ms.author: jingwang
ms.openlocfilehash: adcefc21ac4fc8a00d97d5ac4352bad1287b60de
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102517638"
---
# <a name="quickstart-create-an-azure-data-factory-and-pipeline-by-using-the-rest-api"></a>Quickstart: Create an Azure data factory and pipeline by using the REST API (Guia de Início Rápido: Criar uma fábrica de dados e um pipeline do Azure com a API REST)

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](quickstart-create-data-factory-rest-api.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

O Azure Data Factory é um serviço de integração de dados baseado na cloud que lhe permite criar fluxos de trabalho condicionados por dados na cloud para orquestrar e automatizar o movimento e a transformação de dados. Utilizando a Azure Data Factory, pode criar e agendar fluxos de trabalho baseados em dados (chamados pipelines) que podem ingerir dados de lojas de dados diferentes, processar/transformar os dados utilizando serviços de computação como a Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning, e publicar dados de produção em lojas de dados como aplicações Azure Synapse Analytics para inteligência empresarial (BI) para consumir.

Este início rápido descreve como utilizar a API REST para criar uma fábrica de dados do Azure. O pipeline nesta fábrica de dados copia dados de uma localização para outra localização num armazenamento de blobs do Azure.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Assinatura Azure**. Se não tiver uma subscrição, pode criar uma conta de [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* **Conta de Armazenamento Azure**. Utilize o armazenamento de blobs como arquivo de dados de **origem** e de **sink**. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../storage/common/storage-account-create.md) para obter os passos para criar uma.
* Crie um **contentor de blobs** no Armazenamento de Blobs, crie uma **pasta** de entrada no contentor e carregue alguns ficheiros para a pasta. Pode utilizar ferramentas como [o Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) para ligar ao armazenamento do Azure Blob, criar um recipiente blob, carregar o ficheiro de entrada e verificar o ficheiro de saída.
* Instalar **a Azure PowerShell**. Siga as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-Az-ps). Este início rápido utiliza o PowerShell para invocar chamadas à API REST.
* **Crie uma aplicação no Azure Active Directory** ao seguir [estas instruções](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Tome nota dos seguintes valores que utiliza em etapas posteriores: **ID de aplicação,** **clienteSecrets** e **ID do inquilino.** Atribua a aplicação à função "**Contribuidor**".

## <a name="set-global-variables"></a>Definir variáveis globais

1. **Launch PowerShell**. Mantenha o Azure PowerShell aberto até ao fim deste início rápido. Se o fechar e reabrir, terá de executar os comandos novamente.

    Execute o comando seguinte e introduza o nome de utilizador e a palavra-passe que utiliza para iniciar sessão no Portal do Azure:

    ```powershell
    Connect-AzAccount
    ```
    Execute o comando seguinte para ver todas as subscrições desta conta:

    ```powershell
    Get-AzSubscription
    ```
    Execute o comando seguinte para selecionar a subscrição com a qual pretende trabalhar. Substitua **a SubscriptionId** pelo ID da sua subscrição Azure:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. Execute os seguintes comandos depois de substituir os marcadores de posição pelos seus próprios valores para definir as variáveis globais a utilizar em passos posteriores.

    ```powershell
    $tenantID = "<your tenant ID>"
    $appId = "<your application ID>"
    $clientSecrets = "<your clientSecrets for the application>"
    $subscriptionId = "<your subscription ID to create the factory>"
    $resourceGroupName = "<your resource group to create the factory>"
    $factoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $apiVersion = "2018-06-01"
    ```

## <a name="authenticate-with-azure-ad"></a>Autenticar com o Azure AD

Execute os seguintes comandos para autenticar com o Azure Active Directory (AAD):

```powershell
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]"https://login.microsoftonline.com/${tenantId}"
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($appId, $clientSecrets)
$result = $AuthContext.AcquireTokenAsync("https://management.core.windows.net/", $cred).GetAwaiter().GetResult()
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Execute os seguintes comandos para criar uma fábrica de dados:

```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}?api-version=${apiVersion}"
$body = @"
{
    "name": "$factoryName",
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
* Para obter uma lista de regiões do Azure em que o Data Factory está atualmente disponível, selecione as regiões que lhe interessam na página seguinte e, em seguida, expanda **Analytics** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras regiões.

Segue-se a resposta de exemplo:

```json
{  
    "name":"<dataFactoryName>",
    "identity":{  
        "type":"SystemAssigned",
        "principalId":"<service principal ID>",
        "tenantId":"<tenant ID>"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{  
        "provisioningState":"Succeeded",
        "createTime":"2019-09-03T02:10:27.056273Z",
        "version":"2018-06-01"
    },
    "eTag":"\"0200c876-0000-0100-0000-5d6dcb930000\"",
    "location":"East US",
    "tags":{  

    }
}
```
## <a name="create-linked-services"></a>Criar serviços ligados

Os serviços ligados são criados numa fábrica de dados para ligar os seus arquivos de dados e serviços de computação a essa fábrica de dados. Neste início rápido, só precisa de criar um serviço ligado do Armazenamento do Azure como origem de cópia e arquivo sink, com o nome "AzureStorageLinkedService" no exemplo.

Execute os seguintes comandos para criar um serviço ligado com o nome **AzureStorageLinkedService**:

Substitua &lt;accountName&gt; e &lt;accountKey&gt; pelo nome e chave da sua conta de armazenamento do Azure antes de executar os comandos.

```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/linkedservices/AzureStorageLinkedService?api-version=${apiVersion}"
$body = @"
{  
    "name":"AzureStorageLinkedService",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
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
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/linkedservices/AzureStorageLinkedService",
    "name":"AzureStorageLinkedService",
    "type":"Microsoft.DataFactory/factories/linkedservices",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"DefaultEndpointsProtocol=https;AccountName=<accountName>;"
        }
    },
    "etag":"07011a57-0000-0100-0000-5d6e14a20000"
}
```
## <a name="create-datasets"></a>Criar conjuntos de dados

Defina um conjunto de dados que represente os dados a copiar de uma origem para um sink. Neste exemplo, cria-se dois conjuntos de dados: InputDataset e OutputDataset. Dizem respeito ao serviço ligado do Armazenamento do Azure que criou na secção anterior. O conjunto de dados de entrada representa a origem de dados na pasta de entrada. Na definição do conjunto de dados de entrada, vai especificar o contentor de blobs (adftutorial), a pasta (input) e o ficheiro (emp.txt) que contêm os dados de origem. O conjunto de dados de saída representa os dados que são copiados para o destino. Na definição do conjunto de dados de saída, vai especificar o contentor de blobs (adftutorial), a pasta (output) e o ficheiro para o qual os dados vão ser copiados.

**Criar Conjunto de Dados de Entrada**

```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/InputDataset?api-version=${apiVersion}"
$body = @"
{  
    "name":"InputDataset",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "fileName":"emp.txt",
                "folderPath":"input",
                "container":"adftutorial"
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
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/datasets/InputDataset",
    "name":"InputDataset",
    "type":"Microsoft.DataFactory/factories/datasets",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":"@{type=AzureBlobStorageLocation; fileName=emp.txt; folderPath=input; container=adftutorial}"
        }
    },
    "etag":"07011c57-0000-0100-0000-5d6e14b40000"
}
```
**Criar Conjunto de Dados de Saída**

```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/OutputDataset?api-version=${apiVersion}"
$body = @"
{  
    "name":"OutputDataset",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "folderPath":"output",
                "container":"adftutorial"
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
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/datasets/OutputDataset",
    "name":"OutputDataset",
    "type":"Microsoft.DataFactory/factories/datasets",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":"@{type=AzureBlobStorageLocation; folderPath=output; container=adftutorial}"
        }
    },
    "etag":"07013257-0000-0100-0000-5d6e18920000"
}
```
## <a name="create-pipeline"></a>Criar pipeline

Neste exemplo, este gasoduto contém uma atividade copy. A atividade Copy refere-se ao "InputDataset" e ao "OutputDataset" criado no passo anterior como entrada e saída.

```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelines/Adfv2QuickStartPipeline?api-version=${apiVersion}"
$body = @"
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "BinarySource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        }
                    },
                    "sink": {
                        "type": "BinarySink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        }
                    },
                    "enableStaging": false
                },
                "inputs": [
                    {
                        "referenceName": "InputDataset",
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "OutputDataset",
                        "type": "DatasetReference"
                    }
                ]
            }
        ],
        "annotations": []
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Segue-se o resultado do exemplo:

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/pipelines/Adfv2QuickStartPipeline",
    "name":"Adfv2QuickStartPipeline",
    "type":"Microsoft.DataFactory/factories/pipelines",
    "properties":{  
        "activities":[  
            "@{name=CopyFromBlobToBlob; type=Copy; dependsOn=System.Object[]; policy=; userProperties=System.Object[]; typeProperties=; inputs=System.Object[]; outputs=System.Object[]}"
        ],
        "annotations":[  

        ]
    },
    "etag":"07012057-0000-0100-0000-5d6e14c00000"
}
```

## <a name="create-pipeline-run"></a>Criar execução de pipeline

Neste passo, desencadeia-se uma corrida de oleoduto. O ID da execução de pipeline devolvido no corpo da resposta é utilizado em monitorizações da API posteriores.

```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelines/Adfv2QuickStartPipeline/createRun?api-version=${apiVersion}"
$response = Invoke-RestMethod -Method POST -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
$runId = $response.runId
```

Segue-se o resultado do exemplo:

```json
{  
    "runId":"04a2bb9a-71ea-4c31-b46e-75276b61bafc"
}
```

## <a name="monitor-pipeline"></a>Monitorizar o pipeline

1. Execute o script seguinte para verificar continuamente o estado de execução do pipeline até que este termine de copiar os dados.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ( ($response.Status -eq "InProgress") -or ($response.Status -eq "Queued") ) {
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
        "runId":"04a2bb9a-71ea-4c31-b46e-75276b61bafc",
        "debugRunId":null,
        "runGroupId":"04a2bb9a-71ea-4c31-b46e-75276b61bafc",
        "pipelineName":"Adfv2QuickStartPipeline",
        "parameters":{  

        },
        "invokedBy":{  
            "id":"2bb3938176ee43439752475aa12b2251",
            "name":"Manual",
            "invokedByType":"Manual"
        },
        "runStart":"2019-09-03T07:22:47.0075159Z",
        "runEnd":"2019-09-03T07:22:57.8862692Z",
        "durationInMs":10878,
        "status":"Succeeded",
        "message":"",
        "lastUpdated":"2019-09-03T07:22:57.8862692Z",
        "annotations":[  

        ],
        "runDimension":{  

        },
        "isLatest":true
    }
    ```

2. Execute o script seguinte para obter os detalhes da execução da atividade de cópia, como, por exemplo, o tamanho dos dados lidos/escritos.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelineruns/${runId}/queryActivityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method POST -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```
    Segue-se o resultado do exemplo:

    ```json
    {  
        "value":[  
            {  
                "activityRunEnd":"2019-09-03T07:22:56.6498704Z",
                "activityName":"CopyFromBlobToBlob",
                "activityRunStart":"2019-09-03T07:22:49.0719311Z",
                "activityType":"Copy",
                "durationInMs":7577,
                "retryAttempt":null,
                "error":"@{errorCode=; message=; failureType=; target=CopyFromBlobToBlob}",
                "activityRunId":"32951886-814a-4d6b-b82b-505936e227cc",
                "iterationHash":"",
                "input":"@{source=; sink=; enableStaging=False}",
                "linkedServiceName":"",
                "output":"@{dataRead=20; dataWritten=20; filesRead=1; filesWritten=1; sourcePeakConnections=1; sinkPeakConnections=1; copyDuration=4; throughput=0.01; errors=System.Object[]; effectiveIntegrationRuntime=DefaultIntegrationRuntime (Central US); usedDataIntegrationUnits=4; usedParallelCopies=1; executionDetails=System.Object[]}",
                "userProperties":"",
                "pipelineName":"Adfv2QuickStartPipeline",
                "pipelineRunId":"04a2bb9a-71ea-4c31-b46e-75276b61bafc",
                "status":"Succeeded",
                "recoveryStatus":"None",
                "integrationRuntimeNames":"defaultintegrationruntime",
                "executionDetails":"@{integrationRuntime=System.Object[]}"
            }
        ]
    }
    ```
## <a name="verify-the-output"></a>Verificar a saída

Utilize o explorador de armazenamento Azure para verificar se o ficheiro é copiado para "outputPath" a partir de "inputPath", conforme especificado ao criar uma execução de pipeline.

## <a name="clean-up-resources"></a>Limpar os recursos
Pode limpar os recursos que criou no Guia de Introdução de duas formas. Pode eliminar o [grupo de recursos do Azure](../azure-resource-manager/management/overview.md), que inclui todos os recursos no grupo de recursos. Se quiser manter os outros recursos intactos, elimine apenas a fábrica de dados que criou neste tutorial.

Execute o seguinte comando para eliminar todo o grupo de recursos:
```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

Execute o seguinte comando para eliminar apenas a fábrica de dados:

```powershell
Remove-AzDataFactoryV2 -Name "<NameOfYourDataFactory>" -ResourceGroupName "<NameOfResourceGroup>"
```

## <a name="next-steps"></a>Passos seguintes
O pipeline neste exemplo copia dados de uma localização para outra localização num armazenamento de blobs do Azure. Leia os [tutoriais](tutorial-copy-data-dot-net.md) para saber como utilizar o Data Factory em mais cenários.
