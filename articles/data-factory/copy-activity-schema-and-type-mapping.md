---
title: Mapeamento de esquema na atividade de cópia | Documentos da Microsoft
description: Saiba mais sobre como a atividade de cópia no Azure Data Factory mapeia tipos de dados e esquemas de origem de dados para dados de sink, quando se copiam dados.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 9108f83e854b51720c64c5a74a828543cc5e7688
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64875807"
---
# <a name="schema-mapping-in-copy-activity"></a>Mapeamento de esquema na atividade de cópia

Este artigo descreve como a atividade de cópia de fábrica de dados do Azure faz o mapeamento de esquema e o mapeamento de tipo de dados de origem de dados para dados de sink quando executar a cópia de dados.

## <a name="schema-mapping"></a>Mapeamento de esquema

Mapeamento de colunas aplica-se ao copiar dados de origem para o sink. Por predefinição, a atividade de cópia **mapear dados de origem para o sink por nomes de coluna**. Pode especificar [mapeamento explícito](#explicit-mapping) para personalizar o mapeamento de colunas com base nas suas necessidades. Mais especificamente, a atividade de cópia:

1. Ler os dados de origem e determinar o esquema de origem
2. Utilizar o mapeamento de colunas predefinido para mapear colunas por nome ou aplicar o mapeamento de colunas explícita se for especificado.
3. Escrever os dados de sink

### <a name="explicit-mapping"></a>Mapeamento explícito

Pode especificar as colunas para mapear na atividade de cópia -> `translator`  ->  `mappings` propriedade. O exemplo seguinte define uma atividade de cópia num pipeline para copiar dados de texto delimitado para a base de dados do Azure SQL.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [{
        "referenceName": "DelimitedTextInput",
        "type": "DatasetReference"
    }],
    "outputs": [{
        "referenceName": "AzureSqlOutput",
        "type": "DatasetReference"
    }],
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": {
                        "name": "UserId",
                        "type": "Guid"
                    },
                    "sink": {
                        "name": "MyUserId"
                    }
                }, 
                {
                    "source": {
                        "name": "Name",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyName"
                    }
                }, 
                {
                    "source": {
                        "name": "Group",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyGroup"
                    }
                }
            ]
        }
    }
}
```

As seguintes propriedades são suportadas ao abrigo `translator`  ->  `mappings` -> objeto com `source` e `sink`:

| Propriedade | Descrição                                                  | Necessário |
| -------- | ------------------------------------------------------------ | -------- |
| name     | Nome da coluna de origem ou sink.                           | Sim      |
| ordinal  | Índice de coluna. Comece com 1. <br>Aplicam-se e é necessário quando utilizar delimitados por texto sem a linha de cabeçalho. | Não       |
| path     | Expressão de caminho JSON para cada campo extrair ou mapear. Aplicam-se para dados hierárquicos por exemplo, MongoDB/REST.<br>Para os campos no objeto raiz, o caminho JSON é iniciado com a raiz $; para os campos dentro da matriz escolhida pela `collectionReference` propriedade, caminho JSON começa a partir do elemento de matriz. | Não       |
| type     | Tipo de dados intermediárias de fábrica de dados da coluna de origem ou sink. | Não       |
| culture  | Cultura da coluna de origem ou sink. <br>Aplicam-se quando é do tipo `Datetime` ou `Datetimeoffset`. A predefinição é `en-us`. | Não       |
| format   | Formatar a cadeia de caracteres a ser utilizado quando o tipo é `Datetime` ou `Datetimeoffset`. Consulte a [data de personalizado e cadeias de caracteres de formato de tempo](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) sobre como formatar datetime. | Não       |

As seguintes propriedades são suportadas ao abrigo `translator`  ->  `mappings` além de objeto com `source` e `sink`:

| Propriedade            | Descrição                                                  | Necessário |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Suportado apenas quando dados hierárquicos por exemplo, a MongoDB/REST são origem.<br>Se pretender iterar e extrair dados de objetos **dentro de um campo de matriz** com o mesmo padrão e convertem-se por linha por objeto, especifique o caminho JSON dessa matriz entre-aplicar. | Não       |

### <a name="alternative-column-mapping"></a>Mapeamento de colunas alternativo

Pode especificar cópia atividade -> `translator`  ->  `columnMappings` para mapeamento entre os dados em forma tabela. Neste caso, a seção de "estrutura" é necessária para conjuntos de dados de entrada e saídos. Suporta de mapeamento de coluna **mapeamento todos ou subconjunto de colunas no conjunto de dados de origem "estrutura" para todas as colunas no conjunto de dados de sink "estrutura"** . Seguem-se as condições de erro que resultam numa exceção:

* Resultado não tem um nome de coluna especificado na seção de "estrutura" conjunto de dados de entrada de consulta de arquivo de dados de origem.
* Arquivo de dados de sink (se com o esquema predefinido) não tem um nome de coluna especificado na seção de "estrutura" do conjunto de dados de saída.
* Menos colunas ou mais colunas na "estrutura" do conjunto de dados de sink que especificado no mapeamento.
* Mapeamento de duplicados.

No exemplo a seguir, o conjunto de dados de entrada tem uma estrutura e aponta para uma tabela numa base de dados Oracle no local.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

Neste exemplo, o conjunto de dados de saída tem uma estrutura e aponta para uma tabela no Salesfoce.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

O JSON seguinte define uma atividade de cópia num pipeline. As colunas de origem mapeado para colunas no sink utilizando o **translator** -> **columnMappings** propriedade.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Se estiver a utilizar a sintaxe do `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` para especificar o mapeamento de colunas, ainda é suportado como-é.

### <a name="alternative-schema-mapping"></a>Mapeamento de esquema alternativo

Pode especificar cópia atividade -> `translator`  ->  `schemaMapping` para mapear entre hierárquicas em forma de dados e os dados em forma tabela, por exemplo, copiar do MongoDB/REST para o ficheiro de texto e a cópia do Oracle para a API do Azure Cosmos DB para o MongoDB. As seguintes propriedades são suportadas na atividade de cópia `translator` secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de conversor de atividade de cópia tem de ser definida: **TabularTranslator** | Sim |
| schemaMapping | Uma coleção de pares chave-valor, que representa a relação de mapeamento **do lado de origem para o sink do lado do**.<br/>- **Key:** origem representa. Para **origem tabular**, especifique o nome da coluna, conforme definido na estrutura do conjunto de dados; para **origem hierárquica**, especifique a expressão de caminho JSON para cada campo extrair e mapear.<br>- **Value:** representa sink. Para **tabular sink**, especifique o nome da coluna, conforme definido na estrutura do conjunto de dados; para **sink hierárquica**, especifique a expressão de caminho JSON para cada campo extrair e mapear. <br>No caso de dados hierárquicos, para os campos no objeto raiz, o caminho JSON começa com a raiz $; para os campos dentro da matriz escolhida pela `collectionReference` propriedade, caminho JSON começa a partir do elemento de matriz.  | Sim |
| collectionReference | Se pretender iterar e extrair dados de objetos **dentro de um campo de matriz** com o mesmo padrão e convertem-se por linha por objeto, especifique o caminho JSON dessa matriz entre-aplicar. Esta propriedade só é suportada quando dados hierárquicos são a origem. | Não |

**Exemplo: copie do MongoDB para Oracle:**

Por exemplo, se tiver o documento do MongoDB com o seguinte conteúdo:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

e quiser copiá-lo para uma tabela de SQL do Azure no seguinte formato, ao cruzá-los os dados no interior da matriz *(order_pd e order_price)* e em vários Junte-se com as informações de raiz comuns *(número, data e Cidade)* :

| orderNumber | orderDate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Configure a regra de mapeamento de esquema como o seguinte exemplo JSON de atividade de cópia:

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "orderNumber": "$.number",
                "orderDate": "$.date",
                "order_pd": "prod",
                "order_price": "price",
                "city": " $.city[0].name"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>Mapeamento de tipo de dados

Atividade de cópia executa os tipos de origem para o sink de tipos de mapeamento com a seguinte abordagem de passo 2:

1. Converter de tipos de origem nativas para tipos de dados intermediárias do Azure Data Factory
2. Converter entre tipos de dados intermediárias do Azure Data Factory para o tipo de sink nativo

Pode encontrar o mapeamento entre o tipo nativo para o tipo intermediário na seção "Mapeamento de tipo de dados" em cada tópico de conector.

### <a name="supported-data-types"></a>Tipos de dados suportados

Data Factory suporta os seguintes tipos de dados intermediárias: Pode especificar valores abaixo ao configurar informações de tipo no [estrutura do conjunto de dados](concepts-datasets-linked-services.md#dataset-structure-or-schema) configuração:

* Byte[]
* Boolean
* Datetime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* String
* Timespan

## <a name="next-steps"></a>Passos Seguintes
Consulte os outros artigos de atividade de cópia:

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
