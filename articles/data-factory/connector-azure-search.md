---
title: Copiar dados para índice de pesquisa
description: Saiba como empurrar ou copiar dados para um índice de pesquisa Azure utilizando a Atividade de Cópia num pipeline Azure Data Factory.
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
ms.openlocfilehash: dfa1ad318ccc9e891b646ec050f6a0776e108206
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418240"
---
# <a name="copy-data-to-an-azure-cognitive-search-index-using-azure-data-factory"></a>Copiar dados para um índice de pesquisa cognitiva Azure usando a Fábrica de Dados Azure

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-azure-search-connector.md)
> * [Versão atual](connector-azure-search.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados no índice de Pesquisa Cognitiva Azure. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de qualquer loja de dados de origem suportada no índice de pesquisa. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Fábrica de Dados específicas do conector de Pesquisa Cognitiva Azure.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado à Pesquisa Cognitiva Azure:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **AzureSearch** | Sim |
| url | URL para o serviço de pesquisa. | Sim |
| key | Chave de administrador para o serviço de pesquisa. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Sim |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. Pode utilizar o Tempo de Execução de Integração Azure ou o Tempo de Execução de Integração Auto-hospedado (se a sua loja de dados estiver localizada em rede privada). Se não especificado, utiliza o tempo de funcionar de integração azure padrão. |Não |

> [!IMPORTANT]
> Ao copiar dados de uma loja de dados em nuvem para índice de pesquisa, no serviço ligado à Pesquisa Cognitiva Azure, é necessário remeter um Runtime de Integração Azure com região explícita na ConnactVia. Desloque a região como aquela onde reside o seu serviço de pesquisa. Saiba mais com o Tempo de Execução de [Integração Azure.](concepts-integration-runtime.md#azure-integration-runtime)

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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo conjuntos de [dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados da Pesquisa Cognitiva Azure.

Para copiar dados em Pesquisa Cognitiva Azure, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **AzureSearchIndex** | Sim |
| nome de índice | Nome do índice de pesquisa. Data Factory não cria o índice. O índice deve existir na Pesquisa Cognitiva Azure. | Sim |

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

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte de Pesquisa Cognitiva Azure.

### <a name="azure-cognitive-search-as-sink"></a>Pesquisa Cognitiva Azure como pia

Para copiar dados em Pesquisa Cognitiva Azure, delineie o tipo de origem na atividade de cópia para **AzureSearchIndexSink**. As seguintes propriedades são suportadas na secção de **sumidouro** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **AzureSearchIndexSink** | Sim |
| escreverComportamento | Especifica se deve fundir ou substituir quando um documento já existe no índice. Consulte a [propriedade WriteBehavior](#writebehavior-property).<br/><br/>Os valores permitidos são: **Fusão** (predefinido) e **Upload**. | Não |
| escreverBatchSize | Envia dados para o índice de pesquisa quando o tamanho do tampão atinge o writeBatchSize. Consulte a [propriedade WriteBatchSize](#writebatchsize-property) para mais detalhes.<br/><br/>Os valores permitidos são: inteiro 1 a 1.000; padrão é 1000. | Não |

### <a name="writebehavior-property"></a>WriteBehavior propriedade

AzureSearchSink upserts ao escrever dados. Por outras palavras, ao escrever um documento, se a chave do documento já existir no índice de pesquisa, a Pesquisa Cognitiva Azure atualiza o documento existente em vez de lançar uma exceção de conflito.

O AzureSearchSink fornece os seguintes dois comportamentos upsert (utilizando o AzureSearch SDK):

- **Fusão**: combine todas as colunas do novo documento com a existente. Para colunas com valor nulo no novo documento, o valor no existente é preservado.
- **Upload**: O novo documento substitui o existente. Para colunas não especificadas no novo documento, o valor é definido para anular se existe ou não um valor não nulo no documento existente.

O comportamento padrão é **merge**.

### <a name="writebatchsize-property"></a>Propriedade WriteBatchSize

O serviço de Pesquisa Cognitiva Azure suporta a escrita de documentos como um lote. Um lote pode conter 1 a 1.000 Ações. Uma ação trata de um documento para realizar a operação de upload/fusão.

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

## <a name="data-type-support"></a>Suporte de tipo de dados

A tabela seguinte especifica se um tipo de dados de Pesquisa Cognitiva Azure é suportado ou não.

| Tipo de dados de pesquisa cognitiva azure | Suportado em Pia de Pesquisa Cognitiva Azure |
| ---------------------- | ------------------------------ |
| String | S |
| Int32 | S |
| Int64 | S |
| Double | S |
| Booleano | S |
| DataTimeOffset | S |
| Array de cordas | N |
| GeografiaPoint | N |

Atualmente outros tipos de dados, por exemplo, o ComplexType não são suportados. Para obter uma lista completa dos tipos de dados suportados pela Pesquisa Cognitiva Azure, consulte tipos de [dados suportados (Pesquisa Cognitiva Azure)](https://docs.microsoft.com/rest/api/searchservice/supported-data-types).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
