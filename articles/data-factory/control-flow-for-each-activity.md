---
title: Atividade ForEach no Azure Data Factory | Microsoft Docs
description: A atividade for each define um fluxo de controle repetitivo em seu pipeline. Ele é usado para iterar em uma coleção e executar atividades especificadas.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: 319f4e722184ce840d43b8f23e61711851a6d4a0
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142469"
---
# <a name="foreach-activity-in-azure-data-factory"></a>Atividade ForEach no Azure Data Factory
A atividade ForEach define um fluxo de controle repetitivo em seu pipeline. Esta atividade é utilizada para iterar uma coleção e executa atividades especificadas em ciclo. A implementação de ciclo desta atividade é semelhante à estrutura de ciclo Foreach nas linguagens de programação.

## <a name="syntax"></a>Sintaxe
As propriedades são descritas posteriormente neste artigo. A propriedade Items é a coleção e cada item na coleção é referenciado usando o `@item()` , conforme mostrado na seguinte sintaxe:  

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

Propriedade | Descrição | Valores permitidos | Requerido
-------- | ----------- | -------------- | --------
name | Nome da atividade for-each. | Cadeia | Sim
type | Deve ser definido como **foreach** | Cadeia | Sim
isSequential | Especifica se o loop deve ser executado em sequência ou em paralelo.  O máximo de 20 iterações de loop pode ser executado de uma vez em paralelo). Por exemplo, se você tiver uma atividade ForEach Iterando em uma atividade de cópia com 10 conjuntos de fontes de origem e de coletor diferentes com issequencial definido como false, todas as cópias serão executadas ao mesmo tempo. O padrão é false. <br/><br/> Se "issequential" for definido como false, verifique se há uma configuração correta para executar vários executáveis. Caso contrário, essa propriedade deve ser usada com cuidado para evitar incorrer em conflitos de gravação. Para obter mais informações, consulte a seção [execução paralela](#parallel-execution) . | Booleano | Não. O padrão é false.
batchCount | Contagem de lote a ser usada para controlar o número de execuções paralelas (quando issequencial é definido como false). | Inteiro (máximo de 50) | Não. O padrão é 20.
Itens | Uma expressão que retorna uma matriz JSON a ser iterada. | Expressão (que retorna uma matriz JSON) | Sim
Atividades | As atividades a serem executadas. | Lista de atividades | Sim

## <a name="parallel-execution"></a>Execução paralela
Se issequencial for definido como false, a atividade será iterada em paralelo com um máximo de 20 iterações simultâneas. Essa configuração deve ser usada com cautela. Se as iterações simultâneas estiverem gravando na mesma pasta, mas em arquivos diferentes, essa abordagem será boa. Se as iterações simultâneas estiverem gravando simultaneamente no mesmo arquivo exato, essa abordagem provavelmente causará um erro. 

## <a name="iteration-expression-language"></a>Linguagem de expressão de iteração
Na atividade ForEach, forneça uma matriz a ser iterada para os **itens**de propriedade. " Use `@item()` para iterar em uma única enumeração na atividade ForEach. Por exemplo, se **itens** for uma matriz: [1, 2, 3], `@item()` retornará 1 na primeira iteração, 2 na segunda iteração e 3 na terceira iteração.

## <a name="iterating-over-a-single-activity"></a>Iterando em uma única atividade
**Cenário** Copie do mesmo arquivo de origem no blob do Azure para vários arquivos de destino no blob do Azure.

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

### <a name="blob-dataset-definition"></a>Definição do conjunto de conjuntos de BLOB

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

### <a name="run-parameter-values"></a>Valores de parâmetros de execução

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>Iterar em várias atividades
É possível iterar em várias atividades (por exemplo: cópia e atividades da Web) em uma atividade ForEach. Nesse cenário, é recomendável que você abstrair várias atividades em um pipeline separado. Em seguida, você pode usar a [atividade ExecutePipeline](control-flow-execute-pipeline-activity.md) no pipeline com a atividade ForEach para invocar o pipeline separado com várias atividades. 


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
**Cenário** Iterar em um InnerPipeline dentro de uma atividade ForEach com a atividade executar pipeline. O pipeline interno é copiado com definições de esquema parametrizadas.

#### <a name="master-pipeline-definition"></a>Definição de pipeline mestre

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

#### <a name="inner-pipeline-definition"></a>Definição de pipeline interno

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

#### <a name="source-dataset-definition"></a>Definição do conjunto de fonte de origem

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

#### <a name="sink-dataset-definition"></a>Definição do conjunto de banco de coleta

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

#### <a name="master-pipeline-parameters"></a>Parâmetros de pipeline mestre
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

## <a name="aggregating-outputs"></a>Agregando saídas

Para agregar saídas da atividade __foreach__ , utilize _variáveis_ e _acrescente atividade Variable_ .

Primeiro, declare uma `array` _variável_ no pipeline. Em seguida, invoque a atividade de _acréscimo de variável_ dentro de cada loop __foreach__ . Subsequentemente, você pode recuperar a agregação de sua matriz.

## <a name="limitations-and-workarounds"></a>Limitações e soluções alternativas

Aqui estão algumas limitações da atividade ForEach e soluções alternativas sugeridas.

| Limitação | Solução |
|---|---|
| Não é possível aninhar um loop ForEach dentro de outro loop ForEach (ou um loop Until). | Projete um pipeline de dois níveis em que o pipeline externo com o loop ForEach externo itera em um pipeline interno com o loop aninhado. |
| A atividade ForEach tem um máximo `batchCount` de 50 para processamento paralelo e um máximo de 100.000 itens. | Projete um pipeline de dois níveis em que o pipeline externo com a atividade ForEach itera em um pipeline interno. |
| | |

## <a name="next-steps"></a>Passos Seguintes
Consulte outras atividades de fluxo de controle com suporte pelo Data Factory: 

- [Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md)
- [Atividade Obter Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
