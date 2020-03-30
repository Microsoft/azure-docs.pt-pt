---
title: Pesquisar sobre bolhas JSON
titleSuffix: Azure Cognitive Search
description: Crawl Azure JSON blobs para conteúdo de texto usando o indexador De pesquisa cognitiva Azure Blob. Os indexadores automatizam a ingestão de dados para fontes de dados selecionadas, como o armazenamento de Azure Blob.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 37fc78971124240077a59d4ad99aa06cc408dbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533960"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Como indexar as bolhas JSON usando um indexante Blob em Pesquisa Cognitiva Azure

Este artigo mostra-lhe como configurar um [indexador](search-indexer-overview.md) de blob de pesquisa cognitiva Azure para extrair conteúdo estruturado a partir de documentos JSON no armazenamento de Blob Azure e torná-lo pesquisável em Pesquisa Cognitiva Azure. Este fluxo de trabalho cria um índice de Pesquisa Cognitiva Azure e carrega-o com texto existente extraído de bolhas JSON. 

Pode utilizar o [portal](#json-indexer-portal), [REST APIs,](#json-indexer-rest)ou [.NET SDK](#json-indexer-dotnet) para indexar o conteúdo da JSON. Comum a todas as abordagens é que os documentos da JSON estão localizados num contentor de bolhas numa conta de Armazenamento Azure. Para obter orientações sobre a pressão sobre documentos JSON de outras plataformas não-Azure, consulte a importação de [dados em Pesquisa Cognitiva Azure](search-what-is-data-import.md).

As bolhas JSON no armazenamento da Blob Azure são tipicamente um único `json`documento JSON (o modo de análise é) ou uma coleção de entidades JSON. Para as coleções, a bolha pode ter uma **variedade** de elementos JSON bem formados (o modo de análise é). `jsonArray` As bolhas também podem ser compostas por múltiplas entidades jSON individuais `jsonLines`separadas por uma novalinha (o modo de análise é). O parâmetro **parsingMode** no pedido determina as estruturas de saída.

> [!NOTE]
> Para obter mais informações sobre a indexação de vários documentos de pesquisa a partir de uma única bolha, consulte [indexação de um a muitos](search-howto-index-one-to-many-blobs.md).

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Utilizar o portal

O método mais fácil para indexar documentos JSON é usar um assistente no [portal Azure](https://portal.azure.com/). Ao analisar metadados no contentor de bolhas Azure, o assistente de [**dados de importação**](search-import-data-portal.md) pode criar um índice predefinido, mapear campos de origem para campos de índice seletivas e carregar o índice numa única operação. Dependendo do tamanho e complexidade dos dados de origem, poderá ter um índice operacional de pesquisa de texto completo em minutos.

Recomendamos a utilização da mesma região ou localização tanto para a Pesquisa Cognitiva Azure como para o Armazenamento Azure para uma latência mais baixa e para evitar cargas de largura de banda.

### <a name="1---prepare-source-data"></a>1 - Preparar dados de origem

[Inscreva-se no portal Azure](https://portal.azure.com/) e [crie um recipiente Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) para conter os seus dados. O Nível de Acesso Público pode ser definido para qualquer um dos seus valores válidos.

Necessitará do nome da conta de armazenamento, do nome do contentor e de uma chave de acesso para recuperar os seus dados no assistente de **dados da Importação.**

### <a name="2---start-import-data-wizard"></a>2 - Iniciar o assistente de dados de importação

Na página 'Visão Geral' do seu serviço de pesquisa, pode [iniciar o assistente a](search-import-data-portal.md) partir da barra de comando.

   ![Comando de dados de importação no portal](./media/search-import-data-portal/import-data-cmd2.png "Inicie o assistente de dados de importação")

### <a name="3---set-the-data-source"></a>3 - Definir a fonte de dados

Na página fonte de **dados,** a fonte deve ser o **Armazenamento de Blob Azure,** com as seguintes especificações:

+ **Os dados a extrair** devem ser *Conteúdo e metadados.* A escolha desta opção permite ao assistente inferir um esquema de índice e mapear os campos para importação.
   
+ O **modo de análise** deve ser definido para linhas *JSON,* *JSON* ou *JSON*. 

  *A JSON* articula cada bolha como um único documento de pesquisa, aparecendo como um item independente nos resultados da pesquisa. 

  *A matriz JSON* é para bolhas que contêm dados JSON bem formados - o JSON bem formado corresponde a uma variedade de objetos, ou tem uma propriedade que é uma variedade de objetos e você quer que cada elemento seja articulado como um documento de pesquisa independente e autónomo. Se as bolhas forem complexas, e não escolheres a *matriz JSON,* toda a bolha é ingerida como um único documento.

  *As linhas JSON* são para bolhas compostas por múltiplas entidades JSON separadas por uma nova linha, onde você quer que cada entidade seja articulada como um documento de pesquisa independente autónomo. Se as bolhas forem complexas e não escolheres o modo de análise das *linhas JSON,* então toda a bolha é ingerida como um único documento.
   
+ **O recipiente** de armazenamento deve especificar a sua conta de armazenamento e o seu recipiente, ou uma cadeia de ligação que se resolva ao recipiente. Pode obter cordas de ligação na página do portal do serviço Blob.

   ![Definição de fonte de dados blob](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - Pule a página "Enriquecer conteúdo" no assistente

A adição de competências cognitivas (ou enriquecimento) não é um requisito de importação. A menos que tenha uma necessidade específica de adicionar o enriquecimento de [IA](cognitive-search-concept-intro.md) ao seu pipeline de indexação, deve saltar este passo.

Para saltar o passo, clique nos botões azuis na parte inferior da página para "Seguinte" e "Saltar".

### <a name="5---set-index-attributes"></a>5 - Definir atributos de índice

Na página **Index,** deve ver uma lista de campos com um tipo de dados e uma série de caixas de verificação para definir atributos de índice. O assistente pode gerar uma lista de campos com base em metadados e através da amostragem dos dados de origem. 

Pode selecionar atributos em massa clicando na caixa de verificação no topo de uma coluna de atributos. Escolha **Recuperável** e **Pesquisável** para cada campo que deve ser devolvido a uma aplicação de cliente e sujeito a processamento completo de pesquisa de texto. Notará que os inteiros não são texto completo ou pesquisável (os números são avaliados verbatim e são muitas vezes úteis em filtros).

Reveja a descrição dos [atributos indexados](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) e [dos analisadores](https://docs.microsoft.com/rest/api/searchservice/language-support) de linguagem para obter mais informações. 

Tire um momento para rever as suas seleções. Uma vez executado o assistente, as estruturas de dados físicos são criadas e você não será capaz de editar estes campos sem deixar cair e recriar todos os objetos.

   ![Definição de índice blob](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - Criar indexador

Totalmente especificado, o assistente cria três objetos distintos no seu serviço de pesquisa. Um objeto de origem de dados e objeto de índice são guardados como recursos nomeados no seu serviço de Pesquisa Cognitiva Azure. O último passo cria um objeto indexante. Nomear o indexante permite-lhe existir como um recurso autónomo, que pode agendar e gerir independentemente do objeto de origem de índice e dados, criado na mesma sequência de assistentes.

Se não está familiarizado com os indexadores, um *indexante* é um recurso na Pesquisa Cognitiva Azure que rasteja uma fonte externa de dados para conteúdo pesquisável. A saída do assistente de **dados de Importação** é um indexante que rasteja a sua fonte de dados JSON, extrai conteúdo pesquisável e importa-o para um índice de Pesquisa Cognitiva Azure.

   ![Definição de indexante blob](media/search-howto-index-json/import-wizard-json-indexer.png)

Clique em **OK** para executar o assistente e criar todos os objetos. A indexação começa imediatamente.

Pode monitorizar a importação de dados nas páginas do portal. As notificações de progresso indicam o estado da indexação e quantos documentos são enviados. 

Quando a indexação estiver completa, pode utilizar o explorador de [pesquisa](search-explorer.md) para consultar o seu índice.

> [!NOTE]
> Se não vir os dados que espera, poderá ter de definir mais atributos em mais campos. Elimine o índice e o indexante que acabou de criar e volte a passar pelo assistente, modificando as suas seleções para atributos de índice no passo 5. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Utilizar APIs REST

Pode utilizar a API REST para indexar as bolhas JSON, seguindo um fluxo de trabalho em três partes comum a todos os indexadores em Pesquisa Cognitiva Azure: criar uma fonte de dados, criar um índice, criar um indexante. A extração de dados do armazenamento de blob ocorre quando submete o pedido do Indexante criar. Depois deste pedido estar concluído, terá um índice de consulta. 

Pode rever o código de [exemplo REST](#rest-example) no final desta secção que mostra como criar os três objetos. Esta secção também contém detalhes sobre modos de [aparação JSON,](#parsing-modes) [bolhas simples,](#parsing-single-blobs) [matrizes JSON](#parsing-arrays)e [matrizes aninhadas.](#nested-json-arrays)

Para indexação json baseada em código, utilize o [Carteiro](search-get-started-postman.md) e a API REST para criar estes objetos:

+ [índice](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [indexante](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

A ordem de operações requer que crie e chame objetos por esta ordem. Em contraste com o fluxo de trabalho do portal, uma abordagem de código requer um índice disponível para aceitar os documentos JSON enviados através do pedido do **Indexante criar.**

As bolhas JSON no armazenamento Da Blob Azure são tipicamente um único documento JSON ou uma "matriz" JSON. O indexante blob em Azure Cognitive Search pode analisar qualquer construção, dependendo da forma como define o parâmetro **de parsingMode** no pedido.

| Documento JSON | parsingMode | Descrição | Disponibilidade |
|--------------|-------------|--------------|--------------|
| Um por bolha | `json` | Parses JSON blobs como um único pedaço de texto. Cada blob JSON torna-se um único documento de Pesquisa Cognitiva Azure. | Geralmente disponível tanto em [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API como [em .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Múltiplos por bolha | `jsonArray` | Analisa uma matriz JSON na bolha, onde cada elemento da matriz se torna um documento de pesquisa cognitiva azure separado.  | Geralmente disponível tanto em [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API como [em .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Múltiplos por bolha | `jsonLines` | Analisa uma bolha que contém múltiplas entidades JSON (uma "matriz") separadas por uma novalinha, onde cada entidade se torna um documento de pesquisa cognitiva azure separado. | Geralmente disponível tanto em [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API como [em .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |

### <a name="1---assemble-inputs-for-the-request"></a>1 - Reunir as inputs para o pedido

Para cada pedido, deve fornecer o nome de serviço e a chave de administração para a Pesquisa Cognitiva Azure (no cabeçalho POST), bem como o nome da conta de armazenamento e a chave para armazenamento de bolhas. Pode utilizar [o Carteiro](search-get-started-postman.md) para enviar pedidos http para a Pesquisa Cognitiva Azure.

Copie os seguintes quatro valores no Bloco de Notas para que possa colá-los num pedido:

+ Nome do serviço de pesquisa cognitiva Azure
+ Chave de administração de pesquisa cognitiva azure
+ Nome da conta de armazenamento do Azure
+ Chave da conta de armazenamento azure

Pode encontrar estes valores no portal:

1. Nas páginas do portal para Pesquisa Cognitiva Azure, copie o URL do serviço de pesquisa a partir da página 'Overview'.

2. No painel de navegação à esquerda, clique em **Teclas** e, em seguida, copie a tecla primária ou secundária (são equivalentes).

3. Mude para as páginas do portal para a sua conta de armazenamento. No painel de navegação à esquerda, em **Definições,** clique em **Teclas**de acesso . Esta página fornece tanto o nome da conta como a chave. Copie o nome da conta de armazenamento e uma das chaves do Bloco de Notas.

### <a name="2---create-a-data-source"></a>2 - Criar uma fonte de dados

Este passo fornece informações de ligação de fonte de dados utilizadas pelo indexante. A fonte de dados é um objeto nomeado na Pesquisa Cognitiva Azure que persiste a informação de ligação. O tipo de `azureblob`fonte de dados, determina quais os comportamentos de extração de dados invocados pelo indexante. 

Substitua valores válidos para nome de serviço, chave de administração, conta de armazenamento e porta-chaves da conta.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3 - Criar um índice de pesquisa de alvo 

Os indexadores são emparelhados com um esquema de índice. Se estiver a utilizar a API (em vez do portal), prepare um índice com antecedência para que possa especificá-lo no funcionamento do indexante.

O índice armazena conteúdo pesquisável na Pesquisa Cognitiva Azure. Para criar um índice, forneça um esquema que especifique os campos num documento, atributos e outras construções que moldam a experiência de pesquisa. Se criar um índice que tenha os mesmos nomes de campo e tipos de dados que a fonte, o indexante corresponderá aos campos de origem e destino, poupando-lhe o trabalho de ter de mapear explicitamente os campos.

O exemplo seguinte mostra um pedido de [Criar Index.](https://docs.microsoft.com/rest/api/searchservice/create-index) O índice terá um `content` campo pesquisável para armazenar o texto extraído de bolhas:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4 - Configurar e executar o indexador

Tal como acontece com um índice e uma fonte de dados, e o indexante é também um objeto nomeado que cria e reutiliza num serviço de Pesquisa Cognitiva Azure. Um pedido totalmente especificado para criar um indexante pode parecer o seguinte:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

A configuração do indexante está no corpo do pedido. Requer uma fonte de dados e um índice de alvo vazio que já existe na Pesquisa Cognitiva Azure. 

Os horários e os parâmetros são opcionais. Se os omitia, o indexante `json` funciona imediatamente, utilizando como modo de análise.

Este indexante em particular não inclui mapeamentos de campo. Dentro da definição do indexador, pode deixar de fora **mapeamentos** de campo se as propriedades do documento JSON de origem corresponderem aos campos do seu índice de pesquisa alvo. 


### <a name="rest-example"></a>Exemplo de REPOUSO

Esta secção é uma recapitulação de todos os pedidos utilizados para a criação de objetos. Para uma discussão sobre componentes, consulte as secções anteriores neste artigo.

### <a name="data-source-request"></a>Pedido de fonte de dados

Todos os indexadores requerem um objeto de origem de dados que forneça informações de ligação aos dados existentes. 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Pedido de índice

Todos os indexadores requerem um índice-alvo que receba os dados. O corpo do pedido define o esquema indexado, constituído por campos, atribuído supor os comportamentos desejados num índice pesquisável. Este índice deve estar vazio quando se executa o indexante. 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Pedido de indexante

Este pedido mostra um indexador totalmente especificado. Inclui mapeamentos de campo, que foram omitidos em exemplos anteriores. Lembre-se que "agendar", "parâmetros" e "fieldMappings" são opcionais desde que exista um padrão disponível. Omitir "agenda" faz com que o indexante corra imediatamente. Omitir "parsingMode" faz com que o índice utilize o padrão "json".

Criar o indexador em Azure Cognitive Search despoleta a importação de dados. Funciona imediatamente, e depois, num horário, se tiver fornecido um.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
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


<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>Utilizar o .NET SDK

O .NET SDK tem total paridade com a API REST. Recomendamos que reveja a secção anterior da API REST para aprender conceitos, fluxo de trabalho e requisitos. Pode então consultar a seguinte documentação de referência da API .NET para implementar um indexador JSON em código gerido.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Modos de análise

As bolhas JSON podem assumir múltiplas formas. O parâmetro **de parsingMode** no indexador JSON determina como o conteúdo da bolha JSON é analisado e estruturado num índice de pesquisa cognitiva Azure:

| parsingMode | Descrição |
|-------------|-------------|
| `json`  | Indexe cada bolha como um único documento. Esta é a predefinição. |
| `jsonArray` | Escolha este modo se as suas bolhas consistem em matrizes JSON, e precisa de cada elemento da matriz para se tornar um documento separado na Pesquisa Cognitiva Azure. |
|`jsonLines` | Escolha este modo se as suas bolhas consistem em múltiplas entidades JSON, que são separadas por uma nova linha, e precisa que cada entidade se torne um documento separado na Pesquisa Cognitiva Azure. |

Pode pensar num documento como um único item nos resultados da pesquisa. Se quiser que cada elemento da matriz apareça nos resultados `jsonArray` da `jsonLines` pesquisa como um item independente, utilize a ou a opção conforme apropriado.

Dentro da definição do indexador, pode utilizar opcionalmente [mapeamentos](search-indexer-field-mappings.md) de campo para escolher quais as propriedades do documento JSON de origem que são usadas para povoar o seu índice de pesquisa alvo. Para `jsonArray` o modo de análise, se a matriz existir como uma propriedade de nível inferior, pode definir uma raiz de documento indicando onde a matriz é colocada dentro da bolha.

> [!IMPORTANT]
> Quando utiliza `json` `jsonArray` , `jsonLines` ou em modo de análise, a Pesquisa Cognitiva Azure assume que todas as bolhas na sua fonte de dados contêm JSON. Se necessitar de suportar uma mistura de bolhas JSON e não JSON na mesma fonte de dados, informe-nos no [nosso site userVoice](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Bolhas JSON individuais parse

Por padrão, [o indexante blob de pesquisa cognitiva Azure](search-howto-indexing-azure-blob-storage.md) analisa as bolhas JSON como um único pedaço de texto. Muitas vezes, quer preservar a estrutura dos seus documentos JSON. Por exemplo, assuma que tem o seguinte documento JSON no armazenamento azure Blob:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

O indexante blob analisa o documento JSON num único documento de Pesquisa Cognitiva Azure. O indexante carrega um índice combinando "texto", "dataPublished" e "tags" da fonte contra os mesmos campos de índice sinuoso e dactilografados.

Como notado, não são necessários mapeamentos de campo. Dado um índice com campos de "texto", "dataPublicado e "tags", o indexante blob pode inferir o mapeamento correto sem um mapeamento de campo presente no pedido.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Matrizes Parse JSON

Em alternativa, pode utilizar a opção de matriz JSON. Esta opção é útil quando as bolhas contêm uma *série de objetos JSON bem formados,* e você quer que cada elemento se torne um documento de pesquisa cognitiva azure separado. Por exemplo, dada a seguinte bolha JSON, pode povoar o seu índice de Pesquisa Cognitiva Azure com três documentos separados, cada um com campos de "id" e "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Para uma matriz JSON, a definição do indexante deve ser semelhante ao seguinte exemplo. Note que o parâmetro parsingMode especifica o `jsonArray` parser. Especificar o parser certo e ter a entrada de dados certa são os dois únicos requisitos específicos para indexar as bolhas JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Mais uma vez, note que os mapeamentos de campo podem ser omitidos. Assumindo um índice com campos de "id" e "text" idênticos, o indexante blob pode inferir o mapeamento correto sem uma lista explícita de mapeamento de campo.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>Conjuntos aninhadas parse
Para as matrizes JSON com elementos `documentRoot` aninhados, pode especificar um para indicar uma estrutura de vários níveis. Por exemplo, se as suas bolhas são assim:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Utilize esta configuração para indexar `level2` a matriz contida na propriedade:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="parse-blobs-separated-by-newlines"></a>Bolhas parsiadas separadas por novas linhas

Se a sua bolha contiver várias entidades JSON separadas por uma nova linha, e quiser que cada elemento se torne um documento de Pesquisa Cognitiva Azure separado, pode optar pela opção de linhas JSON. Por exemplo, dada a seguinte bolha (onde existem três diferentes entidades JSON), pode povoar o seu índice de Pesquisa Cognitiva Azure com três documentos separados, cada um com campos de "id" e "text".

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

Para as linhas JSON, a definição do indexante deve ser semelhante ao seguinte exemplo. Note que o parâmetro parsingMode especifica o `jsonLines` parser. 

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

Mais uma vez, note que os mapeamentos de campo podem ser omitidos, semelhantes ao `jsonArray` modo de análise.

## <a name="add-field-mappings"></a>Adicionar mapeamentos de campo

Quando os campos de origem e alvo não estão perfeitamente alinhados, pode definir uma secção de mapeamento de campo no organismo de pedido para associações explícitas de campo-campo.

Atualmente, a Pesquisa Cognitiva Azure não pode indexar documentos jSON arbitrários diretamente porque suporta apenas tipos de dados primitivos, matrizes de cordas e pontos GeoJSON. No entanto, pode utilizar **mapeamentos** de campo para escolher partes do seu documento JSON e "elevá-las" para campos de alto nível do documento de pesquisa. Para saber sobre os mapeamentos de campo básicos, consulte [mapeamentos](search-indexer-field-mappings.md)de campo em indexadores de pesquisa cognitiva Azure .

Revisitando o nosso exemplo documento JSON:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Assuma um índice de `text` pesquisa `Edm.String` `date` com `Edm.DateTimeOffset`os `tags` seguintes `Collection(Edm.String)`campos: de tipo, de tipo, e de tipo . Note a discrepância entre "dataPublicada" `date` na fonte e no campo no índice. Para mapear o seu JSON na forma desejada, utilize os seguintes mapeamentos de campo:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Os nomes de campo de origem nos mapeamentos são especificados utilizando a notação [do ponteiro JSON.](https://tools.ietf.org/html/rfc6901) Comece com um corte dianteiro para se referir à raiz do seu documento JSON, em seguida, escolha a propriedade desejada (ao nível arbitrário de nidificação) usando o caminho separado para a frente.

Também pode consultar elementos de matriz individuais utilizando um índice de base zero. Por exemplo, para escolher o primeiro elemento da matriz "tags" do exemplo acima, utilize um mapeamento de campo como este:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Se um nome de campo de origem num caminho de mapeamento de campo se refere a uma propriedade que não existe em JSON, esse mapeamento é ignorado sem um erro. Isto é feito para que possamos apoiar documentos com um esquema diferente (que é um caso de uso comum). Como não há validação, é necessário ter cuidado para evitar os erros na sua especificação de mapeamento de campo.
>
>

## <a name="see-also"></a>Consulte também

+ [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
+ [Indexação do armazenamento de blob azure com pesquisa cognitiva azure](search-howto-index-json-blobs.md)
+ [Indexação de bolhas CSV com índice de blob de pesquisa cognitiva Azure](search-howto-index-csv-blobs.md)
+ [Tutorial: Pesquisar dados semi-estruturados a partir do armazenamento da Blob Azure](search-semi-structured-data.md)
