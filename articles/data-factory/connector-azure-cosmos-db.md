---
title: Copiar e transformar dados em Azure Cosmos DB (SQL API)
description: Aprenda a copiar dados de e para a Azure Cosmos DB (SQL API), e transforme dados em Azure Cosmos DB (SQL API) utilizando a Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: d42f30ebd72dca81255ddc02a9440db19979536d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608074"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Copiar e transformar os dados no Azure Cosmos DB (API SQL) com o Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-azure-documentdb-connector.md)
> * [Versão atual](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia no Azure Data Factory para copiar dados de e para o Azure Cosmos DB (API SQL) e utilizar o Fluxo de Dados para transformar dados no Azure Cosmos DB (API SQL). Para saber mais sobre a Azure Data Factory, leia o [artigo introdutório](introduction.md).



>[!NOTE]
>Este conector suporta apenas a Cosmos DB SQL API. Para a MongoDB API, consulte o [conector para a API da Azure Cosmos DB para a MongoDB](connector-azure-cosmos-db-mongodb-api.md). Outros tipos de API não são suportados agora.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Azure Cosmos DB (SQL API) é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Para a atividade copy, este conector Azure Cosmos DB (SQL API) suporta:

- Copie os dados de e para o AZure Cosmos DB [SQL API](../cosmos-db/introduction.md).
- Escreva para Azure Cosmos DB como **inserção** ou **upsert**.
- Importar e exportar documentos JSON como é, ou copiar dados de ou para um conjunto de dados tabular. Exemplos incluem uma base de dados SQL e um ficheiro CSV. Para copiar documentos de ou para os ficheiros JSON ou de ou de outra coleção DB da Azure Cosmos, consulte [documentos de Importação e Exportação de JSON](#import-and-export-json-documents).

A Data Factory integra-se com a biblioteca de executores a [granel Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) para proporcionar o melhor desempenho quando escreve para a Azure Cosmos DB.

> [!TIP]
> O [vídeo da Migração](https://youtu.be/5-SRNiC_qOU) de Dados acompanha-o através dos passos de cópia de dados do armazenamento de Azure Blob para a Azure Cosmos DB. O vídeo também descreve considerações de afinação de desempenho para ingerir dados para Azure Cosmos DB em geral.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que pode usar para definir entidades da Data Factory que são específicas da Azure Cosmos DB (SQL API).

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado Azure Cosmos DB (SQL API):

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** deve ser definida para **CosmosDb**. | Yes |
| conexãoStragem |Especifique as informações necessárias para ligar à base de dados DB do Azure Cosmos.<br />**Nota:** Deve especificar as informações da base de dados na cadeia de ligação, tal como indicado nos exemplos que se seguem. <br/> Também pode colocar a chave de conta no Cofre da Chave Azure e retirar a `accountKey` configuração da cadeia de ligação. Consulte as seguintes amostras e [guarde as credenciais no artigo do Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. |Yes |
| connectVia | O [Tempo de Execução de Integração](concepts-integration-runtime.md) para ligar à loja de dados. Pode utilizar o Tempo de Execução da Integração Azure ou um tempo de integração auto-hospedado (se a sua loja de dados estiver localizada numa rede privada). Se esta propriedade não for especificada, o tempo de execução de integração Azure predefinido é utilizado. |No |

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

**Exemplo: chave da conta da loja no Cofre da Chave Azure**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets e serviços ligados](concepts-datasets-linked-services.md).

As seguintes propriedades são suportadas para o conjunto de dados Azure Cosmos DB (SQL API): 

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do **tipo** do conjunto de dados deve ser definida para **CosmosDbSqlApiCollection**. |Yes |
| coleçãoName |O nome da coleção de documentos Azure Cosmos DB. |Yes |

Se utilizar o conjunto de dados do tipo "DocumentDbCollection", ainda é suportado como é para retrocompatibilidade para a atividade copy e Lookup, não é suportado para o Fluxo de Dados. Sugere-se que use o novo modelo para a frente.

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

## <a name="copy-activity-properties"></a>Propriedades de Atividade de Cópia

Esta secção fornece uma lista de propriedades que a fonte Estruz Cosmos DB (SQL API) e o suporte do lavatório. Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) como fonte

Para copiar dados da Azure Cosmos DB (SQL API), decreva o tipo **de fonte** em Atividade de Cópia para **DocumentDbCollectionSource**. 

As seguintes propriedades são suportadas na secção **origem** da Atividade de Cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A  propriedade tipo da fonte de atividade da cópia deve ser definida para **CosmosDbSqlApiSource**. |Yes |
| consulta |Especifique a consulta DB do Azure Cosmos para ler dados.<br/><br/>Exemplo:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |No <br/><br/>Se não for especificada, esta declaração SQL é executada: `select <columns defined in structure> from mycollection` |
| Regiões preferenciais | A lista preferida das regiões a que se conectam ao recuperar dados da Cosmos DB. | No |
| tamanho de página | O número de documentos por página do resultado da consulta. O padrão é "-1", o que significa que utiliza o tamanho da página dinâmica do lado do serviço até 1000. | No |
| detectarDatetime | Se detetar a data a partir dos valores de cadeia nos documentos. Os valores permitidos são: **verdadeiros** (padrão), **falsos.** | No |

Se utilizar a fonte do tipo "DocumentDbCollectionSource", ainda é suportada como é para retrocompatibilidade. Sugere-se que utilize o novo modelo que vai para a frente, que fornece capacidades mais ricas para copiar dados da Cosmos DB.

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

Ao copiar dados da Cosmos DB, a menos que queira [exportar documentos JSON como-é,](#import-and-export-json-documents)a melhor prática é especificar o mapeamento na atividade da cópia. A Data Factory honra o mapeamento especificado na atividade - se uma linha não contiver um valor para uma coluna, é fornecido um valor nulo para o valor da coluna. Se não especificar um mapeamento, a Data Factory infere o esquema utilizando a primeira linha dos dados. Se a primeira linha não contiver o esquema completo, algumas colunas desaparecerão no resultado da operação.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (SQL API) como pia

Para copiar dados para Azure Cosmos DB (SQL API), **decreva** o tipo de pia em Copy Activity para **DocumentDbCollectionSink**. 

As seguintes propriedades são suportadas na secção de **lavatório** Copy Activity:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do **tipo** do lavatório Copy Activity deve ser definida para **CosmosDbSqlApiSink**. |Yes |
| escrever Comportamento |Descreve como escrever dados para Azure Cosmos DB. Valores permitidos: **inserir** e **aumentar.**<br/><br/>O comportamento do **upsert** é substituir o documento se já existir um documento com o mesmo ID; caso contrário, insira o documento.<br /><br />**Nota:** A Data Factory gera automaticamente um ID para um documento se um ID não for especificado no documento original ou por mapeamento de colunas. Isto significa que deve garantir que, para que **o upsert** funcione como esperado, o seu documento tenha uma identificação. |No<br />(o padrão é **inserir)** |
| escreverBatchSize | A Data Factory utiliza a [biblioteca de executores a granel Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) para escrever dados para a Azure Cosmos DB. A **propriedade writeBatchSize** controla o tamanho dos documentos que a ADF fornece à biblioteca. Pode tentar aumentar o valor para **escreverBatchSize** para melhorar o desempenho e diminuir o valor se o tamanho do seu documento for grande - veja abaixo as dicas. |No<br />(o padrão é **de 10.000)** |
| desativaçãoMetricosCollecto | A Data Factory recolhe métricas como Cosmos DB RUs para otimização de desempenho de cópia e recomendações. Se estiver preocupado com este comportamento, especifique `true` para desligá-lo. | Não (o padrão `false` é) |
| maxConcurrentConnections |O limite superior das ligações simultâneas estabelecidas na loja de dados durante a atividade. Especifique um valor apenas quando pretende limitar ligações simultâneas.| No |


>[!TIP]
>Para importar documentos JSON como é, consulte a secção [de documentos de importação ou exportação de JSON;](#import-and-export-json-documents) para copiar a partir de dados em forma de tabular, consulte [a Migração da base de dados relacional para a Cosmos DB](#migrate-from-relational-database-to-cosmos-db).

>[!TIP]
>Cosmos DB limita o tamanho de um único pedido para 2MB. A fórmula é tamanho de pedido = tamanho único do documento * Escreva o tamanho do lote. Se atingir um erro dizendo **"O tamanho do pedido é demasiado grande."** **`writeBatchSize`**

Se utilizar a fonte do tipo "DocumentDbCollectionSink", ainda é suportada como é para retrocompatibilidade. Sugere-se que utilize o novo modelo que vai para a frente, que fornece capacidades mais ricas para copiar dados da Cosmos DB.

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

### <a name="schema-mapping"></a>Mapeamento de Schema

Para copiar dados da Azure Cosmos DB para afundar ou inverter, consulte o [mapeamento de esquemas](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Ao transformar dados no fluxo de dados de mapeamento, você pode ler e escrever para coleções em Cosmos DB. Para obter mais informações, consulte a [transformação](data-flow-source.md) da fonte e [a transformação do sumidouro](data-flow-sink.md) nos fluxos de dados de mapeamento.

### <a name="source-transformation"></a>Transformação de origem

As definições específicas do Azure Cosmos DB estão disponíveis no separador **Opções De Origem** da transformação da fonte. 

**Incluir colunas do sistema:** Se for verdade, ```id``` ```_ts``` , e outras colunas do sistema serão incluídas nos metadados do fluxo de dados da CosmosDB. Ao atualizar as coleções, é importante incluí-lo para que possa agarrar o id de linha existente.

**Tamanho da página:** O número de documentos por página do resultado da consulta. O padrão é "-1" que utiliza a página dinâmica do serviço até 1000.

**Produção:** Descreva um valor opcional para o número de RUs que gostaria de aplicar à sua coleção CosmosDB para cada execução deste fluxo de dados durante a operação de leitura. O mínimo é 400.

**Regiões preferenciais:** Escolha as regiões de leitura preferidas para este processo.

#### <a name="json-settings"></a>Definições de JSON

**Documento único:** Selecione esta opção se a ADF tratar todo o ficheiro como um único doc JSON.

**Nomes de colunas não citados:** Selecione esta opção se os nomes das colunas no JSON não forem citados.

**Tem comentários:** Utilize esta seleção se os seus documentos JSON tiverem comentários nos dados.

**Único citado:** Isto deve ser selecionado se as colunas e valores do seu documento forem citados com cotações únicas.

**Backslash escapou:** Se utilizar as costas para escapar aos caracteres no seu JSON, escolha esta opção.

### <a name="sink-transformation"></a>Transformação do sumidouro

As definições específicas do Azure Cosmos DB estão disponíveis no **separador Definições** da transformação do lavatório.

**Método de atualização:** Determina quais as operações permitidas no seu destino de base de dados. O padrão é apenas permitir inserções. Para atualizar, intensificar ou apagar linhas, é necessária uma transformação de linhas alteradas para marcar linhas para essas ações. Para atualizações, atualizações e eliminações, deve ser definida uma coluna ou colunas-chave para determinar qual a linha a alterar.

**Ação de recolha:** Determina se deve recriar a coleção de destino antes de escrever.
* Nenhuma: nenhuma ação será feita à coleção.
* Recriar: A coleção será abandonada e recriada

**Tamanho do lote**: Um inteiro que representa quantos objetos estão a ser escritos para a coleção Cosmos DB em cada lote. Normalmente, começar pelo tamanho do lote predefinido é suficiente. Para afinar ainda mais este valor, note:

- Cosmos DB limita o tamanho de um único pedido para 2MB. A fórmula é "Tamanho do Pedido = Tamanho único do documento * Tamanho do lote". Se atingir um erro dizendo "O tamanho do pedido é demasiado grande", reduza o valor do tamanho do lote.
- Quanto maior for o tamanho do lote, melhor a produção ADF pode alcançar, ao mesmo tempo que se certifica de que aloca RUs suficientes para capacitar a sua carga de trabalho.

**Chave de partição:** Introduza uma corda que represente a chave de partição da sua coleção. Exemplo: ```/movies/title```

**Produção:** Descreva um valor opcional para o número de RUs que gostaria de aplicar à sua coleção CosmosDB para cada execução deste fluxo de dados. O mínimo é 400.

**Escreva o orçamento de produção:** Um número inteiro que representa as RUs que pretende atribuir para esta operação de escrita data Flow, a partir do total de produção atribuído à coleção.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="import-and-export-json-documents"></a>Importação e exportação de documentos JSON

Pode utilizar este conector Azure Cosmos DB (SQL API) facilmente:

* Copie documentos entre duas coleções DB da Azure Cosmos como está.
* Importar documentos da JSON de várias fontes para a Azure Cosmos DB, incluindo do armazenamento Azure Blob, Azure Data Lake Store, e outras lojas baseadas em ficheiros que a Azure Data Factory suporta.
* Exporte documentos JSON de uma coleção DB da Azure Cosmos para várias lojas baseadas em ficheiros.

Para obter cópia schema-agnóstica:

* Quando utilizar a ferramenta Dados de Cópia, selecione a **exportação como-é para ficheiros JSON ou opção de recolha Cosmos DB.**
* Quando utilizar a autoria da atividade, escolha o formato JSON com a loja de ficheiros correspondente para obter origem ou pia.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Migrar da base de dados relacional para a Cosmos DB

Ao migrar de uma base de dados relacional, por exemplo.SQL Servidor para Azure Cosmos DB, a atividade de cópia pode facilmente mapear dados tabulares de origem para achatar documentos JSON em Cosmos DB. Em alguns casos, poderá querer redesenhar o modelo de dados para otimizá-lo para os casos de utilização noSQL de acordo com a modelação de [dados em Azure Cosmos DB,](../cosmos-db/modeling-data.md)por exemplo, para desnormalizar os dados incorporando todos os sub-itens relacionados dentro de um documento JSON. Para tal caso, consulte [este artigo](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md) com uma análise sobre como alcançá-lo utilizando a atividade de cópia da Azure Data Factory.

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e sumidouros na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)