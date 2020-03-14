---
title: Mapeamento de esquema na atividade de cópia
description: Saiba como a atividade de cópia na Azure Data Factory mapeia esquemas e tipos de dados desde dados de origem até dados afundados ao copiar dados.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: jingwang
ms.openlocfilehash: 9ae07e2a471cc417b467092a2616a5a0cdafb1fe
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260816"
---
# <a name="schema-mapping-in-copy-activity"></a>Mapeamento de esquema na atividade de cópia

Este artigo descreve como a atividade de cópia da Azure Data Factory faz mapeamento de esquemas e mapeamento de tipo de dados de dados de origem para afundar dados ao executar a cópia de dados.

## <a name="schema-mapping"></a>Mapeamento de schema

O mapeamento da coluna aplica-se ao copiar dados de origem para afundar. Por predefinição, copie os dados de origem do mapa de atividade **para afundar por nomes**de colunas . Pode especificar [mapeamento explícito](#explicit-mapping) para personalizar o mapeamento da coluna com base na sua necessidade. Mais especificamente, copiar atividade:

1. Leia os dados de origem e determine o esquema de origem
2. Utilize o mapeamento predefinido da coluna para as colunas de mapas pelo nome, ou aplique o mapeamento explícito da coluna, se especificado.
3. Escreva os dados para afundar

### <a name="explicit-mapping"></a>Mapeamento explícito

Pode especificar as colunas para mapear na atividade de cópia -> `translator` -> `mappings` propriedade. O exemplo seguinte define uma atividade de cópia num pipeline para copiar dados de texto delimitado para base de dados Azure SQL.

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

As seguintes propriedades são suportadas sob `translator` -> `mappings` &> objeto com `source` e `sink`:

| Propriedade | Descrição                                                  | Necessário |
| -------- | ------------------------------------------------------------ | -------- |
| nome     | Nome da fonte ou coluna do lavatório.                           | Sim      |
| ordinal  | Índice de colunas. Comece com 1. <br>Aplicar e exigir quando utilizar texto delimitado sem linha de cabeçalho. | Não       |
| caminho     | Expressão do caminho JSON para cada campo extrair ou mapear. Solicite dados hierárquicos, por exemplo, MongoDB/REST.<br>Para campos sob o objeto raiz, o caminho JSON começa com raiz $; para campos dentro da matriz escolhida por `collectionReference` propriedade, o caminho JSON começa a partir do elemento matriz. | Não       |
| tipo     | Data Factory tipo de dados provisórios da fonte ou coluna do lavatório. | Não       |
| culture  | Cultura da fonte ou coluna de afundar. <br>Aplicar quando o tipo é `Datetime` ou `Datetimeoffset`. A predefinição é `en-us`. | Não       |
| format   | Cadeia de formato a utilizar quando o tipo é `Datetime` ou `Datetimeoffset`. Consulte as [cordas de data e formato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) de tempo personalizados sobre como formatar a data. | Não       |

As seguintes propriedades são suportadas em `translator` -> `mappings`, além de se opor em `source` e `sink`:

| Propriedade            | Descrição                                                  | Necessário |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Suportado apenas quando os dados hierárquicos, por exemplo, MongoDB/REST forem fonte.<br>Se pretender iterar e extrair dados dos objetos dentro de um campo de **matriz** com o mesmo padrão e converter-se por linha por objeto, especifique o caminho JSON dessa matriz para fazer a aplicação cruzada. | Não       |

### <a name="alternative-column-mapping"></a>Mapeamento de colunaalternativa

Pode especificar a atividade de cópia -> `translator` -> `columnMappings` para mapear entre dados em forma de tabular . Neste caso, a secção "estrutura" é necessária tanto para os conjuntos de dados de entrada como para a saída. O mapeamento da coluna suporta **o mapeamento de todas ou subconjuntos de colunas na "estrutura" do conjunto de dados de origem para todas as colunas da "estrutura"** do conjunto de dados do lavatório . Seguem-se as condições de erro que resultam numa exceção:

* O resultado da consulta de consulta de dados de origem não tem um nome de coluna especificado na secção "estrutura" do conjunto de dados de entrada.
* A loja de dados do sink (se com esquema pré-definido) não tem um nome de coluna especificado na secção "estrutura" do conjunto de dados de saída.
* Ou menos colunas ou mais colunas na "estrutura" do conjunto de dados do lavatório do que o especificado no mapeamento.
* Mapeamento duplicado.

No exemplo seguinte, o conjunto de dados de entrada tem uma estrutura, e aponta para uma tabela numa base de dados do Oracle no local.

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

Nesta amostra, o conjunto de dados de saída tem uma estrutura e aponta para uma tabela em Salesfoce.

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

O seguinte JSON define uma atividade de cópia num oleoduto. As colunas da fonte mapeadas para colunas na pia utilizando o **tradutor** -> propriedade **colunaMappings.**

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

Se estiver a utilizar a sintaxe de `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` para especificar o mapeamento da coluna, ainda é suportado como está.

### <a name="alternative-schema-mapping"></a>Mapeamento de esquemaalternativo

Pode especificar a atividade de cópia -> `translator` -> `schemaMapping` para mapear entre dados em forma de hierárquico e dados em forma de tabular, por exemplo, cópia de MongoDB/REST para ficheiro de texto e cópia da Oracle para a API da Azure Cosmos DB para MongoDB. As seguintes propriedades são suportadas na secção `translator` de atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do tradutor de atividade de cópia deve ser definida para: **TabularTradutor** | Sim |
| schemaMapping | Uma coleção de pares de valor-chave, que representa a relação de mapeamento **do lado da fonte para**o lado da pia .<br/>- **Chave:** representa a fonte. Para **a fonte tabular,** especifique o nome da coluna tal como definido na estrutura do conjunto de dados; para **fonte hierárquica,** especifique a expressão do caminho JSON para cada campo extrair e mapear.<br>- **Valor:** representa afundar. Para **o lavatório tabular,** especifique o nome da coluna tal como definido na estrutura do conjunto de dados; para **afundar hierárquico,** especifique a expressão do caminho JSON para cada campo extrair e mapear. <br>No caso dos dados hierárquicos, para campos sob objeto raiz, o caminho jSON começa com raiz $; para campos dentro da matriz escolhida por `collectionReference` propriedade, o caminho JSON começa a partir do elemento matriz.  | Sim |
| collectionReference | Se pretender iterar e extrair dados dos objetos dentro de um campo de **matriz** com o mesmo padrão e converter-se por linha por objeto, especifique o caminho JSON dessa matriz para fazer a aplicação cruzada. Esta propriedade só é suportada quando os dados hierárquicos são fonte. | Não |

**Exemplo: cópia de MongoDB para Oráculo:**

Por exemplo, se tiver um documento MongoDB com o seguinte conteúdo:

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

e pretende copiá-la numa tabela Azure SQL no seguinte formato, achatando os dados dentro da matriz *(order_pd e order_price)* e cruzando-se com a informação de raiz comum *(número, data e cidade)* :

| orderNumber | encomendaData | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Configure a regra de mapeamento de esquemas como a seguinte amostra de atividade de cópia JSON:

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
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>Mapeamento de tipo de dados

A atividade de cópia executa tipos de origem para os tipos de sink type mapeamento com a seguinte abordagem de 2 passos:

1. Converter de tipos de origem nativa para tipos de dados provisórios da Azure Data Factory
2. Converter de tipos de dados provisórios da Azure Data Factory para o tipo de pia nativa

Pode encontrar o mapeamento entre o tipo nativo e o tipo provisório na secção "Mapeamento do tipo data" em cada tópico do conector.

### <a name="supported-data-types"></a>Tipos de dados suportados

Data Factory suporta os seguintes tipos de dados provisórios: Pode especificar abaixo valores ao configurar informações do tipo na configuração da estrutura do conjunto de [dados:](concepts-datasets-linked-services.md#dataset-structure-or-schema)

* Byte[]
* Booleano
* Datetime
* Datetimeoffset
* decimal
* Valor de duplo
* Guid
* Int16
* Int32
* Int64
* Único
* String
* Timespan

## <a name="next-steps"></a>Passos seguintes
Consulte os outros artigos de atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
