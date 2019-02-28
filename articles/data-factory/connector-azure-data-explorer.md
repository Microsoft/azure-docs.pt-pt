---
title: Copiar dados para ou a partir do Explorador de dados do Azure com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados para ou a partir do Explorador de dados do Azure com uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: orspod
ms.openlocfilehash: d30eab024fa988b3341c5efc9fe188ee4802720a
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961079"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>Copiar dados para ou a partir do Explorador de dados do Azure com o Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados para ou a partir [Explorador de dados do Azure](../data-explorer/data-explorer-overview.md). Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de qualquer arquivo de dados de origem suportada para o Explorador de dados do Azure. Também pode copiar dados a partir do Explorador de dados do Azure para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens ou sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md) tabela.

>[!NOTE]
>Copiar dados de/para o Explorador de dados do Azure de/para o arquivo de dados no local com o Runtime de integração autoalojado é suportada desde a versão 3.14.

O conector do Explorador de dados do Azure permite-lhe efetuar o seguinte:

* Copiar dados utilizando a autenticação de token de aplicação de Azure Active Directory (Azure AD) com um **principal de serviço**.
* Como uma origem, obter dados utilizando uma consulta KQL (Kusto).
* Como um sink, acrescente dados a uma tabela de destino.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector do Explorador de dados do Azure.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

O conector do Explorador de dados do Azure utiliza a autenticação do principal de serviço. Siga estes passos para obter um principal de serviço e conceder permissões:

1. Registe-se uma entidade de aplicação no Azure Active Directory (Azure AD) ao seguir [registar a aplicação com um inquilino do Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Tome nota dos seguintes valores, o que utilizar para definir o serviço ligado:

    - ID da aplicação
    - Chave da aplicação
    - ID do inquilino

2. Conceda a service principal permissão adequada no Explorador de dados do Azure. Consulte a [permissões de base de dados de gerir o Azure Data Explorer](../data-explorer/manage-database-permissions.md) com informações detalhadas sobre funções e permissões, bem como instruções sobre como gerir permissões. Em geral, precisa

    - **Como origem**, pelo menos a conceder **Visualizador de base de dados** função à base de dados.
    - **Como sink**, pelo menos a conceder **ingestor de base de dados** função à base de dados.

>[!NOTE]
>Ao utilizar a interface do Usuário do ADF para criar, as operações de listagem de bases de dados no serviço ligado ou lista tabelas num conjunto de dados podem exigir a permissão com privilégios mais alta para o principal de serviço. Em alternativa, pode optar por introduzir manualmente o nome de base de dados e o nome de tabela. Copie funciona de execução de atividade, desde que o principal de serviço é concedido com a permissão adequada para dados de leitura/escrita.

As seguintes propriedades são suportadas para o serviço ligado do Explorador de dados do Azure:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade deve ser definida **AzureDataExplorer** | Sim |
| endpoint | URL de ponto final do cluster do Azure Data Explorer, com o formato como `https://<clusterName>.<regionName>.kusto.windows.net `. | Sim |
| base de dados | Nome da base de dados. | Sim |
| inquilino | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Recuperá-lo passando o Mouse com o mouse no canto superior direito do portal do Azure. | Sim |
| servicePrincipalId | Especifique o ID de cliente. da aplicação | Sim |
| servicePrincipalKey | Especifique a chave da aplicação. Marcar esse campo como um **SecureString** armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |

**Exemplo de propriedades do serviço ligado:**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista de propriedades que são suportadas pelo conjunto de dados do Azure Data Explorer.

Para copiar dados para o Explorador de dados do Azure, defina a propriedade de tipo de conjunto de dados para **AzureDataExplorerTable**.

São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade deve ser definida **AzureDataExplorerTable** | Sim |
| tabela | O nome da tabela que o serviço ligado refere-se a. | Sim para o sink; Não para a origem |

**Exemplo de propriedades do conjunto de dados**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "table": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com o Explorador de dados do Azure de origem e sink.

### <a name="azure-data-explorer-as-source"></a>Explorador de dados do Azure como origem

Para copiar dados a partir do Explorador de dados do Azure, defina o **tipo** propriedade na origem de atividade de cópia para **AzureDataExplorerSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade da origem de atividade de cópia tem de ser definida como: **AzureDataExplorerSource** | Sim |
| consulta | Um pedido de só de leitura fornecido num [formato KQL](/azure/kusto/query/). Utilize a consulta KQL personalizada como uma referência. | Sim |
| queryTimeout | O tempo de espera antes do pedido de consulta exceder o tempo limite. Valor predefinido é 10 minutos (00: 10:00); permitido valor máximo é de 1 hora (01: 00:00). | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Explorador de dados do Azure como sink

Para copiar dados para o Explorador de dados do Azure, definir a propriedade de tipo no sink de atividade de cópia para **AzureDataExplorerSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade do coletor de atividade de cópia tem de ser definida como: **AzureDataExplorerSink** | Sim |
| ingestionMappingName | Nome de uma pré-criada **[mapeamento de CSV](/azure/kusto/management/mappings#csv-mapping)** numa tabela de Kusto. Para mapear as colunas de origem para o Explorador de dados do Azure – o que se aplica ao **[suportadas/formatos de arquivos de origem](copy-activity-overview.md#supported-data-stores-and-formats)** incluindo CSV/JSON/Avro formatos etc., pode utilizar a atividade de cópia [coluna mapeamento](copy-activity-schema-and-type-mapping.md) (implicitamente por nome ou explicitamente configurado) e/ou mapeamentos de CSV de Explorador de dados do Azure. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional csv mapping name>"
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).