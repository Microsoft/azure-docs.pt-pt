---
title: Use pesquisa cognitiva azure em .NET
titleSuffix: Azure Cognitive Search
description: Aprenda a utilizar a Pesquisa Cognitiva Azure numa aplicação .NET utilizando C# e o .NET SDK. As tarefas baseadas em código incluem a ligação ao serviço, o conteúdo do índice e a consulta de um índice.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b31a4e40c1e9095499faf265673ab4213ad6bde0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283072"
---
# <a name="how-to-use-azure-cognitive-search-from-a-net-application"></a>Como utilizar a Pesquisa Cognitiva Azure a partir de uma aplicação .NET

Este artigo é um walkthrough para fazê-lo funcionar com a [Pesquisa Cognitiva Azure .NET SDK](https://aka.ms/search-sdk). Pode utilizar o .NET SDK para implementar uma rica experiência de pesquisa na sua aplicação utilizando a Pesquisa Cognitiva Azure.

## <a name="whats-in-the-azure-cognitive-search-sdk"></a>O que está no SDK de Pesquisa Cognitiva Azure
O SDK é composto por algumas bibliotecas de clientes que lhe permitem gerir os seus índices, fontes de dados, indexadores e mapas de sinónimo, bem como carregar e gerir documentos, e executar consultas, tudo sem ter que lidar com os detalhes de HTTP e JSON. Estas bibliotecas de clientes estão todas distribuídas como pacotes NuGet.

O pacote principal `Microsoft.Azure.Search`nuGet é , que é um meta-pacote que inclui todos os outros pacotes como dependências. Utilize este pacote se estiver apenas a começar ou se souber que a sua aplicação necessitará de todas as funcionalidades da Pesquisa Cognitiva Azure.

Os outros pacotes NuGet no SDK são:
 
  - `Microsoft.Azure.Search.Data`: Utilize este pacote se estiver a desenvolver uma aplicação .NET utilizando a Pesquisa Cognitiva Azure, e só precisa de consultar ou atualizar documentos nos seus índices. Se também precisar de criar ou atualizar índices, mapas sinónimos ou outros `Microsoft.Azure.Search` recursos ao nível do serviço, utilize o pacote em vez disso.
  - `Microsoft.Azure.Search.Service`: Utilize este pacote se estiver a desenvolver automação em .NET para gerir índices de Pesquisa Cognitiva Azure, mapas de sinónimo, indexadores, fontes de dados ou outros recursos ao nível do serviço. Se necessitar apenas de consultar ou atualizar documentos `Microsoft.Azure.Search.Data` nos seus índices, utilize o pacote em vez disso. Se necessitar de toda a funcionalidade de `Microsoft.Azure.Search` Pesquisa Cognitiva Azure, utilize o pacote em vez disso.
  - `Microsoft.Azure.Search.Common`: Tipos comuns necessários pelas bibliotecas Azure Cognitive Search .NET. Não precisa de utilizar este pacote diretamente na sua aplicação. Destina-se apenas a ser usado como uma dependência.

As várias bibliotecas `Index` `Field`de `Document`clientes definem classes `Indexes.Create` `Documents.Search` como, `SearchServiceClient` `SearchIndexClient` e, bem como operações como e nas classes e e classes. Estas aulas são organizadas nos seguintes espaços de nome:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Se quiser fornecer feedback para uma futura atualização do SDK, consulte a nossa página de [feedback](https://feedback.azure.com/forums/263029-azure-search/) ou crie um problema no [GitHub](https://github.com/azure/azure-sdk-for-net/issues) e mencione "Azure Cognitive Search" no título da emissão.

O SDK .NET `2019-05-06` suporta a versão da API de [Pesquisa Cognitiva Azure](https://docs.microsoft.com/rest/api/searchservice/). Esta versão inclui suporte para [tipos complexos](search-howto-complex-data-types.md), enriquecimento de [IA,](cognitive-search-concept-intro.md) [autocompleto,](https://docs.microsoft.com/rest/api/searchservice/autocomplete)e [jsonLines modo de parsing](search-howto-index-json-blobs.md) ao indexar Blobs Azure. 

Este SDK não suporta operações de [gestão](https://docs.microsoft.com/rest/api/searchmanagement/) como a criação e dimensionamento de serviços de pesquisa e a gestão de chaves API. Se necessitar de gerir os seus recursos de Pesquisa a partir de uma aplicação .NET, pode utilizar o [Azure Cognitive Search .NET Management SDK](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Upgrade para a versão mais recente do SDK
Se já está a utilizar uma versão mais antiga do Azure Cognitive Search .NET SDK e gostaria de fazer upgrade para a versão mais recente geralmente disponível, [este artigo](search-dotnet-sdk-migration-version-9.md) explica como.

## <a name="requirements-for-the-sdk"></a>Requisitos para o SDK
1. Estúdio Visual 2017 ou mais tarde.
2. O seu próprio serviço de pesquisa cognitiva Azure. Para utilizar o SDK, necessitará do nome do seu serviço e de uma ou mais teclas API. [Criar um serviço no portal](search-create-service-portal.md) irá ajudá-lo a ultrapassar estes passos.
3. Descarregue o pacote Azure Cognitive Search .NET SDK [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search) utilizando "Manage NuGet Packages" no Estúdio Visual. Basta pesquisar o `Microsoft.Azure.Search` nome do pacote em NuGet.org (ou um dos outros nomes de pacote acima se necessitar apenas de um subconjunto da funcionalidade).

O Azure Cognitive Search .NET SDK suporta aplicações direcionadas para a .NET Framework 4.5.2 e superior, bem como .NET Core 2.0 e superior.

## <a name="core-scenarios"></a>Cenários centrais
Há várias coisas que precisa de fazer no seu pedido de pesquisa. Neste tutorial, cobriremos estes cenários fundamentais:

* Criar um índice
* Povoar o índice com documentos
* Procurar documentos usando pesquisa de texto completo e filtros

O seguinte código de amostra ilustra cada um destes cenários. Sinta-se à vontade para usar os fragmentos de código na sua própria aplicação.

### <a name="overview"></a>Descrição geral
A aplicação de amostra que vamos explorar cria um novo índice chamado "hotéis", povoa-o com alguns documentos, e depois executa algumas consultas de pesquisa. Aqui está o programa principal, mostrando o fluxo geral:

```csharp
// This sample shows how to delete, create, upload documents and query an index
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

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);

    ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

> [!NOTE]
> Pode encontrar o código de origem completo da aplicação de exemplo utilizado nesta apresentação no [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).
> 
>

Vamos passar por este passo a passo. Primeiro, temos de `SearchServiceClient`criar um novo. Este objeto permite-lhe gerir os índices. Para construir um, você precisa fornecer o seu nome de serviço de Pesquisa Cognitiva Azure, bem como uma chave API administradora. Pode introduzir estas informações no `appsettings.json` ficheiro da [aplicação](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)da amostra .

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

> [!NOTE]
> Se fornecer uma tecla incorreta (por exemplo, uma chave de consulta `SearchServiceClient` onde `CloudException` foi necessária uma chave de administração), a será lançada com `Indexes.Create`a mensagem de erro "Forbidden" na primeira vez que chamar um método de operação, como . Se isto lhe acontecer, verifique duas vezes a nossa chave API.
> 
> 

As próximas linhas chamam métodos para criar um índice chamado "hotéis", apagando-o primeiro se já existir. Vamos percorrer estes métodos um pouco mais tarde.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Em seguida, o índice precisa de ser povoado. Para povoar o índice, `SearchIndexClient`vamos precisar de um . Há duas formas de obter uma: construindo-a, ou apelando `Indexes.GetClient` ao `SearchServiceClient`. Usamos este último para conveniência.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> Numa aplicação típica de pesquisa, a gestão de índices e a população podem ser manuseadas por um componente separado das consultas de pesquisa. `Indexes.GetClient`é conveniente para a povoação de um índice porque `SearchCredentials`lhe poupa o trabalho de fornecer adicional . Este é realizado através da transferência da chave de administração que utilizou para criar o `SearchServiceClient` para o novo `SearchIndexClient`. No entanto, na parte da sua aplicação que executa consultas, é melhor criar o `SearchIndexClient` diretamente para que possa passar numa chave de consulta, que só lhe permite ler dados, em vez de uma chave de administração. Este processo é consistente com o princípio do menor privilégio e irá ajudá-lo a tornar a sua aplicação mais segura. Pode saber mais sobre chaves de administração e chaves de consulta [aqui.](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization)
> 
> 

Agora que temos `SearchIndexClient`um, podemos povoar o índice. A população indexada é feita por outro método que iremos percorrer mais tarde.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Finalmente, executamos algumas consultas de pesquisa e exibimos os resultados. Desta vez usamos `SearchIndexClient`um diferente:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

Vamos ver mais de `RunQueries` perto o método mais tarde. Aqui está o código `SearchIndexClient`para criar o novo:

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Desta vez usamos uma chave de consulta, uma vez que não precisamos de acesso escrito ao índice. Pode introduzir estas informações no `appsettings.json` ficheiro da [aplicação](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)da amostra .

Se executar esta aplicação com um nome de serviço válido e teclas API, a saída deve parecer este exemplo: (Alguma saída de consola foi substituída por "..." para fins de ilustração.)

    Deleting index...

    Creating index...

    Uploading documents...

    Waiting for documents to be indexed...

    Search the entire index for the term 'motel' and return only the HotelName field:

    Name: Secret Point Motel

    Name: Twin Dome Motel


    Apply a filter to the index to find hotels with a room cheaper than $100 per night, and return the hotelId and description:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Times Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.

    HotelId: 2
    Description: The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.


    Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

    Name: Triple Landscape Hotel
    Last renovated on: 9/20/2015 12:00:00 AM +00:00

    Name: Twin Dome Motel
    Last renovated on: 2/18/1979 12:00:00 AM +00:00


    Search the hotel names for the term 'hotel':

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

    Complete.  Press any key to end application... 

O código fonte completo do pedido é fornecido no final deste artigo.

Em seguida, vamos analisar mais de perto `Main`cada um dos métodos chamados por .

### <a name="creating-an-index"></a>Criar um índice
Depois de `SearchServiceClient` `Main` criar um , elimina o índice de "hotéis" se já existir. Esta eliminação é feita pelo seguinte método:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Este método utiliza `SearchServiceClient` o dado para verificar se o índice existe e, em caso afirmativo, eliminá-lo.

> [!NOTE]
> O código de exemplo neste artigo utiliza os métodos sincronizados da Pesquisa Cognitiva Azure .NET SDK para a simplicidade. Recomendamos que utilize os métodos assíncronos nas suas próprias aplicações para mantê-las escaláveis e responsivas. Por exemplo, no método acima `ExistsAsync` `DeleteAsync` poderia `Exists` utilizar `Delete`e em vez de e .
> 
> 

Em `Main` seguida, cria um novo índice de "hotéis", chamando este método:

```csharp
private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    
    serviceClient.Indexes.Create(definition);
}
```

Este método cria `Index` um novo `Field` objeto com uma lista de objetos que define o esquema do novo índice. Cada campo tem um nome, tipo de dados e vários atributos que definem o seu comportamento de pesquisa. A `FieldBuilder` classe usa a reflexão para criar uma lista de `Field` objetos `Hotel` para o índice examinando as propriedades públicas e atributos da classe modelo dada. Vamos ver mais de perto `Hotel` a aula mais tarde.

> [!NOTE]
> Pode sempre criar a `Field` lista de objetos diretamente em vez de utilizar, `FieldBuilder` se necessário. Por exemplo, pode não querer usar uma classe modelo ou pode precisar de usar uma classe modelo existente que não quer modificar adicionando atributos.
>
> 

Além dos campos, também pode adicionar perfis de pontuação, sugestões ou opções CORS ao Índice (estes parâmetros são omitidos da amostra para brevidade). Pode encontrar mais informações sobre o objeto Index e as suas partes constituintes na [referência SDK,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)bem como na referência API de [Pesquisa Cognitiva Azure](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Povoar o índice
O próximo `Main` passo na povoação do índice recém-criado. Esta população indexada é feita no seguinte método: (Algum código substituído por "..." para fins de ilustração.  Consulte a solução completa da amostra para o código de população de dados completo.)

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        {
            HotelId = "1",
            HotelName = "Secret Point Motel",
            ...
            Address = new Address()
            {
                StreetAddress = "677 5th Ave",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Budget Room, 1 Queen Bed (Cityside)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (City View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "2",
            HotelName = "Twin Dome Motel",
            ...
            {
                StreetAddress = "140 University Town Center Dr",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Suite, 2 Double Beds (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Standard Room, 1 Queen Bed (City View)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Waterfront View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "3",
            HotelName = "Triple Landscape Hotel",
            ...
            Address = new Address()
            {
                StreetAddress = "3393 Peachtree Rd",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Standard Room, 2 Queen Beds (Amenities)",
                    ...
                },
                new Room ()
                {
                    Description = "Standard Room, 2 Double Beds (Waterfront View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (Cityside)",
                    ...
                }
            }
        }
    };

    var batch = IndexBatch.Upload(hotels);

    try
    {
        indexClient.Documents.Index(batch);
    }
    catch (IndexBatchException e)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
}
```

Este método tem quatro partes. O primeiro cria uma `Hotel` matriz `Room` de 3 objetos cada um com 3 objetos que servirão como nossos dados de entrada para carregar para o índice. Estes dados são codificados para a simplicidade. Na sua própria aplicação, os seus dados provavelmente virão de uma fonte de dados externa, como uma base de dados SQL.

A segunda parte `IndexBatch` cria uma contendo os documentos. Especifica a operação que pretende aplicar ao lote no momento em que `IndexBatch.Upload`o cria, neste caso, ligando . O lote é então enviado para o `Documents.Index` índice de pesquisa cognitiva Azure pelo método.

> [!NOTE]
> Neste exemplo, estamos apenas a enviar documentos. Se quiser fundir alterações nos documentos existentes ou apagar `IndexBatch.Merge`documentos, pode criar lotes ligando, `IndexBatch.MergeOrUpload`ou `IndexBatch.Delete` em vez disso. Também pode misturar diferentes operações `IndexBatch.New`num único lote, `IndexAction` ligando, que requer uma coleção de objetos, cada um dos quais diz ao Azure Cognitive Search para realizar uma determinada operação num documento. Pode criar `IndexAction` cada um com o seu próprio `IndexAction.Merge` `IndexAction.Upload`funcionamento, chamando o método correspondente, como, e assim por diante.
> 
> 

A terceira parte deste método é um bloco de captura que lida com um caso de erro importante para a indexação. Se o seu serviço de Pesquisa Cognitiva Azure não indexar alguns dos documentos do lote, um `IndexBatchException` é lançado por `Documents.Index`. Esta exceção pode acontecer se estiver a indexar documentos enquanto o seu serviço estiver sob carga pesada. **Recomendamos vivamente que processe explicitamente este caso no seu código.** Pode atrasar e, em seguida, repetir a indexação dos documentos que falharam, pode iniciar sessão e continuar como no exemplo ou pode fazer algo diferente dependendo dos requisitos de consistência de dados da aplicação.

> [!NOTE]
> Pode utilizar [`FindFailedActionsToRetry`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) o método para construir um novo lote contendo apenas `Index`as ações que falharam numa chamada anterior para . Há uma discussão sobre como usá-lo corretamente [no StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Finalmente, `UploadDocuments` o método atrasa-se por dois segundos. A indexação acontece de forma assíncrona no seu serviço de Pesquisa Cognitiva Azure, pelo que a aplicação da amostra precisa de esperar um pouco para garantir que os documentos estão disponíveis para pesquisa. Este género de atrasos são normalmente necessários apenas para demonstrações gratuitas, testes e aplicações de exemplo.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>Como o SDK .NET processa documentos
Pode estar a perguntar-se como é que o Azure Cognitive Search .NET `Hotel` SDK é capaz de carregar instâncias de uma classe definida pelo utilizador como o índice. Para ajudar a responder a esta `Hotel` pergunta, vamos olhar para a aula:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsSearchable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.EnLucene)]
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

    // SmokingAllowed reflects whether any room in the hotel allows smoking.
    // The JsonIgnore attribute indicates that a field should not be created 
    // in the index for this property and it will only be used by code in the client.
    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? Rating { get; set; }

    public Address Address { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    public Room[] Rooms { get; set; }
}
```

A primeira coisa a notar é que o `Hotel` nome de cada propriedade pública na classe irá mapear para um campo com o mesmo nome na definição de índice. Se quiser que cada campo comece com uma letra minúscula ("caso de camelo"), pode dizer ao SDK `[SerializePropertyNamesAsCamelCase]` para mapear automaticamente os nomes de propriedade para o caso camelo com o atributo da classe. Este cenário é comum em aplicações .NET que realizam a ligação de dados quando o esquema alvo está fora do controlo do desenvolvedor de aplicações sem ter que violar as diretrizes de nomeação do "caso Pascal" em .NET.

> [!NOTE]
> O Azure Cognitive Search .NET SDK utiliza a biblioteca [JSON.NET NewtonSoft](https://www.newtonsoft.com/json/help/html/Introduction.htm) para serializar e desserializar os seus objetos de modelo personalizados de e para a JSON. Se necessário, pode personalizar esta serialização. Para mais informações, consulte [Personal Serialization com JSON.NET](#JsonDotNet).
> 
> 

A segunda coisa a notar é que cada `IsFilterable` `IsSearchable`imóvel é decorado com atributos como, , `Key`e `Analyzer`. Estes atributos mapeiam diretamente para os [atributos de campo correspondentes num índice](/rest/api/searchservice/create-index)de Pesquisa Cognitiva Azure . A `FieldBuilder` classe usa estas propriedades para construir definições de campo para o índice.

A terceira coisa `Hotel` importante sobre a classe são os tipos de dados das propriedades públicas. Os tipos .NET destas propriedades mapeiam para os tipos de campo equivalentes na definição do índice. Por exemplo, a propriedade da cadeia `Category` mapeia para o campo `category`, que é do tipo `Edm.String`. Existem mapeamentos `bool?`de `Edm.Boolean` `DateTimeOffset?`tipo `Edm.DateTimeOffset` semelhanteentre, , e assim por diante. As regras específicas para o mapeamento de tipo são documentadas com o `Documents.Get` método na referência [Azure Cognitive Search .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get). A `FieldBuilder` aula cuida deste mapeamento para si, mas ainda pode ser útil entender caso precise de resolver problemas de serialização.

Por acaso reparou na `SmokingAllowed` propriedade?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

O `JsonIgnore` atributo nesta propriedade `FieldBuilder` diz ao não serializá-lo para o índice como um campo.  Esta é uma ótima maneira de criar propriedades calculadas do lado do cliente que você pode usar como ajudantes na sua aplicação.  Neste caso, `SmokingAllowed` a propriedade reflete `Room` se `Rooms` algum na coleção permite fumar.  Se todos são falsos, indica que todo o hotel não permite fumar.

Algumas propriedades, como `Address` e `Rooms` são casos de classes .NET.  Estas propriedades representam estruturas de dados mais complexas e, como resultado, requerem campos com um [tipo de dados complexo](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) no índice.

A `Address` propriedade representa um conjunto de `Address` múltiplos valores na classe, definido abaixo:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
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

Esta classe contém os valores padrão usados para descrever endereços nos Estados Unidos ou no Canadá. Você pode usar tipos como este para agrupar campos lógicos juntos no índice.

A `Rooms` propriedade representa uma `Room` variedade de objetos:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Room
    {
        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string Type { get; set; }

        [IsFilterable, IsFacetable]
        public double? BaseRate { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string BedOptions { get; set; }

        [IsFilterable, IsFacetable]
        public int SleepsCount { get; set; }

        [IsFilterable, IsFacetable]
        public bool? SmokingAllowed { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }
    }
}
```

O seu modelo de dados em .NET e o seu esquema de índice correspondente devem ser concebidos para suportar a experiência de pesquisa que gostaria de dar ao seu utilizador final. Cada objeto de nível superior em .NET, ou seja, documento no índice, corresponde a um resultado de pesquisa que apresentaria na sua interface de utilizador. Por exemplo, numa aplicação de pesquisa de hotel, os seus utilizadores finais podem querer pesquisar pelo nome do hotel, características do hotel ou as características de um determinado quarto. Cobriremos alguns exemplos de consulta um pouco mais tarde.

Esta capacidade de usar as suas próprias classes para interagir com documentos no índice funciona em ambas as direções; Também pode recuperar os resultados da pesquisa e fazer com que o SDK os desserialize automaticamente para um tipo de escolha, como veremos na secção seguinte.

> [!NOTE]
> O Azure Cognitive Search .NET SDK também suporta documentos dinicamente dactilografados usando a `Document` classe, que é um mapeamento chave/valor de nomes de campo para valores de campo. Isto é útil em cenários onde não sabe qual o esquema de índice no momento da conceção, nem onde seria inconveniente discretizar as classes do modelo específico. Todos os métodos no SDK que lidam com documentos têm sobrecargas que funcionam com a classe `Document`, bem como as sobrecargas de tipo seguro que assumem um parâmetro do tipo genérico. Apenas estes últimos são utilizados no código da amostra neste tutorial. A [ `Document` classe](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document) herda de. `Dictionary<string, object>`
> 
>

**Por que deve utilizar tipos de dados anuláveis**

Ao conceber as suas próprias classes de modelos para mapear para um índice `bool` `int` de Pesquisa Cognitiva Azure, recomendamos declarar propriedades de tipos de valor como e ser nulidade (por exemplo, `bool?` em vez de). `bool` Se utilizar uma propriedade não anulável, terá de **garantir** que não existem documentos no seu índice de contenham um valor nulo para o campo correspondente. Nem o SDK nem o serviço de Pesquisa Cognitiva Azure irão ajudá-lo a impor isto.

Esta não é apenas uma preocupação hipotética: imagine um cenário onde adiciona um novo campo a um índice existente do tipo `Edm.Int32`. Após a atualização da definição de índice, todos os documentos terão um valor nulo para esse novo campo (uma vez que todos os tipos são anulados na Pesquisa Cognitiva Azure). Se, em seguida, utilizar uma classe de modelo com uma propriedade `int` não anulável para esse campo, obterá uma `JsonSerializationException` assim ao tentar obter documentos:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por este motivo, recomendamos que utilize tipos anuláveis nas suas classes de modelos como uma melhor prática.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Serialização personalizada com JSON.NET
O SDK usa JSON.NET para serializar e desserializar documentos. Pode personalizar a serialização e a desserialização se necessário definindo o seu próprio `JsonConverter` ou `IContractResolver`. Para mais informações, consulte a [documentação JSON.NET.](https://www.newtonsoft.com/json/help/html/Introduction.htm) Isto pode ser útil quando pretende adaptar uma classe modelo existente a partir da sua aplicação para utilização com a Pesquisa Cognitiva Azure, e outros cenários mais avançados. Por exemplo, com a serialização personalizada pode:

* Inclua ou exclua certas propriedades da sua classe modelo de serem armazenadas como campos de documentos.
* Mapeie entre nomes de propriedade no seu código e nomes de campo no seu índice.
* Crie atributos personalizados que podem ser usados para mapear propriedades para campos de documentos.

Pode encontrar exemplos de implementação de serialização personalizada nos testes de unidade para a Pesquisa Cognitiva Azure .NET SDK no GitHub. Um bom ponto de partida é [esta pasta.](https://github.com/Azure/azure-sdk-for-net/tree/4f6f4e4c90200c1b0621c4cead302a91e89f2aba/sdk/search/Microsoft.Azure.Search/tests/Tests/Models) Contém aulas que são usadas pelos testes de serialização personalizados.

### <a name="searching-for-documents-in-the-index"></a>Procura de documentos no índice
O último passo na aplicação da amostra é procurar alguns documentos no índice:

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'motel' and return only the HotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "HotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels with a room cheaper than $100 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(r: r/BaseRate lt 100)",
            Select = new[] { "HotelId", "Description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "LastRenovationDate desc" },
            Select = new[] { "HotelName", "LastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'hotel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("hotel", parameters);

    WriteDocuments(results);
}
```

Cada vez que executa uma consulta, este `SearchParameters` método cria primeiro um novo objeto. Este objeto é utilizado para especificar opções adicionais para a consulta, tais como triagem, filtragem, paging e faceting. Neste método, estamos a `Filter`definir `Select` `OrderBy`a, `Top` e propriedade para diferentes consultas. Todas `SearchParameters` as propriedades estão documentadas [aqui.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters)

O próximo passo é executar a consulta de pesquisa. Executar a procura é `Documents.Search` feito usando o método. Para cada consulta, passamos o texto de pesquisa `"*"` para usar como uma cadeia (ou se não há texto de pesquisa), além dos parâmetros de pesquisa criados anteriormente. Também especificamos `Hotel` como parâmetro `Documents.Search`de tipo para , que indica ao SDK para `Hotel`desserializar documentos nos resultados de pesquisa em objetos de tipo .

> [!NOTE]
> Pode encontrar mais informações sobre a sintaxe de expressão de consulta de pesquisa [aqui](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Finalmente, após cada consulta este método iterates através de todos os jogos nos resultados da pesquisa, imprimindo cada documento para a consola:

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

Vamos ver mais de perto cada uma das perguntas. Aqui está o código para executar a primeira consulta:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

Neste caso, estamos procurando todo o índice para a palavra "motel" em qualquer campo pesquisável `Select` e só queremos recuperar os nomes do hotel, conforme especificado pelo parâmetro. Aqui estão os resultados:

    Name: Secret Point Motel

    Name: Twin Dome Motel

A próxima consulta é um pouco mais interessante.  Queremos encontrar qualquer hotel que tenha um quarto com uma diária inferior a $100 e devolva apenas a identificação e descrição do hotel:

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)",
        Select = new[] { "HotelId", "Description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Esta consulta utiliza uma `$filter` expressão `Rooms/any(r: r/BaseRate lt 100)`OData, para filtrar os documentos no índice. Isto utiliza qualquer [operador](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators) para aplicar a 'BaseRate lt 100' a todos os artigos da coleção de quartos. Pode saber mais sobre a sintaxe OData que a Azure Cognitive Search suporta [aqui](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax).

Aqui estão os resultados da consulta:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

Em seguida, queremos encontrar os dois melhores hotéis que foram recentemente renovados, e mostrar o nome do hotel e a última data de renovação. Apresentamos o código a seguir: 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "LastRenovationDate desc" },
        Select = new[] { "HotelName", "LastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Neste caso, voltamos a usar a sintaxe OData para especificar o `OrderBy` parâmetro como `lastRenovationDate desc`. Também definimos `Top` para 2 para garantir que só recebemos os dois melhores documentos. Como antes, `Select` definimos para especificar quais os campos que devem ser devolvidos.

Aqui estão os resultados:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Finalmente, queremos encontrar todos os nomes de hotéis que correspondam à palavra "hotel":

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

E aqui estão os resultados, que incluem todos os campos, uma vez que não especificámos a `Select` propriedade:

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

Este passo completa o tutorial, mas não pare aqui. **Os próximos passos fornecem recursos adicionais para aprender mais sobre a Pesquisa Cognitiva Azure.

## <a name="next-steps"></a>Passos seguintes
* Procure as referências para o [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) e a [API REST](https://docs.microsoft.com/rest/api/searchservice/).
* Reveja [as convenções](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) de nomeação para aprender as regras para nomear vários objetos.
* Reveja os tipos de [dados suportados](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) em Pesquisa Cognitiva Azure.
