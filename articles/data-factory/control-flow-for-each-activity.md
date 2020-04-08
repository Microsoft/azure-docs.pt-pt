---
title: Atividade para Cada Pessoa na Fábrica de Dados Azure
description: O For Each Activity define um fluxo de controlo repetitivo no seu pipeline. É utilizado para iterar sobre uma coleção e executar atividades especificadas.
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
ms.openlocfilehash: 71528385563b29ce70edf396434be0174beac105
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804848"
---
# <a name="foreach-activity-in-azure-data-factory"></a>Atividade para Cada Pessoa na Fábrica de Dados Azure
A Atividade ForEach define um fluxo de controlo repetitivo no seu pipeline. Esta atividade é utilizada para iterar uma coleção e executa atividades especificadas em ciclo. A implementação de ciclo desta atividade é semelhante à estrutura de ciclo Foreach nas linguagens de programação.

## <a name="syntax"></a>Sintaxe
As propriedades são descritas mais tarde neste artigo. A propriedade dos artigos é a coleção e cada item da coleção é referido utilizando o `@item()` tal como mostrado na seguinte sintaxe:  

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

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
nome | Nome da atividade para cada um. | String | Sim
tipo | Deve ser definido para **ForEach** | String | Sim
isSequential | Especifica se o laço deve ser executado sequencialmente ou em paralelo.  No máximo 20 iterações em loop podem ser executadas de uma só vez em paralelo). Por exemplo, se tiver uma atividade ForEach iterando sobre uma atividade de cópia com 10 conjuntos de dados de origem e pia diferentes com conjunto **isSequential** definido para Falso, todas as cópias são executadas de uma só vez. O padrão é falso. <br/><br/> Se "isSequential" estiver definido para Falso, certifique-se de que existe uma configuração correta para executar vários executáveis. Caso contrário, esta propriedade deve ser usada com cuidado para evitar incorrer em conflitos de escrita. Para mais informações, consulte a secção [de execução paralela.](#parallel-execution) | Booleano | Não. O padrão é falso.
loteCount | Contagem de lotes a utilizar para controlar o número de execução paralela (quando o isSequential for definido como falso). Este é o limite de conmoeda superior, mas a atividade para cada um nem sempre executará neste número | Inteiro (máximo 50) | Não. O padrão é de 20.
Itens | Uma expressão que devolve um Matriz JSON para ser iterado. | Expressão (que devolve um Matriz JSON) | Sim
Atividades | As atividades a serem executadas. | Lista de Actividades | Sim

## <a name="parallel-execution"></a>Execução paralela
Se **o isSequential for** definido como falso, a atividade iterates em paralelo com um máximo de 20 iterações simultâneas. Esta definição deve ser utilizada com cuidado. Se as iterações simultâneas estiverem a escrever para a mesma pasta, mas para ficheiros diferentes, esta abordagem está bem. Se as iterações simultâneas estiverem a escrever simultaneamente para o mesmo ficheiro, esta abordagem provavelmente causa um erro. 

## <a name="iteration-expression-language"></a>Linguagem de expressão de iteração
Na atividade ForEach, forneça uma matriz para ser iterado para os **itens**de propriedade ." Utilize `@item()` para iterar sobre uma única enumeração na atividade ForEach. Por exemplo, se os **itens** forem uma matriz: `@item()` [1, 2, 3], devolve 1 na primeira iteração, 2 na segunda iteração e 3 na terceira iteração.

## <a name="iterating-over-a-single-activity"></a>Iteração sobre uma única atividade
**Cenário:** Cópia do mesmo ficheiro de origem em Azure Blob para vários ficheiros de destino em Azure Blob.

### <a name="pipeline-definition"></a>Definição do gasoduto

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

### <a name="blob-dataset-definition"></a>Definição de conjunto de dados blob

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

### <a name="run-parameter-values"></a>Executar valores de parâmetros

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>Iterado sobre múltiplas atividades
É possível iterar sobre múltiplas atividades (por exemplo: copiar e atividades web) numa atividade ForEach. Neste cenário, recomendamos que resumis várias atividades num oleoduto separado. Em seguida, pode utilizar a [atividade ExecutePipeline](control-flow-execute-pipeline-activity.md) no pipeline com a atividade ForEach para invocar o pipeline separado com múltiplas atividades. 


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
**Cenário:** Iterado sobre um InnerPipeline dentro de uma atividade ForEach com executar atividade pipeline. O gasoduto interno copia com definições de esquema smetrómeti.

#### <a name="master-pipeline-definition"></a>Definição master pipeline

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

#### <a name="inner-pipeline-definition"></a>Definição de gasoduto interior

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

#### <a name="source-dataset-definition"></a>Definição de conjunto de dados de origem

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

#### <a name="sink-dataset-definition"></a>Definição de conjunto de dados de afundar

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

## <a name="aggregating-outputs"></a>Agregação de saídas

Para agregar as saídas de __atividade forcada,__ utilize _variáveis_ e _atividade variável anexada._

Primeiro, declare `array` uma _variável_ no oleoduto. Em seguida, invoque a atividade _do Apêndice Variável_ dentro de cada ciclo __foreach.__ Posteriormente, pode recuperar a agregação da sua matriz.

## <a name="limitations-and-workarounds"></a>Limitações e suposições

Aqui estão algumas limitações da atividade ForEach e suposições sugeridas.

| Limitação | Solução |
|---|---|
| Não é possível nidificar um laço ForEach dentro de outro loop ForEach (ou um loop Until). | Desenhe um gasoduto de dois níveis onde o gasoduto exterior com o circuito fora ForEach iterates sobre um oleoduto interno com o laço aninhado. |
| A atividade ForEach `batchCount` tem um máximo de 50 para processamento paralelo, e um máximo de 100.000 itens. | Desenhe um gasoduto de dois níveis onde o gasoduto exterior com a atividade ForEach iterates sobre um gasoduto interno. |
| | |

## <a name="next-steps"></a>Passos seguintes
Consulte outras atividades de fluxo de controlo suportadas pela Data Factory: 

- [Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md)
- [Atividade Obter Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade Web](control-flow-web-activity.md)
