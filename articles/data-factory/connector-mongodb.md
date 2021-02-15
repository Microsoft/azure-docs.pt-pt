---
title: Copiar dados do MongoDB
description: Saiba como copiar dados da Mongo DB para lojas de dados de sumidouros suportados utilizando uma atividade de cópia num pipeline da Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2021
ms.openlocfilehash: eae2d2adfe2cfdd7e47f2ace9ede9253003aa5bf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368783"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Copiar dados da MongoDB utilizando a Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de uma base de dados mongoDB. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

>[!IMPORTANT]
>A ADF lança esta nova versão do conector MongoDB que proporciona um melhor suporte nativo do MongoDB. Se estiver a utilizar o conector MongoDB anterior na sua solução suportada como é para retrocompatibilidade, consulte o artigo [do conector MongoDB (legado).](connector-mongodb-legacy.md)


## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados da base de dados mongoDB para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector MongoDB suporta **versões até 4.2**.


## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]


## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector MongoDB.


## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado a MongoDB:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo |A propriedade tipo deve ser definida para: **MongoDbV2** |Yes |
| conexãoStragem |Especificar a cadeia de ligação MongoDB, por `mongodb://[username:password@]host[:port][/[database][?options]]` exemplo. Consulte o [manual mongoDB sobre a cadeia de ligação](https://docs.mongodb.com/manual/reference/connection-string/) para obter mais detalhes. <br/><br /> Também pode colocar uma corda de ligação no Cofre da Chave Azure. Consulte [as credenciais da Loja no Cofre de Chaves Azure](store-credentials-in-key-vault.md) com mais detalhes. |Yes |
| base de dados | Nome da base de dados a que pretende aceder. | Yes |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. |No |

**Exemplo:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": "mongodb://[username:password@]host[:port][/[database][?options]]",
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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets e serviços ligados](concepts-datasets-linked-services.md). As seguintes propriedades são suportadas para conjunto de dados MongoDB:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do conjunto de dados deve ser definida para: **MongoDbV2Collection** | Yes |
| coleçãoName |Nome da coleção na base de dados mongoDB. |Yes |

**Exemplo:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```


## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte mongoDB.

### <a name="mongodb-as-source"></a>MongoDB como fonte

As seguintes propriedades são suportadas na secção fonte de **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **MongoDbV2Source** | Yes |
| filter | Especifica o filtro de seleção utilizando operadores de consulta. Para devolver todos os documentos numa coleção, omita este parâmetro ou passe um documento vazio ( {} ). | No |
| cursorMethods.project | Especifica os campos para retornar nos documentos para projeção. Para devolver todos os campos nos documentos correspondentes, omita este parâmetro. | No |
| cursorMethods.sort | Especifica a ordem na qual a consulta devolve documentos correspondentes. Consulte [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | No |
| cursorMethods.limite | Especifica o número máximo de documentos que o servidor devolve. Consulte [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | No |
| cursorMethods.skip | Especifica o número de documentos a saltar e de onde a MongoDB começa a devolver os resultados. Consulte [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | No |
| batchSize | Especifica o número de documentos a devolver em cada lote da resposta a partir da instância MongoDB. Na maioria dos casos, a modificação do tamanho do lote não afetará o utilizador nem a aplicação. Cosmos DB limita cada lote não pode exceder 40 MB de tamanho, que é a soma do tamanho do loteSize número de documentos, por isso diminua este valor se o tamanho do seu documento for grande. | No<br/>(o padrão é **100)** |

>[!TIP]
>Suporte ADF a consumir documento BSON em **modo rígido**. Certifique-se de que a sua consulta de filtro está no modo Rígido em vez do modo Shell. Mais descrição pode ser encontrada no [manual do MongoDB.](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html)

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbV2Source",
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


## <a name="export-json-documents-as-is"></a>Exportar documentos JSON como-is

Você pode usar este conector MongoDB para exportar documentos JSON como é de uma coleção MongoDB para várias lojas baseadas em arquivos ou para Azure Cosmos DB. Para obter tal cópia esquema-agnóstico, salte a secção "estrutura" (também chamada *de esquema)* no conjunto de dados e no mapeamento de esquemas na atividade da cópia.


## <a name="schema-mapping"></a>Mapeamento de Schema

Para copiar dados de MongoDB para pia tabular, consulte o [mapeamento de esquemas](copy-activity-schema-and-type-mapping.md#schema-mapping).


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
