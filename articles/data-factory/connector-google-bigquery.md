---
title: Copie os dados do Google BigQuery utilizando a Azure Data Factory
description: Saiba como copiar dados do Google BigQuery para lojas de dados de sumidouros suportadas utilizando uma atividade de cópia num oleoduto de fábrica de dados.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: e3fcaa6c1542578d983461623da743724a3114d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389693"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Copie os dados do Google BigQuery utilizando a Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a Copy Activity na Azure Data Factory para copiar dados do Google BigQuery. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da Copy Activity que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Google BigQuery é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados do Google BigQuery para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados que são suportadas como fontes ou sumidouros pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

A Data Factory fornece um controlador incorporado para permitir a conectividade. Por isso, não é necessário instalar manualmente um controlador para utilizar este conector.

>[!NOTE]
>Este conector Google BigQuery é construído em cima das APIs BigQuery. Esteja ciente de que a BigQuery limita a taxa máxima de pedidos de entrada e impõe quotas adequadas por projeto, consulte [quotas & Limites - Pedidos de API](https://cloud.google.com/bigquery/quotas#api_requests). Certifique-se de que não desencadeia muitos pedidos simultâneos na conta.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector Google BigQuery.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado ao Google BigQuery.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **GoogleBigQuery**. | Yes |
| project | O projeto ID do projeto padrão BigQuery para consultar contra.  | Yes |
| projetos adicionais | Uma lista separada por vírgula de iDs de projetos públicos bigQuery para aceder.  | No |
| requestGoogleDriveScope | Se solicitar acesso ao Google Drive. Permitir o acesso ao Google Drive permite o suporte para tabelas federadas que combinam dados bigQuery com dados do Google Drive. O valor predefinido é **falso**.  | No |
| authenticationType | O mecanismo de autenticação OAuth 2.0 utilizado para a autenticação. A Autorização de Serviço só pode ser utilizada no tempo de funcionamento da integração auto-hospedado. <br/>Os valores permitidos são **a Autorização de Utilização** e **a Autorização de Serviço**. Consulte as secções abaixo desta tabela sobre mais propriedades e amostras JSON para esses tipos de autenticação, respectivamente. | Yes |

### <a name="using-user-authentication"></a>Utilização da autenticação do utilizador

Descreva a propriedade "autenticaçãoType" à **Propriedade utilizadora,** e especifique as seguintes propriedades juntamente com as propriedades genéricas descritas na secção anterior:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| clientId | ID da aplicação usada para gerar o token de atualização. | No |
| segredo de clientes | Segredo da aplicação usada para gerar o token de atualização. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | No |
| refreshToken | O token de atualização obtido pela Google usado para autorizar o acesso ao BigQuery. Saiba como obter um a partir de obter fichas de [acesso OAuth 2.0](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) e [este blog comunitário.](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59) Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | No |

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

### <a name="using-service-authentication"></a>Utilização da autenticação de serviço

Descreva a propriedade "autenticaçãoType" para a Atribuição de Serviços, e especifique as **seguintes** propriedades juntamente com as propriedades genéricas descritas na secção anterior. Este tipo de autenticação só pode ser utilizado no tempo de funcionaamento de integração auto-hospedado.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| e-mail | O ID de e-mail da conta de serviço que é usado para a Autorização de Serviço. Só pode ser utilizado no tempo de integração auto-hospedado.  | No |
| keyFilePath | O caminho completo para o ficheiro chave .p12 que é usado para autenticar o endereço de e-mail da conta de serviço. | No |
| trustedCertPath | O percurso completo do ficheiro .pem que contém certificados de CA fidedignos utilizados para verificar o servidor quando se liga através de TLS. Esta propriedade só pode ser definida quando utilizar o TLS no Tempo de Execução de Integração Auto-hospedado. O valor predefinido é o ficheiro cacerts.pem instalado com o tempo de execução da integração.  | No |
| useSystemTrustStore | Especifica se deve utilizar um certificado de CA da loja de fidedignidade do sistema ou de um ficheiro .pem especificado. O valor predefinido é **falso**.  | No |

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

Para copiar dados do Google BigQuery, defina a propriedade tipo do conjunto de dados para **o GoogleBigQueryObject**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **GoogleBigQueryObject** | Yes |
| conjunto de dados | Nome do conjunto de dados do Google BigQuery. |Não (se for especificada "consulta" na fonte de atividade)  |
| table | O nome da mesa. |Não (se for especificada "consulta" na fonte de atividade)  |
| tableName | O nome da mesa. Esta propriedade é suportada para retrocompatibilidade. Para nova carga de trabalho, use `dataset` e `table` . | Não (se for especificada "consulta" na fonte de atividade) |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pelo tipo de origem Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource como um tipo de origem

Para copiar dados do Google BigQuery, desave o tipo de fonte na atividade de cópia para **o GoogleBigQuerySource**. As seguintes propriedades são suportadas na secção **de origem** da atividade de cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para **GoogleBigQuerySource**. | Yes |
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

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
