---
title: Copiar dados de ou para Azure Data Explorer
description: Saiba como copiar dados de ou para o Azure Data Explorer utilizando uma atividade de cópia num oleoduto Azure Data Factory.
ms.author: orspodek
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2020
ms.openlocfilehash: 16126e8b9e5c34529016018273edcf65a31e2280
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379986"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Copiar dados de ou para a Azure Data Explorer utilizando a Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a atividade de cópia na Azure Data Factory para copiar dados de ou para [o Azure Data Explorer](/azure/data-explorer/data-explorer-overview). Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia, que oferece uma visão geral da atividade de cópia.

>[!TIP]
>Para a Azure Data Factory e a integração do Azure Data Explorer em geral, saiba mais com a Integração do Explorador de [Dados do Azure com a Azure Data Factory](/azure/data-explorer/data-factory-integration).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Azure Data Explorer é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados de qualquer loja de dados de origem suportada para o Azure Data Explorer. Também pode copiar dados do Azure Data Explorer para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados que a atividade de cópia suporta como fontes ou pias, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

>[!NOTE]
>Copiar dados para ou a partir do Azure Data Explorer através de uma loja de dados no local, utilizando o tempo de integração auto-hospedado, é suportado na versão 3.14 e posterior.

Com o conector Azure Data Explorer, pode fazer o seguinte:

* Copie os dados utilizando a autenticação simbólica de ficha de aplicação Azure Ative (Azure AD) com um **titular de serviço**.
* Como fonte, recupere dados utilizando uma consulta KQL (Kusto).
* Como pia, apenui a uma tabela de destino.

## <a name="getting-started"></a>Introdução

>[!TIP]
>Para uma passagem pelo conector Azure Data Explorer, consulte [copiar dados de/para O Explorador de Dados Azure utilizando a Azure Data Factory](/azure/data-explorer/data-factory-load-data) e cópia em massa de uma base de [dados para O Azure Data Explorer](/azure/data-explorer/data-factory-template).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector Azure Data Explorer.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

O conector Azure Data Explorer utiliza a autenticação principal do serviço. Siga estes passos para obter um diretor de serviço e para conceder permissões:

1. Registe uma entidade de candidatura no Azure Ative Directory seguindo os passos no Registo da [sua candidatura junto de um inquilino da Ad Azure.](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant) Tome nota dos seguintes valores, que utiliza para definir o serviço ligado:

    - ID da Aplicação
    - Chave de aplicação
    - ID do inquilino

2. Conceda ao diretor de serviço as permissões corretas no Azure Data Explorer. Consulte [permissões de base de dados do Manage Azure Data Explorer](/azure/data-explorer/manage-database-permissions) para obter informações detalhadas sobre funções e permissões e sobre a gestão de permissões. Em geral, deve:

    - **Como fonte,** conceda pelo menos o papel de **visualizador da Base de Dados** à sua base de dados
    - **Como pia**, conceda pelo menos a **função de ingestão de dados de dados** à sua base de dados

>[!NOTE]
>Quando utiliza o UI da Data Factory para autor, a sua conta de utilizador de login é utilizada para listar clusters, bases de dados e tabelas do Azure Data Explorer. Introduza manualmente o nome se não tiver permissão para estas operações.

As seguintes propriedades são suportadas para o serviço ligado ao Azure Data Explorer:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** deve ser definida para **AzureDataExplorer**. | Yes |
| endpoint | URL de ponto final do cluster Azure Data Explorer, com o formato como `https://<clusterName>.<regionName>.kusto.windows.net` . | Yes |
| base de dados | Nome da base de dados. | Yes |
| inquilino | Especifique a informação do inquilino (nome de domínio ou ID do inquilino) sob a qual a sua aplicação reside. Isto é conhecido como "ID de autoridade" na [cadeia de ligação Kusto.](/azure/kusto/api/connection-strings/kusto#application-authentication-properties) Recupere-o pairando sobre o ponteiro do rato no canto superior direito do portal Azure. | Yes |
| servicePrincipalId | Especifique a identificação do cliente da aplicação. Isto é conhecido como "ID do cliente de aplicação AAD" na [cadeia de ligação Kusto](/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Yes |
| servicePrincipalKey | Especifique a chave da aplicação. Isto é conhecido como "chave de aplicação AAD" na [cadeia de ligação Kusto](/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Marque este campo como um **SecureString** para armazená-lo de forma segura na Data Factory, ou [referência de dados seguros armazenados no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Yes |

**Exemplo de propriedades de serviço ligadas:**

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [datasets na Azure Data Factory](concepts-datasets-linked-services.md). Esta secção lista as propriedades que o conjunto de dados do Azure Data Explorer suporta.

Para copiar dados para O Azure Data Explorer, defina a propriedade tipo do conjunto de dados para **AzureDataExplorerTable**.

As seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** deve ser definida para **AzureDataExplorerTable**. | Yes |
| table | O nome da tabela a que o serviço ligado se refere. | Sim para a pia; Não para a fonte |

**Exemplo de propriedades do conjunto de dados:**

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

Para obter uma lista completa de secções e propriedades disponíveis para definir [atividades, consulte Pipelines e atividades na Azure Data Factory.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades que o Azure Data Explorer fornece e afunda suporte.

### <a name="azure-data-explorer-as-source"></a>Azure Data Explorer como fonte

Para copiar dados do Azure Data Explorer, deteta **a** propriedade tipo na fonte de atividade copy para **AzureDataExplorerSource**. As seguintes propriedades são suportadas na secção fonte de **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** da fonte de atividade de cópia deve ser definida para: **AzureDataExplorerSource** | Yes |
| consulta | Um pedido de leitura apenas dado num [formato KQL](/azure/kusto/query/). Utilize a consulta KQL personalizada como referência. | Yes |
| consultaTimeout | O tempo de espera antes do pedido de consulta. O valor predefinido é de 10 min (00:10:00); valor máximo permitido é de 1 hora (01:00:00). | No |
| noTruncation | Indica se deve truncar o conjunto de resultados devolvidos. Por padrão, o resultado é truncado após 500.000 registos ou 64 megabytes (MB). A truncação é fortemente recomendada para garantir o comportamento correto da atividade. |No |

>[!NOTE]
>Por padrão, a fonte do Azure Data Explorer tem um limite de tamanho de 500.000 registos ou 64 MB. Para recuperar todos os registos sem truncação, pode especificar `set notruncation;` no início da sua consulta. Para obter mais informações, consulte [os limites de consulta.](/azure/kusto/concepts/querylimits)

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

### <a name="azure-data-explorer-as-sink"></a>Azure Data Explorer como pia

Para copiar dados para O Azure Data Explorer, deteta a propriedade tipo na atividade de cópia para **AzureDataExplorerSink**. As seguintes propriedades são suportadas na secção de **lavatório** de atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do **tipo** do lavatório de atividade de cópia deve ser definida para: **AzureDataExplorerSink**. | Yes |
| ingestionMappingName | Nome de um [mapeamento](/azure/kusto/management/mappings#csv-mapping) pré-criado numa mesa kusto. Para mapear as colunas desde a fonte até ao Azure Data Explorer (que se aplica a [todas as lojas e formatos de origem suportados](copy-activity-overview.md#supported-data-stores-and-formats), incluindo os formatos CSV/JSON/Avro), pode utilizar o [mapeamento](copy-activity-schema-and-type-mapping.md) da coluna de atividade de cópia (implicitamente pelo nome ou explicitamente como configurado) e/ou mapeamentos Azure Data Explorer. | No |
| adicionalesproperias | Um saco de propriedade que pode ser usado para especificar qualquer uma das propriedades de ingestão que não estão sendo definidas já pelo Azure Data Explorer Sink. Especificamente, pode ser útil para especificar tags de ingestão. Saiba mais a partir do [Azure Data Explore o doc de ingestão de dados.](/azure/data-explorer/ingestion-properties) | No |

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
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>",
                "additionalProperties": {<additional settings for data ingestion>}
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

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter mais informações sobre as propriedades, consulte [a atividade da Lookup.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Passos seguintes

* Para obter uma lista de lojas de dados que a atividade de cópia na Azure Data Factory suporta como fontes e sumidouros, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

* Saiba mais sobre como [copiar dados da Azure Data Factory para o Azure Data Explorer.](/azure/data-explorer/data-factory-load-data)