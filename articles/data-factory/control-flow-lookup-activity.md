---
title: Atividade de pesquisa no Azure Data Factory
description: Saiba como usar a atividade de pesquisa para pesquisar um valor de uma fonte externa. Essa saída pode ser referenciada por atividades com sucesso.
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440494"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Atividade de pesquisa no Azure Data Factory

A atividade de pesquisa pode recuperar um conjunto de dados de qualquer uma das fontes de dado com suporte Azure Data Factory. Use-o no cenário a seguir:
- Determine dinamicamente em quais objetos operar em uma atividade subsequente, em vez de embutir o nome do objeto. Alguns exemplos de objeto são arquivos e tabelas.

A atividade de pesquisa lê e retorna o conteúdo de um arquivo de configuração ou tabela. Ele também retorna o resultado da execução de uma consulta ou um procedimento armazenado. A saída da atividade de pesquisa pode ser usada em uma atividade de cópia ou transformação subsequente se for um valor singleton. A saída poderá ser usada em uma atividade ForEach se for uma matriz de atributos.

## <a name="supported-capabilities"></a>Capacidades suportadas

As fontes de dados a seguir têm suporte para a atividade de pesquisa. O maior número de linhas que podem ser retornadas pela atividade de pesquisa é 5.000, com até 2 MB de tamanho. Atualmente, a duração mais longa para a atividade de pesquisa antes do tempo limite é de uma hora.

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

## <a name="type-properties"></a>Propriedades do tipo

Nome | Descrição | Tipo | Necessário?
---- | ----------- | ---- | --------
DataSet | Fornece a referência de DataSet para a pesquisa. Obtenha detalhes da seção de **Propriedades do conjunto** de informações em cada artigo do conector correspondente. | Par chave/valor | Sim
source | Contém propriedades de origem específicas do conjunto de informações, o mesmo que a origem da atividade de cópia. Obtenha detalhes da seção **Propriedades da atividade de cópia** em cada artigo de conector correspondente. | Par chave/valor | Sim
firstRowOnly | Indica se a primeira linha ou todas as linhas devem ser retornadas. | Booleano | Não. A predefinição é `true`.

> [!NOTE]
> 
> * Não há suporte para colunas de origem com tipo **ByteArray** .
> * Não há suporte para a **estrutura** em definições de conjunto de dados. Para arquivos de formato de texto, use a linha de cabeçalho para fornecer o nome da coluna.
> * Se sua fonte de pesquisa for um arquivo JSON, a configuração `jsonPathDefinition` para remodelar o objeto JSON não terá suporte. Todos os objetos serão recuperados.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Usar o resultado da atividade de pesquisa em uma atividade subsequente

O resultado da pesquisa é retornado na seção `output` do resultado da execução da atividade.

* **Quando `firstRowOnly` é definido como `true` (padrão)** , o formato de saída é como mostrado no código a seguir. O resultado da pesquisa está abaixo de uma chave de `firstRow` fixa. Para usar o resultado na atividade subsequente, use o padrão de `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Quando `firstRowOnly` é definido como `false`** , o formato de saída é mostrado no código a seguir. Um campo `count` indica quantos registros são retornados. Os valores detalhados são exibidos em uma matriz de `value` fixa. Nesse caso, a atividade de pesquisa é seguida por uma [atividade ForEach](control-flow-for-each-activity.md). Você passa a matriz de `value` para o campo de `items` atividade ForEach usando o padrão de `@activity('MyLookupActivity').output.value`. Para acessar elementos na matriz de `value`, use a seguinte sintaxe: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Um exemplo é `@{activity('lookupActivity').output.value[0].tablename}`.

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

### <a name="copy-activity-example"></a>Exemplo de atividade de cópia
Neste exemplo, a atividade de cópia copia dados de uma tabela SQL em sua instância do banco de dados SQL do Azure para o armazenamento de BLOBs do Azure. O nome da tabela SQL é armazenado em um arquivo JSON no armazenamento de BLOBs. A atividade de pesquisa procura o nome da tabela em tempo de execução. O JSON é modificado dinamicamente usando essa abordagem. Você não precisa reimplantar pipelines ou conjuntos de valores. 

Este exemplo demonstra a pesquisa apenas para a primeira linha. Para pesquisa de todas as linhas e para encadear os resultados com a atividade ForEach, consulte os exemplos em [copiar várias tabelas em massa usando Azure data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Pipeline
Esse pipeline contém duas atividades: Lookup e Copy. 

- A atividade de pesquisa é configurada para usar **LookupDataset**, que se refere a um local no armazenamento de BLOBs do Azure. A atividade de pesquisa lê o nome da tabela SQL de um arquivo JSON neste local. 
- A atividade de cópia usa a saída da atividade de pesquisa, que é o nome da tabela SQL. A propriedade **TableName** no **SourceDataset** é configurada para usar a saída da atividade de pesquisa. A atividade de cópia copia dados da tabela SQL para um local no armazenamento de BLOBs do Azure. O local é especificado pela propriedade **SinkDataset** . 

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

### <a name="lookup-dataset"></a>Pesquisar conjunto de pesquisa
O conjunto de **pesquisa** é o arquivo **SourceTable. JSON** na pasta de pesquisa do armazenamento do Azure especificada pelo tipo **AzureStorageLinkedService** . 

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

### <a name="source-dataset-for-copy-activity"></a>Conjunto de **fonte de origem** para atividade de cópia
O conjunto de **fonte de origem** usa a saída da atividade de pesquisa, que é o nome da tabela SQL. A atividade de cópia copia dados desta tabela SQL para um local no armazenamento de BLOBs do Azure. O local é especificado pelo conjunto de **coleta** . 

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

### <a name="sink-dataset-for-copy-activity"></a>Conjunto de **coletores** para atividade de cópia
A atividade de cópia copia dados da tabela SQL para o arquivo **filebylookup. csv** na pasta **CSV** no armazenamento do Azure. O arquivo é especificado pela propriedade **AzureStorageLinkedService** . 

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
Essa conta de armazenamento contém o arquivo JSON com os nomes das tabelas SQL. 

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
Esta instância do banco de dados SQL do Azure contém os dados a serem copiados para o armazenamento de BLOBs. 

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

Aqui estão algumas limitações da atividade de pesquisa e soluções alternativas sugeridas.

| Limitação | Solução |
|---|---|
| A atividade de pesquisa tem um máximo de 5.000 linhas e um tamanho máximo de 2 MB. | Projete um pipeline de dois níveis em que o pipeline externo itera em um pipeline interno, que recupera dados que não excedem o tamanho ou as linhas máximos. |
| | |

## <a name="next-steps"></a>Passos seguintes
Consulte outras atividades de fluxo de controle com suporte pelo Data Factory: 

- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Atividade ForEach](control-flow-for-each-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
- [Atividade Web](control-flow-web-activity.md)
