---
title: Copiar dados de Google AdWords com o Azure Data Factory (pré-visualização) | Documentos da Microsoft
description: Saiba como copiar dados do Google AdWords para arquivos de dados de sink suportado através de uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: 0f68627e2db3c08049f0273045906057526bd6aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094923"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory-preview"></a>Copiar dados de Google AdWords com o Azure Data Factory (pré-visualização)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados do Google AdWords. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

> [!IMPORTANT]
> Este conector está atualmente em pré-visualização. Pode experimentá-lo e fornecer comentários. Se quiser realizar uma dependência em conectores de pré-visualização na sua solução, contacte o [Suporte do Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados do Google AdWords para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

O Azure Data Factory fornece um driver incorporado para permitir a conectividade, portanto não precisa de instalar manualmente a qualquer driver utilizar este conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector do Google AdWords.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do Google AdWords:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo tem de ser definida como: **GoogleAdWords** | Sim |
| clientCustomerID | O ID de cliente de cliente da conta AdWords que pretende obter os dados de relatório para.  | Sim |
| developerToken | O token de desenvolvedor associado à conta de gestor que utiliza para conceder acesso à API do AdWords.  Pode optar por marcar esse campo como uma SecureString armazena de forma segura no ADF ou armazenar a palavra-passe no Azure Key Vault e permitir que o ADF copiar extração de atividade a partir daí, quando efetuar a cópia de dados – Saiba mais no [Store credenciais no Key Vault](store-credentials-in-key-vault.md). | Sim |
| authenticationType | O mecanismo de autenticação OAuth 2.0 utilizado para autenticação. ServiceAuthentication só pode ser utilizado em Ir autoalojado. <br/>Valores permitidos são: **ServiceAuthentication**, **UserAuthentication** | Sim |
| refreshToken | O token de atualização obtido a partir do Google para autorizar o acesso a AdWords para UserAuthentication. Pode optar por marcar esse campo como uma SecureString armazena de forma segura no ADF ou armazenar a palavra-passe no Azure Key Vault e permitir que o ADF copiar extração de atividade a partir daí, quando efetuar a cópia de dados – Saiba mais no [Store credenciais no Key Vault](store-credentials-in-key-vault.md). | Não |
| clientId | O id de cliente do google aplicativo usado para adquirir o token de atualização. Pode optar por marcar esse campo como uma SecureString armazena de forma segura no ADF ou armazenar a palavra-passe no Azure Key Vault e permitir que o ADF copiar extração de atividade a partir daí, quando efetuar a cópia de dados – Saiba mais no [Store credenciais no Key Vault](store-credentials-in-key-vault.md). | Não |
| clientSecret | O segredo do cliente do google aplicativo usado para adquirir o token de atualização. Pode optar por marcar esse campo como uma SecureString armazena de forma segura no ADF ou armazenar a palavra-passe no Azure Key Vault e permitir que o ADF copiar extração de atividade a partir daí, quando efetuar a cópia de dados – Saiba mais no [Store credenciais no Key Vault](store-credentials-in-key-vault.md). | Não |
| email | O ID de e-mail de conta de serviço que é utilizado para ServiceAuthentication e apenas pode ser utilizado em Ir autoalojado.  | Não |
| keyFilePath | O caminho completo para o ficheiro de chave. p12 que é utilizado para autenticar o endereço de e-mail da conta de serviço e apenas pode ser utilizado em Ir autoalojado.  | Não |
| trustedCertPath | O caminho completo do ficheiro. pem que contém os certificados de AC fidedigna para verificar o servidor ao ligar-se através de SSL. Esta propriedade só pode ser definida ao utilizar o SSL em Ir autoalojado. O valor predefinido é o arquivo de cacerts.pem instalado com o IR.  | Não |
| useSystemTrustStore | Especifica se pretende utilizar um certificado de AC a partir da loja de confiança do sistema ou a partir de um ficheiro PEM especificado. O valor predefinido é false.  | Não |

**Exemplo:**

```json
{
    "name": "GoogleAdWordsLinkedService",
    "properties": {
        "type": "GoogleAdWords",
        "typeProperties": {
            "clientCustomerID" : "<clientCustomerID>",
            "developerToken": {
                "type": "SecureString",
                "value": "<developerToken>"
            },
            "authenticationType" : "ServiceAuthentication",
            "refreshToken": {
                "type": "SecureString",
                "value": "<refreshToken>"
            },
            "clientId": {
                "type": "SecureString",
                "value": "<clientId>"
            },
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            },
            "email" : "<email>",
            "keyFilePath" : "<keyFilePath>",
            "trustedCertPath" : "<trustedCertPath>",
            "useSystemTrustStore" : true,
        }
    }
}

```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados do Google AdWords.

Para copiar dados do Google AdWords, defina a propriedade de tipo de conjunto de dados para **GoogleAdWordsObject**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados deve ser definida como: **GoogleAdWordsObject** | Sim |
| tableName | Nome da tabela. | Não (se for especificada "consulta" na origem de atividade) |

**Exemplo**

```json
{
    "name": "GoogleAdWordsDataset",
    "properties": {
        "type": "GoogleAdWordsObject",
        "linkedServiceName": {
            "referenceName": "<GoogleAdWords linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}

```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas pela origem AdWords do Google.

### <a name="google-adwords-as-source"></a>Google AdWords como origem

Para copiar dados do Google AdWords, defina o tipo de origem na atividade de cópia para **GoogleAdWordsSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de origem de atividade de cópia tem de ser definida: **GoogleAdWordsSource** | Sim |
| query | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromGoogleAdWords",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleAdWords input dataset name>",
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
                "type": "GoogleAdWordsSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
