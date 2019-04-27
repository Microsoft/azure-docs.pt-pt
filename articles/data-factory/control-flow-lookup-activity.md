---
title: Atividade de pesquisa no Azure Data Factory | Documentos da Microsoft
description: Saiba como utilizar a atividade de pesquisa para procurar um valor de uma origem externa. Este resultado pode ser ainda mais referenciado por atividades subsequentes.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: shlo
ms.openlocfilehash: 4f0662a71ee14af3c2c1aafee210641fc8b51f1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60768671"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Atividade de pesquisa no Azure Data Factory

Atividade de pesquisa pode obter um conjunto de dados a partir de qualquer uma das origens de dados do Azure Data Factory com suporte. Utilize o seguinte cenário:
- Dinamicamente determine quais os objetos a trabalhar numa atividade subsequente, em vez de disco rígido o nome do objeto de codificação. Alguns exemplos de objeto são ficheiros e tabelas.

Atividade lookup lê e devolve o conteúdo de um ficheiro de configuração ou de uma tabela. Ele também retorna o resultado da execução de uma consulta ou um procedimento armazenado. A saída da atividade de pesquisa pode ser utilizada numa cópia subsequente ou atividade de transformação, se for um valor de singleton. A saída pode ser utilizada numa atividade ForEach, se for uma matriz de atributos.

## <a name="supported-capabilities"></a>Capacidades suportadas

As seguintes origens de dados são suportadas para a atividade de pesquisa. O maior número de linhas que podem ser retornados pela atividade de pesquisa é 5.000, até 2 MB de tamanho. Atualmente, a duração mais longa para a pesquisa de atividade antes do tempo limite é de uma hora.

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

Name | Descrição | Type | Necessário?
---- | ----------- | ---- | --------
Conjunto de dados | Fornece a referência de conjunto de dados para a pesquisa. Obtenha detalhes no **propriedades do conjunto de dados** secção cada artigo conector correspondente. | Par chave/valor | Sim
source | Contém as propriedades da origem de conjunto de dados específicas, o mesmo que a origem de atividade de cópia. Obtenha detalhes no **propriedades da atividade de cópia** secção cada artigo conector correspondente. | Par chave/valor | Sim
firstRowOnly | Indica se deve retornar apenas a primeira linha ou todas as linhas. | Boolean | Não. A predefinição é `true`.

> [!NOTE]
> 
> * Colunas com de origem **ByteArray** tipo não são suportados.
> * **Estrutura** não é suportada nas definições do conjunto de dados. Para os ficheiros de formato de texto, utilize a linha de cabeçalho para fornecer o nome da coluna.
> * Se a sua origem de pesquisa é um ficheiro JSON, o `jsonPathDefinition` definição para reformulação do objeto JSON não é suportada. Os objetos inteiros serão obtidos.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Utilizar o resultado da atividade de pesquisa numa atividade subsequente

É devolvido o resultado de pesquisa no `output` seção a resultado da execução da atividade.

* **Quando `firstRowOnly` está definido como `true` (predefinição)**, o formato de saída é conforme mostrado no código a seguir. O resultado de pesquisa é fixa em `firstRow` chave. Para utilizar o resultado na atividade subsequente, utilize o padrão de `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Quando `firstRowOnly` está definido como `false`** , o formato de saída é conforme mostrado no código a seguir. A `count` campo indica o número de registos é devolvido. Valores detalhadas são apresentadas em fixa `value` matriz. Nesse caso, a atividade de pesquisa é seguida por uma [atividade Foreach](control-flow-for-each-activity.md). Passa o `value` matriz para a atividade ForEach `items` campo ao utilizar o padrão de `@activity('MyLookupActivity').output.value`. Para elementos de acesso a `value` matriz, utilize a seguinte sintaxe: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Um exemplo é `@{activity('lookupActivity').output.value[0].tablename}`.

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
Neste exemplo, a atividade de cópia copia dados de uma tabela SQL na sua instância de base de dados do Azure SQL para o armazenamento de Blobs do Azure. O nome da tabela SQL é armazenado num ficheiro JSON no armazenamento de Blobs. A atividade de pesquisa procura o nome da tabela em tempo de execução. JSON é modificado dinamicamente usando essa abordagem. Não precisa de voltar a implementar pipelines ou conjuntos de dados. 

Este exemplo demonstra uma pesquisa na primeira linha. Para a pesquisa para todas as linhas e encadear os resultados com a atividade ForEach, veja os exemplos [copiar várias tabelas em massa com o Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Pipeline
Este pipeline contém duas atividades: Lookup e Copy. 

- A atividade de pesquisa está configurada para utilizar **LookupDataset**, que se refere a uma localização no armazenamento de Blobs do Azure. A atividade de pesquisa lê o nome da tabela SQL a partir de um ficheiro JSON nesta localização. 
- Atividade de cópia utiliza a saída da atividade de pesquisa, que é o nome da tabela SQL. O **tableName** propriedade na **SourceDataset** está configurado para utilizar a saída da atividade Lookup. Copie dados de cópias de atividade da tabela SQL para uma localização no armazenamento de Blobs do Azure. A localização especificada pelos **SinkDataset** propriedade. 

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

### <a name="lookup-dataset"></a>Conjunto de dados de pesquisa
O **lookup** conjunto de dados é a **sourcetable.json** ficheiro na pasta de pesquisa do armazenamento do Azure especificada pelo **AzureStorageLinkedService** tipo. 

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

### <a name="source-dataset-for-copy-activity"></a>**Origem** conjunto de dados para a atividade de cópia
O **origem** conjunto de dados utiliza a saída da atividade de pesquisa, que é o nome da tabela SQL. Copie atividade copia dados desta tabela de SQL para uma localização no armazenamento de Blobs do Azure. A localização especificada pelos **sink** conjunto de dados. 

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

### <a name="sink-dataset-for-copy-activity"></a>**Sink** conjunto de dados para a atividade de cópia
Atividade de cópia copia dados da tabela SQL para o **filebylookup.csv** de ficheiros a **csv** pasta no armazenamento do Azure. O ficheiro for especificado o **AzureStorageLinkedService** propriedade. 

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
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Serviço ligado da Base de Dados SQL do Azure
Esta instância de base de dados do Azure SQL contém os dados para ser copiado para armazenamento de Blobs. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
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
| A atividade de pesquisa possui um máximo de 5000 linhas e um tamanho máximo de 2 MB. | Crie um pipeline de dois níveis em que o pipeline externo itera através de um pipeline interno, que obtém dados que não excedam o máximo de linhas ou o tamanho. |
| | |

## <a name="next-steps"></a>Passos Seguintes
Consulte outras atividades de fluxo de controle suportadas pelo Data Factory: 

- [Atividade execute Pipeline](control-flow-execute-pipeline-activity.md)
- [Atividade ForEach](control-flow-for-each-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
- [Atividade Web](control-flow-web-activity.md)
