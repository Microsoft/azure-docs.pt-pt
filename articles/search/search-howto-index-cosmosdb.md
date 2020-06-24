---
title: Pesquisa sobre os dados do DB da Azure Cosmos
titleSuffix: Azure Cognitive Search
description: Importar dados da Azure Cosmos DB para um índice pesmável na Pesquisa Cognitiva de Azure. Os indexantes automatizam a ingestão de dados para fontes de dados selecionadas como a Azure Cosmos DB.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: d1723b6c5d56554fbff576f6a07e37455845bda4
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84688879"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Como indexar dados do Cosmos DB com um indexador na Pesquisa Cognitiva do Azure 

> [!IMPORTANT] 
> A API SQL está geralmente disponível.
> A API mongoDB, a Gremlin API e o apoio da API da Cassandra estão atualmente em pré-visualização pública. A funcionalidade de pré-visualização é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Pode solicitar o acesso às pré-visualizações preenchendo [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview). A [versão REST API 2019-05-06-Preview](search-api-preview.md) fornece funcionalidades de pré-visualização. Existe atualmente um suporte ao portal limitado e nenhum suporte .NET SDK.

> [!WARNING]
> Apenas as coleções de Cosmis DB com uma [política de indexação](https://docs.microsoft.com/azure/cosmos-db/index-policy) definida para [Consistente](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) são apoiadas pela Azure Cognitive Search. A indexação das coleções com uma política de indexação preguiçosa não é recomendada e pode resultar em falta de dados. As cobranças com indexação de deficientes não são suportadas.

Este artigo mostra-lhe como configurar um [indexador](search-indexer-overview.md) Azure Cosmos DB para extrair conteúdo e torná-lo pesmável em Azure Cognitive Search. Este fluxo de trabalho cria um índice de Pesquisa Cognitiva Azure e carrega-o com texto existente extraído da Azure Cosmos DB. 

Como a terminologia pode ser confusa, vale a pena notar que [a indexação DB de Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/index-overview) e [a indexação de Pesquisa Cognitiva Azure](search-what-is-an-index.md) são operações distintas, únicas para cada serviço. Antes de iniciar a indexação da Pesquisa Cognitiva Azure, a sua base de dados DB Azure Cosmos já deve existir e conter dados.

O indexante Cosmos DB em Azure Cognitive Search pode rastrear [itens DB Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) acedidos através de diferentes protocolos. 

+ Para [a API SQL](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference), que geralmente está disponível, pode utilizar o [portal](#cosmos-indexer-portal)REST , [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)ou [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet) para criar a fonte de dados e indexante.

+ Para [a MongoDB API (pré-visualização)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction), pode utilizar o [portal](#cosmos-indexer-portal) ou a versão [REST API 2019-05-06-Preview](search-api-preview.md) para criar a fonte de dados e indexer.

+ Para [a API cassandra (pré-visualização)](https://docs.microsoft.com/azure/cosmos-db/cassandra-introduction) e [a API gremlin (pré-visualização)](https://docs.microsoft.com/azure/cosmos-db/graph-introduction), só pode utilizar a [versão REST API 2019-05-06-Preview](search-api-preview.md) para criar a fonte de dados e indexante.


> [!Note]
> Pode votar na Voz do Utilizador para a [API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) de tabela se quiser vê-la apoiada na Pesquisa Cognitiva Azure.
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Utilizar o portal

> [!Note]
> O portal suporta atualmente a API SQL e a MongoDB API (pré-visualização).

O método mais fácil para a indexação de itens DB do Azure Cosmos é utilizar um assistente no [portal Azure](https://portal.azure.com/). Ao recolher dados e ler metadados no recipiente, o assistente [**de dados de importação**](search-import-data-portal.md) em Azure Cognitive Search pode criar um índice padrão, mapear campos de origem para os campos de índice alvo, e carregar o índice numa única operação. Dependendo do tamanho e complexidade dos dados de origem, poderá ter um índice operacional de pesquisa de texto completo em minutos.

Recomendamos a utilização da mesma região ou localização tanto para a Pesquisa Cognitiva Azure como para a Azure Cosmos DB para uma latência mais baixa e para evitar cargas de largura de banda.

### <a name="1---prepare-source-data"></a>1 - Preparar dados de origem

Você deve ter uma conta Cosmos DB, uma base de dados DB Azure Cosmos mapeada para a API SQL, MongoDB API (pré-visualização), ou Gremlin API (pré-visualização) e conteúdo na base de dados.

Certifique-se de que a sua base de dados de CosS DB contém dados. O [assistente de dados de Importação](search-import-data-portal.md) lê metadados e executa a amostragem de dados para inferir um esquema de índice, mas também carrega dados da Cosmos DB. Se os dados faltarem, o assistente para com este erro "Erro de deteção de esquema de índice a partir de fonte de dados: Não foi possível construir um índice de protótipo porque a 'recolha vazia' de dados não devolveu dados".

### <a name="2---start-import-data-wizard"></a>2 - Iniciar o assistente de dados de importação

Pode [iniciar o assistente a](search-import-data-portal.md) partir da barra de comando na página de serviço de Pesquisa Cognitiva Azure, ou se estiver a ligar-se ao Cosmos DB SQL API, pode clicar em **Adicionar Azure Cognitive Search** na secção **Definições** do painel de navegação esquerdo da sua conta Cosmos DB.

   ![Comando de dados de importação no portal](./media/search-import-data-portal/import-data-cmd2.png "Inicie o assistente de dados de importação")

### <a name="3---set-the-data-source"></a>3 - Definir a fonte de dados

Na página de origem dos **dados,** a fonte deve ser **Cosmos DB,** com as seguintes especificações:

+ **O nome** é o nome do objeto de origem de dados. Uma vez criado, pode escolhê-lo para outras cargas de trabalho.

+ **A conta de Cosmos DB** deve ser a cadeia de ligação primária ou secundária da Cosmos DB, com um `AccountEndpoint` e um `AccountKey` . Para as coleções MongoDB, adicione **ApiKind=MongoDb** ao fim da cadeia de ligação e separe-a da cadeia de ligação com um ponto e vírgula. Para a API Gremlin e a API Cassandra, utilize as instruções para a [API REST](#cosmosdb-indexer-rest).

+ **A base de dados** é uma base de dados existente a partir da conta. 

+ **A recolha** é um recipiente de documentos. Os documentos devem existir para que a importação seja bem sucedida. 

+ **A consulta** pode ficar em branco se quiser todos os documentos, caso contrário pode inserir uma consulta que selecione um subconjunto de documento. **A consulta** só está disponível para a API SQL.

   ![Definição de fonte de dados do Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Definição de fonte de dados do Cosmos DB")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - Salte a página "Enriquecer conteúdo" no assistente

Adicionar competências cognitivas (ou enriquecimento) não é um requisito de importação. A menos que tenha uma necessidade específica de [adicionar enriquecimento](cognitive-search-concept-intro.md) de IA ao seu pipeline de indexação, deve saltar este passo.

Para saltar o passo, clique nos botões azuis na parte inferior da página para "Next" e "Skip".

### <a name="5---set-index-attributes"></a>5 - Definir atributos de índice

Na página **'Índice',** deverá ver uma lista de campos com um tipo de dados e uma série de caixas de verificação para definir atributos de índice. O assistente pode gerar uma lista de campos com base em metadados e através da amostragem dos dados de origem. 

Pode selecionar em massa atributos clicando na caixa de verificação na parte superior de uma coluna de atributos. Escolha **O Resgate** e o **Pescurável** para todos os campos que devem ser devolvidos a uma aplicação do cliente e sujeitos a um processamento de pesquisa de texto completo. Notará que os números inteiros não são texto completo ou pescatórios difusos (os números são avaliados verbatim e são frequentemente úteis em filtros).

Reveja a descrição dos [atributos](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) de índice e [dos analisadores de linguagem](https://docs.microsoft.com/rest/api/searchservice/language-support) para obter mais informações. 

Tire um momento para rever as suas seleções. Uma vez executado o assistente, as estruturas de dados físicos são criadas e não será capaz de editar estes campos sem deixar cair e recriar todos os objetos.

   ![Definição do índice de DB do Cosmos](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Definição do índice de DB do Cosmos")

### <a name="6---create-indexer"></a>6 - Criar indexante

Totalmente especificado, o assistente cria três objetos distintos no seu serviço de pesquisa. Um objeto de origem de dados e um objeto de índice são guardados como recursos nomeados no seu serviço de Pesquisa Cognitiva Azure. O último passo cria um objeto indexante. Nomear o indexador permite-lhe existir como um recurso autónomo, que pode agendar e gerir independentemente do índice e do objeto de origem de dados, criado na mesma sequência de assistente.

Se não estiver familiarizado com indexadores, um *indexador* é um recurso na Azure Cognitive Search que rastreia uma fonte de dados externa para conteúdo pesquisável. A saída do assistente de **dados de Importação** é um indexante que rasteja a sua fonte de dados Desnós cosmos, extrai conteúdo pes pes pespável e o importa para um índice na Azure Cognitive Search.

A imagem seguinte mostra a configuração do indexante predefinido. Pode mudar para **Uma vez** se quiser executar o indexante uma vez. Clique **em Enviar** para executar o assistente e criar todos os objetos. A indexação começa imediatamente.

   ![Definição do indexante Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Definição do indexante Cosmos DB")

Pode monitorizar a importação de dados nas páginas do portal. As notificações de progresso indicam o estado de indexação e quantos documentos são enviados. 

Quando a indexação estiver concluída, pode utilizar [o Explorador de Pesquisa](search-explorer.md) para consultar o seu índice.

> [!NOTE]
> Se não vir os dados que espera, poderá ter de definir mais atributos em mais campos. Elimine o índice e o indexante que acabou de criar e passe novamente pelo assistente, modificando as suas seleções para atributos de índice no passo 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Utilizar APIs REST

Pode utilizar a API REST para indexar os dados DB do Azure Cosmos, seguindo um fluxo de trabalho de três partes comum a todos os indexantes em Azure Cognitive Search: criar uma fonte de dados, criar um índice, criar um indexante. A extração de dados da Cosmos DB ocorre quando submete o pedido de Criar Indexer. Após o final deste pedido, terá um índice de consulta. 

> [!NOTE]
> Para a indexação de dados da Cosmos DB Gremlin API ou da Cosmos DB Cassandra API, deve primeiro solicitar o acesso às pré-visualizações fechadas preenchendo [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview). Uma vez processado o seu pedido, receberá instruções sobre como utilizar a [versão REST API 2019-05-06-Preview](search-api-preview.md) para criar a fonte de dados.

No início deste artigo é mencionado que [a indexação DB de Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/index-overview) e a [indexação da pesquisa cognitiva Azure](search-what-is-an-index.md) são operações distintas. Para a indexação de DB cosmos, por padrão todos os documentos são automaticamente indexados, exceto com a API cassandra. Se desativar a indexação automática, os documentos só podem ser acedidos através das suas ligações próprias ou através de consultas utilizando o ID do documento. A azure Cognitive Search indexing requer que a indexação automática cosmos DB seja ligada na coleção que será indexada pela Azure Cognitive Search. Ao inscrever-se na pré-visualização do indexante API da Cosmos DB Cassandra, ser-lhe-ão dadas instruções sobre como configurar a indexação do Cosmos DB.

> [!WARNING]
> Azure Cosmos DB é a próxima geração do DocumentDB. Anteriormente com a versão API **2017-11-11** poderá utilizar a `documentdb` sintaxe. Isto significava que poderia especificar o seu tipo de origem de dados como `cosmosdb` ou `documentdb` . Começando pela versão API **2019-05-06** tanto as APIs de Pesquisa Cognitiva Azure como o Portal suportam apenas a `cosmosdb` sintaxe como instruído neste artigo. Isto significa que o tipo de fonte de dados deve `cosmosdb` se quiser ligar-se a um ponto final da Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1 - Montar entradas para o pedido

Para cada pedido, deve fornecer o nome de serviço e a chave de administração para Azure Cognitive Search (no cabeçalho POST), e o nome da conta de armazenamento e chave para armazenamento de bolhas. Pode utilizar [o Carteiro](search-get-started-postman.md) para enviar pedidos HTTP para a Azure Cognitive Search.

Copie os seguintes quatro valores no Bloco de Notas para que possa colar num pedido:

+ Nome do serviço de pesquisa cognitiva Azure
+ Chave de administração de pesquisa cognitiva Azure
+ Cadeia de conexão Cosmos DB

Pode encontrar estes valores no portal:

1. Nas páginas do portal para Azure Cognitive Search, copie o URL do serviço de pesquisa a partir da página 'Vista Geral'.

2. No painel de navegação esquerdo, clique em **Teclas** e, em seguida, copie a tecla primária ou secundária (são equivalentes).

3. Mude para as páginas do portal para a sua conta de armazenamento Cosmos. No painel de navegação esquerdo, em **Definições,** clique em **Teclas**. Esta página fornece um URI, dois conjuntos de cordas de conexão e dois conjuntos de teclas. Copie uma das cadeias de ligação para o Bloco de Notas.

### <a name="2---create-a-data-source"></a>2 - Criar uma fonte de dados

Uma **fonte de dados** especifica os dados para indexar, credenciais e políticas para identificar alterações nos dados (tais como documentos modificados ou eliminados dentro da sua recolha). A fonte de dados é definida como um recurso independente para que possa ser usada por vários indexadores.

Para criar uma fonte de dados, formular um pedido DEM:

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

O corpo do pedido contém a definição de fonte de dados, que deve incluir os seguintes campos:

| Campo   | Description |
|---------|-------------|
| **nome** | Necessário. Escolha qualquer nome para representar o seu objeto de origem de dados. |
|**tipo**| Necessário. Deve `cosmosdb` ser. |
|**credenciais** | Necessário. Deve ser uma cadeia de ligação Cosmos DB.<br/>Para coleções SQL, as cadeias de ligação estão neste formato:`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/><br/>Para as coleções MongoDB, adicione **ApiKind=MongoDb** à cadeia de ligação:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/><br/>Para gráficos gremlin e tabelas Cassandra, inscreva-se para a [pré-visualização](https://aka.ms/azure-cognitive-search/indexer-preview) do indexante fechado para ter acesso à pré-visualização e informações sobre como formatar as credenciais.<br/><br/>Evite os números de porta na url do ponto final. Se incluir o número da porta, a Azure Cognitive Search não poderá indexar a sua base de dados DB Azure Cosmos.|
| **recipiente** | Contém os seguintes elementos: <br/>**nome:** Obrigatório. Especifique o ID da recolha da base de dados a ser indexado.<br/>**consulta**: Opcional. Pode especificar uma consulta para aplainar um documento JSON arbitrário num esquema plano que a Azure Cognitive Search pode indexar.<br/>Para a API mongodb, Gremlin API e Cassandra API, as consultas não são apoiadas. |
| **dataChangeDetectionPolicy** | Recomendado. Consulte a secção [de Documentos Alterados de Indexação.](#DataChangeDetectionPolicy)|
|**dataDeletionDetectionPolicy** | Opcional. Consulte a secção [de Documentos Eliminados de Indexação.](#DataDeletionDetectionPolicy)|

### <a name="using-queries-to-shape-indexed-data"></a>Usando consultas para moldar dados indexados
Pode especificar uma consulta SQL para aplainar propriedades ou matrizes aninhadas, projetar propriedades JSON e filtrar os dados a serem indexados. 

> [!WARNING]
> As consultas personalizadas não são suportadas para **a API mongodb,** **Gremlin API**e **Cassandra API**: `container.query` o parâmetro deve ser definido para nulo ou omitido. Se precisar de utilizar uma consulta personalizada, informe-nos no [User Voice](https://feedback.azure.com/forums/263029-azure-search).

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

Consulta de achatamento:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Consulta de projeção:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Consulta de achatamento de matriz:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3 - Criar um índice de pesquisa de alvo 

[Crie um índice de pesquisa cognitiva Azure](/rest/api/searchservice/create-index) se ainda não tiver um. O exemplo a seguir cria um índice com um campo de ID e descrição:

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

Certifique-se de que o esquema do seu índice-alvo é compatível com o esquema dos documentos JSON de origem ou com a saída da sua projeção de consulta personalizada.

> [!NOTE]
> Para coleções divididas, a chave de documento padrão é a propriedade de Azure Cosmos `_rid` DB, a qual a Azure Cognitive Search automaticamente renomea porque os `rid` nomes de campo não podem começar com um personagem de sublinhado. Além disso, os valores DB de Azure Cosmos `_rid` contêm caracteres inválidos nas teclas de Pesquisa Cognitiva Azure. Por esta razão, os `_rid` valores estão codificados base64.
> 
> Para coleções MongoDB, a Azure Cognitive Search renomeia automaticamente a `_id` propriedade para `id` .  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>Mapeamento entre tipos de dados JSON e tipos de dados de pesquisa cognitiva Azure
| Tipo de dados JSON | Tipos de campo de índice de alvo compatíveis |
| --- | --- |
| Booleano |Edm.Boolean, Edm.String |
| Números que se parecem com inteiros |Edm.Int32, Edm.Int64, Edm.String |
| Números que parecem pontos flutuantes |Edm.Double, Edm.String |
| String |Edm.String |
| Matrizes de tipos primitivos, por exemplo ["a", "b", "c"] |Coleção (Edm.String) |
| Cordas que parecem datas |Edm.DateTimeOffset, Edm.String |
| Objetos GeoJSON, por exemplo { "tipo": "Point", "coordenadas": [longo, lat] } |Edm.GeographyPoint |
| Outros objetos JSON |N/D |

### <a name="4---configure-and-run-the-indexer"></a>4 - Configurar e executar o indexante

Uma vez criado o índice e a fonte de dados, está pronto para criar o indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Este indexante funciona de duas em duas horas (o intervalo de horário é definido para "PT2H"). Para executar um indexante a cada 30 minutos, desajuste o intervalo para "PT30M". O intervalo suportado mais curto é de 5 minutos. O horário é opcional - se omitido, um indexante funciona apenas uma vez quando é criado. No entanto, pode executar um indexante a qualquer momento.   

Para obter mais detalhes sobre a API do Indexante Create, consulte [Create Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Para obter mais informações sobre a definição de horários de indexante, consulte [Como agendar indexadores para a Pesquisa Cognitiva do Azure](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Utilizar .NET

O SDK .NET geralmente disponível tem paridade total com a API REST geralmente disponível. Recomendamos que reveja a secção anterior da API REST para aprender conceitos, fluxo de trabalho e requisitos. Em seguida, pode consultar a documentação de referência .NET API para implementar um indexante JSON em código gerido.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Indexação de documentos alterados

A finalidade de uma política de deteção de alterações de dados é identificar de forma eficiente os itens de dados alterados. Atualmente, a única política suportada é a [`HighWaterMarkChangeDetectionPolicy`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.highwatermarkchangedetectionpolicy) utilização da `_ts` propriedade (timetamp) fornecida pela Azure Cosmos DB, que é especificada da seguinte forma:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

A utilização desta política é altamente recomendada para garantir um bom desempenho do indexante. 

Se estiver a utilizar uma consulta personalizada, certifique-se de que a `_ts` propriedade é projetada pela consulta.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Progresso incremental e consultas personalizadas

O progresso incremental durante a indexação garante que se a execução do indexante for interrompida por falhas transitórias ou por um prazo de execução, o indexante pode retomar onde ficou de fora da próxima vez que funcionar, em vez de ter de reindexar toda a coleção do zero. Isto é especialmente importante na indexação de grandes coleções. 

Para permitir o progresso incremental ao utilizar uma consulta personalizada, certifique-se de que a sua consulta encomenda os resultados pela `_ts` coluna. Isto permite o check-point periódico que a Azure Cognitive Search utiliza para proporcionar progressos incrementais na presença de falhas.   

Em alguns casos, mesmo que a sua consulta contenha uma `ORDER BY [collection alias]._ts` cláusula, a Azure Cognitive Search pode não inferir que a consulta é ordenada pelo `_ts` . Pode dizer ao Azure Cognitive Search que os resultados são encomendados utilizando a `assumeOrderByHighWaterMarkColumn` propriedade de configuração. Para especificar esta sugestão, crie ou atualize o seu indexante da seguinte forma: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indexação de documentos eliminados

Quando as linhas são eliminadas da coleção, normalmente também pretende eliminar essas linhas do índice de pesquisa. O objetivo de uma política de deteção de eliminação de dados é identificar de forma eficiente os itens de dados eliminados. Atualmente, a única política apoiada é a `Soft Delete` política (a supressão é marcada com uma bandeira de algum tipo), que é especificada da seguinte forma:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Se estiver a utilizar uma consulta personalizada, certifique-se de que a propriedade referenciada `softDeleteColumnName` é projetada pela consulta.

O exemplo a seguir cria uma fonte de dados com uma política de eliminação suave:

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

## <a name="next-steps"></a><a name="NextSteps"></a>Passos seguintes

Parabéns! Aprendeu a integrar a Azure Cosmos DB com a Azure Cognitive Search usando um indexante.

* Para saber mais sobre a Azure Cosmos DB, consulte a página de serviço da [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Para saber mais sobre a Azure Cognitive Search, consulte a [página do serviço de pesquisa](https://azure.microsoft.com/services/search/).
