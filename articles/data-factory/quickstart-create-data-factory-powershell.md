---
title: Copiar os dados no Armazenamento de Blobs com o Azure Data Factory | Microsoft Docs
description: Crie uma fábrica de dados do Azure para copiar dados de uma localização no armazenamento de Blobs do Azure para outra localização.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: quickstart
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: b675ab9663be674ec2439bfe9139b7c79c144cbd
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453393"
---
# <a name="quickstart-create-an-azure-data-factory-using-powershell"></a>Início rápido: Criar uma fábrica de dados do Azure com o PowerShell

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](quickstart-create-data-factory-powershell.md)

Este início rápido descreve como utilizar o PowerShell para criar uma fábrica de dados do Azure. O pipeline que criar nesta fábrica de dados **copia** dados de uma pasta para outra pasta num armazenamento de blobs do Azure. Para obter um tutorial sobre como **transformar** dados com o Azure Data Factory, veja [Tutorial: Transformar dados com o Spark](transform-data-using-spark.md).

> [!NOTE]
> Este artigo não disponibiliza uma introdução detalhada do serviço Data Factory. Para obter uma introdução ao serviço Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)]

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Instale os módulos do Azure PowerShell mais recentes ao seguir as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-Az-ps).

#### <a name="log-in-to-powershell"></a>Iniciar sessão no PowerShell

1. Inicie o **PowerShell** no seu computador. Mantenha o PowerShell aberto até ao fim deste início rápido. Se o fechar e reabrir, terá de executar os comandos novamente.

2. Execute o comando seguinte e introduza o mesmo nome de utilizador e a mesma palavra-passe do Azure que utiliza para iniciar sessão no portal do Azure:

    ```powershell
    Connect-AzAccount
    ```

3. Execute o comando seguinte para ver todas as subscrições desta conta:

    ```powershell
    Get-AzSubscription
    ```

4. Se vir várias subscrições associadas à sua conta, execute o comando seguinte selecionar aquela com que pretende trabalhar. Substitua **SubscriptionId** pelo ID da sua subscrição do Azure:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Defina uma variável para o nome do grupo de recursos que vai utilizar nos comandos do PowerShell mais tarde. Copie o texto do comando seguinte para o PowerShell, especifique um nome para o [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) com aspas duplas e execute o comando. Por exemplo: `"ADFQuickStartRG"`.

     ```powershell
    $resourceGroupName = "ADFQuickStartRG";
    ```

    Se o grupo de recursos já existir, pode não substituí-lo. Atribua outro valor à variável `$ResourceGroupName` e execute novamente o comando

2. Para criar o grupo de recursos do Azure, execute o comando abaixo:

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'East US'
    ```

    Se o grupo de recursos já existir, pode não substituí-lo. Atribua outro valor à variável `$ResourceGroupName` e execute novamente o comando.

3. Defina uma variável para o nome da fábrica de dados. 

    > [!IMPORTANT]
    >  Atualize o nome da fábrica de dados para que seja globalmente exclusivo. Por exemplo, ADFTutorialFactorySP1127.

    ```powershell
    $dataFactoryName = "ADFQuickStartFactory";
    ```

4. Para criar a fábrica de dados, execute o seguinte procedimento **Set-AzDataFactoryV2** cmdlet, usando a propriedade de Location e ResourceGroupName da variável de $ResGrp:

    ```powershell
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
        -Location $ResGrp.Location -Name $dataFactoryName
    ```

Tenha em atenção os seguintes pontos:

* O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro seguinte, altere o nome e tente novamente.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Para criar instâncias do Data Factory, a conta de utilizador que utiliza para iniciar sessão no Azure tem de ser um membro das funções **contribuidor** ou **proprietário**, ou um **administrador** da subscrição do Azure.

* Para obter uma lista de regiões do Azure em que a fábrica de dados está atualmente disponível, selecione as regiões que lhe interessam, na página seguinte e, em seguida, expanda **Analytics** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras regiões.

## <a name="create-a-linked-service"></a>Criar um serviço ligado

Crie os serviços ligados numa fábrica de dados para ligar os seus arquivos de dados e serviços de computação a essa fábrica de dados. Neste início rápido, vai criar um serviço ligado do Armazenamento do Azure que será utilizado não só como arquivo de origem como arquivo sink. O serviço ligado tem as informações de ligação utilizadas pelo serviço Data Factory em runtime para se ligar ao mesmo.

1. Crie um ficheiro JSON com o nome **azurestoragelinkedservice. JSON** na **C:\ADFv2QuickStartPSH** pasta com o seguinte conteúdo: (Crie a pasta ADFv2QuickStartPSH caso ainda não exista.).

    > [!IMPORTANT]
    > Substitua &lt;accountName&gt; e &lt;accountKey&gt; pelo nome e chave da sua conta de armazenamento do Azure antes de guardar o ficheiro.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net",
                    "type": "SecureString"
                }
            }
        }
    }
    ```

    Se estiver a utilizar o Bloco de Notas, selecione **Todos os Ficheiros** no campo **Guardar com o tipo**, na caixa de diálogo **Guardar como**. Caso contrário, pode adicionar a extensão `.txt` ao ficheiro. Por exemplo, `AzureStorageLinkedService.json.txt`. Se criar o ficheiro no Explorador de Ficheiros antes de o abrir no Bloco de Notas, poderá não ver a extensão `.txt`, uma vez que a opção **Ocultar extensões para tipos de ficheiros conhecidos** está definida por predefinição. Antes de avançar para o próximo passo, remova a extensão `.txt`.

2. No **PowerShell**, mude para a pasta **ADFv2QuickStartPSH**.

    ```powershell
    Set-Location 'C:\ADFv2QuickStartPSH'
    ```

3. Executar o **Set-AzDataFactoryV2LinkedService** cmdlet para criar o serviço ligado: **AzureStorageLinkedService**.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureStorageLinkedService" `
        -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    Segue-se o resultado do exemplo:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-a-dataset"></a>Criar um conjunto de dados

Neste passo, vai definir um conjunto de dados que representa os dados a copiar de uma origem para um sink. O conjunto de dados é do tipo **AzureBlob**. Reporta ao **serviço ligado do Armazenamento do Azure** que criou no passo anterior. Assume um parâmetro para construir a propriedade **folderPath**. Para um conjunto de dados de entrada, a atividade de cópia no pipeline transmite o caminho de entrada como um valor para este parâmetro. Da mesma forma, para um conjunto de dados de saída, a atividade de cópia transmite o caminho de saída como um valor para este parâmetro. 

1. Crie um ficheiro JSON com o nome **BlobDataset.json** na pasta **C:\ADFv2QuickStartPSH**, com o seguinte conteúdo:

    ```json
    {
        "name": "BlobDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "@{dataset().path}"
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
    ```

2. Para criar o conjunto de dados: **BlobDataset**, execute o **conjunto AzDataFactoryV2Dataset** cmdlet.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "BlobDataset" `
        -DefinitionFile ".\BlobDataset.json"
    ```

    Segue-se o resultado do exemplo:

    ```console
    DatasetName       : BlobDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>Criar um pipeline

Neste início rápido, vai criar um pipeline com uma atividade que assume dois parâmetros: o caminho do blob de entrada e o caminho do blob de saída. Os valores destes parâmetros são definidos quando o pipeline é acionado/executado. A atividade de cópia utiliza o mesmo conjunto de dados de blobs criado no passo anterior como entrada e saída. Quando o conjunto de dados é utilizado como conjunto de dados de entrada, o caminho de entrada é especificado. Da mesma forma, quando o conjunto de dados é utilizado como conjunto de dados de saída, o caminho de saída é especificado.

1. Crie um ficheiro JSON com o nome **Adfv2QuickStartPipeline.json** na pasta **C:\ADFv2QuickStartPSH** com o seguinte conteúdo:

    ```json
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
    ```

2. Para criar o pipeline: **Adfv2QuickStartPipeline**, execute o **conjunto AzDataFactoryV2Pipeline** cmdlet.

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline `
        -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName `
        -Name "Adfv2QuickStartPipeline" `
        -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

## <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline

Neste passo, vai definir os valores para os parâmetros do pipeline:  **inputPath** e **outputPath** com valores reais dos caminhos dos blobs de origem e de sink. Em seguida, vai criar uma execução de pipeline através destes argumentos.

1. Crie um ficheiro JSON com o nome **PipelineParameters.json** na pasta **C:\ADFv2QuickStartPSH** com o seguinte conteúdo:

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
    }
    ```
2. Executar o **Invoke-AzDataFactoryV2Pipeline** cmdlet para criar um pipeline executar e transmitir os valores de parâmetro. O cmdlet devolve o ID de execução do pipeline para monitorização futura.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline `
        -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName `
        -PipelineName $DFPipeLine.Name `
        -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-the-pipeline-run"></a>Monitorizar a execução do pipeline.

1. Execute o seguinte script do PowerShell para verificar continuamente o estado de execução do pipeline até que este termine de copiar os dados. Copie/cole o seguinte script na janela do PowerShell e prima ENTER.

    ```powershell
    while ($True) {
        $Run = Get-AzDataFactoryV2PipelineRun `
            -ResourceGroupName $ResGrp.ResourceGroupName `
            -DataFactoryName $DataFactory.DataFactoryName `
            -PipelineRunId $RunId

        if ($Run) {
            if ($run.Status -ne 'InProgress') {
                Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
                $Run
                break
            }
            Write-Output "Pipeline is running...status: InProgress"
        }

        Start-Sleep -Seconds 10
    }
    ```

    Eis a saída de exemplo da execução de pipeline:

    ```console
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    RunId             : 0000000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 9/28/2017 8:28:38 PM
    Parameters        : {[inputPath, adftutorial/input], [outputPath, adftutorial/output]}
    RunStart          : 9/28/2017 8:28:14 PM
    RunEnd            : 9/28/2017 8:28:38 PM
    DurationInMs      : 24151
    Status            : Succeeded
    Message           :
    ```

    Poderá ver o erro seguinte:

    ```console
    Activity CopyFromBlobToBlob failed: Failed to detect region of linked service 'AzureStorage' : 'AzureStorageLinkedService' with error '[Region Resolver] Azure Storage failed to get address for DNS. Warning: System.Net.Sockets.SocketException (0x80004005): No such host is known
    ```

    Se vir o erro, execute os seguintes passos:

    1. No AzureStorageLinkedService.json, confirme se o nome e a chave da sua Conta de Armazenamento do Azure estão corretos.
    2. Certifique-se de que o formato da cadeia de ligação está correto. As propriedades, por exemplo, AccountName e AccountKey estão separadas pelo caráter de ponto e vírgula (`;`).
    3. Se o nome da conta e a chave da conta estiverem entre sinais de maior e de menor, remova-os.
    4. Segue-se uma cadeia de ligação de exemplo:

        ```json
        "connectionString": {
            "value": "DefaultEndpointsProtocol=https;AccountName=mystorageaccountname;AccountKey=mystorageaccountkey;EndpointSuffix=core.windows.net",
            "type": "SecureString"
        }
        ```

    5. Recrie o serviço ligado seguindo os passos apresentados na secção [Criar um serviço ligado](#create-a-linked-service).
    6. Volte a executar o pipeline seguindo os passos apresentados na secção [Criar uma execução de pipeline](#create-a-pipeline-run).
    7. Execute novamente o comando de monitorização atual para monitorizar a nova execução de pipeline.

2. Execute o script seguinte para obter os detalhes da execução da atividade de cópia, como, por exemplo, o tamanho dos dados lidos/escritos.

    ```powershell
    Write-Output "Activity run details:"
    $Result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineRunId $RunId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $Result

    Write-Output "Activity 'Output' section:"
    $Result.Output -join "`r`n"

    Write-Output "Activity 'Error' section:"
    $Result.Error -join "`r`n"
    ```
3. Confirme se vê uma saída semelhante à seguinte saída de exemplo de resultado da execução de atividade:

    ```console
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, copyDuration, throughput...}
    LinkedServiceName :
    ActivityRunStart  : 9/28/2017 8:28:18 PM
    ActivityRunEnd    : 9/28/2017 8:28:36 PM
    DurationInMs      : 18095
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 38
    "dataWritten": 38
    "copyDuration": 7
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)"
    "usedDataIntegrationUnits": 2
    "billedDuration": 14
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

O pipeline neste exemplo copia dados de uma localização para outra localização num armazenamento de blobs do Azure. Leia os [tutoriais](tutorial-copy-data-dot-net.md) para saber como utilizar o Data Factory em mais cenários.
