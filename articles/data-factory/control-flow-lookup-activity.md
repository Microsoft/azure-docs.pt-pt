---
title: Atividade de procura na Fábrica de Dados Azure
description: Aprenda a usar a atividade de Lookup para procurar um valor a partir de uma fonte externa. Esta saída pode ser ainda referenciada através de atividades de sucesso.
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
ms.openlocfilehash: 02abdaf46ca2af6c96d3b5e8d4ce5876831bd415
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81418006"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Atividade de procura na Fábrica de Dados Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A atividade de procura pode recuperar um conjunto de dados de qualquer uma das fontes de dados suportadas pela Azure Data Factory. Utilize-o no seguinte cenário:
- Determinar dinamicamente quais os objetos a operar numa atividade subsequente, em vez de codificar duramente o nome do objeto. Alguns exemplos de objetos são ficheiros e tabelas.

A atividade de procura lê e devolve o conteúdo de um ficheiro ou tabela de configuração. Também devolve o resultado da execução de uma consulta ou procedimento armazenado. A saída da atividade de Lookup pode ser usada numa atividade de cópia ou transformação subsequente se for um valor singleton. A saída pode ser usada numa atividade ForEach se for uma variedade de atributos.

## <a name="supported-capabilities"></a>Capacidades suportadas

As seguintes fontes de dados são suportadas para a atividade de Procura. O maior número de linhas que podem ser devolvidas pela atividade da Lookup é de 5.000, até 2 MB de tamanho. Atualmente, a duração mais longa para a atividade de Lookup antes do intervalo é de uma hora.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Syntax

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

## <a name="type-properties"></a>Tipo de propriedades

Name | Description | Tipo | Necessário?
---- | ----------- | ---- | --------
conjunto de dados | Fornece a referência de conjunto de dados para a procura. Obtenha detalhes da secção **de propriedades do Dataset** em cada artigo de conector correspondente. | Par chave/valor | Yes
source | Contém propriedades de origem específicas do conjunto de dados, as mesmas que a fonte de Atividade de Cópia. Obtenha detalhes da secção **de propriedades da Atividade de Cópia** em cada artigo de conector correspondente. | Par chave/valor | Yes
firstRowOnly | Indica se deve voltar apenas a primeira linha ou todas as linhas. | Booleano | Não. A predefinição é `true`.

> [!NOTE]
> 
> * As colunas de origem com o tipo **ByteArray** não são suportadas.
> * **A estrutura** não é suportada nas definições de conjunto de dados. Para ficheiros de formato de texto, utilize a linha do cabeçalho para fornecer o nome da coluna.
> * Se a sua fonte de procura for um ficheiro JSON, a `jsonPathDefinition` definição para remodelar o objeto JSON não é suportada. Todos os objetos serão recuperados.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>A utilização da atividade de Lookup resulta numa atividade subsequente

O resultado da procura é devolvido na `output` secção do resultado da execução da atividade.

* **Quando `firstRowOnly` definido para `true` (predefinido)**, o formato de saída é como mostrado no código seguinte. O resultado da procura está sob uma `firstRow` chave fixa. Para utilizar o resultado em atividades subsequentes, utilize o padrão de `@{activity('MyLookupActivity').output.firstRow.TableName}` .

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Quando `firstRowOnly` estiver `false` definido para **, o formato de saída é como mostrado no código seguinte. Um `count` campo indica quantos registos são devolvidos. Os valores detalhados são apresentados sob uma `value` matriz fixa. Neste caso, a atividade De Procura é seguida por uma [atividade forecível](control-flow-for-each-activity.md). Você passa a `value` matriz para o campo de atividades ForEach `items` usando o padrão de `@activity('MyLookupActivity').output.value` . Para aceder aos elementos da `value` matriz, utilize a seguinte sintaxe: `@{activity('lookupActivity').output.value[zero based index].propertyname}` . Um exemplo é `@{activity('lookupActivity').output.value[0].tablename}`.

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
Neste exemplo, copy Activity copia dados de uma tabela SQL na sua caixa de dados Azure SQL para armazenamento Azure Blob. O nome da tabela SQL está guardado num ficheiro JSON no armazenamento Blob. A atividade de Lookup procura o nome da mesa no tempo de execução. O JSON é modificado dinamicamente utilizando esta abordagem. Não é necessário recolocar oleodutos ou conjuntos de dados. 

Este exemplo demonstra a procura apenas para a primeira linha. Para procurar todas as linhas e acorrentar os resultados com a atividade ForEach, consulte as amostras em [Copiar várias tabelas a granel utilizando a Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Pipeline
Este oleoduto contém duas atividades: Procura e Cópia. 

- A atividade de Lookup está configurada para utilizar **o LookupDataset,** que se refere a uma localização no armazenamento Azure Blob. A atividade de Lookup lê o nome da tabela SQL a partir de um ficheiro JSON neste local. 
- Copy Activity utiliza a saída da atividade Lookup, que é o nome da tabela SQL. A **propriedade tableName** no **SourceDataset** está configurada para utilizar a saída da atividade Lookup. Copiar A Atividade copia os dados da tabela SQL para uma localização no armazenamento da Azure Blob. A localização é especificada pela propriedade **SinkDataset.** 

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
O conjunto de dados **de procura** é osourcetable.js**ficheiro na** pasta de procuração Azure Storage especificada pelo tipo **AzureStorageLinkedService.** 

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

### <a name="source-dataset-for-copy-activity"></a>Conjunto de dados de **origem** para atividade de cópia
O conjunto de dados de **origem** utiliza a saída da atividade Desatenção, que é o nome da tabela SQL. Copiar A Atividade copia os dados desta tabela SQL para uma localização no armazenamento da Azure Blob. A localização é especificada pelo conjunto de dados da **pia.** 

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

### <a name="sink-dataset-for-copy-activity"></a>**Conjunto** de dados de sumidouro para atividade de cópia
Copiar A Atividade copia os dados da tabela SQL para o ficheiro **filebylookup.csv** na pasta **csv** no Azure Storage. O ficheiro é especificado pela propriedade **AzureStorageLinkedService.** 

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
Esta instância da Base de Dados Azure SQL contém os dados a copiar para o armazenamento blob. 

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

### <a name="sourcetablejson"></a>sourcetable.jsem

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

#### <a name="array-of-objects"></a>Matriz de objetos

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
