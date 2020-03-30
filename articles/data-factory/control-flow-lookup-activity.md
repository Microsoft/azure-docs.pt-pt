---
title: Atividade de procura na Fábrica de Dados Azure
description: Aprenda a usar a atividade de Lookup para procurar um valor a partir de uma fonte externa. Esta produção pode ser ainda referenciada através de atividades bem sucedidas.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: 08cc7ce8f306095a66bc0f8cf74dff8c8b551ecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75440494"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Atividade de procura na Fábrica de Dados Azure

A atividade de procura ção pode recuperar um conjunto de dados de qualquer uma das fontes de dados suportadas pela Azure Data Factory. Use-o no seguinte cenário:
- Determina dinamicamente quais os objetos a operar numa atividade subsequente, em vez de codificar duramente o nome do objeto. Alguns exemplos de objetos são ficheiros e tabelas.

A atividade de procura ção lê e devolve o conteúdo de um ficheiro ou tabela de configuração. Também devolve o resultado da execução de uma consulta ou procedimento armazenado. A saída da atividade lookup pode ser usada em uma atividade de cópia ou transformação subsequente se for um valor singleton. A saída pode ser usada numa atividade ForEach se for uma variedade de atributos.

## <a name="supported-capabilities"></a>Capacidades suportadas

As seguintes fontes de dados são suportadas para a atividade de procuração. O maior número de linhas que podem ser devolvidas pela atividade da Lookup é de 5.000, até 2 MB de tamanho. Atualmente, a duração mais longa para a atividade de lookup antes do intervalo é de uma hora.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Sintaxe

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>Propriedades de tipo

Nome | Descrição | Tipo | Necessário?
---- | ----------- | ---- | --------
conjunto de dados | Fornece a referência do conjunto de dados para a procura. Obtenha detalhes da secção de **propriedades dataset** em cada artigo de conector correspondente. | Par chave/valor | Sim
source | Contém propriedades de origem específicas do conjunto de dados, as mesmas que a fonte da Atividade de Cópia. Obtenha detalhes da secção de propriedades da **Atividade cópia** em cada artigo de conector correspondente. | Par chave/valor | Sim
firstRowOnly | Indica se deve voltar apenas a primeira fila ou todas as linhas. | Booleano | Não. A predefinição é `true`.

> [!NOTE]
> 
> * As colunas de origem com o tipo **ByteArray** não são suportadas.
> * **A estrutura** não é suportada em definições de conjunto de dados. Para ficheiros de formato de texto, utilize a linha do cabeçalho para fornecer o nome da coluna.
> * Se a sua fonte de procura for `jsonPathDefinition` um ficheiro JSON, a definição para remodelar o objeto JSON não é suportada. Todos os objetos serão recuperados.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Utilize a atividade de lookup resultando numa atividade subsequente

O resultado da procura `output` é devolvido na secção do resultado da execução da atividade.

* **Quando `firstRowOnly` definido `true` para (predefinido)** o formato de saída é como mostrado no seguinte código. O resultado da procura `firstRow` está sob uma chave fixa. Para utilizar o resultado em atividade `@{activity('MyLookupActivity').output.firstRow.TableName}`subsequente, utilize o padrão de .

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Quando `firstRowOnly` está `false`definido para **, o formato de saída é como mostrado no seguinte código. Um `count` campo indica quantos registos são devolvidos. Valores detalhados são `value` apresentados sob uma matriz fixa. Neste caso, a atividade de procuração é seguida por uma [atividade Foreach.](control-flow-for-each-activity.md) Passa a `value` matriz para `items` o campo de `@activity('MyLookupActivity').output.value`atividade ForEach utilizando o padrão de . Para aceder aos `value` elementos da matriz, `@{activity('lookupActivity').output.value[zero based index].propertyname}`utilize a seguinte sintaxe: . Um exemplo é `@{activity('lookupActivity').output.value[0].tablename}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

### <a name="copy-activity-example"></a>Exemplo de Atividade de Cópia
Neste exemplo, copy Activity copia dados de uma tabela SQL na sua instância de Base de Dados Azure SQL para armazenamento De Blob Azure. O nome da tabela SQL é armazenado num ficheiro JSON no armazenamento blob. A atividade de lookup procura o nome da mesa em tempo de execução. A JSON é modificada dinamicamente utilizando esta abordagem. Não é necessário reimplantar oleodutos ou conjuntos de dados. 

Este exemplo demonstra aprocura apenas para a primeira fila. Para procurar todas as linhas e para acorrentar os resultados com a atividade ForEach, consulte as amostras em [Copiar várias tabelas](tutorial-bulk-copy.md)a granel utilizando a Azure Data Factory .

### <a name="pipeline"></a>Pipeline
Este oleoduto contém duas atividades: Lookup e Copy. 

- A atividade lookup está configurada para usar **LookupDataset**, que se refere a uma localização no armazenamento Azure Blob. A atividade de procura ção lê o nome da tabela SQL a partir de um ficheiro JSON neste local. 
- A Copy Activity utiliza a saída da atividade Lookup, que é o nome da tabela SQL. A propriedade **tableName** no **SourceDataset** está configurada para utilizar a saída da atividade De supérbio. Copy Activity copia dados da tabela SQL para um local no armazenamento de Azure Blob. A localização é especificada pela propriedade **SinkDataset.** 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>Conjunto de dados de procura
O conjunto de dados **de procuração** é o ficheiro **sourcetable.json** na pasta de procura ção De Armazenamento Azure especificada pelo tipo **AzureStorageLinkedService.** 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>**Conjunto** de dados de origem para atividade de cópia
O conjunto de dados **de origem** utiliza a saída da atividade Lookup, que é o nome da tabela SQL. Copiar A Atividade copia dados desta tabela SQL para uma localização no armazenamento da Blob Azure. A localização é especificada pelo conjunto de dados do **lavatório.** 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>**Conjunto** de dados de sink para atividade de cópia
Copy Activity copia dados da tabela SQL para o ficheiro **filebylookup.csv** na pasta **csv** no Armazenamento Azure. O ficheiro é especificado pela propriedade **AzureStorageLinkedService.** 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Serviço ligado do Storage do Azure
Esta conta de armazenamento contém o ficheiro JSON com os nomes das tabelas SQL. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>"
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Serviço ligado da Base de Dados SQL do Azure
Esta instância de base de dados Azure SQL contém os dados a copiar para o armazenamento blob. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;"
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>Conjunto de objetos

```json
{
  "Id": "1",
  "tableName": "Table1"
}
{
   "Id": "2",
  "tableName": "Table2"
}
```

#### <a name="array-of-objects"></a>Conjunto de objetos

```json
[ 
    {
        "Id": "1",
        "tableName": "Table1"
    },
    {
        "Id": "2",
        "tableName": "Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>Limitações e suposições

Aqui estão algumas limitações da atividade de Lookup e suposições sugeridas.

| Limitação | Solução |
|---|---|
| A atividade de Lookup tem um máximo de 5.000 linhas, e um tamanho máximo de 2 MB. | Desenhe um gasoduto de dois níveis onde o oleoduto exterior iterates sobre um oleoduto interno, que recupera dados que não excedem as linhas ou tamanho máximos. |
| | |

## <a name="next-steps"></a>Passos seguintes
Consulte outras atividades de fluxo de controlo suportadas pela Data Factory: 

- [Executar atividade do pipeline](control-flow-execute-pipeline-activity.md)
- [Atividade ForEach](control-flow-for-each-activity.md)
- [Obtenha atividade de Metadados](control-flow-get-metadata-activity.md)
- [Atividade web](control-flow-web-activity.md)
