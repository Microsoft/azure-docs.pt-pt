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
author: djpmsft
ms.author: daperlov
manager: craigg
ms.openlocfilehash: 8766ff461227a749a432771dfe7dbe96a291109a
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71008733"
---
# <a name="tutorial-create-an-azure-data-factory-using-azure-resource-manager-template"></a>Tutorial: Criar um data factory do Azure usando o modelo Azure Resource Manager

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Versão atual](quickstart-create-data-factory-resource-manager-template.md)

Este início rápido descreve como utilizar um modelo do Azure Resource Manager para criar uma fábrica de dados do Azure. O pipeline que criar nesta fábrica de dados **copia** dados de uma pasta para outra pasta num armazenamento de blobs do Azure. Para obter um tutorial sobre como **transformar** dados usando Azure data Factory, consulte [o tutorial: Transforme dados usando](transform-data-using-spark.md)o Spark.

> [!NOTE]
> Este artigo não disponibiliza uma introdução detalhada do serviço Data Factory. Para obter uma introdução ao serviço Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)]

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Instale os módulos do Azure PowerShell mais recentes ao seguir as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-Az-ps).

## <a name="resource-manager-templates"></a>Modelos do Resource Manager

Para saber mais sobre os modelos do Azure Resource Manager, veja [Authoring Azure Resource Manager Template](../azure-resource-manager/resource-group-authoring-templates.md) (Criar Modelos do Azure Resource Manager).

A secção seguinte disponibiliza o modelo do Resource Manager completo para a definição de entidades do Data Factory, para que possa rapidamente dar uma vista de olhos pelo tutorial e testar o modelo. Para compreender como cada entidade do Data Factory está definida, consulte a secção [Data Factory entities in the template (Entidades do Data Factory no modelo)](#data-factory-entities-in-the-template).

Para saber mais sobre a sintaxe JSON e as propriedades para Data Factory recursos em um modelo, consulte [tipos de recurso Microsoft. datafactory](/azure/templates/microsoft.datafactory/allversions).

## <a name="data-factory-json"></a>JSON do Data Factory

Crie um arquivo JSON chamado **ADFTutorialARM. JSON** na pasta **C:\ADFTutorial** (crie a pasta ADFTutorial se ela ainda não existir) com o seguinte conteúdo:

```json
{  
    "$schema":"http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion":"1.0.0.0",
    "parameters":{  
        "dataFactoryName":{  
            "type":"string",
            "metadata":"Data Factory Name"
        },
        "dataFactoryLocation":{  
            "type":"string",
            "defaultValue":"East US",
            "metadata":{  
                "description":"Location of the data factory. Currently, only East US, East US 2, and West Europe are supported. "
            }
        },
        "storageAccountName":{  
            "type":"string",
            "metadata":{  
                "description":"Name of the Azure storage account that contains the input/output data."
            }
        },
        "storageAccountKey":{  
            "type":"securestring",
            "metadata":{  
                "description":"Key for the Azure storage account."
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
    "variables":{  
        "factoryId":"[concat('Microsoft.DataFactory/factories/', parameters('dataFactoryName'))]"
    },
    "resources":[  
        {  
            "name":"[parameters('dataFactoryName')]",
            "apiVersion":"2018-06-01",
            "type":"Microsoft.DataFactory/factories",
            "location":"[parameters('dataFactoryLocation')]",
            "identity":{  
                "type":"SystemAssigned"
            },
            "resources":[  
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateStorageLinkedService')]",
                    "type":"Microsoft.DataFactory/factories/linkedServices",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "annotations":[  

                        ],
                        "type":"AzureBlobStorage",
                        "typeProperties":{  
                            "connectionString":"[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetIn')]",
                    "type":"Microsoft.DataFactory/factories/datasets",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "linkedServiceName":{  
                            "referenceName":"ArmtemplateStorageLinkedService",
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
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetOut')]",
                    "type":"Microsoft.DataFactory/factories/datasets",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "linkedServiceName":{  
                            "referenceName":"ArmtemplateStorageLinkedService",
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
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateSampleCopyPipeline')]",
                    "type":"Microsoft.DataFactory/factories/pipelines",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "activities":[  
                            {  
                                "name":"MyCopyActivity",
                                "type":"Copy",
                                "dependsOn":[  

                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  

                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"BinarySource",
                                        "storeSettings":{  
                                            "type":"AzureBlobStorageReadSettings",
                                            "recursive":true
                                        }
                                    },
                                    "sink":{  
                                        "type":"BinarySink",
                                        "storeSettings":{  
                                            "type":"AzureBlobStorageWriteSettings"
                                        }
                                    },
                                    "enableStaging":false
                                },
                                "inputs":[  
                                    {  
                                        "referenceName":"ArmtemplateTestDatasetIn",
                                        "type":"DatasetReference",
                                        "parameters":{  

                                        }
                                    }
                                ],
                                "outputs":[  
                                    {  
                                        "referenceName":"ArmtemplateTestDatasetOut",
                                        "type":"DatasetReference",
                                        "parameters":{  

                                        }
                                    }
                                ]
                            }
                        ],
                        "annotations":[  

                        ]
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetIn')]",
                        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetOut')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmTemplateTestTrigger')]",
                    "type":"Microsoft.DataFactory/factories/triggers",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "annotations":[  

                        ],
                        "runtimeState":"Started",
                        "pipelines":[  
                            {  
                                "pipelineReference":{  
                                    "referenceName":"ArmtemplateSampleCopyPipeline",
                                    "type":"PipelineReference"
                                },
                                "parameters":{  

                                }
                            }
                        ],
                        "type":"ScheduleTrigger",
                        "typeProperties":{  
                            "recurrence":{  
                                "frequency":"Hour",
                                "interval":1,
                                "startTime":"[parameters('triggerStartTime')]",
                                "endTime":"[parameters('triggerEndTime')]",
                                "timeZone":"UTC"
                            }
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/pipelines/ArmtemplateSampleCopyPipeline')]"
                    ]
                }
            ]
        }
    ]
}
```
## <a name="parameters-json"></a>Parâmetros JSON

Crie um ficheiro JSON com o nome **ADFTutorialARM Parameters.json** que contém os parâmetros para o modelo do Azure Resource Manager.

> [!IMPORTANT]
> - Especifique o nome e a chave da sua conta de armazenamento do Azure para os parâmetros **storageAccountName** e **storageAccountKey** neste ficheiro de parâmetros. Criou o contentor adftutorial e carregou o ficheiro de exemplo (emp.txt) para a pasta de entrada neste armazenamento de blobs do Azure.
> - Especifique um nome globalmente exclusivo para a fábrica de dados no parâmetro **dataFactoryName**. Por exemplo: ARMTutorialFactoryJohnDoe11282017.
> - Em **triggerStartTime**, especifique o dia atual no formato: `2019-09-08T00:00:00`.
> - Em **triggerEndTime**, especifique o dia seguinte no formato: `2019-09-09T00:00:00`. Também pode verificar a hora UTC atual e especificar como hora de fim a primeira ou as duas primeiras horas seguintes. Por exemplo, se a hora UTC agora for 1:32, especifique `2019-09-09:03:00:00` como a hora de fim. Neste caso, o acionador executa o pipeline duas vezes (às 2:00 e às 03:00).

```json
{  
    "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":"1.0.0.0",
    "parameters":{  
        "dataFactoryName":{  
            "value":"<datafactoryname>"
        },
        "dataFactoryLocation":{  
            "value":"East US"
        },
        "storageAccountName":{  
            "value":"<yourstorageaccountname>"
        },
        "storageAccountKey":{  
            "value":"<yourstorageaccountkey>"
        },
        "triggerStartTime":{  
            "value":"2019-09-08T11:00:00"
        },
        "triggerEndTime":{  
            "value":"2019-09-08T14:00:00"
        }
    }
}
```

> [!IMPORTANT]
> Pode ter ficheiros JSON de parâmetro separado para ambientes de desenvolvimento, teste e produção, que pode utilizar com o mesmo modelo JSON do Data Factory. Ao utilizar um script do Power Shell, pode automatizar a implementação de entidades do Data Factory nestes ambientes.

## <a name="deploy-data-factory-entities"></a>Implementar entidades do Data Factory

No PowerShell, execute o comando a seguir para implantar Data Factory entidades em seu grupo de recursos (nesse caso, use ADFTutorialResourceGroup como exemplo) usando o modelo do Resource Manager criado anteriormente neste guia de início rápido.

```powershell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFTutorial\ADFTutorialARM.json -TemplateParameterFile C:\ADFTutorial\ADFTutorialARM-Parameters.json
```

Verá ver um resultado semelhante ao exemplo seguinte:

```console
DeploymentName          : MyARMDeployment
ResourceGroupName       : ADFTutorialResourceGroup
ProvisioningState       : Succeeded
Timestamp               : 9/8/2019 10:52:29 AM
Mode                    : Incremental
TemplateLink            : 
Parameters              : 
                          Name                   Type                       Value     
                          =====================  =========================  ==========
                          dataFactoryName        String                     <data factory name>
                          dataFactoryLocation    String                     East US   
                          storageAccountName     String                     <storage account name>
                          storageAccountKey      SecureString                         
                          triggerStartTime       String                     9/8/2019 11:00:00 AM
                          triggerEndTime         String                     9/8/2019 2:00:00 PM
                          
Outputs                 : 
DeploymentDebugLogLevel : 
```

## <a name="start-the-trigger"></a>Iniciar o acionador

O modelo implementa as seguintes entidades do Data Factory:

- Serviço ligado do Storage do Azure
- Conjuntos de dados binários (entrada e saída)
- Pipeline com uma atividade de cópia
- Acionador para acionar o pipeline

O acionador implementado está no estado parado. Uma das maneiras de iniciar o gatilho é usar o cmdlet **Start-AzDataFactoryV2Trigger** do PowerShell. O procedimento seguinte mostra os passos detalhados:

1. Na janela do PowerShell, crie uma variável para conter o nome do grupo de recursos. Copie o comando seguinte na janela do PowerShell e prima ENTER. Se você tiver especificado um nome de grupo de recursos diferente para o comando New-AzResourceGroupDeployment, atualize o valor aqui.

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
    DataFactoryName   : ADFQuickstartsDataFactory0905
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Stopped
    ```
    
    Repare que o estado do runtime do acionador é **Parado**.
5. **Inicie o acionador**. O acionador executa o pipeline definido no modelo à hora certa. Ou seja, se tiver executado o comando às 14:25, o acionador executa o pipeline pela primeira vez às 15:00. Em seguida, ele executa o pipeline por hora até a hora de término especificada para o gatilho.

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
6. Confirme se o gatilho foi iniciado executando o comando Get-AzDataFactoryV2Trigger novamente.

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Segue-se o resultado do exemplo:
    
    ```console
    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFQuickstartsDataFactory0905
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Started
    ```

## <a name="monitor-the-pipeline"></a>Monitorizar o pipeline

1. Depois de iniciar sessão no [portal do Azure](https://portal.azure.com/), clique em **Todos os serviços**, pesquise com uma palavra-chave, como **dados fa**, e selecione **Fábricas de dados**.

2. Na página **Fábricas de Dados**, clique na fábrica de dados que criou. Se for necessário, filtre a lista com o nome da fábrica de dados.

3. Na página data Factory, clique em **criar &** bloco de monitor.

4. Na página **vamos** começar, selecione a **guia Monitor**.  ![Monitorar execução de pipeline](media/doc-common-process/get-started-page-monitor-button.png)

    > [!IMPORTANT]
    > Você verá o pipeline é executado somente no relógio de hora (por exemplo: 4 AM, 17:00, 18:00, etc.). Clique em **Atualizar**, na barra de ferramentas, para atualizar a lista quando a for atingida a hora seguinte.

5. Clique no link **Exibir execuções de atividade** na coluna **ações** .

    ![Ligação de ações do pipeline](media/quickstart-create-data-factory-resource-manager-template/pipeline-actions-link.png)

6. Verá as execuções de atividade associadas à execução do pipeline. Neste guia de início rápido, o pipeline tem apenas uma atividade do tipo: CopiarObjeto. Por conseguinte, verá uma execução dessa atividade.

    ![Execuções de atividade](media/quickstart-create-data-factory-resource-manager-template/activity-runs.png)
7. Clique no link **saída** na coluna ações. Verá a saída da operação de cópia numa janela de **Saída**. Clique no botão de maximizar para ver a saída completa. Pode fechar a janela de saída maximizada ou fechar a saída.

8. Quando vir uma execução com êxito/falha, pare o acionador. O acionador executa o pipeline uma vez por hora. Em cada execução, o pipeline copia o mesmo ficheiro da pasta de entrada para a pasta de saída. Para parar o acionador, execute o comando seguinte na janela do PowerShell.
    
    ```powershell
    Stop-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)]

## <a name="data-factory-entities-in-the-template"></a> Definições de JSON para as entidades

As seguintes entidades do Data Factory são definidas no modelo JSON:

- [Serviço ligado do Armazenamento do Azure](#azure-storage-linked-service)
- [Conjunto de dados de entrada binária](#binary-input-dataset)
- [Conjunto de uma saída binária](#binary-output-dataset)
- [Pipeline de dados com uma atividade de cópia](#data-pipeline)
- [Acionador](#trigger)

#### <a name="azure-storage-linked-service"></a>Serviço ligado do Storage do Azure

O AzureStorageLinkedService liga a sua conta do Armazenamento do Azure à fábrica de dados. Como parte dos pré-requisitos, criou um contentor e carregou dados para esta conta de armazenamento. Especifique o nome e a chave da sua conta de armazenamento do Azure nesta secção. Veja [Azure Storage linked service (Serviço ligado de Armazenamento do Azure)](connector-azure-blob-storage.md#linked-service-properties) para obter detalhes sobre as propriedades JSON utilizadas para definir um serviço ligado de Armazenamento do Azure.

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateStorageLinkedService')]",
    "type":"Microsoft.DataFactory/factories/linkedServices",
    "apiVersion":"2018-06-01",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]"
    ]
}
```

A connectionString utiliza os parâmetros storageAccountName e storageAccountKey. Os valores para estes parâmetros foram transmitidos através da utilização de um ficheiro de configuração. A definição também usa variáveis: azureStorageLinkedService e datafactoryname definidos no modelo.

#### <a name="binary-input-dataset"></a>Conjunto de dados de entrada binária

O serviço ligado do armazenamento do Azure especifica a cadeia de ligação que o serviço Data Factory utiliza no tempo de execução para ligar à sua conta de armazenamento do Azure. Em definição de conjunto de dados binário, você especifica os nomes do contêiner de BLOB, da pasta e do arquivo que contém os dados de entrada. Consulte [Propriedades do conjunto](format-binary.md#dataset-properties) de informações binária para obter detalhes sobre as propriedades JSON usadas para definir um conjunto de um DataSet binário.

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetIn')]",
    "type":"Microsoft.DataFactory/factories/datasets",
    "apiVersion":"2018-06-01",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"ArmtemplateStorageLinkedService",
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
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
    ]
}
```

#### <a name="binary-output-dataset"></a>Conjunto de uma saída binária

Especifique o nome da pasta no Armazenamento de Blobs do Azure que contém os dados copiados a partir da pasta de entrada. Consulte [Propriedades do conjunto](format-binary.md#dataset-properties) de informações binária para obter detalhes sobre as propriedades JSON usadas para definir um conjunto de um DataSet binário.

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetOut')]",
    "type":"Microsoft.DataFactory/factories/datasets",
    "apiVersion":"2018-06-01",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"ArmtemplateStorageLinkedService",
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
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
    ]
}
```

#### <a name="data-pipeline"></a>Pipeline de dados

Você define um pipeline que copia os dados de um DataSet binário para outro DataSet binário. Veja [Pipeline JSON (JSON do Pipeline)](concepts-pipelines-activities.md#pipeline-json) para obter descrições dos elementos JSON utilizados para definir um pipeline neste exemplo.

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateSampleCopyPipeline')]",
    "type":"Microsoft.DataFactory/factories/pipelines",
    "apiVersion":"2018-06-01",
    "properties":{  
        "activities":[  
            {  
                "name":"MyCopyActivity",
                "type":"Copy",
                "dependsOn":[  

                ],
                "policy":{  
                    "timeout":"7.00:00:00",
                    "retry":0,
                    "retryIntervalInSeconds":30,
                    "secureOutput":false,
                    "secureInput":false
                },
                "userProperties":[  

                ],
                "typeProperties":{  
                    "source":{  
                        "type":"BinarySource",
                        "storeSettings":{  
                            "type":"AzureBlobStorageReadSettings",
                            "recursive":true
                        }
                    },
                    "sink":{  
                        "type":"BinarySink",
                        "storeSettings":{  
                            "type":"AzureBlobStorageWriteSettings"
                        }
                    },
                    "enableStaging":false
                },
                "inputs":[  
                    {  
                        "referenceName":"ArmtemplateTestDatasetIn",
                        "type":"DatasetReference",
                        "parameters":{  

                        }
                    }
                ],
                "outputs":[  
                    {  
                        "referenceName":"ArmtemplateTestDatasetOut",
                        "type":"DatasetReference",
                        "parameters":{  

                        }
                    }
                ]
            }
        ],
        "annotations":[  

        ]
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetIn')]",
        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetOut')]"
    ]
}
```

#### <a name="trigger"></a>Acionador

Defina um acionador que executa o pipeline uma vez por hora. O acionador implementado está no estado parado. Inicie o gatilho usando o cmdlet **Start-AzDataFactoryV2Trigger** . Para obter mais informações sobre os acionadores, veja o artigo [Execuções de pipelines e acionadores](concepts-pipeline-execution-triggers.md#triggers).

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmTemplateTestTrigger')]",
    "type":"Microsoft.DataFactory/factories/triggers",
    "apiVersion":"2018-06-01",
    "properties":{  
        "annotations":[  

        ],
        "runtimeState":"Started",
        "pipelines":[  
            {  
                "pipelineReference":{  
                    "referenceName":"ArmtemplateSampleCopyPipeline",
                    "type":"PipelineReference"
                },
                "parameters":{  

                }
            }
        ],
        "type":"ScheduleTrigger",
        "typeProperties":{  
            "recurrence":{  
                "frequency":"Hour",
                "interval":1,
                "startTime":"[parameters('triggerStartTime')]",
                "endTime":"[parameters('triggerEndTime')]",
                "timeZone":"UTC"
            }
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/pipelines/ArmtemplateSampleCopyPipeline')]"
    ]
}
```

## <a name="reuse-the-template"></a>Reutilizar o modelo

No tutorial, criou um modelo para definir as entidades do Data Factory e um modelo para transmitir os valores dos parâmetros. Para utilizar o mesmo modelo para implementar entidades do Data Factory em diferentes ambientes, pode criar um ficheiro de parâmetro para cada ambiente e utilizá-lo quando implementar nesse ambiente.

Exemplo:

```powershell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```

Tenha em atenção que o primeiro comando utiliza o ficheiro de parâmetro para o ambiente de desenvolvimento, o segundo para o ambiente de teste e o terceiro para o ambiente de produção.

Também pode reutilizar o modelo para efetuar tarefas repetidas. Por exemplo, crie muitas fábricas de dados com um ou mais pipelines que implementem a mesma lógica, mas em que cada uma destas utiliza diferentes contas de armazenamento do Azure. Neste cenário, utilize o mesmo modelo no mesmo ambiente (programação, teste ou produção) com os diferentes ficheiros de parâmetro para criar fábricas de dados.

## <a name="next-steps"></a>Passos Seguintes

O pipeline neste exemplo copia dados de uma localização para outra localização num armazenamento de blobs do Azure. Leia os [tutoriais](tutorial-copy-data-dot-net.md) para saber como utilizar o Data Factory em mais cenários.
