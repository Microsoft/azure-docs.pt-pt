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
ms.openlocfilehash: f614c6770dd29bc3d6b42c36fe8c81d9f129cd81
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56816662"
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
| ingestionMappingName | Nome de uma pré-criada **[mapeamento de CSV](/azure/kusto/management/mappings#csv-mapping)** numa tabela de Kusto; Mapeamento de Avro no Explorador de dados do Azure e de mapeamento de JSON não são suportados diretamente, mas ainda pode copiar dados de ficheiros JSON/Avro. Para mapear as colunas de origem para o Azure Data Explorer, pode usar a atividade de cópia [mapeamento de colunas](copy-activity-schema-and-type-mapping.md) que também conjuntamente de funciona com mapeamentos de CSV de Explorador de dados do Azure – copiar atividade maps/voltar-shapes dados de origem para o sink com base na coluna mapeamento de definições, em seguida, mapeia a dados novamente com base na configuração de mapeamento de ingestão se existe. Aplica-se ao [todos os arquivos de origem de suportados](copy-activity-overview.md#supported-data-stores-and-formats) incluindo formatos JSON e Avro. | Não |

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