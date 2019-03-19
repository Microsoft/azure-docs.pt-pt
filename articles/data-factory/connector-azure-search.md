---
title: Copiar dados para o índice de pesquisa com o Azure Data Factory | Documentos da Microsoft
description: Saiba mais sobre como enviar por push ou copiar dados para um índice de pesquisa do Azure com a atividade de cópia num pipeline do Azure Data Factory.
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
ms.openlocfilehash: 1c8cbcd2e5f137b1e8381dcce164ae9a4b87e804
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57852859"
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Copiar dados para um índice da Azure Search utilizando o Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-azure-search-connector.md)
> * [Versão atual](connector-azure-search.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados para o índice da Azure Search. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de qualquer arquivo de dados de origem suportada para o índice da Azure Search. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector do Azure Search.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do Azure Search:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida como: **AzureSearch** | Sim |
| url | URL para o serviço Azure Search. | Sim |
| key | Chave de administrador para o serviço Azure Search. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o Runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

> [!IMPORTANT]
> Ao copiar dados a partir de um arquivo de dados na cloud para o índice da Azure Search, no Azure Search, serviço é ligado, precisa fazer referência um Runtime de integração do Azure com uma região explícita em connactVia. Defina a região que a pesquisa do Azure reside. Saiba mais a partir da [Runtime de integração do Azure](concepts-integration-runtime.md#azure-integration-runtime).

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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo de conjuntos de dados. Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados do Azure Search.

Para copiar dados para o Azure Search, defina a propriedade de tipo de conjunto de dados para **RelationalTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados deve ser definida como: **AzureSearchIndex** | Sim |
| indexName | Nome do índice da Azure Search. Fábrica de dados não cria o índice. O índice tem de existir no Azure Search. | Sim |

**Exemplo:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": {
            "referenceName": "<Azure Search linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties" : {
            "indexName": "products"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas pela origem de Azure Search.

### <a name="azure-search-as-sink"></a>O Azure Search como sink

Para copiar dados para o Azure Search, defina o tipo de origem na atividade de cópia para **AzureSearchIndexSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de origem de atividade de cópia tem de ser definida: **AzureSearchIndexSink** | Sim |
| WriteBehavior | Especifica se deve intercalar ou substituir quando um documento já existe no índice. Consulte a [WriteBehavior propriedade](#writebehavior-property).<br/><br/>Valores permitidos são: **Intercalar** (predefinição), e **carregar**. | Não |
| writeBatchSize | Carrega dados para o índice da Azure Search, quando o tamanho do buffer atinge writeBatchSize. Consulte a [WriteBatchSize propriedade](#writebatchsize-property) para obter detalhes.<br/><br/>Valores permitidos são: número inteiro de 1 a 1000; a predefinição é 1000. | Não |

### <a name="writebehavior-property"></a>Propriedade de WriteBehavior

AzureSearchSink upserts ao gravar dados. Em outras palavras, ao escrever um documento, se a chave do documento já existe no índice de pesquisa do Azure, o Azure Search atualiza o documento existente, em vez de gerar uma exceção de conflito.

O AzureSearchSink fornece os seguintes dois comportamentos de upsert (ao utilizar o SDK do AzureSearch):

- **Intercalar**: combinar todas as colunas no novo documento com a já existente. Para colunas com um valor nulo no documento novo, o valor a já existente é preservado.
- **Carregar**: O novo documento substitui a já existente. Para colunas não especificadas no novo documento, o valor é definido como nulo se existe um valor não nulo no documento existente ou não.

O comportamento predefinido é **intercalar**.

### <a name="writebatchsize-property"></a>Propriedade de WriteBatchSize

O serviço de pesquisa do Azure suporta a escrita de documentos como um lote. Um batch pode conter ações de 1 a 1000. Uma ação processa um documento para executar a operação de carregamento/intercalação.

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

### <a name="data-type-support"></a>Tipo de dados de suporte

A seguinte tabela especifica se um tipo de dados do Azure Search é suportado ou não.

| Tipo de dados do Azure Search | Suportado no Sink do Azure Search |
| ---------------------- | ------------------------------ |
| String | S |
| Int32 | S |
| Int64 | S |
| Double | S |
| Booleano | S |
| DataTimeOffset | S |
| Matriz de cadeia de caracteres | N |
| GeographyPoint | N |

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
