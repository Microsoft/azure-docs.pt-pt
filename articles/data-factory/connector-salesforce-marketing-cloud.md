---
title: Copiar dados da Salesforce Marketing Cloud
description: Saiba como copiar dados da Salesforce Marketing Cloud para lojas de dados de sumidouros suportados utilizando uma atividade de cópia num pipeline da Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/17/2020
ms.openlocfilehash: 161b81b196a1e178c7244845b25594440e6d6e1e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100369752"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory"></a>Copiar dados da Salesforce Marketing Cloud usando a Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados da Salesforce Marketing Cloud. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Salesforce Marketing Cloud é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Salesforce Marketing Cloud para qualquer loja de dados de sumidouro suportado. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

O conector Salesforce Marketing Cloud suporta a autenticação OAuth 2, e suporta tanto tipos de pacotes antigos como melhorados. O conector é construído em cima da [API de Marketing Salesforce Marketing REST](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/index-api.htm).

>[!NOTE]
>Este conector não suporta a recuperação de objetos personalizados ou extensões de dados personalizadas.

## <a name="getting-started"></a>Introdução

Pode criar um pipeline com atividade de cópia utilizando o modelo .NET SDK, Python SDK, Azure PowerShell, REST API ou Azure Resource Manager. Consulte o tutorial de [atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector Salesforce Marketing Cloud.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado à Salesforce Marketing Cloud:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **SalesforceMarketingCloud** | Yes |
| conexõesProperties | Um grupo de propriedades que define como se conectar com a Salesforce Marketing Cloud. | Yes |
| ***Em `connectionProperties` :*** | | |
| authenticationType | Especifica o método de autenticação a utilizar. Os valores permitidos são `Enhanced sts OAuth 2.0` ou `OAuth_2.0` .<br><br>O pacote legado Salesforce Marketing Cloud só `OAuth_2.0` suporta, enquanto o pacote melhorado precisa `Enhanced sts OAuth 2.0` . <br>Desde 1 de agosto de 2019, a Salesforce Marketing Cloud removeu a capacidade de criar pacotes antigos. Todos os novos pacotes são pacotes melhorados. | Yes |
| anfitrião | Para um pacote melhorado, o anfitrião deve ser o seu [subdomínio](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/your-subdomain-tenant-specific-endpoints.htm) que é representado por uma corda de 28 caracteres a começar pelas letras "mc", por `mc563885gzs27c5t9-63k636ttgm` exemplo. <br>Para o pacote legado, especifique `www.exacttargetapis.com` . | Yes |
| clientId | O ID do cliente associado à aplicação Salesforce Marketing Cloud.  | Yes |
| segredo de clientes | O segredo do cliente associado à aplicação Salesforce Marketing Cloud. Pode optar por marcar este campo como um SecureString para armazená-lo de forma segura em ADF, ou armazenar o segredo no Cofre da Chave Azure e deixar a atividade de cópia da ADF puxar daí ao executar a cópia de dados - saiba mais com as credenciais da [Loja no Key Vault.](store-credentials-in-key-vault.md) | Yes |
| useEncryptedEndpoints | Especifica se os pontos finais de origem de dados são encriptados usando HTTPS. O valor predefinido é true.  | No |
| useHostVerification | Especifica se deve exigir que o nome do anfitrião no certificado do servidor corresponda ao nome de anfitrião do servidor ao ligar o TLS. O valor predefinido é true.  | No |
| usePeerVerificação | Especifica se deve verificar a identidade do servidor ao ligar o TLS. O valor predefinido é true.  | No |

**Exemplo: utilização de autenticação STS OAuth 2 melhorada para pacote melhorado** 

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "connectionProperties": {
                "host": "<subdomain e.g. mc563885gzs27c5t9-63k636ttgm>",
                "authenticationType": "Enhanced sts OAuth 2.0",
                "clientId": "<clientId>",
                "clientSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}

```

**Exemplo: utilização da autenticação OAuth 2 para pacote legado** 

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "connectionProperties": {
                "host": "www.exacttargetapis.com",
                "authenticationType": "OAuth_2.0",
                "clientId": "<clientId>",
                "clientSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}

```

Se estava a utilizar o serviço salesforce Marketing Cloud com a seguinte carga útil, ele ainda é suportado como está, enquanto é sugerido que use o novo que vai para a frente, o que adiciona um suporte de pacote melhorado.

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "clientId": "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "useEncryptedEndpoints": true,
            "useHostVerification": true,
            "usePeerVerification": true
        }
    }
}

```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo Conjunto de Dados da Cloud marketing salesforce.

Para copiar dados da Salesforce Marketing Cloud, defina a propriedade tipo do conjunto de dados para **SalesforceMarketingCloudObject**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **SalesforceMarketingCloudObject** | Yes |
| tableName | O nome da mesa. | Não (se for especificada "consulta" na fonte de atividade) |

**Exemplo**

```json
{
    "name": "SalesforceMarketingCloudDataset",
    "properties": {
        "type": "SalesforceMarketingCloudObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SalesforceMarketingCloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de imóveis suportados pela fonte da Salesforce Marketing Cloud.

### <a name="salesforce-marketing-cloud-as-source"></a>Salesforce Marketing Cloud como fonte

Para copiar dados da Salesforce Marketing Cloud, deteta o tipo de origem na atividade de cópia para **SalesforceMarketingCloudSource**. As seguintes propriedades são suportadas na secção fonte de **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **SalesforceMarketingCloudSource** | Yes |
| consulta | Utilize a consulta SQL personalizada para ler dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceMarketingCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SalesforceMarketingCloud input dataset name>",
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
                "type": "SalesforceMarketingCloudSource",
                "query": "SELECT * FROM MyTable"
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
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
