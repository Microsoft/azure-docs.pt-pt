---
title: Copiar dados da API da Azure Cosmos DB para o MongoDB
description: Saiba como copiar dados de lojas de dados de origem suportada de ou para a API da Azure Cosmos DB para a MongoDB para lojas de sink suportadas utilizando data factory.
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/20/2019
ms.openlocfilehash: 9b13920252b3a5626fd192c6e899154efd31a3de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75893249"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Copiar dados de/para a API para MongoDB do Azure Cosmos DB com o Azure Data Factory

Este artigo descreve como utilizar a Atividade de Cópia no Azure Data Factory para copiar dados de e para a API do Azure Cosmos DB para MongoDB. O artigo baseia-se [na Copy Activity na Azure Data Factory,](copy-activity-overview.md)que apresenta uma visão geral da Atividade de Cópia.

>[!NOTE]
>Este conector apenas suporta dados de cópia de/para a API da Azure Cosmos DB para MongoDB. Para SQL API, consulte o [conector Cosmos DB SQL API](connector-azure-cosmos-db.md). Outros tipos de API não são suportados agora.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados da API da Azure Cosmos DB para a MongoDB para qualquer loja de dados de sink suportado, ou copiar dados de qualquer loja de dados de origem suportada para a API da Azure Cosmos DB para a MongoDB. Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e afunda, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)

Você pode usar a API do Azure Cosmos DB para o conector MongoDB para:

- Copiar dados de e para a [API do Azure Cosmos DB para MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction).
- Escreva para o Azure Cosmos DB como **inserção** ou **upsert**.
- Importar e exportar documentos JSON como eis, ou copiar dados de ou para um conjunto de dados tabular. Exemplos incluem uma base de dados SQL e um ficheiro CSV. Para copiar documentos como é de ou para ficheiros JSON ou de ou para ou de outra coleção Azure Cosmos DB, consulte importar ou exportar documentos JSON.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que pode utilizar para definir entidades da Fábrica de Dados específicas da API da Azure Cosmos DB para o MongoDB.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para a API do Azure Cosmos DB para o serviço ligado mongoDB:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** deve ser definida para **CosmosDbMongoDbApi**. | Sim |
| conexãoString |Especifique a cadeia de ligação para a API do seu Azure Cosmos DB para MongoDB. Pode encontrá-lo no portal Azure -> a sua lâmina Cosmos DB -> `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`cadeia de ligação primária ou secundária, com o padrão de . <br/><br />Também pode colocar uma palavra-passe no `password` Cofre de Chaves Azure e retirar a configuração da cadeia de ligação.Consulte [as credenciais da Loja no Cofre](store-credentials-in-key-vault.md) de Chaves Azure com mais detalhes.|Sim |
| base de dados | Nome da base de dados a que pretende aceder. | Sim |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) para utilizar para ligar à loja de dados. Pode utilizar o Tempo de Execução de Integração Azure ou um tempo de execução de integração auto-hospedado (se a sua loja de dados estiver localizada numa rede privada). Se esta propriedade não for especificada, o tempo de execução de integração azure padrão é usado. |Não |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets e serviços ligados.](concepts-datasets-linked-services.md) As seguintes propriedades são suportadas para a API da Azure Cosmos DB para conjunto de dados MongoDB:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** do conjunto de dados deve ser definida para **CosmosDbMongoDbApiCollection**. |Sim |
| coleçãoNome |O nome da coleção Azure Cosmos DB. |Sim |

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

## <a name="copy-activity-properties"></a>Propriedades de Copy Activity

Esta secção fornece uma lista de propriedades que a API do Azure Cosmos DB para fonte mongoDB e suporte a sumidouro.

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>API da Azure Cosmos DB para mongoDB como fonte

As seguintes propriedades são suportadas na secção fonte de **origem** da Atividade de Cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **do tipo** da fonte de atividade de cópia deve ser definida para **CosmosDbMongoDbApiSource**. |Sim |
| filter | Especifica o filtro de seleção utilizando operadores de consulta. Para devolver todos os documentos numa coleção, omita{}este parâmetro ou passe um documento vazio ( ). | Não |
| cursorMethods.project | Especifica os campos para devolver nos documentos para projeção. Para devolver todos os campos nos documentos correspondentes, omita este parâmetro. | Não |
| cursorMethods.sort | Especifica a ordem em que a consulta devolve documentos correspondentes. Consulte [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Não |
| cursorMethods.limit | Especifica o número máximo de documentos que o servidor devolve. Consulte [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Não | 
| cursorMethods.skip | Especifica o número de documentos a saltar e de onde o MongoDB começa a devolver resultados. Consulte [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Não |
| batchSize | Especifica o número de documentos a devolver em cada lote da resposta da instância MongoDB. Na maioria dos casos, modificar o tamanho do lote não afetará o utilizador ou a aplicação. Os limites de DB cosmos de cada lote não podem exceder 40MB de tamanho, que é a soma do tamanho do loteTamanho do tamanho dos documentos, por isso diminua este valor se o tamanho do documento for grande. | Não<br/>(o padrão é **100)** |

>[!TIP]
>Suporte ADF consumindo documento BSON em **modo rígido**. Certifique-se de que a consulta do filtro está no modo Estrito em vez do modo Shell. Mais descrição pode ser encontrada no [manual do MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

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

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>API da Azure Cosmos DB para mongoDB como pia

As seguintes propriedades são suportadas na secção **de sink da** Copy Activity:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** do lavatório Copy Activity deve ser definida para **CosmosDbMongoDbApiSink**. |Sim |
| escreverComportamento |Descreve como escrever dados para o Azure Cosmos DB. Valores permitidos: **inserir** e **serrantes**.<br/><br/>O comportamento do **upsert** é substituir o documento `_id` se um documento com o mesmo já existir; caso contrário, insira o documento.<br /><br />**Nota:** Data Factory gera `_id` automaticamente um para `_id` um documento se um não for especificado no documento original ou por mapeamento de colunas. Isto significa que deve garantir que, para que o **upsert** funcione como esperado, o seu documento tem uma identificação. |Não<br />(a **inserção**por defeito por defeito) |
| escreverBatchSize | A propriedade **writeBatchSize** controla o tamanho dos documentos para escrever em cada lote. Pode tentar aumentar o valor para **a escritaBatchSize** para melhorar o desempenho e diminuir o valor se o tamanho do documento for grande. |Não<br />(o padrão é **de 10.000)** |
| escreverBatchTimeout | O tempo de espera para a operação de inserção do lote terminar antes de sair. O valor permitido é tempo de tempo. | Não<br/>(o padrão é **00:30:00** - 30 minutos) |

>[!TIP]
>Para importar os documentos jSON como eis, consulte a secção de [documentos De Importação ou Exportação jSON;](#import-and-export-json-documents) para copiar a partir de dados em forma de tabular, consulte o [mapeamento de Schema.](#schema-mapping)

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

## <a name="import-and-export-json-documents"></a>Documentos JSON de importação e exportação

Você pode usar este conector Azure Cosmos DB para facilmente:

* Copie documentos entre duas coleções Azure Cosmos DB como está.
* Importar documentos JSON de várias fontes para o Azure Cosmos DB, incluindo a partir de MongoDB, armazenamento Azure Blob, Azure Data Lake Store, e outras lojas baseadas em ficheiros que a Azure Data Factory suporta.
* Exportar documentos JSON de uma coleção Azure Cosmos DB para várias lojas baseadas em ficheiros.

Para obter uma cópia schema-agnóstica:

* Quando utilizar a ferramenta Copy Data, selecione o **exporto como está para ficheiros JSON ou** a opção de recolha cosmos DB.
* Quando utilizar a autoria da atividade, escolha o formato JSON com a loja de ficheiros correspondente para fonte ou pia.

## <a name="schema-mapping"></a>Mapeamento de schema

Para copiar dados da API da Azure Cosmos DB para o MongoDB para afundar tabular ou inverter, consulte o [mapeamento de esquemas](copy-activity-schema-and-type-mapping.md#schema-mapping).

Especificamente para escrever no Cosmos DB, para garantir que povoa o Cosmos DB com o ID do objeto certo a partir dos seus dados de origem, por exemplo, você tem uma coluna "id" na tabela de bases de dados`_id.$oid`SQL e quer usar o valor disso como id documento em MongoDB para inserir/upsert, você precisa definir o mapeamento de esquema adequado de acordo com a definição de modo rígido MongoDB () como o seguinte:

![Id do mapa na pia de MongoDB](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Após a execução da atividade de cópia, abaixo do BSON ObjectId é gerado em pia:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e afunda na Azure Data Factory, consulte as lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
