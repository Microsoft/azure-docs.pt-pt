---
title: Atividade ForEach no Azure Data Factory | Documentos da Microsoft
description: A atividade de cada para define um fluxo de controlo de repetição no seu pipeline. Ele é usado para iterar através de uma coleção e executar atividades especificadas.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: shlo
ms.openlocfilehash: c5c12a66e8f66195a096588d779648d7486ab47b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58092009"
---
# <a name="foreach-activity-in-azure-data-factory"></a>Atividade ForEach no Azure Data Factory
A atividade ForEach define um fluxo de controlo de repetição no seu pipeline. Esta atividade é utilizada para iterar uma coleção e executa atividades especificadas em ciclo. A implementação de ciclo desta atividade é semelhante à estrutura de ciclo Foreach nas linguagens de programação.

## <a name="syntax"></a>Sintaxe
As propriedades são descritas posteriormente neste artigo. A propriedade items é uma coleção e cada item na coleção é referida usando o `@item()` conforme mostrado na seguinte sintaxe:  

```json
{  
   "name":"MyForEachActivityName",
   "type":"ForEach",
   "typeProperties":{  
      "isSequential":"true",
        "items": {
            "value": "@pipeline().parameters.mySinkDatasetFolderPathCollection",
            "type": "Expression"
        },
      "activities":[  
         {  
            "name":"MyCopyActivity",
            "type":"Copy",
            "typeProperties":{  
               ...
            },
            "inputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@pipeline().parameters.mySourceDatasetFolderPath"
                  }
               }
            ],
            "outputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@item()"
                  }
               }
            ]
         }
      ]
   }
}

```

## <a name="type-properties"></a>Propriedades do tipo

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
nome | Nome da atividade para cada. | String | Sim
tipo | Tem de ser definido como **ForEach** | String | Sim
isSequential | Especifica se o loop deve ser executado em seqüência ou em paralelo.  Máximo de 20 iterações de loop pode ser executado ao mesmo tempo em paralelo). Por exemplo, se tiver uma atividade de ForEach iterar através de uma atividade de cópia com 10 origem e sink conjuntos de dados diferentes com **isSequential** definido como False, todas as cópias são executadas ao mesmo tempo. A predefinição é False. <br/><br/> Se "isSequential" estiver definido como False, certifique-se de que existe uma configuração correta para executar vários executáveis. Caso contrário, esta propriedade deve ser utilizada com cuidado para evitar conflitos de escrita. Para obter mais informações, consulte [execução paralela](#parallel-execution) secção. | Booleano | Não. A predefinição é False.
batchCount | Contagem de lotes para ser utilizado para controlar o número de execução paralela (quando isSequential está definido como false). | Número inteiro (máximo de 50) | Não. A predefinição é 20.
Itens | Uma expressão que devolve uma matriz JSON para ser iterado. | Expressão (que devolve uma matriz JSON) | Sim
Atividades | As atividades a ser executado. | Lista de atividades | Sim

## <a name="parallel-execution"></a>Execução paralela
Se **isSequential** é definido como FALSO, a atividade itera em paralelo com um máximo de 20 iterações simultâneas. Esta definição deve ser utilizada com cuidado. Se estiver a escrever as iterações simultâneas para a mesma pasta, mas para arquivos diferentes, esta abordagem é adequada. Se as iterações simultâneas são escrever simultaneamente no mesmo ficheiro exata, essa abordagem faz com que é muito provável que um erro. 

## <a name="iteration-expression-language"></a>Linguagem de expressão de iteração
Na atividade ForEach, fornecer uma matriz para ser iterado para a propriedade **itens**. " Utilize `@item()` para iterar sobre uma enumeração única na atividade ForEach. Por exemplo, se **itens** é uma matriz: [1, 2, 3], `@item()` devolve 1 na primeira iteração, 2 na segunda iteração e 3 na terceira iteração.

## <a name="iterating-over-a-single-activity"></a>Fazendo a iteração através de uma única atividade
**Cenário:** Copiar a partir do mesmo ficheiro de origem no Blob do Azure para vários ficheiros de destino no Blob do Azure.

### <a name="pipeline-definition"></a>Definição de pipeline

```json
{
    "name": "<MyForEachPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyForEachActivity>",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "true",
                    "items": {
                        "value": "@pipeline().parameters.mySinkDatasetFolderPath",
                        "type": "Expression"
                    },
                    "activities": [
                        {
                            "name": "MyCopyActivity",
                            "type": "Copy",
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": "false"
                                },
                                "sink": {
                                    "type": "BlobSink",
                                    "copyBehavior": "PreserveHierarchy"
                                }
                            },
                            "inputs": [
                                {
                                    "referenceName": "<MyDataset>",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@pipeline().parameters.mySourceDatasetFolderPath"
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "MyDataset",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@item()"
                                    }
                                }
                            ]
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "mySourceDatasetFolderPath": {
                "type": "String"
            },
            "mySinkDatasetFolderPath": {
                "type": "String"
            }
        }
    }
}

```

### <a name="blob-dataset-definition"></a>Definição do conjunto de dados de blob

```json
{  
   "name":"<MyDataset>",
   "properties":{  
      "type":"AzureBlob",
      "typeProperties":{  
         "folderPath":{  
            "value":"@dataset().MyFolderPath",
            "type":"Expression"
         }
      },
      "linkedServiceName":{  
         "referenceName":"StorageLinkedService",
         "type":"LinkedServiceReference"
      },
      "parameters":{  
         "MyFolderPath":{  
            "type":"String"
         }
      }
   }
}

```

### <a name="run-parameter-values"></a>Execute os valores de parâmetro

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>Iterar sobre várias atividades
É possível iterar em várias atividades (por exemplo: atividades de cópia e web) numa atividade ForEach. Neste cenário, recomendamos que abstrair múltiplas atividades num pipeline separado. Em seguida, pode utilizar o [atividade ExecutePipeline](control-flow-execute-pipeline-activity.md) no pipeline com atividade ForEach para invocar o pipeline separado com várias atividades. 


### <a name="syntax"></a>Sintaxe

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "<MyForEachMultipleActivities>"
        "typeProperties": {
          "isSequential": true,
          "items": {
            ...
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "name": "<MyInnerPipeline>"
              "typeProperties": {
                "pipeline": {
                  "referenceName": "<copyHttpPipeline>",
                  "type": "PipelineReference"
                },
                "parameters": {
                  ...
                },
                "waitOnCompletion": true
              }
            }
          ]
        }
      }
    ],
    "parameters": {
      ...
    }
  }
}

```

### <a name="example"></a>Exemplo
**Cenário:** Itere sobre uma InnerPipeline dentro de uma atividade ForEach com a atividade executar Pipeline. Copia o pipeline interno com definições de esquema parametrizadas.

#### <a name="master-pipeline-definition"></a>Definição de Pipeline mestre

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "MyForEachActivity",
        "typeProperties": {
          "isSequential": true,
          "items": {
            "value": "@pipeline().parameters.inputtables",
            "type": "Expression"
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "typeProperties": {
                "pipeline": {
                  "referenceName": "InnerCopyPipeline",
                  "type": "PipelineReference"
                },
                "parameters": {
                  "sourceTableName": {
                    "value": "@item().SourceTable",
                    "type": "Expression"
                  },
                  "sourceTableStructure": {
                    "value": "@item().SourceTableStructure",
                    "type": "Expression"
                  },
                  "sinkTableName": {
                    "value": "@item().DestTable",
                    "type": "Expression"
                  },
                  "sinkTableStructure": {
                    "value": "@item().DestTableStructure",
                    "type": "Expression"
                  }
                },
                "waitOnCompletion": true
              },
              "name": "ExecuteCopyPipeline"
            }
          ]
        }
      }
    ],
    "parameters": {
      "inputtables": {
        "type": "Array"
      }
    }
  }
}

```

#### <a name="inner-pipeline-definition"></a>Definição de pipeline interna

```json
{
  "name": "InnerCopyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            }
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "name": "CopyActivity",
        "inputs": [
          {
            "referenceName": "sqlSourceDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sourceTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sourceTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sqlSinkDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sinkTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sinkTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceTableName": {
        "type": "String"
      },
      "sourceTableStructure": {
        "type": "String"
      },
      "sinkTableName": {
        "type": "String"
      },
      "sinkTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="source-dataset-definition"></a>Definição do conjunto de dados de origem

```json
{
  "name": "sqlSourceDataset",
  "properties": {
    "type": "SqlServerTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "sqlserverLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="sink-dataset-definition"></a>Definição do conjunto de dados de sink

```json
{
  "name": "sqlSinkDataSet",
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "azureSqlLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="master-pipeline-parameters"></a>Parâmetros do pipeline principal
```json
{
    "inputtables": [
        {
            "SourceTable": "department",
            "SourceTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ],
            "DestTable": "department2",
            "DestTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ]
        }
    ]
    
}
```

## <a name="aggregating-outputs"></a>Agregação de saídas

Para resultados de agregação da __foreach__ atividade, use a opção _variáveis_ e _acrescentar variável_ atividade.

Em primeiro lugar, declarar uma `array` _variável_ no pipeline. Em seguida, invocar _variável acrescentar_ atividade dentro de cada um __foreach__ loop. Posteriormente, pode obter a agregação de sua matriz.

## <a name="limitations-and-workarounds"></a>Limitações e soluções alternativas

Aqui estão algumas limitações da atividade ForEach e soluções alternativas sugeridas.

| Limitação | Solução |
|---|---|
| Não é possível aninhar um loop ForEach dentro de outro loop ForEach (ou um loop do Until). | Crie um pipeline de dois níveis em que o pipeline externo com o loop externo de ForEach itera num pipeline interno com o loop aninhado. |
| A atividade ForEach tem um máximo `batchCount` de 50 caracteres para processamento paralelo e um máximo de 100 000 itens. | Crie um pipeline de dois níveis em que o pipeline externo com a atividade ForEach itera através de um pipeline interno. |
| | |

## <a name="next-steps"></a>Passos Seguintes
Consulte outras atividades de fluxo de controle suportadas pelo Data Factory: 

- [Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md)
- [Atividade Obter Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade Web](control-flow-web-activity.md)
