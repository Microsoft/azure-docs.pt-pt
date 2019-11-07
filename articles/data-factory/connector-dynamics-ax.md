---
title: Copiar dados do Dynamics AX usando o Azure Data Factory
description: Saiba como copiar dados do Dynamics AX para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 07edc284c29ca209ee20e5de390e8126993f4ce3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681023"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory"></a>Copiar dados do Dynamics AX usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados da origem do Dynamics AX. O artigo se baseia na [atividade de cópia no Azure data Factory](copy-activity-overview.md), que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector do Dynamics AX tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do Dynamics AX para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte da atividade de cópia como fontes e coletores, consulte [armazenamentos e formatos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, esse conector do Dynamics AX dá suporte à cópia de dados do Dynamics AX usando o **protocolo OData** com **autenticação de entidade de serviço**.

>[!TIP]
>Você também pode usar esse conector para copiar dados de **Finanças e operações do Dynamics 365**. Consulte o [método de autenticação](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication)e [suporte OData](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) do Dynamics 365.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que podem ser usadas para definir Data Factory entidades específicas ao conector do Dynamics AX.

## <a name="prerequisites"></a>Pré-requisitos

Para usar a autenticação de entidade de serviço, siga estas etapas:

1. Registre uma entidade de aplicativo no Azure Active Directory (AD do Azure) seguindo [registrar seu aplicativo com um locatário do Azure ad](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anote os seguintes valores, que você usa para definir o serviço vinculado:

    - ID da aplicação
    - Chave do aplicativo
    - ID do inquilino

2. Vá para Dynamics AX e conceda a essa entidade de serviço permissão apropriada para acessar seu Dynamics AX.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Dynamics AX:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** deve ser definida como **DynamicsAX**. |Sim |
| url | O ponto de extremidade OData da instância do Dynamics AX (ou Dynamics 365 Finance and Operations). |Sim |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque este campo como uma **SecureString** para armazená-lo com segurança no data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| vários | Especifique as informações do locatário (nome de domínio ou ID do locatário) em que seu aplicativo reside. Recupere-o passando o mouse no canto superior direito do portal do Azure. | Sim |
| aadResourceId | Especifique o recurso do AAD que você está solicitando para autorização. Por exemplo, se a URL do Dynamics for `https://sampledynamics.sandbox.operations.dynamics.com/data/`, o recurso do AAD correspondente geralmente será `https://sampledynamics.sandbox.operations.dynamics.com`. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode escolher Azure Integration Runtime ou um Integration Runtime hospedado internamente (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, o Azure Integration Runtime padrão será usado. |Não |

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

## <a name="dataset-properties"></a>Propriedades de DataSet

Esta seção fornece uma lista das propriedades com suporte pelo conjunto de um DataSet do Dynamics AX.

Para obter uma lista completa das seções e propriedades que estão disponíveis para definir conjuntos de os, consulte [conjuntos de valores e serviços vinculados](concepts-datasets-linked-services.md). 

Para copiar dados do Dynamics AX, defina a propriedade **Type** do conjunto de dado como **DynamicsAXResource**. As propriedades a seguir têm suporte:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** do conjunto de conjuntos deve ser definida como **DynamicsAXResource**. | Sim |
| Multi-Path | O caminho para a entidade do Dynamics AX OData. | Sim |

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

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Esta seção fornece uma lista das propriedades às quais a origem do Dynamics AX dá suporte.

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte [pipelines](concepts-pipelines-activities.md). 

### <a name="dynamics-ax-as-source"></a>Dynamics AX como fonte

Para copiar dados do Dynamics AX, defina o tipo de **fonte** na atividade de cópia como **DynamicsAXSource**. As propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** da fonte da atividade de cópia deve ser definida como **DynamicsAXSource**. | Sim |
| consulta | Opções de consulta OData para filtrar dados. Exemplo: `"?$select=Name,Description&$top=5"`.<br/><br/>**Observação**: o conector copia dados da URL combinada: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`. Para obter mais informações, consulte [componentes de URL do OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Não |

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


## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de armazenamentos de dados com suporte da atividade de cópia como fontes e coletores em Azure Data Factory, consulte [armazenamentos de dados e formatos com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
