---
title: Copiar dados de fontes do IBM Informix usando Azure Data Factory
description: Saiba como copiar dados das fontes do IBM Informix para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: cb4b81f7c5e219c520078ecf34eba3f5e98da684
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75892578"
---
# <a name="copy-data-from-and-to-ibm-informix-data-stores-using-azure-data-factory"></a>Copiar dados de e para armazenamentos de dados IBM Informix usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um armazenamento de dados do IBM Informix. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector do Informix tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados da origem da Informix para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse conector Informix, você precisa:

- Configure um Integration Runtime auto-hospedado. Ver [Integration Runtime autoalojado](create-self-hosted-integration-runtime.md) artigo para obter detalhes.
- Instale o driver ODBC do Informix para o armazenamento de dados no computador Integration Runtime. Por exemplo, você pode usar o driver "IBM INFORMIX Informix DRIVER (64-bit)".

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector Informix.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para o serviço vinculado do Informix:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **Informix** | Sim |
| connectionString | A cadeia de conexão ODBC excluindo a parte da credencial. Você pode especificar a cadeia de conexão ou usar o DSN do sistema (nome da fonte de dados) que você configurou no computador Integration Runtime (você ainda precisa especificar a parte da credencial no serviço vinculado adequadamente). <br> Você também pode colocar uma senha em Azure Key Vault e efetuar pull do `password` configuração fora da cadeia de conexão. Consulte [armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes.| Sim |
| authenticationType | Tipo de autenticação usado para se conectar ao armazenamento de dados Informix.<br/>Os valores permitidos são: **básico** e **anônimo**. | Sim |
| userName | Especifique o nome de usuário se você estiver usando a autenticação básica. | Não |
| palavra-passe | Especifique a senha para a conta de usuário especificada para o nome do usuário. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| credencial | A parte de credencial de acesso da cadeia de conexão especificada no formato de valor de propriedade específico do driver. Marque este campo como uma SecureString. | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Um Integration Runtime auto-hospedado é necessário conforme mencionado em [pré-requisitos](#prerequisites). |Sim |

**Exemplo:**

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Informix",
        "typeProperties": {
            "connectionString": "<Informix connection string or DSN>",
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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de acordos do Informix.

Para copiar dados do Informix, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **informixtable** | Sim |
| tableName | Nome da tabela no Informix. | Não para origem (se "consulta" na origem da atividade for especificada);<br/>Sim para o coletor |

**Exemplo**

```json
{
    "name": "InformixDataset",
    "properties": {
        "type": "InformixTable",
        "linkedServiceName": {
            "referenceName": "<Informix linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista das propriedades com suporte pela origem da Informix.

### <a name="informix-as-source"></a>Informix como fonte

Para copiar dados do Informix, há suporte para as seguintes propriedades na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como: **Informix** | Sim |
| consulta | Use a consulta personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Informix input dataset name>",
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
                "type": "InformixSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
