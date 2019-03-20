---
title: Criar a primeira fábrica de dados (modelo do Resource Manager) | Microsoft Docs
description: Neste tutorial, vai criar um exemplo de pipeline do Azure Data Factory com um modelo do Azure Resource Manager.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: ''
editor: ''
ms.assetid: eb9e70b9-a13a-4a27-8256-2759496be470
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: d0eda93855c3be66d4c733774a6cc1d5875a8394
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57998299"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-resource-manager-template"></a>Tutorial: Criar a primeira fábrica de dados do Azure com o modelo Azure Resource Manager
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-build-your-first-pipeline.md)
> * [Portal do Azure](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Modelo do Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)
> 
 
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [início rápido: Criar uma fábrica de dados com o Azure Data Factory](../quickstart-create-data-factory-dot-net.md).

Neste artigo, vai utilizar um modelo do Azure Resource Manager para criar a sua primeira fábrica de dados do Azure. Para fazer o tutorial com outras ferramentas/SDKs, selecione uma das opções na lista pendente.

O pipeline neste tutorial tem uma atividade: **Atividade do Hive do HDInsight**. Esta atividade executa um script de ramo de registo num cluster do Azure HDInsight que transforma os dados de entrada para produzir os dados de saída. O pipeline está agendado para ser executado uma vez por mês entre as horas de início e de fim especificadas. 

> [!NOTE]
> O pipeline de dados neste tutorial transforma os dados de entrada para produzirem dados de saída. Para obter um tutorial sobre como copiar dados com o Azure Data Factory, veja [Tutorial: Copiar dados de armazenamento de BLOBs para base de dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> O pipeline neste tutorial tem apenas uma atividade do tipo: HDInsightHive. Um pipeline pode ter mais de uma atividade. Além disso, pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra. Para obter mais informações, veja [scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) (agendamento e execução no Data Factory). 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Leia o artigo [Descrição Geral do Tutorial](data-factory-build-your-first-pipeline.md) e conclua os passos de **pré-requisitos**.
* Siga as instruções no artigo [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview) para instalar a versão mais recente do Azure PowerShell no computador.
* Veja [Authoring Azure Resource Manager Templates (Criação de Modelos Azure Resource Manager)](../../azure-resource-manager/resource-group-authoring-templates.md) para saber mais sobre os modelos Azure Resource Manager. 

## <a name="in-this-tutorial"></a>Neste tutorial

| Entidade | Descrição |
| --- | --- |
| Serviço ligado do Storage do Azure |Liga a sua conta de Armazenamento do Azure à fábrica de dados. A conta de Armazenamento do Azure possui os dados de entrada e de saída do pipeline neste exemplo. |
| Serviço ligado do HDInsight a pedido |Liga um cluster HDInsight a pedido à fábrica de dados. O cluster é automaticamente criado para que possa processar dados e é eliminado após o processamento estar concluído. |
| Conjunto de dados de entrada de Blobs do Azure |Refere-se ao serviço ligado de Armazenamento do Azure. O serviço ligado refere-se a uma conta de armazenamento do Azure e o conjunto de dados do Blob do Azure especifica um contentor, uma pasta e um nome de ficheiro no armazenamento que contém os dados de entrada. |
| Conjunto de dados de saída do Blob do Azure |Refere-se ao serviço ligado de Armazenamento do Azure. O serviço ligado refere-se a uma conta de armazenamento do Azure e o conjunto de dados do Blob do Azure especifica um contentor, uma pasta e um nome de ficheiro no armazenamento que contém os dados de saída. |
| Pipeline de dados |O pipeline tem uma atividade do tipo HDInsightHive que consome o conjunto de dados de entrada e produz o conjunto de dados de saída. |

Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline pode conter uma atividade ou mais. Existem dois tipos de atividades: [atividades de movimento de dados](data-factory-data-movement-activities.md) e [atividades de transformação de dados](data-factory-data-transformation-activities.md). Neste tutorial, pode criar um pipeline com uma atividade (atividade do Hive).

A secção seguinte disponibiliza o modelo do Resource Manager completo para a definição de entidades do Data Factory, para que possa rapidamente dar uma vista de olhos pelo tutorial e testar o modelo. Para compreender como cada entidade do Data Factory está definida, consulte a secção [Data Factory entities in the template (Entidades do Data Factory no modelo)](#data-factory-entities-in-the-template). Para saber mais sobre a sintaxe JSON e propriedades de recursos do Data Factory num modelo, veja [tipos de recursos de DataFactory](/azure/templates/microsoft.datafactory/allversions).

## <a name="data-factory-json-template"></a>Modelo JSON do Data Factory
O modelo do Resource Manager de nível superior para definir uma fábrica de dados é: 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```
Crie um ficheiro JSON com o nome **ADFTutorialARM.json** na pasta **C:\ADFGetStarted** com o seguinte conteúdo:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the input/output data." } },
          "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
          "blobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
          "inputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that has the input file." } },
          "inputBlobName": { "type": "string", "metadata": { "description": "Name of the input file/blob." } },
          "outputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that will hold the transformed data." } },
          "hiveScriptFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that contains the Hive query file." } },
          "hiveScriptFile": { "type": "string", "metadata": { "description": "Name of the hive query (HQL) file." } }
    },
    "variables": {
          "dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
          "azureStorageLinkedServiceName": "AzureStorageLinkedService",
          "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
          "blobInputDatasetName": "AzureBlobInput",
          "blobOutputDatasetName": "AzureBlobOutput",
          "pipelineName": "HiveTransformPipeline"
    },
    "resources": [
      {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US",
        "resources": [
          {
            "type": "linkedservices",
            "name": "[variables('azureStorageLinkedServiceName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureStorage",
                  "description": "Azure Storage linked service",
                  "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                  }
            }
          },
          {
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "HDInsightOnDemand",
                  "typeProperties": {
                    "version": "3.5",
                    "clusterSize": 1,
                    "timeToLive": "00:05:00",
                    "osType": "Linux",
                    "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
                  }
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobInputDatasetName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "typeProperties": {
                    "fileName": "[parameters('inputBlobName')]",
                    "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
                    "format": {
                          "type": "TextFormat",
                          "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Month",
                    "interval": 1
                  },
                  "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobOutputDatasetName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "typeProperties": {
                    "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
                    "format": {
                          "type": "TextFormat",
                          "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Month",
                    "interval": 1
                  }
            }
          },
          {
            "type": "datapipelines",
            "name": "[variables('pipelineName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]",
                  "[variables('hdInsightOnDemandLinkedServiceName')]",
                  "[variables('blobInputDatasetName')]",
                  "[variables('blobOutputDatasetName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "description": "Pipeline that transforms data using Hive script.",
                  "activities": [
                {
                      "type": "HDInsightHive",
                      "typeProperties": {
                        "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                        "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                        "defines": {
                              "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                              "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                        }
                      },
                      "inputs": [
                        {
                              "name": "[variables('blobInputDatasetName')]"
                        }
                      ],
                      "outputs": [
                        {
                              "name": "[variables('blobOutputDatasetName')]"
                        }
                      ],
                      "policy": {
                        "concurrency": 1,
                        "retry": 3
                      },
                      "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                      },
                      "name": "RunSampleHiveActivity",
                      "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
                }
                  ],
                  "start": "2017-07-01T00:00:00Z",
                  "end": "2017-07-02T00:00:00Z",
                  "isPaused": false
              }
          }
        ]
      }
    ]
}
```

> [!NOTE]
> Pode encontrar outro exemplo de modelo do Resource Manager para criar uma fábrica de dados do Azure no [Tutorial: Criar um pipeline com a atividade de cópia com um modelo Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md).  
> 
> 

## <a name="parameters-json"></a>Parâmetros JSON
Crie um ficheiro JSON com o nome **ADFTutorialARM Parameters.json** que contém os parâmetros para o modelo do Azure Resource Manager.  

> [!IMPORTANT]
> Especifique o nome e a chave da sua conta de armazenamento do Azure para os parâmetros **storageAccountName** e **storageAccountKey** neste ficheiro de parâmetros. 
> 
> 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "value": "<Name of your Azure Storage account>"
        },
        "storageAccountKey": {
            "value": "<Key of your Azure Storage account>"
        },
        "blobContainer": {
            "value": "adfgetstarted"
        },
        "inputBlobFolder": {
            "value": "inputdata"
        },
        "inputBlobName": {
            "value": "input.log"
        },
        "outputBlobFolder": {
            "value": "partitioneddata"
        },
        "hiveScriptFolder": {
              "value": "script"
        },
        "hiveScriptFile": {
              "value": "partitionweblogs.hql"
        }
    }
}
```

> [!IMPORTANT]
> Pode ter ficheiros JSON de parâmetro separado para ambientes de desenvolvimento, teste e produção, que pode utilizar com o mesmo modelo JSON do Data Factory. Ao utilizar um script do Power Shell, pode automatizar a implementação de entidades do Data Factory nestes ambientes. 
> 
> 

## <a name="create-data-factory"></a>Criar fábrica de dados
1. Inicie o **Azure PowerShell** e execute o seguinte comando: 
   * Execute o comando seguinte e introduza o nome de utilizador e a palavra-passe que utiliza para iniciar sessão no portal do Azure.
     ```PowerShell
     Connect-AzAccount
     ```  
   * Execute o comando seguinte para ver todas as subscrições para esta conta.
     ```PowerShell
     Get-AzSubscription
     ``` 
   * Execute o comando seguinte para selecionar a subscrição com a qual pretende trabalhar. Esta subscrição deve ser idêntica à que utilizou no Portal do Azure.
     ```
     Get-AzSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzContext
     ```   
2. Execute o seguinte comando para implementar as entidades do Data Factory com o modelo do Resource Manager que criou no Passo 1. 

    ```PowerShell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFTutorialARM-Parameters.json
    ```

## <a name="monitor-pipeline"></a>Monitorizar o pipeline
1. Depois de iniciar sessão no [portal do Azure](https://portal.azure.com/), clique em **Procurar** e selecione **Fábricas de dados**.
     ![Procurar->Fábricas de dados](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2. No painel **Fábricas de dados**, clique na fábrica de dados (**TutorialFactoryARM**) que criou.    
3. No painel **Data Factory** da fábrica de dados, clique em **Diagrama**.

     ![Mosaico do Diagrama](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4. Na **Vista de Diagrama**, verá uma descrição geral dos pipelines e dos conjuntos de dados utilizados neste tutorial.
   
   ![Vista de Diagrama](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
5. Na Vista de Diagrama, faça duplo clique no conjunto de dados **AzureBlobOutput**. Verá o setor que está atualmente a ser processado.
   
    ![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
6. Quando o processamento terminar, verá o setor no estado **Pronto**. A criação de um cluster do HDInsight a pedido demora, por norma, algum tempo (cerca de 20 minutos). Assim, prevê-se que o pipeline demore **aproximadamente 30 minutos** a processar o setor.
   
    ![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)    
7. Quando o setor estiver no estado **Pronto**, verifique a pasta **partitioneddata** no contentor **adfgetstarted** do seu armazenamento de blobs para os dados de saída.  

Veja [Monitor datasets and pipeline (Monitorizar os conjuntos de dados e o pipeline)](data-factory-monitor-manage-pipelines.md) para obter instruções sobre como utilizar os painéis do Portal do Azure para monitorizar o pipeline e os conjuntos de dados que criou neste tutorial.

Pode ainda utilizar a Aplicação de Monitorização e Gestão para monitorizar os seus pipelines de dados. Veja [Monitor and manage Azure Data Factory pipelines using Monitoring App (Monitorizar e gerir pipelines do Azure Data Factory com a Aplicação de Monitorização)](data-factory-monitor-manage-app.md) para obter detalhes sobre a utilização da aplicação. 

> [!IMPORTANT]
> O ficheiro de entrada é eliminado quando o setor é processado com êxito. Por conseguinte, se pretender voltar a executar o setor ou repetir o tutorial, carregue o ficheiro de entrada (input.log) na pasta inputdata do contentor adfgetstarted.
> 
> 

## <a name="data-factory-entities-in-the-template"></a>Entidades do Data Factory no modelo
### <a name="define-data-factory"></a>Definir fábrica de dados
Defina uma fábrica de dados no modelo do Resource Manager, conforme mostrado no exemplo seguinte:  

```json
"resources": [
{
    "name": "[variables('dataFactoryName')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "West US"
}
```
O dataFactoryName é definido como: 

```json
"dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
```
É uma cadeia exclusiva com base no ID do grupo do recursos.  

### <a name="defining-data-factory-entities"></a>Definir entidades do Data Factory
As seguintes entidades do Data Factory são definidas no modelo JSON: 

* [Serviço ligado do Armazenamento do Azure](#azure-storage-linked-service)
* [Serviço ligado do HDInsight a pedido](#hdinsight-on-demand-linked-service)
* [Conjunto de dados de entrada do blobs do Azure](#azure-blob-input-dataset)
* [Conjunto de dados de saída do blob do Azure](#azure-blob-output-dataset)
* [Pipeline de dados com uma atividade de cópia](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Serviço ligado do Storage do Azure
Especifique o nome e a chave da sua conta de armazenamento do Azure nesta secção. Veja [Azure Storage linked service (Serviço ligado de Armazenamento do Azure)](data-factory-azure-blob-connector.md#azure-storage-linked-service) para obter detalhes sobre as propriedades JSON utilizadas para definir um serviço ligado de Armazenamento do Azure. 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
A **connectionString** utiliza os parâmetros storageAccountName e storageAccountKey. Os valores para estes parâmetros foram transmitidos através da utilização de um ficheiro de configuração. A definição também utiliza variáveis: azureStorageLinkedService e dataFactoryName definidas no modelo. 

#### <a name="hdinsight-on-demand-linked-service"></a>Serviço ligado do HDInsight a pedido
Veja o artigo [Serviços ligados de computação](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes sobre as propriedades JSON utilizadas para definir um serviço ligado do HDInsight a pedido.  

```json
{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
        }
    }
}
```
Tenha em atenção os seguintes pontos: 

* O Data Factory cria um cluster do HDInsight **baseado no Linux** com o JSON anterior. Veja [On-demand HDInsight Linked Service (Serviço Ligado do HDInsight a Pedido)](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes. 
* Também pode utilizar o **seu próprio cluster do HDInsight** em vez de utilizar um cluster do HDInsight a pedido. Veja [HDInsight Linked Service (Serviço Ligado do HDInsight)](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para obter detalhes.
* O cluster do HDInsight cria um **contentor predefinido** no armazenamento de blobs especificado no JSON (**linkedServiceName**). Quando o cluster é eliminado, o HDInsight não é eliminado deste contentor. Este comportamento é propositado. Com o serviço ligado do HDInsight a pedido, é criado um cluster do HDInsight sempre que um setor tiver de ser processado, exceto se houver um cluster em direto (**timeToLive**) que será eliminado no fim do processamento.
  
    À medida que são processados mais setores, verá muitos contentores no armazenamento de blobs do Azure. Se não precisar deles para a resolução de problemas das tarefas, poderá eliminá-los para reduzir o custo de armazenamento. Os nomes destes contentores seguem um padrão: "adf**nomedafábricadedados**-**nomedoserviçoligado**-carimbodedataehora". Utilize ferramentas como o [Explorador de Armazenamento do Microsoft](https://storageexplorer.com/) para eliminar contentores no armazenamento de blobs do Azure.

Veja [On-demand HDInsight Linked Service (Serviço Ligado do HDInsight a Pedido)](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.

#### <a name="azure-blob-input-dataset"></a>Conjunto de dados de entrada de blobs do Azure
Especifique os nomes do contentor de blob, da pasta e do ficheiro que contém os dados de entrada. Veja [Azure Blob dataset properties (Propriedades do conjunto de dados de Blobs do Azure)](data-factory-azure-blob-connector.md#dataset-properties) para obter detalhes sobre as propriedades JSON utilizadas para definir um conjunto de dados de Blobs do Azure. 

```json
{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "typeProperties": {
            "fileName": "[parameters('inputBlobName')]",
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true
    }
}
```
Esta definição utiliza os seguintes parâmetros definidos no modelo de parâmetro: blobContainer, inputBlobFolder e inputBlobName. 

#### <a name="azure-blob-output-dataset"></a>Conjunto de dados de saída do Blob do Azure
Especifique os nomes do contentor de blob e pasta que contêm os dados de saída. Veja [Azure Blob dataset properties (Propriedades do conjunto de dados de Blobs do Azure)](data-factory-azure-blob-connector.md#dataset-properties) para obter detalhes sobre as propriedades JSON utilizadas para definir um conjunto de dados de Blobs do Azure.  

```json
{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        }
    }
}
```

Esta definição utiliza os seguintes parâmetros definidos no modelo de parâmetro: blobContainer e outputBlobFolder. 

#### <a name="data-pipeline"></a>Pipeline de dados
Defina um pipeline que transforme dados executando o script de ramo de registo num cluster HDInsight a pedido do Azure. Veja [Pipeline JSON (JSON do Pipeline)](data-factory-create-pipelines.md#pipeline-json) para obter descrições dos elementos JSON utilizados para definir um pipeline neste exemplo. 

```json
{
    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('hdInsightOnDemandLinkedServiceName')]",
        "[variables('blobInputDatasetName')]",
        "[variables('blobOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "description": "Pipeline that transforms data using Hive script.",
        "activities": [
        {
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                "defines": {
                    "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                    "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                }
            },
            "inputs": [
            {
                "name": "[variables('blobInputDatasetName')]"
            }
            ],
            "outputs": [
            {
                "name": "[variables('blobOutputDatasetName')]"
            }
            ],
            "policy": {
                "concurrency": 1,
                "retry": 3
            },
            "scheduler": {
                "frequency": "Month",
                "interval": 1
            },
            "name": "RunSampleHiveActivity",
            "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
        }
        ],
        "start": "2017-07-01T00:00:00Z",
        "end": "2017-07-02T00:00:00Z",
        "isPaused": false
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

Também pode reutilizar o modelo para efetuar tarefas repetidas. Por exemplo, tem de criar muitas fábricas de dados com um ou mais pipelines que implementem a mesma lógica, mas cada fábrica de dados utiliza diferentes contas de armazenamento do Azure e da Base de Dados SQL do Azure. Neste cenário, utilize o mesmo modelo no mesmo ambiente (programação, teste ou produção) com os diferentes ficheiros de parâmetro para criar fábricas de dados. 

## <a name="resource-manager-template-for-creating-a-gateway"></a>Modelo do Resource Manager para criar um gateway
Eis o exemplo de um modelo do Resource Manager para criar um gateway lógico. Instale um gateway no computador no local ou na VM de IaaS do Azure e registe o gateway com o serviço do Data Factory com uma chave. Veja [Move data between on-premises and cloud (Mover dados entre o local e a nuvem)](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
    },
    "variables": {
        "dataFactoryName":  "GatewayUsingArmDF",
        "apiVersion": "2015-10-01",
        "singleQuote": "'"
    },
    "resources": [
        {
            "name": "[variables('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "eastus",
            "resources": [
                {
                    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
                    "type": "gateways",
                    "apiVersion": "[variables('apiVersion')]",
                    "name": "GatewayUsingARM",
                    "properties": {
                        "description": "my gateway"
                    }
                }            
            ]
        }
    ]
}
```
Este modelo cria uma fábrica de dados com o nome GatewayUsingArmDF com um gateway designado: GatewayUsingARM. 

## <a name="see-also"></a>Consultar Também

| Tópico | Descrição |
|:--- |:--- |
| [Pipelines](data-factory-create-pipelines.md) |Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory e como os utilizar para construir fluxos de dados ponto a ponto condicionados por dados para o seu cenário ou empresa. |
| [Conjuntos de dados](data-factory-create-datasets.md) |Este artigo ajuda-o a compreender os conjuntos de dados no Azure Data Factory. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) |Este artigo explica os aspetos de agendamento e execução do modelo da aplicação do Azure Data Factory. |
| [Monitorizar e gerir pipelines com a Aplicação de Monitorização](data-factory-monitor-manage-app.md) |Este artigo descreve como monitorizar, gerir e depurar pipelines com a Aplicação de Monitorização e Gestão. |

