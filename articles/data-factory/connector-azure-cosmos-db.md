---
title: Copiar e transformar dados em Azure Cosmos DB (SQL API)
description: Saiba como copiar dados de e para o Azure Cosmos DB (SQL API), e transforme dados em Azure Cosmos DB (SQL API) utilizando a Data Factory.
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
ms.openlocfilehash: 7096b429145a54b5a09fe38eb8099c4ff24ac452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243617"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Copiar e transformar dados em Azure Cosmos DB (SQL API) utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-azure-documentdb-connector.md)
> * [Versão atual](connector-azure-cosmos-db.md)

Este artigo descreve como utilizar a Atividade de Cópia no Azure Data Factory para copiar dados de e para o Azure Cosmos DB (API SQL) e utilizar o Fluxo de Dados para transformar dados no Azure Cosmos DB (API SQL). Para conhecer a Azure Data Factory, leia o [artigo introdutório.](introduction.md)

>[!NOTE]
>Este conector suporta apenas cosmos DB SQL API. Para a API mongoDB, consulte o [conector para a API da Azure Cosmos DB para mongoDB](connector-azure-cosmos-db-mongodb-api.md). Outros tipos de API não são suportados agora.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Azure Cosmos DB (SQL API) é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Mapeando o fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Para a atividade de Cópia, este conector Azure Cosmos DB (SQL API) suporta:

- Copiar dados de e para o Azure Cosmos DB [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Escreva para o Azure Cosmos DB como **inserção** ou **upsert**.
- Importar e exportar documentos JSON como eis, ou copiar dados de ou para um conjunto de dados tabular. Exemplos incluem uma base de dados SQL e um ficheiro CSV. Para copiar documentos como é de ou para ficheiros JSON ou de ou para ou de outra coleção Azure Cosmos DB, consulte [importar e exportar documentos JSON](#import-and-export-json-documents).

Data Factory integra-se com a biblioteca de [executora a granel Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) para proporcionar o melhor desempenho quando escrever para o Azure Cosmos DB.

> [!TIP]
> O [vídeo Data Migration](https://youtu.be/5-SRNiC_qOU) percorre-o através dos passos de copiar dados do armazenamento do Azure Blob para o Azure Cosmos DB. O vídeo também descreve considerações de afinação de desempenho para ingerir dados para o Azure Cosmos DB em geral.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que pode utilizar para definir entidades da Fábrica de Dados específicas do Azure Cosmos DB (SQL API).

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado azure Cosmos DB (SQL API):

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** deve ser definida para **CosmosDb**. | Sim |
| conexãoString |Especifique informações necessárias para ligar à base de dados Do Azure Cosmos DB.<br />**Nota:** Deve especificar as informações da base de dados na cadeia de ligação, tal como mostrado nos exemplos que se seguem. <br/> Também pode colocar a chave de conta `accountKey` no Cofre de Chaves Azure e retirar a configuração da cadeia de ligação. Consulte as seguintes amostras e [guarde as credenciais no](store-credentials-in-key-vault.md) artigo do Cofre chave Azure com mais detalhes. |Sim |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) para utilizar para ligar à loja de dados. Pode utilizar o Tempo de Execução de Integração Azure ou um tempo de execução de integração auto-hospedado (se a sua loja de dados estiver localizada numa rede privada). Se esta propriedade não for especificada, o tempo de execução de integração azure padrão é usado. |Não |

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

**Exemplo: chave da conta da loja no Cofre chave Azure**

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets e serviços ligados.](concepts-datasets-linked-services.md)

As seguintes propriedades são suportadas para o conjunto de dados Azure Cosmos DB (SQL API): 

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** do conjunto de dados deve ser definida para **CosmosDbSqlApiCollection**. |Sim |
| coleçãoNome |O nome da coleção de documentos Azure Cosmos DB. |Sim |

Se utilizar o conjunto de dados do tipo "DocumentDbCollection", ainda é suportado como é para a retrocompatibilidade para a atividade de Copy e Lookup, não é suportado para data Flow. É-lhe sugerido que use o novo modelo para a frente.

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

## <a name="copy-activity-properties"></a>Propriedades de Copy Activity

Esta secção fornece uma lista de propriedades que a fonte do Azure Cosmos DB (SQL API) e o suporte afundado. Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) como fonte

Para copiar dados do Azure Cosmos DB (SQL API), delineie o tipo de **origem** na Copy Activity para **DocumentDbCollectionSource**. 

As seguintes propriedades são suportadas na secção fonte de **origem** da Atividade de Cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **do tipo** da fonte de atividade de cópia deve ser definida para **CosmosDbSqlApiSource**. |Sim |
| consulta |Especifique a consulta do Azure Cosmos DB para ler dados.<br/><br/>Exemplo:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Não <br/><br/>Se não especificado, esta declaração sQL é executada:`select <columns defined in structure> from mycollection` |
| regiões preferenciais | A lista preferida das regiões a ligar ao recuperar dados da Cosmos DB. | Não |
| páginaTamanho | O número de documentos por página do resultado da consulta. O padrão é "-1", o que significa que utiliza o tamanho dinâmico da página do lado do serviço até 1000. | Não |

Se utilizar a fonte do tipo "DocumentDbCollectionSource", continua a ser suportado como está para a retrocompatibilidade. É-lhe sugerido que utilize o novo modelo que vai para a frente que fornece capacidades mais ricas para copiar dados da Cosmos DB.

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

Quando copia dados da Cosmos DB, a menos que pretenda [exportar documentos JSON como está,](#import-and-export-json-documents)a melhor prática é especificar o mapeamento na atividade de cópia. Data Factory homenageia o mapeamento especificado na atividade - se uma linha não contiver um valor para uma coluna, é fornecido um valor nulo para o valor da coluna. Se não especificar um mapeamento, a Data Factory infere o esquema utilizando a primeira linha dos dados. Se a primeira linha não contiver o esquema completo, algumas colunas desaparecerão no resultado da operação de atividade.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (SQL API) como pia

Para copiar dados para o Azure Cosmos DB (SQL API), delineie o tipo de **pia** em Copy Activity para **DocumentDbCollectionSink**. 

As seguintes propriedades são suportadas na secção fonte de **origem** da Atividade de Cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** do lavatório Copy Activity deve ser definida para **CosmosDbSqlApiSink**. |Sim |
| escreverComportamento |Descreve como escrever dados para o Azure Cosmos DB. Valores permitidos: **inserir** e **serrantes**.<br/><br/>O comportamento do **upsert** é substituir o documento se um documento com o mesmo ID já existir; caso contrário, insira o documento.<br /><br />**Nota:** Data Factory gera automaticamente um ID para um documento se um ID não for especificado nem no documento original nem através do mapeamento da coluna. Isto significa que deve garantir que, para que o **upsert** funcione como esperado, o seu documento tem uma identificação. |Não<br />(a **inserção**por defeito por defeito) |
| escreverBatchSize | A Data Factory utiliza a biblioteca de [executora a granel Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) para escrever dados para o Azure Cosmos DB. A propriedade **writeBatchSize** controla o tamanho dos documentos que a ADF fornece à biblioteca. Pode tentar aumentar o valor para **a escritaBatchSize** para melhorar o desempenho e diminuir o valor se o tamanho do seu documento for grande - ver abaixo as dicas. |Não<br />(o padrão é **de 10.000)** |
| desativarAColeção Métrica | Data Factory recolhe métricas como Cosmos DB RUs para otimização de desempenho de cópia seleções e recomendações. Se estiver preocupado com este `true` comportamento, especifique para desligá-lo. | Não (padrão `false`é) |

>[!TIP]
>Para importar os documentos jSON como eis, consulte a secção de [documentos De Importação ou Exportação jSON;](#import-and-export-json-documents) para copiar a partir de dados em forma de tabular, consulte [migrate da base de dados relacional para Cosmos DB](#migrate-from-relational-database-to-cosmos-db).

>[!TIP]
>Cosmos DB limita o tamanho de um único pedido a 2MB. A fórmula é tamanho de pedido = tamanho de documento único * Tamanho do lote de escrita. Se tiver um erro a dizer "O ** `writeBatchSize` ** tamanho do pedido é demasiado **grande."**

Se utilizar a fonte do tipo "DocumentDbCollectionSink", continua a ser suportado como está para a retrocompatibilidade. É-lhe sugerido que utilize o novo modelo que vai para a frente que fornece capacidades mais ricas para copiar dados da Cosmos DB.

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

### <a name="schema-mapping"></a>Mapeamento de schema

Para copiar dados do Azure Cosmos DB para afundar tabular ou invertido, consulte o [mapeamento de esquemas](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="mapping-data-flow-properties"></a>Mapeando propriedades de fluxo de dados

Ao transformar dados no fluxo de dados de mapeamento, pode ler e escrever para coleções em Cosmos DB. Para obter mais informações, consulte a [transformação](data-flow-source.md) de origem e a transformação do [sumidouro](data-flow-sink.md) nos fluxos de dados de mapeamento.

### <a name="source-transformation"></a>Transformação de origem

As configurações específicas do Azure Cosmos DB estão disponíveis no separador **Opções De Origem** da transformação de origem. 

**Incluir colunas do sistema:** Se for ```id``` ```_ts```verdade, e outras colunas do sistema serão incluídas nos metadados de fluxo de dados da CosmosDB. Ao atualizar as coleções, é importante incluí-lo para que possa agarrar o id de linha existente.

**Tamanho da página:** O número de documentos por página do resultado da consulta. O padrão é "-1" que utiliza a página dinâmica do serviço até 1000.

**Entrada:** Defino um valor opcional para o número de RUs que gostaria de aplicar na sua coleção CosmosDB para cada execução deste fluxo de dados durante a operação de leitura. O mínimo é 400.

**Regiões preferenciais:** Escolha as regiões de leitura preferenciais para este processo.

#### <a name="json-settings"></a>Definições de JSON

**Documento único:** Selecione esta opção se a ADF for tratar todo o ficheiro como um único doc JSON.

**Nomes de colunas não citados:** Selecione esta opção se os nomes das colunas no JSON não estiverem citados.

**Tem comentários:** Utilize esta seleção se os seus documentos JSON tiverem comentários nos dados.

**Única citação:** Isto deve ser selecionado se as colunas e valores do seu documento forem citados com cotações únicas.

**Backslash escapou:** Se utilizar backslashes para escapar aos caracteres no seu JSON, escolha esta opção.

### <a name="sink-transformation"></a>Transformação de pia

As configurações específicas do Azure Cosmos DB estão disponíveis no separador **Definições** da transformação do lavatório.

Método de **atualização:** Determina quais as operações permitidas no seu destino de base de dados. O padrão é apenas permitir inserções. Para atualizar, atualizar ou eliminar linhas, é necessária uma transformação alter-row para marcar linhas para essas ações. Para atualizações, upserts e eliminações, deve ser definida uma coluna ou colunas chave para determinar qual a linha a alterar.

**Ação de recolha:** Determina se recria a coleção de destino antes da escrita.
* Nenhuma: nenhuma ação será feita à coleção.
* Recriar: A coleção será abandonada e recriada

**Tamanho do lote**: Controla quantas filas estão a ser escritas em cada balde. Tamanhos maiores do lote melhoram a compressão e a otimização da memória, mas arriscam-se a partir de exceções de memória ao cortar dados.

**Chave da partilha:** Introduza uma corda que represente a chave de partição para a sua coleção. Exemplo: ```/movies/title```

**Entrada:** Defino um valor opcional para o número de RUs que gostaria de aplicar na sua coleção CosmosDB para cada execução deste fluxo de dados. O mínimo é 400.

**Escreva o orçamento de entrada:** Um inteiro que representa o número de RUs que quer atribuir ao trabalho de spark de ingestão a granel. Este número está fora do total de produção atribuído à coleção.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="import-and-export-json-documents"></a>Documentos JSON de importação e exportação

Você pode usar este conector Azure Cosmos DB (SQL API) para facilmente:

* Copie documentos entre duas coleções Azure Cosmos DB como está.
* Importar documentos JSON de várias fontes para o Azure Cosmos DB, incluindo a partir do armazenamento Azure Blob, Azure Data Lake Store, e outras lojas baseadas em ficheiros que a Azure Data Factory suporta.
* Exportar documentos JSON de uma coleção Azure Cosmos DB para várias lojas baseadas em ficheiros.

Para obter uma cópia schema-agnóstica:

* Quando utilizar a ferramenta Copy Data, selecione o **exporto como está para ficheiros JSON ou** a opção de recolha cosmos DB.
* Quando utilizar a autoria da atividade, escolha o formato JSON com a loja de ficheiros correspondente para fonte ou pia.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Migrar da base de dados relacional para cosmos DB

Ao migrar de uma base de dados relacional, por exemplo, SQL Server para Azure Cosmos DB, a atividade de cópia pode facilmente mapear dados tabulares da fonte para aplainar documentos JSON em Cosmos DB. Em alguns casos, poderá querer redesenhar o modelo de dados para o otimizar para os casos de utilização no NoSQL de acordo com a [modelação de dados em Azure Cosmos DB](../cosmos-db/modeling-data.md), por exemplo, para desnormalizar os dados incorporando todos os subitens relacionados dentro de um documento JSON. Para este caso, consulte [este artigo](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md) com uma passagem por cima sobre como o conseguir utilizando a atividade de cópia da Azure Data Factory.

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e afunda na Azure Data Factory, consulte as lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
