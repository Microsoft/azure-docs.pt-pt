---
title: Copiar dados do Sybase usando o Azure Data Factory
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
ms.openlocfilehash: 4877706c487f389539e8e5c73bb984b01d200f38
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680109"
---
# <a name="copy-data-from-sybase-using-azure-data-factory"></a>Copiar dados do Sybase usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-onprem-sybase-connector.md)
> * [Versão atual](connector-sybase.md)

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de um banco de dado Sybase. Ele se baseia no artigo [visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector do Sybase tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

É possível copiar dados do banco de dados Sybase para qualquer armazenamento de dado de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como fontes/coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

Especificamente, esse conector do Sybase dá suporte a:

- SAP Sybase SQL Anywhere (ASA) **versão 16 e superior**; IQ e ASE não têm suporte.
- Copiar dados usando a autenticação **básica** ou do **Windows** .

## <a name="prerequisites"></a>Pré-requisitos

Para usar este conector Sybase, você precisa:

- Configure um Integration Runtime auto-hospedado. Confira o artigo de [Integration Runtime auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.
- Instale o [provedor de dados para Sybase iAnywhere. Data. SQLem qualquer lugar](https://go.microsoft.com/fwlink/?linkid=324846) 16 ou superior na máquina Integration Runtime.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao Sybase Connector.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Sybase:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **Sybase** | Sim |
| servidor | Nome do servidor Sybase. |Sim |
| base de dados | Nome do banco de dados Sybase. |Sim |
| authenticationType | Tipo de autenticação usado para se conectar ao banco de dados Sybase.<br/>Os valores permitidos são: **básico**e **Windows**. |Sim |
| o nome de utilizador | Especifique o nome de usuário para se conectar ao banco de dados Sybase. |Sim |
| palavra-passe | Especifique a senha para a conta de usuário especificada para o nome do usuário. Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Um Integration Runtime auto-hospedado é necessário conforme mencionado em [pré-requisitos](#prerequisites). |Sim |

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

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . Esta seção fornece uma lista das propriedades com suporte pelo conjunto de e do Sybase.

Para copiar dados do Sybase, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **sybasetable** | Sim |
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

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela origem do Sybase.

### <a name="sybase-as-source"></a>Sybase como fonte

Para copiar dados do Sybase, há suporte para as seguintes propriedades na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como: **Sybase** | Sim |
| consulta | Use a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se "TableName" no DataSet for especificado) |

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

Se você estivesse usando `RelationalSource` fonte tipada, ainda há suporte no estado em que se encontra, enquanto você é sugerido para usar o novo no futuro.

## <a name="data-type-mapping-for-sybase"></a>Mapeamento de tipo de dados para Sybase

Ao copiar dados do Sybase, os seguintes mapeamentos são usados de tipos de dados do Sybase para Azure Data Factory tipos de dados provisórios. Consulte [mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e o esquema de origem para o coletor.

O Sybase dá suporte a tipos T-SQL. Para uma tabela de mapeamento de tipos SQL para Azure Data Factory tipos de dados provisórios, consulte [a seção conector de banco de dados SQL do Azure – mapeamento de tipo de dado](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database) .

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).



## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
