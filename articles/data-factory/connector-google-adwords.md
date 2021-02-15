---
title: Copiar dados do Google AdWords
description: Saiba como copiar dados do Google AdWords para lojas de dados de sumidouros suportados utilizando uma atividade de cópia num pipeline da Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/25/2019
ms.openlocfilehash: 8b3036f09e41b20bc3c190f06842acd817fcece6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380955"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory"></a>Copiar dados do Google AdWords usando a Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados do Google AdWords. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector do Google AdWords é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)


Pode copiar dados do Google AdWords para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

A Azure Data Factory fornece um controlador incorporado para permitir a conectividade, pelo que não é necessário instalar manualmente qualquer controlador utilizando este conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector Google AdWords.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado ao Google AdWords:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **GoogleAdWords** | Yes |
| clienteStomerID | O ID do cliente cliente da conta AdWords para a quais pretende obter dados de relatório.  | Yes |
| developerToken | O símbolo do programador associado à conta de gestor que utiliza para conceder acesso à API do AdWords.  Pode optar por marcar este campo como um SecureString para armazená-lo de forma segura em ADF, ou armazenar a palavra-passe no Cofre da Chave Azure e deixar a atividade de cópia da ADF puxar daí quando executar a cópia de dados - saiba mais com [as credenciais](store-credentials-in-key-vault.md)da Loja no Key Vault . | Yes |
| authenticationType | O mecanismo de autenticação OAuth 2.0 utilizado para a autenticação. Serviço A autorização de serviço só pode ser utilizada em IR auto-hospedado. <br/>Os valores permitidos são: **Concessão de Serviço,** **Concessão de Utilizador** | Yes |
| refreshToken | O token de atualização obtido pela Google para autorizar o acesso ao AdWords para a Autorização de Utilizador. Pode optar por marcar este campo como um SecureString para armazená-lo de forma segura em ADF, ou armazenar a palavra-passe no Cofre da Chave Azure e deixar a atividade de cópia da ADF puxar daí quando executar a cópia de dados - saiba mais com [as credenciais](store-credentials-in-key-vault.md)da Loja no Key Vault . | No |
| clientId | O ID do cliente da aplicação google usado para adquirir o token de atualização. Pode optar por marcar este campo como um SecureString para armazená-lo de forma segura em ADF, ou armazenar a palavra-passe no Cofre da Chave Azure e deixar a atividade de cópia da ADF puxar daí quando executar a cópia de dados - saiba mais com [as credenciais](store-credentials-in-key-vault.md)da Loja no Key Vault . | No |
| segredo de clientes | O segredo do cliente da aplicação google usado para adquirir o token de atualização. Pode optar por marcar este campo como um SecureString para armazená-lo de forma segura em ADF, ou armazenar a palavra-passe no Cofre da Chave Azure e deixar a atividade de cópia da ADF puxar daí quando executar a cópia de dados - saiba mais com [as credenciais](store-credentials-in-key-vault.md)da Loja no Key Vault . | No |
| e-mail | O ID de e-mail da conta de serviço que é usado para a Autorização de Serviço e só pode ser usado em IR auto-hospedado.  | No |
| keyFilePath | O caminho completo para o ficheiro de chave .p12 que é usado para autenticar o endereço de e-mail da conta de serviço e só pode ser utilizado em IR auto-hospedado.  | No |
| trustedCertPath | O percurso completo do ficheiro .pem que contém certificados ca fidedignos para verificar o servidor ao ligar através de TLS. Esta propriedade só pode ser definida quando se utiliza TLS em IR auto-hospedado. O valor predefinido é o ficheiro cacerts.pem instalado com o IR.  | No |
| useSystemTrustStore | Especifica se deve utilizar um certificado de CA da loja de fidedignidade do sistema ou de um ficheiro PEM especificado. O valor predefinido é false.  | No |

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados do Google AdWords.

Para copiar dados do Google AdWords, defina a propriedade tipo do conjunto de dados para **o GoogleAdWordsObject**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **GoogleAdWordsObject** | Yes |
| tableName | O nome da mesa. | Não (se for especificada "consulta" na fonte de atividade) |

**Exemplo**

```json
{
    "name": "GoogleAdWordsDataset",
    "properties": {
        "type": "GoogleAdWordsObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleAdWords linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte do Google AdWords.

### <a name="google-adwords-as-source"></a>Google AdWords como fonte

Para copiar dados do Google AdWords, desa estale o tipo de origem na atividade de cópia para **o GoogleAdWordsSource**. As seguintes propriedades são suportadas na secção fonte de **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **GoogleAdWordsSource** | Yes |
| consulta | Utilize a consulta SQL personalizada para ler dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

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

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
