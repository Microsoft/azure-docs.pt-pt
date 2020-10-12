---
title: Copiar dados da Dynamics AX
description: Saiba como copiar dados da Dynamics AX para lojas de dados de sumidouros suportados utilizando uma atividade de cópia num pipeline da Azure Data Factory.
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
ms.date: 06/12/2020
ms.openlocfilehash: 23a486dfe1256cea46f6722873950ffcb1bde084
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84982701"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory"></a>Copiar dados da Dynamics AX utilizando a Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Copy Activity na Azure Data Factory para copiar dados da Dynamics AX source. O artigo baseia-se [na Copy Activity in Azure Data Factory,](copy-activity-overview.md)que apresenta uma visão geral da Atividade de Cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Dynamics AX é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Dynamics AX para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e sumidouros, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector Dynamics AX suporta a cópia de dados do Dynamics AX utilizando **o protocolo OData** com **a autenticação principal do serviço**.

>[!TIP]
>Também pode utilizar este conector para copiar dados da **Dynamics 365 Finanças e Operações.** Consulte o método de suporte e [autenticação](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication) [OData](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) da Dynamics 365 .

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que pode usar para definir entidades da Data Factory que são específicas do conector Dynamics AX.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar a autenticação principal do serviço, siga estes passos:

1. Registe uma entidade de candidatura no Azure Ative Directory (Azure AD) seguindo [o Registo da sua candidatura junto de um inquilino da AD Azure.](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant) Tome nota dos seguintes valores, que utiliza para definir o serviço ligado:

    - ID da Aplicação
    - Chave de aplicação
    - ID do inquilino

2. Vá ao Dynamics AX e conceda a este serviço a permissão adequada para aceder ao seu Dynamics AX.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado à Dynamics AX:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** deve ser definida para **DynamicsAX**. |Sim |
| url | O ponto final do Dynamics AX (ou Dynamics 365 Finance and Operations) é o ponto final do OData. |Sim |
| servicePrincipalId | Especifique a identificação do cliente da aplicação. | Sim |
| servicePrincipalKey | Especifique a chave da aplicação. Marque este campo como um **SecureString** para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Sim |
| inquilino | Especifique a informação do inquilino (nome de domínio ou ID do inquilino) sob a qual a sua aplicação reside. Recupere-o pairando sobre o rato no canto superior direito do portal Azure. | Sim |
| aadResourceId | Especifique o recurso AAD que está a solicitar para autorização. Por exemplo, se o seu URL Dinâmico `https://sampledynamics.sandbox.operations.dynamics.com/data/` for, o recurso AAD correspondente é geralmente `https://sampledynamics.sandbox.operations.dynamics.com` . | Sim |
| connectVia | O [Tempo de Execução de Integração](concepts-integration-runtime.md) para ligar à loja de dados. Pode escolher o Tempo de Execução da Integração Azure ou um Tempo de Execução de Integração auto-hospedado (se a sua loja de dados estiver localizada numa rede privada). Se não for especificado, utiliza-se o tempo de execução de integração Azure predefinido. |Não |

**Exemplo**

```json
{
    "name": "DynamicsAXLinkedService",
    "properties": {
        "type": "DynamicsAX",
        "typeProperties": {
            "url": "<Dynamics AX instance OData endpoint>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource, e.g. https://sampledynamics.sandbox.operations.dynamics.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}

```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Esta secção fornece uma lista de propriedades que o conjunto de dados Dynamics AX suporta.

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets e serviços ligados](concepts-datasets-linked-services.md). 

Para copiar dados da Dynamics AX, defina a propriedade **tipo** do conjunto de dados para **DynamicsAXResource**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** do conjunto de dados deve ser definida para **DynamicsAXResource**. | Sim |
| caminho | O caminho para a entidade Dynamics AX OData. | Sim |

**Exemplo**

```json
{
    "name": "DynamicsAXResourceDataset",
    "properties": {
        "type": "DynamicsAXResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Dynamics AX linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades de Atividade de Cópia

Esta secção fornece uma lista de propriedades que a fonte Dynamics AX suporta.

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md). 

### <a name="dynamics-ax-as-source"></a>Dynamics AX como fonte

Para copiar dados da Dynamics AX, desafine o tipo **de fonte** em Atividade de Cópia para **DynamicsAXSource**. As seguintes propriedades são suportadas na secção **origem** da Atividade de Cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** da fonte de Atividade de Cópia deve ser definida como **DynamicsAXSource**. | Sim |
| consulta | Opções de consulta OData para filtragem de dados. Exemplo: `"?$select=Name,Description&$top=5"`.<br/><br/>**Nota:** O conector copia os dados do URL combinado: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]` . Para obter mais informações, consulte [os componentes URL do OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Não |
| httpRequestTimeout | O tempo limite (o valor **TimeSpan)** para o pedido HTTP obter uma resposta. Este valor é o tempo limite para obter uma resposta, não o tempo limite para ler dados de resposta. Se não for especificado, o valor predefinido é **00:30:00** (30 minutos). | Não |

**Exemplo**

```json
"activities":[
    {
        "name": "CopyFromDynamicsAX",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics AX input dataset name>",
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
                "type": "DynamicsAXSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e sumidouros na Azure Data Factory, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)
