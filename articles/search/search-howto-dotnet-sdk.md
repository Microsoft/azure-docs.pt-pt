---
title: Usar o Pesquisa Cognitiva do Azure no .NET
titleSuffix: Azure Cognitive Search
description: Saiba como usar o Azure Pesquisa Cognitiva em um aplicativo .NET usando C# o e o SDK do .net. As tarefas baseadas em código incluem conectar-se ao serviço, índice de conteúdo e consultar um índice.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 54fcd1fb936b5dd41715798408b604106a24bcf9
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112586"
---
# <a name="how-to-use-azure-cognitive-search-from-a-net-application"></a>Como usar o Azure Pesquisa Cognitiva de um aplicativo .NET

Este artigo é explicativo para colocar em funcionamento o [SDK do .net pesquisa cognitiva do Azure](https://aka.ms/search-sdk). Você pode usar o SDK do .NET para implementar uma experiência de pesquisa avançada em seu aplicativo usando o Azure Pesquisa Cognitiva.

## <a name="whats-in-the-azure-cognitive-search-sdk"></a>O que há no SDK de Pesquisa Cognitiva do Azure
O SDK consiste em algumas bibliotecas de cliente que permitem gerenciar seus índices, fontes de dados, indexadores e mapas de sinônimos, bem como carregar e gerenciar documentos e executar consultas, tudo isso sem precisar lidar com os detalhes de HTTP e JSON. Essas bibliotecas de cliente são todas distribuídas como pacotes NuGet.

O pacote principal do NuGet é `Microsoft.Azure.Search`, que é um meta-Package que inclui todos os outros pacotes como dependências. Use este pacote se você estiver apenas começando ou se souber que seu aplicativo precisará de todos os recursos do Azure Pesquisa Cognitiva.

Os outros pacotes NuGet no SDK são:
 
  - `Microsoft.Azure.Search.Data`: Use este pacote se você estiver desenvolvendo um aplicativo .NET usando o Pesquisa Cognitiva do Azure e precisar consultar ou atualizar documentos em seus índices. Se você também precisar criar ou atualizar índices, mapas de sinônimos ou outros recursos de nível de serviço, use o pacote `Microsoft.Azure.Search` em vez disso.
  - `Microsoft.Azure.Search.Service`: Use este pacote se você estiver desenvolvendo automação no .NET para gerenciar índices de Pesquisa Cognitiva do Azure, mapas de sinônimos, indexadores, fontes de dados ou outros recursos de nível de serviço. Se você precisar apenas consultar ou atualizar documentos em seus índices, use o pacote `Microsoft.Azure.Search.Data` em vez disso. Se você precisar de toda a funcionalidade do Pesquisa Cognitiva do Azure, use o pacote `Microsoft.Azure.Search` em vez disso.
  - `Microsoft.Azure.Search.Common`: tipos comuns necessários para as bibliotecas .NET do Azure Pesquisa Cognitiva. Você não precisa usar esse pacote diretamente em seu aplicativo. Ele destina-se apenas a ser usado como uma dependência.

As várias bibliotecas de cliente definem classes como `Index`, `Field`e `Document`, bem como operações como `Indexes.Create` e `Documents.Search` nas classes `SearchServiceClient` e `SearchIndexClient`. Essas classes são organizadas nos seguintes namespaces:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Se você quiser fornecer comentários para uma atualização futura do SDK, consulte nossa [página de comentários](https://feedback.azure.com/forums/263029-azure-search/) ou crie um problema no [GitHub](https://github.com/azure/azure-sdk-for-net/issues) e mencione "Azure pesquisa cognitiva" no título do problema.

O SDK do .NET dá suporte à versão `2019-05-06` da [API REST do Azure pesquisa cognitiva](https://docs.microsoft.com/rest/api/searchservice/). Esta versão inclui suporte para [tipos complexos](search-howto-complex-data-types.md), [enriquecimento de ia](cognitive-search-concept-intro.md), [preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete)e modo de [análise de JsonLines](search-howto-index-json-blobs.md) ao indexar BLOBs do Azure. 

Esse SDK não oferece suporte a [operações de gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/) , como criação e dimensionamento de serviços de pesquisa e gerenciamento de chaves de API. Se você precisar gerenciar seus recursos de pesquisa de um aplicativo .NET, poderá usar o [SDK de gerenciamento .net pesquisa cognitiva do Azure](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Atualizando para a versão mais recente do SDK
Se você já estiver usando uma versão mais antiga do SDK do .NET Pesquisa Cognitiva do Azure e quiser atualizar para a versão mais recente disponível, [Este artigo](search-dotnet-sdk-migration-version-9.md) explicará como.

## <a name="requirements-for-the-sdk"></a>Requisitos para o SDK
1. Visual Studio 2017 ou posterior.
2. Seu próprio serviço de Pesquisa Cognitiva do Azure. Para usar o SDK, você precisará do nome do seu serviço e de uma ou mais chaves de API. [Criar um serviço no portal](search-create-service-portal.md) ajudará você a usar essas etapas.
3. Baixe o [pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search) do SDK do .net pesquisa cognitiva do Azure usando "gerenciar pacotes NuGet" no Visual Studio. Basta procurar o nome do pacote `Microsoft.Azure.Search` em NuGet.org (ou um dos outros nomes de pacote acima se você precisar apenas de um subconjunto da funcionalidade).

O SDK do .NET Pesquisa Cognitiva do Azure dá suporte a aplicativos destinados ao .NET Framework 4.5.2 e superior, bem como ao .NET Core 2,0 e superior.

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

Vamos percorrer este passo a passo. Primeiro, precisamos criar um novo `SearchServiceClient`. Esse objeto permite que você gerencie índices. Para construir um, você precisa fornecer o nome do serviço de Pesquisa Cognitiva do Azure, bem como uma chave de API de administrador. Você pode inserir essas informações no arquivo `appsettings.json` do aplicativo de [exemplo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

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
> Se você fornecer uma chave incorreta (por exemplo, uma chave de consulta onde uma chave de administrador era necessária), a `SearchServiceClient` lançará um `CloudException` com a mensagem de erro "proibido" na primeira vez em que você chamar um método de operação, como `Indexes.Create`. Se isso acontecer com você, verifique novamente nossa chave de API.
> 
> 

As próximas linhas chamam métodos para criar um índice chamado "Hotéis", excluindo-o primeiro se ele já existir. Abordaremos esses métodos um pouco mais tarde.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Em seguida, o índice precisa ser preenchido. Para popular o índice, precisaremos de um `SearchIndexClient`. Há duas maneiras de obter uma: ao construí-la ou ao chamar `Indexes.GetClient` no `SearchServiceClient`. Usamos o último para sua conveniência.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> Em um aplicativo de pesquisa típico, o gerenciamento de índice e a população podem ser tratados por um componente separado das consultas de pesquisa. `Indexes.GetClient` é conveniente para popular um índice porque ele poupa o problema de fornecer `SearchCredentials`adicionais. Este é realizado através da transferência da chave de administração que utilizou para criar o `SearchServiceClient` para o novo `SearchIndexClient`. No entanto, na parte do seu aplicativo que executa consultas, é melhor criar o `SearchIndexClient` diretamente para que você possa passar uma chave de consulta, que só permite a leitura de dados, em vez de uma chave de administração. Isso é consistente com o princípio de privilégios mínimos e ajudará a tornar seu aplicativo mais seguro. Você pode encontrar mais informações sobre chaves de administração e chaves de consulta [aqui](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Agora que temos uma `SearchIndexClient`, podemos popular o índice. A população de índice é feita por outro método que veremos posteriormente.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Por fim, executamos algumas consultas de pesquisa e exibimos os resultados. Desta vez, usamos um `SearchIndexClient`diferente:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

Vamos examinar mais de perto o método `RunQueries` mais tarde. Este é o código para criar o novo `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Desta vez, usamos uma chave de consulta, pois não precisamos de acesso de gravação ao índice. Você pode inserir essas informações no arquivo `appsettings.json` do aplicativo de [exemplo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Se você executar esse aplicativo com um nome de serviço válido e chaves de API, a saída deverá ser semelhante a este exemplo: (alguma saída de console foi substituída por "..." para fins de ilustração.)

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

Em seguida, vamos examinar mais de perto cada um dos métodos chamados por `Main`.

### <a name="creating-an-index"></a>Criando um índice
Depois de criar um `SearchServiceClient`, `Main` exclui o índice "Hotéis", caso ele já exista. Essa exclusão é feita pelo seguinte método:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Esse método usa o `SearchServiceClient` fornecido para verificar se o índice existe e, nesse caso, excluí-lo.

> [!NOTE]
> O código de exemplo neste artigo usa os métodos síncronos do SDK do .NET do Azure Pesquisa Cognitiva para simplificar. Recomendamos que utilize os métodos assíncronos nas suas próprias aplicações para mantê-las escaláveis e responsivas. Por exemplo, no método acima, você pode usar `ExistsAsync` e `DeleteAsync` em vez de `Exists` e `Delete`.
> 
> 

Em seguida, `Main` cria um novo índice "Hotéis" chamando este método:

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

Esse método cria um novo objeto `Index` com uma lista de objetos `Field` que definem o esquema do novo índice. Cada campo tem um nome, tipo de dados e vários atributos que definem seu comportamento de pesquisa. A classe `FieldBuilder` usa a reflexão para criar uma lista de objetos `Field` para o índice examinando as propriedades públicas e os atributos da classe de modelo `Hotel` fornecida. Vamos examinar mais de perto a classe `Hotel` mais tarde.

> [!NOTE]
> Você sempre pode criar a lista de objetos `Field` diretamente, em vez de usar `FieldBuilder` se necessário. Por exemplo, talvez você não queira usar uma classe de modelo ou pode precisar usar uma classe de modelo existente que não deseja modificar adicionando atributos.
>
> 

Além dos campos, você também pode adicionar perfis de pontuação, sugestores ou opções de CORS ao índice (esses parâmetros são omitidos do exemplo para fins de brevidade). Você pode encontrar mais informações sobre o objeto de índice e suas partes constituintes na [referência do SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index), bem como na [referência da API REST do Azure pesquisa cognitiva](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Populando o índice
A próxima etapa no `Main` popula o índice recém-criado. Essa população de índice é feita no seguinte método: (algum código foi substituído por "..." para fins de ilustração.  Consulte a solução de exemplo completo para obter o código completo de população de dados.)

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

Esse método tem quatro partes. O primeiro cria uma matriz de 3 `Hotel` objetos cada um com 3 `Room` objetos que servirão como nossos dados de entrada para carregar no índice. Esses dados são embutidos em código para simplificar. Em seu próprio aplicativo, seus dados provavelmente serão provenientes de uma fonte de dados externa, como um SQL Database.

A segunda parte cria um `IndexBatch` que contém os documentos. Você especifica a operação que deseja aplicar ao lote no momento em que criá-lo, nesse caso, chamando `IndexBatch.Upload`. Em seguida, o lote é carregado no índice de Pesquisa Cognitiva do Azure pelo método `Documents.Index`.

> [!NOTE]
> Neste exemplo, estamos apenas carregando documentos. Se você quisesse mesclar alterações em documentos existentes ou excluir documentos, você pode criar lotes chamando `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`ou `IndexBatch.Delete` em vez disso. Você também pode misturar operações diferentes em um único lote chamando `IndexBatch.New`, que usa uma coleção de objetos `IndexAction`, cada um dos quais diz ao Azure Pesquisa Cognitiva para executar uma operação específica em um documento. Você pode criar cada `IndexAction` com sua própria operação chamando o método correspondente, como `IndexAction.Merge`, `IndexAction.Upload`e assim por diante.
> 
> 

A terceira parte desse método é um bloco catch que manipula um caso de erro importante para indexação. Se o serviço de Pesquisa Cognitiva do Azure falhar ao indexar alguns dos documentos no lote, uma `IndexBatchException` será gerada pelo `Documents.Index`. Essa exceção pode ocorrer se você estiver indexando documentos enquanto seu serviço está sob carga pesada. **Recomendamos vivamente que processe explicitamente este caso no seu código.** Pode atrasar e, em seguida, repetir a indexação dos documentos que falharam, pode iniciar sessão e continuar como no exemplo ou pode fazer algo diferente dependendo dos requisitos de consistência de dados da aplicação.

> [!NOTE]
> Você pode usar o método [`FindFailedActionsToRetry`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) para construir um novo lote contendo apenas as ações que falharam em uma chamada anterior para `Index`. Há uma discussão sobre como usá-lo corretamente [no stackoverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Por fim, o método `UploadDocuments` atrasa por dois segundos. A indexação ocorre de forma assíncrona em seu serviço de Pesquisa Cognitiva do Azure, portanto, o aplicativo de exemplo precisa esperar um pouco de tempo para garantir que os documentos estejam disponíveis para pesquisa. Este género de atrasos são normalmente necessários apenas para demonstrações gratuitas, testes e aplicações de exemplo.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>Como o SDK .NET processa documentos
Você pode estar se perguntando como o SDK .NET do Azure Pesquisa Cognitiva é capaz de carregar instâncias de uma classe definida pelo usuário, como `Hotel` para o índice. Para ajudar a responder a essa pergunta, vamos examinar a classe `Hotel`:

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

A primeira coisa a ser observada é que o nome de cada propriedade pública na classe `Hotel` será mapeado para um campo com o mesmo nome na definição do índice. Se desejar que cada campo comece com uma letra minúscula ("camel case"), você poderá dizer ao SDK para mapear os nomes de propriedade para o camel case automaticamente com o atributo `[SerializePropertyNamesAsCamelCase]` na classe. Esse cenário é comum em aplicativos .NET que executam a vinculação de dados em que o esquema de destino está fora do controle do desenvolvedor do aplicativo sem a necessidade de violar as diretrizes de nomenclatura do "caso do Pascal" no .NET.

> [!NOTE]
> O SDK do .NET Pesquisa Cognitiva do Azure usa a biblioteca [NewtonSoft JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm) para serializar e desserializar seus objetos de modelo personalizados de e para JSON. Se necessário, pode personalizar esta serialização. Para obter mais informações, consulte [serialização personalizada com JSON.net](#JsonDotNet).
> 
> 

A segunda coisa a ser observada é que cada propriedade é decorada com atributos como `IsFilterable`, `IsSearchable`, `Key`e `Analyzer`. Esses atributos são mapeados diretamente para os [atributos de campo correspondentes em um índice de pesquisa cognitiva do Azure](https://docs.microsoft.com/rest/api/searchservice/create-index#request). A classe `FieldBuilder` usa essas propriedades para construir definições de campo para o índice.

A terceira coisa importante sobre a classe `Hotel` é os tipos de dados das propriedades públicas. Os tipos .NET destas propriedades mapeiam para os tipos de campo equivalentes na definição do índice. Por exemplo, a propriedade da cadeia `Category` mapeia para o campo `category`, que é do tipo `Edm.String`. Há mapeamentos de tipo semelhantes entre `bool?`, `Edm.Boolean`, `DateTimeOffset?`e `Edm.DateTimeOffset` e assim por diante. As regras específicas para o mapeamento de tipo são documentadas com o método `Documents.Get` na [referência do SDK .net do Azure pesquisa cognitiva](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get). A classe `FieldBuilder` cuida desse mapeamento para você, mas ainda pode ser útil entender caso você precise solucionar problemas de serialização.

Você observou a propriedade `SmokingAllowed`?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

O atributo `JsonIgnore` nessa propriedade informa ao `FieldBuilder` não serializá-lo para o índice como um campo.  Essa é uma ótima maneira de criar propriedades calculadas no lado do cliente que você pode usar como auxiliares em seu aplicativo.  Nesse caso, a propriedade `SmokingAllowed` reflete se qualquer `Room` na coleção de `Rooms` permite fumante.  Se todos forem falsos, isso indica que o Hotel inteiro não permite fumante.

Algumas propriedades como `Address` e `Rooms` são instâncias de classes .NET.  Essas propriedades representam estruturas de dados mais complexas e, como resultado, exigem campos com um [tipo de dados complexo](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) no índice.

A propriedade `Address` representa um conjunto de vários valores na classe `Address`, definido abaixo:

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

A propriedade `Rooms` representa uma matriz de objetos `Room`:

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
> O SDK .NET do Pesquisa Cognitiva do Azure também dá suporte a documentos de tipo dinâmico usando a classe `Document`, que é um mapeamento de chave/valor de nomes de campo para valores de campo. Isto é útil em cenários onde não sabe qual o esquema de índice no momento da conceção, nem onde seria inconveniente discretizar as classes do modelo específico. Todos os métodos no SDK que lidam com documentos têm sobrecargas que funcionam com a classe `Document`, bem como as sobrecargas de tipo seguro que assumem um parâmetro do tipo genérico. Somente os últimos são usados no código de exemplo neste tutorial. A [classe de`Document`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document) herda de `Dictionary<string, object>`.
> 
>

**Por que deve utilizar tipos de dados anuláveis**

Ao criar suas próprias classes de modelo para mapear para um índice de Pesquisa Cognitiva do Azure, é recomendável declarar Propriedades de tipos de valor, como `bool` e `int` como anuláveis (por exemplo, `bool?` em vez de `bool`). Se utilizar uma propriedade não anulável, terá de **garantir** que não existem documentos no seu índice de contenham um valor nulo para o campo correspondente. Nem o SDK nem o serviço de Pesquisa Cognitiva do Azure o ajudarão a impor isso.

Esta não é apenas uma preocupação hipotética: imagine um cenário onde adiciona um novo campo a um índice existente do tipo `Edm.Int32`. Depois de atualizar a definição do índice, todos os documentos terão um valor nulo para esse novo campo (já que todos os tipos são anuláveis no Pesquisa Cognitiva do Azure). Se, em seguida, utilizar uma classe de modelo com uma propriedade `int` não anulável para esse campo, obterá uma `JsonSerializationException` assim ao tentar obter documentos:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por este motivo, recomendamos que utilize tipos anuláveis nas suas classes de modelos como uma melhor prática.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Serialização personalizada com JSON.NET
O SDK usa JSON.NET para serializar e desserializar documentos. Você pode personalizar a serialização e desserialização, se necessário, definindo seu próprio `JsonConverter` ou `IContractResolver`. Para obter mais informações, consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm). Isso pode ser útil quando você deseja adaptar uma classe de modelo existente do seu aplicativo para uso com o Azure Pesquisa Cognitiva e outros cenários mais avançados. Por exemplo, com a serialização personalizada, você pode:

* Inclua ou exclua determinadas propriedades de sua classe de modelo para serem armazenadas como campos de documento.
* Mapear entre nomes de propriedade em seu código e nomes de campo no índice.
* Crie atributos personalizados que podem ser usados para mapear Propriedades para campos de documento.

Você pode encontrar exemplos de implementação de serialização personalizada nos testes de unidade para o SDK do .NET Pesquisa Cognitiva do Azure no GitHub. Um bom ponto de partida é [essa pasta](https://github.com/Azure/azure-sdk-for-net/tree/4f6f4e4c90200c1b0621c4cead302a91e89f2aba/sdk/search/Microsoft.Azure.Search/tests/Tests/Models). Ele contém classes que são usadas pelos testes de serialização personalizados.

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

Cada vez que ele executa uma consulta, esse método primeiro cria um novo objeto `SearchParameters`. Esse objeto é usado para especificar opções adicionais para a consulta, como classificação, filtragem, paginação e faceta. Nesse método, estamos definindo a `Filter`, `Select`, `OrderBy`e a propriedade `Top` para consultas diferentes. Todas as propriedades de `SearchParameters` estão documentadas [aqui](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

A próxima etapa é realmente executar a consulta de pesquisa. A execução da pesquisa é feita usando o método `Documents.Search`. Para cada consulta, passamos o texto de pesquisa para usar como uma cadeia de caracteres (ou `"*"` se não houver nenhum texto de pesquisa), além dos parâmetros de pesquisa criados anteriormente. Também especificamos `Hotel` como o parâmetro de tipo para `Documents.Search`, que diz ao SDK para desserializar documentos nos resultados da pesquisa em objetos do tipo `Hotel`.

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

Nesse caso, Estamos pesquisando o índice inteiro para a palavra "Motel" em qualquer campo pesquisável e só desejamos recuperar os nomes dos hotéis, conforme especificado pelo parâmetro `Select`. Estes são os resultados:

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

Essa consulta usa uma expressão de `$filter` OData, `Rooms/any(r: r/BaseRate lt 100)`, para filtrar os documentos no índice. Isso usa o [operador any](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators) para aplicar o ' BaseRate lt 100 ' a todos os itens na coleção Rooms. Você pode saber mais sobre a sintaxe do OData que o Azure Pesquisa Cognitiva dá suporte [aqui](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax).

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

Nesse caso, usamos novamente a sintaxe OData para especificar o parâmetro `OrderBy` como `lastRenovationDate desc`. Também definimos `Top` como 2 para garantir que só obtemos os dois principais documentos. Como antes, definimos `Select` para especificar quais campos devem ser retornados.

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

E aqui estão os resultados, que incluem todos os campos, já que nós não especificamos a propriedade `Select`:

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

Esta etapa conclui o tutorial, mas não pare aqui. \* * As próximas etapas fornecem recursos adicionais para aprender mais sobre o Azure Pesquisa Cognitiva.

## <a name="next-steps"></a>Passos seguintes
* Procure as referências para o [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) e a [API REST](https://docs.microsoft.com/rest/api/searchservice/).
* Examine as [convenções de nomenclatura](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) para aprender as regras para nomear vários objetos.
* Examine [os tipos de dados com suporte](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) no pesquisa cognitiva do Azure.
