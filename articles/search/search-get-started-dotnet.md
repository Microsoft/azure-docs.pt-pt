---
title: 'Quickstart: Criar um índice de pesquisa em .NET'
titleSuffix: Azure Cognitive Search
description: Neste início rápido C#, aprenda a criar um índice, a carregar dados e a executar consultas utilizando a biblioteca de clientes Azure.Search.Documents.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 448f2b3e42e98d78652a005f5d1c11f55acdebb3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021189"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>Quickstart: Criar um índice de pesquisa utilizando a biblioteca de clientes Azure.Search.Documents

Utilize a nova [ biblioteca de clientesAzure.Search.Doc(versão 11)](/dotnet/api/overview/azure/search.documents-readme) para criar uma aplicação de consola .NET Core em C# que cria, carrega e consulta um índice de pesquisa.

Você pode [baixar o código fonte](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) para começar com um projeto acabado ou seguir os passos deste artigo para criar o seu próprio.

> [!NOTE]
> À procura de uma versão anterior? Consulte [Criar um índice de pesquisa utilizando microsoft.Azure.Search v10](search-get-started-dotnet-v10.md) em vez disso.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, tenha as seguintes ferramentas e serviços:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/)

+ Um serviço de Pesquisa Cognitiva Azure. [Crie um serviço](search-create-service-portal.md) ou [encontre um serviço existente.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) Pode utilizar um serviço gratuito para este arranque rápido. 

+ [Estúdio Visual,](https://visualstudio.microsoft.com/downloads/)qualquer edição. O código de amostra foi testado na edição comunitária gratuita do Visual Studio 2019.

Ao configurar o seu projeto, você irá baixar o [ pacoteAzure.Search.Documents NuGet](https://www.nuget.org/packages/Azure.Search.Documents/).

Azure SDK para .NET está em conformidade com [a Norma .NET 2.0,](/dotnet/standard/net-standard#net-implementation-support)que significa .NET Framework 4.6.1 e .NET Core 2.0 como requisitos mínimos.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Monte as informações de ligação de serviço e, em seguida, inicie o Visual Studio para criar um novo projeto de App de Consola que pode funcionar em .NET Core.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>Copiar uma chave e ponto final

As chamadas para o serviço requerem um ponto final URL e uma chave de acesso em cada pedido. Como primeiro passo, encontre a chave API e URL para adicionar ao seu projeto. Irá especificar ambos os valores ao criar o cliente num passo posterior.

1. [Inscreva-se no portal Azure,](https://portal.azure.com/)e na página **geral do** seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

2. Em **Settings**  >  **Definições Teclas**, obtenha uma chave de administração para todos os direitos sobre o serviço, necessários para criar ou eliminar objetos. Há duas chaves primárias e secundárias intercambiáveis. Pode usar qualquer um.

   ![Obtenha uma chave de acesso http e acesso](media/search-get-started-rest/get-url-key.png "Obtenha uma chave de acesso http e acesso")

Todos os pedidos requerem uma chave API em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

### <a name="install-the-nuget-package"></a>Instale o pacote NuGet

Após a criação do projeto, adicione a biblioteca do cliente. O [ pacoteAzure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/) é composto por uma biblioteca de clientes que fornece todas as APIs utilizadas para trabalhar com um serviço de pesquisa em .NET.

1. Inicie o Visual Studio e crie uma aplicação de consola .NET Core.

1. In **Tools**  >  **NuGet Package Manager**, selecione Gerir **pacotes nuget para solução...**. 

1. Clique em **Browse** (Procurar).

1. Procure `Azure.Search.Documents` e selecione a versão 11.0 ou posterior.

1. Clique em **Instalar** no direito de adicionar o conjunto ao seu projeto e solução.

### <a name="create-a-search-client"></a>Criar um cliente de pesquisa

1. Em **Program.cs,** altere o espaço de nome para `AzureSearch.SDK.Quickstart.v11` e, em seguida, adicione as `using` seguintes diretivas.

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Criar dois clientes: [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) cria o índice, e [SearchClient](/dotnet/api/azure.search.documents.searchclient) carrega e consulta um índice existente. Ambos precisam do ponto final de serviço e de uma chave API de administração para autenticação com direitos de criação/eliminação.

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

        // Create a SearchIndexClient to send create/delete index commands
        Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
        AzureKeyCredential credential = new AzureKeyCredential(apiKey);
        SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, credential);

        // Create a SearchClient to load and query documents
        SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1 - Criar um índice

Este quickstart constrói um índice de Hotéis que você carregará com dados do hotel e executará consultas contra. Neste passo, definir os campos no índice. Cada definição de campo inclui um nome, tipo de dados e atributos que determinam como o campo é usado.

Neste exemplo, os métodos sincronizados da biblioteca Azure.Search.Documents são usados para a simplicidade e legibilidade. No entanto, para cenários de produção, deve utilizar métodos assíncronos para manter a sua aplicação escalável e responsiva. Por exemplo, utilizaria [o CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) em vez de [CreateIndex.](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex)

1. Adicione uma definição de classe vazia ao seu projeto: **Hotel.cs**

1. Copie o seguinte código em **Hotel.cs** para definir a estrutura de um documento de hotel. Os atributos no campo determinam como é usado numa aplicação. Por exemplo, o `IsFilterable` atributo deve ser atribuído a todos os campos que suportem uma expressão de filtro.

    ```csharp
    using System;
    using System.Text.Json.Serialization;
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;

    namespace AzureSearch.Quickstart
    {
        public partial class Hotel
        {
            [SimpleField(IsKey = true, IsFilterable = true)]
            public string HotelId { get; set; }

            [SearchableField(IsSortable = true)]
            public string HotelName { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Category { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public bool? ParkingIncluded { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public double? Rating { get; set; }

            [SearchableField]
            public Address Address { get; set; }
        }
    }
    ```

   Na biblioteca de clientes Azure.Search.Doc, pode utilizar [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) e [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) para simplificar definições de campo. Ambos são derivados de um [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) e podem potencialmente simplificar o seu código:

   + `SimpleField` pode ser qualquer tipo de dados, é sempre não pesmável (é ignorado para consultas completas de pesquisa de texto), e é recuperável (não está escondido). Outros atributos estão desligados por padrão, mas podem ser ativados. Pode utilizar um `SimpleField` para identificação de documentos ou campos utilizados apenas em filtros, facetas ou perfis de pontuação. Em caso afirmativo, certifique-se de aplicar quaisquer atributos necessários para o cenário, como `IsKey = true` para um documento ID. Para mais informações, consulte [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) no código fonte.

   + `SearchableField` deve ser uma corda, e é sempre pescaizável e recuperável. Outros atributos estão desligados por padrão, mas podem ser ativados. Como este tipo de campo é pes pes pes pesjável, suporta sinónimos e o complemento completo das propriedades do analisador. Para mais informações, consulte a [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) no código fonte.

   Quer utilize a `SearchField` API básica ou qualquer um dos modelos auxiliares, tem de ativar explicitamente os atributos de filtro, faceta e classificação. Por exemplo, [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable)e [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) devem ser explicitamente atribuídos, como mostrado na amostra acima. 

1. Adicione uma segunda definição de classe vazia ao seu projeto: **Address.cs**.  Copie o seguinte código para a classe.

   ```csharp
   using Azure.Search.Documents.Indexes;

    namespace AzureSearch.Quickstart
    {
        public partial class Address
        {
            [SearchableField(IsFilterable = true)]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
   ```

1. Crie mais duas classes: **Hotel.Methods.cs** e **Address.Methods.cs** para substituições de ToString.). Estas classes são usadas para tornar os resultados da pesquisa na saída da consola.  O conteúdo destas aulas não é fornecido neste artigo, mas pode copiar o código a partir de [ficheiros no GitHub](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11/AzureSearchQuickstart-v11).

1. Em **Program.cs,** crie um objeto [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) e, em seguida, ligue para o método [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) para expressar o índice no seu serviço de pesquisa. O índice também inclui um [SearchSuggester](/dotnet/api/azure.search.documents.indexes.models.searchsuggester) para permitir o preconto automático nos campos especificados.

   ```csharp
    // Create hotels-quickstart index
    private static void CreateIndex(string indexName, SearchIndexClient adminClient)
    {
        FieldBuilder fieldBuilder = new FieldBuilder();
        var searchFields = fieldBuilder.Build(typeof(Hotel));

        var definition = new SearchIndex(indexName, searchFields);

        var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
        definition.Suggesters.Add(suggester);

        adminClient.CreateOrUpdateIndex(definition);
    }
   ```

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Documentos de carga

A azure Cognitive Search procura sobre o conteúdo armazenado no serviço. Neste passo, você carregará documentos JSON que estejam em conformidade com o índice hotel que acabou de criar.

Na Pesquisa Cognitiva Azure, os documentos de pesquisa são estruturas de dados que são entradas para indexação e saídas de consultas. Tal como obtido a partir de uma fonte de dados externa, as entradas de documentos podem ser linhas numa base de dados, bolhas no armazenamento blob ou documentos JSON no disco. Neste exemplo, estamos pegando um atalho e incorporando documentos JSON para quatro hotéis no próprio código. 

Ao carregar documentos, deve utilizar um objeto [IndexDocumentsBatch.](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) Um `IndexDocumentsBatch` objeto contém uma coleção de [Ações,](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions)cada uma das quais contém um documento e uma propriedade que diz à Azure Cognitive Search que ação a executar ([upload, fusão, exclusão e fusãoOrUpload).](search-what-is-data-import.md#indexing-actions)

1. Em **Program.cs,** crie uma série de documentos e ações de índice, e depois passe a matriz para `IndexDocumentsBatch` . Os documentos abaixo estão em conformidade com o índice hotéis-quickstart, conforme definido pela classe hoteleira.

    ```csharp
    // Upload documents in a single Upload request.
    private static void UploadDocuments(SearchClient searchClient)
    {
        IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "1",
                    HotelName = "Secret Point Motel",
                    Description = "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                    DescriptionFr = "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "air conditioning", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1970, 1, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.6,
                    Address = new Address()
                    {
                        StreetAddress = "677 5th Ave",
                        City = "New York",
                        StateProvince = "NY",
                        PostalCode = "10022",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "3",
                    HotelName = "Triple Landscape Hotel",
                    Description = "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Resort and Spa",
                    Tags = new[] { "air conditioning", "bar", "continental breakfast" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(2015, 9, 20, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.80,
                    Address = new Address()
                    {
                        StreetAddress = "3393 Peachtree Rd",
                        City = "Atlanta",
                        StateProvince = "GA",
                        PostalCode = "30326",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "4",
                    HotelName = "Sublime Cliff Hotel",
                    Description = "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                    DescriptionFr = "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                    Category = "Boutique",
                    Tags = new[] { "concierge", "view", "24-hour front desk service" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1960, 2, 06, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.60,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                })
            );

        try
        {
            IndexDocumentsResult result = searchClient.IndexDocuments(batch);
        }
        catch (Exception)
        {
            // If for some reason any documents are dropped during indexing, you can compensate by delaying and
            // retrying. This simple demo just logs the failed document keys and continues.
            Console.WriteLine("Failed to index some of the documents: {0}");
        }
    }
    ```

    Uma vez inicializando o objeto [IndexDocumentsBatch,](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) pode enviá-lo para o índice chamando [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) no seu objeto [SearchClient.](/dotnet/api/azure.search.documents.searchclient)

1. Adicione as seguintes linhas ao Main(). Os documentos de carregamento são feitos usando SearchClient, mas a operação também requer direitos de administração no serviço, que é tipicamente associado ao SearchIndexClient. Uma forma de configurar esta operação é obter SearchClient através do SearchIndexClient (adminClient neste exemplo).

   ```csharp
    SearchClient ingesterClient = adminClient.GetSearchClient(indexName);

    // Load documents
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(ingesterClient);
   ```

1. Como esta é uma aplicação de consola que executa todos os comandos sequencialmente, adicione um tempo de espera de 2 segundos entre indexação e consultas.

    ```csharp
    // Wait 2 seconds for indexing to complete before starting queries (for demo and console-app purposes only)
    Console.WriteLine("Waiting for indexing...\n");
    System.Threading.Thread.Sleep(2000);
    ```

    O atraso de 2 segundos compensa a indexação, que é assíncronia, para que todos os documentos possam ser indexados antes de as consultas serem executadas. A codificação num atraso é normalmente apenas necessária em demos, testes e aplicações de amostra.

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Pode obter resultados de consulta assim que o primeiro documento estiver indexado, mas os testes reais do seu índice devem aguardar até que todos os documentos estejam indexados.

Esta secção adiciona duas peças de funcionalidade: lógica de consulta e resultados. Para consultas, utilize o método ['Procurar'.](/dotnet/api/azure.search.documents.searchclient.search) Este método requer o texto de pesquisa (a cadeia de consulta) bem como outras [opções](/dotnet/api/azure.search.documents.searchoptions).

A classe [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1) representa os resultados.

1. Em **Program.cs**, crie um método **WriteDocuments** que imprime os resultados de pesquisa para a consola.

    ```csharp
    // Write search results to console
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.GetResults())
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. Crie um método **RunQueries** para executar consultas e obter resultados de retorno. Os resultados são objetos do hotel. Esta amostra mostra a assinatura do método e a primeira consulta. Esta consulta demonstra o parâmetro Select que permite compor o resultado utilizando campos selecionados do documento.

    ```csharp
    // Run queries, use WriteDocuments to print output
    private static void RunQueries(SearchClient srchclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on empty term '*' to return all documents, showing a subset of fields...\n");

        options = new SearchOptions()
        {
            IncludeTotalCount = true,
            Filter = "",
            OrderBy = { "" }
        };

        options.Select.Add("HotelId");
        options.Select.Add("HotelName");
        options.Select.Add("Address/City");

        response = srchclient.Search<Hotel>("*", options);
        WriteDocuments(response);
        ```

1. In the second query, search on a term, add a filter that selects documents where Rating is greater than 4, and then sort by Rating in descending order. Filter is a boolean expression that is evaluated over [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) fields in an index. Filter queries either include or exclude values. As such, there is no relevance score associated with a filter query. 

    ```csharp
    Console.WriteLine("Query #2: Search on 'hotels', filter on 'Rating gt 4', sort by Rating in descending order...\n");

    options = new SearchOptions()
    {
        Filter = "Rating gt 4",
        OrderBy = { "Rating desc" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Rating");

    response = srchclient.Search<Hotel>("hotels", options);
    WriteDocuments(response);
    ```

1. A terceira consulta demonstra searchFields, usada para estender uma operação completa de pesquisa de texto a campos específicos.

    ```csharp
    Console.WriteLine("Query #3: Limit search to specific fields (pool in Tags field)...\n");

    options = new SearchOptions()
    {
        SearchFields = { "Tags" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Tags");

    response = srchclient.Search<Hotel>("pool", options);
    WriteDocuments(response);
    ```

1. A quarta consulta demonstra facetas, que podem ser usadas para estruturar uma estrutura de navegação facetada. 

   ```csharp
    Console.WriteLine("Query #4: Facet on 'Category'...\n");

    options = new SearchOptions()
    {
        Filter = ""
    };

    options.Facets.Add("Category");

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Category");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
   ```

1. Na quinta consulta, devolva um documento específico. Uma procura de documento é uma resposta típica ao evento OnClick num conjunto de resultados.

   ```csharp
    Console.WriteLine("Query #5: Look up a specific document...\n");

    Response<Hotel> lookupResponse;
    lookupResponse = srchclient.GetDocument<Hotel>("3");

    Console.WriteLine(lookupResponse.Value.HotelId);
   ```

1. A última consulta mostra a sintaxe para autocomplete, simulando uma entrada parcial do utilizador de "sa" que resolve dois possíveis jogos nos campos de origem associados ao sugestivo que definiu no índice.

   ```csharp
    Console.WriteLine("Query #6: Call Autocomplete on HotelName that starts with 'sa'...\n");

    var autoresponse = srchclient.Autocomplete("sa", "sg");
    WriteDocuments(autoresponse);
   ```

1. Adicione **RunQueries** ao Main().

    ```csharp
    // Call the RunQueries method to invoke a series of queries
    Console.WriteLine("Starting queries...\n");
    RunQueries(srchclient);

    // End the program
    Console.WriteLine("{0}", "Complete. Press any key to end this program...\n");
    Console.ReadKey();
    ```

As consultas anteriores mostram múltiplas [formas de combinar termos numa consulta:](search-query-overview.md#types-of-queries)pesquisa de texto completo, filtros e preconto automático.

A pesquisa completa por texto e os filtros são realizados utilizando o método [SearchClient.Search.](/dotnet/api/azure.search.documents.searchclient.search) Uma consulta de pesquisa pode ser passada na `searchText` cadeia, enquanto uma expressão de filtro pode ser passada na propriedade [Filter](/dotnet/api/azure.search.documents.searchoptions.filter) da classe [SearchOptions.](/dotnet/api/azure.search.documents.searchoptions) Para filtrar sem procurar, basta passar `"*"` para o parâmetro do método De `searchText` [pesquisa.](/dotnet/api/azure.search.documents.searchclient.search) Para pesquisar sem filtrar, deixe a `Filter` propriedade desaparada, ou não passe em qualquer `SearchOptions` caso.

## <a name="run-the-program"></a>Execute o programa

Prima F5 para reconstruir a app e executar o programa na sua totalidade. 

A saída inclui mensagens do [Console.WriteLine,](/dotnet/api/system.console.writeline)com a adição de informações e resultados de consulta.

## <a name="clean-up-resources"></a>Limpar recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **de todos os recursos** ou **grupos** de recursos no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para ficar abaixo do limite. 

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido C#, trabalhou através de um conjunto de tarefas para criar um índice, carregá-lo com documentos e executar consultas. Em diferentes fases, tomamos atalhos para simplificar o código de legibilidade e compreensão. Se se sentir confortável com os conceitos básicos, recomendamos o próximo artigo para uma exploração de abordagens e conceitos alternativos que irão aprofundar o seu conhecimento. 

> [!div class="nextstepaction"]
> [Como desenvolver em .NET](search-howto-dotnet-sdk.md)

Quer otimizar e economizar nos gastos na nuvem?

> [!div class="nextstepaction"]
> [Comece a analisar custos com a Gestão de Custos](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)