---
title: Mapeamento de esquema na atividade de cópia | Documentos da Microsoft
description: Saiba mais sobre como a atividade de cópia no Azure Data Factory mapeia tipos de dados e esquemas de origem de dados para dados de sink, quando se copiam dados.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 99798b35419ec9574c99aaba42803fbeeb1555f1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59267128"
---
# <a name="schema-mapping-in-copy-activity"></a>Mapeamento de esquema na atividade de cópia
Este artigo descreve como a atividade de cópia de fábrica de dados do Azure faz o mapeamento de esquema e o mapeamento de tipo de dados de origem de dados para dados de sink quando executar a cópia de dados.

## <a name="column-mapping"></a>Mapeamento de colunas

Mapeamento de colunas aplica-se ao copiar dados entre os dados em forma tabela. Por predefinição, a atividade de cópia **mapear dados de origem para os nomes das colunas de sink**, a menos que [mapeamento de colunas explícita](#explicit-column-mapping) está configurado. Mais especificamente, a atividade de cópia:

1. Ler os dados de origem e determinar o esquema de origem

    * Para origens de dados com o esquema predefinido no dados store/formato de ficheiro, por exemplo, bancos de dados/arquivos com metadados (Avro/ORC/Parquet/texto com cabeçalho), o esquema de origem é extraída dos metadados de ficheiro ou o resultado da consulta.
    * Para origens de dados com esquema flexível, por exemplo, o Azure tabela/Cosmos DB, o esquema de origem é inferida do resultado da consulta. Pode substituí-lo ao configurar a "estrutura" no conjunto de dados.
    * Para o ficheiro de texto sem cabeçalho, os nomes predefinidos das colunas são gerados com o padrão "Prop_0", "Prop_1",... Pode substituí-lo ao configurar a "estrutura" no conjunto de dados.
    * Para a origem do Dynamics, terá de fornecer as informações de esquema na secção de "estrutura" conjunto de dados.

2. Se for especificado, aplicam-se o mapeamento de colunas explícita.

3. Escrever os dados de sink

    * Para arquivos de dados com o esquema predefinido, os dados são gravados para as colunas com o mesmo nome.
    * Para arquivos de dados sem esquema fixo e formatos de arquivo, os nomes de coluna/metadados será gerado com base no esquema de origem.

### <a name="explicit-column-mapping"></a>Mapeamento de colunas explícito

Pode especificar **columnMappings** no **typeProperties** secção da atividade de cópia para fazer o mapeamento de colunas explícita. Neste cenário, a seção de "estrutura" é necessária para conjuntos de dados de entrada e saídos. Suporta de mapeamento de coluna **mapeamento todos ou subconjunto de colunas no conjunto de dados de origem "estrutura" para todas as colunas no conjunto de dados de sink "estrutura"**. Seguem-se as condições de erro que resultam numa exceção:

* Resultado não tem um nome de coluna especificado na seção de "estrutura" conjunto de dados de entrada de consulta de arquivo de dados de origem.
* Arquivo de dados de sink (se com o esquema predefinido) não tem um nome de coluna especificado na seção de "estrutura" do conjunto de dados de saída.
* Menos colunas ou mais colunas na "estrutura" do conjunto de dados de sink que especificado no mapeamento.
* Mapeamento de duplicados.

#### <a name="explicit-column-mapping-example"></a>Exemplo de mapeamento de colunas explícito

Neste exemplo, a tabela de entrada tem uma estrutura e aponta para uma tabela numa base de dados SQL no local.

```json
{
    "name": "SqlServerInput",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "SqlServerLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

Neste exemplo, a tabela de saída tem uma estrutura e aponta para uma tabela numa base de dados SQL do Azure.

```json
{
    "name": "AzureSqlOutput",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

O JSON seguinte define uma atividade de cópia num pipeline. As colunas de origem mapeado para colunas no sink (**columnMappings**), utilizando o **translator** propriedade.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "SqlServerInput",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "AzureSqlOutput",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "SqlSource" },
        "sink": { "type": "SqlSink" },
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

**Fluxo de mapeamento de colunas:**

![Fluxo de mapeamento de coluna](./media/copy-activity-schema-and-type-mapping/column-mapping-sample.png)

## <a name="schema-mapping"></a>Mapeamento de esquema

Mapeamento de esquema aplica-se ao copiar dados entre hierárquicas em forma de dados e dados em forma tabela, por exemplo, a cópia a partir de MongoDB/REST para o arquivo de texto e a cópia da SQL API do Azure Cosmos DB para MongoDB. As seguintes propriedades são suportadas na atividade de cópia `translator` secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de conversor de atividade de cópia tem de ser definida: **TabularTranslator** | Sim |
| schemaMapping | Uma coleção de pares chave-valor, que representa a relação de mapeamento **do lado de origem para o sink do lado do**.<br/>- **Chave:** origem representa. Para **origem tabular**, especifique o nome da coluna, conforme definido na estrutura do conjunto de dados; para **origem hierárquica**, especifique a expressão de caminho JSON para cada campo extrair e mapear.<br/>- **Valor:** representa sink. Para **tabular sink**, especifique o nome da coluna, conforme definido na estrutura do conjunto de dados; para **sink hierárquica**, especifique a expressão de caminho JSON para cada campo extrair e mapear. <br/> No caso de dados hierárquicos, para os campos no objeto raiz, o caminho JSON começa com a raiz $; para os campos dentro da matriz escolhida pela `collectionReference` propriedade, caminho JSON começa a partir do elemento de matriz.  | Sim |
| collectionReference | Se pretender iterar e extrair dados de objetos **dentro de um campo de matriz** com o mesmo padrão e convertem-se por linha por objeto, especifique o caminho JSON dessa matriz entre-aplicar. Esta propriedade só é suportada quando dados hierárquicos são a origem. | Não |

**Exemplo: copie do MongoDB para o SQL:**

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
    "name": "CopyFromMongoDBToSqlAzure",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "SqlSink"
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

Data Factory suporta os seguintes tipos de dados intermediárias: Pode especificar valores abaixo ao configurar informações de tipo no [estrutura do conjunto de dados](concepts-datasets-linked-services.md#dataset-structure) configuração:

* Byte[]
* Booleano
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

### <a name="explicit-data-type-conversion"></a>Conversão de tipo de dados explícito

Quando copiar dados para dados armazena com esquema fixo, por exemplo, SQL Server/Oracle, quando a origem e sink tem o tipo de diferente na mesma coluna, a conversão de tipo explícito deve ser declarada no lado da origem:

* Para a origem de ficheiro, por exemplo, CSV/Avro, a conversão de tipo deve ser declarada através de estrutura de origem com a lista de colunas completo (origem lado coluna nome e o sink de tipo de lado)
* Para uma origem relacional (por exemplo, SQL/Oracle), a conversão de tipo deve ser obtida com a conversão de tipo explícito na instrução de consulta.

## <a name="when-to-specify-dataset-structure"></a>Quando especificar o conjunto de dados "estrutura"

No abaixo cenários, "estrutura" no conjunto de dados é necessária:

* Aplicando [conversão de tipos de dados explícito](#explicit-data-type-conversion) para origens de ficheiros durante a cópia (conjunto de dados de entrada)
* Aplicando [mapeamento de colunas explícita](#explicit-column-mapping) durante a cópia (ambos de entrada e saída do conjunto de dados)
* Copiar da origem do Dynamics 365/CRM (conjunto de dados de entrada)
* Copiar para o Cosmos DB como objeto aninhado quando a origem não for ficheiros JSON (conjunto de dados de saída)

Na abaixo cenários, é aconselhável "estrutura" no conjunto de dados:

* Cópia de arquivo de texto sem cabeçalho (conjunto de dados de entrada). Pode especificar os nomes de coluna para alinhar com as colunas correspondentes do sink, para guardar a partir da configuração de mapeamento de colunas explícita de arquivo de texto.
* Copiar a partir de dados armazena com esquema flexível, por exemplo, Azure tabela/Cosmos DB (conjunto de dados de entrada), para garantir os dados esperados (colunas) que está a ser copiados em vez de copiar let atividade inferir esquema com base na linha superior (s) durante cada execução de atividade.


## <a name="next-steps"></a>Passos Seguintes
Consulte os outros artigos de atividade de cópia:

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Copie a tolerância a falhas de atividade](copy-activity-fault-tolerance.md)
- [Desempenho de atividade de cópia](copy-activity-performance.md)
