---
title: Copiar e transformar dados no Azure Cosmos DB (API do SQL)
description: Saiba como copiar dados de e para Azure Cosmos DB (API do SQL) e transformar dados em Azure Cosmos DB (API do SQL) usando Data Factory.
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/11/2019
ms.openlocfilehash: 2a712b7b9ce1982f40465e95754f0bae9cce5c58
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444435"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Copiar e transformar dados em Azure Cosmos DB (API do SQL) usando Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-azure-documentdb-connector.md)
> * [Versão atual](connector-azure-cosmos-db.md)

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de e para Azure Cosmos DB (API do SQL) e usar o fluxo de dados para transformar dados em Azure Cosmos DB (API do SQL). Para saber mais sobre o Azure Data Factory, leia os [artigo introdutório](introduction.md).

>[!NOTE]
>Este conector dá suporte apenas à API do SQL Cosmos DB. Para a API do MongoDB, consulte o [conector para a API de Azure Cosmos DB para MongoDB](connector-azure-cosmos-db-mongodb-api.md). Não há suporte para outros tipos de API agora.

## <a name="supported-capabilities"></a>Capacidades suportadas

Esse conector de Azure Cosmos DB (API do SQL) tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Mapeando fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Para a atividade de cópia, esse conector de Azure Cosmos DB (API do SQL) dá suporte a:

- Copiar dados de e para o Azure Cosmos DB [API do SQL](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Escrever para o Azure Cosmos DB como **inserir** ou **upsert**.
- Importar e exportar documentos JSON como-é ou copiar dados de ou para um conjunto de dados em tabela. Os exemplos incluem uma base de dados SQL e um ficheiro CSV. Para copiar documentos como estão para ou de arquivos JSON ou de outra coleção de Azure Cosmos DB, consulte [importar e exportar documentos JSON](#import-and-export-json-documents).

Fábrica de dados se integra com o [biblioteca de executor do Azure Cosmos DB em massa](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) para proporcionar o melhor desempenho quando escreve para o Azure Cosmos DB.

> [!TIP]
> O [vídeo de migração de dados](https://youtu.be/5-SRNiC_qOU) explica-lhe os passos para copiar dados do armazenamento de Blobs do Azure para o Azure Cosmos DB. O vídeo também descreve considerações de ajuste de desempenho para a ingestão de dados para o Azure Cosmos DB em geral.

## <a name="get-started"></a>Começar

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que podem ser usadas para definir Data Factory entidades específicas para Azure Cosmos DB (API do SQL).

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para o serviço vinculado do Azure Cosmos DB (API do SQL):

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade tem de ser definida como **CosmosDb**. | Sim |
| connectionString |Especifique as informações necessárias para ligar à base de dados do Azure Cosmos DB.<br />**Tenha em atenção**: tem de especificar informações de base de dados na cadeia de ligação conforme mostrado nos exemplos que se seguem. <br/> Você também pode colocar a chave de conta em Azure Key Vault e extrair a configuração de `accountKey` da cadeia de conexão. Consulte os exemplos a seguir e [armazene as credenciais no artigo Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a utilizar para ligar ao arquivo de dados. Pode usar o Runtime de integração do Azure ou um runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se esta propriedade não for especificada, é utilizada a predefinição de Runtime de integração do Azure. |Não |

**Exemplo**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: armazenar chave de conta no Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Para obter uma lista completa de seções e as propriedades que estão disponíveis para definir conjuntos de dados, consulte [conjuntos de dados e serviços ligados](concepts-datasets-linked-services.md).

As propriedades a seguir têm suporte para o conjunto de Azure Cosmos DB (API do SQL): 

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** do conjunto de conjuntos deve ser definida como **CosmosDbSqlApiCollection**. |Sim |
| collectionName |O nome da coleção de documentos do Azure Cosmos DB. |Sim |

Se você usar o conjunto de dados do tipo "DocumentDbCollection", ainda terá suporte como está para compatibilidade com versões anteriores para a atividade de cópia e pesquisa, não há suporte para o fluxo de dados. Você é sugerido para usar o novo modelo no futuro.

**Exemplo**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade copy

Esta seção fornece uma lista de propriedades com suporte do coletor e da fonte do Azure Cosmos DB (API do SQL). Para obter uma lista completa de seções e as propriedades que estão disponíveis para a definição de atividades, consulte [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (API do SQL) como fonte

Para copiar dados de Azure Cosmos DB (API do SQL), defina o tipo de **fonte** na atividade de cópia como **DocumentDbCollectionSource**. 

As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** da fonte da atividade de cópia deve ser definida como **CosmosDbSqlApiSource**. |Sim |
| consulta |Especifique a consulta do Azure Cosmos DB para ler os dados.<br/><br/>Exemplo:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Não <br/><br/>Se não for especificado, essa instrução de SQL é executada: `select <columns defined in structure> from mycollection` |
| preferredRegions | A lista preferencial de regiões às quais se conectar ao recuperar dados de Cosmos DB. | Não |
| pageSize | O número de documentos por página do resultado da consulta. O padrão é "-1", que significa usar o tamanho de página dinâmico do lado do serviço até 1000. | Não |

Se você usar a origem do tipo "DocumentDbCollectionSource", ainda terá suporte como está para compatibilidade com versões anteriores. É recomendável usar o novo modelo no futuro, que fornece recursos mais avançados para copiar dados de Cosmos DB.

**Exemplo**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Ao copiar dados de Cosmos DB, a menos que você queira [exportar documentos JSON no estado em que se encontram](#import-and-export-json-documents), a prática recomendada é especificar o mapeamento na atividade de cópia. Data Factory honra o mapeamento especificado na atividade-se uma linha não contiver um valor para uma coluna, um valor nulo será fornecido para o valor da coluna. Se você não especificar um mapeamento, o Data Factory inferirá o esquema usando a primeira linha dos dados. Se a primeira linha não contiver o esquema completo, algumas colunas estarão ausentes no resultado da operação de atividade.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (API do SQL) como coletor

Para copiar dados para Azure Cosmos DB (API do SQL), defina o tipo de **coletor** na atividade de cópia como **DocumentDbCollectionSink**. 

As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** do coletor da atividade de cópia deve ser definida como **CosmosDbSqlApiSink**. |Sim |
| writeBehavior |Descreve como escrever dados do Azure Cosmos DB. Valores permitidos: **inserir** e **upsert**.<br/><br/>O comportamento das **upsert** é substituir o documento se um documento com o mesmo ID já existe; caso contrário, insira o documento.<br /><br />**Tenha em atenção**: Data Factory gera automaticamente um ID de um documento se não for especificado um ID do documento original ou por mapeamento de colunas. Isso significa que é necessário garantir que, para **upsert** a funcionar conforme esperado, o seu documento tem um ID. |Não<br />(a predefinição é **inserir**) |
| writeBatchSize | O Data Factory utiliza a [biblioteca de executor do Azure Cosmos DB em massa](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) para escrever dados do Azure Cosmos DB. A propriedade **writeBatchSize** controla o tamanho dos documentos que o ADF fornece à biblioteca. Pode experimentar aumentar o valor para **writeBatchSize** para melhorar o desempenho e a diminuição do valor se o documento ser grandes de tamanho - veja abaixo dicas. |Não<br />(a predefinição é **10.000**) |
| disableMetricsCollection | Data Factory coleta métricas como Cosmos DB RUs para otimização e recomendações de desempenho de cópia. Se você estiver preocupado com esse comportamento, especifique `true` para desativá-lo. | Não (o padrão é `false`) |

>[!TIP]
>Para importar documentos JSON no estado em que se encontram, consulte a seção [importar ou exportar documentos JSON](#import-and-export-json-documents) ; para copiar de dados em formato de tabela, consulte [migrar do banco de dado relacional para Cosmos DB](#migrate-from-relational-database-to-cosmos-db).

>[!TIP]
>O cosmos DB limita o tamanho de único pedido a 2MB. A fórmula é o tamanho do pedido = único Document Size * o tamanho do lote de escrita. Se tiver atingido o ditado de erro **"Tamanho pedido é demasiado grande."** , **reduzir a `writeBatchSize` valor** na configuração de sink de cópia.

Se você usar a origem do tipo "DocumentDbCollectionSink", ainda terá suporte como está para compatibilidade com versões anteriores. É recomendável usar o novo modelo no futuro, que fornece recursos mais avançados para copiar dados de Cosmos DB.

**Exemplo**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

### <a name="schema-mapping"></a>Mapeamento de esquema

Para copiar dados de Azure Cosmos DB para o coletor tabular ou vice-versa, consulte [mapeamento de esquema](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="mapping-data-flow-properties"></a>Mapeando Propriedades de fluxo de dados

Ao transformar dados no fluxo de dados de mapeamento, você pode ler e gravar em coleções no Cosmos DB. Para obter mais informações, consulte a [transformação origem](data-flow-source.md) e a [transformação coletor](data-flow-sink.md) no mapeamento de fluxos de dados.

### <a name="source-transformation"></a>Transformação de origem

As configurações específicas para Azure Cosmos DB estão disponíveis na guia **Opções de origem** da transformação origem. 

**Incluir colunas do sistema:** Se for true, ```id```, ```_ts```e outras colunas do sistema serão incluídas nos metadados do fluxo de dados do CosmosDB. Ao atualizar as coleções, é importante incluí-las para que você possa obter a ID de linha existente.

**Tamanho da página:** O número de documentos por página do resultado da consulta. O padrão é "-1", que usa a página dinâmica do serviço até 1000.

**Taxa de transferência:** Defina um valor opcional para o número de RUs que você gostaria de aplicar à sua coleção CosmosDB para cada execução desse fluxo de dados durante a operação de leitura. O mínimo é 400.

**Regiões preferenciais:** Escolha as regiões de leitura preferenciais para esse processo.

### <a name="sink-transformation"></a>Transformação do coletor

As configurações específicas para Azure Cosmos DB estão disponíveis na guia **configurações** da transformação coletor.

**Método de atualização:** Determina quais operações são permitidas no destino do banco de dados. O padrão é permitir apenas inserções. Para atualizar, upsertr ou excluir linhas, uma transformação ALTER-Row é necessária para marcar linhas para essas ações. Para atualizações, upserts e exclusões, uma coluna ou colunas de chave deve ser definida para determinar qual linha alterar.

**Ação de coleta:** Determina se a coleção de destino deve ser recriada antes da gravação.
* Nenhum: nenhuma ação será feita para a coleção.
* Recriar: a coleção será descartada e recriada

**Tamanho do lote**: controla quantas linhas estão sendo gravadas em cada Bucket. Tamanhos de lote maiores melhoram a compactação e a otimização de memória, mas não têm risco de exceções de memória ao armazenar dados em cache.

**Chave de partição:** Insira uma cadeia de caracteres que represente a chave de partição para sua coleção. Exemplo: ```/movies/title```

**Taxa de transferência:** Defina um valor opcional para o número de RUs que você gostaria de aplicar à sua coleção CosmosDB para cada execução desse fluxo de dados. O mínimo é 400.

**Orçamento de taxa de transferência de gravação:** Um inteiro que representa o número de RUs que você deseja alocar para o trabalho de ingestão em massa do Spark. Esse número está fora da taxa de transferência total alocada para a coleção.

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="import-and-export-json-documents"></a>Importar e exportar documentos JSON

Você pode usar esse conector de Azure Cosmos DB (API do SQL) para facilmente:

* Copiar documentos entre duas coleções do Azure Cosmos DB como-é.
* Importe documentos JSON de várias origens para o Azure Cosmos DB, incluindo a partir do armazenamento de Blobs do Azure, Azure Data Lake Store e outros arquivos baseados em ficheiros que suporte o Azure Data Factory.
* Exporte documentos JSON de uma coleção do Azure Cosmos DB para vários arquivos baseados em ficheiros.

Para alcançar a cópia de esquema desconhecido:

* Quando utiliza a ferramenta copiar dados, selecione o **exportar como-é ficheiros JSON ou coleção do Cosmos DB** opção.
* Ao usar a criação de atividade, escolha o formato JSON com o repositório de arquivos correspondente para a origem ou o coletor.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Migrar do banco de dados relacional para Cosmos DB

Ao migrar de um banco de dados relacional, por exemplo, SQL Server para Azure Cosmos DB, a atividade de cópia pode facilmente mapear dados tabulares de documentos JSON de origem para mesclar em Cosmos DB. Em alguns casos, talvez você queira recriar o modelo de dados para otimizá-lo para os casos de uso NoSQL de acordo com a [modelagem de dados no Azure Cosmos DB](../cosmos-db/modeling-data.md), por exemplo, para desnormalizar os dados inserindo todos os subitens relacionados em um documento JSON. Para tal caso, consulte [esta postagem de blog](https://medium.com/@ArsenVlad/denormalizing-via-embedding-when-copying-data-from-sql-to-cosmos-db-649a649ae0fb) com um passo a passos sobre como obtê-lo usando Azure data Factory atividade de cópia.

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista dos arquivos de dados que a atividade de cópia suporta como origens e sinks no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
