---
title: Atividade forEach na Azure Data Factory
description: O For Each Activity define um fluxo de controlo repetitivo no seu oleoduto. É utilizado para iterar sobre uma coleção e executar atividades especificadas.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: a0c3a3cbaa71d627f54550cf92c067afbb1eb3f0
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786214"
---
# <a name="foreach-activity-in-azure-data-factory"></a>Atividade forEach na Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Atividade ForEach define um fluxo de controlo repetitivo no seu oleoduto. Esta atividade é utilizada para iterar uma coleção e executa atividades especificadas em ciclo. A implementação de ciclo desta atividade é semelhante à estrutura de ciclo Foreach nas linguagens de programação.

## <a name="syntax"></a>Syntax
As propriedades são descritas mais tarde neste artigo. A propriedade dos itens é a coleção e cada item da coleção é referido utilizando o `@item()` como indicado na seguinte sintaxe:  

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

## <a name="type-properties"></a>Tipo de propriedades

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
name | Nome da atividade para cada atividade. | String | Sim
tipo | Deve ser definido para **ForEach** | String | Sim
isSequential | Especifica se o laço deve ser executado sequencialmente ou em paralelo.  No máximo 20 iterações em loop podem ser executadas ao mesmo tempo em paralelo). Por exemplo, se tiver uma atividade ForEach a iterar sobre uma atividade de cópia com 10 conjuntos de dados de origem e pia diferentes com conjuntos de dados **isSequential** para Falso, todas as cópias são executadas de uma só vez. O padrão é falso. <br/><br/> Se "isSequential" estiver definido para Falso, certifique-se de que existe uma configuração correta para executar vários executáveis. Caso contrário, esta propriedade deve ser usada com cuidado para evitar conflitos de escrita incorrendo. Para mais informações, consulte a secção [execução paralela.](#parallel-execution) | Booleano | N.º O padrão é falso.
lotCount | Contagem de lotes a utilizar para controlar o número de execuções paralelas (quando o isequential é definido como falso). Este é o limite superior de concência, mas a atividade para cada atividade nem sempre será executada neste número | Inteiro (máximo 50) | N.º O padrão é 20.
Itens | Uma expressão que devolve um JSON Array para ser iterado. | Expressão (que devolve uma Matriz JSON) | Sim
Atividades | As atividades a executar. | Lista de Actividades | Sim

## <a name="parallel-execution"></a>Execução paralela
Se **o isequential** for definido como falso, a atividade itera em paralelo com um máximo de 20 iterações simultâneas. Esta regulação deve ser utilizada com cuidado. Se as iterações simultâneas estiverem a escrever para a mesma pasta, mas para diferentes ficheiros, esta abordagem está bem. Se as iterações simultâneas estiverem a escrever simultaneamente para o mesmo ficheiro, esta abordagem provavelmente causa um erro. 

## <a name="iteration-expression-language"></a>Linguagem de expressão de iteração
Na atividade ForEach, forneça uma matriz para ser iterada para os **itens de** propriedade ." Use `@item()` para iterar sobre uma única enumeração na atividade forEach. Por exemplo, se **os itens** forem uma matriz: [1, 2, 3], `@item()` devolve 1 na primeira iteração, 2 na segunda iteração e 3 na terceira iteração.

## <a name="iterating-over-a-single-activity"></a>Itering sobre uma única atividade
**Cenário:** Copie do mesmo ficheiro de origem em Azure Blob para vários ficheiros de destino em Azure Blob.

### <a name="pipeline-definition"></a>Definição de gasoduto

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

### <a name="blob-dataset-definition"></a>Definição de conjunto de dados de bolhas

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

## <a name="iterate-over-multiple-activities"></a>Iterate sobre múltiplas atividades
É possível iterar sobre múltiplas atividades (por exemplo: cópia e atividades web) numa atividade forEach. Neste cenário, recomendamos que abstrate múltiplas atividades num oleoduto separado. Em seguida, pode utilizar a [atividade ExecutPipeline](control-flow-execute-pipeline-activity.md) no oleoduto com a atividade ForEach para invocar o gasoduto separado com múltiplas atividades. 


### <a name="syntax"></a>Syntax

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
**Cenário:** Iterate sobre um InnerPipeline dentro de uma atividade ForEach com atividade de Pipeline Execut. O gasoduto interno copia com definições de esquema parametrizadas.

#### <a name="master-pipeline-definition"></a>Definição de Pipeline Master

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

#### <a name="sink-dataset-definition"></a>Definição de conjunto de dados de sumidouro

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

#### <a name="master-pipeline-parameters"></a>Parâmetros do gasoduto principal
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

Para agregar saídas de atividade __de foreach,__ utilize _variáveis_ e atividade _variável de apêndice._

Primeiro, declare uma `array` _variável_ no oleoduto. Em seguida, invoque a atividade _variável do Apêndice_ dentro de cada circuito __de foreach.__ Posteriormente, pode recuperar a agregação da sua matriz.

## <a name="limitations-and-workarounds"></a>Limitações e soluções alternativas

Aqui estão algumas limitações da atividade forEach e soluções alternativas sugeridas.

| Limitação | Solução |
|---|---|
| Não é possível aninhar um laço ForEach dentro de outro laço ForEach (ou um loop Until). | Desenhe um gasoduto de dois níveis onde o gasoduto exterior com o laço forEach exterior itera sobre um gasoduto interior com o laço aninhado. |
| A atividade ForEach tem um máximo `batchCount` de 50 para processamento paralelo, e um máximo de 100.000 itens. | Desenhe um oleoduto de dois níveis onde o gasoduto exterior com a atividade ForEach itera sobre um gasoduto interior. |
| O SetVariable não pode ser usado dentro de uma atividade ForEach que funciona em paralelo, uma vez que as variáveis são globais a todo o oleoduto, não são telescópios para um ForEach ou qualquer outra atividade. | Considere utilizar o ForEach sequencial ou utilizar o Pipeline executar dentro do ForEach (Variável/Parâmetro manuseado no pipeline infantil).|
| | |

## <a name="next-steps"></a>Passos seguintes
Consulte outras atividades de fluxo de controlo suportadas pela Data Factory: 

- [Executar atividade de gasoduto](control-flow-execute-pipeline-activity.md)
- [Obter Atividade de Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade Web](control-flow-web-activity.md)
