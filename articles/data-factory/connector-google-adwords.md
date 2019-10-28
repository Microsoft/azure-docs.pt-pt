---
title: Copiar dados do Google AdWords usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados do Google AdWords para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: jingwang
ms.openlocfilehash: 65bf1fa37ecb8d9e862b0e5042bed29470d750e1
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935627"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory"></a>Copiar dados do Google AdWords usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados do Google AdWords. Ele se baseia no artigo [visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector do Google AdWords tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)


Você pode copiar dados do Google AdWords para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como fontes/coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

O Azure Data Factory fornece um driver interno para habilitar a conectividade, portanto, você não precisa instalar manualmente nenhum driver usando esse conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector do Google AdWords.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Google AdWords:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **GoogleAdWords** | Sim |
| clientCustomerID | A ID do cliente do cliente da conta AdWords para a qual você deseja buscar dados de relatório.  | Sim |
| developerToken | O token do desenvolvedor associado à conta do Gerenciador que você usa para conceder acesso à API do AdWords.  Você pode optar por marcar esse campo como uma SecureString para armazená-lo com segurança no ADF ou armazenar a senha em Azure Key Vault e deixar a atividade de cópia do ADF efetuar pull a partir daí ao executar a cópia de dados-Saiba mais em [armazenar credenciais no Key Vault](store-credentials-in-key-vault.md). | Sim |
| authenticationType | O mecanismo de autenticação OAuth 2,0 usado para autenticação. O pré-autenticação só pode ser usado no IR de hospedagem interna. <br/>Os valores permitidos são: **pré-autenticação**, **userAuthentication** | Sim |
| refreshToken | O token de atualização obtido do Google para autorizar o acesso ao AdWords para userAuthentication. Você pode optar por marcar esse campo como uma SecureString para armazená-lo com segurança no ADF ou armazenar a senha em Azure Key Vault e deixar a atividade de cópia do ADF efetuar pull a partir daí ao executar a cópia de dados-Saiba mais em [armazenar credenciais no Key Vault](store-credentials-in-key-vault.md). | Não |
| clientId | A ID do cliente do aplicativo Google usado para adquirir o token de atualização. Você pode optar por marcar esse campo como uma SecureString para armazená-lo com segurança no ADF ou armazenar a senha em Azure Key Vault e deixar a atividade de cópia do ADF efetuar pull a partir daí ao executar a cópia de dados-Saiba mais em [armazenar credenciais no Key Vault](store-credentials-in-key-vault.md). | Não |
| clientSecret | O segredo do cliente do aplicativo do Google usado para adquirir o token de atualização. Você pode optar por marcar esse campo como uma SecureString para armazená-lo com segurança no ADF ou armazenar a senha em Azure Key Vault e deixar a atividade de cópia do ADF efetuar pull a partir daí ao executar a cópia de dados-Saiba mais em [armazenar credenciais no Key Vault](store-credentials-in-key-vault.md). | Não |
| e-mail | A ID de email da conta de serviço que é usada para a pré-autenticação e só pode ser usada no IR via hospedagem interna.  | Não |
| keyFilePath | O caminho completo para o arquivo de chave. p12 que é usado para autenticar o endereço de email da conta de serviço e só pode ser usado no IR via hospedagem interna.  | Não |
| trustedCertPath | O caminho completo do arquivo. pem que contém certificados de AC confiáveis para verificar o servidor ao se conectar via SSL. Essa propriedade só pode ser definida ao usar SSL no IR auto-hospedado. O valor padrão é o arquivo cacerts. pem instalado com o IR.  | Não |
| useSystemTrustStore | Especifica se um certificado de autoridade de certificação deve ser usado do repositório de confiança do sistema ou de um arquivo PEM especificado. O valor padrão é false.  | Não |

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

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . Esta seção fornece uma lista das propriedades com suporte pelo conjunto de AdWords do Google.

Para copiar dados do Google AdWords, defina a propriedade Type do conjunto de dado como **GoogleAdWordsObject**. As propriedades a seguir têm suporte:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **GoogleAdWordsObject** | Sim |
| tableName | Nome da tabela. | Não (se "Query" na origem da atividade for especificada) |

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

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela origem do Google AdWords.

### <a name="google-adwords-as-source"></a>Google AdWords como fonte

Para copiar dados do Google AdWords, defina o tipo de fonte na atividade de cópia como **GoogleAdWordsSource**. As propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como: **GoogleAdWordsSource** | Sim |
| consulta | Use a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se "TableName" no DataSet for especificado) |

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

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
