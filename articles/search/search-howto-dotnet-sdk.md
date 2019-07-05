---
title: Como utilizar o Azure Search a partir de um aplicativo .NET - Azure Search
description: Saiba como utilizar o Azure Search num aplicativo .NET usando C# e o SDK de .NET. Tarefas baseadas em código incluem a ligar ao serviço, indexar conteúdo e consultar um índice.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: brjohnst
ms.openlocfilehash: 9f0af40d442747181636b50612f7d2162ead6a86
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450020"
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>Como utilizar o Azure Search a partir de uma aplicação .NET

Este artigo é um passo a passo para ajudá-lo em execução com o [SDK .NET da Azure Search](https://aka.ms/search-sdk). Pode utilizar o SDK de .NET para implementar uma experiência de pesquisa avançadas na sua aplicação com o Azure Search.

## <a name="whats-in-the-azure-search-sdk"></a>Novidades no Azure SDK de pesquisa
O SDK é constituído por algumas bibliotecas de cliente que permitem-lhe gerir os índices, sinónimos, indexadores e origens de dados mapeia, bem como carregar e gerir documentos e executar consultas, tudo sem ter que lidar com os detalhes de HTTP e JSON. Estas bibliotecas de cliente são distribuídas como pacotes de NuGet.

O pacote NuGet principal é `Microsoft.Azure.Search`, que é um pacote de metadados que inclui todos os outros pacotes como dependências. Utilize este pacote, se estiver apenas começando, ou se souber que seu aplicativo precisará todos os recursos do Azure Search.

São os outros pacotes de NuGet no SDK:
 
  - `Microsoft.Azure.Search.Data`: Utilize este pacote, se estiver desenvolvendo um aplicativo do .NET com o Azure Search e só tem de consultar ou atualizar documentos em índices. Se também terá de criar ou atualizar índices, mapas de sinónimos, ou outros recursos de nível de serviço, utilizam o `Microsoft.Azure.Search` em vez disso, o pacote.
  - `Microsoft.Azure.Search.Service`: Utilize este pacote, se estiver a desenvolver automatização no .NET para gerir os índices da Azure Search, mapas de sinónimos, indexadores, origens de dados ou outros recursos de nível de serviço. Se só precisa de documentos de consulta ou atualização em seus índices, utilize o `Microsoft.Azure.Search.Data` em vez disso, o pacote. Se precisar de todas as funcionalidades do Azure Search, utilize o `Microsoft.Azure.Search` em vez disso, o pacote.
  - `Microsoft.Azure.Search.Common`: Tipos comuns necessários para as bibliotecas .NET de pesquisa do Azure. Não é necessário utilizar este pacote diretamente na sua aplicação. Destina-se apenas a ser utilizado como uma dependência.

As várias bibliotecas de cliente definem classes como `Index`, `Field`, e `Document`, bem como operações como `Indexes.Create` e `Documents.Search` no `SearchServiceClient` e `SearchIndexClient` classes. Essas classes estão organizadas nas seguintes espaços de nomes:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Se desejar dar feedback para uma atualização futura do SDK, consulte nosso [página de comentários](https://feedback.azure.com/forums/263029-azure-search/) ou crie um problema no [GitHub](https://github.com/azure/azure-sdk-for-net/issues) e dizer "Azure Search" no título do problema.

O SDK de .NET suporta a versão `2019-05-06` das [API de REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/). Esta versão inclui suporte para [tipos complexos](search-howto-complex-data-types.md), [pesquisa cognitiva](cognitive-search-concept-intro.md), [preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete), e [JsonLines modo de análise](search-howto-index-json-blobs.md) quando indexar Blobs do Azure. 

Este SDK não suporta [operações de gestão](https://docs.microsoft.com/rest/api/searchmanagement/) , tais como criar e dimensionar os serviços de pesquisa e gerir chaves de API. Se precisar de gerir os recursos de pesquisa a partir de uma aplicação .NET, pode utilizar o [SDK de gestão do Azure Search .NET](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Atualizar para a versão mais recente do SDK
Se já estiver a utilizar uma versão mais antiga do SDK de .NET de pesquisa do Azure e pretende atualizar para a versão mais recente versão disponível geralmente, [este artigo](search-dotnet-sdk-migration-version-9.md) explica como.

## <a name="requirements-for-the-sdk"></a>Requisitos para o SDK
1. Visual Studio 2017 ou posterior.
2. O próprio serviço de Azure Search. Para utilizar o SDK, terá o nome do seu serviço e uma ou mais chaves de API. [Criar um serviço no portal do](search-create-service-portal.md) irá ajudá-lo a esses passos.
3. Transferir o SDK .NET da Azure Search [pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search) utilizando "Gerir pacotes NuGet" no Visual Studio. Procurar pelo nome do pacote `Microsoft.Azure.Search` NuGet.org (ou uma das outras, se tiver apenas um subconjunto da funcionalidade do pacote nomes acima).

O SDK .NET da Azure Search oferece suporte a aplicativos, visando o .NET Framework 4.5.2 e superior, como também como o .NET Core 2.0 e versões superiores.

## <a name="core-scenarios"></a>Principais cenários
Há várias coisas que precisará fazer no seu aplicativo de pesquisa. Neste tutorial, iremos abranger esses cenários principais:

* Criar um índice
* Preenchimento do índice com documentos
* Pesquisa de documentos através da pesquisa de texto completo e filtros

O código de exemplo seguinte ilustra cada um destes cenários. Pode utilizar os fragmentos de código em seu próprio aplicativo.

### <a name="overview"></a>Descrição geral
Vamos explorar a aplicação de exemplo cria um novo índice com o nome "Hotéis", preenche-a com alguns documentos, em seguida, executa algumas consultas de pesquisa. Este é o programa principal, que mostra o fluxo geral:

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

Vamos examinar isso instruções passo a passo. Primeiro, precisamos criar um novo `SearchServiceClient`. Este objeto permite-lhe gerir índices. Para construir um, terá de fornecer o nome do serviço Azure Search, bem como uma chave de API de administração. Pode introduzir estas informações no `appsettings.json` ficheiros do [aplicação de exemplo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

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
> Se fornecer uma chave incorreta (por exemplo, uma chave de consulta em que era necessária uma chave de administração), o `SearchServiceClient` lançará um `CloudException` com o erro da mensagem "a dizer proibido" na primeira vez que chamar um método de operação, como `Indexes.Create`. Se isto acontecer, verifique novamente nossa chave de API.
> 
> 

As próximas linhas chamam métodos para criar um índice designado "Hotéis", eliminá-lo primeiro se já existir. Vamos descrever esses métodos um pouco mais tarde.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Em seguida, o índice tem de ser preenchidos. Para preencher o índice, temos um `SearchIndexClient`. Existem duas formas de obter uma: construindo-lo, ou chamando `Indexes.GetClient` sobre o `SearchServiceClient`. Podemos usar a segunda opção para sua comodidade.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> Num aplicativo típico de pesquisa, a gestão de índices e a população podem ser manipuladas por um componente separado das consultas de pesquisa. `Indexes.GetClient` é útil para preencher um índice, uma vez que lhe poupa o trabalho de fornecer adicional `SearchCredentials`. Este é realizado através da transferência da chave de administração que utilizou para criar o `SearchServiceClient` para o novo `SearchIndexClient`. No entanto, na parte de seu aplicativo que executa as consultas, é melhor criar o `SearchIndexClient` diretamente para que pode passar uma chave de consulta, que só permite-lhe ler dados, em vez de uma chave de administração. Isso é consistente com o princípio de privilégio mínimo e irá ajudar a tornar a sua aplicação mais segura. Pode encontrar mais informações sobre chaves de administração e chaves de consulta [aqui](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Agora que temos um `SearchIndexClient`, pode preencher o índice. População do índice é feita através de outro método que percorreremos mais tarde.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Por fim, vamos executar algumas consultas de pesquisa e exibir os resultados. Dessa vez usamos outro `SearchIndexClient`:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

Podemos irá dar uma olhada mais de perto o `RunQueries` método mais tarde. Aqui está o código para criar o novo `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Desta vez, iremos utilizar uma chave de consulta, uma vez que não é necessário acesso de escrita para o índice. Pode introduzir estas informações no `appsettings.json` ficheiros do [aplicação de exemplo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Se executar esse aplicativo com um nome de serviço válido e chaves de API, o resultado deverá ser semelhante a este exemplo: (Algumas saídas de consola foi substituída por "..." para fins de ilustração.)

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

O código-fonte completo do aplicativo é fornecido no final deste artigo.

Em seguida, tomaremos olhar detalhadamente cada um dos métodos chamados pelo `Main`.

### <a name="creating-an-index"></a>Criar um índice
Depois de criar uma `SearchServiceClient`, `Main` elimina o índice "Hotéis" se já existir. Essa eliminação é feita com o seguinte método:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Este método utiliza a determinado `SearchServiceClient` para verificar se existe o índice e em caso afirmativo, elimine-a.

> [!NOTE]
> O código de exemplo neste artigo utiliza os métodos síncronos do SDK .NET da Azure Search por uma questão de simplicidade. Recomendamos que utilize os métodos assíncronos nas suas próprias aplicações para mantê-las escaláveis e responsivas. Por exemplo, no método acima poderia usar `ExistsAsync` e `DeleteAsync` em vez de `Exists` e `Delete`.
> 
> 

Em seguida, `Main` cria um novo índice "Hotéis" ao chamar este método:

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

Este método cria uma nova `Index` objeto com uma lista de `Field` objetos que define o esquema do índice novo. Cada campo tem um nome, tipo de dados e vários atributos que definem seu comportamento de pesquisa. O `FieldBuilder` classe usa a reflexão para criar uma lista de `Field` objetos para o índice ao examinar as propriedades públicas e os atributos da determinado `Hotel` classe de modelo. Vamos olhar detalhadamente o `Hotel` classe mais tarde.

> [!NOTE]
> Pode sempre criar a lista de `Field` objetos diretamente em vez de usar `FieldBuilder` se for necessário. Por exemplo, pode não querer usar uma classe de modelo ou poderá ter de utilizar uma classe de modelo existente que não pretende modificar adicionando atributos.
>
> 

Além dos campos, também pode adicionar perfis de classificação, sugestores ou opções CORS para o índice (esses parâmetros são omitidos do exemplo para fins de brevidade). Pode encontrar mais informações sobre o objeto de índice e suas partes constituintes no [referência do SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index), e na [referência da API REST da Azure Search](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Preenchimento do índice
O próximo passo `Main` preenche o índice recentemente criado. A população do índice é feita no método seguinte: (Alguns códigos substituído por "..." Para efeitos de ilustração.  Veja a solução de exemplo completo para o código de população de dados completo.)

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

Este método tem quatro partes. O primeiro cria uma matriz de 3 `Hotel` objetos cada um com 3 `Room` objetos que irão servir como nossos dados de entrada para carregar para o índice. Estes dados estão hard-coded para manter a simplicidade. Em seu próprio aplicativo, seus dados provavelmente serão proveniente de uma origem de dados externos, como uma base de dados SQL.

A segunda parte cria um `IndexBatch` que contém os documentos. Especificar a operação que pretende aplicar para o batch no momento criá-la, neste caso, chamando `IndexBatch.Upload`. O batch, em seguida, é carregado para o índice da Azure Search pelo `Documents.Index` método.

> [!NOTE]
> Neste exemplo, estamos apenas estiver a carregar documentos. Se quiser mesclar alterações em documentos existentes ou eliminar documentos, pode criar lotes chamando `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, ou `IndexBatch.Delete` em vez disso. Também pode misturar operações diferentes num único lote chamando `IndexBatch.New`, que aceita um conjunto de `IndexAction` objetos, cada um dos quais informa a Azure Search para executar uma determinada operação num documento. Pode criar cada `IndexAction` com a sua própria operação chamando o método correspondente como `IndexAction.Merge`, `IndexAction.Upload`e assim por diante.
> 
> 

A terceira parte desse método é um bloco catch que processa um caso de erro importantes para indexação. Se o serviço Azure Search não consegue indexar alguns dos documentos no lote, uma `IndexBatchException` é emitida pelo `Documents.Index`. Essa exceção pode acontecer se indexar documentos enquanto o seu serviço estiver sobrecarregado. **Recomendamos vivamente que processe explicitamente este caso no seu código.** Pode atrasar e, em seguida, repetir a indexação dos documentos que falharam, pode iniciar sessão e continuar como no exemplo ou pode fazer algo diferente dependendo dos requisitos de consistência de dados da aplicação.

> [!NOTE]
> Pode utilizar o [ `FindFailedActionsToRetry` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) método para construir um novo lote que contém apenas as ações que falha numa chamada anterior para `Index`. Há uma discussão sobre como usar adequadamente [no StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Por fim, o `UploadDocuments` atrasos de método por dois segundos. A indexação acontece de forma assíncrona no seu serviço da Azure Search, desta forma, a aplicação de exemplo tem de aguardar alguns momentos para se certificar de que os documentos estão disponíveis para pesquisa. Este género de atrasos são normalmente necessários apenas para demonstrações gratuitas, testes e aplicações de exemplo.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>Como o SDK .NET processa documentos
Pode estar a pensar como é que o SDK NET da Azure Search consegue carregar instâncias de uma classe definida por utilizador, tal como `Hotel`, para o índice. Para ajudar a responder a essa questão, vamos examinar o `Hotel` classe:

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

A primeira coisa a observar é que o nome de cada propriedade pública no `Hotel` classe será mapeada para um campo com o mesmo nome na definição do índice. Se gostaria de cada campo para começar com uma letra minúscula ("camel case"), pode indicar ao SDK para mapear os nomes de propriedade para camel-case automaticamente com o `[SerializePropertyNamesAsCamelCase]` atributo na classe. Este cenário é comum em aplicativos .NET que realizam enlace de dados em que o esquema de destino está fora do controlo do programador da aplicação sem ter de violar o "Pascal case" diretrizes no .NET de nomenclatura.

> [!NOTE]
> O SDK .NET da Azure Search utiliza a biblioteca [NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) para serializar e anular a serialização dos objetos do modelo personalizado de e para JSON. Se necessário, pode personalizar esta serialização. Para obter mais informações, consulte [personalizados de serialização com JSON.NET](#JsonDotNet).
> 
> 

A segunda coisa a observar é que cada propriedade está decorada com atributos, tais como `IsFilterable`, `IsSearchable`, `Key`, e `Analyzer`. Esses atributos são mapeados diretamente para o [atributos de campo correspondente no índice da Azure Search](https://docs.microsoft.com/rest/api/searchservice/create-index#request). O `FieldBuilder` classe usa essas propriedades para construir as definições de campo para o índice.

O terceiro ponto importante sobre o `Hotel` classe é os tipos de dados das propriedades públicas. Os tipos .NET destas propriedades mapeiam para os tipos de campo equivalentes na definição do índice. Por exemplo, a propriedade da cadeia `Category` mapeia para o campo `category`, que é do tipo `Edm.String`. Existem mapeamentos de tipo semelhantes entre `bool?`, `Edm.Boolean`, `DateTimeOffset?`, e `Edm.DateTimeOffset` e assim por diante. As regras específicas para o mapeamento do tipo estão documentadas com o método `Documents.Get` em [Azure Search .NET SDK reference (Referência SDK .NET do Azure Search)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get). O `FieldBuilder` classe se encarrega desse mapeamento para, mas ainda pode ser útil compreender caso precise resolver quaisquer problemas de serialização.

Aconteceu tenha em atenção o `SmokingAllowed` propriedade?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

O `JsonIgnore` atributo tuto vlastnost informa o `FieldBuilder` para não serializá-lo para o índice como um campo.  Esta é uma excelente forma de criar propriedades calculadas do lado do cliente que pode utilizar como auxiliares em seu aplicativo.  Neste caso, o `SmokingAllowed` propriedade reflete se qualquer `Room` no `Rooms` coleção permite fumar.  Se todos estiverem FALSO, indica que o hotel todo não permite fumar.

Algumas propriedades, tais como `Address` e `Rooms` são instâncias de classes do .NET.  Essas propriedades representam as estruturas de dados mais complexas e, assim, exigir campos com um [tipo de dados complexos](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) no índice.

O `Address` propriedade representa um conjunto de valores múltiplos no `Address` classe, definido abaixo:

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

Essa classe contém os valores padrão usados para descrever endereços nos Estados Unidos ou Canadá. Pode utilizar esses tipos de agrupar os campos de lógicos no índice.

O `Rooms` propriedade representa uma matriz de `Room` objetos:

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

O modelo de dados no .NET e seu esquema de índice correspondente deve ser concebido para suportar a experiência de pesquisa que pretende dar ao usuário final. Cada objeto de nível superior no .NET, ie documento no índice, corresponde a um resultado de pesquisa que poderia apresentar na interface do usuário. Por exemplo, num aplicativo de pesquisa do hotel aos utilizadores finais talvez queira procurar por nome de hotel, funcionalidades do hotel ou as características de uma sala específica. Vamos abordar alguns exemplos de consulta um pouco mais tarde.

Essa capacidade de usar suas próprias classes para interagir com documentos no índice funciona em ambas as direções; Também pode obter os resultados da pesquisa e ter o SDK automaticamente tirar de série-los a um tipo de sua escolha, como veremos na próxima seção.

> [!NOTE]
> O SDK .NET da Azure Search também suporta documentos dinâmicos utilizando a classe `Document`, que é um mapeamento de chave/valor dos nomes de campo para valores de campo. Isto é útil em cenários onde não sabe qual o esquema de índice no momento da conceção, nem onde seria inconveniente discretizar as classes do modelo específico. Todos os métodos no SDK que lidam com documentos têm sobrecargas que funcionam com a classe `Document`, bem como as sobrecargas de tipo seguro que assumem um parâmetro do tipo genérico. Apenas o último caso é utilizado no código de exemplo neste tutorial. O [ `Document` classe](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document) herda de `Dictionary<string, object>`.
> 
>

**Por que deve utilizar tipos de dados anuláveis**

Ao conceber as suas próprias classes de modelo para mapear para um índice da Azure Search, recomendamos que declare as propriedades dos tipos de valores, tais como `bool` e `int` para que seja anulável (por exemplo, `bool?` ao invés de `bool`). Se utilizar uma propriedade não anulável, terá de **garantir** que não existem documentos no seu índice de contenham um valor nulo para o campo correspondente. Nem o SDK nem o serviço da Azure Search irão ajudá-lo a impor tais pedidos.

Não é apenas uma preocupação hipotética: Imagine um cenário em que adicionar um novo campo para um índice existente que é do tipo `Edm.Int32`. Depois de atualizar a definição do índice, todos os documentos terão um valor nulo para esse campo novo (uma vez que todos os tipos são anuláveis na Azure Search). Se, em seguida, utilizar uma classe de modelo com uma propriedade `int` não anulável para esse campo, obterá uma `JsonSerializationException` assim ao tentar obter documentos:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por este motivo, recomendamos que utilize tipos anuláveis nas suas classes de modelos como uma melhor prática.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Personalizado serialização com JSON.NET
O SDK utiliza JSON.NET para serializar e anular a serialização de documentos. Pode personalizar a serialização e desserialização se for necessário, definindo suas próprias `JsonConverter` ou `IContractResolver`. Para obter mais informações, consulte a [JSON.NET documentação](https://www.newtonsoft.com/json/help/html/Introduction.htm). Isso pode ser útil quando quer adaptar uma classe de modelo existente de seu aplicativo para uso com o Azure Search e outros cenários mais avançados. Por exemplo, com a serialização personalizada, pode:

* Incluir ou excluir determinadas propriedades de classe do modelo de que está a ser armazenados como campos de documentos.
* Mapear entre os nomes das propriedades em seu código e os nomes de campos no seu índice.
* Crie atributos personalizados que podem ser utilizados para mapear as propriedades para campos de documentos.

Pode encontrar exemplos de implementação de serialização personalizada nos testes de unidade para o SDK de .NET de pesquisa do Azure no GitHub. É um ponto de partida [nesta pasta](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models). Ela contém classes que são utilizadas pelos testes de serialização personalizada.

### <a name="searching-for-documents-in-the-index"></a>Pesquisa de documentos no índice
A última etapa no aplicativo de exemplo é procurar alguns documentos no índice:

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

Sempre que ele executa uma consulta, este método cria uma nova `SearchParameters` objeto. Este objeto é utilizado para especificar opções adicionais para a consulta, como a classificação, filtragem, paginação e facetamento. Nesse método, estamos definindo a `Filter`, `Select`, `OrderBy`, e `Top` propriedade para consultas diferentes. Todos os `SearchParameters` propriedades estão documentadas [aqui](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

A próxima etapa é realmente executar a consulta de pesquisa. Realizar a pesquisa é feito usando o `Documents.Search` método. Para cada consulta, passamos o texto de pesquisa para utilizar como uma cadeia de caracteres (ou `"*"` se não houver nenhum texto de pesquisa), além dos parâmetros de pesquisa que criou anteriormente. Também especificamos `Hotel` como o parâmetro de tipo para `Documents.Search`, que informa o SDK para desserializar documentos nos resultados da pesquisa em objetos do tipo `Hotel`.

> [!NOTE]
> Pode encontrar mais informações sobre a sintaxe de expressão de consulta de pesquisa [aqui](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Por fim, depois de cada consulta esse método itera em todas as correspondências nos resultados da pesquisa, impressão de cada documento para a consola:

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

Por sua vez, vamos ver mais detalhadamente cada uma das consultas. Aqui está o código para executar a primeira consulta:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

Neste caso, estamos pesquisando no índice completo para a palavra "motel" em qualquer campo pesquisável e queremos apenas recuperar os nomes de hotel, tal como especificado pelo `Select` parâmetro. Aqui estão os resultados:

    Name: Secret Point Motel

    Name: Twin Dome Motel

A consulta seguinte é um pouco mais interessante.  Queremos encontrar qualquer hotéis que tenham uma sala com uma taxa noturna de menos de US $100 e devolvem apenas o ID de hotel e a descrição:

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

Esta consulta utiliza um OData `$filter` expressão, `Rooms/any(r: r/BaseRate lt 100)`, para filtrar os documentos no índice. Esta opção utiliza a [operador any](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators) para aplicar o ' BaseRate lt 100' a todos os itens da coleção de ambientes. Pode encontrar mais informações sobre a sintaxe OData que suporte o Azure Search [aqui](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax).

Aqui estão os resultados da consulta:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

Em seguida, queremos encontrar os duas hotéis principais que têm sido mais recentemente renovated e mostrar o nome de hotel e a última data de renovação. Apresentamos o código a seguir: 

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

Neste caso, estamos novamente Utilize sintaxe OData para especificar a `OrderBy` parâmetro como `lastRenovationDate desc`. Também definimos `Top` para 2 para garantir que obtém apenas os documentos de duas principais. Como antes, definimos `Select` para especificar os campos que devem ser devolvidos.

Aqui estão os resultados:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Por fim, queremos localizar todos os nomes de hotéis que correspondem a palavra "hotel":

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

E aqui estão os resultados, que incluem todos os campos, uma vez que estamos não especificou o `Select` propriedade:

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

Este passo conclui o tutorial, mas não pare aqui. \* * Próximas etapas fornecem recursos adicionais para saber mais sobre o Azure Search.

## <a name="next-steps"></a>Passos Seguintes
* Procure as referências para o [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) e a [API REST](https://docs.microsoft.com/rest/api/searchservice/).
* Revisão [convenções de nomenclatura](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) para saber as regras para atribuição de nomes de vários objetos.
* Revisão [tipos de dados suportados](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) no Azure Search.
