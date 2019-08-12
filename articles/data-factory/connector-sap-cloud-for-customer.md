---
title: Copiar dados de/para o SAP Cloud for Customer usando Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados do SAP Cloud for Customer para armazenamentos de dados de coletor com suporte (ou) de armazenamentos de dados de origem com suporte para o SAP Cloud for Customer usando Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: jingwang
ms.openlocfilehash: 30025499ae3073a04863d711423bd9556e7fc6c4
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726026"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Copiar dados do SAP Cloud for Customer (C4C) usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de/para o SAP Cloud for Customer (C4C). Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Você pode copiar dados do SAP Cloud for Customer para qualquer armazenamento de dados de coletor com suporte ou copiar dados de qualquer armazenamento de dados de origem com suporte para o SAP Cloud for Customer. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, esse conector permite que o Azure Data Factory Copie dados de/para o SAP Cloud for Customer, incluindo a nuvem do SAP para vendas, o SAP Cloud for Service e as soluções de engajamento do SAP Cloud for social.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas do SAP Cloud for Customer Connector.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para o serviço vinculado do SAP Cloud for Customer:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como: **SapCloudForCustomer**. | Sim |
| url | A URL do serviço do SAP C4C OData. | Sim |
| username | Especifique o nome de usuário para se conectar ao SAP C4C. | Sim |
| password | Especifique a senha da conta de usuário que você especificou para o username. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Se não for especificado, ele usa o padrão do Runtime de integração do Azure. | Não para a origem, Sim para o sink |

>[!IMPORTANT]
>Para copiar dados para o SAP Cloud for Customer, [crie explicitamente um Azure ir](create-azure-integration-runtime.md#create-azure-ir) com um local perto de seu SAP Cloud for Customer e associe-se ao serviço vinculado como o exemplo a seguir:

**Example:**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de clientes do SAP Cloud for Customer.

Para copiar dados do SAP Cloud for Customer, defina a propriedade Type do conjunto de dado como **SapCloudForCustomerResource**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como: **SapCloudForCustomerResource** |Sim |
| path | Especifique o caminho da entidade do SAP C4C OData. |Sim |

**Example:**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typeProperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista das propriedades com suporte pela origem do SAP Cloud for Customer.

### <a name="sap-c4c-as-source"></a>SAP C4C como fonte

Para copiar dados do SAP Cloud for Customer, defina o tipo de fonte na atividade de cópia como **SapCloudForCustomerSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como: **SapCloudForCustomerSource**  | Sim |
| query | Especifique a consulta OData personalizada para ler os dados. | Não |

Exemplo de consulta para obter dados de um dia específico:`"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**Example:**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapCloudForCustomerSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>SAP C4C como coletor

Para copiar dados para o SAP Cloud for Customer, defina o tipo de coletor na atividade de cópia como **sapcloudforcustomersource**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como: **SapCloudForCustomerSink**  | Sim |
| writeBehavior | O comportamento de gravação da operação. Poderia ser "Insert", "Update". | Não. "Insert" padrão. |
| writeBatchSize | O tamanho do lote da operação de gravação. O tamanho do lote para obter o melhor desempenho pode ser diferente para uma tabela ou servidor diferente. | Não. Padrão 10. |

**Example:**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "dataIntegrationUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Mapeamento de tipo de dados para o SAP Cloud for Customer

Ao copiar dados do SAP Cloud for Customer, os seguintes mapeamentos são usados de tipos de dados do SAP Cloud for Customer para Azure Data Factory tipos de dados provisórios. Ver [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados do SAP C4C OData | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| EDM.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
