---
title: Indexar uma fonte de dados Azure Cosmos DB-Azure Search
description: Rastreie uma fonte de dados Azure Cosmos DB e ingerir dados em um índice pesquisável de texto completo no Azure Search. Indexadores automatizam a ingestão de dados para fontes de dados selecionadas como Azure Cosmos DB.
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 802a4e9c6191d33051eb075543691845595bc9c3
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "69656700"
---
# <a name="how-to-index-cosmos-db-using-an-azure-search-indexer"></a>Como indexar Cosmos DB usando um indexador de Azure Search


> [!Note]
> O suporte à API do MongoDB está em versão prévia e não se destina ao uso em produção. A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece esse recurso. Não há suporte para Portal ou SDK do .NET no momento.
>
> A API do SQL está geralmente disponível.

Este artigo mostra como configurar um [indexador](search-indexer-overview.md) Azure Cosmos DB para extrair conteúdo e torná-lo pesquisável no Azure Search. Este fluxo de trabalho cria um índice de Azure Search e o carrega com o texto existente extraído do Azure Cosmos DB. 

Como a terminologia pode ser confusa, vale a pena observar que [Azure Cosmos DB indexação](https://docs.microsoft.com/azure/cosmos-db/index-overview) e a [indexação de Azure Search](search-what-is-an-index.md) são operações distintas, exclusivas para cada serviço. Antes de iniciar a indexação de Azure Search, seu banco de dados Azure Cosmos DB já deve existir e conter dados.

Você pode usar o [portal](#cosmos-indexer-portal), as APIs REST ou o SDK do .net para indexar o conteúdo do cosmos. O indexador Cosmos DB no Azure Search pode rastrear [itens Cosmos do Azure](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) acessados por meio desses protocolos:

* [API DO SQL](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference) 
* [API do MongoDB (versão prévia)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)

> [!Note]
> A voz do usuário tem itens existentes para suporte adicional à API. Você pode transmitir um voto para as APIs do cosmos que você gostaria de ver com suporte no Azure Search: [API de tabela](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab), [API do Graph](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4) [API do Apache Cassandra](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu).
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Utilizar o portal

O método mais fácil para indexação de itens de Cosmos do Azure é usar um assistente no [portal do Azure](https://portal.azure.com/). Por amostragem de dados e leitura de metadados no contêiner, o assistente de [**importação de dados**](search-import-data-portal.md) no Azure Search pode criar um índice padrão, mapear campos de origem para campos de índice de destino e carregar o índice em uma única operação. Dependendo do tamanho e da complexidade dos dados de origem, você pode ter um índice operacional de pesquisa de texto completo em minutos.

É recomendável usar a mesma assinatura do Azure para Azure Search e Azure Cosmos DB, preferencialmente na mesma região.

### <a name="1---prepare-source-data"></a>1-preparar dados de origem

Você deve ter uma conta Cosmos, um banco de dados Cosmos do Azure mapeado para a API do SQL ou a API do MongoDB e um contêiner de documentos JSON. 

Certifique-se de que seu banco de dados do Cosmos DB contenha dado. O [Assistente de importação de dados](search-import-data-portal.md) lê metadados e executa a amostragem de dados para inferir um esquema de índice, mas também carrega dados de Cosmos DB. Se os dados estiverem ausentes, o assistente parará com esse erro "erro ao detectar o esquema de índice da fonte de dados: não foi possível compilar um índice de protótipo porque a fonte de dados ' emptycollection ' não retornou nenhum dado".

### <a name="2---start-import-data-wizard"></a>2-iniciar o assistente de importação de dados

Você pode [iniciar o assistente](search-import-data-portal.md) na barra de comandos na página Azure Search serviço ou clicando em **Adicionar Azure Search** na seção **configurações** do painel de navegação esquerdo da sua conta de armazenamento.

   ![Comando importar dados no portal](./media/search-import-data-portal/import-data-cmd2.png "Iniciar o assistente de importação de dados")

### <a name="3---set-the-data-source"></a>3-definir a fonte de dados

> [!NOTE] 
> No momento, não é possível criar ou editar fontes de dados do **MongoDB** usando portal do Azure ou o SDK do .net. No entanto, você **pode** monitorar o histórico de execução de indexadores do MongoDB no Portal.

Na página **fonte de dados** , a origem deve ser **Cosmos DB**, com as seguintes especificações:

+ **Nome** é o nome do objeto de fonte de dados. Depois de criado, você pode escolher para outras cargas de trabalho.

+ A **conta de Cosmos DB** deve ser a cadeia de conexão primária ou secundária do cosmos DB, com um `AccountEndpoint` e um `AccountKey`. A conta determina se os dados são convertidos como API do SQL ou API do Mongo DB

+ **Banco** de dados é um banco de dados existente da conta. 

+ A **coleção** é um contêiner de documentos. Os documentos devem existir para que a importação seja realizada com sucesso. 

+ A **consulta** poderá ficar em branco se você quiser todos os documentos, caso contrário, poderá inserir uma consulta que selecione um subconjunto de documentos. 

   ![Cosmos DB definição de fonte de dados](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB definição de fonte de dados")

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4-ignorar a página "Adicionar pesquisa cognitiva" no assistente

Adicionar habilidades cognitivas não é necessário para a importação de documentos. A menos que você tenha uma necessidade específica de [incluir API de serviços cognitivos e transformações](cognitive-search-concept-intro.md) em seu pipeline de indexação, ignore esta etapa.

Para ignorar a etapa, primeiro vá para a próxima página.

   ![Botão próxima página para pesquisa cognitiva](media/search-get-started-portal/next-button-add-cog-search.png)

Nessa página, você pode pular para a personalização do índice.

   ![Ignorar o passo de capacidades cognitivas](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5-definir atributos de índice

Na página **índice** , você verá uma lista de campos com um tipo de dados e uma série de caixas de seleção para definir atributos de índice. O assistente pode gerar uma lista de campos com base em metadados e por amostragem dos dados de origem. 

Você pode selecionar atributos em massa clicando na caixa de seleção na parte superior de uma coluna de atributo. Escolha **recuperável** e **pesquisável** para cada campo que deve ser retornado a um aplicativo cliente e sujeito ao processamento de pesquisa de texto completo. Você observará que inteiros não são de texto completo ou de pesquisa difusa (os números são avaliados de forma idêntica e geralmente são úteis em filtros).

Examine a descrição de [atributos de índice](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) e [analisadores de idioma](https://docs.microsoft.com/rest/api/searchservice/language-support) para obter mais informações. 

Reserve um tempo para examinar suas seleções. Depois de executar o assistente, as estruturas de dados físicos são criadas e você não poderá editar esses campos sem descartar e recriar todos os objetos.

   ![Definição de índice de Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Definição de índice de Cosmos DB")

### <a name="6---create-indexer"></a>6-criar indexador

Totalmente especificado, o assistente cria três objetos distintos em seu serviço de pesquisa. Um objeto de fonte de dados e um objeto de índice são salvos como recursos nomeados em seu serviço de Azure Search. A última etapa cria um objeto de indexador. Nomear o indexador permite que ele exista como um recurso autônomo, que você pode agendar e gerenciar independentemente do índice e do objeto de fonte de dados, criado na mesma sequência do assistente.

Se você não estiver familiarizado com indexadores, um *indexador* é um recurso em Azure Search que rastreia uma fonte de dados externa para conteúdo pesquisável. A saída do assistente de **importação de dados** é um indexador que rastreia sua fonte de dados Cosmos DB, extrai o conteúdo pesquisável e o importa para um índice em Azure Search.

A captura de tela a seguir mostra a configuração padrão do indexador. Você pode alternar para **uma vez** se quiser executar o indexador uma vez. Clique em **Enviar** para executar o assistente e criar todos os objetos. A indexação começa imediatamente.

   ![Cosmos DB definição do indexador](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB definição do indexador")

Você pode monitorar a importação de dados nas páginas do Portal. As notificações de progresso indicam o status de indexação e quantos documentos são carregados. 

Quando a indexação estiver concluída, você poderá usar o [Search Explorer](search-explorer.md) para consultar o índice.

> [!NOTE]
> Se você não vir os dados esperados, talvez seja necessário definir mais atributos em mais campos. Exclua o índice e o indexador que você acabou de criar e percorra o assistente novamente, modificando suas seleções de atributos de índice na etapa 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Utilizar APIs REST

Você pode usar a API REST para indexar Azure Cosmos DB dados, seguindo um fluxo de trabalho de três partes comum a todos os indexadores no Azure Search: criar uma fonte de dados, criar um índice, criar um indexador. A extração de dados do armazenamento Cosmos ocorre quando você envia a solicitação criar indexador. Depois que essa solicitação for concluída, você terá um índice passível de consulta. 

Se você estiver avaliando o MongoDB, deverá usar o `api-version=2019-05-06-Preview` REST para criar a fonte de dados.

Em sua conta do Cosmos DB, você pode escolher se deseja que a coleção indexe automaticamente todos os documentos. Por padrão, todos os documentos são indexados automaticamente, mas você pode desativar a indexação automática. Quando a indexação está desativada, os documentos podem ser acessados somente por meio de seus links automáticos ou por consultas usando a ID do documento. Azure Search requer que Cosmos DB indexação automática seja ativada na coleção que será indexada por Azure Search. 

> [!WARNING]
> Azure Cosmos DB é a próxima geração do DocumentDB. Anteriormente, com a versão **2017-11-11** da API, você poderia usar a sintaxe `documentdb`. Isso significa que você pode especificar o tipo de fonte de dados como `cosmosdb` ou `documentdb`. A partir da versão de API **2019-05-06** , o portal e as APIs de Azure Search só dão suporte à sintaxe de `cosmosdb` conforme instruído neste artigo. Isso significa que o tipo de fonte de dados deve `cosmosdb` se você deseja se conectar a um ponto de extremidade de Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1-montar entradas para a solicitação

Para cada solicitação, você deve fornecer o nome do serviço e a chave de administração para Azure Search (no cabeçalho da POSTAgem) e o nome da conta de armazenamento e a chave para o armazenamento de BLOBs. Você pode usar o [postmaster](search-get-started-postman.md) para enviar solicitações HTTP para Azure Search.

Copie os quatro valores a seguir no bloco de notas para que você possa colá-los em uma solicitação:

+ Nome do serviço de Azure Search
+ Azure Search chave de administração
+ Cosmos DB cadeia de conexão

Você pode encontrar esses valores no Portal:

1. Nas páginas do portal para Azure Search, copie a URL do serviço de pesquisa da página Visão geral.

2. No painel de navegação esquerdo, clique em **chaves** e copie a chave primária ou secundária (elas são equivalentes).

3. Alterne para as páginas do portal para sua conta de armazenamento do cosmos. No painel de navegação à esquerda, em **configurações**, clique em **chaves**. Esta página fornece um URI, dois conjuntos de cadeias de conexão e dois conjuntos de chaves. Copie uma das cadeias de conexão para o bloco de notas.

### <a name="2---create-a-data-source"></a>2-criar uma fonte de dados

Uma **fonte de dados** especifica os dados a serem indexados, as credenciais e as políticas para identificar alterações nos dados (como documentos modificados ou excluídos dentro de sua coleção). A fonte de dados é definida como um recurso independente para que possa ser usada por vários indexadores.

Para criar uma fonte de dados, formule uma solicitação POST:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

O corpo da solicitação contém a definição da fonte de dados, que deve incluir os seguintes campos:

| Campo   | Descrição |
|---------|-------------|
| **nomes** | Necessário. Escolha qualquer nome para representar o objeto de fonte de dados. |
|**tipo**| Necessário. Deve ser `cosmosdb`. |
|**fornecidas** | Necessário. Deve ser uma cadeia de conexão Cosmos DB.<br/>Para coleções SQL, as cadeias de conexão estão neste formato: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/>Para coleções do MongoDB, adicione **ApiKind = MongoDB** à cadeia de conexão:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/>Evite números de porta na URL do ponto de extremidade. Se você incluir o número da porta, Azure Search não poderá indexar o banco de dados Azure Cosmos DB.|
| **Container** | Contém os seguintes elementos: <br/>**nome**: obrigatório. Especifique a ID da coleção de banco de dados a ser indexada.<br/>**consulta**: opcional. Você pode especificar uma consulta para mesclar um documento JSON arbitrário em um esquema simples que Azure Search pode indexar.<br/>Para coleções do MongoDB, não há suporte para consultas. |
| **dataChangeDetectionPolicy** | Aconselhável. Consulte a seção [indexando documentos alterados](#DataChangeDetectionPolicy) .|
|**dataDeletionDetectionPolicy** | Opcional. Consulte a seção [indexando documentos excluídos](#DataDeletionDetectionPolicy) .|

### <a name="using-queries-to-shape-indexed-data"></a>Usando consultas para formatar dados indexados
Você pode especificar uma consulta SQL para mesclar propriedades aninhadas ou matrizes, projetar Propriedades JSON e filtrar os dados a serem indexados. 

> [!WARNING]
> Não há suporte para consultas personalizadas para coleções do **MongoDB** : o parâmetro `container.query` deve ser definido como nulo ou omitido. Se você precisar usar uma consulta personalizada, informe-nos sobre o [User Voice](https://feedback.azure.com/forums/263029-azure-search).

Documento de exemplo:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }

Consulta de filtro:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Consulta de mesclagem:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Consulta de projeção:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Consulta de mesclagem de matriz:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3-criar um índice de pesquisa de destino 

[Crie um índice de Azure Search de destino](/rest/api/searchservice/create-index) se você ainda não tiver um. O exemplo a seguir cria um índice com um campo de ID e descrição:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

Verifique se o esquema do índice de destino é compatível com o esquema dos documentos JSON de origem ou a saída da projeção de consulta personalizada.

> [!NOTE]
> Para coleções particionadas, a chave de documento padrão é Azure Cosmos DB `_rid` Propriedade, que Azure Search automaticamente renomear como `rid` porque os nomes de campo não podem começar com um caractere sublinhado. Além disso, Azure Cosmos DB valores de `_rid` contêm caracteres inválidos em chaves Azure Search. Por esse motivo, os valores de `_rid` são codificados em base64.
> 
> Para coleções do MongoDB, Azure Search renomeia automaticamente a propriedade `_id` como `doc_id`.  

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mapeamento entre tipos de dados JSON e tipos de dados Azure Search
| Tipo de dados JSON | Tipos de campo de índice de destino compatíveis |
| --- | --- |
| bool |EDM. Boolean, EDM. String |
| Números que se parecem com números inteiros |EDM. Int32, EDM. Int64, EDM. String |
| Números que se parecem com pontos flutuantes |EDM. Double, EDM. String |
| String |Edm.String |
| Matrizes de tipos primitivos, por exemplo ["a", "b", "c"] |Coleção (Edm.String) |
| Cadeias de caracteres parecidas com datas |EDM. DateTimeOffset, EDM. String |
| Objetos geojson, por exemplo {"tipo": "ponto", "coordenadas": [Long, Lat]} |Edm.GeographyPoint |
| Outros objetos JSON |N/A |

### <a name="4---configure-and-run-the-indexer"></a>4-configurar e executar o indexador

Depois que o índice e a fonte de dados tiverem sido criados, você estará pronto para criar o indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Esse indexador é executado a cada duas horas (o intervalo de agendamento é definido como "PT2H"). Para executar um indexador a cada 30 minutos, defina o intervalo como "PT30M". O intervalo mais curto com suporte é de 5 minutos. A agenda é opcional-se omitida, um indexador será executado apenas uma vez quando for criado. No entanto, você pode executar um indexador sob demanda a qualquer momento.   

Para obter mais detalhes sobre a API criar indexador, confira [criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Para obter mais informações sobre como definir agendas do indexador, consulte [como agendar indexadores para Azure Search](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Utilizar .NET

O SDK do .NET disponível para o público geral tem paridade total com a API REST disponível. Recomendamos que você examine a seção anterior da API REST para saber os conceitos, o fluxo de trabalho e os requisitos. Em seguida, você pode consultar a seguinte documentação de referência da API .NET para implementar um indexador JSON em código gerenciado.

+ [Microsoft. Azure. Search. Models. DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [Microsoft. Azure. Search. Models. DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [Microsoft. Azure. Search. Models. index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [Microsoft. Azure. Search. Models. indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Indexando documentos alterados

A finalidade de uma política de detecção de alteração de dados é identificar com eficiência os itens de dados alterados. Atualmente, a única política com suporte é a política de `High Water Mark` usando a propriedade `_ts` (carimbo de data/hora) fornecida pelo Azure Cosmos DB, que é especificado da seguinte maneira:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Usar essa política é altamente recomendável para garantir um bom desempenho do indexador. 

Se você estiver usando uma consulta personalizada, certifique-se de que a propriedade `_ts` seja projetada pela consulta.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Progresso incremental e consultas personalizadas

O progresso incremental durante a indexação garante que, se a execução do indexador for interrompida por falhas transitórias ou por um limite de tempo de execução, o indexador poderá escolher onde parou na próxima vez em que for executado, em vez de ter que reindexar toda a coleção do zero. Isso é especialmente importante ao indexar grandes coleções. 

Para habilitar o progresso incremental ao usar uma consulta personalizada, certifique-se de que sua consulta ordena os resultados pela coluna `_ts`. Isso habilita o ponto de verificação periódico que o Azure Search usa para fornecer o progresso incremental na presença de falhas.   

Em alguns casos, mesmo que a consulta contenha uma cláusula `ORDER BY [collection alias]._ts`, Azure Search pode não inferir que a consulta é ordenada pela `_ts`. Você pode informar Azure Search que os resultados são ordenados usando a propriedade de configuração `assumeOrderByHighWaterMarkColumn`. Para especificar essa dica, crie ou atualize o indexador da seguinte maneira: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indexando documentos excluídos

Quando as linhas são excluídas da coleção, normalmente você também deseja excluir essas linhas do índice de pesquisa. A finalidade de uma política de detecção de exclusão de dados é identificar com eficiência os itens de dados excluídos. Atualmente, a única política com suporte é a política de `Soft Delete` (a exclusão é marcada com um sinalizador de algum tipo), que é especificado da seguinte maneira:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Se você estiver usando uma consulta personalizada, certifique-se de que a propriedade referenciada por `softDeleteColumnName` seja projetada pela consulta.

O exemplo a seguir cria uma fonte de dados com uma política de exclusão reversível:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="NextSteps"></a>Passos seguintes

Parabéns! Você aprendeu a integrar o Azure Cosmos DB com Azure Search usando um indexador.

* Para saber mais sobre Azure Cosmos DB, consulte a [página de serviço do Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Para saber mais sobre Azure Search, consulte a [página de serviço Search](https://azure.microsoft.com/services/search/).
