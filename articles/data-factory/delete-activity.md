---
title: Atividade de Eliminação no Azure Data Factory
description: Saiba como eliminar ficheiros em várias lojas de ficheiros com a Atividade de Eliminar na Fábrica de Dados Azure.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f265cdc955becd53ae7ba61ad827b2be69b92907
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618271"
---
# <a name="delete-activity-in-azure-data-factory"></a>Atividade de Eliminação no Azure Data Factory

Pode utilizar a Atividade de Exclusão na Fábrica de Dados Azure para eliminar ficheiros ou pastas de lojas de armazenamento no local ou lojas de armazenamento em nuvem. Utilize esta atividade para limpar ou arquivar ficheiros quando já não forem necessários.

> [!WARNING]
> Os ficheiros ou pastas eliminados não podem ser restaurados (a menos que o armazenamento tenha sido ativado por eliminação suave). Tenha cuidado ao utilizar a ação Eliminar para eliminar ficheiros ou pastas.

## <a name="best-practices"></a>Melhores práticas

Aqui ficam algumas recomendações para a utilização da atividade Delete:

-   Faça cópias de tempo dos seus ficheiros antes de os eliminar com a atividade Delete, caso necessite de os restaurar no futuro.

-   Certifique-se de que a Data Factory tem permissões de escrita para eliminar pastas ou ficheiros do armazém.

-   Certifique-se de que não está a apagar ficheiros que estão a ser escritos ao mesmo tempo. 

-   Se pretender eliminar ficheiros ou pastas de um sistema no local, certifique-se de que está a utilizar um tempo de funcionação de integração auto-hospedado com uma versão superior a 3.14.

## <a name="supported-data-stores"></a>Arquivos de dados suportados

-   [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md)
-   [Armazenamento do Azure Data Lake Ger1](connector-azure-data-lake-store.md)
-   [Armazenamento do Azure Data Lake Ger2](connector-azure-data-lake-storage.md)
-   [Armazenamento de Ficheiros do Azure](connector-azure-file-storage.md)

### <a name="file-system-data-stores"></a>Lojas de dados do sistema de ficheiros

-   [Sistema de Ficheiros](connector-file-system.md)
-   [FTP](connector-ftp.md)
-   [SFTP](connector-sftp.md)
-   [Amazónia S3](connector-amazon-simple-storage-service.md)
-   [Google Cloud Storage](connector-google-cloud-storage.md)

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

## <a name="type-properties"></a>Propriedades de tipo

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| conjunto de dados | Fornece a referência do conjunto de dados para determinar quais ficheiros ou pasta a eliminar | Sim |
| recursivo | Indica se os ficheiros são eliminados recursivamente das subpastas ou apenas da pasta especificada.  | Não. A predefinição é `false`. |
| maxConcurrentConnections | O número das ligações para ligar simultaneamente à loja de armazenamento para apagar pasta ou ficheiros.   |  Não. A predefinição é `1`. |
| habilitação de exploração | Indica se precisa de gravar a pasta ou os nomes de ficheiros que tenham sido eliminados. Se for verdade, é necessário fornecer ainda mais uma conta de armazenamento para guardar o ficheiro de registo, para que possa acompanhar os comportamentos da atividade do Delete através da leitura do ficheiro de registo. | Não |
| logStorageSettings | Só aplicável quando ativar = verdadeiro.<br/><br/>Um grupo de propriedades de armazenamento que podem ser especificadas onde pretende guardar o ficheiro de registo contendo a pasta ou os nomes de ficheiros que tenham sido eliminados pela atividade Delete. | Não |
| linkedServiceName | Só aplicável quando ativar = verdadeiro.<br/><br/>O serviço ligado ao [Armazenamento Azure,](connector-azure-blob-storage.md#linked-service-properties) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#linked-service-properties), ou [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) para armazenar o ficheiro de registo que contém a pasta ou nomes de ficheiros que tenham sido eliminados pela atividade Delete. Esteja ciente de que deve ser configurado com o mesmo tipo de Tempo de Funcionação de Integração a partir do utilizado por eliminar a atividade para eliminar ficheiros. | Não |
| path | Só aplicável quando ativar = verdadeiro.<br/><br/>O caminho para guardar o ficheiro de registo na sua conta de armazenamento. Se não fornecer um caminho, o serviço cria um recipiente para si. | Não |

## <a name="monitoring"></a>Monitorização

Existem dois locais onde pode ver e monitorizar os resultados da atividade Desativação: 
-   A partir da saída da atividade Delete.
-   Do ficheiro de registo.

### <a name="sample-output-of-the-delete-activity"></a>Saída da amostra da atividade Delete

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

### <a name="sample-log-file-of-the-delete-activity"></a>Arquivo de registo de amostras da atividade de Eliminar

| Nome | Categoria | Estado | Erro |
|:--- |:--- |:--- |:--- |
| test1/yyy.json | Ficheiro | Eliminado |  |
| test2/hello789.txt | Ficheiro | Eliminado |  |
| test2/test3/hello000.txt | Ficheiro | Eliminado |  |
| test2/test3/zzz.json | Ficheiro | Eliminado |  |

## <a name="examples-of-using-the-delete-activity"></a>Exemplos de utilização da atividade Delete

### <a name="delete-specific-folders-or-files"></a>Eliminar pastas ou ficheiros específicos

A loja tem a seguinte estrutura de pasta:

Raiz/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt

Agora está a utilizar a atividade Delete para eliminar pastas ou ficheiros pela combinação de diferentes valores de propriedade do conjunto de dados e da atividade de Eliminar:

| folderPath (a partir de dataset) | nome de ficheiro (a partir de dataset) | recursivo (da atividade Eliminar) | Saída |
|:--- |:--- |:--- |:--- |
| Raiz/ Folder_A_2 | NULL | Falso | Raiz/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Raiz/ Folder_A_2 | NULL | Verdadeiro | Raiz/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_A_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_1/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>7.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |
| Raiz/ Folder_A_2 | *.txt | Falso | Raiz/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Raiz/ Folder_A_2 | *.txt | Verdadeiro | Raiz/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |

### <a name="periodically-clean-up-the-time-partitioned-folder-or-files"></a>Limpe periodicamente a pasta ou ficheiros com divisórias de tempo

Pode criar um pipeline para limpar periodicamente a pasta ou ficheiros divididos pelo tempo.  Por exemplo, a estrutura da `/mycontainer/2018/12/14/*.csv`pasta é semelhante a: .  Pode alavancar a variável do sistema ADF a partir do gatilho de agenda para identificar qual a pasta ou ficheiros que devem ser eliminados em cada execução do pipeline. 

#### <a name="sample-pipeline"></a>Gasoduto de amostra

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

#### <a name="sample-dataset"></a>Conjunto de dados da amostra

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

#### <a name="sample-trigger"></a>Gatilho da amostra

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

### <a name="clean-up-the-expired-files-that-were-last-modified-before-201811"></a>Limpe os ficheiros expirados que foram modificados pela última vez antes de 2018.1.1

Pode criar um pipeline para limpar os ficheiros antigos ou expirados, aproveitando o filtro de atributo supor ficheiros: "LastModificadod" no conjunto de dados.  

#### <a name="sample-pipeline"></a>Gasoduto de amostra

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

#### <a name="sample-dataset"></a>Conjunto de dados da amostra

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
            "fileName": "*",
            "folderPath": "mycontainer",
            "modifiedDatetimeEnd": "2018-01-01T00:00:00.000Z"
        }
    }
}
```

### <a name="move-files-by-chaining-the-copy-activity-and-the-delete-activity"></a>Mover ficheiros acorrentando a atividade da Cópia e a atividade de Eliminar

Pode mover um ficheiro utilizando uma atividade de cópia para copiar um ficheiro e, em seguida, eliminar uma atividade para eliminar um ficheiro num pipeline.  Quando pretender mover vários ficheiros, pode utilizar a atividade GetMetadata + Atividade de filtragem + Atividade foreach + Atividade de cópia + Eliminar a atividade como na seguinte amostra:

> [!NOTE]
> Se pretender mover toda a pasta definindo um conjunto de dados que contenha apenas um caminho de pasta e, em seguida, utilizando uma atividade de cópia e uma atividade de Eliminar para referência ao mesmo conjunto de dados que representa uma pasta, tem de ter muito cuidado. É porque tem de se certificar de que não haverá novos ficheiros que chegam à pasta entre a operação de cópia e a eliminação da operação.  Se houver novos ficheiros que chegam à pasta no momento em que a sua atividade de cópia acabou de completar a função de cópia, mas a atividade de Eliminar não foi encarada, é possível que a atividade do Delete apague este novo ficheiro de chegada que ainda não foi copiado para o destino, eliminando toda a pasta. 

#### <a name="sample-pipeline"></a>Gasoduto de amostra

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

Conjunto de dados utilizado pela atividade getMetadata para enumerar a lista de ficheiros.

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

Conjunto de dados para fonte de dados utilizado pela atividade de cópia e pela atividade de Eliminar.

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

Conjunto de dados para destino de dados utilizado pela atividade de cópia.

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

Também pode obter o modelo para mover ficheiros a partir [daqui](solution-template-move-files.md).

## <a name="known-limitation"></a>Limitação conhecida

-   Eliminar atividade não suporta a eliminação da lista de pastas descritas por wildcard.

-   Quando utilizar o filtro de atributo de ficheiro: modificadoDatetimeStart e modificadoDatetimeEnd para selecionar ficheiros a eliminar, certifique-se de definir "fileName": "*" no conjunto de dados.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a movimentação de ficheiros na Azure Data Factory.

-   [Ferramenta Copiar Dados no Azure Data Factory](copy-data-tool.md)
