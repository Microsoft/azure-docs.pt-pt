---
title: Como usar Azure Search de um aplicativo .NET-Azure Search
description: Saiba como usar Azure Search em um aplicativo .NET usando C# o e o SDK do .net. As tarefas baseadas em código incluem conectar-se ao serviço, índice de conteúdo e consultar um índice.
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: brjohnst
ms.openlocfilehash: 3f167ee082b751f6bd686c0543db6a262615e486
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208254"
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>Como usar Azure Search de um aplicativo .NET

Este artigo é explicativo para colocar em funcionamento o [SDK do .net Azure Search](https://aka.ms/search-sdk). Você pode usar o SDK do .NET para implementar uma experiência de pesquisa avançada em seu aplicativo usando Azure Search.

## <a name="whats-in-the-azure-search-sdk"></a>O que há no SDK do Azure Search
O SDK consiste em algumas bibliotecas de cliente que permitem gerenciar seus índices, fontes de dados, indexadores e mapas de sinônimos, bem como carregar e gerenciar documentos e executar consultas, tudo isso sem precisar lidar com os detalhes de HTTP e JSON. Essas bibliotecas de cliente são todas distribuídas como pacotes NuGet.

O principal pacote NuGet é `Microsoft.Azure.Search`, que é um meta-Package que inclui todos os outros pacotes como dependências. Use este pacote se você estiver apenas começando ou se souber que seu aplicativo precisará de todos os recursos do Azure Search.

Os outros pacotes NuGet no SDK são:
 
  - `Microsoft.Azure.Search.Data`: Use este pacote se você estiver desenvolvendo um aplicativo .NET usando Azure Search e precisar apenas consultar ou atualizar documentos em seus índices. Se você também precisar criar ou atualizar índices, mapas de sinônimos ou outros recursos de nível de serviço, use `Microsoft.Azure.Search` o pacote em vez disso.
  - `Microsoft.Azure.Search.Service`: Use este pacote se você estiver desenvolvendo automação no .NET para gerenciar índices de Azure Search, mapas de sinônimos, indexadores, fontes de dados ou outros recursos de nível de serviço. Se você precisar apenas consultar ou atualizar documentos em seus índices, use o `Microsoft.Azure.Search.Data` pacote em vez disso. Se você precisar de toda a funcionalidade do Azure Search, use `Microsoft.Azure.Search` o pacote em vez disso.
  - `Microsoft.Azure.Search.Common`: Tipos comuns necessários para as bibliotecas do Azure Search .NET. Você não precisa usar esse pacote diretamente em seu aplicativo. Ele destina-se apenas a ser usado como uma dependência.

As várias bibliotecas de cliente definem `Index`classes `Field`como, `Document`e, bem como operações como `Indexes.Create` e `Documents.Search` nas `SearchServiceClient` classes e `SearchIndexClient` . Essas classes são organizadas nos seguintes namespaces:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Se você quiser fornecer comentários para uma atualização futura do SDK, consulte nossa [página de comentários](https://feedback.azure.com/forums/263029-azure-search/) ou crie um problema no [GitHub](https://github.com/azure/azure-sdk-for-net/issues) e mencione "Azure Search" no título do problema.

O SDK do .net dá `2019-05-06` suporte à versão da [API REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/). Esta versão inclui suporte para [tipos complexos](search-howto-complex-data-types.md), [pesquisa cognitiva](cognitive-search-concept-intro.md), [preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete)e [modo de análise de JsonLines](search-howto-index-json-blobs.md) ao indexar BLOBs do Azure. 

Esse SDK não oferece suporte a [operações de gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/) , como criação e dimensionamento de serviços de pesquisa e gerenciamento de chaves de API. Se você precisar gerenciar seus recursos de pesquisa de um aplicativo .NET, poderá usar o [SDK de gerenciamento Azure Search .net](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Atualizando para a versão mais recente do SDK
Se você já estiver usando uma versão mais antiga do SDK do .NET Azure Search e quiser atualizar para a versão mais recente disponível, [Este artigo](search-dotnet-sdk-migration-version-9.md) explica como.

## <a name="requirements-for-the-sdk"></a>Requisitos para o SDK
1. Visual Studio 2017 ou posterior.
2. Seu próprio serviço de Azure Search. Para usar o SDK, você precisará do nome do seu serviço e de uma ou mais chaves de API. [Criar um serviço no portal](search-create-service-portal.md) ajudará você a usar essas etapas.
3. Baixe o [pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search) do SDK do .net Azure Search usando "gerenciar pacotes NuGet" no Visual Studio. Basta procurar o nome `Microsoft.Azure.Search` do pacote em NuGet.org (ou um dos outros nomes de pacote acima se você precisar apenas de um subconjunto da funcionalidade).

O SDK do .NET Azure Search dá suporte a aplicativos que visam o .NET Framework 4.5.2 e superior, bem como o .NET Core 2,0 e superior.

## <a name="core-scenarios"></a>Cenários principais
Há várias coisas que você precisará fazer em seu aplicativo de pesquisa. Neste tutorial, abordaremos estes cenários principais:

* Criando um índice
* Populando o índice com documentos
* Pesquisando documentos usando filtros e pesquisa de texto completo

O código de exemplo a seguir ilustra cada um desses cenários. Sinta-se à vontade para usar os trechos de código em seu próprio aplicativo.

### <a name="overview"></a>Descrição geral
O aplicativo de exemplo que iremos explorar criará um novo índice chamado "Hotéis", o preencherá com alguns documentos e, em seguida, executará algumas consultas de pesquisa. Este é o programa principal, mostrando o fluxo geral:

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

Vamos percorrer este passo a passo. Primeiro, precisamos criar um novo `SearchServiceClient`. Esse objeto permite que você gerencie índices. Para construir um, você precisa fornecer o nome do serviço Azure Search, bem como uma chave de API de administração. Você pode inserir essas informações no `appsettings.json` arquivo do aplicativo de [exemplo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

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
> Se você fornecer uma chave incorreta (por exemplo, uma chave de consulta onde uma chave de administrador era necessária `SearchServiceClient` ), o `CloudException` gerará um com a mensagem de erro "proibido" na primeira vez `Indexes.Create`em que você chamar um método de operação, como. Se isso acontecer com você, verifique novamente nossa chave de API.
> 
> 

As próximas linhas chamam métodos para criar um índice chamado "Hotéis", excluindo-o primeiro se ele já existir. Abordaremos esses métodos um pouco mais tarde.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Em seguida, o índice precisa ser preenchido. Para popular o índice, será necessário um `SearchIndexClient`. Há duas maneiras de obter uma: construindo-a ou chamando `Indexes.GetClient` `SearchServiceClient`no. Usamos o último para sua conveniência.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> Em um aplicativo de pesquisa típico, o gerenciamento de índice e a população podem ser tratados por um componente separado das consultas de pesquisa. `Indexes.GetClient`é conveniente para popular um índice, pois ele poupa o problema de fornecer outros `SearchCredentials`. Este é realizado através da transferência da chave de administração que utilizou para criar o `SearchServiceClient` para o novo `SearchIndexClient`. No entanto, na parte do aplicativo que executa consultas, é melhor criar o `SearchIndexClient` diretamente para que você possa passar uma chave de consulta, o que permite somente a leitura de dados, em vez de uma chave de administração. Isso é consistente com o princípio de privilégios mínimos e ajudará a tornar seu aplicativo mais seguro. Você pode encontrar mais informações sobre chaves de administração e chaves de consulta [aqui](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Agora que temos um `SearchIndexClient`, podemos preencher o índice. A população de índice é feita por outro método que veremos posteriormente.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Por fim, executamos algumas consultas de pesquisa e exibimos os resultados. Desta vez, usamos outro `SearchIndexClient`:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

Examinaremos mais detalhadamente o `RunQueries` método mais tarde. Este é o código para criar o novo `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Desta vez, usamos uma chave de consulta, pois não precisamos de acesso de gravação ao índice. Você pode inserir essas informações no `appsettings.json` arquivo do aplicativo de [exemplo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Se você executar esse aplicativo com um nome de serviço válido e chaves de API, a saída deverá ser semelhante a este exemplo: (Parte da saída do console foi substituída por "..." para fins de ilustração.)

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

Em seguida, vamos examinar mais de perto cada um dos métodos chamados pelo `Main`.

### <a name="creating-an-index"></a>Criando um índice
Depois de criar `SearchServiceClient`um `Main` , o exclui o índice "Hotéis", caso ele já exista. Essa exclusão é feita pelo seguinte método:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Esse método usa o fornecido `SearchServiceClient` para verificar se o índice existe e, nesse caso, excluí-lo.

> [!NOTE]
> O código de exemplo neste artigo utiliza os métodos síncronos do SDK .NET da Azure Search por uma questão de simplicidade. Recomendamos que utilize os métodos assíncronos nas suas próprias aplicações para mantê-las escaláveis e responsivas. Por exemplo, no método acima, você pode usar `ExistsAsync` e `DeleteAsync` em vez `Exists` de `Delete`e.
> 
> 

Em seguida `Main` , cria um novo índice de "Hotéis" chamando este método:

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

Esse método cria um novo `Index` objeto com uma lista de `Field` objetos que define o esquema do novo índice. Cada campo tem um nome, tipo de dados e vários atributos que definem seu comportamento de pesquisa. A `FieldBuilder` classe usa a reflexão para criar uma lista `Field` de objetos para o índice examinando as propriedades públicas e os atributos da `Hotel` classe de modelo fornecida. Vamos examinar mais de perto a `Hotel` classe mais tarde.

> [!NOTE]
> Você sempre pode criar a lista de `Field` objetos diretamente em vez de `FieldBuilder` usar, se necessário. Por exemplo, talvez você não queira usar uma classe de modelo ou pode precisar usar uma classe de modelo existente que não deseja modificar adicionando atributos.
>
> 

Além dos campos, você também pode adicionar perfis de pontuação, sugestores ou opções de CORS ao índice (esses parâmetros são omitidos do exemplo para fins de brevidade). Você pode encontrar mais informações sobre o objeto de índice e suas partes constituintes na [referência do SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index), bem como na [referência da API REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Populando o índice
A próxima etapa em `Main` popula o índice recém-criado. Essa população de índice é feita no seguinte método: (Algum código foi substituído por "..." para fins de ilustração.  Consulte a solução de exemplo completo para obter o código completo de população de dados.)

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

Esse método tem quatro partes. O primeiro cria uma matriz de três `Hotel` objetos cada um com `Room` três objetos que servirão como nossos dados de entrada para carregar no índice. Esses dados são embutidos em código para simplificar. Em seu próprio aplicativo, seus dados provavelmente serão provenientes de uma fonte de dados externa, como um SQL Database.

A segunda parte cria um `IndexBatch` contendo os documentos. Você especifica a operação que deseja aplicar ao lote no momento em que criá-lo, nesse caso chamando `IndexBatch.Upload`. Em seguida, o lote é carregado no índice de Azure Search `Documents.Index` pelo método.

> [!NOTE]
> Neste exemplo, estamos apenas carregando documentos. Se você quisesse mesclar alterações em documentos existentes ou excluir documentos, poderá criar lotes chamando `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`ou `IndexBatch.Delete` em vez disso. Você também pode misturar operações diferentes em um único lote chamando `IndexBatch.New`, que usa uma coleção de `IndexAction` objetos, cada um dos quais diz Azure Search para executar uma operação específica em um documento. Você pode criar cada `IndexAction` uma com sua própria operação chamando o método `IndexAction.Merge`correspondente, como, `IndexAction.Upload`e assim por diante.
> 
> 

A terceira parte desse método é um bloco catch que manipula um caso de erro importante para indexação. Se o serviço Azure Search não consegue indexar alguns dos documentos no lote, uma `IndexBatchException` é emitida pelo `Documents.Index`. Essa exceção pode ocorrer se você estiver indexando documentos enquanto seu serviço está sob carga pesada. **Recomendamos vivamente que processe explicitamente este caso no seu código.** Pode atrasar e, em seguida, repetir a indexação dos documentos que falharam, pode iniciar sessão e continuar como no exemplo ou pode fazer algo diferente dependendo dos requisitos de consistência de dados da aplicação.

> [!NOTE]
> Você pode usar o [`FindFailedActionsToRetry`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) método para construir um novo lote contendo apenas as ações que falharam em uma chamada anterior `Index`para. Há uma discussão sobre como usá-lo corretamente [no stackoverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Por fim, `UploadDocuments` o método atrasa por dois segundos. A indexação acontece de forma assíncrona no seu serviço da Azure Search, desta forma, a aplicação de exemplo tem de aguardar alguns momentos para se certificar de que os documentos estão disponíveis para pesquisa. Este género de atrasos são normalmente necessários apenas para demonstrações gratuitas, testes e aplicações de exemplo.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>Como o SDK .NET processa documentos
Pode estar a pensar como é que o SDK NET da Azure Search consegue carregar instâncias de uma classe definida por utilizador, tal como `Hotel`, para o índice. Para ajudar a responder a essa pergunta, vamos examinar a `Hotel` classe:

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

A primeira coisa a ser observada é que o nome de cada propriedade pública `Hotel` na classe será mapeado para um campo com o mesmo nome na definição do índice. Se desejar que cada campo comece com uma letra minúscula ("camel case"), você poderá dizer ao SDK para mapear os nomes de propriedade para camel case automaticamente com o `[SerializePropertyNamesAsCamelCase]` atributo na classe. Esse cenário é comum em aplicativos .NET que executam a vinculação de dados em que o esquema de destino está fora do controle do desenvolvedor do aplicativo sem a necessidade de violar as diretrizes de nomenclatura do "caso do Pascal" no .NET.

> [!NOTE]
> O SDK .NET da Azure Search utiliza a biblioteca [NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) para serializar e anular a serialização dos objetos do modelo personalizado de e para JSON. Se necessário, pode personalizar esta serialização. Para obter mais informações, consulte [serialização personalizada com JSON.net](#JsonDotNet).
> 
> 

A segunda coisa a ser observada é que cada propriedade é decorada `IsFilterable`com `IsSearchable`atributos `Key`como, `Analyzer`, e. Esses atributos são mapeados diretamente para os [atributos de campo correspondentes em um índice de Azure Search](https://docs.microsoft.com/rest/api/searchservice/create-index#request). A `FieldBuilder` classe usa essas propriedades para construir definições de campo para o índice.

A terceira coisa importante sobre a `Hotel` classe são os tipos de dados das propriedades públicas. Os tipos .NET destas propriedades mapeiam para os tipos de campo equivalentes na definição do índice. Por exemplo, a propriedade da cadeia `Category` mapeia para o campo `category`, que é do tipo `Edm.String`. Há mapeamentos de tipo semelhantes entre `bool?` `DateTimeOffset?`, `Edm.Boolean`, e `Edm.DateTimeOffset` e assim por diante. As regras específicas para o mapeamento do tipo estão documentadas com o método `Documents.Get` em [Azure Search .NET SDK reference (Referência SDK .NET do Azure Search)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get). A `FieldBuilder` classe cuida desse mapeamento para você, mas ainda pode ser útil entender caso você precise solucionar problemas de serialização.

Você observou a `SmokingAllowed` Propriedade?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

O `JsonIgnore` atributo nessa propriedade `FieldBuilder` informa ao para não serializá-lo para o índice como um campo.  Essa é uma ótima maneira de criar propriedades calculadas no lado do cliente que você pode usar como auxiliares em seu aplicativo.  Nesse caso, a `SmokingAllowed` propriedade reflete se alguma `Room` na `Rooms` coleção permite fumante.  Se todos forem falsos, isso indica que o Hotel inteiro não permite fumante.

Algumas propriedades como `Address` e `Rooms` são instâncias de classes .net.  Essas propriedades representam estruturas de dados mais complexas e, como resultado, exigem campos com um [tipo de dados complexo](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) no índice.

A `Address` propriedade representa um conjunto de vários valores `Address` na classe, definido abaixo:

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

Essa classe contém os valores padrão usados para descrever os endereços no Estados Unidos ou no Canadá. Você pode usar tipos como este para agrupar campos lógicos juntos no índice.

A `Rooms` propriedade representa uma matriz de `Room` objetos:

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

Seu modelo de dados no .NET e seu esquema de índice correspondente devem ser projetados para dar suporte à experiência de pesquisa que você gostaria de dar ao usuário final. Cada objeto de nível superior no .NET, documento do IE no índice, corresponde a um resultado de pesquisa que você estaria presente na sua interface do usuário. Por exemplo, em um aplicativo de pesquisa de Hotel, seus usuários finais talvez queiram Pesquisar por nome de Hotel, recursos do hotel ou as características de um espaço específico. Abordaremos alguns exemplos de consulta um pouco mais tarde.

Essa capacidade de usar suas próprias classes para interagir com documentos no índice funciona em ambas as direções; Você também pode recuperar os resultados da pesquisa e fazer com que o SDK Desserialize-os automaticamente para um tipo de sua escolha, como veremos na próxima seção.

> [!NOTE]
> O SDK .NET da Azure Search também suporta documentos dinâmicos utilizando a classe `Document`, que é um mapeamento de chave/valor dos nomes de campo para valores de campo. Isto é útil em cenários onde não sabe qual o esquema de índice no momento da conceção, nem onde seria inconveniente discretizar as classes do modelo específico. Todos os métodos no SDK que lidam com documentos têm sobrecargas que funcionam com a classe `Document`, bem como as sobrecargas de tipo seguro que assumem um parâmetro do tipo genérico. Somente os últimos são usados no código de exemplo neste tutorial. `Dictionary<string, object>` [ A`Document` classe](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document) é herdada de.
> 
>

**Por que deve utilizar tipos de dados anuláveis**

Ao conceber as suas próprias classes de modelo para mapear para um índice da Azure Search, recomendamos que declare as propriedades dos tipos de valores, tais como `bool` e `int` para que seja anulável (por exemplo, `bool?` ao invés de `bool`). Se utilizar uma propriedade não anulável, terá de **garantir** que não existem documentos no seu índice de contenham um valor nulo para o campo correspondente. Nem o SDK nem o serviço da Azure Search irão ajudá-lo a impor tais pedidos.

Isso não é apenas uma preocupação hipotética: Imagine um cenário em que você adiciona um novo campo a um índice existente que é do `Edm.Int32`tipo. Depois de atualizar a definição do índice, todos os documentos terão um valor nulo para esse campo novo (uma vez que todos os tipos são anuláveis na Azure Search). Se, em seguida, utilizar uma classe de modelo com uma propriedade `int` não anulável para esse campo, obterá uma `JsonSerializationException` assim ao tentar obter documentos:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por este motivo, recomendamos que utilize tipos anuláveis nas suas classes de modelos como uma melhor prática.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Serialização personalizada com JSON.NET
O SDK usa JSON.NET para serializar e desserializar documentos. Você pode personalizar a serialização e desserialização, se necessário, definindo `JsonConverter` seu `IContractResolver`próprio ou. Para obter mais informações, consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm). Isso pode ser útil quando você deseja adaptar uma classe de modelo existente do seu aplicativo para uso com o Azure Search e outros cenários mais avançados. Por exemplo, com a serialização personalizada, você pode:

* Inclua ou exclua determinadas propriedades de sua classe de modelo para serem armazenadas como campos de documento.
* Mapear entre nomes de propriedade em seu código e nomes de campo no índice.
* Crie atributos personalizados que podem ser usados para mapear Propriedades para campos de documento.

Você pode encontrar exemplos de implementação de serialização personalizada nos testes de unidade para o SDK Azure Search .NET no GitHub. Um bom ponto de partida é [essa pasta](https://github.com/Azure/azure-sdk-for-net/tree/4f6f4e4c90200c1b0621c4cead302a91e89f2aba/sdk/search/Microsoft.Azure.Search/tests/Tests/Models). Ele contém classes que são usadas pelos testes de serialização personalizados.

### <a name="searching-for-documents-in-the-index"></a>Pesquisando documentos no índice
A última etapa do aplicativo de exemplo é Pesquisar alguns documentos no índice:

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

Cada vez que ele executa uma consulta, esse método primeiro cria um novo `SearchParameters` objeto. Esse objeto é usado para especificar opções adicionais para a consulta, como classificação, filtragem, paginação e faceta. Nesse método, `Filter`estamos definindo a propriedade, `Select`, `OrderBy`e `Top` para consultas diferentes. Todas as propriedades estão documentadas [aqui.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters) `SearchParameters`

A próxima etapa é realmente executar a consulta de pesquisa. A execução da pesquisa é feita usando `Documents.Search` o método. Para cada consulta, passamos o texto de pesquisa para usar como uma cadeia de `"*"` caracteres (ou se não houver nenhum texto de pesquisa), além dos parâmetros de pesquisa criados anteriormente. Também especificamos `Hotel` como o parâmetro de tipo `Documents.Search`para, que informa ao SDK para desserializar documentos nos resultados da pesquisa em objetos do `Hotel`tipo.

> [!NOTE]
> Você pode encontrar mais informações sobre a sintaxe de expressão de consulta de pesquisa [aqui](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Por fim, depois de cada consulta, esse método itera todas as correspondências nos resultados da pesquisa, imprimindo cada documento no console:

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

Vamos examinar mais de perto cada uma das consultas por vez. Este é o código para executar a primeira consulta:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

Nesse caso, Estamos pesquisando o índice inteiro para a palavra "Motel" em qualquer campo pesquisável e só desejamos recuperar os nomes dos hotéis, conforme especificado pelo `Select` parâmetro. Estes são os resultados:

    Name: Secret Point Motel

    Name: Twin Dome Motel

A próxima consulta é um pouco mais interessante.  Queremos encontrar os hotéis que têm uma sala com uma taxa noturna de menos de $100 e retornam apenas a ID e a descrição do Hotel:

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

Essa consulta usa uma expressão `$filter` OData, `Rooms/any(r: r/BaseRate lt 100)`, para filtrar os documentos no índice. Isso usa o [operador any](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators) para aplicar o ' BaseRate lt 100 ' a todos os itens na coleção Rooms. Você pode saber mais sobre a sintaxe do OData que Azure Search dá suporte [aqui](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax).

Estes são os resultados da consulta:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

Em seguida, queremos encontrar os dois principais hotéis que foram mais recentemente renovadosdos e mostrar o nome do hotel e a data da última reforma. Apresentamos o código a seguir: 

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

Nesse caso, usamos novamente a sintaxe OData para especificar o `OrderBy` parâmetro como. `lastRenovationDate desc` Também definimos `Top` como 2 para garantir que só obtemos os dois principais documentos. Como antes, definimos `Select` para especificar quais campos devem ser retornados.

Estes são os resultados:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Finalmente, queremos encontrar todos os nomes de hotéis que correspondam à palavra "Hotel":

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

E aqui estão os resultados, que incluem todos os campos, já que nós não `Select` especificamos a propriedade:

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

Esta etapa conclui o tutorial, mas não pare aqui. \* * As próximas etapas fornecem recursos adicionais para saber mais sobre Azure Search.

## <a name="next-steps"></a>Passos seguintes
* Procure as referências para o [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) e a [API REST](https://docs.microsoft.com/rest/api/searchservice/).
* Examine as [convenções de nomenclatura](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) para aprender as regras para nomear vários objetos.
* Examine [os tipos de dados com suporte](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) no Azure Search.
