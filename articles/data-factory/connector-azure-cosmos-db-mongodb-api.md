---
title: Copiar dados de ou para a API do Azure Cosmos DB para o MongoDB com o Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de arquivos de dados de origem suportada para ou a partir da API do Azure Cosmos DB para o MongoDB para lojas de sink suportado com o Data Factory.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 82418c03039219adedf45828d769d278a14499ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61259729"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Copiar dados para ou a partir da API do Azure Cosmos DB para o MongoDB com o Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de e para a API do Azure Cosmos DB para o MongoDB. O artigo se baseia no [atividade de cópia no Azure Data Factory](copy-activity-overview.md), que apresenta uma visão geral da atividade de cópia.

>[!NOTE]
>Esse suporte apenas do conector copiar dados de/para API do Azure Cosmos DB para o MongoDB. Para a API de SQL, consulte [conector de API do Cosmos DB SQL](connector-azure-cosmos-db.md). Outros tipos de API não são agora suportados.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados a partir da API do Azure Cosmos DB para o MongoDB para qualquer arquivo de dados de sink suportados ou copiar os dados de qualquer arquivo de dados de origem suportada para a API do Azure Cosmos DB para o MongoDB. Para obter uma lista de dados armazena se a atividade de cópia suporta como origens e sinks, consulte [arquivos de dados e formatos suportados](copy-activity-overview.md#supported-data-stores-and-formats).

Pode usar a API do Azure Cosmos DB para o conector do MongoDB para:

- Copiar dados de e para o [API do Azure Cosmos DB para o MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction).
- Escrever para o Azure Cosmos DB como **inserir** ou **upsert**.
- Importar e exportar documentos JSON como-é ou copiar dados de ou para um conjunto de dados em tabela. Os exemplos incluem uma base de dados SQL e um ficheiro CSV. Para copiar documentos como-é a partir de ficheiros JSON ou para ou a partir de outra coleção do Azure Cosmos DB, consulte a secção importar ou exportar documentos JSON.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que pode usar para definir entidades do Data Factory que são específicas para a API do Azure Cosmos DB para o MongoDB.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para a API do Azure Cosmos DB para o serviço ligado do MongoDB:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade tem de ser definida como **CosmosDbMongoDbApi**. | Sim |
| connectionString |Especifique a cadeia de ligação para o Azure Cosmos DB API para MongoDB. Pode encontrá-lo no portal do Azure -> painel do Cosmos DB -> cadeia de ligação primária ou secundária, com o padrão de `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`. <br/><br />Marcar esse campo como um **SecureString** tipo armazena de forma segura no Data Factory. Também pode [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| base de dados | Nome da base de dados que pretende aceder. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a utilizar para ligar ao arquivo de dados. Pode usar o Runtime de integração do Azure ou um runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se esta propriedade não for especificada, é utilizada a predefinição de Runtime de integração do Azure. |Não |

**Exemplo**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
            },
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa de seções e as propriedades que estão disponíveis para definir conjuntos de dados, consulte [conjuntos de dados e serviços ligados](concepts-datasets-linked-services.md). As seguintes propriedades são suportadas para a API do Azure Cosmos DB para o conjunto de dados do MongoDB:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade do conjunto de dados tem de ser definida como **CosmosDbMongoDbApiCollection**. |Sim |
| CollectionName |O nome da coleção do Azure Cosmos DB. |Sim |

**Exemplo**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade copy

Esta seção fornece uma lista de propriedades que oferecem suporte a API do Azure Cosmos DB para a origem do MongoDB e de sink.

Para obter uma lista completa de seções e as propriedades que estão disponíveis para a definição de atividades, consulte [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>API do Azure Cosmos DB para o MongoDB como origem

As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade da origem de atividade de cópia tem de ser definida como **CosmosDbMongoDbApiSource**. |Sim |
| filtro | Especifica o filtro de seleção usando operadores de consulta. Para devolver todos os documentos numa coleção, omitir este parâmetro ou transmita um documento vazio ({}). | Não |
| cursorMethods.project | Especifica os campos a devolver em documentos para projeção. Para devolver todos os campos nos documentos correspondentes, omita este parâmetro. | Não |
| cursorMethods.sort | Especifica a ordem em que a consulta devolve documentos correspondentes. Consulte a [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Não |
| cursorMethods.limit | Especifica o número máximo de documentos, que o servidor devolve. Consulte a [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Não | 
| cursorMethods.skip | Especifica o número de documentos para ignorar e para onde o MongoDB começa a devolver resultados. Consulte a [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Não |
| batchSize | Especifica o número de documentos a devolver em cada lote da resposta de instância de MongoDB. Na maioria dos casos, modificar o tamanho do lote não afetarão o utilizador ou a aplicação. Limites do cosmos DB cada lote não pode exceder 40MB de tamanho, o que é a soma do número batchSize do tamanho dos documentos, por isso, reduza este valor se o tamanho do documento a ser grande. | Não<br/>(a predefinição é **100**) |

>[!TIP]
>Suporte do ADF consumir o documento BSON no **Strict modo**. Certifique-se de que a consulta de filtro está no modo de Strict em vez do modo de Shell. Descrição mais pode ser encontrada em [MongoDB manual](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Exemplo**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Cosmos DB's API for MongoDB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "CosmosDbMongoDbApiSource",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>API do Azure Cosmos DB para o MongoDB como sink

As seguintes propriedades são suportadas na atividade de cópia **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade do coletor de atividade de cópia tem de ser definida como **CosmosDbMongoDbApiSink**. |Sim |
| WriteBehavior |Descreve como escrever dados do Azure Cosmos DB. Valores permitidos: **inserir** e **upsert**.<br/><br/>O comportamento das **upsert** é substituir o documento se um documento com o mesmo ID já existe; caso contrário, insira o documento.<br /><br />**Nota**: Fábrica de dados gera automaticamente um ID de um documento se não for especificado um ID do documento original ou por mapeamento de colunas. Isso significa que é necessário garantir que, para **upsert** a funcionar conforme esperado, o seu documento tem um ID. |Não<br />(a predefinição é **inserir**) |
| writeBatchSize | O **writeBatchSize** propriedade controla o tamanho de documentos para escrever em cada lote. Pode experimentar aumentar o valor para **writeBatchSize** para melhorar o desempenho e a diminuição do valor se o tamanho do documento a ser grande. |Não<br />(a predefinição é **10.000**) |
| writeBatchTimeout | O tempo de espera para o lote de inserção operação seja concluída antes de atingir o tempo limite. O valor permitido é o período de tempo. | Não<br/>(a predefinição é **00:00 30:** - 30 minutos) |

**Exemplo**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

>[!TIP]
>Para importar documentos JSON como-é, consulte [importar ou exportar documentos JSON](#import-or-export-json-documents) secção; para copiar a partir dos dados em forma tabela, consulte [mapeamento de esquema](#schema-mapping).

## <a name="import-or-export-json-documents"></a>Importar ou exportar documentos JSON

Pode utilizar este conector do Azure Cosmos DB para facilmente:

* Importe documentos JSON de várias origens para o Azure Cosmos DB, incluindo a partir do armazenamento de Blobs do Azure, Azure Data Lake Store e outros arquivos baseados em ficheiros que suporte o Azure Data Factory.
* Exporte documentos JSON de uma coleção do Azure Cosmos DB para vários arquivos baseados em ficheiros.
* Copiar documentos entre duas coleções do Azure Cosmos DB como-é.

Para alcançar essa cópia sem esquema, ignore a "estrutura" (também denominado *esquema*) secção no conjunto de dados e o mapeamento de esquema na atividade de cópia.

## <a name="schema-mapping"></a>Mapeamento de esquema

Para copiar dados a partir da API do Azure Cosmos DB para o MongoDB para o tabular sink ou invertido, consulte [mapeamento de esquema](copy-activity-schema-and-type-mapping.md#schema-mapping).

Especificamente para gravação para o Cosmos DB, para se certificar de que a preencher o Cosmos DB com o ID de objeto correto a partir dos seus dados de origem, por exemplo, tem uma coluna de "id" na tabela de base de dados SQL e pretende utilizar o valor do que como o ID do documento na MongoDB para inserir/upsert , precisa definir o mapeamento de esquema apropriados, de acordo com a definição de modo strict do MongoDB (`_id.$oid`) como o seguinte:

![ID de mapa no sink do MongoDB](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Após a cópia de execução da atividade, abaixo BSON ObjectId é gerada de coletor:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Passos Seguintes

Para obter uma lista dos arquivos de dados que a atividade de cópia suporta como origens e sinks no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
