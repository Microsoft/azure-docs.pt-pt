---
title: Copiar dados de Sybase com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de Sybase para arquivos de dados de sink suportado através de uma atividade de cópia num pipeline do Azure Data Factory.
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
ms.openlocfilehash: 55ff6d37f18f4ffa2f12e17bd33dd196b77f79af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61473064"
---
# <a name="copy-data-from-sybase-using-azure-data-factory"></a>Copiar dados de Sybase com o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory, que está a utilizar:"]
> * [Versão 1](v1/data-factory-onprem-sybase-connector.md)
> * [Versão atual](connector-sybase.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de uma base de dados Sybase. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de base de dados Sybase para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector Sybase suporta:

- SAP Sybase SQL em qualquer lugar (ASA) **versão 16 e acima**; QI e ASE não são suportadas.
- Copiar dados utilizando **básica** ou **Windows** autenticação.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector de Sybase, terá de:

- Configure um Runtime de integração autoalojado. Ver [Integration Runtime autoalojado](create-self-hosted-integration-runtime.md) artigo para obter detalhes.
- Instalar o [fornecedor de dados para ianywhere. data de Sybase](https://go.microsoft.com/fwlink/?linkid=324846) 16 ou superior no computador Runtime de integração.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector do Sybase.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço de Sybase ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo tem de ser definida como: **Sybase** | Sim |
| server | Nome do servidor Sybase. |Sim |
| database | Nome da base de dados Sybase. |Sim |
| authenticationType | Tipo de autenticação utilizado para ligar à base de dados Sybase.<br/>Valores permitidos são: **Básica**, e **Windows**. |Sim |
| username | Especifique o nome de utilizador para ligar à base de dados Sybase. |Sim |
| password | Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Um Runtime de integração autoalojado é necessário, conforme mencionado na [pré-requisitos](#prerequisites). |Sim |

**Exemplo:**

```json
{
    "name": "SybaseLinkedService",
    "properties": {
        "type": "Sybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "authenticationType": "Basic",
            "username": "<username>",
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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo de conjuntos de dados. Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados Sybase.

Para copiar dados de Sybase, defina a propriedade de tipo de conjunto de dados para **RelationalTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados deve ser definida como: **RelationalTable** | Sim |
| tableName | Nome da tabela na base de dados Sybase. | Não (se for especificada "consulta" na origem de atividade) |

**Exemplo**

```json
{
    "name": "SybaseDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Sybase linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas pela origem de Sybase.

### <a name="sybase-as-source"></a>Sybase como origem

Para copiar dados de Sybase, definir o tipo de origem na atividade de cópia para **RelationalSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de origem de atividade de cópia tem de ser definida: **RelationalSource** | Sim |
| query | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSybase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Sybase input dataset name>",
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
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sybase"></a>Tipo de dados de mapeamento para Sybase

Ao copiar dados de Sybase, os seguintes mapeamentos são utilizados entre tipos de dados Sybase para tipos de dados intermediárias do Azure Data Factory. Ver [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

Sybase suporta tipos de T-SQL. Para uma tabela de mapeamento de tipos SQL para tipos de dados intermediárias do Azure Data Factory, veja [conector de base de dados de SQL do Azure - mapeamento de tipo de dados](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database) secção.


## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
