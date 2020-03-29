---
title: Copiar dados do Google AdWords
description: Saiba como copiar dados do Google AdWords para lojas de dados de sink suportadas utilizando uma atividade de cópia num pipeline azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/25/2019
ms.openlocfilehash: b01dcad71747da6b7aa770e3993cb82892ae55fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929439"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory"></a>Copiar dados do Google AdWords utilizando a Azure Data Factory

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados do Google AdWords. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Google AdWords é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)


Pode copiar dados do Google AdWords para qualquer loja de dados de sink suportado. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

A Azure Data Factory fornece um controlador incorporado para permitir a conectividade, pelo que não necessita de instalar manualmente qualquer controlador utilizando este conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Fábrica de Dados específicas do conector Google AdWords.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado ao Google AdWords:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **GoogleAdWords** | Sim |
| clientoCustomerID | O Cliente ID da conta AdWords que pretende obter dados do relatório.  | Sim |
| desenvolvedorToken | O símbolo do desenvolvedor associado à conta de gestor que utiliza para conceder acesso à API AdWords.  Pode optar por marcar este campo como um SecureString para o armazenar de forma segura em ADF, ou armazenar senha no Cofre de Chaves Azure e deixar a atividade de cópia da ADF puxar a partir daí ao executar cópia de dados - saiba mais com [as credenciais da Loja no Cofre chave](store-credentials-in-key-vault.md). | Sim |
| authenticationType | O mecanismo de autenticação OAuth 2.0 utilizado para autenticação. ServiceAuthentication só pode ser usado em IV auto-hospedado. <br/>Os valores permitidos são: **Autenticação do Serviço,** **Autenticação do Utilizador** | Sim |
| refreshToken | O token de atualização obtido da Google para autorizar o acesso ao AdWords para AdAuthentication. Pode optar por marcar este campo como um SecureString para o armazenar de forma segura em ADF, ou armazenar senha no Cofre de Chaves Azure e deixar a atividade de cópia da ADF puxar a partir daí ao executar cópia de dados - saiba mais com [as credenciais da Loja no Cofre chave](store-credentials-in-key-vault.md). | Não |
| clientId | O ID cliente da aplicação da Google usado para adquirir o token de atualização. Pode optar por marcar este campo como um SecureString para o armazenar de forma segura em ADF, ou armazenar senha no Cofre de Chaves Azure e deixar a atividade de cópia da ADF puxar a partir daí ao executar cópia de dados - saiba mais com [as credenciais da Loja no Cofre chave](store-credentials-in-key-vault.md). | Não |
| clienteSecret | O segredo do cliente da aplicação da Google usado para adquirir o token de atualização. Pode optar por marcar este campo como um SecureString para o armazenar de forma segura em ADF, ou armazenar senha no Cofre de Chaves Azure e deixar a atividade de cópia da ADF puxar a partir daí ao executar cópia de dados - saiba mais com [as credenciais da Loja no Cofre chave](store-credentials-in-key-vault.md). | Não |
| e-mail | O ID de e-mail da conta de serviço que é usado para serviceAuthentication e só pode ser usado em IV auto-hospedado.  | Não |
| keyFilePath | O caminho completo para o ficheiro chave .p12 que é usado para autenticar o endereço de e-mail da conta de serviço e só pode ser usado em IV auto-hospedado.  | Não |
| trustedCertPath | O caminho completo do ficheiro .pem contendo certificados CA fidedignos para verificar o servidor ao ligar-se através do SSL. Esta propriedade só pode ser definida quando se utiliza SSL em IR auto-hospedado. O valor predefinido é o ficheiro cacerts.pem instalado com o IR.  | Não |
| useSystemTrustStore | Especifica se deve utilizar um certificado CA a partir da loja fiduciário do sistema ou de um ficheiro PEM especificado. O valor predefinido é false.  | Não |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo conjuntos de [dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados do Google AdWords.

Para copiar dados do Google AdWords, detete a propriedade do tipo do conjunto de dados para **o GoogleAdWordsObject**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **GoogleAdWordsObject** | Sim |
| tableName | Nome da mesa. | Não (se for especificada a "consulta" na fonte de atividade) |

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

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte do Google AdWords.

### <a name="google-adwords-as-source"></a>Google AdWords como fonte

Para copiar dados do Google AdWords, delineie o tipo de origem na atividade de cópia para **o GoogleAdWordsSource**. As seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo da fonte de atividade de cópia deve ser definida para: **GoogleAdWordsSource** | Sim |
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

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
