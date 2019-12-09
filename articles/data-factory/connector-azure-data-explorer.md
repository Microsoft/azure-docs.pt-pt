---
title: Copiar dados de ou para o Azure Data Explorer
description: Saiba como copiar dados de ou para o Azure Data Explorer usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
ms.author: orspodek
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/26/2019
ms.openlocfilehash: 4cc315b91b5dbedcb22091149ca37061ff956efa
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74913432"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Copiar dados de ou para o Azure Data Explorer usando Azure Data Factory

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de ou para o [Azure data Explorer](../data-explorer/data-explorer-overview.md). Ele se baseia no artigo [visão geral da atividade de cópia](copy-activity-overview.md) , que oferece uma visão geral da atividade de cópia.

>[!TIP]
>Para Azure Data Factory e integração de Data Explorer do Azure em geral, saiba mais em [integrar o azure data Explorer com o Azure data Factory](../data-explorer/data-factory-integration.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector de Data Explorer do Azure tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de qualquer armazenamento de dados de origem com suporte para o Azure Data Explorer. Você também pode copiar dados do Azure Data Explorer para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte da atividade de cópia como fontes ou coletores, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

>[!NOTE]
>A cópia de dados de ou para o Azure Data Explorer por meio de um armazenamento de dados local usando o tempo de execução de integração auto-hospedado tem suporte na versão 3,14 e posterior.

Com o conector de Data Explorer do Azure, você pode fazer o seguinte:

* Copie dados usando a autenticação de token de aplicativo Azure Active Directory (Azure AD) com uma **entidade de serviço**.
* Como origem, recupere dados usando uma consulta KQL (Kusto).
* Como coletor, acrescente dados a uma tabela de destino.

## <a name="getting-started"></a>Introdução

>[!TIP]
>Para obter instruções sobre o conector de Data Explorer do Azure, consulte [copiar dados de/para o azure data Explorer usando Azure data Factory](../data-explorer/data-factory-load-data.md) e [cópia em massa de um banco de dados para o Azure data Explorer](../data-explorer/data-factory-template.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector de Data Explorer do Azure.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

O conector de Data Explorer do Azure usa a autenticação de entidade de serviço. Siga estas etapas para obter uma entidade de serviço e conceder permissões:

1. Registre uma entidade de aplicativo no Azure Active Directory seguindo as etapas em [registrar seu aplicativo com um locatário do Azure ad](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Tome nota dos seguintes valores, o que utilizar para definir o serviço ligado:

    - ID da aplicação
    - Chave da aplicação
    - ID do inquilino

2. Conceda à entidade de serviço as permissões corretas no Azure Data Explorer. Consulte [gerenciar permissões de banco de dados do Azure data Explorer](../data-explorer/manage-database-permissions.md) para obter informações detalhadas sobre funções e permissões e sobre como gerenciar permissões. Em geral, você deve:

    - **Como fonte**, conceda pelo menos a função de **Visualizador de banco de dados** ao seu banco de dados
    - **Como coletor**, conceda pelo menos a função de **ingestão de banco de dados** ao seu banco de dados

>[!NOTE]
>Quando você usa a interface do usuário do Data Factory para criar, sua conta de logon é usada para listar clusters, bancos de dados e tabelas do Azure Data Explorer. Insira o nome manualmente se você não tiver permissão para essas operações.

As propriedades a seguir têm suporte para o serviço vinculado do Azure Data Explorer:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** deve ser definida como **AzureDataExplorer**. | Sim |
| endpoint | URL do ponto de extremidade do cluster do Data Explorer do Azure, com o formato como `https://<clusterName>.<regionName>.kusto.windows.net`. | Sim |
| base de dados | Nome do banco de dados. | Sim |
| tenant | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Isso é conhecido como "ID de autoridade" na [cadeia de conexão Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Recupere-o passando o ponteiro do mouse no canto superior direito do portal do Azure. | Sim |
| servicePrincipalId | Especifique o ID de cliente. da aplicação Isso é conhecido como "ID do cliente do aplicativo do AAD" na [cadeia de conexão Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Sim |
| servicePrincipalKey | Especifique a chave da aplicação. Isso é conhecido como "chave de aplicativo do AAD" na [cadeia de conexão Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Marque este campo como uma **SecureString** para armazená-lo com segurança no data Factory ou [faça referência a dados seguros armazenados no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |

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

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte [DataSets in Azure data Factory](concepts-datasets-linked-services.md). Esta seção lista as propriedades às quais o conjunto de Data Explorer do Azure oferece suporte.

Para copiar dados para o Azure Data Explorer, defina a propriedade Type do conjunto como **AzureDataExplorerTable**.

São suportadas as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** deve ser definida como **AzureDataExplorerTable**. | Sim |
| table | O nome da tabela à qual o serviço vinculado se refere. | Sim para coletor; Não para origem |

**Exemplo de propriedades de DataSet:**

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

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte [pipelines e atividades no Azure data Factory](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades que o Azure Data Explorer fontes e coletores dão suporte.

### <a name="azure-data-explorer-as-source"></a>Data Explorer do Azure como fonte

Para copiar dados do Azure Data Explorer, defina a propriedade **Type** na origem da atividade de cópia para **AzureDataExplorerSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** da fonte da atividade de cópia deve ser definida como: **AzureDataExplorerSource** | Sim |
| consulta | Uma solicitação somente leitura fornecida em um [formato KQL](/azure/kusto/query/). Use a consulta KQL personalizada como uma referência. | Sim |
| queryTimeout | O tempo de espera antes que a solicitação de consulta expire. O valor padrão é 10 min (00:10:00); o valor máximo permitido é de 1 hora (01:00:00). | Não |
| notruncamento | Indica se o conjunto de resultados retornado deve ser truncado. Por padrão, o resultado é truncado após 500.000 registros ou 64 megabytes (MB). O truncamento é altamente recomendável para garantir o comportamento correto da atividade. |Não |

>[!NOTE]
>Por padrão, a fonte de Data Explorer do Azure tem um limite de tamanho de 500.000 registros ou 64 MB. Para recuperar todos os registros sem truncamento, você pode especificar `set notruncation;` no início da consulta. Para obter mais informações, consulte [limites de consulta](https://docs.microsoft.com/azure/kusto/concepts/querylimits).

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

### <a name="azure-data-explorer-as-sink"></a>Data Explorer do Azure como coletor

Para copiar dados para o Azure Data Explorer, defina a propriedade Type no coletor de atividade de cópia como **AzureDataExplorerSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** do coletor da atividade de cópia deve ser definida como: **AzureDataExplorerSink**. | Sim |
| ingestionMappingName | Nome de um [mapeamento](/azure/kusto/management/mappings#csv-mapping) criado previamente em uma tabela Kusto. Para mapear as colunas da origem para o Azure Data Explorer (que se aplica a [todos os formatos e repositórios de origem com suporte](copy-activity-overview.md#supported-data-stores-and-formats), incluindo os formatos CSV/JSON/Avro), você pode usar o [mapeamento de coluna](copy-activity-schema-and-type-mapping.md) de atividade de cópia (implicitamente por nome ou explicitamente como configurado) e/ou mapeamentos de data Explorer do Azure. | Não |

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

Para obter mais informações sobre as propriedades, consulte [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes

* Para obter uma lista de armazenamentos de dados que a atividade de cópia no Azure Data Factory dá suporte como fontes e coletores, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

* Saiba mais sobre como [copiar dados de Azure data Factory para o data Explorer do Azure](/azure/data-explorer/data-factory-load-data).
