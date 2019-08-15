---
title: Copiar dados do Netezza com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de Netezza para arquivos de dados de sink suportado através de uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: c3c179cfbf86c2dddfb34b46540aba8898038751
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966496"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Copiar dados do Netezza com o Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de Netezza. O artigo se baseia no [atividade de cópia no Azure Data Factory](copy-activity-overview.md), que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de Netezza para qualquer arquivo de dados de sink suportados. Para obter uma lista de dados armazena se a atividade de cópia suporta como origens e sinks, consulte [arquivos de dados e formatos suportados](copy-activity-overview.md#supported-data-stores-and-formats).

O Azure Data Factory fornece um driver incorporado para permitir a conectividade. Não precisa de instalar manualmente a qualquer driver para utilizar este conector.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

Pode criar um pipeline que utiliza uma atividade de cópia com o SDK de .NET, o SDK de Python, Azure PowerShell, a API REST ou um modelo Azure Resource Manager. Consulte a [tutorial da atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo sobre como criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que pode usar para definir entidades do Data Factory que são específicas para o conector de Netezza.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço de Netezza ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | O **tipo** propriedade tem de ser definida como **Netezza**. | Sim |
| connectionString | Uma cadeia de ligação de ODBC para estabelecer ligação à Netezza. <br/>Marque este campo como uma SecureString para armazená-lo com segurança em Data Factory. Você também pode colocar a senha em Azure Key Vault e efetuar pull da `pwd` configuração da cadeia de conexão. Consulte os exemplos a seguir e [armazene as credenciais no artigo Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a utilizar para ligar ao arquivo de dados. Saiba mais na seção de [pré-requisitos](#prerequisites) . Se não for especificado, é utilizada a predefinição de Runtime de integração do Azure. |Não |

É uma cadeia de ligação típica `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. A tabela seguinte descreve mais propriedades que pode definir:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| SecurityLevel | O nível de segurança (SSL/TLS), que o driver usa para a ligação ao arquivo de dados. Exemplo: `SecurityLevel=preferredSecured`. Os valores suportados são:<br/>- **Somente não seguro** (**onlyUnSecured**): O driver não usa SSL.<br/>- **Preferencial não seguro (preferredUnSecured) (padrão)** : Se o servidor fornecer uma opção, o driver não usará SSL. <br/>- **Seguro preferencial (preferredSecured)** : Se o servidor fornecer uma opção, o driver usará SSL. <br/>- **Somente protegido (onlySecured)** : O driver não se conecta, a menos que uma conexão SSL esteja disponível. | Não |
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

**Exemplo: armazenar a senha no Azure Key Vault**

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
| type | A propriedade Type do conjunto de conjuntos deve ser definida como: **NetezzaTable** | Sim |
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
