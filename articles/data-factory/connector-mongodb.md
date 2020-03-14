---
title: Copiar dados do MongoDB
description: Saiba como copiar dados do Mongo DB para lojas de dados de sink suportadas utilizando uma atividade de cópia num pipeline azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 08/12/2019
ms.openlocfilehash: a7bb74c09b45429a160a3ec481c23073575cfe3c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251716"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Copiar dados da MongoDB utilizando a Azure Data Factory

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de uma base de dados mongoDB. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

>[!IMPORTANT]
>A ADF lança esta nova versão do conector MongoDB que fornece um melhor suporte nativo mongoDB. Se estiver a utilizar o conector MongoDB anterior na sua solução, que é suportado como é para retrocompatibilidade, consulte o artigo do [conector MongoDB (legado).](connector-mongodb-legacy.md)

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados da base de dados do MongoDB para qualquer loja de dados suportada. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector MongoDB suporta **versões até 3.4**.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas do conector MongoDB.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado ao MongoDB:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade tipo deve ser definida para: **MongoDbV2** |Sim |
| connectionString |Especifique a cadeia de ligação MongoDB, por exemplo, `mongodb://[username:password@]host[:port][/[database][?options]]`. Consulte o [manual mongoDB sobre](https://docs.mongodb.com/manual/reference/connection-string/) a cadeia de ligação para obter mais detalhes. <br/><br /> Também pode colocar uma palavra-passe no Cofre de Chaves Azure e retirar a configuração   `password`da cadeia de ligação. Consulte [as credenciais da Loja no Cofre de Chaves Azure](store-credentials-in-key-vault.md) com mais detalhes. |Sim |
| base de dados | Nome da base de dados a que pretende aceder. | Sim |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets e serviços ligados.](concepts-datasets-linked-services.md) As seguintes propriedades são suportadas para dataset MongoDB:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **MongoDbV2Collection** | Sim |
| collectionName |Nome da coleção na base de dados mongoDB. |Sim |

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

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte mongoDB.

### <a name="mongodb-as-source"></a>MongoDB como fonte

As seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **MongoDbV2Source** | Sim |
| filter | Especifica o filtro de seleção utilizando operadores de consulta. Para devolver todos os documentos numa coleção, omita este parâmetro ou passe um documento vazio ({}). | Não |
| cursorMethods.project | Especifica os campos para devolver nos documentos para projeção. Para devolver todos os campos nos documentos correspondentes, omita este parâmetro. | Não |
| cursorMethods.sort | Especifica a ordem em que a consulta devolve documentos correspondentes. Consulte [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Não |
| cursorMethods.limit | Especifica o número máximo de documentos que o servidor devolve. Consulte [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Não |
| cursorMethods.skip | Especifica o número de documentos a saltar e de onde o MongoDB começa a devolver resultados. Consulte [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Não |
| batchSize | Especifica o número de documentos a devolver em cada lote da resposta da instância MongoDB. Na maioria dos casos, modificar o tamanho do lote não afetará o utilizador ou a aplicação. Os limites de DB cosmos de cada lote não podem exceder 40MB de tamanho, que é a soma do tamanho do loteTamanho do tamanho dos documentos, por isso diminua este valor se o tamanho do documento for grande. | Não<br/>(o padrão é **100)** |

>[!TIP]
>Suporte ADF consumindo documento BSON em **modo rígido**. Certifique-se de que a consulta do filtro está no modo Estrito em vez do modo Shell. Mais descrição pode ser encontrada no [manual do MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

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

## <a name="export-json-documents-as-is"></a>Exportar documentos JSON as-is

Você pode usar este conector MongoDB para exportar documentos JSON como é de uma coleção MongoDB para várias lojas baseadas em arquivos ou para Azure Cosmos DB. Para obter tal cópia schema-agnóstica, ignore a secção "estrutura" (também chamada *de esquema*) no conjunto de dados e no mapeamento de esquemas na atividade de cópia.

## <a name="schema-mapping"></a>Mapeamento de schema

Para copiar dados do MongoDB para a pia tabular, consulte o [mapeamento de esquemas](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
