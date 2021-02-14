---
title: Copiar dados da API da Azure Cosmos para a MongoDB
description: Saiba como copiar dados de lojas de dados de origem suportadas de ou para a API da Azure Cosmos para a MongoDB para lojas de lavatórios suportadas utilizando a Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/20/2019
ms.openlocfilehash: 6f1e865daf9ba42126c0f8a341a54d87ac7f374a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393093"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Copiar dados de/para a API para MongoDB do Azure Cosmos DB com o Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia no Azure Data Factory para copiar dados de e para a API do Azure Cosmos DB para MongoDB. O artigo baseia-se [na Copy Activity in Azure Data Factory,](copy-activity-overview.md)que apresenta uma visão geral da Atividade de Cópia.

>[!NOTE]
>Este conector suporta apenas dados de cópia de/para AZure Cosmos DB's API para MongoDB. Para a API SQL, consulte o [conector API API da Cosmos DB SQL](connector-azure-cosmos-db.md). Outros tipos de API não são suportados agora.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados da API da Azure Cosmos DB para a MongoDB para qualquer loja de dados de lavatórios suportados, ou copiar dados de qualquer loja de dados de origem suportada para a API da Azure Cosmos DB para a MongoDB. Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e sumidouros, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)

Você pode usar a API da Azure Cosmos DB para:

- Copie os dados de e para a [API da Azure Cosmos DB para a MongoDB](../cosmos-db/mongodb-introduction.md).
- Escreva para Azure Cosmos DB como **inserção** ou **upsert**.
- Importar e exportar documentos JSON como é, ou copiar dados de ou para um conjunto de dados tabular. Exemplos incluem uma base de dados SQL e um ficheiro CSV. Para copiar documentos de ou para os ficheiros JSON ou de ou de outra coleção DB da Azure Cosmos, consulte documentos de Importação ou exportação de JSON.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que pode usar para definir entidades da Data Factory que são específicas da API da Azure Cosmos DB para a MongoDB.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para a API da Azure Cosmos DB para o serviço ligado à MongoDB:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **do tipo** deve ser definida para **CosmosDbMongoDbApi**. | Yes |
| conexãoStragem |Especifique a cadeia de ligação para a API da Azure Cosmos DB para o MongoDB. Pode encontrá-lo no portal Azure -> a sua lâmina Cosmos DB -> cadeia de ligação primária ou secundária, com o padrão de `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb` . <br/><br />Também pode colocar uma palavra-passe no Cofre da Chave Azure e retirar a `password` configuração da cadeia de ligação. Consulte [as credenciais da Loja no Cofre de Chaves Azure](store-credentials-in-key-vault.md) com mais detalhes.|Yes |
| base de dados | Nome da base de dados a que pretende aceder. | Yes |
| connectVia | O [Tempo de Execução de Integração](concepts-integration-runtime.md) para ligar à loja de dados. Pode utilizar o Tempo de Execução da Integração Azure ou um tempo de integração auto-hospedado (se a sua loja de dados estiver localizada numa rede privada). Se esta propriedade não for especificada, o tempo de execução de integração Azure predefinido é utilizado. |No |

**Exemplo**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb",
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets e serviços ligados](concepts-datasets-linked-services.md). As seguintes propriedades são suportadas para a API da Azure Cosmos DB para conjunto de dados MongoDB:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do **tipo** do conjunto de dados deve ser definida para **CosmosDbMongoDbApiCollection**. |Yes |
| coleçãoName |O nome da coleção Azure Cosmos DB. |Yes |

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

## <a name="copy-activity-properties"></a>Propriedades de Atividade de Cópia

Esta secção fornece uma lista de propriedades que a API da Azure Cosmos DB para a fonte mongoDB e suporte para o lavatório.

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>A API da Azure Cosmos DB para a MongoDB como fonte

As seguintes propriedades são suportadas na secção **origem** da Atividade de Cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A  propriedade tipo da fonte de atividade da cópia deve ser definida para **CosmosDbMongoDbApiSource**. |Yes |
| filter | Especifica o filtro de seleção utilizando operadores de consulta. Para devolver todos os documentos numa coleção, omita este parâmetro ou passe um documento vazio ( {} ). | No |
| cursorMethods.project | Especifica os campos para retornar nos documentos para projeção. Para devolver todos os campos nos documentos correspondentes, omita este parâmetro. | No |
| cursorMethods.sort | Especifica a ordem na qual a consulta devolve documentos correspondentes. Consulte [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | No |
| cursorMethods.limite |    Especifica o número máximo de documentos que o servidor devolve. Consulte [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | No | 
| cursorMethods.skip | Especifica o número de documentos a saltar e de onde a MongoDB começa a devolver os resultados. Consulte [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | No |
| batchSize | Especifica o número de documentos a devolver em cada lote da resposta a partir da instância MongoDB. Na maioria dos casos, a modificação do tamanho do lote não afetará o utilizador nem a aplicação. Cosmos DB limita cada lote não pode exceder 40MB de tamanho, que é a soma do tamanho do loteSize número de documentos, por isso diminua este valor se o tamanho do seu documento for grande. | No<br/>(o padrão é **100)** |

>[!TIP]
>Suporte ADF a consumir documento BSON em **modo rígido**. Certifique-se de que a sua consulta de filtro está no modo Rígido em vez do modo Shell. Mais descrição pode ser encontrada no [manual do MongoDB.](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html)

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

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>A AZure Cosmos DB's API para MongoDB como pia

As seguintes propriedades são suportadas na secção de **lavatório** Copy Activity:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do **tipo** do lavatório Copy Activity deve ser definida para **CosmosDbMongoDbApiSink**. |Yes |
| escrever Comportamento |Descreve como escrever dados para Azure Cosmos DB. Valores permitidos: **inserir** e **aumentar.**<br/><br/>O comportamento do **upsert** é substituir o documento se já existir um documento com o `_id` mesmo; caso contrário, insira o documento.<br /><br />**Nota:** A Data Factory gera automaticamente `_id` um documento para um documento se um não for especificado no documento original ou por `_id` mapeamento de colunas. Isto significa que deve garantir que, para que **o upsert** funcione como esperado, o seu documento tenha uma identificação. |No<br />(o padrão é **inserir)** |
| escreverBatchSize | A **propriedade writeBatchSize** controla o tamanho dos documentos para escrever em cada lote. Pode tentar aumentar o valor para **escreverBatchSize** para melhorar o desempenho e diminuir o valor se o tamanho do seu documento for grande. |No<br />(o padrão é **de 10.000)** |
| escreverBatchTimeout | O tempo de espera para o funcionamento do encaixe do lote terminar antes de se esgotar. O valor permitido é o tempo. | No<br/>(o padrão é **00:30:00** - 30 minutos) |

>[!TIP]
>Para importar documentos JSON como é, consulte a secção [de documentos de importação ou exportação de JSON;](#import-and-export-json-documents) para copiar a partir de dados em forma de tabular, consulte o [mapeamento de Schema](#schema-mapping).

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

## <a name="import-and-export-json-documents"></a>Importação e exportação de documentos JSON

Pode utilizar este conector Azure Cosmos DB facilmente:

* Copie documentos entre duas coleções DB da Azure Cosmos como está.
* Importar documentos da JSON de várias fontes para a Azure Cosmos DB, incluindo de MongoDB, armazenamento Azure Blob, Azure Data Lake Store, e outras lojas baseadas em ficheiros que a Azure Data Factory suporta.
* Exporte documentos JSON de uma coleção DB da Azure Cosmos para várias lojas baseadas em ficheiros.

Para obter cópia schema-agnóstica:

* Quando utilizar a ferramenta Dados de Cópia, selecione a **exportação como-é para ficheiros JSON ou opção de recolha Cosmos DB.**
* Quando utilizar a autoria da atividade, escolha o formato JSON com a loja de ficheiros correspondente para obter origem ou pia.

## <a name="schema-mapping"></a>Mapeamento de Schema

Para copiar os dados da API da Azure Cosmos para a MongoDB para afundar ou inverter, consulte o [mapeamento de esquemas](copy-activity-schema-and-type-mapping.md#schema-mapping).

Especificamente para escrever no Cosmos DB, para ter a certeza de que povoa o Cosmos DB com o iD do objeto certo a partir dos seus dados de origem, por exemplo, tem uma coluna de "id" na tabela de base de dados SQL e quer usar o valor disso como o documento ID em MongoDB para inserir/upsert, você precisa definir o mapa de esquema adequado de acordo com a definição de modo rígido mongoDB ( `_id.$oid` ) como o seguinte:

![ID do mapa na pia mongoDB](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Após a execução da atividade de cópia, abaixo bson ObjectId é gerado na pia:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e sumidouros na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)