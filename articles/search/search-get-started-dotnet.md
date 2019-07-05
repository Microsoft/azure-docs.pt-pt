---
title: 'C#Início rápido: Criar, carregar e consultar índices com o SDK de .NET - Azure Search'
description: Explica como criar um índice, carregar dados e executar consultas com o C# e o SDK .NET da Azure Search.
author: heidisteen
manager: cgronlun
ms.author: heidist
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/20/2019
ms.openlocfilehash: a5cbd2036f92c27709d92d0cf415cc9837645fb8
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485606"
---
# <a name="quickstart-create-an-azure-search-index-in-c-using-the-net-sdk"></a>Início rápido: Criar um índice da Azure Search no C# utilizando o SDK .NET
> [!div class="op_single_selector"]
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)
>*

Criar um .NET Core C# consola de aplicação que cria, carrega e consulta o índice da Azure Search com o Visual Studio e o [SDK .NET da Azure Search](https://aka.ms/search-sdk). Este artigo explica como criar o aplicativo passo a passo. Em alternativa, pode [transferir e executar a aplicação completa](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/Quickstart).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> O código de demonstração neste artigo utiliza os métodos síncronos do SDK de .NET de pesquisa do Azure para manter a simplicidade. No entanto, para cenários de produção, recomendamos que utilize os métodos assíncronos em seus próprios aplicativos para mantê-las escaláveis e reativas. Por exemplo, poderia usar `CreateAsync` e `DeleteAsync` em vez de `Create` e `Delete`.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes serviços, ferramentas e dados são utilizados neste início rápido. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), qualquer edição. Código de exemplo e instruções foram testadas na edição de Comunidade gratuita.

+ Índice de exemplo e os documentos são fornecidos neste artigo, bem como no [solução do Visual Studio](https://github.com/Azure-Samples/azure-search-dotnet-samples/quickstart) para este início rápido.

+ [Criar um serviço Azure Search](search-create-service-portal.md) ou [localizar um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na subscrição atual. Pode usar um serviço gratuito para este início rápido.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Obter uma chave e o URL

Chamadas para o serviço requerem um ponto de final do URL e uma chave de acesso em cada solicitação. É criado um serviço de pesquisa com ambos os elementos, pelo que, se tiver adicionado o Azure Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inicie sessão no portal do Azure](https://portal.azure.com/)e no seu serviço de pesquisa **descrição geral** página, obter o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

2. Na **configurações** > **chaves**, obter uma chave de administrador para todos os direitos no serviço. Existem duas chaves de administração intercambiáveis, fornecidas para a continuidade do negócio, caso seja necessário fazer o rollover um. Pode utilizar tanto a chave primária ou secundária em pedidos para adicionar, modificar e eliminar objetos.

   Obtenha também a chave de consulta. É melhor prática para emitir pedidos de consulta com acesso só de leitura.

![Obter uma chave de acesso e de ponto final HTTP](media/search-get-started-postman/get-url-key.png "obter uma chave de acesso e de ponto final HTTP")

Todos os pedidos requerem uma chave de api em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="set-up-your-environment"></a>Configurar o ambiente

Comece abrindo o Visual Studio e criando um novo projeto de aplicação de consola que pode ser executados no .NET Core.

### <a name="install-nuget-packages"></a>Instalar pacotes NuGet

O [SDK .NET da Azure Search](https://aka.ms/search-sdk) consiste em algumas bibliotecas de cliente que são distribuídas como pacotes NuGet.

Para este projeto, utilize a versão 9 dos `Microsoft.Azure.Search` NuGet pacote e a versão mais recente `Microsoft.Extensions.Configuration.Json` pacote NuGet.

1. Na **ferramentas** > **Gestor de pacotes NuGet**, selecione **gerir pacotes NuGet para solução de...** . 

1. Clique em **Browse** (Procurar).

1. Procure `Microsoft.Azure.Search` e selecione a versão 9.0.1 ou posterior.

1. Clique em **instalar** à direita para adicionar o assembly ao seu projeto e a solução.

1. Repita esta operação para `Microsoft.Extensions.Configuration.Json`, selecionando a versão 2.2.0 ou posterior.


### <a name="add-azure-search-service-information"></a>Adicionar informações do serviço Azure Search

1. No Solution Explorer, clique com botão direito no projeto e selecione **Add** > **Novo Item...**  . 

1. No Add New Item, procure "JSON" devolver uma lista relacionados com o JSON de tipos de item.

1. Escolher **ficheiro JSON**, nomeie o arquivo "appSettings" e clique em **Add**. 

1. Adicione o ficheiro para o seu diretório de saída. Com o botão direito appSettings e selecione **propriedades**. Na **Copy to Output Directory**, selecione **copiar se for mais recente**.

1. Copie o JSON seguinte para o novo arquivo JSON. Substitua o nome do serviço de pesquisa (seu-pesquisa-SERVICE-NAME) e a chave de API de administração (seu-ADMIN-API-KEY) com valores válidos. Se o ponto final de serviço é `https://mydemo.search.windows.net`, o nome do serviço seria "mydemo".

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "hotels-quickstart"
}
```

### <a name="add-class-method-files-to-your-project"></a>Adicionar a classe ". Ficheiros de método"ao seu projeto

Ao imprimir os resultados para a janela da consola, tem de voltar o campos individuais do objeto Hotel como cadeias de caracteres. Pode implementar [ToString ()](https://docs.microsoft.com/dotnet/api/system.object.tostring?view=netframework-4.8) para realizar esta tarefa, copiar o código necessário para dois novos ficheiros.

1. Adicione duas definições de classe vazia ao seu projeto: Address.Methods.cs, Hotel.Methods.cs

1. No Address.Methods.cs, substituir o conteúdo de padrão com o código a seguir, [linhas de 1 a 32](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Address.Methods.cs/#L1-L32).

1. No Hotel.Methods.cs, copie [linhas de 1-66](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Hotel.Methods.cs/#L1-L66).


## <a name="1---create-index"></a>1 - criar índice

O índice de hotéis é constituído por campos simples e complexos, onde um campo simples é "HotelName" ou "Descrição" e campos complexos são um endereço com subcampos ou uma coleção de ambientes. Quando um índice inclui tipos complexos, Isole as definições de campo complexas em classes separadas.

1. Adicione duas definições de classe vazia ao seu projeto: Address.cs, Hotel.cs

1. No Address.cs, substitua o conteúdo de padrão com o código a seguir:

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

1. Em Hotel.cs, a classe define a estrutura geral do índice, incluindo referências para a classe de endereço.

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

    Atributos no campo determinam como são utilizadas numa aplicação. Por exemplo, o `IsSearchable` atributo é atribuído a todos os campos que devem ser incluídos numa pesquisa em texto completo. No SDK do .NET, a predefinição é desativar os comportamentos de campo que não sejam habilitados explicitamente.

    Exatamente um campo no seu índice do tipo `string` tem de ser o *chave* campo, identificando exclusivamente cada documento. Neste esquema, a chave é `HotelId`.

    Este índice, os campos de descrição utilizam a propriedade de analisador opcional, especificada quando pretende substituir o analisador de Lucene padrão do padrão. O `description_fr` campo está a utilizar o analisador de Lucene francês ([FrLucene](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.frlucene?view=azure-dotnet)) porque ele armazena o texto em francês. O `description` está a utilizar o analisador de idioma Microsoft opcional ([EnMicrosoft](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft?view=azure-dotnet)).

1. Em Program.cs, crie uma instância do [ `SearchServiceClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) classe para ligar ao serviço, utilizando os valores que estão armazenados no arquivo de configuração do aplicativo (appSettings). 

   `SearchServiceClient` tem um [ `Indexes` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexes?view=azure-dotnet) propriedade, fornecendo a todos os métodos necessários para criar, listar, atualizar ou eliminar índices da Azure Search. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program
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

    Se possível, partilhar uma única instância do `SearchServiceClient` na sua aplicação para evitar abrir demasiadas ligações. Métodos de classe são thread-safe para permitir esta partilha.

   A classe tem vários construtores. O pretendido recebe o nome do serviço de pesquisa e um objeto `SearchCredentials` como parâmetros. `SearchCredentials` molda a sua chave de API.

    Na definição do índice, a maneira mais fácil de criar o `Field` objetos está chamando o `FieldBuilder.BuildForType` método, passando uma classe de modelo para o parâmetro de tipo. Uma classe de modelo tem propriedades que mapeiam os campos do seu índice. Este mapeamento permite-lhe vincular documentos a partir do seu índice de pesquisa para instâncias da sua classe de modelo.

    > [!NOTE]
    > Se não pretender utilizar uma classe de modelo, ainda pode definir o índice ao criar `Field` objetos diretamente. Pode fornecer o nome do campo ao construtor, juntamente com o tipo de dados (ou analisador para os campos de cadeia). Também pode definir outras propriedades, como `IsSearchable`, `IsFilterable`, para citar alguns.
    >

1. Prima F5 para compilar a aplicação e criar o índice. 

    Se o projeto é compilado com êxito, é aberta uma janela de console, escrevendo mensagens de estado para o ecrã para a eliminação e criar o índice. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - carregar documentos

No Azure Search, os documentos são estruturas de dados que são as duas entradas para a indexação e produz das consultas. Tal como obtidas a partir de uma origem de dados externa, entradas de documento poderão ser linhas numa base de dados, os blobs no armazenamento de BLOBs ou documentos JSON no disco. Neste exemplo, estamos pegar um atalho e a incorporação de documentos JSON para quatro hotéis no próprio código. 

Quando o carregamento de documentos, tem de utilizar um [ `IndexBatch` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) objeto. Uma `IndexBatch` contém uma coleção de [ `IndexAction` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) objetos, cada um deles contendo um documento e uma propriedade informando-Azure Search qual a ação a efetuar ([carregar, intercalar, eliminar e mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. Em Program.cs, criar uma matriz de documentos e as ações de índice e, em seguida, transmita a matriz ao `IndexBatch`. Os documentos abaixo estão em conformidade com o índice de início rápido do hotel, conforme definido pelas classes hotel e endereço.

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

    Assim que inicializar o`IndexBatch` objeto, pode enviá-lo para o índice ao chamar [ `Documents.Index` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) no seu [ `SearchIndexClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) objeto. `Documents` é uma propriedade de `SearchIndexClient` que fornece métodos para adicionar, modificar, eliminar ou consultar documentos no seu índice.

    O `try` / `catch` adjacente à chamada para o `Index` dificuldades de método indexação falhas, o que podem acontecer se o seu serviço estiver sobrecarregado. No código de produção, pode atrasar e, em seguida, repita a indexação dos documentos que falharam, ou iniciar sessão e continuam como o exemplo faz ou processá-la de alguma forma que cumpra os requisitos de consistência de dados da sua aplicação.

    O atraso de 2 segundos compensa indexação, que é assíncrona, para que todos os documentos podem ser indexados antes das consultas são executadas. Codificação num atraso, normalmente, só é necessária em demonstrações, testes e aplicativos de exemplo.

1. Em Program.cs, em main, anule os comentários as linhas para "2 - documentos de carga". 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Pressione F5 para reconstruir a aplicação. 

    Se o projeto é compilado com êxito, é aberta uma janela de console, escrever mensagens de estado, desta vez com uma mensagem sobre como carregar documentos. No portal do Azure, no serviço de pesquisa **descrição geral** página, o índice de hotéis-quickstart deve agora ter 4 documentos.

Para obter mais informações sobre o processamento de documentos, consulte ["Como o SDK .NET processa documentos"](search-howto-dotnet-sdk.md#how-dotnet-handles-documents).

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Pode obter os resultados da consulta, assim como o primeiro documento é indexado, mas o teste real do seu índice deve aguardar até que todos os documentos são indexados. 

Esta secção adiciona duas partes de funcionalidades: lógica de consulta e os resultados. Para consultas, utilize o [ `Search` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search?view=azure-dotnet
) método. Este método aceita o texto de pesquisa, bem como outros [parâmetros](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet). 

O [ `DocumentsSearchResult` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1?view=azure-dotnet) classe representa os resultados.


1. Em Program.cs, crie um método de WriteDocuments que imprime os resultados para a consola de pesquisa.

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

1. Crie um método de RunQueries para executar consultas e devolver resultados. Os resultados são objetos de Hotel. Pode utilizar o parâmetro Selecione para superfície campos individuais. Se um campo não está incluído no parâmetro selecione, sua propriedade de Hotel correspondente será nula.

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

    Existem duas [formas de termos correspondentes numa consulta](search-query-overview.md#types-of-queries): pesquisa em texto completo e filtros. Procura de uma consulta de pesquisa em texto completo para um ou mais termos em `IsSearchable` campos no seu índice. Um filtro é uma expressão booleana que é avaliada através de `IsFilterable` campos num índice. Pode utilizar a pesquisa em texto completo e filtros em conjunto ou separadamente.

    As pesquisas e os filtros são efetuados utilizando o método `Documents.Search`. Uma consulta de pesquisa pode ser transmitida no parâmetro `searchText`, enquanto uma expressão de filtro pode ser transmitida na propriedade `Filter` da classe `SearchParameters`. Para filtrar sem pesquisar, basta passar `"*"` para o parâmetro `searchText` . Para pesquisar sem filtrar, basta deixar a propriedade `Filter` por definir ou não transmitir numa instância `SearchParameters` de todo.

1. Em Program.cs, em main, anule os comentários as linhas de "3 – pesquisa". 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. A solução agora está concluída. Pressione F5 para reconstruir a aplicação e execute o programa em sua totalidade. 

    Saída inclui a mesma mensagem que antes, com a adição de informações de consulta e os resultados.

## <a name="clean-up"></a>Limpeza

Quando terminar com um índice e pretender eliminá-lo, chame o `Indexes.Delete` método no seu `SearchServiceClient`.

```csharp
serviceClient.Indexes.Delete("hotels");
```

Se também tiver concluído o serviço de pesquisa, pode eliminar recursos a partir do portal do Azure.

## <a name="next-steps"></a>Passos Seguintes

Neste C# início rápido, trabalhou por uma série de tarefas para criar um índice, carregá-lo com documentos e executar consultas. Em diferentes fases, tivemos de atalhos para simplificar o código para facilitar a leitura e compreensão. Se estiver familiarizado com os conceitos básicos, recomendamos que o artigo seguinte para uma exploração de abordagens alternativas e conceitos que irão aprofundar seu conhecimento. 

O código de exemplo e índice são versões expandidas deste artigo. O exemplo seguinte adiciona uma coleção de ambientes, utiliza diferentes classes e as ações e fará uma análise detalhada de como funciona o processamento.

> [!div class="nextstepaction"]
> [Como programar em .NET](search-howto-dotnet-sdk.md)