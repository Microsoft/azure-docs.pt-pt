---
title: Criar uma fábrica de dados do Azure com o modelo do Resource Manager | Microsoft Docs
description: Neste tutorial, vai criar um exemplo de pipeline do Azure Data Factory com um modelo do Azure Resource Manager.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 02/20/2019
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 1d4eb3d2978be98d81b42dd66a75b21563c23a1a
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576655"
---
# <a name="tutorial-create-an-azure-data-factory-using-azure-resource-manager-template"></a>Tutorial: Criar uma fábrica de dados do Azure com o modelo Azure Resource Manager

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Versão atual](quickstart-create-data-factory-resource-manager-template.md)

Este início rápido descreve como utilizar um modelo do Azure Resource Manager para criar uma fábrica de dados do Azure. O pipeline que criar nesta fábrica de dados **copia** dados de uma pasta para outra pasta num armazenamento de blobs do Azure. Para obter um tutorial sobre como **transformar** dados com o Azure Data Factory, veja [Tutorial: Transformar dados com o Spark](transform-data-using-spark.md).

> [!NOTE]
> Este artigo não disponibiliza uma introdução detalhada do serviço Data Factory. Para obter uma introdução ao serviço Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)]

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Instale os módulos do Azure PowerShell mais recentes ao seguir as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-Az-ps).

## <a name="resource-manager-templates"></a>Modelos do Gestor de Recursos

Para saber mais sobre os modelos do Azure Resource Manager, veja [Authoring Azure Resource Manager Template](../azure-resource-manager/resource-group-authoring-templates.md) (Criar Modelos do Azure Resource Manager).

A secção seguinte disponibiliza o modelo do Resource Manager completo para a definição de entidades do Data Factory, para que possa rapidamente dar uma vista de olhos pelo tutorial e testar o modelo. Para compreender como cada entidade do Data Factory está definida, consulte a secção [Data Factory entities in the template (Entidades do Data Factory no modelo)](#data-factory-entities-in-the-template).

Para saber mais sobre a sintaxe JSON e propriedades de recursos do Data Factory num modelo, veja [tipos de recursos de DataFactory](/azure/templates/microsoft.datafactory/allversions).

## <a name="data-factory-json"></a>JSON do Data Factory

Crie um ficheiro JSON com o nome **ADFTutorialARM.json** na pasta **C:\ADFTutorial** com o conteúdo abaixo:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "dataFactoryName": {
            "type": "string",
            "metadata": {
                "description": "Name of the data factory. Must be globally unique."
            }
        },
        "dataFactoryLocation": {
            "type": "string",
            "allowedValues": [
                "East US",
                "East US 2",
                "West Europe"
            ],
            "defaultValue": "East US",
            "metadata": {
                "description": "Location of the data factory. Currently, only East US, East US 2, and West Europe are supported. "
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Azure storage account that contains the input/output data."
            }
        },
        "storageAccountKey": {
            "type": "securestring",
            "metadata": {
                "description": "Key for the Azure storage account."
            }
        },
        "blobContainer": {
            "type": "string",
            "metadata": {
                "description": "Name of the blob container in the Azure Storage account."
            }
        },
        "inputBlobFolder": {
            "type": "string",
            "metadata": {
                "description": "The folder in the blob container that has the input file."
            }
        },
        "inputBlobName": {
            "type": "string",
            "metadata": {
                "description": "Name of the input file/blob."
            }
        },
        "outputBlobFolder": {
            "type": "string",
            "metadata": {
                "description": "The folder in the blob container that will hold the transformed data."
            }
        },
        "outputBlobName": {
            "type": "string",
            "metadata": {
                "description": "Name of the output file/blob."
            }
        },
        "triggerStartTime": {
            "type": "string",
            "metadata": {
                "description": "Start time for the trigger."
            }
        },
        "triggerEndTime": {
            "type": "string",
            "metadata": {
                "description": "End time for the trigger."
            }
        }
    },
    "variables": {
        "azureStorageLinkedServiceName": "ArmtemplateStorageLinkedService",
        "inputDatasetName": "ArmtemplateTestDatasetIn",
        "outputDatasetName": "ArmtemplateTestDatasetOut",
        "pipelineName": "ArmtemplateSampleCopyPipeline",
        "triggerName": "ArmTemplateTestTrigger"
    },
    "resources": [{
        "name": "[parameters('dataFactoryName')]",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "[parameters('dataFactoryLocation')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "resources": [{
                "type": "linkedservices",
                "name": "[variables('azureStorageLinkedServiceName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]"
                ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "AzureStorage",
                    "description": "Azure Storage linked service",
                    "typeProperties": {
                        "connectionString": {
                            "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]",
                            "type": "SecureString"
                        }
                    }
                }
            },
            {
                "type": "datasets",
                "name": "[variables('inputDatasetName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "AzureBlob",
                    "typeProperties": {
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'), '/')]",
                        "fileName": "[parameters('inputBlobName')]"
                    },
                    "linkedServiceName": {
                        "referenceName": "[variables('azureStorageLinkedServiceName')]",
                        "type": "LinkedServiceReference"
                    }
                }
            },
            {
                "type": "datasets",
                "name": "[variables('outputDatasetName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "AzureBlob",
                    "typeProperties": {
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'), '/')]",
                        "fileName": "[parameters('outputBlobName')]"
                    },
                    "linkedServiceName": {
                        "referenceName": "[variables('azureStorageLinkedServiceName')]",
                        "type": "LinkedServiceReference"
                    }
                }
            },
            {
                "type": "pipelines",
                "name": "[variables('pipelineName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]",
                    "[variables('inputDatasetName')]",
                    "[variables('outputDatasetName')]"
                ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "activities": [{
                        "type": "Copy",
                        "typeProperties": {
                            "source": {
                                "type": "BlobSource"
                            },
                            "sink": {
                                "type": "BlobSink"
                            }
                        },
                        "name": "MyCopyActivity",
                        "inputs": [{
                            "referenceName": "[variables('inputDatasetName')]",
                            "type": "DatasetReference"
                        }],
                        "outputs": [{
                            "referenceName": "[variables('outputDatasetName')]",
                            "type": "DatasetReference"
                        }]
                    }]
                }
            },
            {
                "type": "triggers",
                "name": "[variables('triggerName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]",
                    "[variables('inputDatasetName')]",
                    "[variables('outputDatasetName')]",
                    "[variables('pipelineName')]"
                ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "ScheduleTrigger",
                    "typeProperties": {
                        "recurrence": {
                            "frequency": "Hour",
                            "interval": 1,
                            "startTime": "[parameters('triggerStartTime')]",
                            "endTime": "[parameters('triggerEndTime')]",
                            "timeZone": "UTC"
                        }
                    },
                    "pipelines": [{
                        "pipelineReference": {
                            "type": "PipelineReference",
                            "referenceName": "ArmtemplateSampleCopyPipeline"
                        },
                        "parameters": {}
                    }]
                }
            }
        ]
    }]
}
```

## <a name="parameters-json"></a>Parâmetros JSON

Crie um ficheiro JSON com o nome **ADFTutorialARM Parameters.json** que contém os parâmetros para o modelo do Azure Resource Manager.

> [!IMPORTANT]
> - Especifique o nome e a chave da sua conta de armazenamento do Azure para os parâmetros **storageAccountName** e **storageAccountKey** neste ficheiro de parâmetros. Criou o contentor adftutorial e carregou o ficheiro de exemplo (emp.txt) para a pasta de entrada neste armazenamento de blobs do Azure.
> - Especifique um nome globalmente exclusivo para a fábrica de dados no parâmetro **dataFactoryName**. Por exemplo: ARMTutorialFactoryJohnDoe11282017.
> - Em **triggerStartTime**, especifique o dia atual no formato: `2017-11-28T00:00:00`.
> - Em **triggerEndTime**, especifique o dia seguinte no formato: `2017-11-29T00:00:00`. Também pode verificar a hora UTC atual e especificar como hora de fim a primeira ou as duas primeiras horas seguintes. Por exemplo, se a hora UTC agora for 1:32, especifique `2017-11-29:03:00:00` como a hora de fim. Neste caso, o acionador executa o pipeline duas vezes (às 2:00 e às 03:00).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataFactoryName": {
      "value": "<datafactoryname>"
    },
    "dataFactoryLocation": {
      "value": "East US"
    },
    "storageAccountName": {
      "value": "<yourstorageaccountname>"
    },
    "storageAccountKey": {
      "value": "<yourstorageaccountkey>"
    },
    "blobContainer": {
      "value": "adftutorial"
    },
    "inputBlobFolder": {
      "value": "input"
    },
    "inputBlobName": {
      "value": "emp.txt"
    },
    "outputBlobFolder": {
      "value": "output"
    },
    "outputBlobName": {
      "value": "emp.txt"
    },
    "triggerStartTime": {
        "value": "2017-11-28T00:00:00. Set to today"
    },
    "triggerEndTime": {
        "value": "2017-11-29T00:00:00. Set to tomorrow"
    }
  }
}
```

> [!IMPORTANT]
> Pode ter ficheiros JSON de parâmetro separado para ambientes de desenvolvimento, teste e produção, que pode utilizar com o mesmo modelo JSON do Data Factory. Ao utilizar um script do Power Shell, pode automatizar a implementação de entidades do Data Factory nestes ambientes.

## <a name="deploy-data-factory-entities"></a>Implementar entidades do Data Factory

No PowerShell, execute o comando seguinte para implementar as entidades do Data Factory com o modelo do Resource Manager que criou anteriormente neste início rápido.

```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFTutorial\ADFTutorialARM.json -TemplateParameterFile C:\ADFTutorial\ADFTutorialARM-Parameters.json
```

Verá ver um resultado semelhante ao exemplo seguinte:

```console
DeploymentName          : MyARMDeployment
ResourceGroupName       : ADFTutorialResourceGroup
ProvisioningState       : Succeeded
Timestamp               : 11/29/2017 3:11:13 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                 Type            Value
                          ===============      ============    ==========
                          dataFactoryName      String          <data factory name>
                          dataFactoryLocation  String          East US
                          storageAccountName   String          <storage account name>
                          storageAccountKey    SecureString
                          blobContainer        String          adftutorial
                          inputBlobFolder      String          input
                          inputBlobName        String          emp.txt
                          outputBlobFolder     String          output
                          outputBlobName       String          emp.txt
                          triggerStartTime     String          11/29/2017 12:00:00 AM
                          triggerEndTime       String          11/29/2017 4:00:00 AM

Outputs                 :
DeploymentDebugLogLevel :
```

## <a name="start-the-trigger"></a>Iniciar o acionador

O modelo implementa as seguintes entidades do Data Factory:

- Serviço ligado do Storage do Azure
- Conjuntos de dados dos Blobs do Azure
- Pipeline com uma atividade de cópia
- Acionador para acionar o pipeline

O acionador implementado está no estado parado. Uma das formas de iniciar o acionador é utilizar o **Start-AzDataFactoryV2Trigger** cmdlet do PowerShell. O procedimento seguinte mostra os passos detalhados:

1. Na janela do PowerShell, crie uma variável para conter o nome do grupo de recursos. Copie o comando seguinte na janela do PowerShell e prima ENTER. Se tiver especificado um nome de grupo de recursos diferente para o comando New-AzResourceGroupDeployment, atualize o valor aqui.

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```
2. Crie uma variável que contenha o nome da fábrica de dados. Especifique o mesmo nome que especificou no ficheiro ADFTutorialARM-Parameters.json.

    ```powershell
    $dataFactoryName = "<yourdatafactoryname>"
    ```
3. Defina uma variável para o nome do acionador. O nome do acionador é codificado no ficheiro de modelo do Resource Manager (adftutorialarm. JSON).

    ```powershell
    $triggerName = "ArmTemplateTestTrigger"
    ```
4. Obtenha o **estado do acionador** mediante a execução do comando do PowerShell seguinte após especificar o nome da fábrica de dados e do acionador:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

    Segue-se o resultado do exemplo:

    ```json
    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ARMFactory1128
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Stopped
    ```
    
    Repare que o estado do runtime do acionador é **Parado**.
5. **Inicie o acionador**. O acionador executa o pipeline definido no modelo à hora certa. Ou seja, se tiver executado o comando às 14:25, o acionador executa o pipeline pela primeira vez às 15:00. Em seguida, executa o pipeline uma vez por hora até a hora de fim que especificou para o acionador.

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Segue-se o resultado do exemplo:
    
    ```console
    Confirm
    Are you sure you want to start trigger 'ArmTemplateTestTrigger' in data factory 'ARMFactory1128'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
    True
    ```
6. Confirme que o acionador foi iniciado executando o comando Get-AzDataFactoryV2Trigger novamente.

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Segue-se o resultado do exemplo:
    
    ```console
    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ARMFactory1128
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Started
    ```

## <a name="monitor-the-pipeline"></a>Monitorizar o pipeline

1. Depois de iniciar sessão no [portal do Azure](https://portal.azure.com/), clique em **Todos os serviços**, pesquise com uma palavra-chave, como **dados fa**, e selecione **Fábricas de dados**.

    ![Menu Procurar fábricas de dados](media/quickstart-create-data-factory-resource-manager-template/browse-data-factories-menu.png)

2. Na página **Fábricas de Dados**, clique na fábrica de dados que criou. Se for necessário, filtre a lista com o nome da fábrica de dados.

    ![Selecionar fábrica de dados](media/quickstart-create-data-factory-resource-manager-template/select-data-factory.png)

3. Na página Fábrica de dados, clique no mosaico **Monitorizar e Gerir**.

    ![Mosaico Monitorizar e gerir](media/quickstart-create-data-factory-resource-manager-template/monitor-manage-tile.png)

4. O **Data Integration Application** deve ser aberto noutro separador do browser. Se não estiver ativo, mude para o **separador do monitor**. Repare que a execução do pipeline foi acionada por um **acionador de agendador**.

    ![Monitorizar execução do pipeline](media/quickstart-create-data-factory-resource-manager-template/monitor-pipeline-run.png)

    > [!IMPORTANT]
    > Ver execuções de pipeline apenas à hora certa (por exemplo: 4 AM, 5 AM, 6 da Manhã, etc.). Clique em **Atualizar**, na barra de ferramentas, para atualizar a lista quando a for atingida a hora seguinte.

5. Clique na ligação na coluna **Ações**.

    ![Ligação de ações do pipeline](media/quickstart-create-data-factory-resource-manager-template/pipeline-actions-link.png)

6. Verá as execuções de atividade associadas à execução do pipeline. Neste início rápido, o pipeline tem apenas uma atividade do tipo: Copie. Por conseguinte, verá uma execução dessa atividade.

    ![Execuções de atividade](media/quickstart-create-data-factory-resource-manager-template/activity-runs.png)
7. Clique na ligação na coluna **Saída**. Verá a saída da operação de cópia numa janela de **Saída**. Clique no botão de maximizar para ver a saída completa. Pode fechar a janela de saída maximizada ou fechar a saída.

    ![Janela de saída](media/quickstart-create-data-factory-resource-manager-template/output-window.png)
8. Quando vir uma execução com êxito/falha, pare o acionador. O acionador executa o pipeline uma vez por hora. Em cada execução, o pipeline copia o mesmo ficheiro da pasta de entrada para a pasta de saída. Para parar o acionador, execute o comando seguinte na janela do PowerShell.
    
    ```powershell
    Stop-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)]

## <a name="data-factory-entities-in-the-template"></a> Definições de JSON para as entidades

As seguintes entidades do Data Factory são definidas no modelo JSON:

- [Serviço ligado do Armazenamento do Azure](#azure-storage-linked-service)
- [Conjunto de dados de entrada do blobs do Azure](#azure-blob-input-dataset)
- [Conjunto de dados dos Blobs do Azure](#azure-blob-output-dataset)
- [Pipeline de dados com uma atividade de cópia](#data-pipeline)
- [Acionador](#trigger)

#### <a name="azure-storage-linked-service"></a>Serviço ligado do Storage do Azure

O AzureStorageLinkedService liga a sua conta do Armazenamento do Azure à fábrica de dados. Como parte dos pré-requisitos, criou um contentor e carregou dados para esta conta de armazenamento. Especifique o nome e a chave da sua conta de armazenamento do Azure nesta secção. Veja [Azure Storage linked service (Serviço ligado de Armazenamento do Azure)](connector-azure-blob-storage.md#linked-service-properties) para obter detalhes sobre as propriedades JSON utilizadas para definir um serviço ligado de Armazenamento do Azure.

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[parameters('dataFactoryName')]"
    ],
    "apiVersion": "2018-06-01",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": {
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]",
                "type": "SecureString"
            }
        }
    }
}
```

A connectionString utiliza os parâmetros storageAccountName e storageAccountKey. Os valores para estes parâmetros foram transmitidos através da utilização de um ficheiro de configuração. A definição também utiliza variáveis: azureStorageLinkedService e dataFactoryName definidas no modelo.

#### <a name="azure-blob-input-dataset"></a>Conjunto de dados de entrada de blobs do Azure

O serviço ligado do armazenamento do Azure especifica a cadeia de ligação que o serviço Data Factory utiliza no tempo de execução para ligar à sua conta de armazenamento do Azure. Na definição do conjunto de dados dos blobs do Azure, especifique os nomes do contentor de blob, da pasta e do ficheiro que contém os dados de entrada. Veja [Azure Blob dataset properties (Propriedades do conjunto de dados de Blobs do Azure)](connector-azure-blob-storage.md#dataset-properties) para obter detalhes sobre as propriedades JSON utilizadas para definir um conjunto de dados de Blobs do Azure.

```json
{
    "type": "datasets",
    "name": "[variables('inputDatasetName')]",
    "dependsOn": [
        "[parameters('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2018-06-01",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'), '/')]",
            "fileName": "[parameters('inputBlobName')]"
        },
        "linkedServiceName": {
            "referenceName": "[variables('azureStorageLinkedServiceName')]",
            "type": "LinkedServiceReference"
        }
    }
},
```

#### <a name="azure-blob-output-dataset"></a>Conjunto de dados de saída dos blobs do Azure

Especifique o nome da pasta no Armazenamento de Blobs do Azure que contém os dados copiados a partir da pasta de entrada. Veja [Azure Blob dataset properties (Propriedades do conjunto de dados de Blobs do Azure)](connector-azure-blob-storage.md#dataset-properties) para obter detalhes sobre as propriedades JSON utilizadas para definir um conjunto de dados de Blobs do Azure.

```json
{
    "type": "datasets",
    "name": "[variables('outputDatasetName')]",
    "dependsOn": [
        "[parameters('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2018-06-01",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'), '/')]",
            "fileName": "[parameters('outputBlobName')]"
        },
        "linkedServiceName": {
            "referenceName": "[variables('azureStorageLinkedServiceName')]",
            "type": "LinkedServiceReference"
        }
    }
}
```

#### <a name="data-pipeline"></a>Pipeline de dados

Defina um pipeline que copia os dados de um conjunto de dados dos blobs do Azure para outro. Veja [Pipeline JSON (JSON do Pipeline)](concepts-pipelines-activities.md#pipeline-json) para obter descrições dos elementos JSON utilizados para definir um pipeline neste exemplo.

```json
{
    "type": "pipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[parameters('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('inputDatasetName')]",
        "[variables('outputDatasetName')]"
    ],
    "apiVersion": "2018-06-01",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "name": "MyCopyActivity",
            "inputs": [{
                "referenceName": "[variables('inputDatasetName')]",
                "type": "DatasetReference"
            }],
            "outputs": [{
                "referenceName": "[variables('outputDatasetName')]",
                "type": "DatasetReference"
            }]
        }]
    }
}
```

#### <a name="trigger"></a>Acionamento

Defina um acionador que executa o pipeline uma vez por hora. O acionador implementado está no estado parado. Iniciar o acionador com o **Start-AzDataFactoryV2Trigger** cmdlet. Para obter mais informações sobre os acionadores, veja o artigo [Execuções de pipelines e acionadores](concepts-pipeline-execution-triggers.md#triggers).

```json
{
    "type": "triggers",
    "name": "[variables('triggerName')]",
    "dependsOn": [
        "[parameters('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('inputDatasetName')]",
        "[variables('outputDatasetName')]",
        "[variables('pipelineName')]"
    ],
    "apiVersion": "2018-06-01",
    "properties": {
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Hour",
                "interval": 1,
                "startTime": "2017-11-28T00:00:00",
                "endTime": "2017-11-29T00:00:00",
                "timeZone": "UTC"
            }
        },
        "pipelines": [{
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "ArmtemplateSampleCopyPipeline"
            },
            "parameters": {}
        }]
    }
}
```

## <a name="reuse-the-template"></a>Reutilizar o modelo

No tutorial, criou um modelo para definir as entidades do Data Factory e um modelo para transmitir os valores dos parâmetros. Para utilizar o mesmo modelo para implementar entidades do Data Factory em diferentes ambientes, pode criar um ficheiro de parâmetro para cada ambiente e utilizá-lo quando implementar nesse ambiente.

Exemplo:

```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```

Tenha em atenção que o primeiro comando utiliza o ficheiro de parâmetro para o ambiente de desenvolvimento, o segundo para o ambiente de teste e o terceiro para o ambiente de produção.

Também pode reutilizar o modelo para efetuar tarefas repetidas. Por exemplo, crie muitas fábricas de dados com um ou mais pipelines que implementem a mesma lógica, mas em que cada uma destas utiliza diferentes contas de armazenamento do Azure. Neste cenário, utilize o mesmo modelo no mesmo ambiente (programação, teste ou produção) com os diferentes ficheiros de parâmetro para criar fábricas de dados.

## <a name="next-steps"></a>Passos Seguintes

O pipeline neste exemplo copia dados de uma localização para outra localização num armazenamento de blobs do Azure. Leia os [tutoriais](tutorial-copy-data-dot-net.md) para saber como utilizar o Data Factory em mais cenários.
