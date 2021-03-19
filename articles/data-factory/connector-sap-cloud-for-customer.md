---
title: Copiar dados de/para nuvem SAP para cliente
description: Saiba como copiar dados da SAP Cloud para o Cliente para lojas de dados de sumidouros suportados (ou) de lojas de dados de origem suportadas para SAP Cloud para Cliente utilizando a Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: b86559422b6efeed666a3ae35022563a66d9c7e6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597331"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Copiar dados da NUVEM SAP para Cliente (C4C) utilizando a Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de/para SAP Cloud para Cliente (C4C). Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

>[!TIP]
>Para obter o apoio global da ADF no cenário de integração de dados SAP, consulte [a integração de dados SAP utilizando o papel branco da Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução detalhada em cada conector SAP, comparação e orientação.

## <a name="supported-capabilities"></a>Capacidades suportadas

Esta nuvem SAP para conector de clientes é suportada para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da SAP Cloud para o Cliente para qualquer loja de dados de lavatórios suportados ou copiar dados de qualquer loja de dados de origem suportada para SAP Cloud para Cliente. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector permite que a Azure Data Factory copie dados de/para NUVEM SAP para cliente, incluindo a Nuvem SAP para Vendas, Nuvem SAP para Serviço e Nuvem SAP para soluções de Envolvimento Social.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas da NUVEM SAP para conector de clientes.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço SAP Cloud para o cliente ligado:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **SapCloudForCustomer**. | Yes |
| url | O URL do serviço SAP C4C OData. | Yes |
| nome de utilizador | Especifique o nome de utilizador para ligar ao SAP C4C. | Yes |
| palavra-passe | Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Yes |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. | No |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pela SAP Cloud para conjunto de dados do cliente.

Para copiar dados da SAP Cloud para Cliente, defina a propriedade tipo do conjunto de dados para **SapCloudForCustomerResource**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **SapCloudForCustomerResource** |Yes |
| caminho | Especificar o caminho da entidade SAP C4C OData. |Yes |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela SAP Cloud para fonte de cliente.

### <a name="sap-c4c-as-source"></a>SAP C4C como fonte

Para copiar dados da SAP Cloud para Cliente, deteta o tipo de origem na atividade de cópia para **SapCloudForCustomerSource**. As seguintes propriedades são suportadas na secção fonte de **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **SapCloudForCustomerSource**  | Yes |
| consulta | Especifique a consulta personalizada OData para ler dados. | No |
| httpRequestTimeout | O tempo limite (o valor **TimeSpan)** para o pedido HTTP obter uma resposta. Este valor é o tempo limite para obter uma resposta, não o tempo limite para ler dados de resposta. Se não for especificado, o valor predefinido é **00:30:00** (30 minutos). | No |

Consulta de amostra para obter dados para um dia específico: `"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

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

Para copiar dados para SAP Cloud para Cliente, desaperte o tipo de pia na atividade de cópia para **SapCloudForCustomerSink**. As seguintes propriedades são suportadas na secção de **lavatório** de atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **SapCloudForCustomerSink**  | Yes |
| escrever Comportamento | O comportamento de escrita da operação. Pode ser "Inserir", "Atualizar". | N.º Predefinitivo "Inserir". |
| escreverBatchSize | O tamanho do lote da operação de escrita. O tamanho do lote para obter o melhor desempenho pode ser diferente para diferentes tabelas ou servidores. | N.º Padrão 10. |
| maxConcurrentConnections |O limite superior das ligações simultâneas estabelecidas na loja de dados durante a atividade. Especifique um valor apenas quando pretende limitar ligações simultâneas.| No |

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

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Mapeamento do tipo de dados para nuvem SAP para cliente

Ao copiar dados da SAP Cloud para o Cliente, os seguintes mapeamentos são usados da NUVEM SAP para tipos de dados do Cliente para tipos de dados provisórios da Azure Data Factory. Consulte [os mapeamentos de schema e tipo de dados](copy-activity-schema-and-type-mapping.md) para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia.

| Tipo de dados SAP C4C OData | Tipo de dados provisórios da fábrica de dados |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Booleano |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double (Duplo) |
| Edm.Single | Único |
| Edm.Guid | GUID |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Tempo | TimeSpan |
| Edm.DateTimeOffset | Início de execução de tempo de data |


## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
