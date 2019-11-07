---
title: Copiar dados de/para o SAP Cloud for Customer usando Azure Data Factory
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
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 5c47eead8bc8428f533374b2d4892ed684c1f3c7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680270"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Copiar dados do SAP Cloud for Customer (C4C) usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de/para o SAP Cloud for Customer (C4C). Ele se baseia no artigo [visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

>[!TIP]
>Para aprender o suporte geral do ADF no cenário de integração de dados do SAP, consulte [integração de dados SAP usando Azure data Factory White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução, análise e diretrizes detalhadas.

## <a name="supported-capabilities"></a>Recursos com suporte

Esse conector do SAP Cloud for Customer tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do SAP Cloud for Customer para qualquer armazenamento de dados de coletor com suporte ou copiar dados de qualquer armazenamento de dados de origem com suporte para o SAP Cloud for Customer. Para obter uma lista de armazenamentos de dados com suporte como fontes/coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

Especificamente, esse conector permite que o Azure Data Factory Copie dados de/para o SAP Cloud for Customer, incluindo a nuvem do SAP para vendas, o SAP Cloud for Service e as soluções de engajamento do SAP Cloud for social.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas do SAP Cloud for Customer Connector.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do SAP Cloud for Customer:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **SapCloudForCustomer**. | Sim |
| url | A URL do serviço do SAP C4C OData. | Sim |
| o nome de utilizador | Especifique o nome de usuário para se conectar ao SAP C4C. | Sim |
| palavra-passe | Especifique a senha da conta de usuário que você especificou para o username. Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Se não for especificado, ele usará o Azure Integration Runtime padrão. | Não para fonte, sim para coletor |

>[!IMPORTANT]
>Para copiar dados para o SAP Cloud for Customer, [crie explicitamente um Azure ir](create-azure-integration-runtime.md#create-azure-ir) com um local perto de seu SAP Cloud for Customer e associe-se ao serviço vinculado como o exemplo a seguir:

**Exemplo:**

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

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . Esta seção fornece uma lista das propriedades com suporte pelo conjunto de clientes do SAP Cloud for Customer.

Para copiar dados do SAP Cloud for Customer, defina a propriedade Type do conjunto de dado como **SapCloudForCustomerResource**. As propriedades a seguir têm suporte:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **SapCloudForCustomerResource** |Sim |
| Multi-Path | Especifique o caminho da entidade do SAP C4C OData. |Sim |

**Exemplo:**

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

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela origem do SAP Cloud for Customer.

### <a name="sap-c4c-as-source"></a>SAP C4C como fonte

Para copiar dados do SAP Cloud for Customer, defina o tipo de fonte na atividade de cópia como **SapCloudForCustomerSource**. As propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **SapCloudForCustomerSource**  | Sim |
| consulta | Especifique a consulta OData personalizada para ler os dados. | Não |

Exemplo de consulta para obter dados de um dia específico: `"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**Exemplo:**

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

Para copiar dados para o SAP Cloud for Customer, defina o tipo de coletor na atividade de cópia como **sapcloudforcustomersource**. As propriedades a seguir têm suporte na seção **coletor** de atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **sapcloudforcustomersource**  | Sim |
| WriteBehavior | O comportamento de gravação da operação. Poderia ser "Insert", "Update". | Não. "Insert" padrão. |
| writeBatchSize | O tamanho do lote da operação de gravação. O tamanho do lote para obter o melhor desempenho pode ser diferente para uma tabela ou servidor diferente. | Não. Padrão 10. |

**Exemplo:**

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

Ao copiar dados do SAP Cloud for Customer, os seguintes mapeamentos são usados de tipos de dados do SAP Cloud for Customer para Azure Data Factory tipos de dados provisórios. Consulte [mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e o esquema de origem para o coletor.

| Tipo de dados do SAP C4C OData | Tipo de dados provisório do data Factory |
|:--- |:--- |
| EDM. Binary | Byte [] |
| Edm.Boolean | bool |
| EDM. byte | Byte [] |
| EDM. DateTime | DateTime |
| EDM. decimal | Vírgula |
| Edm.Double | Clique |
| EDM. single | Único |
| EDM. GUID | GUID |
| EDM. Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| EDM. SByte | Int16 |
| Edm.String | String |
| EDM.Time | Período |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
