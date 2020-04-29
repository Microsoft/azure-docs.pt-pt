---
title: Pesquisa sobre os dados do Azure Cosmos DB
titleSuffix: Azure Cognitive Search
description: Importe dados da Azure Cosmos DB num índice pesquisável na Pesquisa Cognitiva Azure. Os indexadores automatizam a ingestão de dados para fontes de dados selecionadas como o Azure Cosmos DB.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: d1723b6c5d56554fbff576f6a07e37455845bda4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79283007"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Como indexar dados do Cosmos DB com um indexador na Pesquisa Cognitiva do Azure 

> [!IMPORTANT] 
> A SQL API está geralmente disponível.
> MongoDB API, Gremlin API e Cassandra API estão atualmente em pré-visualização pública. A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure . Pode solicitar acesso às pré-visualizações preenchendo [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview). A [versão REST API 2019-05-06-Preview](search-api-preview.md) fornece funcionalidades de pré-visualização. Existe atualmente um suporte de portal limitado e nenhum suporte sdk .NET.

> [!WARNING]
> Apenas coleções cosmos DB com uma [política de indexação](https://docs.microsoft.com/azure/cosmos-db/index-policy) definida para [Consistente](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) são suportadas pela Pesquisa Cognitiva Azure. A indexação das coleções com uma política de indexação preguiçosa não é recomendada e pode resultar em falta de dados. As coleções com indexação desativadas não são suportadas.

Este artigo mostra-lhe como configurar um [indexador](search-indexer-overview.md) Azure Cosmos DB para extrair conteúdo e torná-lo pesquisável na Pesquisa Cognitiva Azure. Este fluxo de trabalho cria um índice de Pesquisa Cognitiva Azure e carrega-o com texto existente extraído do Azure Cosmos DB. 

Como a terminologia pode ser confusa, vale a pena notar que a indexação de [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview) e a indexação da [Pesquisa Cognitiva Azure](search-what-is-an-index.md) são operações distintas, únicas para cada serviço. Antes de iniciar a indexação da Pesquisa Cognitiva Azure, a sua base de dados Azure Cosmos DB já deve existir e conter dados.

O indexante Cosmos DB em Azure Cognitive Search pode rastejar [itens D D Do Azure Cosmos acedidos](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) através de diferentes protocolos. 

+ Para [a SQL API](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference), que está geralmente disponível, pode utilizar o [portal](#cosmos-indexer-portal), [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations), ou [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet) para criar a fonte de dados e o indexante.

+ Para [mongoDB API (pré-visualização),](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)pode utilizar o [portal](#cosmos-indexer-portal) ou a [versão REST API 2019-05-06-Preview](search-api-preview.md) para criar a fonte de dados e o indexador.

+ Para [Cassandra API (pré-visualização)](https://docs.microsoft.com/azure/cosmos-db/cassandra-introduction) e [API Gremlin (pré-visualização),](https://docs.microsoft.com/azure/cosmos-db/graph-introduction)só pode utilizar a [versão REST API 2019-05-06-Preview](search-api-preview.md) para criar a fonte de dados e o indexante.


> [!Note]
> Pode votar na User Voice for the [Table API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) se quiser vê-la apoiada na Pesquisa Cognitiva Azure.
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Utilizar o portal

> [!Note]
> O portal suporta atualmente a API SQL e API MongoDB (pré-visualização).

O método mais fácil para indexar itens DB Azure Cosmos é usar um assistente no [portal Azure.](https://portal.azure.com/) Ao recolher dados e ler metadados no recipiente, o assistente de [**dados de importação**](search-import-data-portal.md) em Pesquisa Cognitiva Azure pode criar um índice padrão, mapear campos de origem para campos de índice seletivas e carregar o índice numa única operação. Dependendo do tamanho e complexidade dos dados de origem, poderá ter um índice operacional de pesquisa de texto completo em minutos.

Recomendamos a utilização da mesma região ou localização tanto para a Pesquisa Cognitiva Azure como para o Azure Cosmos DB para uma latência mais baixa e para evitar cargas de largura de banda.

### <a name="1---prepare-source-data"></a>1 - Preparar dados de origem

Deve ter uma conta Cosmos DB, uma base de dados Azure Cosmos DB mapeada para a API SQL, MongoDB API (pré-visualização) ou API Gremlin (pré-visualização) e conteúdo na base de dados.

Certifique-se de que a sua base de dados Cosmos DB contém dados. O assistente de [dados da Importação](search-import-data-portal.md) lê metadados e executa a amostragem de dados para inferir um esquema de índice, mas também carrega dados da Cosmos DB. Se os dados estiverem em falta, o assistente para com este erro "Error detetando esquema de índice a partir de fonte de dados: Não conseguiu construir um índice de protótipo porque a 'recolha vazia' de fonte de dados não devolveu dados".

### <a name="2---start-import-data-wizard"></a>2 - Iniciar o assistente de dados de importação

Pode [iniciar o assistente a](search-import-data-portal.md) partir da barra de comando na página de serviço de Pesquisa Cognitiva Azure, ou se estiver a ligar-se à Cosmos DB SQL API pode clicar em **Adicionar Pesquisa Cognitiva Azure** na secção **Definições** da sua conta Cosmos DB' left navigation pane.

   ![Comando de dados de importação no portal](./media/search-import-data-portal/import-data-cmd2.png "Inicie o assistente de dados de importação")

### <a name="3---set-the-data-source"></a>3 - Definir a fonte de dados

Na página fonte de **dados,** a fonte deve ser **Cosmos DB,** com as seguintes especificações:

+ **O nome** é o nome do objeto de origem de dados. Uma vez criado, pode escolhê-lo para outras cargas de trabalho.

+ **A conta Cosmos DB** deve ser a cadeia de ligação `AccountEndpoint` primária `AccountKey`ou secundária da Cosmos DB, com um e um . Para as coleções MongoDB, adicione **ApiKind=MongoDb** na extremidade da corda de ligação e separe-a da cadeia de ligação com um ponto e vírgula. Para a API Gremlin e Cassandra API, utilize as instruções para a [API REST](#cosmosdb-indexer-rest).

+ **A base** de dados é uma base de dados existente da conta. 

+ **A recolha** é um recipiente de documentos. Os documentos devem existir para que a importação tenha êxito. 

+ **A consulta** pode ficar em branco se quiser todos os documentos, caso contrário pode inserir uma consulta que selecione um subconjunto de documentos. **A consulta** só está disponível para a API SQL.

   ![Definição de fonte de dados cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Definição de fonte de dados cosmos DB")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - Pule a página "Enriquecer conteúdo" no assistente

A adição de competências cognitivas (ou enriquecimento) não é um requisito de importação. A menos que tenha uma necessidade específica de adicionar o enriquecimento de [IA](cognitive-search-concept-intro.md) ao seu pipeline de indexação, deve saltar este passo.

Para saltar o passo, clique nos botões azuis na parte inferior da página para "Seguinte" e "Saltar".

### <a name="5---set-index-attributes"></a>5 - Definir atributos de índice

Na página **Index,** deve ver uma lista de campos com um tipo de dados e uma série de caixas de verificação para definir atributos de índice. O assistente pode gerar uma lista de campos com base em metadados e através da amostragem dos dados de origem. 

Pode selecionar atributos em massa clicando na caixa de verificação no topo de uma coluna de atributos. Escolha **Recuperável** e **Pesquisável** para cada campo que deve ser devolvido a uma aplicação de cliente e sujeito a processamento completo de pesquisa de texto. Notará que os inteiros não são texto completo ou pesquisável (os números são avaliados verbatim e são muitas vezes úteis em filtros).

Reveja a descrição dos [atributos indexados](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) e [dos analisadores](https://docs.microsoft.com/rest/api/searchservice/language-support) de linguagem para obter mais informações. 

Tire um momento para rever as suas seleções. Uma vez executado o assistente, as estruturas de dados físicos são criadas e você não será capaz de editar estes campos sem deixar cair e recriar todos os objetos.

   ![Definição de índice Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Definição de índice Cosmos DB")

### <a name="6---create-indexer"></a>6 - Criar indexador

Totalmente especificado, o assistente cria três objetos distintos no seu serviço de pesquisa. Um objeto de origem de dados e objeto de índice são guardados como recursos nomeados no seu serviço de Pesquisa Cognitiva Azure. O último passo cria um objeto indexante. Nomear o indexante permite-lhe existir como um recurso autónomo, que pode agendar e gerir independentemente do objeto de origem de índice e dados, criado na mesma sequência de assistentes.

Se não está familiarizado com os indexadores, um *indexante* é um recurso na Pesquisa Cognitiva Azure que rasteja uma fonte externa de dados para conteúdo pesquisável. A saída do assistente de **dados de Importação** é um indexante que rasteja a sua fonte de dados Cosmos DB, extrai conteúdo pesquisável e importa-o para um índice de Pesquisa Cognitiva Azure.

A imagem que se segue mostra a configuração do indexante predefinido. Pode mudar para **Once** se quiser executar o indexador uma vez. Clique em **Submeter** para executar o assistente e criar todos os objetos. A indexação começa imediatamente.

   ![Definição de indexador Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Definição de indexador Cosmos DB")

Pode monitorizar a importação de dados nas páginas do portal. As notificações de progresso indicam o estado da indexação e quantos documentos são enviados. 

Quando a indexação estiver completa, pode utilizar o explorador de [pesquisa](search-explorer.md) para consultar o seu índice.

> [!NOTE]
> Se não vir os dados que espera, poderá ter de definir mais atributos em mais campos. Elimine o índice e o indexante que acabou de criar e volte a passar pelo assistente, modificando as suas seleções para atributos de índice no passo 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Utilizar APIs REST

Pode utilizar a API REST para indexar os dados do Azure Cosmos DB, seguindo um fluxo de trabalho em três partes comum a todos os indexadores em Pesquisa Cognitiva Azure: criar uma fonte de dados, criar um índice, criar um indexante. A extração de dados da Cosmos DB ocorre quando submete o pedido de Criar Indexer. Depois deste pedido estar concluído, terá um índice de consulta. 

> [!NOTE]
> Para indexar dados da Cosmos DB Gremlin API ou Cosmos DB Cassandra API, primeiro deve solicitar o acesso às pré-visualizações fechadas preenchendo [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview). Uma vez processado o seu pedido, receberá instruções para como utilizar a [versão REST API 2019-05-06-Preview](search-api-preview.md) para criar a fonte de dados.

No início deste artigo é mencionado que a indexação de indexação de [Db da Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/index-overview) e a [indexação da pesquisa cognitiva azure](search-what-is-an-index.md) são operações distintas. Para a indexação de Cosmos DB, por padrão, todos os documentos são automaticamente indexados, exceto com a API Cassandra. Se desligar a indexação automática, os documentos só podem ser acedidos através dos seus auto-links ou através de consultas utilizando o ID do documento. O índice de pesquisa cognitiva Azure requer que o indexação automática Cosmos DB seja ligado na coleção que será indexada pela Pesquisa Cognitiva Azure. Ao inscrever-se na pré-visualização do indexer Cosmos DB Cassandra API, receberão instruções sobre como configurar a indexação do Cosmos DB.

> [!WARNING]
> Azure Cosmos DB é a próxima geração da DocumentDB. Anteriormente, com a versão API **2017-11-11,** poderia utilizar a `documentdb` sintaxe. Isto significa que pode especificar o `cosmosdb` `documentdb`seu tipo de fonte de dados como ou . Começando com a versão API **2019-05-06** tanto as APIs `cosmosdb` de Pesquisa Cognitiva Azure como o Portal apenas suportam a sintaxe, conforme instruído neste artigo. Isto significa que o `cosmosdb` tipo de fonte de dados deve, se quiser ligar-se a um ponto final do Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1 - Reunir as inputs para o pedido

Para cada pedido, deve fornecer o nome de serviço e a chave de administração para a Pesquisa Cognitiva Azure (no cabeçalho POST), bem como o nome da conta de armazenamento e a chave para armazenamento de bolhas. Pode utilizar [o Carteiro](search-get-started-postman.md) para enviar pedidos http para a Pesquisa Cognitiva Azure.

Copie os seguintes quatro valores no Bloco de Notas para que possa colá-los num pedido:

+ Nome do serviço de pesquisa cognitiva Azure
+ Chave de administração de pesquisa cognitiva azure
+ Cadeia de conexão Cosmos DB

Pode encontrar estes valores no portal:

1. Nas páginas do portal para Pesquisa Cognitiva Azure, copie o URL do serviço de pesquisa a partir da página 'Overview'.

2. No painel de navegação à esquerda, clique em **Teclas** e, em seguida, copie a tecla primária ou secundária (são equivalentes).

3. Mude para as páginas do portal para a sua conta de armazenamento Cosmos. No painel de navegação à esquerda, em **Definições,** clique em **Teclas**. Esta página fornece um URI, dois conjuntos de cordas de ligação e dois conjuntos de chaves. Copie uma das cordas de ligação ao Bloco de Notas.

### <a name="2---create-a-data-source"></a>2 - Criar uma fonte de dados

Uma fonte de **dados** especifica os dados ao indexação, credenciais e políticas para identificar alterações nos dados (tais como documentos modificados ou eliminados dentro da sua recolha). A fonte de dados é definida como um recurso independente para que possa ser usada por vários indexantes.

Para criar uma fonte de dados, formular um pedido post:

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

| Campo   | Descrição |
|---------|-------------|
| **nome** | Necessário. Escolha qualquer nome para representar o seu objeto de origem de dados. |
|**tipo**| Necessário. Deve `cosmosdb`ser. |
|**credenciais** | Necessário. Deve ser uma corda de ligação Cosmos DB.<br/>Para coleções SQL, as cordas de ligação estão neste formato:`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/><br/>Para coleções MongoDB, adicione **ApiKind=MongoDb** à cadeia de ligação:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/><br/>Para gráficos Gremlin e tabelas Cassandra, inscreva-se na [pré-visualização do indexante gated](https://aka.ms/azure-cognitive-search/indexer-preview) para ter acesso à pré-visualização e informações sobre como formatar as credenciais.<br/><br/>Evite os números de porta na url final. Se incluir o número de porta, a Pesquisa Cognitiva Azure não poderá indexar a sua base de dados Azure Cosmos DB.|
| **recipiente** | Contém os seguintes elementos: <br/>**nome**: Obrigatório. Especifique a identificação da coleção de dados a indexar.<br/>**consulta**: Opcional. Pode especificar uma consulta para aplainar um documento JSON arbitrário num esquema plano que a Pesquisa Cognitiva Azure pode indexar.<br/>Para a API MongoDB, API Gremlin e Cassandra API, as consultas não são apoiadas. |
| **dataChangeDetectionPolicy** | Recomendado. Consulte a secção [de Documentos Alterados de Indexação.](#DataChangeDetectionPolicy)|
|**dadosDeletionDetectionPolicy** | Opcional. Consulte a secção [de Documentos Eliminados de Indexação.](#DataDeletionDetectionPolicy)|

### <a name="using-queries-to-shape-indexed-data"></a>Usando consultas para moldar dados indexados
Pode especificar uma consulta SQL para aplainar propriedades ou matrizes aninhadas, projetar propriedades JSON e filtrar os dados a indexar. 

> [!WARNING]
> As consultas personalizadas não são suportadas para a **API MongoDB,** **Gremlin API,** e **Cassandra API:** `container.query` o parâmetro deve ser definido para nula ou omitida. Se precisar de uma consulta personalizada, informe-nos no [User Voice](https://feedback.azure.com/forums/263029-azure-search).

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

[Crie um índice](/rest/api/searchservice/create-index) de pesquisa cognitiva Azure alvo se ainda não tiver um. O exemplo seguinte cria um índice com um campo de identificação e descrição:

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
> Para coleções divididas, a chave de documento padrão `_rid` é propriedade da Azure Cosmos `rid` DB, a que a Azure Cognitive Search automaticamente renomea porque os nomes de campo não podem começar com um caráter de sublinhado. Além disso, os `_rid` valores de Azure Cosmos DB contêm caracteres que são inválidos em chaves de pesquisa cognitiva Azure. Por esta razão, os `_rid` valores são codificados pela Base64.
> 
> Para coleções MongoDB, a Azure Cognitive `_id` Search `id`renomea automaticamente a propriedade para .  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>Mapeamento entre tipos de dados jSON e tipos de dados de pesquisa cognitiva azure
| Tipo de dados JSON | Tipos de campo de índice de destino compatíveis |
| --- | --- |
| Booleano |Edm.Boolean, Edm.String |
| Números que parecem inteiros |Edm.Int32, Edm.Int64, Edm.String |
| Números que parecem pontos flutuantes |Edm.Double, Edm.String |
| String |Edm.String |
| Matrizes de tipos primitivos, por exemplo [a", "b", "c"] |Coleção (Edm.String) |
| Cordas que parecem datas |Edm.DateTimeOffset, Edm.String |
| Objetos GeoJSON, por exemplo { "type": "Ponto", "coordenadas": [longo, lat] } |Edm.GeographyPoint |
| Outros objetos JSON |N/D |

### <a name="4---configure-and-run-the-indexer"></a>4 - Configurar e executar o indexador

Uma vez criado o índice e a fonte de dados, está pronto para criar o indexante:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Este indexante funciona de duas em duas horas (o intervalo de horário está definido para "PT2H"). Para executar um indexante a cada 30 minutos, detete o intervalo para "PT30M". O intervalo suportado mais curto é de 5 minutos. O horário é opcional - se omitido, um indexante funciona apenas uma vez quando é criado. No entanto, pode executar um indexer a qualquer momento.   

Para mais detalhes sobre a API Create Indexer, consulte [create indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Para obter mais informações sobre a definição de horários do indexante, consulte [como agendar indexadores para pesquisa cognitiva azure](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Utilizar .NET

O SDK geralmente disponível tem total paridade com a API REST geralmente disponível. Recomendamos que reveja a secção anterior da API REST para aprender conceitos, fluxo de trabalho e requisitos. Pode então consultar a seguinte documentação de referência da API .NET para implementar um indexador JSON em código gerido.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Indexação de documentos alterados

O objetivo de uma política de deteção de alterações de dados é identificar eficientemente itens de dados alterados. Atualmente, a única política [`HighWaterMarkChangeDetectionPolicy`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.highwatermarkchangedetectionpolicy) apoiada `_ts` é a utilização da propriedade (timestamp) fornecida pela Azure Cosmos DB, que é especificada da seguinte forma:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

A utilização desta política é altamente recomendada para garantir um bom desempenho indexador. 

Se estiver a utilizar uma consulta personalizada, certifique-se de que a `_ts` propriedade é projetada pela consulta.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Progresso incremental e consultas personalizadas

O progresso incremental durante a indexação garante que, se a execução do indexante for interrompida por falhas transitórias ou limite de tempo de execução, o indexante pode retomar onde deixou de funcionar da próxima vez que funcionar, em vez de ter de reindexar toda a coleção do zero. Isto é especialmente importante quando se indexam grandes coleções. 

Para permitir o progresso incremental ao utilizar uma consulta personalizada, `_ts` certifique-se de que a sua consulta encomenda os resultados pela coluna. Isto permite a verificação periódica que a Pesquisa Cognitiva Azure utiliza para proporcionar progressos incrementais na presença de falhas.   

Em alguns casos, mesmo `ORDER BY [collection alias]._ts` que a sua consulta contenha uma cláusula, a `_ts`Pesquisa Cognitiva Azure pode não inferir que a consulta é ordenada pelo . Pode dizer ao Azure Cognitive Search que `assumeOrderByHighWaterMarkColumn` os resultados são encomendados utilizando a propriedade de configuração. Para especificar esta dica, crie ou atualize o seu indexante da seguinte forma: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indexação de documentos eliminados

Quando as linhas são eliminadas da coleção, normalmente pretende eliminar essas linhas do índice de pesquisa também. O objetivo de uma política de deteção de dados é identificar eficientemente os itens de dados eliminados. Atualmente, a única política `Soft Delete` apoiada é a política (a eliminação é marcada com uma bandeira de algum tipo), que é especificada da seguinte forma:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Se estiver a utilizar uma consulta personalizada, certifique-se de que a propriedade referenciada `softDeleteColumnName` é projetada pela consulta.

O exemplo seguinte cria uma fonte de dados com uma política de eliminação suave:

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

Parabéns! Aprendeu a integrar o Azure Cosmos DB com a Azure Cognitive Search usando um indexador.

* Para saber mais sobre o Azure Cosmos DB, consulte a página de [serviço do Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)
* Para saber mais sobre a Pesquisa Cognitiva Azure, consulte a página de [serviço de Pesquisa](https://azure.microsoft.com/services/search/).
