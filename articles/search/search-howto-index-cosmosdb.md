---
title: Índice de uma origem de dados do Azure Cosmos DB - Azure Search
description: Pesquise a uma origem de dados do Azure Cosmos DB e ingestão de dados num índice de texto completo pesquisável no Azure Search. Indexadores automatizam ingestão de dados para origens de dados selecionada, como o Azure Cosmos DB.
ms.date: 02/28/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 9021bb2a030472d4e83d78c8fc6363db570c3554
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57318007"
---
# <a name="how-to-index-cosmos-db-using-an-azure-search-indexer"></a>Como o índice do Cosmos DB com um indexador de Azure Search

Este artigo mostra-lhe como configurar o Azure Cosmos DB [indexador](search-indexer-overview.md) de extrair conteúdo e torná-lo pesquisáveis no Azure Search. Este fluxo de trabalho cria um índice da Azure Search e carrega-o com o texto existente extraído do Azure Cosmos DB. 

Como a terminologia pode ser confusa, vale a pena observar que [indexação do Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview) e [indexação de pesquisa do Azure](search-what-is-an-index.md) são operações distintas, exclusivas para cada serviço. Antes de começar a Azure Search indexação, a base de dados do Azure Cosmos DB tem já existem e contêm dados.

Pode utilizar o [portal](#cosmos-indexer-portal), [REST APIs](#cosmos-indexer-rest), ou [SDK de .NET](#cosmos-indexer-dotnet) para indexar o conteúdo do Cosmos. O indexador de Cosmos DB no Azure Search consegue pesquisar [itens de Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) acessados por meio desses protocolos:

* [API DE SQL](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference) 
* [API do MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction) (suporte do Azure Search para esta API está em pré-visualização pública)  

> [!Note]
> Voz do utilizador tem itens existentes para o suporte de API adicional. Pode converter um voto para as APIs de Cosmos gostaria de ver suportados no Azure Search: [API de tabela](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab), [Graph API](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4), [Apache Cassandra API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu).
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Utilizar o portal

O método mais fácil para indexação de itens do Cosmos do Azure é usar um assistente no [portal do Azure](https://portal.azure.com/). Dados de amostragem e lendo metadados no contentor, o [ **importar dados** ](search-import-data-portal.md) pode criar um índice predefinido, mapear campos de origem para campos de índice de destino e carregar o índice num único assistente no Azure Search operação. Dependendo do tamanho e complexidade da origem de dados, poderia ter um índice de pesquisa de texto completo operacional em minutos.

Recomendamos que utilize a mesma subscrição do Azure para o Azure Search e o Azure Cosmos DB, de preferência na mesma região.

### <a name="1---prepare-source-data"></a>1 - preparar os dados de origem

Deve ter uma conta do Cosmos, uma base de dados do Cosmos do Azure mapeada para a API de SQL ou a API do MongoDB e um contentor de documentos JSON. 

Certifique-se que a base de dados do Cosmos DB contém dados. O [Assistente de importação de dados](search-import-data-portal.md) lê metadados e realiza a amostragem de dados para inferir um esquema de índice, mas também cargas de dados do Cosmos DB. Se os dados estão em falta, o assistente será interrompido com este erro "esquema índice detectando de erro da origem de dados: Não foi possível criar um protótipo de índice porque a origem de dados "emptycollection" não devolveu dados".

### <a name="2---start-import-data-wizard"></a>2 - iniciar o Assistente de importação de dados

Pode [iniciar o assistente](search-import-data-portal.md) na barra de comandos na página do serviço de pesquisa do Azure ou ao clicar **adicionar Azure Search** no **definições** esquerda secção da sua conta de armazenamento Painel de navegação.

   ![Importar o comando de dados no portal](./media/search-import-data-portal/import-data-cmd2.png "iniciar o Assistente de importação de dados")

### <a name="3---set-the-data-source"></a>3 - definir a origem de dados

> [!NOTE] 
> Atualmente, não é possível criar ou editar **MongoDB** origens de dados com o portal do Azure ou o SDK do .NET. No entanto, **pode** monitorizar o histórico de execução do MongoDB indexadores no portal.

Na **origem de dados** página, a origem tem de ser **Cosmos DB**, com as seguintes especificações:

+ **Nome** é o nome do objeto de origem de dados. Depois de criado, pode escolhê-lo para outras cargas de trabalho.

+ **Conta do cosmos DB** deve ser a cadeia de ligação primária ou secundária do Cosmos DB, com um `AccountEdpointPoint` e uma `AccountKey`. A conta determina se os dados é converter como API de SQL ou a API do Mongo DB

+ **Base de dados** é uma base de dados existente da conta. 

+ **Coleção** é um contentor de documentos. Documentos têm de existir na ordem para importação com êxito. 

+ **Consulta** pode estar em branco se pretender que todos os documentos, caso contrário, pode inserir uma consulta que seleciona um subconjunto de documentos. 

   ![Definição de origem de dados do cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "definição da origem de dados do Cosmos DB")

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - ignorar a página de "Adicionar a pesquisa cognitiva" no Assistente

Adicionar capacidades cognitivas não é necessário para importação de documento. A menos que tenha uma necessidade específica de [incluem APIs serviços cognitivos e transformações](cognitive-search-concept-intro.md) para o seu pipeline de indexação, deve ignorar este passo.

Para ignorar a etapa, primeiro, ir até a página seguinte.

   ![Botão seguinte da página de pesquisa cognitiva](media/search-get-started-portal/next-button-add-cog-search.png)

Dessa página pode avançar diretamente para personalização do índice.

   ![Ignorar o passo de capacidades cognitivas](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 - conjunto de atributos de índice

Na **índice** página, deve ver uma lista de campos com um tipo de dados e uma série de caixas de seleção para a definição de atributos de índice. O assistente pode gerar uma lista de campos com base nos metadados e a origem de dados de amostragem. 

Pode em massa-selecionar atributos ao clicar na caixa de verificação na parte superior de uma coluna de atributo. Escolher **recuperável** e **pesquisável** para todos os campos que devem ser devolvidos para uma aplicação de cliente e está sujeita a processamento de pesquisa de texto completo. Observará que os números inteiros não são texto completo ou difusa pesquisáveis (números são avaliados literalmente e, muitas vezes, são úteis em filtros).

Reveja a descrição da [atributos de índice](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) e [analisadores de idiomas](https://docs.microsoft.com/rest/api/searchservice/language-support) para obter mais informações. 

Dedique uns momentos para rever as suas seleções. Depois de executar o assistente, estruturas de dados físico são criadas e não será possível editar estes campos sem remover e recriar todos os objetos.

   ![Definição de índice do cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "definição de índice do Cosmos DB")

### <a name="6---create-indexer"></a>6 - criar indexador

Total especificado, o assistente cria 3 objetos distintos no seu serviço de pesquisa. Um objeto de origem de dados e o objeto de índice são guardadas como recursos nomeados do seu serviço Azure Search. A última etapa cria um objeto de indexador. O indexador de nomenclatura permite que ele existe como um recurso de autónomo, o que pode agendar e gerir de forma independente o objeto de origem dados e índice, criado na mesma sequência em Assistente.

Se não estiver familiarizado com indexadores, um *indexador* é um recurso no Azure Search, que pesquisa uma origem de dados externos para conteúdo pesquisável. A saída do **importar dados** assistente é um indexador que rastreia a sua origem de dados do Cosmos DB, extrai o conteúdo pesquisável e importa-lo para um índice na Azure Search.

Captura de ecrã seguinte mostra a configuração predefinida do indexador. Pode mudar para o **uma vez** se quiser executar o indexador uma vez. Clique em **submeter** para executar o assistente e criar todos os objetos. Indexação começa imediatamente.

   ![Definição de indexador do cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "definição de indexador do Cosmos DB")

Pode monitorizar a importação de dados nas páginas de portais. Notificações de progresso indicam o estado de indexação e o número de documentos são carregados. 

Quando a indexação é concluída, pode utilizar [Explorador de pesquisa](search-explorer.md) para consultar o índice.

> [!NOTE]
> Se não vir os dados esperados, poderá ter de definir mais atributos em mais campos. Elimine o índice e indexador que acabou de criar e siga os passos do assistente mais uma vez, modificar as suas seleções para atributos de índice no passo 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Utilizar APIs REST

Pode utilizar a API de REST para dados do índice do Azure Cosmos DB, segue um fluxo de trabalho de três partes comuns a todos os indexadores na pesquisa do Azure: criar uma origem de dados, criar um índice, criar um indexador. Extração de dados do armazenamento de Cosmos ocorre ao submeter o pedido de criar indexador. Após a conclusão deste pedido, terá um índice queryable. 

Se estiver a avaliar o MongoDB, tem de utilizar a API REST para criar a origem de dados.

Na sua conta do Cosmos DB pode escolher se pretende que a coleção para indexar automaticamente todos os documentos. Por predefinição, todos os documentos são indexados automaticamente, mas pode desativar a indexação automática. Quando a indexação é desativada, documentos podem ser acedidos através de apenas seus auto-ligações ou por consultas utilizando o documento ID. O Azure Search necessita do Cosmos DB automática de indexação ativada na coleção que será indexada pelo Azure Search. 

> [!NOTE]
> O Azure Cosmos DB é a próxima geração do DocumentDB. Embora o nome do produto for alterado, o `documentdb` sintaxe em indexadores do Azure Search ainda existe para efeitos compatibilidade de APIs de pesquisa do Azure e páginas de portal. Ao configurar os indexadores, certifique-se de que especifique o `documentdb` sintaxe de acordo com as instruções neste artigo.


### <a name="1---assemble-inputs-for-the-request"></a>1 - montar entradas para o pedido

Para cada pedido, tem de fornecer o nome do serviço e a chave de administrador para o Azure Search (no cabeçalho da mensagem) e o nome da conta de armazenamento e a chave para o armazenamento de Blobs. Pode usar [Postman](search-fiddler.md) para enviar solicitações HTTP para o Azure Search.

Copie os seguintes quatro valores no bloco de notas, para que pode colá-los numa solicitação:

+ Nome do serviço de pesquisa do Azure
+ Chave de administração de pesquisa do Azure
+ Cadeia de ligação do cosmos DB

Pode encontrar estes valores no portal do:

1. As páginas do portal do Azure Search, copie o URL do serviço de pesquisa da página de descrição geral.

2. No painel de navegação esquerdo, clique em **chaves** e, em seguida, copie qualquer um da chave primária ou secundária (ambos são equivalentes).

3. Mude para as páginas de portal para a sua conta de armazenamento do Cosmos. No painel de navegação esquerdo, sob **configurações**, clique em **chaves**. Esta página fornece um URI, dois conjuntos de cadeias de ligação, e dois conjuntos de chaves. Copie uma das cadeias de ligação para o bloco de notas.

### <a name="2---create-a-data-source"></a>2 - criar uma origem de dados

R **origem de dados** Especifica os dados para o índice, credenciais e políticas para identificar alterações nos dados (como documentos modificados ou eliminados dentro da coleção). A origem de dados está definida como um recurso independente, para que possa ser utilizado por vários indexadores.

Para criar uma origem de dados, formule um pedido POST:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

O corpo do pedido contém a definição de origem de dados, que deve incluir os seguintes campos:

| Campo   | Descrição |
|---------|-------------|
| **name** | Necessário. Escolha o nome para representar o objeto de origem de dados. |
|**tipo**| Necessário. Tem de ser `documentdb`. |
|**credentials** | Necessário. Tem de ser uma cadeia de ligação do Cosmos DB.<br/>Para coleções de SQL, cadeias de ligação estão neste formato: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/>Para coleções de MongoDB, adicione **ApiKind = MongoDb** para a cadeia de ligação:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/>Evite os números de porta no url do ponto final. Se incluir o número de porta, o Azure Search será não é possível indexar a base de dados do Azure Cosmos DB.|
| **container** | Contém os seguintes elementos: <br/>**name**: Necessário. Especifica o ID da coleção da base de dados ser indexados.<br/>**query**: Opcional. Pode especificar uma consulta para nivelamento um documento JSON arbitrário num esquema simples que o Azure Search pode indexar.<br/>Para coleções de MongoDB, as consultas não são suportadas. |
| **dataChangeDetectionPolicy** | Recomendado. Ver [indexar documentos alterados](#DataChangeDetectionPolicy) secção.|
|**dataDeletionDetectionPolicy** | Opcional. Ver [indexar documentos eliminado](#DataDeletionDetectionPolicy) secção.|

### <a name="using-queries-to-shape-indexed-data"></a>Através de consultas a forma indexou dados
Pode especificar uma consulta SQL para nivelamento propriedades aninhadas ou matrizes, as propriedades do projeto JSON e filtrar os dados a ser indexados. 

> [!WARNING]
> Consultas personalizadas não são suportadas para **MongoDB** coleções: `container.query` parâmetro tem de ser definido como nulo ou omitido. Se precisar de utilizar uma consulta personalizada, faça contato no [User Voice](https://feedback.azure.com/forums/263029-azure-search).

Documento de exemplo:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "documentdb", "search"]
    }

Consulta de filtro:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Mesclar consulta:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Consulta de projeção:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Consulta de mesclagem de matriz:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3 - criar um índice de pesquisa de destino 

[Criar um índice da Azure Search destino](/rest/api/searchservice/create-index) se ainda não tiver um. O exemplo seguinte cria um índice com um campo de ID e a descrição:

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
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

Certifique-se de que o esquema do seu índice de destino é compatível com o esquema dos documentos JSON de origem ou a saída da sua projecção de consulta personalizada.

> [!NOTE]
> Para coleções particionadas, a chave do documento padrão é o Azure Cosmos DB `_rid` propriedade, que o Azure Search muda automaticamente o nome para `rid` porque os nomes de campo não podem começar com um caráter de undescore. Além disso, o Azure Cosmos DB `_rid` valores contêm carateres que são inválidas em chaves do Azure Search. Por esse motivo, o `_rid` valores são codificados em Base64.
> 
> Para coleções de MongoDB, Azure Search automaticamente muda o nome da `_id` propriedade `doc_id`.  

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mapeamento entre tipos de dados JSON e tipos de dados de pesquisa do Azure
| Tipo de dados JSON | Tipos de campo de índice de destino compatível |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Números que são semelhantes a números inteiros |Edm.Int32, Edm.Int64, Edm.String |
| Números entre aquela aparência como os pontos de vírgula flutuante |Edm.Double, Edm.String |
| String |Edm.String |
| Matrizes de tipos primitivos, por exemplo ["a", "b", "c"] |Coleção (Edm.String) |
| Cadeias de caracteres que se pareçam com datas |Edm.DateTimeOffset, Edm.String |
| Objetos GeoJSON, por exemplo {"type": "Point", "coordenadas": [longo, lat]} |Edm.GeographyPoint |
| Outros objetos JSON |N/A |

### <a name="4---configure-and-run-the-indexer"></a>4 - configurar e executar o indexador

Assim que tiver sido criado a origem de dados e índice, está pronto para criar o indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mydocdbindexer",
      "dataSourceName" : "mydocdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Este indexador é executado a cada duas horas (intervalo de agendamento está definido para "PT2H"). Para executar um indexador a cada 30 minutos, defina o intervalo para "PT30M". O mais curto intervalo suportado é de 5 minutos. A agenda é opcional - se for omitido, um indexador é executado apenas uma vez, quando é criado. No entanto, pode executar uma indexador sob demanda em qualquer altura.   

Para obter mais detalhes sobre a API de indexador criar, confira [criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="use-net"></a>Utilizar .NET

O SDK de .NET totalmente tem paridade com a API REST. Recomendamos que reveja a secção de REST API anterior para aprender conceitos, fluxo de trabalho e os requisitos. Em seguida, pode consultar a seguinte documentação de referência de .NET API para implementar um indexador JSON em código gerenciado.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Indexar documentos alterados

O objetivo de uma política de deteção de alteração de dados é eficiente identificar itens de dados alterados. Atualmente, a política de suporte única é a `High Water Mark` política utilizando o `_ts` a propriedade (timestamp) fornecida pelo Azure Cosmos DB, que é especificada da seguinte forma:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Com esta política é altamente recomendado para garantir um desempenho bom indexador. 

Se estiver a utilizar uma consulta personalizada, certifique-se de que o `_ts` propriedade está projetada pela consulta.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Progresso incremental e consultas personalizadas

Progresso incremental durante a indexação garante que se a execução do indexador é interrompida por falhas transitórias ou limite de tempo de execução, o indexador possam começar onde parou próxima vez que é executado, em vez de precisar reindexar toda a coleção do zero. Isto é especialmente importante quando grandes coleções de indexação. 

Para ativar o progresso incremental ao utilizar uma consulta personalizada, certifique-se de que a sua consulta ordena os resultados pelo `_ts` coluna. Isto permite que periódica verificação apontar que utiliza o Azure Search para fornecer o progresso incremental na presença de falhas.   

Em alguns casos, mesmo se sua consulta contém um `ORDER BY [collection alias]._ts` cláusula, o Azure Search pode não inferir que a consulta está ordenada pelo `_ts`. Pode dizer ao Azure Search que os resultados são ordenados, utilizando o `assumeOrderByHighWaterMarkColumn` propriedade de configuração. Para especificar esta sugestão, criar ou atualizar o indexador da seguinte forma: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indexar os documentos excluídos

Quando as linhas são eliminadas da coleção, normalmente pretende eliminar as linhas do também o índice de pesquisa. O objetivo de uma política de deteção de eliminação de dados é eficiente identificar itens de dados eliminados. Atualmente, a política de suporte única é a `Soft Delete` política (eliminação está marcada com um sinalizador de algum tipo), que é especificado da seguinte forma:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Se estiver a utilizar uma consulta personalizada, certifique-se de que a propriedade referenciados por `softDeleteColumnName` está projetada pela consulta.

O exemplo seguinte cria uma origem de dados com uma política de eliminação de forma recuperável:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId" },
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

## <a name="watch-this-video"></a>Assistir a este vídeo

Neste vídeo de 7 minutos ligeiramente mais antigos, Andrew Liu gerente de programa do DB Cosmos Azure demonstra como adicionar um índice da Azure Search para um contentor do Azure Cosmos DB. As páginas de portal mostradas no vídeo estão Desatualizadas, mas as informações ainda são relevantes.

>[!VIDEO https://www.youtube.com/embed/OyoYu1Wzk4w]

## <a name="NextSteps"></a>Passos seguintes

Parabéns! Aprendeu como integrar o Azure Cosmos DB com o Azure Search com um indexador.

* Para saber mais sobre o Azure Cosmos DB, veja a [página do serviço do Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Para saber mais sobre o Azure Search, consulte a [página do serviço de pesquisa](https://azure.microsoft.com/services/search/).
