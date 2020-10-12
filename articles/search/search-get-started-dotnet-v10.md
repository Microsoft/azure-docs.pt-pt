---
title: Início rápido Legacy C#
titleSuffix: Azure Cognitive Search
description: Este quickstart C# utiliza a biblioteca de clientes da versão 10 (Microsoft.Azure.Search) para criar, carregar e consultar um índice de pesquisa.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ce676c8966f67aeb233b2b9daf3f8f1c57327e6a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89462093"
---
# <a name="quickstart-create-a-search-index-using-the-microsoftazuresearch-v10-client-library"></a>Quickstart: Criar um índice de pesquisa utilizando a biblioteca cliente Microsoft.Azure.Search v10

Este artigo é o início rápido C# para a biblioteca cliente legado Microsoft.Azure.Search (versão 10), agora substituído pela biblioteca cliente Azure.Search.Doc(versão 11). Se tiver soluções de pesquisa existentes que utilizem as bibliotecas Microsoft.Azure.Search, pode utilizar este quickstart para aprender sobre essas APIs. 

Para novas soluções, recomendamos a nova biblioteca Azure.Search.Documents. Para uma introdução, consulte [Quickstart: Crie um índice de pesquisa utilizando Azure.Search.Docbiblioteca de uments](search-get-started-dotnet.md).

## <a name="about-this-quickstart"></a>Sobre este arranque rápido

Crie uma aplicação de consola .NET Core em C# que cria, carrega e consulta um índice de Pesquisa Cognitiva Azure utilizando o Visual Studio e as [bibliotecas de clientes Microsoft.Azure.Search](/dotnet/api/overview/azure/search/client10?view=azure-dotnet). 

Este artigo explica como criar a aplicação. Também pode [descarregar e executar a aplicação completa.](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v10)

> [!NOTE]
> O código de demonstração deste artigo utiliza os métodos sincronizados da versão 10 .NET SDK para simplicidade. No entanto, para cenários de produção, recomendamos a utilização dos métodos assíncronos nas suas próprias aplicações para mantê-los escaláveis e responsivos. Por exemplo, pode usar `CreateAsync` e em vez de `DeleteAsync` `Create` `Delete` e. .

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deve ter o seguinte:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/)

+ Um serviço de Pesquisa Cognitiva Azure. [Crie um serviço](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Pode utilizar um serviço gratuito para este arranque rápido. 

+ [Estúdio Visual,](https://visualstudio.microsoft.com/downloads/)qualquer edição. O código de amostra e as instruções foram testados na edição comunitária gratuita.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Obtenha uma chave e URL

As chamadas para o serviço requerem um ponto final URL e uma chave de acesso em cada pedido. Um serviço de pesquisa é criado com ambos, por isso, se adicionar Azure Cognitive Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure,](https://portal.azure.com/)e na página **geral do** seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

2. Em **Definições**  >  **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio, caso precise de rolar uma. Pode utilizar a tecla primária ou secundária nos pedidos de adição, modificação e eliminação de objetos.

   Pegue a chave de consulta também. É uma boa prática emitir pedidos de consulta com acesso apenas de leitura.

![Obtenha uma chave de acesso http e acesso](media/search-get-started-postman/get-url-key.png "Obtenha uma chave de acesso http e acesso")

Todos os pedidos requerem uma chave API em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="set-up-your-environment"></a>Configurar o ambiente

Comece por abrir o Visual Studio e criar um novo projeto de App de Consola que pode funcionar em .NET Core.

### <a name="install-nuget-packages"></a>Instalar pacotes NuGet

O [pacote Microsoft.Azure.Search](https://www.nuget.org/packages/Microsoft.Azure.Search/) é composto por algumas bibliotecas de clientes que são distribuídas como pacotes NuGet.

Para este projeto, utilize a versão 10 do `Microsoft.Azure.Search` pacote NuGet e o mais recente `Microsoft.Extensions.Configuration.Json` pacote NuGet.

1. In **Tools**  >  **NuGet Package Manager**, selecione Gerir **pacotes nuget para solução...**. 

1. Clique em **Browse** (Procurar).

1. Procure `Microsoft.Azure.Search` e selecione a versão 10.

1. Clique em **Instalar** no direito de adicionar o conjunto ao seu projeto e solução.

1. Repita para `Microsoft.Extensions.Configuration.Json` , selecionar a versão 2.2.0 ou posterior.


### <a name="add-azure-cognitive-search-service-information"></a>Adicionar informações do serviço de pesquisa cognitiva Azure

1. No Solution Explorer, clique no projeto e **selecione Add**  >  **New Item...** . 

1. Em Add New Item, procure por "JSON" para devolver uma lista de tipos de artigos relacionados com JSON.

1. Escolha **o Ficheiro JSON,** nomeie o ficheiro "appsettings.jsligado", e clique em **Adicionar**. 

1. Adicione o ficheiro ao seu diretório de saída. Clique appsettings.jsà direita e selecione **Propriedades.** No **Copy to Output Directory**, selecione Copy if **newer**.

1. Copie o seguinte JSON no seu novo ficheiro JSON. 

    ```json
    {
      "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
      "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
      "SearchIndexName": "hotels-quickstart"
    }
    ```

1. Substitua o nome do serviço de pesquisa (O SEU NOME DE SERVIÇO DE BUSCA) e a tecla API de administração (YOUR-ADMIN-API-KEY) por valores válidos. Se o seu ponto final de serviço `https://mydemo.search.windows.net` for, o nome de serviço será `mydemo` " ".

### <a name="add-class-method-files-to-your-project"></a>Adicionar classe ". Method" ficheiros para o seu projeto

Este passo é necessário para produzir uma saída significativa na consola. Ao imprimir os resultados para a janela da consola, os campos individuais do objeto Hotel devem ser devolvidos como cordas. Este passo implementa [o ToString para](/dotnet/api/system.object.tostring?view=netframework-4.8) executar esta tarefa, o que faz copiando o código necessário para dois novos ficheiros.

1. Adicione duas definições de classe vazias ao seu projeto: Address.Methods.cs, Hotel.Methods.cs

1. Em Address.Methods.cs, substitua o conteúdo predefinido com o seguinte código, [linhas 1-25](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/quickstart/v10/AzureSearchQuickstart/Address.Methods.cs#L1-L25).

1. Em Hotel.Methods.cs, copiar [linhas 1-68](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/quickstart/v10/AzureSearchQuickstart/Hotel.Methods.cs#L1-L68).

## <a name="1---create-index"></a>1 - Criar índice

O índice de hotéis é composto por campos simples e complexos, onde um campo simples é "HotelName" ou "Descrição", e campos complexos são um endereço com subcampos, ou uma coleção de quartos. Quando um índice inclui tipos complexos, isole as definições complexas de campo em classes separadas.

1. Adicione duas definições de classe vazias ao seu projeto: Address.cs, Hotel.cs

1. Em Address.cs, substitua o conteúdo predefinido com o seguinte código:

    ```csharp
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace AzureSearchQuickstart
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

1. Em Hotel.cs, a classe define a estrutura global do índice, incluindo referências à classe de endereço.

    ```csharp
    namespace AzureSearchQuickstart
    {
        using System;
        using Microsoft.Azure.Search;
        using Microsoft.Azure.Search.Models;
        using Newtonsoft.Json;

        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrLucene)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Category { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public bool? ParkingIncluded { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }
        }
    }
    ```

    Os atributos no campo determinam como é usado numa aplicação. Para melhor, todos os casos, é a sua mensagem na pesquisa por `IsSearchable` texto. Pode pesquisar por palavras ou frases ou frases ou frases ou frases ou frases ou frases ou frases ou frases ou frases ou frases da semana. 
    
    > [!NOTE]
    > No .NET SDK, os campos devem ser explicitamente atribuídos como [`IsSearchable`](/dotnet/api/microsoft.azure.search.models.field.issearchable?view=azure-dotnet) [`IsFilterable`](/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) , e [`IsSortable`](/dotnet/api/microsoft.azure.search.models.field.issortable?view=azure-dotnet) [`IsFacetable`](/dotnet/api/microsoft.azure.search.models.field.isfacetable?view=azure-dotnet) . Este comportamento contrasta com a API REST que permite implicitamente a atribuição com base no tipo de dados (por exemplo, os campos de cordas simples são automaticamente pesquisáveis).

    Exatamente um campo no seu índice de tipo `string` deve ser o campo *chave,* identificando exclusivamente cada documento. Neste esquema, a chave `HotelId` é.

    Neste índice, os campos de descrição utilizam a [`analyzer`](/dotnet/api/microsoft.azure.search.models.field.analyzer?view=azure-dotnet) propriedade opcional, especificada quando pretende anular o analisador padrão padrão Lucene. O `description_fr` campo está a usar o analisador francês Lucene[(FrLucene)](/dotnet/api/microsoft.azure.search.models.analyzername.frlucene?view=azure-dotnet)porque armazena texto francês. Está `description` a utilizar o analisador de idiomas opcional da Microsoft[(EnMicrosoft).](/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft?view=azure-dotnet)

1. Em Program.cs, crie uma instância da [`SearchServiceClient`](/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) classe para ligar ao serviço, utilizando valores que são armazenados no ficheiro config da aplicação (appsettings.jsligado). 

   `SearchServiceClient` tem uma [`Indexes`](/dotnet/api/microsoft.azure.search.searchserviceclient.indexes?view=azure-dotnet) propriedade, fornecendo todos os métodos necessários para criar, listar, atualizar ou eliminar índices de Pesquisa Cognitiva Azure. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program {
            // Demonstrates index delete, create, load, and query
            // Commented-out code is uncommented in later steps
            static void Main(string[] args)
            {
                IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
                IConfigurationRoot configuration = builder.Build();

                SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

                string indexName = configuration["SearchIndexName"];

                Console.WriteLine("{0}", "Deleting index...\n");
                DeleteIndexIfExists(indexName, serviceClient);

                Console.WriteLine("{0}", "Creating index...\n");
                CreateIndex(indexName, serviceClient);

                // Uncomment next 3 lines in "2 - Load documents"
                // ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
                // Console.WriteLine("{0}", "Uploading documents...\n");
                // UploadDocuments(indexClient);

                // Uncomment next 2 lines in "3 - Search an index"
                // Console.WriteLine("{0}", "Searching index...\n");
                // RunQueries(indexClient);

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            // Create the search service client
            private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
            {
                string searchServiceName = configuration["SearchServiceName"];
                string adminApiKey = configuration["SearchServiceAdminApiKey"];

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
                return serviceClient;
            }

            // Delete an existing index to reuse its name
            private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists(indexName))
                {
                    serviceClient.Indexes.Delete(indexName);
                }
            }

            // Create an index whose fields correspond to the properties of the Hotel class.
            // The Address property of Hotel will be modeled as a complex field.
            // The properties of the Address class in turn correspond to sub-fields of the Address complex field.
            // The fields of the index are defined by calling the FieldBuilder.BuildForType() method.
            private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
            {
                var definition = new Microsoft.Azure.Search.Models.Index()
                {
                    Name = indexName,
                    Fields = FieldBuilder.BuildForType<Hotel>()
                };

                serviceClient.Indexes.Create(definition);
            }
        }
    }    
    ```

    Se possível, partilhe uma única instância da `SearchServiceClient` sua aplicação para evitar a abertura de demasiadas ligações. Os métodos de classe são seguros para permitir tal partilha.

   A classe tem vários construtores. O pretendido recebe o nome do serviço de pesquisa e um objeto `SearchCredentials` como parâmetros. `SearchCredentials` molda a sua chave de API.

    Na definição de índice, a maneira mais fácil de criar os `Field` objetos é chamando o `FieldBuilder.BuildForType` método, passando uma classe modelo para o parâmetro do tipo. Uma classe de modelo tem propriedades que mapeiam os campos do seu índice. Este mapeamento permite-lhe ligar documentos do seu índice de pesquisa a instâncias da sua classe de modelo.

    > [!NOTE]
    > Se não pretender utilizar uma classe de modelo, ainda pode definir o índice ao criar `Field` objetos diretamente. Pode fornecer o nome do campo ao construtor, juntamente com o tipo de dados (ou analisador para os campos de cadeia). Também pode definir outras propriedades `IsSearchable` como, `IsFilterable` para citar alguns.
    >

1. Prima F5 para construir a app e criar o índice. 

    Se o projeto for construído com sucesso, abre-se uma janela de consola, escrevendo mensagens de estado para o ecrã para eliminar e criar o índice. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Documentos de carga

Na Pesquisa Cognitiva Azure, os documentos são estruturas de dados que são entradas para indexação e saídas de consultas. Tal como obtido a partir de uma fonte de dados externa, as entradas de documentos podem ser linhas numa base de dados, bolhas no armazenamento blob ou documentos JSON no disco. Neste exemplo, estamos pegando um atalho e incorporando documentos JSON para quatro hotéis no próprio código. 

Ao carregar documentos, deve utilizar um [`IndexBatch`](/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) objeto. Um `IndexBatch` contém uma coleção de [`IndexAction`](/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) objetos, cada um dos quais contém um documento e uma propriedade que diz à Azure Cognitive Search que ação a executar ([upload, merge, delete e mergeOrUpload).](search-what-is-data-import.md#indexing-actions)

1. Em Program.cs, crie uma série de documentos e ações de índice, e depois passe a matriz para `IndexBatch` . Os documentos abaixo estão em conformidade com o índice hotel-quickstart, tal como definido pelas classes de hotel e endereço.

    ```csharp
    // Upload documents as a batch
    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var actions = new IndexAction<Hotel>[]
        {
            IndexAction.Upload(
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
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate =  new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
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
                }
            ),
            IndexAction.Upload(
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
                    Rating = 4.6,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                }
            ),
        };

        var batch = IndexBatch.New(actions);

        try
        {
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // When a service is under load, indexing might fail for some documents in the batch. 
            // Depending on your application, you can compensate by delaying and retrying. 
            // For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait 2 seconds before starting queries 
        Console.WriteLine("Waiting for indexing...\n");
        Thread.Sleep(2000);
    }
    ```

    Uma vez inicializando o `IndexBatch` objeto, pode enviá-lo para o índice chamando [`Documents.Index`](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) o seu [`SearchIndexClient`](/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) objeto. `Documents` é uma propriedade `SearchIndexClient` que fornece métodos para adicionar, modificar, eliminar ou consultar documentos no seu índice.

    A `try` / `catch` chamada ao `Index` método apanha falhas de indexação, o que pode acontecer se o seu serviço estiver sob carga pesada. No código de produção, pode atrasar e, em seguida, voltar a tentar indexar os documentos que falharam, ou registar e continuar como a amostra faz, ou manuseá-lo de outra forma que satisfaça os requisitos de consistência de dados da sua aplicação.

    O atraso de 2 segundos compensa a indexação, que é assíncronia, para que todos os documentos possam ser indexados antes de as consultas serem executadas. A codificação num atraso é normalmente apenas necessária em demos, testes e aplicações de amostra.

1. Em Program.cs, em geral, descompromes as linhas para "2 - Carregar documentos". 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Prima F5 para reconstruir a aplicação. 

    Se o projeto for construído com sucesso, abre-se uma janela de consola, escrevendo mensagens de estado, desta vez com uma mensagem sobre o upload de documentos. No portal Azure, na página **overview** do serviço de pesquisa, o índice hotéis-quickstart deverá passar a ter 4 documentos.

Para obter mais informações sobre o processamento de [documentos, consulte "Como o .NET SDK lida com documentos".](search-howto-dotnet-sdk.md#how-dotnet-handles-documents)

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Pode obter resultados de consulta assim que o primeiro documento estiver indexado, mas os testes reais do seu índice devem aguardar até que todos os documentos estejam indexados. 

Esta secção adiciona duas peças de funcionalidade: lógica de consulta e resultados. Para consultas, utilize o [`Search`](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search?view=azure-dotnet
) método. Este método requer texto de pesquisa, bem como [outros parâmetros.](/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet) 

A [`DocumentsSearchResult`](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1?view=azure-dotnet) classe representa os resultados.


1. Em Program.cs, crie um método WriteDocuments que imprime os resultados de pesquisa para a consola.

    ```csharp
    private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.Results)
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. Crie um método RunQueries para executar consultas e obter resultados de retorno. Os resultados são objetos do hotel. Pode utilizar o parâmetro selecionado para superfície de campos individuais. Se um campo não estiver incluído no parâmetro selecionado, a propriedade correspondente do Hotel será nula.

    ```csharp
    private static void RunQueries(ISearchIndexClient indexClient)
    {
        SearchParameters parameters;
        DocumentSearchResult<Hotel> results;

        // Query 1 
        Console.WriteLine("Query 1: Search for term 'Atlanta' with no result trimming");
        parameters = new SearchParameters();
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 2
        Console.WriteLine("Query 2: Search on the term 'Atlanta', with trimming");
        Console.WriteLine("Returning only these fields: HotelName, Tags, Address:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Tags", "Address" },
            };
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 3
        Console.WriteLine("Query 3: Search for the terms 'restaurant' and 'wifi'");
        Console.WriteLine("Return only these fields: HotelName, Description, and Tags:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Description", "Tags" }
            };
        results = indexClient.Documents.Search<Hotel>("restaurant, wifi", parameters);
        WriteDocuments(results);

        // Query 4 -filtered query
        Console.WriteLine("Query 4: Filter on ratings greater than 4");
        Console.WriteLine("Returning only these fields: HotelName, Rating:\n");
        parameters =
            new SearchParameters()
            {
                Filter = "Rating gt 4",
                Select = new[] { "HotelName", "Rating" }
            };
        results = indexClient.Documents.Search<Hotel>("*", parameters);
        WriteDocuments(results);

        // Query 5 - top 2 results
        Console.WriteLine("Query 5: Search on term 'boutique'");
        Console.WriteLine("Sort by rating in descending order, taking the top two results");
        Console.WriteLine("Returning only these fields: HotelId, HotelName, Category, Rating:\n");
        parameters =
            new SearchParameters()
            {
                OrderBy = new[] { "Rating desc" },
                Select = new[] { "HotelId", "HotelName", "Category", "Rating" },
                Top = 2
            };
        results = indexClient.Documents.Search<Hotel>("boutique", parameters);
        WriteDocuments(results);
    }
    ```

    Existem [duas formas de combinar termos numa consulta:](search-query-overview.md#types-of-queries)pesquisa de texto completo e filtros. Uma consulta de pesquisa por texto completo procura um ou mais termos nos `IsSearchable` campos do seu índice. Um filtro é uma expressão booleana que é avaliada sobre `IsFilterable` campos em um índice. Pode utilizar a pesquisa por texto completo e os filtros juntos ou separadamente.

    As pesquisas e os filtros são efetuados utilizando o método `Documents.Search`. Uma consulta de pesquisa pode ser transmitida no parâmetro `searchText`, enquanto uma expressão de filtro pode ser transmitida na propriedade `Filter` da classe `SearchParameters`. Para filtrar sem pesquisar, basta passar `"*"` para o parâmetro `searchText` . Para pesquisar sem filtrar, basta deixar a propriedade `Filter` por definir ou não transmitir numa instância `SearchParameters` de todo.

1. Em Program.cs, no essencial, descomprometam as linhas para "3 - Pesquisa". 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. A solução está agora terminada. Prima F5 para reconstruir a app e executar o programa na sua totalidade. 

    A saída inclui as mesmas mensagens de antes, com adição de informações e resultados de consulta.

## <a name="clean-up-resources"></a>Limpar os recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **de todos os recursos** ou **grupos** de recursos no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para ficar abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Neste início rápido C#, trabalhou através de uma série de tarefas para criar um índice, carregá-lo com documentos e executar consultas. Em diferentes fases, tomamos atalhos para simplificar o código de legibilidade e compreensão. Se se sentir confortável com os conceitos básicos, recomendamos o próximo artigo para uma exploração de abordagens e conceitos alternativos que irão aprofundar o seu conhecimento. 

O código de amostra e o índice são versões expandidas desta. A próxima amostra adiciona uma coleção de quartos, usa diferentes classes e ações, e dá uma olhada mais atenta no funcionamento do processamento.

> [!div class="nextstepaction"]
> [Como desenvolver em .NET](search-howto-dotnet-sdk.md)

Quer otimizar e economizar nos gastos na nuvem?

> [!div class="nextstepaction"]
> [Comece a analisar custos com a Gestão de Custos](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)