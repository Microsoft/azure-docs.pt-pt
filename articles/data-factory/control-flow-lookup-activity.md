---
title: Atividade de procura na Fábrica de Dados Azure
description: Aprenda a usar a atividade de Lookup para procurar um valor a partir de uma fonte externa. Esta saída pode ser ainda referenciada através de atividades de sucesso.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 5f46e2871aa0017f0a4b33df04a8ae9058c59e17
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385477"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Atividade de procura na Fábrica de Dados Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A atividade de procura pode recuperar um conjunto de dados de qualquer uma das fontes de dados suportadas pela Azure Data Factory. Utilize-o no seguinte cenário:
- Determinar dinamicamente quais os objetos a operar numa atividade subsequente, em vez de codificar duramente o nome do objeto. Alguns exemplos de objetos são ficheiros e tabelas.

A atividade de procura lê e devolve o conteúdo de um ficheiro ou tabela de configuração. Também devolve o resultado da execução de uma consulta ou procedimento armazenado. A saída da atividade de Lookup pode ser usada numa atividade de cópia ou transformação subsequente se for um valor singleton. A saída pode ser usada numa atividade ForEach se for uma variedade de atributos.

## <a name="supported-capabilities"></a>Capacidades suportadas

As seguintes fontes de dados são suportadas para a atividade de Procura. 

A atividade de Lookup pode voltar até 5000 linhas; se o conjunto de resultados contiver mais registos, as primeiras 5000 linhas serão devolvidas. A produção da atividade do Lookup suporta até cerca de 4 MB de tamanho, a atividade falhará se o tamanho exceder o limite. Atualmente, a duração mais longa para a atividade de Lookup antes do intervalo é de 24 horas.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Syntax

```json
{
    "name":"LookupActivity",
    "type":"Lookup",
    "typeProperties":{
        "source":{
            "type":"<source type>"
        },
        "dataset":{
            "referenceName":"<source dataset name>",
            "type":"DatasetReference"
        },
        "firstRowOnly":<true or false>
    }
}
```

## <a name="type-properties"></a>Tipo de propriedades

Nome | Descrição | Tipo | Necessário?
---- | ----------- | ---- | --------
conjunto de dados | Fornece a referência de conjunto de dados para a procura. Obtenha detalhes da secção **de propriedades do Dataset** em cada artigo de conector correspondente. | Par chave/valor | Yes
source | Contém propriedades de origem específicas do conjunto de dados, as mesmas que a fonte de Atividade de Cópia. Obtenha detalhes da secção **de propriedades da Atividade de Cópia** em cada artigo de conector correspondente. | Par chave/valor | Yes
firstRowOnly | Indica se deve voltar apenas a primeira linha ou todas as linhas. | Booleano | N.º A predefinição é `true`.

> [!NOTE]
> 
> * As colunas de origem com o tipo **ByteArray** não são suportadas.
> * **A estrutura** não é suportada nas definições de conjunto de dados. Para ficheiros de formato de texto, utilize a linha do cabeçalho para fornecer o nome da coluna.
> * Se a sua fonte de procura for um ficheiro JSON, a `jsonPathDefinition` definição para remodelar o objeto JSON não é suportada. Todos os objetos serão recuperados.

## <a name="use-the-lookup-activity-result"></a>Utilize o resultado da atividade de Lookup

O resultado da procura é devolvido na `output` secção do resultado da execução da atividade.

* **Quando `firstRowOnly` definido para `true` (predefinido)**, o formato de saída é como mostrado no código seguinte. O resultado da procura está sob uma `firstRow` chave fixa. Para utilizar o resultado em atividades subsequentes, utilize o padrão de  `@{activity('LookupActivity').output.firstRow.table}` .

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "schema":"dbo",
            "table":"Table1"
        }
    }
    ```

* **Quando `firstRowOnly` estiver `false` definido para**, o formato de saída é como mostrado no código seguinte. Um `count` campo indica quantos registos são devolvidos. Os valores detalhados são apresentados sob uma `value` matriz fixa. Neste caso, a atividade De Procura é seguida por uma [atividade forecível](control-flow-for-each-activity.md). Você passa a `value` matriz para o campo de atividades ForEach `items` usando o padrão de `@activity('MyLookupActivity').output.value` . Para aceder aos elementos da `value` matriz, utilize a seguinte sintaxe: `@{activity('lookupActivity').output.value[zero based index].propertyname}` . Um exemplo é `@{activity('lookupActivity').output.value[0].schema}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "schema":"dbo",
                "table":"Table1"
            },
            {
                "Id": "2",
                "schema":"dbo",
                "table":"Table2"
            }
        ]
    } 
    ```

## <a name="example"></a>Exemplo

Neste exemplo, o gasoduto contém duas atividades: **Lookup** e **Copy**. A Copy Activity copia dados de uma tabela SQL na sua caixa de base de dados Azure SQL para o armazenamento de Azure Blob. O nome da tabela SQL está guardado num ficheiro JSON no armazenamento Blob. A atividade de Lookup procura o nome da mesa no tempo de execução. O JSON é modificado dinamicamente utilizando esta abordagem. Não é necessário recolocar oleodutos ou conjuntos de dados. 

Este exemplo demonstra a procura apenas para a primeira linha. Para procurar todas as linhas e acorrentar os resultados com a atividade ForEach, consulte as amostras em [Copiar várias tabelas a granel utilizando a Azure Data Factory](tutorial-bulk-copy.md).


### <a name="pipeline"></a>Pipeline

- A atividade de Lookup está configurada para utilizar **o LookupDataset,** que se refere a uma localização no armazenamento Azure Blob. A atividade de Lookup lê o nome da tabela SQL a partir de um ficheiro JSON neste local. 
- A Atividade copy utiliza a saída da atividade Lookup, que é o nome da tabela SQL. A **propriedade tableName** no **SourceDataset** está configurada para utilizar a saída da atividade Lookup. Copiar A Atividade copia os dados da tabela SQL para uma localização no armazenamento da Azure Blob. A localização é especificada pela propriedade **SinkDataset.** 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "JsonSource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        },
                        "formatSettings": {
                            "type": "JsonReadSettings"
                        }
                    },
                    "dataset": {
                        "referenceName": "LookupDataset",
                        "type": "DatasetReference"
                    },
                    "firstRowOnly": true
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "dependsOn": [
                    {
                        "activity": "LookupActivity",
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
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "AzureSqlSource",
                        "sqlReaderQuery": {
                            "value": "select * from [@{activity('LookupActivity').output.firstRow.schema}].[@{activity('LookupActivity').output.firstRow.table}]",
                            "type": "Expression"
                        },
                        "queryTimeout": "02:00:00",
                        "partitionOption": "None"
                    },
                    "sink": {
                        "type": "DelimitedTextSink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        },
                        "formatSettings": {
                            "type": "DelimitedTextWriteSettings",
                            "quoteAllText": true,
                            "fileExtension": ".txt"
                        }
                    },
                    "enableStaging": false,
                    "translator": {
                        "type": "TabularTranslator",
                        "typeConversion": true,
                        "typeConversionSettings": {
                            "allowDataTruncation": true,
                            "treatBooleanAsNumber": false
                        }
                    }
                },
                "inputs": [
                    {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schemaName": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "tableName": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "SinkDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schema": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "table": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        ],
        "annotations": [],
        "lastPublishTime": "2020-08-17T10:48:25Z"
    }
}
```

### <a name="lookup-dataset"></a>Conjunto de dados de procura

O conjunto de dados **de procura** é osourcetable.js **ficheiro na** pasta de procuração Azure Storage especificada pelo tipo **AzureBlobStorageLinkedService.** 

```json
{
    "name": "LookupDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "Json",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "sourcetable.json",
                "container": "lookup"
            }
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>Conjunto de dados de **origem** para atividade de cópia

O conjunto de dados de **origem** utiliza a saída da atividade Desatenção, que é o nome da tabela SQL. Copiar A Atividade copia os dados desta tabela SQL para uma localização no armazenamento da Azure Blob. A localização é especificada pelo conjunto de dados da **pia.** 

```json
{
    "name": "SourceDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureSqlDatabase",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schemaName": {
                "type": "string"
            },
            "tableName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "AzureSqlTable",
        "schema": [],
        "typeProperties": {
            "schema": {
                "value": "@dataset().schemaName",
                "type": "Expression"
            },
            "table": {
                "value": "@dataset().tableName",
                "type": "Expression"
            }
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>**Conjunto** de dados de sumidouro para atividade de cópia

Copiar A Atividade copia os dados da tabela SQL para o ficheiro **filebylookup.csv** na pasta **csv** no Azure Storage. O ficheiro é especificado pela propriedade **AzureBlobStorageLinkedService.** 

```json
{
    "name": "SinkDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schema": {
                "type": "string"
            },
            "table": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@{dataset().schema}_@{dataset().table}.csv",
                    "type": "Expression"
                },
                "container": "csv"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.jsem

Pode utilizar dois tipos de formatos para **sourcetable.jsficheiro.**

#### <a name="set-of-objects"></a>Conjunto de objetos

```json
{
   "Id":"1",
   "schema":"dbo",
   "table":"Table1"
}
{
   "Id":"2",
   "schema":"dbo",
   "table":"Table2"
}
```

#### <a name="array-of-objects"></a>Matriz de objetos

```json
[ 
    {
        "Id": "1",
        "schema":"dbo",
        "table":"Table1"
    },
    {
        "Id": "2",
        "schema":"dbo",
        "table":"Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>Limitações e soluções alternativas

Aqui estão algumas limitações da atividade de Lookup e soluções alternativas sugeridas.

| Limitação | Solução |
|---|---|
| A atividade de Lookup tem um máximo de 5.000 linhas, e um tamanho máximo de 2 MB. | Desenhe um oleoduto de dois níveis onde o gasoduto exterior itera sobre um gasoduto interior, que recupera dados que não excedam as linhas máximas ou o tamanho. |
| | |

## <a name="next-steps"></a>Passos seguintes
Consulte outras atividades de fluxo de controlo suportadas pela Data Factory: 

- [Executar atividade de Pipeline](control-flow-execute-pipeline-activity.md)
- [Atividade ForEach](control-flow-for-each-activity.md)
- [Atividade getMetadata](control-flow-get-metadata-activity.md)
- [Atividade web](control-flow-web-activity.md)
