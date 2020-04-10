---
title: Copiar dados do HubSpot utilizando a Azure Data Factory (Pré-visualização)
description: Saiba como copiar dados do HubSpot para lojas de dados de sink suportadas utilizando uma atividade de cópia num pipeline Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: jingwang
ms.openlocfilehash: cb0e20a96bebec22dff99d89c6be0a6d294c80a1
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992152"
---
# <a name="copy-data-from-hubspot-using-azure-data-factory-preview"></a>Copiar dados do HubSpot utilizando a Azure Data Factory (Pré-visualização)

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados do HubSpot. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!IMPORTANT]
> Este conector encontra-se atualmente em pré-visualização. Pode sacá-lo e dar-nos feedback. Se quiser realizar uma dependência em conectores de pré-visualização na sua solução, contacte o [Suporte do Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector HubSpot é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)


Pode copiar dados do HubSpot para qualquer loja de dados suportada. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

A Azure Data Factory fornece um controlador incorporado para permitir a conectividade, pelo que não necessita de instalar manualmente qualquer controlador utilizando este conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas do conector HubSpot.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado ao HubSpot:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **Hubspot** | Sim |
| clientId | O ID do cliente associado à sua aplicação HubSpot. Saiba como criar uma aplicação no HubSpot a partir [daqui.](https://developers.hubspot.com/docs/faq/how-do-i-create-an-app-in-hubspot) | Sim |
| clienteSecret | O segredo do cliente associado à sua aplicação HubSpot. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Sim |
| acessoToken | O sinal de acesso obtido ao autenticar inicialmente a sua integração OAuth. Saiba como obter acesso com a identificação do seu cliente e segredo a partir [daqui.](https://developers.hubspot.com/docs/methods/oauth2/get-access-and-refresh-tokens) Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Sim |
| refreshToken | O token de atualização obtido ao autenticar inicialmente a sua integração OAuth. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Sim |
| useEncryptedEndpoints | Especifica se os pontos finais da fonte de dados são encriptados usando HTTPS. O valor predefinido é true.  | Não |
| utilizaçãoVerificação de anfitriões | Especifica se deve exigir o nome de anfitrião no certificado do servidor para corresponder ao nome de anfitrião do servidor ao ligar-se ao TLS. O valor predefinido é true.  | Não |
| utilizaçãoPeerVerification | Especifica se deve verificar a identidade do servidor ao ligar-se através do TLS. O valor predefinido é true.  | Não |

**Exemplo:**

```json
{
    "name": "HubSpotLinkedService",
    "properties": {
        "type": "Hubspot",
        "typeProperties": {
            "clientId" : "<clientId>",
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            },
            "accessToken": {
                "type": "SecureString",
                "value": "<accessToken>"
            },
            "refreshToken": {
                "type": "SecureString",
                "value": "<refreshToken>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo conjuntos de [dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados HubSpot.

Para copiar dados do HubSpot, detete a propriedade do tipo do conjunto de dados para **HubspotObject**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **HubspotObject** | Sim |
| tableName | Nome da mesa. | Não (se for especificada a "consulta" na fonte de atividade) |

**Exemplo**

```json
{
    "name": "HubSpotDataset",
    "properties": {
        "type": "HubspotObject",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<HubSpot linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte do HubSpot.

### <a name="hubspotsource-as-source"></a>HubspotSource como fonte

Para copiar dados do HubSpot, delineie o tipo de origem na atividade de cópia para **HubspotSource**. As seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **HubspotSource** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler dados. Por exemplo: `"SELECT * FROM Companies where Company_Id = xxx"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromHubspot",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HubSpot input dataset name>",
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
                "type": "HubspotSource",
                "query": "SELECT * FROM Companies where Company_Id = xxx"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
