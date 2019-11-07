---
title: Copiar dados do Google BigQuery usando o Azure Data Factory
description: Saiba como copiar dados do Google BigQuery para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de data factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: d57260db74a1373eb461c560e013be620910eee9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680926"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Copiar dados do Google BigQuery usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados do Google BigQuery. Ele se baseia no artigo [visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector do Google BigQuery tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do Google BigQuery para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como fontes ou coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

O Data Factory fornece um driver interno para habilitar a conectividade. Portanto, você não precisa instalar manualmente um driver para usar esse conector.

>[!NOTE]
>Este conector do Google BigQuery é criado sobre as APIs do BigQuery. Lembre-se de que o BigQuery limita a taxa máxima de solicitações de entrada e impõe cotas apropriadas em uma base por projeto, consulte [cotas & limites-solicitações de API](https://cloud.google.com/bigquery/quotas#api_requests). Verifique se você não está disparando muitas solicitações simultâneas para a conta.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector do Google BigQuery.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Google BigQuery.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como **Google bigquery**. | Sim |
| projeto | A ID de projeto do projeto de BigQuery padrão a ser consultado.  | Sim |
| additionalProjects | Uma lista separada por vírgulas de IDs de projeto de projetos BigQuery públicos a serem acessados.  | Não |
| requestGoogleDriveScope | Se deseja solicitar acesso ao Google Drive. Permitir o acesso ao Google Drive permite o suporte para tabelas federadas que combinam dados do BigQuery com dados do Google Drive. O valor padrão é **false**.  | Não |
| authenticationType | O mecanismo de autenticação OAuth 2,0 usado para autenticação. O pré-autenticação só pode ser usado em Integration Runtime hospedados internamente. <br/>Os valores permitidos são **userAuthentication** e **pré-autenticação**. Consulte as seções abaixo desta tabela em mais propriedades e exemplos JSON para esses tipos de autenticação, respectivamente. | Sim |

### <a name="using-user-authentication"></a>Usando a autenticação do usuário

Defina a propriedade "authenticationType" como **userAuthentication**e especifique as propriedades a seguir junto com as propriedades genéricas descritas na seção anterior:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| clientId | ID do aplicativo usado para gerar o token de atualização. | Não |
| clientSecret | Segredo do aplicativo usado para gerar o token de atualização. Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| refreshToken | O token de atualização obtido do Google usado para autorizar o acesso ao BigQuery. Saiba como obter um de [obter tokens de acesso OAuth 2,0](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) e [este blog da Comunidade](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59). Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Não |

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

### <a name="using-service-authentication"></a>Usando a autenticação de serviço

Defina a propriedade "authenticationType" como **pré-autenticação**e especifique as propriedades a seguir junto com as propriedades genéricas descritas na seção anterior. Esse tipo de autenticação pode ser usado somente em Integration Runtime hospedados internamente.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| e-mail | A ID de email da conta de serviço usada para a autenticação. Ele pode ser usado somente em Integration Runtime hospedados internamente.  | Não |
| keyFilePath | O caminho completo para o arquivo de chave. p12 que é usado para autenticar o endereço de email da conta de serviço. | Não |
| trustedCertPath | O caminho completo do arquivo. pem que contém certificados de AC confiáveis usados para verificar o servidor quando você se conecta via SSL. Essa propriedade só pode ser definida quando você usa SSL em Integration Runtime hospedados internamente. O valor padrão é o arquivo cacerts. pem instalado com o Integration Runtime.  | Não |
| useSystemTrustStore | Especifica se um certificado de autoridade de certificação deve ser usado do repositório de confiança do sistema ou de um arquivo. pem especificado. O valor padrão é **false**.  | Não |

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

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . Esta seção fornece uma lista das propriedades com suporte pelo conjunto de BigQuery do Google.

Para copiar dados do Google BigQuery, defina a propriedade Type do conjunto de dado como **GoogleBigQueryObject**. As propriedades a seguir têm suporte:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **GoogleBigQueryObject** | Sim |
| DataSet | Nome do conjunto de BigQuery do Google. |Não (se for especificada "query" na origem de atividade)  |
| tabela | Nome da tabela. |Não (se for especificada "query" na origem de atividade)  |
| tableName | Nome da tabela. Essa propriedade tem suporte para compatibilidade com versões anteriores. Para uma nova carga de trabalho, use `dataset` e `table`. | Não (se for especificada "query" na origem de atividade) |

**Exemplo**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pelo tipo de origem do Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource como um tipo de origem

Para copiar dados do Google BigQuery, defina o tipo de fonte na atividade de cópia como **GoogleBigQuerySource**. As propriedades a seguir têm suporte na seção **origem** da atividade de cópia.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como **GoogleBigQuerySource**. | Sim |
| consulta | Use a consulta SQL personalizada para ler os dados. Um exemplo é `"SELECT * FROM MyTable"`. | Não (se "TableName" no DataSet for especificado) |

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

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
