---
title: Como utilizar o Azure Search a partir de um aplicativo .NET - Azure Search
description: Saiba como utilizar o Azure Search num aplicativo .NET usando C# e o SDK de .NET. Tarefas baseadas em código incluem a ligar ao serviço, indexar conteúdo e consultar um índice.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: afc60e933c9fcc154af74c47e382d8b8e7b0df8d
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286317"
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>Como utilizar o Azure Search a partir de uma aplicação .NET
Este artigo é um passo a passo para ajudá-lo em execução com o [SDK .NET da Azure Search](https://aka.ms/search-sdk). Pode utilizar o SDK de .NET para implementar uma experiência de pesquisa avançadas na sua aplicação com o Azure Search.

## <a name="whats-in-the-azure-search-sdk"></a>Novidades no Azure SDK de pesquisa
O SDK é constituído por algumas bibliotecas de cliente que permitem-lhe gerir os índices, sinónimos, indexadores e origens de dados mapeia, bem como carregar e gerir documentos e executar consultas, tudo sem ter que lidar com os detalhes de HTTP e JSON. Estas bibliotecas de cliente são distribuídas como pacotes de NuGet.

O pacote NuGet principal é `Microsoft.Azure.Search`, que é um pacote de metadados que inclui todos os outros pacotes como dependências. Utilize este pacote, se estiver apenas começando, ou se souber que seu aplicativo precisará todos os recursos do Azure Search.

São os outros pacotes de NuGet no SDK:
 
  - `Microsoft.Azure.Search.Data`: Utilize este pacote, se estiver desenvolvendo um aplicativo do .NET com o Azure Search e só tem de consultar ou atualizar documentos em índices. Se também terá de criar ou atualizar índices, mapas de sinónimos, ou outros recursos de nível de serviço, utilizam o `Microsoft.Azure.Search` em vez disso, o pacote.
  - `Microsoft.Azure.Search.Service`: Utilize este pacote, se estiver a desenvolver automatização no .NET para gerir os índices da Azure Search, mapas de sinónimos, indexadores, origens de dados ou outros recursos de nível de serviço. Se só precisa de documentos de consulta ou atualização em seus índices, utilize o `Microsoft.Azure.Search.Data` em vez disso, o pacote. Se precisar de todas as funcionalidades do Azure Search, utilize o `Microsoft.Azure.Search` em vez disso, o pacote.
  - `Microsoft.Azure.Search.Common`: Tipos comuns necessários para as bibliotecas .NET de pesquisa do Azure. Não é preciso usar esse pacote diretamente em seu aplicativo; Destina-se apenas a ser utilizado como uma dependência.

As várias bibliotecas de cliente definem classes como `Index`, `Field`, e `Document`, bem como operações como `Indexes.Create` e `Documents.Search` no `SearchServiceClient` e `SearchIndexClient` classes. Essas classes estão organizadas nas seguintes espaços de nomes:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

A versão atual do SDK de .NET da Azure Search está agora em disponibilidade geral. Se quiser enviar comentários para nós incorporar na próxima versão, visite nosso [página de comentários](https://feedback.azure.com/forums/263029-azure-search/).

O SDK de .NET suporta a versão `2017-11-11` das [API de REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/). Esta versão inclui agora suporte para sinónimos, bem como melhoramentos incrementais para indexadores. Funcionalidades de pré-visualização *não* parte desta versão, como o suporte para indexação de matrizes JSON e arquivos CSV, estão na [pré-visualização](search-api-2016-09-01-preview.md) e disponível por meio de [versão 4.0-pré-visualização do .NET SDK](https://aka.ms/search-sdk-preview).

Este SDK não suporta [operações de gestão](https://docs.microsoft.com/rest/api/searchmanagement/) , tais como criar e dimensionar os serviços de pesquisa e gerir chaves de API. Se precisar de gerir os recursos de pesquisa a partir de uma aplicação .NET, pode utilizar o [SDK de gestão do Azure Search .NET](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Atualizar para a versão mais recente do SDK
Se já estiver a utilizar uma versão mais antiga do SDK de .NET de pesquisa do Azure e pretende atualizar para a nova versão disponível em geral, [este artigo](search-dotnet-sdk-migration-version-5.md) explica como.

## <a name="requirements-for-the-sdk"></a>Requisitos para o SDK
1. O Visual Studio 2017.
2. O próprio serviço de Azure Search. Para utilizar o SDK, terá o nome do seu serviço e uma ou mais chaves de API. [Criar um serviço no portal do](search-create-service-portal.md) irá ajudá-lo a esses passos.
3. Transferir o SDK .NET da Azure Search [pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search) utilizando "Gerir pacotes NuGet" no Visual Studio. Procurar pelo nome do pacote `Microsoft.Azure.Search` NuGet.org (ou uma das outras, se tiver apenas um subconjunto da funcionalidade do pacote nomes acima).

O SDK .NET da Azure Search oferece suporte a aplicativos, visando o .NET Framework 4.5.2 e versões superiores, bem como o .NET Core.

## <a name="core-scenarios"></a>Principais cenários
Há várias coisas que precisará fazer no seu aplicativo de pesquisa. Neste tutorial, iremos abranger esses cenários principais:

* Criar um índice
* Preenchimento do índice com documentos
* Pesquisa de documentos através da pesquisa de texto completo e filtros

O código de exemplo seguinte ilustra cada uma delas. Pode utilizar os fragmentos de código em seu próprio aplicativo.

### <a name="overview"></a>Descrição geral
Vamos explorar a aplicação de exemplo cria um novo índice com o nome "Hotéis", preenche-a com alguns documentos, em seguida, executa algumas consultas de pesquisa. Este é o programa principal, que mostra o fluxo geral:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteHotelsIndexIfExists(serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateHotelsIndex(serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

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
DeleteHotelsIndexIfExists(serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateHotelsIndex(serviceClient);
```

Em seguida, o índice tem de ser preenchidos. Para fazer isso, temos um `SearchIndexClient`. Existem duas formas de obter uma: construindo-lo, ou chamando `Indexes.GetClient` sobre o `SearchServiceClient`. Podemos usar a segunda opção para sua comodidade.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> Numa aplicação de pesquisa normal, a gestão de índice e a população são processadas por um componente separado das consultas de pesquisa. `SearchCredentials` é útil para preencher um índice, uma vez que lhe poupa o trabalho de proporcionar outras `Indexes.GetClient`. Este é realizado através da transferência da chave de administração que utilizou para criar o `SearchServiceClient` para o novo `SearchIndexClient`. No entanto, na parte da sua aplicação que executa as consultas, é melhor criar o `SearchIndexClient` diretamente para que possa transferir uma chave de consulta em vez de uma chave de administração. Isso é consistente com o princípio de privilégio mínimo e irá ajudar a tornar a sua aplicação mais segura. Pode encontrar mais informações sobre chaves de administração e chaves de consulta [aqui](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Agora que temos um `SearchIndexClient`, pode preencher o índice. Isso é feito através de outro método que percorreremos mais tarde.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Por fim, vamos executar algumas consultas de pesquisa e exibir os resultados. Dessa vez usamos outro `SearchIndexClient`:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

RunQueries(indexClientForQueries);
```

Podemos irá dar uma olhada mais de perto o `RunQueries` método mais tarde. Aqui está o código para criar o novo `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Desta vez, iremos utilizar uma chave de consulta, uma vez que não é necessário acesso de escrita para o índice. Pode introduzir estas informações no `appsettings.json` ficheiros do [aplicação de exemplo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Se executar esse aplicativo com um nome de serviço válido e chaves de API, o resultado deve ser assim:

    Deleting index...
    
    Creating index...
    
    Uploading documents...
    
    Waiting for documents to be indexed...
    
    Search the entire index for the term 'budget' and return only the hotelName field:
    
    Name: Roach Motel
    
    Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:
    
    ID: 2   Description: Cheapest hotel in town
    ID: 3   Description: Close to town hall and the river
    
    Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:
    
    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00
    
    Search the entire index for the term 'motel':
    
    ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577
    
    Complete.  Press any key to end application...

O código-fonte completo do aplicativo é fornecido no final deste artigo.

Em seguida, tomaremos olhar detalhadamente cada um dos métodos chamados pelo `Main`.

### <a name="creating-an-index"></a>Criar um índice
Depois de criar uma `SearchServiceClient`, `Main` elimina o índice "Hotéis" se já existir. Que é feito com o seguinte método:

```csharp
private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists("hotels"))
    {
        serviceClient.Indexes.Delete("hotels");
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
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
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

Além dos campos, também pode adicionar perfis de classificação, sugestores ou opções CORS para o índice (elas são omitidas do exemplo para fins de brevidade). Pode encontrar mais informações sobre o objeto de índice e suas partes constituintes no [referência do SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index), e na [referência da API REST da Azure Search](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Preenchimento do índice
O próximo passo `Main` é preencher o índice recentemente criado. Isso é feito no método seguinte:

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        { 
            HotelId = "1", 
            BaseRate = 199.0, 
            Description = "Best hotel in town",
            DescriptionFr = "Meilleur hôtel en ville",
            HotelName = "Fancy Stay",
            Category = "Luxury", 
            Tags = new[] { "pool", "view", "wifi", "concierge" },
            ParkingIncluded = false, 
            SmokingAllowed = false,
            LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
            Rating = 5, 
            Location = GeographyPoint.Create(47.678581, -122.131577)
        },
        new Hotel()
        { 
            HotelId = "2", 
            BaseRate = 79.99,
            Description = "Cheapest hotel in town",
            DescriptionFr = "Hôtel le moins cher en ville",
            HotelName = "Roach Motel",
            Category = "Budget",
            Tags = new[] { "motel", "budget" },
            ParkingIncluded = true,
            SmokingAllowed = true,
            LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
            Rating = 1,
            Location = GeographyPoint.Create(49.678581, -122.131577)
        },
        new Hotel() 
        { 
            HotelId = "3", 
            BaseRate = 129.99,
            Description = "Close to town hall and the river"
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

Este método tem quatro partes. O primeiro cria uma matriz de `Hotel` objetos que irão servir como nossos dados de entrada para carregar para o índice. Estes dados estão hard-coded para manter a simplicidade. Em seu próprio aplicativo, seus dados provavelmente serão proveniente de uma origem de dados externos, como uma base de dados SQL.

A segunda parte cria um `IndexBatch` que contém os documentos. Especificar a operação que pretende aplicar para o batch no momento criá-la, neste caso, chamando `IndexBatch.Upload`. O batch, em seguida, é carregado para o índice da Azure Search pelo `Documents.Index` método.

> [!NOTE]
> Neste exemplo, estamos apenas estiver a carregar documentos. Se quiser mesclar alterações em documentos existentes ou eliminar documentos, pode criar lotes chamando `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, ou `IndexBatch.Delete` em vez disso. Também pode misturar operações diferentes num único lote chamando `IndexBatch.New`, que aceita um conjunto de `IndexAction` objetos, cada um dos quais informa a Azure Search para executar uma determinada operação num documento. Pode criar cada `IndexAction` com a sua própria operação chamando o método correspondente como `IndexAction.Merge`, `IndexAction.Upload`e assim por diante.
> 
> 

A terceira parte desse método é um bloco catch que processa um caso de erro importantes para indexação. Se o serviço Azure Search não consegue indexar alguns dos documentos no lote, uma `IndexBatchException` é emitida pelo `Documents.Index`. Isto pode acontecer se indexar documentos enquanto o seu serviço estiver sobrecarregado. **Recomendamos vivamente que processe explicitamente este caso no seu código.** Pode atrasar e, em seguida, repetir a indexação dos documentos que falharam, pode iniciar sessão e continuar como no exemplo ou pode fazer algo diferente dependendo dos requisitos de consistência de dados da aplicação.

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

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }
}
```

A primeira coisa a observar é que cada propriedade pública de `Hotel` corresponde a um campo na definição do índice, mas com uma diferença crucial: O nome de cada campo começa com uma letra minúscula ("camel case"), enquanto o nome de cada propriedade pública de `Hotel` começa com uma letra maiúscula ("Pascal case"). Este é um cenário comum em aplicações .NET que realizam enlace de dados no qual o esquema de destino está fora do controlo do programador da aplicação. Em vez de ter de violar as diretrizes de nomenclatura .NET aplicando o estilo camel-case aos nomes de propriedade, pode indicar ao SDK para mapear os nomes das propriedades no estilo camel-case automaticamente com o atributo `[SerializePropertyNamesAsCamelCase]`.

> [!NOTE]
> O SDK .NET da Azure Search utiliza a biblioteca [NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) para serializar e anular a serialização dos objetos do modelo personalizado de e para JSON. Se necessário, pode personalizar esta serialização. Para obter mais detalhes, consulte [personalizados de serialização com JSON.NET](#JsonDotNet).
> 
> 

A segunda coisa a observar é os atributos que decoram cada propriedade pública (por exemplo, `IsFilterable`, `IsSearchable`, `Key`, e `Analyzer`). Esses atributos são mapeados diretamente para o [atributos correspondentes do índice da Azure Search](https://docs.microsoft.com/rest/api/searchservice/create-index#request). O `FieldBuilder` classe utiliza-os para construir as definições de campo para o índice.

O terceiro ponto importante sobre o `Hotel` classe é os tipos de dados das propriedades públicas. Os tipos .NET destas propriedades mapeiam para os tipos de campo equivalentes na definição do índice. Por exemplo, a propriedade da cadeia `Category` mapeia para o campo `category`, que é do tipo `Edm.String`. Existem mapeamentos de tipo semelhantes entre `bool?` e `Edm.Boolean`, `DateTimeOffset?` e `Edm.DateTimeOffset`, etc. As regras específicas para o mapeamento do tipo estão documentadas com o método `Documents.Get` em [Azure Search .NET SDK reference (Referência SDK .NET do Azure Search)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get). O `FieldBuilder` classe se encarrega desse mapeamento para, mas ainda pode ser útil compreender caso precise resolver quaisquer problemas de serialização.

Essa capacidade de usar suas próprias classes, como documentos funciona em ambas as direções; Também pode obter os resultados da pesquisa e ter o SDK automaticamente tirar de série-los a um tipo de sua escolha, como veremos na próxima seção.

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
O SDK utiliza JSON.NET para serializar e anular a serialização de documentos. Pode personalizar a serialização e desserialização se for necessário, definindo suas próprias `JsonConverter` ou `IContractResolver` (consulte a [JSON.NET documentação](https://www.newtonsoft.com/json/help/html/Introduction.htm) para obter mais detalhes). Isso pode ser útil quando quer adaptar uma classe de modelo existente de seu aplicativo para uso com o Azure Search e outros cenários mais avançados. Por exemplo, com a serialização personalizada, pode:

* Incluir ou excluir determinadas propriedades de classe do modelo de que está a ser armazenados como campos de documentos.
* Mapear entre os nomes das propriedades em seu código e os nomes de campos no seu índice.
* Crie atributos personalizados que podem ser utilizados para mapear as propriedades para campos de documentos.

Pode encontrar exemplos de implementação de serialização personalizada nos testes de unidade para o SDK de .NET de pesquisa do Azure no GitHub. É um ponto de partida [nesta pasta](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models). Ela contém classes que são utilizadas pelos testes de serialização personalizada.

### <a name="searching-for-documents-in-the-index"></a>Pesquisa de documentos no índice
A última etapa no aplicativo de exemplo é procurar alguns documentos no índice. O método a seguir faz isso:

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "hotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("budget", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "baseRate lt 150",
            Select = new[] { "hotelId", "description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "lastRenovationDate desc" },
            Select = new[] { "hotelName", "lastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'motel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);
}
```

Sempre que ele executa uma consulta, este método cria uma nova `SearchParameters` objeto. Isto é utilizado para especificar opções adicionais para a consulta, como a classificação, filtragem, paginação e facetamento. Nesse método, estamos definindo a `Filter`, `Select`, `OrderBy`, e `Top` propriedade para consultas diferentes. Todos os `SearchParameters` propriedades estão documentadas [aqui](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

A próxima etapa é realmente executar a consulta de pesquisa. Isso é feito usando o `Documents.Search` método. Para cada consulta, passamos o texto de pesquisa para utilizar como uma cadeia de caracteres (ou `"*"` se não houver nenhum texto de pesquisa), além dos parâmetros de pesquisa que criou anteriormente. Também especificamos `Hotel` como o parâmetro de tipo para `Documents.Search`, que informa o SDK para desserializar documentos nos resultados da pesquisa em objetos do tipo `Hotel`.

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
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);
```

Neste caso, estamos procurando hotéis que correspondem a palavra "orçamento", e queremos obter apenas os nomes do hotel, tal como especificado pelo `Select` parâmetro. Aqui estão os resultados:

    Name: Roach Motel

Em seguida, queremos localizar hotéis com uma taxa noturna de menos de US $150 e devolver apenas o ID de hotel e a descrição:

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Esta consulta utiliza um OData `$filter` expressão, `baseRate lt 150`, para filtrar os documentos no índice. Pode encontrar mais informações sobre a sintaxe OData que suporte o Azure Search [aqui](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search).

Aqui estão os resultados da consulta:

    ID: 2   Description: Cheapest hotel in town
    ID: 3   Description: Close to town hall and the river

Em seguida, queremos encontrar os duas hotéis principais que têm sido mais recentemente renovated e mostrar o nome de hotel e a última data de renovação. Apresentamos o código a seguir: 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Neste caso, estamos novamente Utilize sintaxe OData para especificar a `OrderBy` parâmetro como `lastRenovationDate desc`. Também definimos `Top` para 2 para garantir que obtém apenas os documentos de duas principais. Como antes, definimos `Select` para especificar os campos que devem ser devolvidos.

Aqui estão os resultados:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Por fim, queremos localizar hotéis todos os que correspondem a palavra "hotel":

```csharp
parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

E aqui estão os resultados, que incluem todos os campos, uma vez que estamos não especificou o `Select` propriedade:

    ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

Este passo conclui o tutorial, mas não pare aqui. * * Próximas etapas fornecem recursos adicionais para saber mais sobre o Azure Search.

## <a name="next-steps"></a>Passos Seguintes
* Procure as referências para o [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) e a [API REST](https://docs.microsoft.com/rest/api/searchservice/).
* Revisão [convenções de nomenclatura](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) para saber as regras para atribuição de nomes de vários objetos.
* Revisão [tipos de dados suportados](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) no Azure Search.
