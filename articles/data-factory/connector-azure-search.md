---
title: Copiar dados para o índice de pesquisa usando Azure Data Factory | Microsoft Docs
description: Saiba mais sobre como enviar por Push ou copiar dados para um índice do Azure Search usando a atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: bc914d27e9bea15a625dd2cd196401d733f8cfb8
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70275942"
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Copiar dados para um índice de Azure Search usando Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-azure-search-connector.md)
> * [Versão atual](connector-azure-search.md)

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados no índice Azure Search. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Você pode copiar dados de qualquer armazenamento de dados de origem com suporte para Azure Search índice. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector Azure Search.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para Azure Search serviço vinculado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como: **AzureSearch** | Sim |
| url | URL para o serviço de Azure Search. | Sim |
| key | Chave de administração para o serviço de Azure Search. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o Runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

> [!IMPORTANT]
> Ao copiar dados de um armazenamento de dados de nuvem para Azure Search índice, em Azure Search serviço vinculado, você precisará fazer referência a um Azure Integration Runtime com uma região explícita no connactVia. Defina a região como a Azure Search reside. Saiba mais em [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime).

**Example:**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de Azure Search DataSet.

Para copiar dados para o Azure Search, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como: **AzureSearchIndex** | Sim |
| indexName | Nome do índice de Azure Search. Data Factory não cria o índice. O índice deve existir no Azure Search. | Sim |

**Example:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "typeProperties" : {
            "indexName": "products"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista das propriedades com suporte pela origem do Azure Search.

### <a name="azure-search-as-sink"></a>Azure Search como coletor

Para copiar dados em Azure Search, defina o tipo de fonte na atividade de cópia como **AzureSearchIndexSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida como: **AzureSearchIndexSink** | Sim |
| writeBehavior | Especifica se é para mesclar ou substituir quando um documento já existe no índice. Consulte a [Propriedade WriteBehavior](#writebehavior-property).<br/><br/>Valores permitidos são: **Mesclagem** (padrão) e **carregar**. | Não |
| writeBatchSize | Carrega dados no índice de Azure Search quando o tamanho do buffer atinge writeBatchSize. Consulte a [Propriedade WriteBatchSize](#writebatchsize-property) para obter detalhes.<br/><br/>Os valores permitidos são: inteiro 1 a 1.000; o padrão é 1000. | Não |

### <a name="writebehavior-property"></a>Propriedade WriteBehavior

AzureSearchSink upserts ao gravar dados. Em outras palavras, ao gravar um documento, se a chave de documento já existir no índice de Azure Search, Azure Search atualizará o documento existente em vez de lançar uma exceção de conflito.

O AzureSearchSink fornece os dois comportamentos de Upsert a seguir (usando o SDK do AzureSearch):

- **Mesclagem**: combine todas as colunas no novo documento com a existente. Para colunas com valor nulo no novo documento, o valor do existente é preservado.
- **Carregar**: O novo documento substitui o existente. Para colunas não especificadas no novo documento, o valor é definido como nulo se há um valor não nulo no documento existente ou não.

O comportamento padrão é **mesclar**.

### <a name="writebatchsize-property"></a>Propriedade WriteBatchSize

Azure Search serviço dá suporte à gravação de documentos como um lote. Um lote pode conter de 1 a 1.000 ações. Uma ação manipula um documento para executar a operação de upload/mesclagem.

**Example:**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

### <a name="data-type-support"></a>Suporte ao tipo de dados

A tabela a seguir especifica se um tipo de dados Azure Search tem suporte ou não.

| Tipo de dados Azure Search | Com suporte no coletor Azure Search |
| ---------------------- | ------------------------------ |
| Cadeia | S |
| Int32 | S |
| Int64 | S |
| Double | S |
| Boolean | S |
| DataTimeOffset | S |
| String Array | N |
| GeographyPoint | N |

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
