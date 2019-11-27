---
title: Copiar dados de ou para a API do Azure Cosmos DB para o MongoDB usando Data Factory
description: Saiba como copiar dados de armazenamentos de dados de origem com suporte de ou para a API do Azure Cosmos DB para o MongoDB para armazenamentos de coletor com suporte usando Data Factory.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: 0da3452964a7c4bb7d2a22ce4cd5164ad8c1e3fb
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280695"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Copiar dados de ou para a API do Azure Cosmos DB para o MongoDB usando Azure Data Factory

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de e para a API de Azure Cosmos DB para MongoDB. O artigo se baseia na [atividade de cópia no Azure data Factory](copy-activity-overview.md), que apresenta uma visão geral da atividade de cópia.

>[!NOTE]
>Esse conector só dá suporte à cópia de dados de/para a API do Azure Cosmos DB para MongoDB. Para a API do SQL, consulte [Cosmos DB conector de API do SQL](connector-azure-cosmos-db.md). Não há suporte para outros tipos de API agora.

## <a name="supported-capabilities"></a>Capacidades suportadas

Você pode copiar dados da API do Azure Cosmos DB para o MongoDB para qualquer armazenamento de dados de coletor com suporte ou copiar dados de qualquer armazenamento de dados de origem com suporte para a API do Azure Cosmos DB para o MongoDB. Para obter uma lista de armazenamentos de dados com suporte da atividade de cópia como fontes e coletores, consulte [armazenamentos e formatos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Você pode usar a API do Azure Cosmos DB para o conector do MongoDB para:

- Copie dados de e para a [API do Azure Cosmos DB para MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction).
- Gravar em Azure Cosmos DB como **Insert** ou **Upsert**.
- Importar e exportar documentos JSON como-é ou copiar dados de ou para um conjunto de dados em tabela. Os exemplos incluem uma base de dados SQL e um ficheiro CSV. Para copiar documentos como estão para ou de arquivos JSON ou de outra coleção de Azure Cosmos DB, consulte importar ou exportar documentos JSON.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que podem ser usadas para definir Data Factory entidades específicas para a API do Azure Cosmos DB para MongoDB.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para a API do Azure Cosmos DB para o serviço vinculado do MongoDB:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** deve ser definida como **CosmosDbMongoDbApi**. | Sim |
| connectionString |Especifique a cadeia de conexão para a API de seu Azure Cosmos DB para MongoDB. Você pode encontrá-lo no portal do Azure-> sua Cosmos DB folha de conexão > primária ou secundária, com o padrão de `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`. <br/><br />Marque este campo como um tipo **SecureString** para armazená-lo com segurança em data Factory. Você também pode [fazer referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| base de dados | Nome do banco de dados que você deseja acessar. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Pode usar o Runtime de integração do Azure ou um runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se esta propriedade não for especificada, é utilizada a predefinição de Runtime de integração do Azure. |Não |

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

Para obter uma lista completa das seções e propriedades que estão disponíveis para definir conjuntos de os, consulte [conjuntos de valores e serviços vinculados](concepts-datasets-linked-services.md). As propriedades a seguir têm suporte para a API de Azure Cosmos DB para o conjunto de conjuntos do MongoDB:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** do conjunto de conjuntos deve ser definida como **CosmosDbMongoDbApiCollection**. |Sim |
| collectionName |O nome da coleção de Azure Cosmos DB. |Sim |

**Exemplo**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "typeProperties": {
            "collectionName": "<collection name>"
        },
        "schema": [],
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade copy

Esta seção fornece uma lista das propriedades que a API do Azure Cosmos DB para suporte de origem e coletor do MongoDB.

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte [pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>API do Azure Cosmos DB para MongoDB como fonte

As propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** da fonte da atividade de cópia deve ser definida como **CosmosDbMongoDbApiSource**. |Sim |
| filter | Especifica o filtro de seleção usando operadores de consulta. Para retornar todos os documentos em uma coleção, omita esse parâmetro ou passe um documento vazio ({}). | Não |
| cursorMethods.project | Especifica os campos a serem retornados nos documentos para projeção. Para retornar todos os campos nos documentos correspondentes, omita esse parâmetro. | Não |
| cursorMethods.sort | Especifica a ordem na qual a consulta retorna documentos correspondentes. Consulte [cursor. Sort ()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Não |
| cursorMethods.limit | Especifica o número máximo de documentos que o servidor retorna. Consulte [cursor. Limit ()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Não | 
| cursorMethods.skip | Especifica o número de documentos a serem ignorados e de onde o MongoDB começa a retornar os resultados. Consulte [cursor. Skip ()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Não |
| batchSize | Especifica o número de documentos a serem retornados em cada lote da instância de resposta do MongoDB. Na maioria dos casos, modificar o tamanho do lote não afetará o usuário ou o aplicativo. Cosmos DB limita que cada lote não pode exceder 40 MB de tamanho, que é a soma do tamanho do batchSize do número de documentos, portanto, diminua esse valor se o tamanho do documento for grande. | Não<br/>(o padrão é **100**) |

>[!TIP]
>O ADF dá suporte ao consumo de documento BSON no **modo estrito**. Verifique se a consulta de filtro está no modo estrito, em vez do modo Shell. Mais descrição pode ser encontrada no [manual do MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

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

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>API do Azure Cosmos DB para MongoDB como coletor

As propriedades a seguir têm suporte na seção **coletor** de atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** do coletor da atividade de cópia deve ser definida como **CosmosDbMongoDbApiSink**. |Sim |
| writeBehavior |Descreve como escrever dados do Azure Cosmos DB. Valores permitidos: **Insert** e **Upsert**.<br/><br/>O comportamento de **Upsert** é substituir o documento se já existir um documento com o mesmo `_id`; caso contrário, insira o documento.<br /><br />**Observação**: data Factory gera automaticamente um `_id` para um documento se um `_id` não é especificado no documento original ou no mapeamento de coluna. Isso significa que você deve garantir que, para **Upsert** funcione conforme o esperado, o documento tem uma ID. |Não<br />(o padrão é **Inserir**) |
| writeBatchSize | A propriedade **writeBatchSize** controla o tamanho dos documentos a serem gravados em cada lote. Você pode tentar aumentar o valor de **writeBatchSize** para melhorar o desempenho e diminuir o valor se o tamanho do documento for grande. |Não<br />(o padrão é **10.000**) |
| writeBatchTimeout | O tempo de espera para que a operação de inserção em lote seja concluída antes de atingir o tempo limite. O valor permitido é TimeSpan. | Não<br/>(o padrão é **00:30:00** -30 minutos) |

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
>Para importar documentos JSON no estado em que se encontram, consulte a seção [importar ou exportar documentos JSON](#import-or-export-json-documents) ; para copiar de dados em formato de tabela, consulte [mapeamento de esquema](#schema-mapping).

## <a name="import-or-export-json-documents"></a>Importar ou exportar documentos JSON

Pode utilizar este conector do Azure Cosmos DB para facilmente:

* Importe documentos JSON de várias origens para o Azure Cosmos DB, incluindo a partir do armazenamento de Blobs do Azure, Azure Data Lake Store e outros arquivos baseados em ficheiros que suporte o Azure Data Factory.
* Exporte documentos JSON de uma coleção do Azure Cosmos DB para vários arquivos baseados em ficheiros.
* Copiar documentos entre duas coleções do Azure Cosmos DB como-é.

Para obter essa cópia independente de esquema, ignore a seção "estrutura" (também chamada de *esquema*) no conjunto de dados e no mapeamento de esquema na atividade de cópia.

## <a name="schema-mapping"></a>Mapeamento de esquema

Para copiar dados da API do Azure Cosmos DB para o MongoDB para o coletor tabular ou vice-versa, consulte [mapeamento de esquema](copy-activity-schema-and-type-mapping.md#schema-mapping).

Especificamente para gravar em Cosmos DB, para certificar-se de preencher Cosmos DB com a ID de objeto correta de seus dados de origem, por exemplo, você tem uma coluna "ID" na tabela do banco de dados SQL e deseja usar o valor dela como a ID do documento no MongoDB para Insert/Upsert , você precisa definir o mapeamento de esquema adequado de acordo com a definição de modo estrito do MongoDB (`_id.$oid`) da seguinte maneira:

![ID do mapa no coletor do MongoDB](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Após a execução da atividade de cópia, abaixo do ObjectId de BSON é gerado no coletor:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de armazenamentos de dados com suporte da atividade de cópia como fontes e coletores no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
