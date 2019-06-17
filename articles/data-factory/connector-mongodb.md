---
title: Copiar dados do MongoDB com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de Mongo DB para arquivos de dados de sink suportado através de uma atividade de cópia num pipeline do Azure Data Factory.
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
ms.openlocfilehash: ca6040bb74839f30a2f1b13297f6037f05240c67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61400445"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Copiar dados do MongoDB com o Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de uma base de dados do MongoDB. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

>[!IMPORTANT]
>Esta nova versão do conector do MongoDB que fornece melhor suporte nativo do MongoDB de versão do ADF. Se estiver a utilizar o conector do MongoDB anterior na sua solução que é suportada como-é para compatibilidade com versões anteriores, consulte [conector do MongoDB (Legado)](connector-mongodb-legacy.md) artigo.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados da base de dados do MongoDB para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector do MongoDB suporta **versões até 3.4**.

## <a name="prerequisites"></a>Pré-requisitos

Para copiar dados de uma base de dados do MongoDB que não está acessível ao público, terá de configurar um Runtime de integração autoalojado. Ver [Integration Runtime autoalojado](create-self-hosted-integration-runtime.md) artigo para saber mais detalhes.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector do MongoDB.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do MongoDB:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type |A propriedade de tipo tem de ser definida como: **MongoDbV2** |Sim |
| connectionString |Por exemplo, a especificar a cadeia de ligação do MongoDB `mongodb://[username:password@]host[:port][/[database][?options]]`. Consulte a [MongoDB manual na cadeia de ligação](https://docs.mongodb.com/manual/reference/connection-string/) para obter mais detalhes. <br/><br />Marcar esse campo como um **SecureString** tipo armazena de forma segura no Data Factory. Também pode [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| database | Nome da base de dados que pretende aceder. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração autoalojado ou Runtime de integração do Azure (se o seu armazenamento de dados está acessível ao público). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

**Exemplo:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://[username:password@]host[:port][/[database][?options]]"
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

Para obter uma lista completa de seções e as propriedades que estão disponíveis para definir conjuntos de dados, consulte [conjuntos de dados e serviços ligados](concepts-datasets-linked-services.md). As seguintes propriedades são suportadas para o conjunto de dados do MongoDB:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados deve ser definida como: **MongoDbV2Collection** | Sim |
| collectionName |Nome da coleção na base de dados do MongoDB. |Sim |

**Exemplo:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas por origem do MongoDB.

### <a name="mongodb-as-source"></a>MongoDB como origem

As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de origem de atividade de cópia tem de ser definida: **MongoDbV2Source** | Sim |
| filter | Especifica o filtro de seleção usando operadores de consulta. Para devolver todos os documentos numa coleção, omitir este parâmetro ou transmita um documento vazio ({}). | Não |
| cursorMethods.project | Especifica os campos a devolver em documentos para projeção. Para devolver todos os campos nos documentos correspondentes, omita este parâmetro. | Não |
| cursorMethods.sort | Especifica a ordem em que a consulta devolve documentos correspondentes. Consulte a [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Não |
| cursorMethods.limit | Especifica o número máximo de documentos, que o servidor devolve. Consulte a [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Não |
| cursorMethods.skip | Especifica o número de documentos para ignorar e para onde o MongoDB começa a devolver resultados. Consulte a [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Não |
| batchSize | Especifica o número de documentos a devolver em cada lote da resposta de instância de MongoDB. Na maioria dos casos, modificar o tamanho do lote não afetarão o utilizador ou a aplicação. Limites do cosmos DB cada lote não pode exceder 40MB de tamanho, o que é a soma do número batchSize do tamanho dos documentos, por isso, reduza este valor se o tamanho do documento a ser grande. | Não<br/>(a predefinição é **100**) |

>[!TIP]
>Suporte do ADF consumir o documento BSON no **Strict modo**. Certifique-se de que a consulta de filtro está no modo de Strict em vez do modo de Shell. Descrição mais pode ser encontrada em [MongoDB manual](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

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

## <a name="export-json-documents-as-is"></a>Exportar documentos JSON como-é

Pode utilizar este conector do MongoDB para exportar documentos JSON como-é a partir de uma coleção do MongoDB para vários arquivos baseados em ficheiros ou ao Azure Cosmos DB. Para alcançar essa cópia sem esquema, ignore a "estrutura" (também denominado *esquema*) secção no conjunto de dados e o mapeamento de esquema na atividade de cópia.

## <a name="schema-mapping"></a>Mapeamento de esquema

Para copiar dados do MongoDB para o sink de tabela, consulte [mapeamento de esquema](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
