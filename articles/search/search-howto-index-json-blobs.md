---
title: Indexar BLOBs JSON do indexador de blob do Azure para pesquisa de texto completo-Azure Search
description: Rastreie BLOBs JSON do Azure para conteúdo de texto usando o indexador de blob Azure Search. Indexadores automatizam a ingestão de dados para fontes de dados selecionadas, como o armazenamento de BLOBs do Azure.
ms.date: 05/02/2019
author: HeidiSteen
manager: nitinme
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: d266f5edb85dd732cc39cfe98a64bee8019cdbd1
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656668"
---
# <a name="how-to-index-json-blobs-using-azure-search-blob-indexer"></a>Como indexar BLOBs JSON usando Azure Search indexador de BLOB
Este artigo mostra como configurar um indexador de blob [](search-indexer-overview.md) Azure Search para extrair conteúdo estruturado de documentos JSON no armazenamento de BLOBs do Azure e torná-lo pesquisável no Azure Search. Este fluxo de trabalho cria um índice de Azure Search e o carrega com o texto existente extraído de BLOBs JSON. 

Você pode usar o [portal](#json-indexer-portal), as [APIs REST](#json-indexer-rest)ou o [SDK do .net](#json-indexer-dotnet) para indexar conteúdo JSON. Comum a todas as abordagens é que os documentos JSON estão localizados em um contêiner de BLOB em uma conta de armazenamento do Azure. Para obter diretrizes sobre o envio de documentos JSON de outras plataformas que não são do Azure, consulte [importação de dados em Azure Search](search-what-is-data-import.md).

Os BLOBs JSON no armazenamento de BLOBs do Azure normalmente são um único documento JSON ou uma coleção de entidades JSON. Para coleções JSON, o blob poderia ter uma **matriz** de elementos JSON bem formados. Os BLOBs também podem ser compostos por várias entidades JSON individuais separadas por uma nova linha. O indexador de blob no Azure Search pode analisar qualquer construção desse tipo, dependendo de como você define o parâmetro **parsingMode** na solicitação.

Todos os modos de análise JSON`json`( `jsonArray`, `jsonLines`,) agora estão disponíveis para o público geral. 

> [!NOTE]
> Siga as recomendações de configuração do indexador em indexação de [um para muitos](search-howto-index-one-to-many-blobs.md) para produzir vários documentos de pesquisa de um blob do Azure.

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Utilizar o portal

O método mais fácil para indexar documentos JSON é usar um assistente no [portal do Azure](https://portal.azure.com/). Ao analisar os metadados no contêiner de blob do Azure, o assistente de [**importação de dados**](search-import-data-portal.md) pode criar um índice padrão, mapear campos de origem para campos de índice de destino e carregar o índice em uma única operação. Dependendo do tamanho e da complexidade dos dados de origem, você pode ter um índice operacional de pesquisa de texto completo em minutos.

É recomendável usar a mesma assinatura do Azure para o Azure Search e o armazenamento do Azure, preferencialmente na mesma região.

### <a name="1---prepare-source-data"></a>1-preparar dados de origem

1. [Entre no portal do Azure](https://portal.azure.com/).

1. [Crie um contêiner](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) de BLOBs para conter seus dados. O nível de acesso público pode ser definido como qualquer um de seus valores válidos.

Você precisará do nome da conta de armazenamento, do nome do contêiner e de uma chave de acesso para recuperar seus dados no assistente de **importação de dados** .

### <a name="2---start-import-data-wizard"></a>2-iniciar o assistente de importação de dados

Na página Visão geral do seu serviço de Azure Search, você pode [iniciar o assistente](search-import-data-portal.md) na barra de comandos ou clicando em **Adicionar Azure Search** na seção **serviço blob** do painel de navegação esquerdo da sua conta de armazenamento.

   ![Comando importar dados no portal](./media/search-import-data-portal/import-data-cmd2.png "Iniciar o assistente de importação de dados")

### <a name="3---set-the-data-source"></a>3-definir a fonte de dados

Na página **fonte de dados** , a origem deve ser o **armazenamento de BLOBs do Azure**, com as seguintes especificações:

+ **Os dados a serem extraídos** devem ser *conteúdo e metadados*. A escolha dessa opção permite que o assistente inferir um esquema de índice e mapear os campos para importação.
   
+ O **modo de análise** deve ser definido como *JSON*, *matriz JSON* ou *linhas JSON*. 

  O *JSON* articula cada blob como um único documento de pesquisa, mostrando como um item independente nos resultados da pesquisa. 

  A *matriz JSON* é para BLOBs que contêm dados JSON bem formados – o JSON bem formado corresponde a uma matriz de objetos ou tem uma propriedade que é uma matriz de objetos e você deseja que cada elemento seja articulado como um documento de pesquisa independente e autônomo. Se os BLOBs forem complexos e você não escolher a *matriz JSON* , todo o blob será ingerido como um único documento.

  *As linhas JSON* são para BLOBs compostos por várias entidades JSON separadas por uma nova linha, em que você deseja que cada entidade seja articulada como um documento de pesquisa independente autônomo. Se os BLOBs forem complexos e você não escolher o modo de análise de *linhas JSON* , todo o blob será ingerido como um único documento.
   
+ O **contêiner de armazenamento** deve especificar sua conta de armazenamento e o contêiner ou uma cadeia de conexão que resolve para o contêiner. Você pode obter cadeias de conexão na página do portal do serviço BLOB.

   ![Definição da fonte de dados do blob](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4-ignorar a página "Adicionar pesquisa cognitiva" no assistente

Adicionar habilidades cognitivas não é necessário para a importação de documento JSON. A menos que você tenha uma necessidade específica de [incluir API de serviços cognitivos e](cognitive-search-concept-intro.md) transformações em seu pipeline de indexação, ignore esta etapa.

Para ignorar a etapa, primeiro vá para a próxima página.

   ![Botão próxima página para pesquisa cognitiva](media/search-get-started-portal/next-button-add-cog-search.png)

Nessa página, você pode pular para a personalização do índice.

   ![Ignorar o passo de capacidades cognitivas](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5-definir atributos de índice

Na página **índice** , você verá uma lista de campos com um tipo de dados e uma série de caixas de seleção para definir atributos de índice. O assistente pode gerar uma lista de campos com base em metadados e por amostragem dos dados de origem. 

Você pode selecionar atributos em massa clicando na caixa de seleção na parte superior de uma coluna de atributo. Escolha **recuperável** e **pesquisável** para cada campo que deve ser retornado a um aplicativo cliente e sujeito ao processamento de pesquisa de texto completo. Você observará que inteiros não são de texto completo ou de pesquisa difusa (os números são avaliados de forma idêntica e geralmente são úteis em filtros).

Examine a descrição de [atributos de índice](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) e analisadores de [idioma](https://docs.microsoft.com/rest/api/searchservice/language-support) para obter mais informações. 

Reserve um tempo para examinar suas seleções. Depois de executar o assistente, as estruturas de dados físicos são criadas e você não poderá editar esses campos sem descartar e recriar todos os objetos.

   ![Definição de índice de BLOB](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6-criar indexador

Totalmente especificado, o assistente cria três objetos distintos em seu serviço de pesquisa. Um objeto de fonte de dados e um objeto de índice são salvos como recursos nomeados em seu serviço de Azure Search. A última etapa cria um objeto de indexador. Nomear o indexador permite que ele exista como um recurso autônomo, que você pode agendar e gerenciar independentemente do índice e do objeto de fonte de dados, criado na mesma sequência do assistente.

Se você não estiver familiarizado com indexadores, um *indexador* é um recurso em Azure Search que rastreia uma fonte de dados externa para conteúdo pesquisável. A saída do assistente de **importação de dados** é um indexador que rastreia sua fonte de dados JSON, extrai conteúdo pesquisável e importa-o para um índice em Azure Search.

   ![Definição do indexador de BLOB](media/search-howto-index-json/import-wizard-json-indexer.png)

Clique em **OK** para executar o assistente e criar todos os objetos. A indexação começa imediatamente.

Você pode monitorar a importação de dados nas páginas do Portal. As notificações de progresso indicam o status de indexação e quantos documentos são carregados. 

Quando a indexação estiver concluída, você poderá usar o [Search Explorer](search-explorer.md) para consultar o índice.

> [!NOTE]
> Se você não vir os dados esperados, talvez seja necessário definir mais atributos em mais campos. Exclua o índice e o indexador que você acabou de criar e percorra o assistente novamente, modificando suas seleções de atributos de índice na etapa 5. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Utilizar APIs REST

Você pode usar a API REST para indexar BLOBs JSON, seguindo um fluxo de trabalho de três partes comum a todos os indexadores no Azure Search: criar uma fonte de dados, criar um índice, criar um indexador. A extração de dados do armazenamento de BLOBs ocorre quando você envia a solicitação criar indexador. Depois que essa solicitação for concluída, você terá um índice passível de consulta. 

Você pode examinar o [código de exemplo REST](#rest-example) no final desta seção que mostra como criar todos os três objetos. Esta seção também contém detalhes sobre os [modos de análise JSON](#parsing-modes), [BLOBs únicos](#parsing-single-blobs), [matrizes JSON](#parsing-arrays)e [matrizes aninhadas](#nested-json-arrays).

Para a indexação JSON baseada em código, use o [postmaster](search-get-started-postman.md) e a API REST para criar esses objetos:

+ [index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

A ordem das operações requer que você crie e chame objetos nesta ordem. Em contraste com o fluxo de trabalho do portal, uma abordagem de código requer um índice disponível para aceitar os documentos JSON enviados por meio da solicitação **criar indexador** .

Os BLOBs JSON no armazenamento de BLOBs do Azure normalmente são um único documento JSON ou uma "matriz" JSON. O indexador de blob no Azure Search pode analisar qualquer construção, dependendo de como você define o parâmetro **parsingMode** na solicitação.

| Documento JSON | parsingMode | Descrição | Disponibilidade |
|--------------|-------------|--------------|--------------|
| Um por blob | `json` | Analisa os BLOBs JSON como uma única parte do texto. Cada blob JSON se torna um único documento Azure Search. | Geralmente disponível na API [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) e no SDK do [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) . |
| Vários por blob | `jsonArray` | Analisa uma matriz JSON no BLOB, em que cada elemento da matriz se torna um documento Azure Search separado.  | Geralmente disponível na API [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) e no SDK do [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) . |
| Vários por blob | `jsonLines` | Analisa um blob que contém várias entidades JSON (uma "matriz") separadas por uma nova linha, em que cada entidade se torna um documento Azure Search separado. | Geralmente disponível na API [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) e no SDK do [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) . |

### <a name="1---assemble-inputs-for-the-request"></a>1-montar entradas para a solicitação

Para cada solicitação, você deve fornecer o nome do serviço e a chave de administração para Azure Search (no cabeçalho da POSTAgem) e o nome da conta de armazenamento e a chave para o armazenamento de BLOBs. Você pode usar o [postmaster](search-get-started-postman.md) para enviar solicitações HTTP para Azure Search.

Copie os quatro valores a seguir no bloco de notas para que você possa colá-los em uma solicitação:

+ Nome do serviço de Azure Search
+ Azure Search chave de administração
+ Nome da conta de armazenamento do Azure
+ Chave de conta de armazenamento do Azure

Você pode encontrar esses valores no Portal:

1. Nas páginas do portal para Azure Search, copie a URL do serviço de pesquisa da página Visão geral.

2. No painel de navegação esquerdo, clique em **chaves** e copie a chave primária ou secundária (elas são equivalentes).

3. Alterne para as páginas do portal da sua conta de armazenamento. No painel de navegação à esquerda, em **configurações**, clique em **chaves de acesso**. Essa página fornece o nome da conta e a chave. Copie o nome da conta de armazenamento e uma das chaves para o bloco de notas.

### <a name="2---create-a-data-source"></a>2-criar uma fonte de dados

Esta etapa fornece informações de conexão de fonte de dados usadas pelo indexador. A fonte de dados é um objeto nomeado no Azure Search que persiste as informações de conexão. O tipo de fonte de `azureblob`dados,, determina quais comportamentos de extração de dados são invocados pelo indexador. 

Substitua os valores válidos para o nome do serviço, a chave de administração, a conta de armazenamento e os espaços reservados da chave de conta.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3-criar um índice de pesquisa de destino 

Os indexadores são emparelhados com um esquema de índice. Se você estiver usando a API (em vez do Portal), prepare um índice antecipadamente para que você possa especificá-lo na operação do indexador.

O índice armazena conteúdo pesquisável em Azure Search. Para criar um índice, forneça um esquema que especifique os campos em um documento, atributos e outras construções que modelam a experiência de pesquisa. Se você criar um índice que tenha os mesmos nomes de campo e tipos de dados que a origem, o indexador corresponderá aos campos de origem e de destino, poupando-lhe o trabalho de ter que mapear explicitamente os campos.

O exemplo a seguir mostra uma solicitação [CREATE INDEX](https://docs.microsoft.com/rest/api/searchservice/create-index) . O índice terá um `content` campo pesquisável para armazenar o texto extraído dos BLOBs:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4-configurar e executar o indexador

Assim como ocorre com um índice e uma fonte de dados, e o indexador também é um objeto nomeado que você cria e reutiliza em um serviço de Azure Search. Uma solicitação totalmente especificada para criar um indexador pode ter a seguinte aparência:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

A configuração do indexador está no corpo da solicitação. Ele requer uma fonte de dados e um índice de destino vazio que já exista no Azure Search. 

Agendamento e parâmetros são opcionais. Se você omiti-los, o indexador será executado imediatamente `json` , usando como o modo de análise.

Esse indexador específico não inclui mapeamentos de campo. Dentro da definição do indexador, você pode deixar os mapeamentos de **campo** se as propriedades do documento JSON de origem corresponderem aos campos do índice de pesquisa de destino. 


### <a name="rest-example"></a>Exemplo de REST

Esta seção é uma recapitulação de todas as solicitações usadas para criar objetos. Para obter uma discussão sobre partes do componente, consulte as seções anteriores neste artigo.

### <a name="data-source-request"></a>Solicitação de fonte de dados

Todos os indexadores exigem um objeto de fonte de dados que fornece informações de conexão para os dados existentes. 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Solicitação de índice

Todos os indexadores exigem um índice de destino que recebe os dados. O corpo da solicitação define o esquema de índice, que consiste em campos, atribuídos para dar suporte aos comportamentos desejados em um índice pesquisável. Esse índice deve estar vazio quando você executa o indexador. 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Solicitação do indexador

Essa solicitação mostra um indexador totalmente especificado. Ele inclui mapeamentos de campo, que foram omitidos nos exemplos anteriores. Lembre-se de que "Schedule", "Parameters" e "fieldMappings" são opcionais, desde que haja um padrão disponível. Omitir "Schedule" faz com que o indexador seja executado imediatamente. Omitir "parsingMode" faz com que o índice use o padrão "JSON".

Criar o indexador em Azure Search aciona a importação de dados. Ele é executado imediatamente e depois de um agendamento, se você tiver fornecido um.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

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

O SDK do .NET tem paridade total com a API REST. Recomendamos que você examine a seção anterior da API REST para saber os conceitos, o fluxo de trabalho e os requisitos. Em seguida, você pode consultar a seguinte documentação de referência da API .NET para implementar um indexador JSON em código gerenciado.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Modos de análise

Os BLOBs JSON podem assumir vários formulários. O parâmetro **parsingMode** no indexador JSON determina como o conteúdo do blob JSON é analisado e estruturado em um índice de Azure Search:

| parsingMode | Descrição |
|-------------|-------------|
| `json`  | Indexe cada blob como um único documento. Esse é o padrão. |
| `jsonArray` | Escolha esse modo se os BLOBs consistirem em matrizes JSON e você precisar que cada elemento da matriz se torne um documento separado em Azure Search. |
|`jsonLines` | Escolha esse modo se seus BLOBs consistirem em várias entidades JSON, que são separadas por uma nova linha, e você precisa que cada entidade se torne um documento separado em Azure Search. |

Você pode considerar um documento como um único item nos resultados da pesquisa. Se você quiser que cada elemento na matriz apareça nos resultados da pesquisa como um item independente, use a `jsonArray` opção ou `jsonLines` conforme apropriado.

Na definição do indexador, opcionalmente, você pode usar mapeamentos de [campo](search-indexer-field-mappings.md) para escolher quais propriedades do documento JSON de origem serão usadas para preencher o índice de pesquisa de destino. Para `jsonArray` o modo de análise, se a matriz existir como uma propriedade de nível inferior, você poderá definir uma raiz do documento indicando onde a matriz é colocada no BLOB.

> [!IMPORTANT]
> Quando você usa `json`o `jsonArray` , `jsonLines` ou o modo de análise, Azure Search pressupõe que todos os BLOBs em sua fonte de dados contêm JSON. Se você precisar dar suporte a uma combinação de BLOBs JSON e não JSON na mesma fonte de dados, informe-nos em [nosso site UserVoice](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Analisar BLOBs JSON únicos

Por padrão, [Azure Search indexador de blob](search-howto-indexing-azure-blob-storage.md) analisa os BLOBs JSON como uma única parte do texto. Geralmente, você deseja preservar a estrutura de seus documentos JSON. Por exemplo, suponha que você tenha o seguinte documento JSON no armazenamento de BLOBs do Azure:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

O indexador de blob analisa o documento JSON em um único documento Azure Search. O indexador carrega um índice por correspondência de "texto", "datePublished" e "marcações" da origem em relação aos campos de índice de destino nomeados e digitados de forma idêntica.

Como observado, os mapeamentos de campo não são necessários. Dado um índice com os campos "text", "datePublished" e "tags", o indexador de blob pode inferir o mapeamento correto sem um mapeamento de campo presente na solicitação.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Analisar matrizes JSON

Como alternativa, você pode usar a opção de matriz JSON. Essa opção é útil quando os BLOBs contêm uma *matriz de objetos JSON bem formados*e você deseja que cada elemento se torne um documento Azure Search separado. Por exemplo, dado o blob JSON a seguir, você pode preencher o índice de Azure Search com três documentos separados, cada um com os campos "ID" e "texto".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Para uma matriz JSON, a definição do indexador deve ser semelhante ao exemplo a seguir. Observe que o parâmetro parsingMode especifica o `jsonArray` analisador. Especificar o analisador correto e ter a entrada de dados correta são os únicos dois requisitos específicos de matriz para indexação de BLOBs JSON.

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

Novamente, observe que os mapeamentos de campo podem ser omitidos. Supondo um índice com nomes de campos "ID" e "texto" idênticos, o indexador de blob pode inferir o mapeamento correto sem uma lista explícita de mapeamento de campos.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>Analisar matrizes aninhadas
Para matrizes JSON com elementos aninhados, você pode `documentRoot` especificar um para indicar uma estrutura de vários níveis. Por exemplo, se seus BLOBs tiverem esta aparência:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Use essa configuração para indexar a matriz contida na `level2` Propriedade:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="parse-blobs-separated-by-newlines"></a>Analisar BLOBs separados por novas linhas

Se o blob contiver várias entidades JSON separadas por uma nova linha e você desejar que cada elemento se torne um documento Azure Search separado, você poderá optar pela opção de linhas JSON. Por exemplo, considerando o blob a seguir (em que há três entidades JSON diferentes), você pode preencher o índice de Azure Search com três documentos separados, cada um com os campos "ID" e "text".

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

Para linhas JSON, a definição do indexador deve ser semelhante ao exemplo a seguir. Observe que o parâmetro parsingMode especifica o `jsonLines` analisador. 

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

Novamente, observe que os mapeamentos de campo podem ser omitidos, `jsonArray` semelhante ao modo de análise.

## <a name="add-field-mappings"></a>Adicionar mapeamentos de campo

Quando os campos de origem e de destino não estiverem perfeitamente alinhados, você poderá definir uma seção de mapeamento de campo no corpo da solicitação para associações explícitas de campo para campo.

Atualmente, Azure Search não pode indexar documentos JSON arbitrários diretamente porque ele dá suporte apenas a tipos de dados primitivos, matrizes de cadeia de caracteres e pontos geojson. No entanto, você pode usar mapeamentos de **campo** para escolher partes do seu documento JSON e "retirá-los" em campos de nível superior do documento de pesquisa. Para saber mais sobre os mapeamentos de campo básicos, consulte [mapeamentos de campo em indexadores de Azure Search](search-indexer-field-mappings.md).

Revisitando nosso documento JSON de exemplo:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Suponha um índice de pesquisa com os seguintes campos `text` : do `Edm.String`tipo `date` , do `Edm.DateTimeOffset`tipo e `tags` do tipo `Collection(Edm.String)`. Observe a discrepância entre "datePublished" na origem e `date` no campo no índice. Para mapear o JSON para a forma desejada, use os seguintes mapeamentos de campo:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Os nomes de campo de origem nos mapeamentos são especificados usando a notação de [ponteiro JSON](https://tools.ietf.org/html/rfc6901) . Você começa com uma barra invertida para fazer referência à raiz do seu documento JSON e escolhe a propriedade desejada (em nível arbitrário de aninhamento) usando o caminho separado por barra invertida.

Você também pode se referir a elementos individuais da matriz usando um índice de base zero. Por exemplo, para escolher o primeiro elemento da matriz "tags" do exemplo acima, use um mapeamento de campo como este:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Se um nome de campo de origem em um caminho de mapeamento de campo se referir a uma propriedade que não existe em JSON, esse mapeamento será ignorado sem erro. Isso é feito para que possamos dar suporte a documentos com um esquema diferente (que é um caso de uso comum). Como não há nenhuma validação, você precisa tomar cuidado para evitar erros de digitação em sua especificação de mapeamento de campo.
>
>

## <a name="see-also"></a>Consulte também

+ [Indexadores no Azure Search](search-indexer-overview.md)
+ [Indexando o armazenamento de BLOBs do Azure com o Azure Search](search-howto-index-json-blobs.md)
+ [Indexando BLOBs CSV com o indexador de blob Azure Search](search-howto-index-csv-blobs.md)
+ [Tutorial: Pesquisar dados semiestruturados do armazenamento de BLOBs do Azure](search-semi-structured-data.md)
