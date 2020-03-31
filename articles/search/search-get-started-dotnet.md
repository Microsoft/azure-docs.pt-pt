---
title: 'Quickstart: Criar um índice de pesquisa em C# usando .NET'
titleSuffix: Azure Cognitive Search
description: Neste C# quickstart, aprenda a criar um índice, dados de carga e executar consultas usando o Azure Cognitive Search .NET SDK.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 3d0006a3c77050c1bb21a0da8d6be51e659f933d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77589220"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-c-using-the-net-sdk"></a>Quickstart: Criar um índice de pesquisa cognitiva azure em C# usando o .NET SDK
> [!div class="op_single_selector"]
> * [C #](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Pitão](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)
>*

Crie uma aplicação de consola .NET Core C# que crie, carregue e questione um índice de pesquisa cognitiva Azure utilizando o Visual Studio e o [Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk). Este artigo explica como criar a aplicação passo a passo. Em alternativa, pode [descarregar e executar a aplicação completa.](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/Quickstart)

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> O código de demonstração deste artigo utiliza os métodos sincronizados da Pesquisa Cognitiva Azure .NET SDK para a simplicidade. No entanto, para cenários de produção, recomendamos a utilização dos métodos assíncronos nas suas próprias aplicações para os manter escaláveis e responsivos. Por exemplo, poderia `CreateAsync` `DeleteAsync` usar `Create` e `Delete`em vez de e .

## <a name="prerequisites"></a>Pré-requisitos

São necessários os seguintes serviços e ferramentas para este arranque rápido.

+ [Estúdio Visual,](https://visualstudio.microsoft.com/downloads/)qualquer edição. O código de amostra e as instruções foram testados na edição comunitária gratuita.

+ [Crie um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Pode utilizar um serviço gratuito para este arranque rápido.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Obtenha uma chave e URL

As chamadas para o serviço requerem um ponto final url e uma chave de acesso em cada pedido. Um serviço de pesquisa é criado com ambos, por isso, se você adicionar Pesquisa Cognitiva Azure à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure](https://portal.azure.com/), e na página de **visão geral** do seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

2. Em **Definições** > **Keys,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio no caso de precisar de rolar uma. Pode utilizar a chave primária ou secundária nos pedidos de adição, modificação e aparas de objetos.

   Pegue a chave de consulta também. É uma boa prática emitir pedidos de consulta com acesso só para leitura.

![Obtenha um ponto final http e chave de acesso](media/search-get-started-postman/get-url-key.png "Obtenha um ponto final http e chave de acesso")

Todos os pedidos requerem uma chave de api em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="set-up-your-environment"></a>Configurar o ambiente

Comece por abrir o Visual Studio e criar um novo projeto de App console que possa funcionar em .NET Core.

### <a name="install-nuget-packages"></a>Instalar pacotes NuGet

O [Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk) é composto por algumas bibliotecas de clientes que são distribuídas como pacotes NuGet.

Para este projeto, utilize `Microsoft.Azure.Search` a versão 9 `Microsoft.Extensions.Configuration.Json` do pacote NuGet e o mais recente pacote NuGet.

1. Nas **ferramentas** > **NuGet Package Manager,** selecione Gerir **pacotes NuGet para solução...**. 

1. Clique em **Browse** (Procurar).

1. Procure `Microsoft.Azure.Search` e selecione a versão 9.0.1 ou posterior.

1. Clique em **Instalar** à direita para adicionar o conjunto ao seu projeto e solução.

1. Repita `Microsoft.Extensions.Configuration.Json`para , selecionando a versão 2.2.0 ou posterior.


### <a name="add-azure-cognitive-search-service-information"></a>Adicione informações do serviço de pesquisa cognitiva Azure

1. No Solution Explorer, clique no projeto e selecione **Adicionar** > **Novo Item...** . 

1. Em Adicionar Novo Item, procure "JSON" para devolver uma lista de tipos de itens relacionados com a JSON.

1. Escolha **o Ficheiro JSON,** nomeie o ficheiro "appsettings.json", e clique em **Adicionar**. 

1. Adicione o ficheiro ao seu diretório de saída. Clique direito nas definições.json e selecione **Propriedades**. Em **Copiar para O Diretório**de Saída, selecione Copiar se for mais **recente**.

1. Copie o seguinte JSON no seu novo ficheiro JSON. Substitua o nome do serviço de pesquisa (YOUR-SEARCH-SERVICE-NAME) e a chave API de administrador (YOUR-ADMIN-API-KEY) por valores válidos. Se o seu `https://mydemo.search.windows.net`ponto final de serviço for , o nome do serviço seria "mydemo".

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "hotels-quickstart"
}
```

### <a name="add-class-method-files-to-your-project"></a>Adicione classe". Método" ficheiros para o seu projeto

Ao imprimir os resultados para a janela da consola, os campos individuais do objeto do Hotel devem ser devolvidos como cordas. Pode implementar o [ToString()](https://docs.microsoft.com/dotnet/api/system.object.tostring?view=netframework-4.8) para executar esta tarefa, copiando o código necessário para dois novos ficheiros.

1. Adicione duas definições de classe vazias ao seu projeto: Address.Methods.cs, Hotel.Methods.cs

1. Em Address.Methods.cs, substitui o conteúdo predefinido com o seguinte código, [linhas 1-32](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Address.Methods.cs/#L1-L32).

1. Em Hotel.Methods.cs, copiar [linhas 1-66](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Hotel.Methods.cs/#L1-L66).


## <a name="1---create-index"></a>1 - Criar índice

O índice de hotéis é composto por campos simples e complexos, onde um campo simples é "HotelName" ou "Descrição", e campos complexos são um endereço com subcampos, ou uma coleção de quartos. Quando um índice inclui tipos complexos, isola as definições complexas de campo em classes separadas.

1. Adicione duas definições de classe vazias ao seu projeto: Address.cs, Hotel.cs

1. Em Address.cs, repor o conteúdo predefinido com o seguinte código:

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

1. Em Hotel.cs, a classe define a estrutura global do índice, incluindo referências à classe de endereços.

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

    Os atributos no campo determinam como é usado numa aplicação. Por exemplo, `IsSearchable` o atributo deve ser atribuído a todos os campos que devem ser incluídos numa pesquisa completa de texto. 
    
    > [!NOTE]
    > No SDK .NET, os campos devem [`IsSearchable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issearchable?view=azure-dotnet)ser [`IsFilterable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) [`IsSortable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issortable?view=azure-dotnet)explicitamente atribuídos como, , e [`IsFacetable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfacetable?view=azure-dotnet). Este comportamento contrasta com a API REST que implicitamente permite a atribuição com base no tipo de dados (por exemplo, os simples campos de cordas são automaticamente pesquisáveis).

    Exatamente um campo no `string` seu índice de tipo deve ser o campo *chave,* identificando exclusivamente cada documento. Neste esquema, a chave `HotelId`é.

    Neste índice, os campos de [`analyzer`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.analyzer?view=azure-dotnet) descrição utilizam a propriedade opcional, especificada quando pretende anular o analisador lucene padrão padrão padrão padrão. O `description_fr` campo está a usar o analisador lucene francês[(FrLucene)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.frlucene?view=azure-dotnet)porque armazena texto francês. O `description` está a utilizar o analisador de idiomas opcional da Microsoft[(EnMicrosoft).](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft?view=azure-dotnet)

1. Em Program.cs, crie [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) uma instância da classe para se conectar ao serviço, utilizando valores que são armazenados no ficheiro config da aplicação (appsettings.json). 

   `SearchServiceClient`tem [`Indexes`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexes?view=azure-dotnet) uma propriedade, fornecendo todos os métodos necessários para criar, listar, atualizar ou eliminar índices de Pesquisa Cognitiva Azure. 

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
                var definition = new Index()
                {
                    Name = indexName,
                    Fields = FieldBuilder.BuildForType<Hotel>()
                };

                serviceClient.Indexes.Create(definition);
            }
        }
    }    
    ```

    Se possível, partilhe `SearchServiceClient` uma única instância na sua aplicação para evitar abrir demasiadas ligações. Os métodos de classe são seguros para permitir essa partilha.

   A classe tem vários construtores. O pretendido recebe o nome do serviço de pesquisa e um objeto `SearchCredentials` como parâmetros. `SearchCredentials` molda a sua chave de API.

    Na definição de índice, a `Field` maneira mais `FieldBuilder.BuildForType` fácil de criar os objetos é chamando o método, passando uma classe modelo para o parâmetro do tipo. Uma classe de modelo tem propriedades que mapeiam os campos do seu índice. Este mapeamento permite-lhe ligar documentos do seu índice de pesquisa a instâncias da sua classe modelo.

    > [!NOTE]
    > Se não pretender utilizar uma classe de modelo, ainda pode definir o índice ao criar `Field` objetos diretamente. Pode fornecer o nome do campo ao construtor, juntamente com o tipo de dados (ou analisador para os campos de cadeia). Você também pode definir `IsSearchable` `IsFilterable`outras propriedades como, para citar algumas.
    >

1. Pressione F5 para construir a app e criar o índice. 

    Se o projeto for construído com sucesso, abre-se uma janela de consola, escrevendo mensagens de estado para o ecrã para apagar e criar o índice. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Carregar documentos

Na Pesquisa Cognitiva Azure, os documentos são estruturas de dados que são tanto inputs para indexação como saídas de consultas. Conforme obtido a partir de uma fonte externa de dados, as inputs de documentos podem ser linhas numa base de dados, bolhas no armazenamento blob ou documentos JSON no disco. Neste exemplo, estamos a fazer um atalho e a incorporar documentos da JSON para quatro hotéis no próprio código. 

Ao fazer o upload de [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) documentos, deve utilizar um objeto. Uma `IndexBatch` coleção de [`IndexAction`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) objetos, cada um dos quais contém um documento e uma propriedade que diz à Azure Cognitive Search que ação executar[(carregar, fundir, excluir e fundirOrUpload).](search-what-is-data-import.md#indexing-actions)

1. Em Program.cs, crie uma série de documentos e `IndexBatch`ações de índice, e depois passe a matriz para . Os documentos abaixo estão em conformidade com o índice hotel-quickstart, conforme definido pelas aulas de hotel e morada.

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

    Assim que rubricar o`IndexBatch` objeto, pode enviá-lo para o índice chamando [`Documents.Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) o seu [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) objeto. `Documents`é uma `SearchIndexClient` propriedade que fornece métodos para adicionar, modificar, apagar ou consultar documentos no seu índice.

    `try` / O `catch` que rodeia `Index` a chamada para o método apanha falhas de indexação, o que pode acontecer se o seu serviço estiver sob carga pesada. No código de produção, pode atrasar e, em seguida, voltar a indexar os documentos que falharam, ou registar e continuar como a amostra faz, ou manuseá-lo de outra forma que satisfaça os requisitos de consistência de dados da sua aplicação.

    O atraso de 2 segundos compensa a indexação, que é assíncrona, para que todos os documentos possam ser indexados antes de as consultas serem executadas. A codificação em atraso é normalmente necessária apenas em demonstrações, testes e aplicações de amostra.

1. Em Program.cs, em primeiro momento, não comentar as linhas para "2 - Carregar documentos". 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Pressione F5 para reconstruir a aplicação. 

    Se o projeto for construído com sucesso, abre-se uma janela de consola, escrevendo mensagens de estado, desta vez com uma mensagem sobre o upload de documentos. No portal Azure, na página de visão **geral** do serviço de pesquisa, o índice hotéis-quickstart deverá agora ter 4 documentos.

Para obter mais informações sobre o processamento de [documentos, consulte "Como o SDK .NET lida com documentos".](search-howto-dotnet-sdk.md#how-dotnet-handles-documents)

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Pode obter resultados de consulta assim que o primeiro documento estiver indexado, mas os testes reais do seu índice devem esperar até que todos os documentos estejam indexados. 

Esta secção adiciona duas peças de funcionalidade: lógica de consulta e resultados. Para consultas, utilize [`Search`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search?view=azure-dotnet
) o método. Este método requer texto de pesquisa, bem como outros [parâmetros](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet). 

A [`DocumentsSearchResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1?view=azure-dotnet) classe representa os resultados.


1. Em Program.cs, crie um método WriteDocuments que imprima os resultados da pesquisa para a consola.

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

1. Crie um método RunQueries para executar consultas e resultados de retorno. Os resultados são objetos do hotel. Pode utilizar o parâmetro select o que se pode utilizar para superfície de campos individuais. Se um campo não estiver incluído no parâmetro selecionado, a sua propriedade hoteleira correspondente será nula.

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

    Existem duas [formas de combinar termos numa consulta:](search-query-overview.md#types-of-queries)pesquisa de texto completo e filtros. Uma consulta de pesquisa de texto completo procura `IsSearchable` um ou mais termos em campos do seu índice. Um filtro é uma expressão booleana que é avaliada sobre `IsFilterable` campos em um índice. Pode utilizar pesquisas de texto completo e filtros em conjunto ou separadamente.

    As pesquisas e os filtros são efetuados utilizando o método `Documents.Search`. Uma consulta de pesquisa pode ser transmitida no parâmetro `searchText`, enquanto uma expressão de filtro pode ser transmitida na propriedade `Filter` da classe `SearchParameters`. Para filtrar sem pesquisar, basta passar `"*"` para o parâmetro `searchText` . Para pesquisar sem filtrar, basta deixar a propriedade `Filter` por definir ou não transmitir numa instância `SearchParameters` de todo.

1. Em Program.cs, em principal parte, não comentar as linhas para "3 - Procurar". 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. A solução está agora terminada. Pressione f5 para reconstruir a app e executar o programa na sua totalidade. 

    A saída inclui as mesmas mensagens que antes, com adição de informações de consulta e resultados.

## <a name="clean-up-resources"></a>Limpar recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **De Todos os recursos** ou **grupos de Recursos** no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se de que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para se manter abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Neste C# quickstart, trabalhou através de uma série de tarefas para criar um índice, carregá-lo com documentos e executar consultas. Em diferentes fases, tomámos atalhos para simplificar o código de legibilidade e compreensão. Se se sentir confortável com os conceitos básicos, recomendamos o próximo artigo para uma exploração de abordagens e conceitos alternativos que aprofundarão os seus conhecimentos. 

O código da amostra e o índice são versões expandidas deste. A próxima amostra adiciona uma coleção de quartos, usa diferentes classes e ações, e dá uma olhada mais atenta no funcionamento do processamento.

> [!div class="nextstepaction"]
> [Como se desenvolver em .NET](search-howto-dotnet-sdk.md)
