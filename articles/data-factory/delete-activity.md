---
title: Eliminar a atividade na fábrica de dados do Azure | Documentos da Microsoft
description: Saiba como eliminar ficheiros em vários arquivos de ficheiros com a atividade eliminar no Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: fab5d69239c420c394645cef632d119848d0f4c4
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818838"
---
# <a name="delete-activity-in-azure-data-factory"></a>Eliminar a atividade de Azure Data Factory

Pode utilizar a atividade eliminar no Azure Data Factory para eliminar os ficheiros ou pastas a partir do armazenamento no local armazena ou arquivos de armazenamento da cloud. Utilize esta atividade para limpar ou arquivar ficheiros que já não são necessários.

> [!WARNING]
> Não não possível restaurar pastas ou arquivos excluídos. Tenha cuidado ao usar a atividade de eliminação para eliminar ficheiros ou pastas.

## <a name="best-practices"></a>Melhores práticas

Seguem-se algumas recomendações para usar a atividade de eliminação:

-   Cópia de segurança os ficheiros antes de excluí-los com a atividade de eliminação, caso seja necessário para restaurá-los no futuro.

-   Certifique-se de que o Data Factory tem permissões de escrita para eliminar pastas ou ficheiros a partir da loja de armazenamento.

-   Certifique-se de que não está a eliminar ficheiros que estão sendo gravados em simultâneo. 

-   Se pretender eliminar ficheiros ou pasta a partir de um sistema no local, certifique-se de que está a utilizar um runtime de integração autoalojado com uma versão superior a 3.14.

## <a name="supported-data-stores"></a>Armazena os dados suportados

-   [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md)
-   [Armazenamento do Azure Data Lake Ger1](connector-azure-data-lake-store.md)
-   [Geração 2 Lake armazenamento de dados do Azure](connector-azure-data-lake-storage.md)

### <a name="file-system-data-stores"></a>Arquivos de dados do sistema de ficheiros

-   [Sistema de Ficheiros](connector-file-system.md)
-   [FTP](connector-ftp.md)
-   [SFTP](connector-sftp.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)

## <a name="syntax"></a>Sintaxe

```json
{
    "name": "DeleteActivity",
    "type": "Delete",
    "typeProperties": {
        "dataset": {
            "referenceName": "<dataset name>",
            "type": "DatasetReference"
        },
        "recursive": true/false,
        "maxConcurrentConnections": <number>,
        "enableLogging": true/false,
        "logStorageSettings": {
            "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference"
            },
            "path": "<path to save log file>"
        }
    }
}
```

## <a name="type-properties"></a>Propriedades do tipo

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Conjunto de dados | Fornece a referência de conjunto de dados para determinar quais arquivos ou a pasta a eliminar | Sim |
| recursiva | Indica se os ficheiros são eliminado recursivamente das subpastas ou apenas a partir da pasta especificada.  | Não. A predefinição é `false`. |
| maxConcurrentConnections | O número de ligações ao se conectar ao armazenamento de armazenamento em simultâneo para eliminar a pasta ou nos ficheiros.   |  Não. A predefinição é `1`. |
| EnableLogging | Indica se é preciso registe os nomes de pasta ou ficheiro tem sido eliminados. Se for VERDADEIRO, terá de fornecer ainda mais uma conta de armazenamento para guardar o ficheiro de registo, para que pode controlar os comportamentos da atividade de eliminação ao ler o ficheiro de registo. | Não |
| logStorageSettings | Apenas aplicável quando enablelogging = true.<br/><br/>Um grupo de propriedades de armazenamento que pode ser especificado em que pretende guardar o ficheiro de registo que contém os nomes de ficheiro ou pasta que tenham sido excluídos até a atividade de eliminação. | Não |
| linkedServiceName | Apenas aplicável quando enablelogging = true.<br/><br/>Serviço ligado do [armazenamento do Azure](connector-azure-blob-storage.md#linked-service-properties), [Gen1 de armazenamento do Azure Data Lake](connector-azure-data-lake-store.md#linked-service-properties), ou [Gen2 de armazenamento do Azure Data Lake](connector-azure-data-lake-storage.md#linked-service-properties) para armazenar o ficheiro de registo que contém a pasta ou nomes de ficheiro tenha sido excluído até a atividade de eliminação. | Não |
| caminho | Apenas aplicável quando enablelogging = true.<br/><br/>O caminho para guardar o ficheiro de registo na sua conta de armazenamento. Se não fornecer um caminho, o serviço cria um contentor para. | Não |

## <a name="monitoring"></a>Monitorização

Existem dois locais onde pode ver e monitorizar os resultados da atividade de eliminação: 
-   A partir da saída da atividade de eliminação.
-   De ficheiro de registo.

### <a name="sample-output-of-the-delete-activity"></a>Saída de exemplo da atividade de eliminação

```json
{ 
  "datasetName": "AmazonS3",
  "type": "AmazonS3Object",
  "prefix": "test",
  "bucketName": "adf",
  "recursive": true,
  "isWildcardUsed": false,
  "maxConcurrentConnections": 2,  
  "filesDeleted": 4,
  "logPath": "https://sample.blob.core.windows.net/mycontainer/5c698705-a6e2-40bf-911e-e0a927de3f07",
  "effectiveIntegrationRuntime": "MyAzureIR (West Central US)",
  "executionDuration": 650
}
```

### <a name="sample-log-file-of-the-delete-activity"></a>Ficheiro de registo de exemplo da atividade de eliminação

| Name | Categoria | Estado | Erro |
|:--- |:--- |:--- |:--- |
| test1/yyy.json | Ficheiro | Eliminada |  |
| test2/hello789.txt | Ficheiro | Eliminada |  |
| test2/test3/hello000.txt | Ficheiro | Eliminada |  |
| test2/test3/zzz.json | Ficheiro | Eliminada |  |

## <a name="examples-of-using-the-delete-activity"></a>Exemplos de como utilizar a atividade de eliminação

### <a name="delete-specific-folders-or-files"></a>Eliminar ficheiros ou pastas específicas

O arquivo tem a seguinte estrutura de pastas:

Raiz /<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt

Agora está a utilizar a atividade de eliminação para eliminar a pasta ou nos ficheiros pela combinação do valor da propriedade diferente do conjunto de dados e a atividade de eliminação:

| folderPath (a partir do conjunto de dados) | nome de ficheiro (a partir do conjunto de dados) | recursiva (da atividade de eliminação) | Saída |
|:--- |:--- |:--- |:--- |
| Raiz / Folder_A_2 | NULL | Falso | Raiz /<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Raiz / Folder_A_2 | NULL | Verdadeiro | Raiz /<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_A_2 /</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_1 /</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>7.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |
| Raiz / Folder_A_2 | *.txt | Falso | Raiz /<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Raiz / Folder_A_2 | *.txt | Verdadeiro | Raiz /<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1 /<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |

### <a name="periodically-clean-up-the-time-partitioned-folder-or-files"></a>Limpar periodicamente o tempo particionada pasta ou nos ficheiros

Pode criar um pipeline para limpar periodicamente a pasta de particionada de tempo ou ficheiros.  Por exemplo, a estrutura de pastas é semelhante a como: `/mycontainer/2018/12/14/*.csv`.  Pode aproveitar a variável do sistema ADF do acionador de agenda para identificar qual pasta ou nos ficheiros devem ser eliminados em cada execução de pipeline. 

#### <a name="sample-pipeline"></a>Pipeline de exemplo

```json
{
    "name": "cleanup_time_partitioned_folder",
    "properties": {
        "activities": [
            {
                "name": "DeleteOneFolder",
                "type": "Delete",
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "typeProperties": {
                    "dataset": {
                        "referenceName": "PartitionedFolder",
                        "type": "DatasetReference",
                        "parameters": {
                            "TriggerTime": {
                                "value": "@formatDateTime(pipeline().parameters.TriggerTime, 'yyyy/MM/dd')",
                                "type": "Expression"
                            }
                        }
                    },
                    "recursive": true,
                    "logStorageSettings": {
                        "linkedServiceName": {
                            "referenceName": "BloblinkedService",
                            "type": "LinkedServiceReference"
                        },
                        "path": "mycontainer/log"
                    },
                    "enableLogging": true
                }
            }
        ],
        "parameters": {
            "TriggerTime": {
                "type": "String"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

#### <a name="sample-dataset"></a>Conjunto de dados de exemplo

```json
{
    "name": "PartitionedFolder",
    "properties": {
        "linkedServiceName": {
            "referenceName": "BloblinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "TriggerTime": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": {
                "value": "@concat('mycontainer/',dataset().TriggerTime)",
                "type": "Expression"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/datasets"
}
```

#### <a name="sample-trigger"></a>Acionador de exemplo

```json
{
    "name": "DailyTrigger",
    "properties": {
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "cleanup_time_partitioned_folder",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "TriggerTime": "@trigger().scheduledTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Day",
                "interval": 1,
                "startTime": "2018-12-13T00:00:00.000Z",
                "timeZone": "UTC",
                "schedule": {
                    "minutes": [
                        59
                    ],
                    "hours": [
                        23
                    ]
                }
            }
        }
    }
}
```

### <a name="clean-up-the-expired-files-that-were-last-modified-before-201811"></a>Limpar os ficheiros expirados, que foram modificada pela última vez antes de 2018.1.1

Pode criar um pipeline para limpar os arquivos antigos ou expirados ao tirar partido do filtro de atributo de ficheiro: "LastModified" no conjunto de dados.  

#### <a name="sample-pipeline"></a>Pipeline de exemplo

```json
{
    "name": "CleanupExpiredFiles",
    "properties": {
        "activities": [
            {
                "name": "DeleteFilebyLastModified",
                "type": "Delete",
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "typeProperties": {
                    "dataset": {
                        "referenceName": "BlobFilesLastModifiedBefore201811",
                        "type": "DatasetReference"
                    },
                    "recursive": true,
                    "logStorageSettings": {
                        "linkedServiceName": {
                            "referenceName": "BloblinkedService",
                            "type": "LinkedServiceReference"
                        },
                        "path": "mycontainer/log"
                    },
                    "enableLogging": true
                }
            }
        ]
    }
}
```

#### <a name="sample-dataset"></a>Conjunto de dados de exemplo

```json
{
    "name": "BlobFilesLastModifiedBefore201811",
    "properties": {
        "linkedServiceName": {
            "referenceName": "BloblinkedService",
            "type": "LinkedServiceReference"
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": "",
            "folderPath": "mycontainer",
            "modifiedDatetimeEnd": "2018-01-01T00:00:00.000Z"
        }
    }
}
```

### <a name="move-files-by-chaining-the-copy-activity-and-the-delete-activity"></a>Mover ficheiros por meio do encadeamento a atividade de cópia e a atividade de eliminação

Pode mover um ficheiro com uma atividade de cópia para copiar um ficheiro e, em seguida, uma atividade de eliminação para eliminar um ficheiro num pipeline.  Quando deseja mover vários ficheiros, pode utilizar a atividade GetMetadata + a atividade de filtro + a atividade Foreach + a atividade de cópia + eliminar a atividade, como no exemplo a seguir:

> [!NOTE]
> Se pretender mover toda a pasta definindo um conjunto de dados que contém apenas um caminho de pasta e, em seguida, utilizar uma atividade de cópia e uma atividade Delete para fazer referência ao mesmo conjunto de dados que representa uma pasta, precisa ter muito cuidado. É porque tem para se certificar de que não haverá novos ficheiros que estão a chegar para a pasta entre a operação de cópia e a eliminar a operação.  Se existirem novos ficheiros que estão a chegar na pasta neste momento, quando sua atividade de cópia acabou de concluir a tarefa de cópia, a atividade de eliminação não foi stared, mas, é possível que a atividade de eliminação irá eliminar este novo ficheiro que são recebido que não foi copiado para o destinati no ainda ao eliminar a pasta inteira. 

#### <a name="sample-pipeline"></a>Pipeline de exemplo

```json
{
    "name": "MoveFiles",
    "properties": {
        "activities": [
            {
                "name": "GetFileList",
                "type": "GetMetadata",
                "typeProperties": {
                    "dataset": {
                        "referenceName": "OneSourceFolder",
                        "type": "DatasetReference"
                    },
                    "fieldList": [
                        "childItems"
                    ]
                }
            },
            {
                "name": "FilterFiles",
                "type": "Filter",
                "dependsOn": [
                    {
                        "activity": "GetFileList",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "typeProperties": {
                    "items": {
                        "value": "@activity('GetFileList').output.childItems",
                        "type": "Expression"
                    },
                    "condition": {
                        "value": "@equals(item().type, 'File')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "ForEachFile",
                "type": "ForEach",
                "dependsOn": [
                    {
                        "activity": "FilterFiles",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "typeProperties": {
                    "items": {
                        "value": "@activity('FilterFiles').output.value",
                        "type": "Expression"
                    },
                    "batchCount": 20,
                    "activities": [
                        {
                            "name": "CopyAFile",
                            "type": "Copy",
                            "policy": {
                                "timeout": "7.00:00:00",
                                "retry": 0,
                                "retryIntervalInSeconds": 30,
                                "secureOutput": false,
                                "secureInput": false
                            },
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": false
                                },
                                "sink": {
                                    "type": "BlobSink"
                                },
                                "enableStaging": false,
                                "dataIntegrationUnits": 0
                            },
                            "inputs": [
                                {
                                    "referenceName": "OneSourceFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "path": "myFolder",
                                        "filename": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "OneDestinationFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "DestinationFileName": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                }
                            ]
                        },
                        {
                            "name": "DeleteAFile",
                            "type": "Delete",
                            "dependsOn": [
                                {
                                    "activity": "CopyAFile",
                                    "dependencyConditions": [
                                        "Succeeded"
                                    ]
                                }
                            ],
                            "policy": {
                                "timeout": "7.00:00:00",
                                "retry": 0,
                                "retryIntervalInSeconds": 30,
                                "secureOutput": false,
                                "secureInput": false
                            },
                            "typeProperties": {
                                "dataset": {
                                    "referenceName": "OneSourceFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "path": "myFolder",
                                        "filename": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                },
                                "logStorageSettings": {
                                    "linkedServiceName": {
                                        "referenceName": "BloblinkedService",
                                        "type": "LinkedServiceReference"
                                    },
                                    "path": "Container/log"
                                },
                                "enableLogging": true
                            }
                        }
                    ]
                }
            }
        ]
    }
}
```

#### <a name="sample-datasets"></a>Conjuntos de dados de exemplo

O conjunto de dados utilizado pela atividade GetMetadata para enumerar a lista de ficheiros.

```json
{
    "name": "OneSourceFolder",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": "",
            "folderPath": "myFolder"
        }
    }
}
```

Conjunto de dados para a origem de dados utilizado pela atividade de cópia e a atividade de eliminação.

```json
{
    "name": "OneSourceFile",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            },
            "filename": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": {
                "value": "@dataset().filename",
                "type": "Expression"
            },
            "folderPath": {
                "value": "@{dataset().path}",
                "type": "Expression"
            }
        }
    }
}
```

Conjunto de dados para o destino dos dados utilizado pela atividade de cópia.

```json
{
    "name": "OneDestinationFile",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "DestinationFileName": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": {
                "value": "@dataset().DestinationFileName",
                "type": "Expression"
            },
            "folderPath": "mycontainer/dest"
        }
    }
}
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre como mover ficheiros no Azure Data Factory.

-   [Ferramenta copiar dados no Azure Data Factory](copy-data-tool.md)