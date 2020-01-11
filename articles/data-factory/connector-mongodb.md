---
title: Copiar dados do MongoDB
description: Saiba como copiar dados do Mongo DB para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
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
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75892512"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Copiar dados do MongoDB usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de um banco de dados MongoDB. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

>[!IMPORTANT]
>Versão do ADF esta nova versão do conector do MongoDB que fornece melhor suporte nativo do MongoDB. Se você estiver usando o conector do MongoDB anterior em sua solução com suporte no estado em que se encontra para compatibilidade com versões anteriores, consulte o artigo [conector do MongoDB (Herdado)](connector-mongodb-legacy.md) .

## <a name="supported-capabilities"></a>Capacidades suportadas

Você pode copiar dados de um banco de dados MongoDB para qualquer armazenamento de dado de coletor com suporte. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, esse conector do MongoDB dá suporte **a versões de até 3,4**.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector do MongoDB.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para o serviço vinculado do MongoDB:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo |A propriedade Type deve ser definida como: **MongoDbV2** |Sim |
| connectionString |Especifique a cadeia de conexão do MongoDB, por exemplo, `mongodb://[username:password@]host[:port][/[database][?options]]`. Consulte o [manual do MongoDB na cadeia de conexão](https://docs.mongodb.com/manual/reference/connection-string/) para obter mais detalhes. <br/><br /> Você também pode colocar uma senha em Azure Key Vault e efetuar pull do `password` configuração fora da cadeia de conexão. Consulte [armazenar credenciais em Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. |Sim |
| base de dados | Nome do banco de dados que você deseja acessar. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Saiba mais na seção de [pré-requisitos](#prerequisites) . Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

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

Para obter uma lista completa de seções e as propriedades que estão disponíveis para definir conjuntos de dados, consulte [conjuntos de dados e serviços ligados](concepts-datasets-linked-services.md). As propriedades a seguir têm suporte para o conjunto de conjuntos do MongoDB:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **MongoDbV2Collection** | Sim |
| collectionName |Nome da coleção no banco de dados MongoDB. |Sim |

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

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista das propriedades com suporte pela origem do MongoDB.

### <a name="mongodb-as-source"></a>MongoDB como fonte

As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como: **MongoDbV2Source** | Sim |
| filter | Especifica o filtro de seleção usando operadores de consulta. Para retornar todos os documentos em uma coleção, omita esse parâmetro ou passe um documento vazio ({}). | Não |
| cursorMethods.project | Especifica os campos a serem retornados nos documentos para projeção. Para retornar todos os campos nos documentos correspondentes, omita esse parâmetro. | Não |
| cursorMethods.sort | Especifica a ordem na qual a consulta retorna documentos correspondentes. Consulte [cursor. Sort ()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Não |
| cursorMethods.limit | Especifica o número máximo de documentos que o servidor retorna. Consulte [cursor. Limit ()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Não |
| cursorMethods.skip | Especifica o número de documentos a serem ignorados e de onde o MongoDB começa a retornar os resultados. Consulte [cursor. Skip ()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Não |
| batchSize | Especifica o número de documentos a serem retornados em cada lote da instância de resposta do MongoDB. Na maioria dos casos, modificar o tamanho do lote não afetará o usuário ou o aplicativo. Cosmos DB limita que cada lote não pode exceder 40 MB de tamanho, que é a soma do tamanho do batchSize do número de documentos, portanto, diminua esse valor se o tamanho do documento for grande. | Não<br/>(o padrão é **100**) |

>[!TIP]
>O ADF dá suporte ao consumo de documento BSON no **modo estrito**. Verifique se a consulta de filtro está no modo estrito, em vez do modo Shell. Mais descrição pode ser encontrada no [manual do MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

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

## <a name="export-json-documents-as-is"></a>Exportar documentos JSON como estão

Você pode usar este conector do MongoDB para exportar documentos JSON no estado em que se encontram de uma coleção do MongoDB para vários repositórios baseados em arquivo ou para Azure Cosmos DB. Para obter essa cópia independente de esquema, ignore a seção "estrutura" (também chamada de *esquema*) no conjunto de dados e no mapeamento de esquema na atividade de cópia.

## <a name="schema-mapping"></a>Mapeamento de esquema

Para copiar dados do MongoDB para o coletor de tabela, consulte [mapeamento de esquema](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
