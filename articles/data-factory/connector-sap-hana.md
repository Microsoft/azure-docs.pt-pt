---
title: Copiar dados do SAP HANA com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados do SAP HANA para arquivos de dados de sink suportado através de uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: cdd83c3ff9d34a5e8b7f2c164136ab82f498ffb5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60343771"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Copiar dados do SAP HANA com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-sap-hana-connector.md)
> * [Versão atual](connector-sap-hana.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de uma base de dados do SAP HANA. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados da base de dados do SAP HANA de qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, oferece suporte a este conector de SAP HANA:

- Copiar dados a partir de qualquer versão da base de dados do SAP HANA.
- Copiar dados a partir **modelos de informação HANA** (por exemplo, as vistas análise e cálculo) e **tabelas de linha/coluna** através de consultas SQL.
- Copiar dados utilizando **básica** ou **Windows** autenticação.

> [!NOTE]
> Para copiar dados **em** dados SAP HANA armazenar, utilize o conector do ODBC genérico. Ver [sink de SAP HANA](connector-odbc.md#sap-hana-sink) com detalhes. Portanto, não pode ser reutilizada observe que os serviços ligados para o conector do SAP HANA e o conector do ODBC são com tipo diferente.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector de SAP HANA, tem de:

- Configure um Runtime de integração autoalojado. Ver [Integration Runtime autoalojado](create-self-hosted-integration-runtime.md) artigo para obter detalhes.
- Instale o controlador ODBC do SAP HANA na máquina do Integration Runtime. Pode transferir o controlador ODBC do SAP HANA do [SAP Software Download Center](https://support.sap.com/swdc). Pesquisa com a palavra-chave **SAP HANA cliente para Windows**.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector do SAP HANA.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do SAP HANA:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo tem de ser definida como: **SapHana** | Sim |
| servidor | Nome do servidor no qual reside a instância do SAP HANA. Se o servidor estiver a utilizar uma porta personalizada, especifique `server:port`. | Sim |
| authenticationType | Tipo de autenticação utilizado para ligar à base de dados SAP HANA.<br/>Valores permitidos são: **Básica**, e **Windows** | Sim |
| userName | Nome de utilizador que tem acesso ao servidor SAP. | Sim |
| palavra-passe | A palavra-passe do utilizador. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Um Runtime de integração autoalojado é necessário, conforme mencionado na [pré-requisitos](#prerequisites). |Sim |

**Exemplo:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo de conjuntos de dados. Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados do SAP HANA.

Para copiar dados a partir do SAP HANA, defina a propriedade de tipo de conjunto de dados para **RelationalTable**. Embora não haja nenhuma propriedade de específicos do tipo suportada para o conjunto de dados do SAP HANA de tipo RelationalTable.

**Exemplo:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas por origem do SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA como origem

Para copiar dados a partir do SAP HANA, defina o tipo de origem na atividade de cópia para **RelationalSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de origem de atividade de cópia tem de ser definida: **RelationalSource** | Sim |
| consulta | Especifica a consulta SQL para ler dados a partir da instância do SAP HANA. | Sim |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "RelationalSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-hana"></a>Mapeamento de tipo de dados para o SAP HANA

Quando se copiam dados a partir do SAP HANA, os seguintes mapeamentos são utilizados entre tipos de dados do SAP HANA para tipos de dados intermediárias do Azure Data Factory. Ver [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados do SAP HANA | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
| ALPHANUM | String |
| BIGINT | Int64 |
| BLOB | Byte[] |
| VALOR BOOLEANO | Byte |
| CLOB | Byte[] |
| DATA | DateTime |
| DECIMAL | Decimal |
| VALOR DE DUPLO | Single |
| INT | Int32 |
| NVARCHAR | String |
| REAL | Single |
| SECONDDATE | DateTime |
| SMALLINT | Int16 |
| HORA | TimeSpan |
| CARIMBO DE DATA/HORA | DateTime |
| TINYINT | Byte |
| VARCHAR | String |

## <a name="known-limitations"></a>Limitações conhecidas

Existem algumas limitações conhecidas quando se copiam dados a partir do SAP HANA:

- As cadeias NVARCHAR são truncadas para o comprimento máximo de 4000 carateres Unicode
- SMALLDECIMAL não é suportado
- VARBINARY não é suportada
- Datas válidas estão entre 1899/12/30 e 9999/12/31


## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
