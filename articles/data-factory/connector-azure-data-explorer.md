---
title: Copiar dados de ou para o Azure Data Explorer
description: Saiba como copiar dados de ou para o Azure Data Explorer utilizando uma atividade de cópia num pipeline azure Data Factory.
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
ms.date: 02/18/2020
ms.openlocfilehash: 5f0eada5184f2c8e36b0deb5a9d27a8a9230776d
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382767"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Copiar dados de ou para o Azure Data Explorer utilizando a Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia na Azure Data Factory para copiar dados de ou para o [Azure Data Explorer](/azure/data-explorer/data-explorer-overview). Baseia-se no artigo de visão geral da atividade de [cópia,](copy-activity-overview.md) que oferece uma visão geral da atividade de cópia.

>[!TIP]
>Para a Azure Data Factory e para a integração do Azure Data Explorer em geral, saiba mais com [a Integração azure Data Explorer com a Azure Data Factory.](/azure/data-explorer/data-factory-integration)

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Azure Data Explorer é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados de qualquer loja de dados de origem suportada para o Azure Data Explorer. Também pode copiar dados do Azure Data Explorer para qualquer loja de dados de sink suportado. Para obter uma lista de lojas de dados que a atividade de cópia suporta como fontes ou pias, consulte a tabela de [lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

>[!NOTE]
>Copiar dados de ou para o Azure Data Explorer através de uma loja de dados no local, utilizando o tempo de execução de integração auto-hospedado é suportado na versão 3.14 e posteriormente.

Com o conector Azure Data Explorer, pode fazer o seguinte:

* Copiar dados utilizando a autenticação token da aplicação Azure Ative Directory (Azure AD) com um diretor de **serviço**.
* Como fonte, recupere dados utilizando uma consulta KQL (Kusto).
* Como pia, anexar dados a uma tabela de destino.

## <a name="getting-started"></a>Introdução

>[!TIP]
>Para uma passagem pelo conector Azure Data Explorer, consulte [os dados de Cópia de/para o Azure Data Explorer utilizando](/azure/data-explorer/data-factory-load-data) a Azure Data Factory e a cópia a granel de uma base de dados para o [Azure Data Explorer](/azure/data-explorer/data-factory-template).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Fábrica de Dados específicas do conector Azure Data Explorer.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

O conector Azure Data Explorer utiliza a autenticação principal do serviço. Siga estes passos para obter um diretor de serviço e para conceder permissões:

1. Registe uma entidade de aplicação no Diretório Ativo Azure seguindo as etapas em Registar a [sua candidatura com um inquilino da AD Azure.](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant) Tome nota dos seguintes valores, que utiliza para definir o serviço vinculado:

    - ID da aplicação
    - Chave de aplicação
    - ID do inquilino

2. Conceda ao diretor de serviço as permissões corretas no Azure Data Explorer. Consulte [a Manage Azure Data Explorer permissões](/azure/data-explorer/manage-database-permissions) para obter informações detalhadas sobre papéis e permissões e sobre gestão de permissões. Em geral, deve:

    - **Como fonte,** conceda pelo menos o papel de **visualizador** da Base de Dados à sua base de dados
    - **Como pia,** conceda pelo menos o papel **ingestor** da Base de Dados na sua base de dados

>[!NOTE]
>Quando utiliza a UI da Fábrica de Dados para autor, a sua conta de utilizador de login é utilizada para listar clusters, bases de dados e tabelas do Azure Data Explorer. Introduza manualmente o nome se não tiver permissão para estas operações.

As seguintes propriedades são suportadas para o serviço ligado ao Explorador de Dados Azure:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **do tipo** deve ser definida para **AzureDataExplorer**. | Sim |
| endpoint | URL endpoint do cluster Azure Data Explorer, com o formato como `https://<clusterName>.<regionName>.kusto.windows.net`. | Sim |
| base de dados | Nome da base de dados. | Sim |
| inquilino | Especifique as informações do arrendatário (nome de domínio ou ID do inquilino) sob a qual reside a sua candidatura. Isto é conhecido como "Id da Autoridade" na cadeia de [ligação kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Recupere-o pairando sobre o ponteiro do rato no canto superior direito do portal Azure. | Sim |
| serviçoPrincipalId | Especifique a identificação do cliente do pedido. Isto é conhecido como "Id cliente de aplicação AAD" na cadeia de [ligação Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Sim |
| serviçoPrincipalKey | Especifique a chave da aplicação. Isto é conhecido como "chave de aplicação AAD" na cadeia de [ligação Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Marque este campo como um **SecureString** para o armazenar de forma segura na Data Factory ou dados [seguros de referência armazenados no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Sim |

**As propriedades de serviço ligadas exemplo:**

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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets na Azure Data Factory](concepts-datasets-linked-services.md). Esta secção lista propriedades que o conjunto de dados do Azure Data Explorer suporta.

Para copiar dados para o Azure Data Explorer, detete a propriedade do tipo do conjunto de dados para **o AzureDataExplorerTable**.

As seguintes propriedades são suportadas:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **do tipo** deve ser definida para **AzureDataExplorerTable**. | Sim |
| tabela | O nome da tabela a que o serviço ligado se refere. | Sim para afundar; Não para a fonte |

**As propriedades do conjunto de dados exemplo:**

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

Para obter uma lista completa de secções e imóveis disponíveis para definir [atividades, consulte Pipelines e atividades na Azure Data Factory.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades que o Azure Data Explorer fornece e afunda suporte.

### <a name="azure-data-explorer-as-source"></a>Explorador de dados azure como fonte

Para copiar dados do Azure Data Explorer, delineie a propriedade do **tipo** na fonte de atividade da Cópia para **o AzureDataExplorerSource**. As seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **do tipo** da fonte de atividade de cópia deve ser definida para: **AzureDataExplorerSource** | Sim |
| consulta | Um pedido de leitura apenas dado em [formato KQL](/azure/kusto/query/). Utilize a consulta kQL personalizada como referência. | Sim |
| consultaTimeout | O tempo de espera antes da consulta pedir tempo saem. O valor predefinido é de 10 min (00:10:00); o valor máximo permitido é de 1 hora (01:00:00). | Não |
| noTruncation | Indica se trunca o conjunto de resultados devolvidos. Por padrão, o resultado é truncado após 500.000 registos ou 64 megabytes (MB). A truncação é fortemente recomendada para garantir o comportamento correto da atividade. |Não |

>[!NOTE]
>Por padrão, a fonte do Azure Data Explorer tem um limite de tamanho de 500.000 registos ou 64 MB. Para recuperar todos os registos sem `set notruncation;` truncation, pode especificar no início da sua consulta. Para mais informações, consulte os [limites](https://docs.microsoft.com/azure/kusto/concepts/querylimits)de consulta .

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

### <a name="azure-data-explorer-as-sink"></a>Explorador de dados azure como pia

Para copiar dados para o Azure Data Explorer, delineie a propriedade do tipo na atividade de cópia afunda-se no **AzureDataExplorerSink**. As seguintes propriedades são suportadas na secção de **sumidouro** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** do sumidouro da atividade de cópia deve ser definida para: **AzureDataExplorerSink**. | Sim |
| ingestionMappingName | Nome de um [mapeamento](/azure/kusto/management/mappings#csv-mapping) pré-criado numa mesa de Kusto. Para mapear as colunas da fonte para o Azure Data Explorer (que se aplica a [todas as lojas e formatos de origem suportados,](copy-activity-overview.md#supported-data-stores-and-formats)incluindo os formatos CSV/JSON/Avro), pode utilizar o [mapeamento](copy-activity-schema-and-type-mapping.md) da coluna de atividade de cópia (implicitamente pelo nome ou explicitamente como configurado) e/ou mapeamento do Explorador de Dados Azure. | Não |
| propriedades adicionais | Um saco de propriedade que pode ser usado para especificar qualquer uma das propriedades de ingestão que ainda não estão sendo definidas pelo Azure Data Explorer Sink. Especificamente, pode ser útil para especificar etiquetas de ingestão. Saiba mais com o Azure Data Explore a [ingestão de dados do c.](https://kusto.azurewebsites.net/docs/management/data-ingestion/index.html) | Não |

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

Para mais informações sobre as propriedades, consulte a [atividade de lookup.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Passos seguintes

* Para obter uma lista de lojas de dados que a atividade de cópia na Azure Data Factory suporta como fontes e pias, consulte lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).

* Saiba mais sobre como [copiar dados da Azure Data Factory para o Azure Data Explorer](/azure/data-explorer/data-factory-load-data).
