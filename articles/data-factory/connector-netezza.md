---
title: Copiar dados do Netezza com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de Netezza para arquivos de dados de sink suportado através de uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 02/01/2019
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: 9bf90c9d3ce593ba5bf6339cd9cec31bb49f14f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61399932"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Copiar dados do Netezza com o Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de Netezza. O artigo se baseia no [atividade de cópia no Azure Data Factory](copy-activity-overview.md), que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de Netezza para qualquer arquivo de dados de sink suportados. Para obter uma lista de dados armazena se a atividade de cópia suporta como origens e sinks, consulte [arquivos de dados e formatos suportados](copy-activity-overview.md#supported-data-stores-and-formats).

O Azure Data Factory fornece um driver incorporado para permitir a conectividade. Não precisa de instalar manualmente a qualquer driver para utilizar este conector.

## <a name="get-started"></a>Introdução

Pode criar um pipeline que utiliza uma atividade de cópia com o SDK de .NET, o SDK de Python, Azure PowerShell, a API REST ou um modelo Azure Resource Manager. Consulte a [tutorial da atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo sobre como criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que pode usar para definir entidades do Data Factory que são específicas para o conector de Netezza.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço de Netezza ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | O **tipo** propriedade tem de ser definida como **Netezza**. | Sim |
| connectionString | Uma cadeia de ligação de ODBC para estabelecer ligação à Netezza. <br/>Marca esse campo como uma SecureString armazena de forma segura no Data Factory. Também pode colocar a palavra-passe no Azure Key Vault e obter o `pwd` configuração fora de cadeia de ligação. Consulte os exemplos seguintes e [Store credenciais no Azure Key Vault](store-credentials-in-key-vault.md) artigo com mais detalhes. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a utilizar para ligar ao arquivo de dados. Pode escolher um Runtime de integração autoalojado ou o Runtime de integração do Azure (se o seu armazenamento de dados está acessível ao público). Se não for especificado, é utilizada a predefinição de Runtime de integração do Azure. |Não |

É uma cadeia de ligação típica `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. A tabela seguinte descreve mais propriedades que pode definir:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| SecurityLevel | O nível de segurança (SSL/TLS), que o driver usa para a ligação ao arquivo de dados. Exemplo: `SecurityLevel=preferredSecured`. Os valores suportados são:<br/>- **Apenas desprotegido** (**onlyUnSecured**): O controlador não utiliza SSL.<br/>- **Preferencial não segura (preferredUnSecured) (predefinição)**: Se o servidor fornece uma opção, o driver não utiliza SSL. <br/>- **Preferencial segura (preferredSecured)**: Se o servidor fornece uma opção, o driver utiliza SSL. <br/>- **Protegido (onlySecured) apenas**: O controlador não ligar, a menos que uma conexão SSL está disponível. | Não |
| CaCertFile | O caminho completo para o certificado SSL que é utilizado pelo servidor. Exemplo: `CaCertFile=<cert path>;`| Sim, se o SSL esteja ativado |

**Exemplo**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: armazenar a palavra-passe no Azure Key Vault**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;"
            },
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Esta seção fornece uma lista de propriedades que suporta o conjunto de dados Netezza.

Para obter uma lista completa de seções e as propriedades que estão disponíveis para definir conjuntos de dados, consulte [conjuntos de dados](concepts-datasets-linked-services.md).

Para copiar dados de Netezza, defina o **tipo** propriedade do conjunto de dados para **NetezzaTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados deve ser definida como: **NetezzaTable** | Sim |
| tableName | Nome da tabela. | Não (se for especificada "consulta" na origem de atividade) |

**Exemplo**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade copy

Esta seção fornece uma lista de propriedades que suporta a origem de Netezza.

Para obter uma lista completa de seções e as propriedades que estão disponíveis para a definição de atividades, consulte [Pipelines](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza como origem

Para copiar dados de Netezza, defina o **origem** tipo de atividade de cópia para **NetezzaSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | O **tipo** propriedade da origem de atividade de cópia tem de ser definida como **NetezzaSource**. | Sim |
| query | Utilize a consulta SQL personalizada para ler os dados. Exemplo: `"SELECT * FROM MyTable"` | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
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

Para obter uma lista dos arquivos de dados que a atividade de cópia suporta como origens e sinks no Azure Data Factory, veja [arquivos de dados e formatos suportados](copy-activity-overview.md#supported-data-stores-and-formats).
