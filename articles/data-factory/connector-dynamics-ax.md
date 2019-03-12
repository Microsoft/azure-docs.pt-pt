---
title: Copiar dados do Dynamics AX com o Azure Data Factory (pré-visualização) | Documentos da Microsoft
description: Saiba como copiar dados do Dynamics AX para arquivos de dados de sink suportado através de uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: jingwang
ms.openlocfilehash: 05bd4fdd220b47b11dfed9857dbc8dbe25b236df
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57529798"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory-preview"></a>Copiar dados do Dynamics AX com o Azure Data Factory (pré-visualização)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de origem do Dynamics AX. O artigo se baseia no [atividade de cópia no Azure Data Factory](copy-activity-overview.md), que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados do Dynamics AX para qualquer arquivo de dados de sink suportados. Para obter uma lista de dados armazena se a atividade de cópia suporta como origens e sinks, consulte [arquivos de dados e formatos suportados](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do Dynamics AX suporta a cópia de dados de utilização do Dynamics AX **protocolo OData** com **autenticação do Principal de serviço**.

>[!TIP]
>Também pode utilizar este conector para copiar dados a partir **Dynamics 365 Finanças e operações**. Consulte do Dynamics 365 [suporte OData](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) e [método de autenticação](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que pode usar para definir entidades do Data Factory que são específicas para o conector do Dynamics AX.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar autenticação do principal de serviço, siga estes passos:

1. Registe-se uma entidade de aplicação no Azure Active Directory (Azure AD) ao seguir [registar a aplicação com um inquilino do Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Tome nota dos seguintes valores, o que utilizar para definir o serviço ligado:

    - ID da aplicação
    - Chave da aplicação
    - ID do Inquilino

2. Vá para o Dynamics AX e conceder este serviço principal permissão adequada para aceder ao seu Dynamics AX.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do Dynamics AX:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade tem de ser definida como **DynamicsAX**. |Sim |
| url | O Dynamics AX (ou Dynamics 365 Finanças e operações) instância ponto final de OData. |Sim |
| servicePrincipalId | Especifique o ID de cliente. da aplicação | Sim |
| servicePrincipalKey | Especifique a chave da aplicação. Marcar esse campo como um **SecureString** armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| inquilino | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Recuperá-la ao pairar o cursor do rato no canto superior direito do portal do Azure. | Sim |
| aadResourceId | Especifique o recurso do AAD que está a pedir para autorização. Por exemplo, se o seu URL do Dynamics for `https://sampledynamics.sandbox.operations.dynamics.com/data/`, o recurso do AAD correspondente é normalmente `https://sampledynamics.sandbox.operations.dynamics.com`. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a utilizar para ligar ao arquivo de dados. Pode escolher o Runtime de integração do Azure ou um Runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se não for especificado, é utilizada a predefinição de Runtime de integração do Azure. |Não |

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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Esta seção fornece uma lista de propriedades que suporta o conjunto de dados do Dynamics AX.

Para obter uma lista completa de seções e as propriedades que estão disponíveis para definir conjuntos de dados, consulte [conjuntos de dados e serviços ligados](concepts-datasets-linked-services.md). 

Para copiar dados do Dynamics AX, defina o **tipo** propriedade do conjunto de dados para **DynamicsAXResource**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade do conjunto de dados tem de ser definida como **DynamicsAXResource**. | Sim |
| caminho | O caminho para a entidade de OData do Dynamics AX. | Sim |

**Exemplo**

```json
{
    "name": "DynamicsAXResourceDataset",
    "properties": {
        "type": "DynamicsAXResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics AX linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade copy

Esta seção fornece uma lista de propriedades que suporta a origem de Dynamics AX.

Para obter uma lista completa de seções e as propriedades que estão disponíveis para a definição de atividades, consulte [Pipelines](concepts-pipelines-activities.md). 

### <a name="dynamics-ax-as-source"></a>Dynamics AX como origem

Para copiar dados do Dynamics AX, defina o **origem** tipo de atividade de cópia para **DynamicsAXSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade da origem de atividade de cópia tem de ser definida como **DynamicsAXSource**. | Sim |
| consulta | Opções de consulta de OData para filtrar os dados. Exemplo: `"?$select=Name,Description&$top=5"`.<br/><br/>**Nota**: O conector copia dados a partir do URL combinado: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`. Para obter mais informações, consulte [componentes do URL de OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Não |

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

## <a name="next-steps"></a>Passos Seguintes

Para obter uma lista dos arquivos de dados que a atividade de cópia suporta como origens e sinks no Azure Data Factory, veja [arquivos de dados e formatos suportados](copy-activity-overview.md##supported-data-stores-and-formats).
