---
title: Copiar dados para o índice de pesquisa
description: Saiba mais sobre como enviar por Push ou copiar dados para um índice do Azure Search usando a atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/13/2019
ms.openlocfilehash: 8a5b7bd366c504f0f5f4652728bf265289fb92e8
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929680"
---
# <a name="copy-data-to-an-azure-cognitive-search-index-using-azure-data-factory"></a>Copiar dados para um índice de Pesquisa Cognitiva do Azure usando Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-azure-search-connector.md)
> * [Versão atual](connector-azure-search.md)

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados para o índice de Pesquisa Cognitiva do Azure. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Você pode copiar dados de qualquer armazenamento de dados de origem com suporte no índice de pesquisa. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector de Pesquisa Cognitiva do Azure.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para o serviço vinculado do Azure Pesquisa Cognitiva:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **AzureSearch** | Sim |
| url | URL para o serviço de pesquisa. | Sim |
| key | Chave de administração para o serviço de pesquisa. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o Runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

> [!IMPORTANT]
> Ao copiar dados de um armazenamento de dados de nuvem no índice de pesquisa, no serviço vinculado do Azure Pesquisa Cognitiva, você precisará consultar um Azure Integration Runtime com uma região explícita no connactVia. Defina a região como aquela em que o serviço de pesquisa reside. Saiba mais em [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime).

**Exemplo:**

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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de Pesquisa Cognitiva do Azure.

Para copiar dados para o Azure Pesquisa Cognitiva, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **AzureSearchIndex** | Sim |
| indexName | Nome do índice de pesquisa. Data Factory não cria o índice. O índice deve existir no Pesquisa Cognitiva do Azure. | Sim |

**Exemplo:**

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
            "referenceName": "<Azure Cognitive Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista das propriedades com suporte pela fonte de Pesquisa Cognitiva do Azure.

### <a name="azure-cognitive-search-as-sink"></a>Pesquisa Cognitiva do Azure como coletor

Para copiar dados para o Azure Pesquisa Cognitiva, defina o tipo de fonte na atividade de cópia como **AzureSearchIndexSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como: **AzureSearchIndexSink** | Sim |
| writeBehavior | Especifica se é para mesclar ou substituir quando um documento já existe no índice. Consulte a [Propriedade WriteBehavior](#writebehavior-property).<br/><br/>Os valores permitidos são: **Merge** (padrão) e **upload**. | Não |
| writeBatchSize | Carrega dados no índice de pesquisa quando o tamanho do buffer atinge writeBatchSize. Consulte a [Propriedade WriteBatchSize](#writebatchsize-property) para obter detalhes.<br/><br/>Os valores permitidos são: inteiro 1 a 1.000; o padrão é 1000. | Não |

### <a name="writebehavior-property"></a>Propriedade WriteBehavior

AzureSearchSink upserts ao gravar dados. Em outras palavras, ao gravar um documento, se a chave do documento já existir no índice de pesquisa, o Azure Pesquisa Cognitiva atualizará o documento existente em vez de lançar uma exceção de conflito.

O AzureSearchSink fornece os dois comportamentos de Upsert a seguir (usando o SDK do AzureSearch):

- **Mesclagem**: combine todas as colunas no novo documento com a existente. Para colunas com valor nulo no novo documento, o valor do existente é preservado.
- **Carregar**: o novo documento substitui o existente. Para colunas não especificadas no novo documento, o valor é definido como nulo se há um valor não nulo no documento existente ou não.

O comportamento padrão é **mesclar**.

### <a name="writebatchsize-property"></a>Propriedade WriteBatchSize

O serviço de Pesquisa Cognitiva do Azure dá suporte à gravação de documentos como um lote. Um lote pode conter de 1 a 1.000 ações. Uma ação manipula um documento para executar a operação de upload/mesclagem.

**Exemplo:**

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
                "referenceName": "<Azure Cognitive Search output dataset name>",
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

## <a name="data-type-support"></a>Suporte ao tipo de dados

A tabela a seguir especifica se um tipo de dados do Azure Pesquisa Cognitiva tem suporte ou não.

| Tipo de dados Pesquisa Cognitiva do Azure | Com suporte no coletor de Pesquisa Cognitiva do Azure |
| ---------------------- | ------------------------------ |
| String | S |
| Int32 | S |
| Int64 | S |
| Double | S |
| Booleano | S |
| DataTimeOffset | S |
| String Array | N |
| GeographyPoint | N |

Atualmente, outros tipos de dados, por exemplo, complexType, não são suportados. Para obter uma lista completa de tipos de dados com suporte do Azure Pesquisa Cognitiva, consulte [tipos de dados com suporte (azure pesquisa cognitiva)](https://docs.microsoft.com/rest/api/searchservice/supported-data-types).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
