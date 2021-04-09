---
title: Copiar dados para índice de pesquisa
description: Saiba como empurrar ou copiar dados para um índice de pesquisa Azure utilizando a Atividade de Cópia num pipeline Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 82f5d4f4c3f12b6e14e260fa004bb031247e1096
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597509"
---
# <a name="copy-data-to-an-azure-cognitive-search-index-using-azure-data-factory"></a>Copiar dados para um índice de pesquisa cognitiva Azure usando Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-azure-search-connector.md)
> * [Versão atual](connector-azure-search.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados no índice de Pesquisa Cognitiva Azure. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de qualquer loja de dados de origem suportada no índice de pesquisa. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector de Pesquisa Cognitiva Azure.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado à Pesquisa Cognitiva Azure:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **AzureSearch** | Yes |
| url | URL para o serviço de pesquisa. | Yes |
| key | Chave de administração para o serviço de pesquisa. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Yes |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Pode utilizar o Tempo de Execução da Integração Azure ou o Tempo de Execução de Integração Auto-hospedado (se a sua loja de dados estiver localizada em rede privada). Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. |No |

> [!IMPORTANT]
> Ao copiar dados de uma loja de dados em nuvem para índice de pesquisa, no serviço Azure Cognitive Search ligado, você precisa encaminhar um Tempo de Execução de Integração Azure com região explícita em ConnactVia. Desacorda a região como a que reside o seu serviço de pesquisa. Saiba mais sobre o tempo de execução da [integração Azure.](concepts-integration-runtime.md#azure-integration-runtime)

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados de Pesquisa Cognitiva Azure.

Para copiar dados na Azure Cognitive Search, as seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **AzureSearchIndex** | Yes |
| nome do índice | Nome do índice de pesquisa. A Data Factory não cria o índice. O índice deve existir na Pesquisa Cognitiva Azure. | Yes |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte de Pesquisa Cognitiva Azure.

### <a name="azure-cognitive-search-as-sink"></a>Pesquisa Cognitiva Azure como pia

Para copiar dados para a Azure Cognitive Search, defina o tipo de origem na atividade de cópia para **AzureSearchIndexSink**. As seguintes propriedades são suportadas na secção de **lavatório** de atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **AzureSearchIndexSink** | Yes |
| escrever Comportamento | Especifica se deve fundir ou substituir quando um documento já existe no índice. Consulte a [propriedade WriteBehavior](#writebehavior-property).<br/><br/>Os valores permitidos são: **Fusão** (padrão) e **Upload**. | No |
| escreverBatchSize | Envia dados para o índice de pesquisa quando o tamanho do tampão atinge o writeBatchSize. Consulte a [propriedade WriteBatchSize](#writebatchsize-property) para mais detalhes.<br/><br/>Os valores permitidos são: inteiro 1 a 1.000; padrão é 1000. | No |
| maxConcurrentConnections |O limite superior das ligações simultâneas estabelecidas na loja de dados durante a atividade. Especifique um valor apenas quando pretende limitar ligações simultâneas.| No |

### <a name="writebehavior-property"></a>Propriedade WriteBehavior

AzureSearchSinksersers ao escrever dados. Por outras palavras, ao escrever um documento, se a chave de documento já existe no índice de pesquisa, a Azure Cognitive Search atualiza o documento existente em vez de lançar uma exceção de conflito.

O AzureSearchSink fornece os seguintes dois comportamentos mais abrangentes (utilizando a AzureSearch SDK):

- **Fusão:** combine todas as colunas do novo documento com a existente. Para colunas com valor nulo no novo documento, o valor do existente é preservado.
- **Upload**: O novo documento substitui o existente. Para as colunas não especificadas no novo documento, o valor é definido para nula se existe ou não um valor não nulo no documento existente.

O comportamento padrão é **a Fusão.**

### <a name="writebatchsize-property"></a>Propriedade WriteBatchsize

O serviço de Pesquisa Cognitiva Azure suporta a escrita de documentos como um lote. Um lote pode conter 1 a 1.000 Ações. Uma ação trata de um documento para executar a operação de upload/fusão.

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

A tabela seguinte especifica se um tipo de dados de pesquisa cognitiva Azure é suportado ou não.

| Tipo de dados de pesquisa cognitiva Azure | Suportado em Azure Cognitive Search Sink |
| ---------------------- | ------------------------------ |
| String | Y |
| Int32 | Y |
| Int64 | Y |
| Double (Duplo) | Y |
| Booleano | Y |
| DataTimeOffset | Y |
| Matriz de Cordas | N |
| GeografiaPoint | N |

Atualmente outros tipos de dados, por exemplo, ComplexType não são suportados. Para obter uma lista completa dos tipos de dados suportados pela Azure Cognitive Search, consulte [tipos de dados suportados (Pesquisa Cognitiva Azure)](/rest/api/searchservice/supported-data-types).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)