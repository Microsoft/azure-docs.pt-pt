---
title: Copiar dados do Google BigQuery utilizando a Fábrica de Dados Azure
description: Saiba como copiar dados do Google BigQuery para lojas de dados de sink suportadas utilizando uma atividade de cópia num pipeline de fábrica de dados.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: c0eb043ce040f154050ef4c3675f165dad326e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929429"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Copiar dados do Google BigQuery utilizando a Fábrica de Dados Azure

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados do Google BigQuery. Baseia-se no artigo de visão geral da [Copy Activity](copy-activity-overview.md) que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Google BigQuery é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados do Google BigQuery para qualquer loja de dados suportada. Para obter uma lista de lojas de dados que sejam suportadas como fontes ou afunda-se pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

A Data Factory fornece um condutor incorporado para permitir a conectividade. Por isso, não é necessário instalar manualmente um controlador para utilizar este conector.

>[!NOTE]
>Este conector Google BigQuery é construído em cima das APIs BigQuery. Esteja ciente de que o BigQuery limita a taxa máxima dos pedidos de entrada e aplica quotas adequadas por projeto, consulte quotas [& limites - pedidos](https://cloud.google.com/bigquery/quotas#api_requests)de API . Certifique-se de que não desencadeia muitos pedidos simultâneos para a conta.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas do conector Google BigQuery.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado ao Google BigQuery.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **o GoogleBigQuery**. | Sim |
| project | A identificação do projeto padrão BigQuery para consultar.  | Sim |
| projetos adicionais | Uma lista separada de iDs de projetos públicos de bigquery para aceder.  | Não |
| solicitar GoogleDriveScope | Se solicitar acesso ao Google Drive. Permitir o acesso ao Google Drive permite suporte para tabelas federadas que combinam dados bigQuery com dados do Google Drive. O valor predefinido é **falso**.  | Não |
| authenticationType | O mecanismo de autenticação OAuth 2.0 utilizado para autenticação. ServiceAuthentication só pode ser usado em Tempo de Integração Auto-hospedado. <br/>Os valores permitidos são **a Autenticação do Utilizador** e a **Autenticação do Serviço.** Consulte as secções abaixo desta tabela sobre mais propriedades e amostras JSON para esses tipos de autenticação, respectivamente. | Sim |

### <a name="using-user-authentication"></a>Utilização da autenticação do utilizador

Detete a propriedade "autenticaçãoType" para **a Autenticação**do Utilizador, e especifique as seguintes propriedades juntamente com as propriedades genéricas descritas na secção anterior:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| clientId | Id da aplicação usada para gerar o token de atualização. | Não |
| clienteSecret | Segredo da aplicação usada para gerar o token de atualização. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Não |
| refreshToken | O token de atualização obtido pela Google usado para autorizar o acesso ao BigQuery. Saiba como obter um da Obtenção de Fichas de [Acesso OAuth 2.0](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) e [este blog comunitário](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59). Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Não |

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

### <a name="using-service-authentication"></a>Utilização da autenticação do serviço

Detete a propriedade "autenticaçãoType" para **a Autenticação**do Serviço, e especifique as seguintes propriedades juntamente com as propriedades genéricas descritas na secção anterior. Este tipo de autenticação só pode ser utilizado no Tempo de Integração Auto-hospedado.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| e-mail | O id de e-mail da conta de serviço que é usado para A Autenticação de Serviços. Só pode ser utilizado em Tempo de Funcionação de Integração Auto-hospedado.  | Não |
| keyFilePath | O caminho completo para o ficheiro chave .p12 que é usado para autenticar o endereço de e-mail da conta de serviço. | Não |
| trustedCertPath | O caminho completo do ficheiro .pem que contém certificados CA fidedignos utilizados para verificar o servidor quando se conecta através do SSL. Esta propriedade só pode ser definida quando utilizar o SSL no Tempo de Execução de Integração Auto-hospedado. O valor predefinido é o ficheiro cacerts.pem instalado com o tempo de execução da integração.  | Não |
| useSystemTrustStore | Especifica se utilizará um certificado CA a partir da loja fiduciário do sistema ou de um ficheiro .pem especificado. O valor predefinido é **falso**.  | Não |

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados do Google BigQuery.

Para copiar dados do Google BigQuery, detete a propriedade do tipo do conjunto de dados para **o GoogleBigQueryObject**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **GoogleBigQueryObject** | Sim |
| conjunto de dados | Nome do conjunto de dados do Google BigQuery. |Não (se for especificada a "consulta" na fonte de atividade)  |
| tabela | Nome da mesa. |Não (se for especificada a "consulta" na fonte de atividade)  |
| tableName | Nome da mesa. Esta propriedade é suportada para retrocompatibilidade. Para uma nova `dataset` `table`carga de trabalho, use e . | Não (se for especificada a "consulta" na fonte de atividade) |

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

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pelo tipo de fonte do Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource como um tipo de fonte

Para copiar dados do Google BigQuery, delineie o tipo de origem na atividade de cópia para **o GoogleBigQuerySource**. As seguintes propriedades são suportadas na secção de **origem** da atividade de cópia.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo da fonte de atividade de cópia deve ser definida para **o GoogleBigQuerySource**. | Sim |
| consulta | Utilize a consulta SQL personalizada para ler dados. Um exemplo é `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

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

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
