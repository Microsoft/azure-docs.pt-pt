---
title: Copiar dados do Atlas mongoDB
description: Saiba como copiar dados do Atlas MongoDB para lojas de dados de sumidouros suportados utilizando uma atividade de cópia num pipeline da Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 09/28/2020
ms.openlocfilehash: b2f77e4bd8df66084937da3dd203ebb71d9a3511
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100368800"
---
# <a name="copy-data-from-mongodb-atlas-using-azure-data-factory"></a>Copiar dados do Atlas mongoDB usando a Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de uma base de dados do Atlas MongoDB. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados da base de dados do Atlas mongoDB para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector Atlas MongoDB suporta **versões até 4.2**.

## <a name="prerequisites"></a>Pré-requisitos

Se utilizar o Tempo de Execução da Integração Azure para cópia, certifique-se de adicionar os IPs de runtime de [integração Azure](azure-integration-runtime-ip-addresses.md) da região eficaz à Lista de Acesso IP do Atlas Do Atlas.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector Atlas MongoDB.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado ao Atlas MongoDB:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo |A propriedade tipo deve ser definida para: **MongoDbAtlas** |Yes |
| conexãoStragem |Especificar a cadeia de ligação Atlas MongoDB, por `mongodb+srv://<username>:<password>@<clustername>.<randomString>.<hostName>/<dbname>?<otherProperties>` exemplo. <br/><br /> Também pode colocar uma corda de ligação no Cofre da Chave Azure. Consulte [as credenciais da Loja no Cofre de Chaves Azure](store-credentials-in-key-vault.md) com mais detalhes. |Yes |
| base de dados | Nome da base de dados a que pretende aceder. | Yes |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. |No |

**Exemplo:**

```json
{
    "name": "MongoDbAtlasLinkedService",
    "properties": {
        "type": "MongoDbAtlas",
        "typeProperties": {
            "connectionString": "mongodb+srv://<username>:<password>@<clustername>.<randomString>.<hostName>/<dbname>?<otherProperties>",
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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets e serviços ligados](concepts-datasets-linked-services.md). As seguintes propriedades são suportadas para o conjunto de dados do Atlas MongoDB:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **MongoDbAtlasCollection** | Yes |
| coleçãoName |Nome da coleção na base de dados do Atlas mongoDB. |Yes |

**Exemplo:**

```json
{
    "name": "MongoDbAtlasDataset",
    "properties": {
        "type": "MongoDbAtlasCollection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB Atlas linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte do Atlas MongoDB.

### <a name="mongodb-atlas-as-source"></a>MongoDB Atlas como fonte

As seguintes propriedades são suportadas na secção fonte de **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **MongoDbAtlasSource** | Yes |
| filter | Especifica o filtro de seleção utilizando operadores de consulta. Para devolver todos os documentos numa coleção, omita este parâmetro ou passe um documento vazio ( {} ). | No |
| cursorMethods.project | Especifica os campos para retornar nos documentos para projeção. Para devolver todos os campos nos documentos correspondentes, omita este parâmetro. | No |
| cursorMethods.sort | Especifica a ordem na qual a consulta devolve documentos correspondentes. Consulte [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | No |
| cursorMethods.limite | Especifica o número máximo de documentos que o servidor devolve. Consulte [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | No |
| cursorMethods.skip | Especifica o número de documentos a saltar e de onde o Atlas mongoDB começa a devolver os resultados. Consulte [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | No |
| batchSize | Especifica o número de documentos a devolver em cada lote da resposta a partir da instância Do Atlas mongoDB. Na maioria dos casos, a modificação do tamanho do lote não afetará o utilizador nem a aplicação. Cosmos DB limita cada lote não pode exceder 40MB de tamanho, que é a soma do tamanho do loteSize número de documentos, por isso diminua este valor se o tamanho do seu documento for grande. | No<br/>(o padrão é **100)** |

>[!TIP]
>Suporte ADF a consumir documento BSON em **modo rígido**. Certifique-se de que a sua consulta de filtro está no modo Rígido em vez do modo Shell. Mais descrição pode ser encontrada no [manual do MongoDB.](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html)

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromMongoDbAtlas",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB Atlas input dataset name>",
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
                "type": "MongoDbAtlasSource",
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

Você pode usar este conector Atlas MongoDB para exportar documentos JSON como é de uma coleção Do Atlas MongoDB para várias lojas baseadas em arquivos ou para Azure Cosmos DB. Para obter tal cópia esquema-agnóstico, salte a secção "estrutura" (também chamada *de esquema)* no conjunto de dados e no mapeamento de esquemas na atividade da cópia.

## <a name="schema-mapping"></a>Mapeamento de Schema

Para copiar dados do Atlas mongoDB para a pia tabular, consulte o [mapeamento de esquemas](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
