---
title: Copiar dados do DB2 usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados do DB2 para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 033d25d6ff2da580523e13e25771faef14cfe5a9
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009446"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Copiar dados do DB2 usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-onprem-db2-connector.md)
> * [Versão atual](connector-db2.md)

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de um banco de dado DB2. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector de banco de dados DB2 tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de um banco de dados DB2 para qualquer armazenamento de dado de coletor com suporte. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, esse conector do DB2 dá suporte às seguintes plataformas e versões do IBM DB2 com o DRDA (Distributed Relacionative Database Architecture) versão 9, 10 e 11:

* IBM DB2 para z/OS 12,1
* IBM DB2 para z/OS 11,1
* IBM DB2 para z/OS 10,1
* IBM DB2 para i 7,3
* IBM DB2 para i 7,2
* IBM DB2 para i 7,1
* IBM DB2 para LUW 11
* IBM DB2 para LUW 10,5
* IBM DB2 para LUW 10,1

> [!TIP]
> Se você receber uma mensagem de erro afirmando "o pacote correspondente a uma solicitação de execução de instrução SQL não foi encontrado. SQLSTATE = 51002 SQLCODE =-805 ", o motivo é que um pacote necessário não é criado para o usuário normal nesse sistema operacional. Siga estas instruções de acordo com o tipo de servidor DB2:
> - DB2 para i (AS400): Deixe o usuário avançado criar a coleção para o usuário de logon antes de usar a atividade de cópia. Linha`create collection <username>`
> - DB2 para z/OS ou LUW: usar uma conta de alto privilégio – usuário avançado ou administrador com autoridades de pacote e BIND, BINDADD, conceder permissões executar para público-para executar a atividade de cópia uma vez, o pacote necessário é criado automaticamente durante a cópia. Posteriormente, você pode alternar de volta para o usuário normal para as execuções de cópia subsequentes.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

O Integration Runtime fornece um driver do DB2 interno, portanto, você não precisa instalar manualmente nenhum driver ao copiar dados do DB2.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector do DB2.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para o serviço vinculado do DB2:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como: **Db2** | Sim |
| server |Nome do servidor DB2. Você pode especificar o número da porta seguindo o nome do servidor delimitado por dois `server:port`-pontos, por exemplo,. |Sim |
| database |Nome do banco de dados DB2. |Sim |
| authenticationType |Tipo de autenticação usado para se conectar ao banco de dados DB2.<br/>O valor permitido é: **Básico**. |Sim |
| username |Especifique o nome de usuário para se conectar ao banco de dados DB2. |Sim |
| password |Especifique a senha para a conta de usuário especificada para o nome do usuário. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Saiba mais na seção de [pré-requisitos](#prerequisites) . Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

**Exemplo:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de banco de e do DB2.

Para copiar dados do DB2, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como: **Db2Table** | Sim |
| schema | Nome do esquema. |Não (se for especificada "query" na origem de atividade)  |
| table | Nome da tabela. |Não (se for especificada "query" na origem de atividade)  |
| tableName | Nome da tabela com esquema. Essa propriedade tem suporte para compatibilidade com versões anteriores. Use `schema` e`table` para uma nova carga de trabalho. | Não (se for especificada "query" na origem de atividade) |

**Exemplo**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Se você estivesse usando `RelationalTable` dataset tipado, ele ainda tem suporte como está, enquanto você é sugerido para usar o novo no futuro.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista das propriedades com suporte pela origem do DB2.

### <a name="db2-as-source"></a>DB2 como fonte

Para copiar dados do DB2, há suporte para as seguintes propriedades na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida como: **Db2Source** | Sim |
| query | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Não (se for especificado "tableName" no conjunto de dados) |

**Example:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se você estiver usando `RelationalSource` a fonte digitada, ainda haverá suporte como está, enquanto você é sugerido para usar a nova no futuro.

## <a name="data-type-mapping-for-db2"></a>Mapeamento de tipo de dados para DB2

Ao copiar dados do DB2, os seguintes mapeamentos são usados de tipos de dados do DB2 para Azure Data Factory tipos de dados provisórios. Ver [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de banco de dados DB2 | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
| BigInt |Int64 |
| Binary |Byte[] |
| Blob |Byte[] |
| Char |Cadeia |
| Clob |Cadeia |
| Date |Datetime |
| DB2DynArray |Cadeia |
| DbClob |Cadeia |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Graphic |Cadeia |
| Integer |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |Cadeia |
| LongVarGraphic |Cadeia |
| Numeric |Decimal |
| Real |Single |
| SmallInt |Int16 |
| Time |TimeSpan |
| Timestamp |DateTime |
| VarBinary |Byte[] |
| VarChar |Cadeia |
| VarGraphic |Cadeia |
| Xml |Byte[] |

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
