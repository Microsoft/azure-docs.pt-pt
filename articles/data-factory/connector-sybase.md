---
title: Copiar dados do Sybase usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados do Sybase para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: ab2035ec344e07d88e2ac4ffb19cb1b2361f2e92
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70277473"
---
# <a name="copy-data-from-sybase-using-azure-data-factory"></a>Copiar dados do Sybase usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-onprem-sybase-connector.md)
> * [Versão atual](connector-sybase.md)

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de um banco de dado Sybase. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

É possível copiar dados do banco de dados Sybase para qualquer armazenamento de dado de coletor com suporte. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, esse conector do Sybase dá suporte a:

- SAP Sybase SQL Anywhere (ASA) **versão 16 e superior**; IQ e ASE não têm suporte.
- Copiar dados usando a autenticação **básica** ou do **Windows** .

## <a name="prerequisites"></a>Pré-requisitos

Para usar este conector Sybase, você precisa:

- Configure um Integration Runtime auto-hospedado. Ver [Integration Runtime autoalojado](create-self-hosted-integration-runtime.md) artigo para obter detalhes.
- Instale o [provedor de dados para Sybase iAnywhere. Data. SQLem qualquer lugar](https://go.microsoft.com/fwlink/?linkid=324846) 16 ou superior na máquina Integration Runtime.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao Sybase Connector.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para o serviço vinculado do Sybase:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como: **Sybase** | Sim |
| server | Nome do servidor Sybase. |Sim |
| database | Nome do banco de dados Sybase. |Sim |
| authenticationType | Tipo de autenticação usado para se conectar ao banco de dados Sybase.<br/>Valores permitidos são: **Basic**, e **Windows**. |Sim |
| username | Especifique o nome de usuário para se conectar ao banco de dados Sybase. |Sim |
| password | Especifique a senha para a conta de usuário especificada para o nome do usuário. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Um Integration Runtime auto-hospedado é necessário conforme mencionado em [pré-requisitos](#prerequisites). |Sim |

**Example:**

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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de e do Sybase.

Para copiar dados do Sybase, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como: **Sybasetable** | Sim |
| tableName | Nome da tabela no banco de dados Sybase. | Não (se for especificada "query" na origem de atividade) |

**Exemplo**

```json
{
    "name": "SybaseDataset",
    "properties": {
        "type": "SybaseTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Sybase linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Se você estivesse usando `RelationalTable` dataset tipado, ele ainda tem suporte como está, enquanto você é sugerido para usar o novo no futuro.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista das propriedades com suporte pela origem do Sybase.

### <a name="sybase-as-source"></a>Sybase como fonte

Para copiar dados do Sybase, há suporte para as seguintes propriedades na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida como: **Sybase** | Sim |
| query | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Example:**

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
                "type": "SybaseSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se você estiver usando `RelationalSource` a fonte digitada, ainda haverá suporte como está, enquanto você é sugerido para usar a nova no futuro.

## <a name="data-type-mapping-for-sybase"></a>Mapeamento de tipo de dados para Sybase

Ao copiar dados do Sybase, os seguintes mapeamentos são usados de tipos de dados do Sybase para Azure Data Factory tipos de dados provisórios. Ver [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

O Sybase dá suporte a tipos T-SQL. Para uma tabela de mapeamento de tipos SQL para Azure Data Factory tipos de dados provisórios, consulte [a seção conector de banco de dados SQL do Azure – mapeamento de tipo de dado](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database) .


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
