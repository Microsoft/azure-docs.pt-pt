---
title: Copiar dados de ou para o Azure Data Explorer usando Azure Data Factory
description: Saiba como copiar dados de ou para o Azure Data Explorer usando uma atividade de cópia em um pipeline de Azure Data Factory.
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
ms.date: 08/01/2019
ms.author: orspodek
ms.openlocfilehash: 5cb08ddafe2075ae27ced6d70894696025df0a86
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010257"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>Copiar dados de ou para o Azure Data Explorer usando Azure Data Factory

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de ou para o [Azure data Explorer](../data-explorer/data-explorer-overview.md). Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector de Data Explorer do Azure tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de qualquer armazenamento de dados de origem com suporte para o Azure Data Explorer. Você também pode copiar dados do Azure Data Explorer para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista dos arquivos de dados que são suportados como origens ou sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md) tabela.

>[!NOTE]
>A cópia de dados de/para o Azure Data Explorer de/para o armazenamento de dados local usando o Integration Runtime de hospedagem interna tem suporte desde a versão 3,14.

O conector de Data Explorer do Azure permite que você faça o seguinte:

* Copie dados usando a autenticação de token de aplicativo Azure Active Directory (Azure AD) com uma **entidade de serviço**.
* Como origem, recupere dados usando uma consulta KQL (Kusto).
* Como coletor, acrescente dados a uma tabela de destino.

## <a name="getting-started"></a>Introdução

>[!TIP]
>Para obter uma explicação sobre como usar o conector de Data Explorer do Azure, consulte [copiar dados de/para o azure data Explorer usando o Azure data Factory](../data-explorer/data-factory-load-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector de Data Explorer do Azure.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

O conector de Data Explorer do Azure usa a autenticação de entidade de serviço. Siga estas etapas para obter uma entidade de serviço e conceder permissões:

1. Registe-se uma entidade de aplicação no Azure Active Directory (Azure AD) ao seguir [registar a aplicação com um inquilino do Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Tome nota dos seguintes valores, o que utilizar para definir o serviço ligado:

    - ID da aplicação
    - Chave da aplicação
    - ID do inquilino

2. Conceda a permissão apropriada à entidade de serviço no Data Explorer do Azure. Consulte [gerenciar permissões do banco de dados do Azure data Explorer](../data-explorer/manage-database-permissions.md) com informações detalhadas sobre funções e permissões, bem como percorrer o gerenciamento de permissões. Em geral, você precisa

    - **Como fonte**, conceda pelo menos a função de **Visualizador de banco de dados** ao seu banco de dados.
    - **Como coletor**, conceda pelo menos a função de **ingestão de banco de dados** ao seu banco de dados.

>[!NOTE]
>Ao usar a IU do ADF para criar, sua conta de usuário de logon é usada para listar clusters, bancos de dados e tabelas do Azure Data Explorer. Insira manualmente o nome se você não tiver permissão para essa operação.

As propriedades a seguir têm suporte para o serviço vinculado do Azure Data Explorer:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade **Type** deve ser definida como **AzureDataExplorer** | Sim |
| endpoint | URL do ponto de extremidade do cluster do Azure Data Explorer, com `https://<clusterName>.<regionName>.kusto.windows.net`o formato como. | Sim |
| database | Nome do banco de dados. | Sim |
| tenant | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Isso é o que você normalmente sabe como "**ID de autoridade**" na cadeia de [conexão Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Recupere-o passando o mouse no canto superior direito do portal do Azure. | Sim |
| servicePrincipalId | Especifique o ID de cliente. da aplicação Isso é o que você normalmente sabe como "**ID do cliente do aplicativo do AAD**" na cadeia de [conexão Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Sim |
| servicePrincipalKey | Especifique a chave da aplicação. Isso é o que você normalmente sabe como "**chave de aplicativo do AAD**" na cadeia de [conexão Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Marcar esse campo como um **SecureString** armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |

**Exemplo de propriedades do serviço vinculado:**

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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de Data Explorer do Azure.

Para copiar dados para o Azure Data Explorer, defina a propriedade Type do conjunto como **AzureDataExplorerTable**.

São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade **Type** deve ser definida como **AzureDataExplorerTable** | Sim |
| table | O nome da tabela à qual o serviço vinculado se refere. | Sim para coletor; Não para origem |

**Exemplo de propriedades de DataSet**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista das propriedades com suporte pela fonte de Data Explorer do Azure e pelo coletor.

### <a name="azure-data-explorer-as-source"></a>Data Explorer do Azure como fonte

Para copiar dados do Azure Data Explorer, defina a propriedade **Type** na origem da atividade de cópia para **AzureDataExplorerSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade **Type** da fonte da atividade de cópia deve ser definida como: **AzureDataExplorerSource** | Sim |
| query | Uma solicitação somente leitura fornecida em um [formato KQL](/azure/kusto/query/). Use a consulta KQL personalizada como uma referência. | Sim |
| queryTimeout | O tempo de espera antes que a solicitação de consulta expire. O valor padrão é 10 min (00:10:00); o valor máximo permitido é de 1 hora (01:00:00). | Não |
| notruncamento | Indica se o conjunto de resultados retornado deve ser truncado. Por padrão, o resultado é truncado após 500.000 registros ou 64 MB. O truncamento é altamente recomendado para um comportamento adequado da atividade. |Não |

>[!NOTE]
>Por padrão, a fonte de Data Explorer do Azure tem um limite de tamanho de 500.000 registros ou 64 MB. Para recuperar todos os registros sem truncamento, você pode especificar `set notruncation;` no início da consulta. Consulte [os limites de consulta](https://docs.microsoft.com/azure/kusto/concepts/querylimits) para obter mais detalhes.

**Example:**

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

### <a name="azure-data-explorer-as-sink"></a>Data Explorer do Azure como coletor

Para copiar dados para o Azure Data Explorer, defina a propriedade Type no coletor de atividade de cópia como **AzureDataExplorerSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade **Type** do coletor da atividade de cópia deve ser definida como: **AzureDataExplorerSink** | Sim |
| ingestionMappingName | Nome de um **[mapeamento](/azure/kusto/management/mappings#csv-mapping)** criado previamente em uma tabela Kusto. Para mapear as colunas da origem para o Azure Data Explorer-que se aplica a **[todos os repositórios/formatos de origem com suporte](copy-activity-overview.md#supported-data-stores-and-formats)** , incluindo os formatos CSV/JSON/Avro, etc., você pode usar o [mapeamento de coluna](copy-activity-schema-and-type-mapping.md) da atividade de cópia (implicitamente por nome ou explicitamente como configurado) e /ou mapeamentos de Data Explorer do Azure. | Não |

**Example:**

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
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>"
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

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).

* Saiba mais sobre como [copiar dados de Azure data Factory para o data Explorer do Azure](/azure/data-explorer/data-factory-load-data).