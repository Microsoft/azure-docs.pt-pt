---
title: Pesquisa rumo às bolhas JSON
titleSuffix: Azure Cognitive Search
description: Crawl Azure JSON blobs para conteúdo de texto usando o indexante Azure Cognitive Search Blob. Os indexantes automatizam a ingestão de dados para fontes de dados selecionadas como o armazenamento de Azure Blob.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 1fc6c7086917f2bcd6e4991d2dac37ea24cbfa83
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116387"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Como indexar as bolhas JSON usando um indexante Blob em Azure Cognitive Search

Este artigo mostra-lhe como configurar um [indexante](search-indexer-overview.md) de blob de pesquisa cognitiva Azure para extrair conteúdo estruturado de documentos JSON no armazenamento de Azure Blob e torná-lo pesjável em Azure Cognitive Search. Este fluxo de trabalho cria um índice de Pesquisa Cognitiva Azure e carrega-o com texto existente extraído de bolhas JSON. 

Pode utilizar o [portal](#json-indexer-portal), [REST APIs](#json-indexer-rest)ou [.NET SDK](#json-indexer-dotnet) para indexar o conteúdo JSON. Comum a todas as abordagens é que os documentos JSON estão localizados num recipiente de bolhas numa conta de Armazenamento Azure. Para obter orientações sobre a pressão dos documentos JSON de outras plataformas não-Azure, consulte a importação de [dados em Azure Cognitive Search](search-what-is-data-import.md).

As bolhas JSON no armazenamento Azure Blob são tipicamente um único documento JSON (modo de análise `json` é) ou uma coleção de entidades JSON. Para coleções, a bolha pode ter uma **variedade** de elementos JSON bem formados (modo de análise é `jsonArray` ). As bolhas também podem ser compostas por múltiplas entidades JSON individuais separadas por uma nova linha (o modo de análise é `jsonLines` ). O parâmetro **parsingMode** no pedido determina as estruturas de saída.

> [!NOTE]
> Para obter mais informações sobre a indexação de vários documentos de pesquisa a partir de uma única bolha, consulte [a indexação one-to-many](search-howto-index-one-to-many-blobs.md).

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Utilizar o portal

O método mais fácil para a indexação dos documentos JSON é utilizar um assistente no [portal Azure](https://portal.azure.com/). Ao analisar metadados no recipiente blob Azure, o assistente [**de dados de importação**](search-import-data-portal.md) pode criar um índice padrão, mapear campos de origem para os campos de índice alvo e carregar o índice numa única operação. Dependendo do tamanho e complexidade dos dados de origem, poderá ter um índice operacional de pesquisa de texto completo em minutos.

Recomendamos a utilização da mesma região ou local tanto para a Pesquisa Cognitiva Azure como para o Armazenamento Azure para uma menor latência e para evitar cargas de largura de banda.

### <a name="1---prepare-source-data"></a>1 - Preparar dados de origem

[Inscreva-se no portal Azure](https://portal.azure.com/) e [crie um recipiente Blob](../storage/blobs/storage-quickstart-blobs-portal.md) para conter os seus dados. O Nível de Acesso Público pode ser definido em qualquer um dos seus valores válidos.

Necessitará do nome da conta de armazenamento, nome do recipiente e uma chave de acesso para recuperar os seus dados no assistente **de dados De importação.**

### <a name="2---start-import-data-wizard"></a>2 - Iniciar o assistente de dados de importação

Na página geral do seu serviço de pesquisa, pode [iniciar o assistente a](search-import-data-portal.md) partir da barra de comando.

   :::image type="content" source="media/search-import-data-portal/import-data-cmd2.png" alt-text="Comando de dados de importação no portal" border="false":::

### <a name="3---set-the-data-source"></a>3 - Definir a fonte de dados

Na página de origem dos **dados,** a fonte deve ser **Azure Blob Storage,** com as seguintes especificações:

+ **Os dados a extrair** devem ser *conteúdo e metadados.* A escolha desta opção permite ao assistente inferir um esquema de índice e mapear os campos para importação.
   
+ **O modo de análise** deve ser definido para as linhas *JSON,* *JSON ou* *JSON*. 

  *JSON* articula cada blob como um único documento de pesquisa, aparecendo como um item independente nos resultados da pesquisa. 

  *A matriz JSON* destina-se a bolhas que contêm dados JSON bem formados - o JSON bem formado corresponde a uma matriz de objetos, ou tem uma propriedade que é uma matriz de objetos e você quer que cada elemento seja articulado como um documento de pesquisa independente e autónomo. Se as bolhas forem complexas, e não escolher a *matriz JSON,* toda a bolha é ingerida como um único documento.

  *As linhas JSON* são para blobs compostos por várias entidades JSON separadas por uma nova linha, onde pretende que cada entidade seja articulada como um documento de pesquisa independente autónomo. Se as bolhas forem complexas e não escolheres o modo de análise das *linhas JSON,* então toda a bolha é ingerida como um único documento.
   
+ **O recipiente de armazenamento** deve especificar a sua conta de armazenamento e o seu recipiente, ou uma cadeia de ligação que se resolva para o recipiente. Pode obter cordas de ligação na página do portal de serviço Blob.

   :::image type="content" source="media/search-howto-index-json/import-wizard-json-data-source.png" alt-text="Definição de fonte de dados blob" border="false":::

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - Salte a página "Enriquecer conteúdo" no assistente

Adicionar competências cognitivas (ou enriquecimento) não é um requisito de importação. A menos que tenha uma necessidade específica de [adicionar enriquecimento](cognitive-search-concept-intro.md) de IA ao seu pipeline de indexação, deve saltar este passo.

Para saltar o passo, clique nos botões azuis na parte inferior da página para "Next" e "Skip".

### <a name="5---set-index-attributes"></a>5 - Definir atributos de índice

Na página **'Índice',** deverá ver uma lista de campos com um tipo de dados e uma série de caixas de verificação para definir atributos de índice. O assistente pode gerar uma lista de campos com base em metadados e através da amostragem dos dados de origem. 

Pode selecionar em massa atributos clicando na caixa de verificação na parte superior de uma coluna de atributos. Escolha **O Resgate** e o **Pescurável** para todos os campos que devem ser devolvidos a uma aplicação do cliente e sujeitos a um processamento de pesquisa de texto completo. Notará que os números inteiros não são texto completo ou pescatórios difusos (os números são avaliados verbatim e são frequentemente úteis em filtros).

Reveja a descrição dos [atributos](/rest/api/searchservice/create-index#bkmk_indexAttrib) de índice e [dos analisadores de linguagem](/rest/api/searchservice/language-support) para obter mais informações. 

Tire um momento para rever as suas seleções. Uma vez executado o assistente, as estruturas de dados físicos são criadas e não será capaz de editar estes campos sem deixar cair e recriar todos os objetos.

   :::image type="content" source="media/search-howto-index-json/import-wizard-json-index.png" alt-text="Definição do índice blob" border="false":::

### <a name="6---create-indexer"></a>6 - Criar indexante

Totalmente especificado, o assistente cria três objetos distintos no seu serviço de pesquisa. Um objeto de origem de dados e um objeto de índice são guardados como recursos nomeados no seu serviço de Pesquisa Cognitiva Azure. O último passo cria um objeto indexante. Nomear o indexador permite-lhe existir como um recurso autónomo, que pode agendar e gerir independentemente do índice e do objeto de origem de dados, criado na mesma sequência de assistente.

Se não estiver familiarizado com indexadores, um *indexador* é um recurso na Azure Cognitive Search que rastreia uma fonte de dados externa para conteúdo pesquisável. A saída do assistente de **dados de Importação** é um indexante que rastreia a sua fonte de dados JSON, extrai conteúdo pespável e o importa para um índice na Pesquisa Cognitiva Azure.

   :::image type="content" source="media/search-howto-index-json/import-wizard-json-indexer.png" alt-text="Definição de indexante blob" border="false":::

Clique **em OK** para executar o assistente e criar todos os objetos. A indexação começa imediatamente.

Pode monitorizar a importação de dados nas páginas do portal. As notificações de progresso indicam o estado de indexação e quantos documentos são enviados. 

Quando a indexação estiver concluída, pode utilizar [o Explorador de Pesquisa](search-explorer.md) para consultar o seu índice.

> [!NOTE]
> Se não vir os dados que espera, poderá ter de definir mais atributos em mais campos. Elimine o índice e o indexante que acabou de criar e passe novamente pelo assistente, modificando as suas seleções para atributos de índice no passo 5. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Utilizar APIs REST

Pode utilizar a API REST para indexar as bolhas JSON, seguindo um fluxo de trabalho em três partes comum a todos os indexantes em Azure Cognitive Search: criar uma fonte de dados, criar um índice, criar um indexante. A extração de dados do armazenamento de bolhas ocorre quando submete o pedido do 'Criar Indexer'. Após o final deste pedido, terá um índice de consulta. 

Pode rever o código de [exemplo REST](#rest-example) no final desta secção que mostra como criar os três objetos. Esta secção também contém detalhes sobre [os modos de análise JSON,](#parsing-modes) [bolhas únicas,](#parsing-single-blobs) [matrizes JSON](#parsing-arrays)e [matrizes aninhadas](#nested-json-arrays).

Para indexação JSON baseada em código, utilize [o Código do Carteiro](search-get-started-rest.md) ou [do Estúdio Visual](search-get-started-vs-code.md) e a API REST para criar estes objetos:

+ [índice](/rest/api/searchservice/create-index)
+ [fonte de dados](/rest/api/searchservice/create-data-source)
+ [indexante](/rest/api/searchservice/create-indexer)

A ordem de operações requer que crie e chame objetos por esta ordem. Em contraste com o fluxo de trabalho do portal, uma abordagem de código requer um índice disponível para aceitar os documentos JSON enviados através do pedido **de Criar Indexer.**

As bolhas JSON no armazenamento Azure Blob são normalmente um único documento JSON ou uma "matriz" JSON. O indexante blob em Azure Cognitive Search pode analisar qualquer construção, dependendo de como definir o parâmetro **parsingMode** no pedido.

| Documento JSON | parsingMode | Descrição | Disponibilidade |
|--------------|-------------|--------------|--------------|
| Um por bolha | `json` | Parses JSON blobs como um único pedaço de texto. Cada bolha JSON torna-se um único documento de Pesquisa Cognitiva Azure. | Geralmente disponível tanto em [REST](/rest/api/searchservice/indexer-operations) API como [em .NET](/dotnet/api/azure.search.documents.indexes.models.searchindexer) SDK. |
| Múltiplos por bolha | `jsonArray` | Analisa uma matriz JSON na bolha, onde cada elemento da matriz torna-se um documento separado de Pesquisa Cognitiva Azure.  | Geralmente disponível tanto em [REST](/rest/api/searchservice/indexer-operations) API como [em .NET](/dotnet/api/azure.search.documents.indexes.models.searchindexer) SDK. |
| Múltiplos por bolha | `jsonLines` | Analisa uma bolha que contém várias entidades JSON (uma "matriz") separadas por uma nova linha, onde cada entidade se torna um documento separado de Pesquisa Cognitiva Azure. | Geralmente disponível tanto em [REST](/rest/api/searchservice/indexer-operations) API como [em .NET](/dotnet/api/azure.search.documents.indexes.models.searchindexer) SDK. |

### <a name="1---assemble-inputs-for-the-request"></a>1 - Montar entradas para o pedido

Para cada pedido, deve fornecer o nome de serviço e a chave de administração para Azure Cognitive Search (no cabeçalho POST), e o nome da conta de armazenamento e chave para armazenamento de bolhas. Pode utilizar uma [ferramenta de teste web API](search-get-started-rest.md) para enviar pedidos HTTP para Azure Cognitive Search.

Copie os seguintes quatro valores no Bloco de Notas para que possa colar num pedido:

+ Nome do serviço de pesquisa cognitiva Azure
+ Chave de administração de pesquisa cognitiva Azure
+ Nome da conta de armazenamento do Azure
+ Chave da conta de armazenamento Azure

Pode encontrar estes valores no portal:

1. Nas páginas do portal para Azure Cognitive Search, copie o URL do serviço de pesquisa a partir da página 'Vista Geral'.

2. No painel de navegação esquerdo, clique em **Teclas** e, em seguida, copie a tecla primária ou secundária (são equivalentes).

3. Mude para as páginas do portal para a sua conta de armazenamento. No painel de navegação esquerdo, em **Definições,** clique em **Teclas de acesso**. Esta página fornece o nome da conta e a chave. Copie o nome da conta de armazenamento e uma das chaves do Bloco de Notas.

### <a name="2---create-a-data-source"></a>2 - Criar uma fonte de dados

Este passo fornece informações de ligação de fonte de dados utilizadas pelo indexante. A fonte de dados é um objeto nomeado na Pesquisa Cognitiva Azure que persiste na informação de ligação. O tipo de fonte de `azureblob` dados, determina quais os comportamentos de extração de dados invocados pelo indexante. 

Substitua valores válidos para nome de serviço, chave de administração, conta de armazenamento e espaços-chave de conta.

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   
```

### <a name="3---create-a-target-search-index"></a>3 - Criar um índice de pesquisa de alvo 

Os indexantes são emparelhados com um esquema de índice. Se estiver a utilizar a API (em vez do portal), prepare um índice com antecedência para que possa especirá-lo na operação do indexante.

O índice armazena conteúdo pesmável na Pesquisa Cognitiva Azure. Para criar um índice, forneça um esquema que especifique os campos num documento, atributos e outras construções que moldam a experiência de pesquisa. Se criar um índice que tenha os mesmos nomes de campo e tipos de dados que a fonte, o indexante corresponderá aos campos de origem e destino, poupando-lhe o trabalho de ter de mapear explicitamente os campos.

O exemplo a seguir mostra um pedido [de 'Criar Index'.](/rest/api/searchservice/create-index) O índice terá um campo pes `content` pes pesjável para armazenar o texto extraído das bolhas:   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```


### <a name="4---configure-and-run-the-indexer"></a>4 - Configurar e executar o indexante

Tal como acontece com um índice e uma fonte de dados, e indexer é também um objeto nomeado que cria e reutiliza num serviço de Pesquisa Cognitiva Azure. Um pedido totalmente especificado para criar um indexante pode parecer o seguinte:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }
```

A configuração do indexante está no corpo do pedido. Requer uma fonte de dados e um índice de alvo vazio que já existe na Pesquisa Cognitiva do Azure. 

Os horários e os parâmetros são opcionais. Se os omitir, o indexante funciona imediatamente, utilizando `json` como modo de análise.

Este indexante em particular não inclui mapeamentos de campo. Dentro da definição do indexante, pode deixar de fora **os mapeamentos** de campo se as propriedades do documento JSON de origem corresponderem aos campos do seu índice de pesquisa alvo. 


### <a name="rest-example"></a>EXEMPLO DE REPOUSO

Esta secção é uma recapitulação de todos os pedidos utilizados para a criação de objetos. Para uma discussão de componentes, consulte as secções anteriores neste artigo.

### <a name="data-source-request"></a>Pedido de fonte de dados

Todos os indexantes requerem um objeto de fonte de dados que forneça informações de ligação aos dados existentes. 

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  
```

### <a name="index-request"></a>Pedido de índice

Todos os indexantes requerem um índice-alvo que receba os dados. O corpo do pedido define o esquema de índice, constituído por campos, atribuído para suportar os comportamentos desejados num índice pesmável. Este índice deve estar vazio quando executar o indexante. 

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

### <a name="indexer-request"></a>Pedido de indexante

Este pedido mostra um indexante totalmente especificado. Inclui mapeamentos de campo, que foram omitidos em exemplos anteriores. Lembre-se que "agendar", "parâmetros" e "fieldMappings" são opcionais desde que exista um padrão disponível. Omitir "agendar" faz com que o indexante corra imediatamente. Omitir "parsingMode" faz com que o índice utilize o padrão "json".

A criação do indexante na Azure Cognitive Search despoleta a importação de dados. Funciona imediatamente, e depois com um horário, se tiver fornecido um.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }
```

<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>Utilizar o .NET SDK

O .NET SDK tem paridade total com a API REST. Recomendamos que reveja a secção anterior da API REST para aprender conceitos, fluxo de trabalho e requisitos. Em seguida, pode consultar a documentação de referência .NET API para implementar um indexante JSON em código gerido.

+ [azure.search.documents.indexes.models.searchindexerdatasourceconnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)
+ [azure.search.documents.indexes.models.searchindexerdatasourcetype](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) 
+ [azure.search.documents.indexes.models.searchindex](/dotnet/api/azure.search.documents.indexes.models.searchindex) 
+ [azure.search.documents.indexes.models.searchindexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Modos de análise

As bolhas JSON podem assumir múltiplas formas. O parâmetro **parsingMode** no indexante JSON determina como o conteúdo da bolha JSON é analisado e estruturado num índice de Pesquisa Cognitiva Azure:

| parsingMode | Descrição |
|-------------|-------------|
| `json`  | Indexe cada bolha como um único documento. Esta é a predefinição. |
| `jsonArray` | Escolha este modo se as suas bolhas forem compostas por matrizes JSON, e precisa de cada elemento da matriz para se tornar um documento separado na Pesquisa Cognitiva Azure. |
|`jsonLines` | Escolha este modo se as suas bolhas forem constituídas por várias entidades JSON, que são separadas por uma nova linha, e precisa que cada entidade se torne um documento separado na Pesquisa Cognitiva Azure. |

Pode pensar num documento como um único item nos resultados da pesquisa. Se quiser que cada elemento da matriz apareça nos resultados de pesquisa como um item independente, use a `jsonArray` opção ou `jsonLines` opção conforme apropriado.

Dentro da definição do indexante, pode utilizar opcionalmente [mapeamentos](search-indexer-field-mappings.md) de campo para escolher quais as propriedades do documento JSON de origem que são usadas para preencher o seu índice de pesquisa alvo. Para `jsonArray` o modo de análise, se a matriz existir como uma propriedade de nível inferior, pode definir uma raiz de documento indicando onde a matriz é colocada dentro da bolha.

> [!IMPORTANT]
> Quando utiliza `json` , ou modo de `jsonArray` `jsonLines` análise, a Azure Cognitive Search assume que todas as bolhas na sua fonte de dados contêm JSON. Se necessitar de suportar uma mistura de bolhas JSON e não-JSON na mesma fonte de dados, informe-nos no [nosso site UserVoice](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Bolhas JSON únicas parse

Por padrão, [o indexante de blob de pesquisa cognitiva Azure](search-howto-indexing-azure-blob-storage.md) analisa as bolhas JSON como um único pedaço de texto. Muitas vezes, quer preservar a estrutura dos seus documentos JSON. Por exemplo, assuma que tem o seguinte documento JSON no armazenamento da Azure Blob:

```http
    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }
```

O indexante blob analisa o documento JSON num único documento de Pesquisa Cognitiva Azure. O indexante carrega um índice combinando "texto", "data publicada" e "tags" da fonte contra os campos de índice de destino de nome idêntico e dactilografado.

Como notado, não são necessários mapeamentos de campo. Dado um índice com campos de "texto", "datas publicadas" e "tags", o indexante blob pode inferir o mapeamento correto sem um mapeamento de campo presente no pedido.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Parse JSON arrays

Em alternativa, pode utilizar a opção de matriz JSON. Esta opção é útil quando as bolhas contêm uma *variedade de objetos JSON bem formados*, e você quer que cada elemento se torne um documento separado de Pesquisa Cognitiva Azure. Por exemplo, dada a seguinte bolha JSON, pode povoar o seu índice de Pesquisa Cognitiva Azure com três documentos separados, cada um com campos de "id" e "texto".  

```text
    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]
```

Para uma matriz JSON, a definição do indexante deve ser semelhante ao exemplo seguinte. Note que o parâmetro parsingMode especifica o `jsonArray` parser. Especificar o analisador direito e ter a entrada de dados certa são os dois únicos requisitos específicos da matriz para a indexação de bolhas JSON.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
```

Mais uma vez, note que os mapeamentos de campo podem ser omitidos. Assumindo um índice com campos de "id" e "texto" idênticos, o indexante blob pode inferir o mapeamento correto sem uma lista explícita de mapeamento de campo.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>Matrizes aninhadas de parse
Para as matrizes JSON com elementos aninhados, pode especificar uma `documentRoot` para indicar uma estrutura de vários níveis. Por exemplo, se as suas bolhas forem assim:

```http
    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }
```

Utilize esta configuração para indexar a matriz contida na `level2` propriedade:

```http
    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }
```

## <a name="parse-blobs-separated-by-newlines"></a>Bolhas de parse separadas por linhas novas

Se a sua bolha contiver várias entidades JSON separadas por uma nova linha, e quiser que cada elemento se torne um documento separado de Pesquisa Cognitiva Azure, pode optar pela opção linhas JSON. Por exemplo, dada a seguinte bolha (onde existem três entidades JSON diferentes), pode preencher o seu índice de Pesquisa Cognitiva Azure com três documentos separados, cada um com campos de "id" e "texto".

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

Para as linhas JSON, a definição do indexante deve ser semelhante ao exemplo seguinte. Note que o parâmetro parsingMode especifica o `jsonLines` parser. 

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }
```

Mais uma vez, note que os mapeamentos de campo podem ser `jsonArray` omitidos, semelhantes ao modo de análise.

## <a name="add-field-mappings"></a>Adicionar mapeamentos de campo

Quando os campos de origem e alvo não estiverem perfeitamente alinhados, pode definir uma secção de mapeamento de campo no corpo de pedido para associações explícitas de campo para campo.

Atualmente, a Azure Cognitive Search não pode indexar documentos arbitrários de JSON diretamente porque suporta apenas tipos de dados primitivos, matrizes de cordas e pontos GeoJSON. No entanto, pode utilizar **mapeamentos** de campo para escolher partes do documento JSON e "levantá-las" em campos de alto nível do documento de pesquisa. Para saber mais sobre os básicos de mapeamento de campo, consulte [mapeamentos de campo em indexadores de Pesquisa Cognitiva Azure](search-indexer-field-mappings.md).

Revisitar o nosso exemplo documento JSON:

```http
    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }
```

Assuma um índice de pesquisa com os seguintes campos: `text` `Edm.String` tipo, `date` `Edm.DateTimeOffset` tipo, e `tags` tipo `Collection(Edm.String)` . Note a discrepância entre "data publicada" na fonte e `date` no campo no índice. Para mapear o seu JSON na forma desejada, utilize os seguintes mapeamentos de campo:

```http
    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]
```

Os nomes de campo de origem nos mapeamentos são especificados usando a notação [do ponteiro JSON.](https://tools.ietf.org/html/rfc6901) Comece com um corte para a frente para se referir à raiz do seu documento JSON, em seguida, escolha a propriedade desejada (ao nível arbitrário de nidificação) utilizando o caminho separado do corte dianteiro.

Também pode consultar elementos de matriz individuais utilizando um índice baseado em zero. Por exemplo, para escolher o primeiro elemento da matriz "tags" do exemplo acima, utilize um mapeamento de campo como este:

```http
    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> Se um nome de campo de origem num caminho de mapeamento de campo se refere a uma propriedade que não existe em JSON, esse mapeamento é ignorado sem um erro. Isto é feito para que possamos apoiar documentos com um esquema diferente (que é um caso de uso comum). Como não há validação, tem de ter cuidado para evitar tipografias na especificação de mapeamento de campo.
>

## <a name="help-us-make-azure-cognitive-search-better"></a>Ajude-nos a melhorar a pesquisa cognitiva do Azure
Se tiver pedidos de funcionalidades ou ideias para melhorias, forneça a sua entrada no [UserVoice](https://feedback.azure.com/forums/263029-azure-search/). Se precisar de ajuda para utilizar a função existente, publique a sua pergunta no [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870).

## <a name="see-also"></a>Veja também

+ [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
+ [Indexação do armazenamento de blob Azure com pesquisa cognitiva Azure](search-howto-index-json-blobs.md)
+ [Indexação de bolhas de CSV com indexante de bolha de pesquisa cognitiva Azure](search-howto-index-csv-blobs.md)
+ [Tutorial: Pesquisar dados semi-estruturados a partir do armazenamento do Azure Blob](search-semi-structured-data.md)