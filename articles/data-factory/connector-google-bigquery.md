---
title: Copiar dados do Google BigQuery com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados do Google BigQuery para arquivos de dados de sink suportado através de uma atividade de cópia num pipeline de fábrica de dados.
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 12/07/2018
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: c9320c8d0cf512bc9145accc07ab4c79630a7c84
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60808881"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Copiar dados do Google BigQuery com o Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados do Google BigQuery. Ele se baseia no [descrição geral da atividade de cópia](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados do Google BigQuery para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens ou sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Data Factory fornece um driver incorporado para permitir a conectividade. Portanto, não precisa de instalar manualmente um driver para utilizar este conector.

>[!NOTE]
>Este conector Google BigQuery é criada sobre as APIs de BigQuery. Lembre-se de que os limites de BigQuery a velocidade máxima de entrada de pedidos e impõe quotas adequadas numa base por projeto, consulte [Quotas e limites - solicitações da API](https://cloud.google.com/bigquery/quotas#api_requests). Certifique-se de que não acionam demasiados pedidos simultâneos para a conta.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector do Google BigQuery.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o Google BigQuery de serviço ligado.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como **GoogleBigQuery**. | Sim |
| project | O ID de projeto do projeto BigQuery padrão para consultas.  | Sim |
| additionalProjects | Uma lista separada por vírgulas de IDs de projeto do público BigQuery projetos para o acesso.  | Não |
| requestGoogleDriveScope | Se pedir acesso para o Google Drive. Permitir o acesso do Google Drive ativa o suporte para tabelas federadas que combinam dados BigQuery com dados do Google Drive. O valor predefinido é **false**.  | Não |
| authenticationType | O mecanismo de autenticação OAuth 2.0 utilizado para autenticação. ServiceAuthentication pode ser utilizado apenas no Runtime de integração autoalojado. <br/>Valores permitidos são **UserAuthentication** e **ServiceAuthentication**. Consulte a secções abaixo desta tabela em mais propriedades e exemplos JSON para esses tipos de autenticação, respetivamente. | Sim |

### <a name="using-user-authentication"></a>Utilizar a autenticação de utilizador

Defina a propriedade de "authenticationType" como **UserAuthentication**e especifique as seguintes propriedades, juntamente com as propriedades genéricas descritas na secção anterior:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| clientId | ID do aplicativo usado para gerar o token de atualização. | Não |
| clientSecret | Segredo do aplicativo usado para gerar o token de atualização. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| refreshToken | O token de atualização obtido a partir do Google utilizado para autorizar o acesso a BigQuery. Saiba como obter um em [tokens de acesso de obtenção de OAuth 2.0](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) e [este blogue da Comunidade](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59). Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Não |

**Exemplo:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "clientId": "<id of the application used to generate the refresh token>",
            "clientSecret": {
                "type": "SecureString",
                "value":"<secret of the application used to generate the refresh token>"
            },
            "refreshToken": {
                "type": "SecureString",
                "value": "<refresh token>"
            }
        }
    }
}
```

### <a name="using-service-authentication"></a>Utilizar a autenticação de serviço

Defina a propriedade de "authenticationType" como **ServiceAuthentication**e especifique as seguintes propriedades, juntamente com as propriedades genéricas descritas na secção anterior. Este tipo de autenticação pode ser utilizado apenas no Runtime de integração autoalojado.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| email | O ID de e-mail de conta do serviço é utilizado para ServiceAuthentication. Ele pode ser usado apenas em Runtime de integração autoalojado.  | Não |
| keyFilePath | O caminho completo para o ficheiro de chave. p12 que é utilizado para autenticar o endereço de e-mail da conta de serviço. | Não |
| trustedCertPath | O caminho completo do ficheiro. pem que contém os certificados de AC fidedigna, utilizados para verificar se o servidor quando se liga através de SSL. Esta propriedade pode ser definida apenas se utilizar o SSL no Integration Runtime autoalojado. O valor predefinido é o arquivo de cacerts.pem instalado com o runtime de integração.  | Não |
| useSystemTrustStore | Especifica se pretende utilizar um certificado de AC a partir da loja de confiança do sistema ou de um ficheiro. pem especificado. O valor predefinido é **false**.  | Não |

**Exemplo:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project id>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "ServiceAuthentication",
            "email": "<email>",
            "keyFilePath": "<.p12 key path on the IR machine>"
        },
        "connectVia": {
            "referenceName": "<name of Self-hosted Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com o conjunto de dados do Google BigQuery.

Para copiar dados do Google BigQuery, defina a propriedade de tipo de conjunto de dados para **GoogleBigQueryObject**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados deve ser definida como: **GoogleBigQueryObject** | Sim |
| tableName | Nome da tabela. | Não (se for especificada "consulta" na origem de atividade) |

**Exemplo**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com o tipo de origem do Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource como um tipo de origem

Para copiar dados do Google BigQuery, defina o tipo de origem na atividade de cópia para **GoogleBigQuerySource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de origem de atividade de cópia tem de ser definida **GoogleBigQuerySource**. | Sim |
| query | Utilize a consulta SQL personalizada para ler os dados. Um exemplo é `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
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
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
