---
title: Copiar dados de/para SAP Cloud para Cliente
description: Saiba como copiar dados do SAP Cloud para o Cliente para suportar lojas de dados (ou) de lojas de dados de origem suportada para SAP Cloud para Cliente utilizando data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: 1d3772a17d0429d9b3a5bf95d2060f2dfbbbafe1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418053"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Copiar dados da Nuvem SAP para Cliente (C4C) utilizando a Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de/para SAP Cloud para Cliente (C4C). Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

>[!TIP]
>Para conhecer o suporte global da ADF no cenário de integração de dados DoSAP, consulte a integração de [dados do SAP utilizando o livro branco da Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução detalhada, comparação e orientação.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector SAP Cloud para cliente é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados do SAP Cloud para o Cliente para qualquer loja de dados de sink suportado, ou copiar dados de qualquer loja de dados de origem suportada para sap Cloud para Cliente. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector permite à Azure Data Factory copiar dados de/para SAP Cloud para Cliente, incluindo a Nuvem SAP para Vendas, Nuvem SAP para Serviço e SAP Cloud para soluções de envolvimento social.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Fábrica de Dados específicas do SAP Cloud para o conector do Cliente.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o Serviço SAP Cloud para o serviço ligado ao Cliente:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo deve ser definida para: **SapCloudForCustomer**. | Sim |
| url | O URL do serviço SAP C4C OData. | Sim |
| o nome de utilizador | Especifique o nome do utilizador para ligar ao SAP C4C. | Sim |
| palavra-passe | Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Sim |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. Se não especificado, utiliza o tempo de funcionar de integração azure padrão. | Não para a fonte, sim para afundar |

>[!IMPORTANT]
>Para copiar dados no SAP Cloud para cliente, crie explicitamente [um IR Azure](create-azure-integration-runtime.md#create-azure-ir) com uma localização perto da sua Nuvem SAP para Cliente, e associe no serviço ligado como o seguinte exemplo:

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo conjuntos de [dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pela SAP Cloud para dataset do Cliente.

Para copiar dados da SAP Cloud para o Cliente, detete a propriedade do tipo do conjunto de dados para **SapCloudForCustomerResource**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **SapCloudForCustomerResource** |Sim |
| path | Especifique o caminho da entidade SAP C4C OData. |Sim |

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

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela SAP Cloud para fonte do Cliente.

### <a name="sap-c4c-as-source"></a>SAP C4C como fonte

Para copiar dados da Nuvem SAP para cliente, delineie o tipo de origem na atividade de cópia para **SapCloudForCustomerSource**. As seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **SapCloudForCustomerSource**  | Sim |
| consulta | Especifique a consulta personalizada do OData para ler dados. | Não |

Amostra de consulta para obter dados para um dia específico:`"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

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

### <a name="sap-c4c-as-sink"></a>SAP C4C como pia

Para copiar dados para SAP Cloud para Cliente, delineie o tipo de pia na atividade de cópia para **SapCloudForCustomerSink**. As seguintes propriedades são suportadas na secção de **sumidouro** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **SapCloudForCustomerSink**  | Sim |
| escreverComportamento | O comportamento de escrita da operação. Pode ser "Inserir", "Atualização". | Não. "Inserir". |
| escreverBatchSize | O tamanho do lote da operação de escrita. O tamanho do lote para obter o melhor desempenho pode ser diferente para diferentes tabelas ou servidores. | Não. Padrão 10. |

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

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Mapeamento de tipo de dados para Nuvem SAP para Cliente

Ao copiar dados da SAP Cloud para o Cliente, os seguintes mapeamentos são utilizados a partir de SAP Cloud para tipos de dados do Cliente para tipos de dados provisórios da Azure Data Factory. Consulte [schema e mapeamentos](copy-activity-schema-and-type-mapping.md) de tipo de dados para saber como a atividade de cópia mapeia o esquema de origem e o tipo de dados para a pia.

| Tipo de dados de dados sap C4C Odata | Tipo de dados provisórios da fábrica de dados |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Booleano |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Único |
| Edm.Guid | GUID |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Tempo | TimeSpan |
| Edm.DateTimeOffset | DataTimeOffset |


## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
