---
title: Executar atividade de gasoduto na Fábrica de Dados Azure
description: Saiba como pode utilizar a Atividade do Gasoduto executar para invocar um oleoduto data factory a partir de outro oleoduto data factory.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9bcf8fd62e3b20701136d7912074afb37d567e2f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383352"
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Executar atividade de pipeline na Fábrica de Dados Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A atividade Executar Pipeline permite que um pipeline do Data Factory invoque outro pipeline.



## <a name="syntax"></a>Syntax

```json
{
    "name": "MyPipeline",
    "properties": {
        "activities": [
            {
                "name": "ExecutePipelineActivity",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {                        
                        "mySourceDatasetFolderPath": {
                            "value": "@pipeline().parameters.mySourceDatasetFolderPath",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "<InvokedPipelineName>",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                 }
            }
        ],
        "parameters": [
            {
                "mySourceDatasetFolderPath": {
                    "type": "String"
                }
            }
        ]
    }
}
```

## <a name="type-properties"></a>Tipo de propriedades

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
name | Nome da atividade do gasoduto de execução. | String | Yes
tipo | Deve ser definido para: **ExecutePipeline**. | String | Yes
oleoduto | Referência do gasoduto ao gasoduto dependente que este gasoduto invoca. Um objeto de referência do gasoduto tem duas propriedades: nome de **referência** e **tipo**. A propriedade referenceName especifica o nome do gasoduto de referência. A propriedade tipo deve ser definida para PipelineReference. | PipelineReference | Yes
parâmetros | Parâmetros a passar para o gasoduto invocado | Um objeto JSON que mapeia nomes de parâmetros para valores de argumento | No
esperaCompletion | Define se a execução da atividade aguarda o fim da execução do gasoduto dependente. A predefinição é falso. | Booleano | No

## <a name="sample"></a>Sample
Este cenário tem dois oleodutos:

- **Oleoduto principal** - Este gasoduto tem uma atividade do Pipeline Execut que chama o gasoduto invocado. O oleoduto principal tem dois parâmetros: `masterSourceBlobContainer` `masterSinkBlobContainer` . .
- **Pipeline invocado** - Este gasoduto tem uma atividade copy que copia dados de uma fonte Azure Blob para a pia Azure Blob. O gasoduto invocado requer dois parâmetros: `sourceBlobContainer` `sinkBlobContainer` . .

### <a name="master-pipeline-definition"></a>Definição de gasoduto principal

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ExecutePipeline",
        "typeProperties": {
          "pipeline": {
            "referenceName": "invokedPipeline",
            "type": "PipelineReference"
          },
          "parameters": {
            "sourceBlobContainer": {
              "value": "@pipeline().parameters.masterSourceBlobContainer",
              "type": "Expression"
            },
            "sinkBlobContainer": {
              "value": "@pipeline().parameters.masterSinkBlobContainer",
              "type": "Expression"
            }
          },
          "waitOnCompletion": true
        },
        "name": "MyExecutePipelineActivity"
      }
    ],
    "parameters": {
      "masterSourceBlobContainer": {
        "type": "String"
      },
      "masterSinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="invoked-pipeline-definition"></a>Definição de gasoduto invocado

```json
{
  "name": "invokedPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "SourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

**Serviço ligado**

```json
{
    "name": "BlobStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=*****;AccountKey=*****"
    }
  }
}
```

**Conjunto de dados de origem**
```json
{
    "name": "SourceBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sourceBlobContainer",
        "type": "Expression"
      },
      "fileName": "salesforce.txt"
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

**Conjunto de dados sink**
```json
{
    "name": "sinkBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sinkBlobContainer",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

### <a name="running-the-pipeline"></a>A executar o oleoduto

Para executar o oleoduto principal neste exemplo, são passados os seguintes valores para os parâmetros masterSourceBlobContainer e masterSinkBlobContainer: 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

O gasoduto principal encaminha estes valores para o gasoduto invocado, como mostra o seguinte exemplo: 

```json
{
    "type": "ExecutePipeline",
    "typeProperties": {
      "pipeline": {
        "referenceName": "invokedPipeline",
        "type": "PipelineReference"
      },
      "parameters": {
        "sourceBlobContainer": {
          "value": "@pipeline().parameters.masterSourceBlobContainer",
          "type": "Expression"
        },
        "sinkBlobContainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>Passos seguintes
Consulte outras atividades de fluxo de controlo suportadas pela Data Factory: 

- [Para Cada Atividade](control-flow-for-each-activity.md)
- [Obter Atividade de Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade Web](control-flow-web-activity.md)
