---
title: Utilize Microsoft.Azure.Search (v10) em .NET
titleSuffix: Azure Cognitive Search
description: Saiba como criar e gerir objetos de pesquisa uma aplicação .NET utilizando C# e a versão 10 do .NET SDK. Os snippets de código demonstram a ligação ao serviço, criando índices e consultas.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 394c87bcd3e4580289fbccc6a31b164f914dc8a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020801"
---
# <a name="how-to-use-microsoftazuresearch-v10-in-a-net-application"></a>Como utilizar o Microsoft.Azure.Search (v10) numa aplicação .NET

Este artigo explica como criar e gerir objetos de pesquisa utilizando C# e [a Pesquisa Cognitiva Azure (v10) .NET SDK](/dotnet/api/overview/azure/search). A versão 10 é a última versão do pacote Microsoft.Azure.Search. Seguindo em frente, novas funcionalidades serão [ lançadas](/dotnet/api/overview/azure/search.documents-readme) emAzure.Search.Docda equipa Azure SDK.

Se tiver projetos de desenvolvimento existentes ou de bordo, continue a utilizar a versão 10. Para novos projetos, ou para utilizar novas funcionalidades, deve transitar uma solução de pesquisa existente para a nova biblioteca.

## <a name="whats-in-version-10"></a>O que está na versão 10

O SDK é composto por algumas bibliotecas de clientes que lhe permitem gerir os seus índices, fontes de dados, indexadores e mapas de sinónimos, bem como carregar e gerir documentos, e executar consultas, tudo sem ter de lidar com os detalhes de HTTP e JSON. Estas bibliotecas de clientes são distribuídas como pacotes NuGet.

O pacote principal do NuGet é `Microsoft.Azure.Search` , que é um meta-pacote que inclui todos os outros pacotes como dependências. Use este pacote se estiver apenas a começar ou se souber que a sua aplicação necessitará de todas as funcionalidades da Azure Cognitive Search.

Os outros pacotes NuGet no SDK são:
 
  - `Microsoft.Azure.Search.Data`: Utilize este pacote se estiver a desenvolver uma aplicação .NET utilizando a Azure Cognitive Search, e só precisa de consultar ou atualizar documentos nos seus índices. Se também precisar de criar ou atualizar índices, mapas de sinónimo ou outros recursos de nível de serviço, utilize o `Microsoft.Azure.Search` pacote em vez disso.
  - `Microsoft.Azure.Search.Service`: Utilize este pacote se estiver a desenvolver automatização em .NET para gerir índices de Pesquisa Cognitiva Azure, mapas de sinónimos, indexadores, fontes de dados ou outros recursos de nível de serviço. Se necessitar apenas de consultar ou atualizar documentos nos seus índices, utilize a `Microsoft.Azure.Search.Data` embalagem. Se precisar de toda a funcionalidade de Pesquisa Cognitiva Azure, use o `Microsoft.Azure.Search` pacote em vez disso.
  - `Microsoft.Azure.Search.Common`: Tipos comuns necessários pelas bibliotecas Azure Cognitive Search .NET. Não precisa de utilizar este pacote diretamente na sua aplicação. Destina-se apenas a ser usada como dependência.

As várias bibliotecas de clientes definem classes `Index` `Field` como, `Document` e, bem como operações como `Indexes.Create` e sobre as classes e `Documents.Search` `SearchServiceClient` `SearchIndexClient` classes. Estas aulas são organizadas nos seguintes espaços de nome:

* [Microsoft.Azure.Search](/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](/dotnet/api/microsoft.azure.search.models)

Se quiser fornecer feedback para uma futura atualização do SDK, consulte a nossa página de [feedback](https://feedback.azure.com/forums/263029-azure-search/) ou crie um problema no [GitHub](https://github.com/azure/azure-sdk-for-net/issues) e mencione "Azure Cognitive Search" no título de edição.

O .NET SDK visa a versão `2019-05-06` da [AZure Cognitive Search REST API](/rest/api/searchservice/). Esta versão inclui suporte para [tipos complexos](search-howto-complex-data-types.md), [enriquecimento de IA,](cognitive-search-concept-intro.md) [autocompleto](/rest/api/searchservice/autocomplete)e [modo de análise JsonLines](search-howto-index-json-blobs.md) ao indexar Azure Blobs. 

Este SDK não suporta [Operações de Gestão](/rest/api/searchmanagement/) como a criação e dimensionamento de serviços de pesquisa e gestão de chaves API. Se precisar de gerir os seus recursos de pesquisa a partir de uma aplicação .NET, pode utilizar o [Azure Cognitive Search .NET Management SDK](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Upgrade para a versão mais recente do SDK
Se já está a utilizar uma versão mais antiga do Azure Cognitive Search .NET SDK e gostaria de fazer upgrade para a versão mais recente geralmente disponível, [este artigo](search-dotnet-sdk-migration-version-9.md) explica como.

## <a name="requirements-for-the-sdk"></a>Requisitos para o SDK
1. Visual Studio 2017 ou mais tarde.
2. O seu próprio serviço de Pesquisa Cognitiva Azure. Para utilizar o SDK, necessitará do nome do seu serviço e de uma ou mais teclas API. [Criar um serviço no portal](search-create-service-portal.md) irá ajudá-lo através destes passos.
3. Descarregue o pacote Azure Cognitive Search .NET SDK [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search) utilizando "Manage NuGet Packages" em Visual Studio. Basta pesquisar o nome do pacote `Microsoft.Azure.Search` no NuGet.org (ou um dos outros nomes de pacotes acima se necessitar apenas de um subconjunto da funcionalidade).

O Azure Cognitive Search .NET SDK suporta aplicações direcionadas para o Quadro .NET 4.5.2 e superior, bem como .NET Core 2.0 e superior.

## <a name="core-scenarios"></a>Cenários centrais
Há várias coisas que terá de fazer no seu pedido de pesquisa. Neste tutorial, cobriremos estes cenários principais:

* Criar um índice
* Povoar o índice com documentos
* Procurar documentos utilizando pesquisa e filtros de texto completo

O seguinte código de amostra ilustra cada um destes cenários. Sinta-se livre para usar os cortes de código na sua própria aplicação.

### <a name="overview"></a>Descrição geral
A aplicação de amostra que vamos explorar cria um novo índice chamado "hotéis", povoa-o com alguns documentos e executa algumas consultas de pesquisa. Aqui está o programa principal, mostrando o fluxo geral:

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

Vamos andar por este passo a passo. Primeiro temos de criar um `SearchServiceClient` novo. Este objeto permite-lhe gerir índices. Para construir um, precisa fornecer o seu nome de serviço de Pesquisa Cognitiva Azure, bem como uma chave API de administração. Pode introduzir esta informação no `appsettings.json` ficheiro do pedido de [amostragem.](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)

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
> Se fornecer uma chave incorreta (por exemplo, uma chave de consulta onde era necessária uma chave de administração), `SearchServiceClient` a mensagem de `CloudException` erro "Proibida" é a primeira vez que chama um método de operação, como `Indexes.Create` . Se isto lhe acontecer, verifique duas vezes a nossa chave API.
> 
> 

As próximas linhas chamam métodos para criar um índice chamado "hotéis", eliminando-o primeiro se já existir. Vamos percorrer estes métodos um pouco mais tarde.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Em seguida, o índice precisa ser povoado. Para preencher o índice, precisaremos de `SearchIndexClient` um. Há duas formas de obter uma: construindo-a, ou apelando `Indexes.GetClient` ao `SearchServiceClient` . Usamos este último para conveniência.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> Numa aplicação de pesquisa típica, a gestão de índices e a população podem ser tratadas por um componente separado das consultas de pesquisa. `Indexes.GetClient` é conveniente para povoar um índice porque poupa-lhe o trabalho de fornecer adicional `SearchCredentials` . Este é realizado através da transferência da chave de administração que utilizou para criar o `SearchServiceClient` para o novo `SearchIndexClient`. No entanto, na parte da sua aplicação que executa consultas, é melhor criar o diretamente para `SearchIndexClient` que possa passar numa chave de consulta, que apenas lhe permite ler dados, em vez de uma chave de administração. Este processo é consistente com o princípio do menor privilégio e irá ajudá-lo a tornar a sua aplicação mais segura. Pode saber mais sobre as chaves de administração e as chaves de consulta [aqui.](/rest/api/searchservice/#authentication-and-authorization)
> 
> 

Agora que temos `SearchIndexClient` um, podemos preencher o índice. A população indexa-se a outro método que iremos percorrer mais tarde.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Finalmente, executamos algumas consultas de pesquisa e exibimos os resultados. Desta vez usamos um `SearchIndexClient` diferente:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

Veremos mais de perto o `RunQueries` método mais tarde. Aqui está o código para criar o `SearchIndexClient` novo:

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Desta vez usamos uma chave de consulta, uma vez que não precisamos de escrever acesso ao índice. Pode introduzir esta informação no `appsettings.json` ficheiro do pedido de [amostragem.](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)

Se executar esta aplicação com um nome de serviço válido e teclas API, a saída deve parecer-se com este exemplo: (Alguma saída da consola foi substituída por "..." para fins de ilustração.)

```output

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
```

O código fonte completo da aplicação é fornecido no final deste artigo.

Em seguida, vamos analisar mais de perto cada um dos métodos chamados por `Main` .

### <a name="creating-an-index"></a>Criar um índice
Depois de criar um `SearchServiceClient` índice de `Main` "hotéis" se já existir. Esta supressão é feita pelo seguinte método:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Este método utiliza o dado `SearchServiceClient` para verificar se o índice existe e, em caso afirmativo, elimine-o.

> [!NOTE]
> O código de exemplo neste artigo utiliza os métodos sincronizados da Pesquisa Cognitiva Azure .NET SDK para a simplicidade. Recomendamos que utilize os métodos assíncronos nas suas próprias aplicações para mantê-las escaláveis e responsivas. Por exemplo, no método acima pode ser utilizado `ExistsAsync` e em vez de `DeleteAsync` `Exists` `Delete` .
> 
> 

Em seguida, `Main` cria um novo índice de "hotéis", chamando este método:

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

Este método cria um novo `Index` objeto com uma lista de `Field` objetos que define o esquema do novo índice. Cada campo tem um nome, tipo de dados e vários atributos que definem o seu comportamento de pesquisa. A `FieldBuilder` classe usa a reflexão para criar uma lista de `Field` objetos para o índice examinando as propriedades públicas e atributos da `Hotel` classe modelo dada. Vamos ver mais de perto a `Hotel` aula mais tarde.

> [!NOTE]
> Pode sempre criar a lista de `Field` objetos diretamente em vez de utilizar `FieldBuilder` se necessário. Por exemplo, pode não querer usar uma classe de modelo ou pode precisar de usar uma classe de modelo existente que não pretende modificar adicionando atributos.
>
> 

Além dos campos, também pode adicionar perfis de pontuação, sugestivos ou opções DE CORS ao Índice (estes parâmetros são omitidos da amostra para a brevidade). Pode encontrar mais informações sobre o objeto Index e as suas partes constituintes na [referência SDK,](/dotnet/api/microsoft.azure.search.models.index)bem como na referência API do [Azure Cognitive Search REST](/rest/api/searchservice/).

### <a name="populating-the-index"></a>Povoando o índice
O próximo passo em `Main` povoar o índice recém-criado. Esta população de índice é feita no seguinte método: (Algum código substituído por "..." para fins de ilustração.  Consulte a solução de amostra completa para o código completo da população de dados.)

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

Este método tem quatro partes. O primeiro cria uma matriz de 3 `Hotel` objetos cada um com 3 `Room` objetos que servirão como os nossos dados de entrada para carregar para o índice. Estes dados são codificados para a simplicidade. Na sua própria aplicação, os seus dados provavelmente virão de uma fonte de dados externa, como uma base de dados SQL.

A segunda parte cria um `IndexBatch` contendo os documentos. Especifica a operação que pretende aplicar ao lote no momento em que o cria, neste caso, ligando `IndexBatch.Upload` . O lote é então enviado para o índice de Pesquisa Cognitiva Azure pelo `Documents.Index` método.

> [!NOTE]
> Neste exemplo, estamos apenas a enviar documentos. Se quiser fundir alterações em documentos existentes ou apagar documentos, poderá criar lotes chamando `IndexBatch.Merge` , ou em vez `IndexBatch.MergeOrUpload` `IndexBatch.Delete` disso. Também pode misturar diferentes operações num único lote, `IndexBatch.New` chamando, que leva uma coleção de `IndexAction` objetos, cada um dos quais diz à Azure Cognitive Search para realizar uma determinada operação num documento. Pode criar cada um `IndexAction` com o seu próprio funcionamento, chamando o método `IndexAction.Merge` `IndexAction.Upload` correspondente, como, e assim por diante.
> 
> 

A terceira parte deste método é um bloco de captura que lida com um caso de erro importante para a indexação. Se o seu serviço de Pesquisa Cognitiva Azure não indexar alguns dos documentos do lote, um `IndexBatchException` é atirado por `Documents.Index` . Esta exceção pode acontecer se estiver a indexar documentos enquanto o seu serviço estiver sob carga pesada. **Recomendamos vivamente que processe explicitamente este caso no seu código.** Pode atrasar e, em seguida, repetir a indexação dos documentos que falharam, pode iniciar sessão e continuar como no exemplo ou pode fazer algo diferente dependendo dos requisitos de consistência de dados da aplicação.

> [!NOTE]
> Pode utilizar o [`FindFailedActionsToRetry`](/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) método para construir um novo lote contendo apenas as ações que falharam numa chamada anterior a `Index` . Há uma discussão sobre como usá-lo corretamente [no StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Finalmente, o `UploadDocuments` método atrasa-se por dois segundos. A indexação ocorre de forma assíncrona no seu serviço de Pesquisa Cognitiva Azure, pelo que a aplicação da amostra precisa de esperar um pouco para garantir que os documentos estão disponíveis para pesquisa. Este género de atrasos são normalmente necessários apenas para demonstrações gratuitas, testes e aplicações de exemplo.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>Como o SDK .NET processa documentos
Pode estar a perguntar-se como é que o Azure Cognitive Search .NET SDK é capaz de carregar instâncias de uma classe definida pelo utilizador como `Hotel` no índice. Para ajudar a responder a esta pergunta, vamos olhar para a `Hotel` aula:

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

A primeira coisa a notar é que o nome de cada propriedade pública na `Hotel` classe irá mapear para um campo com o mesmo nome na definição de índice. Se quiser que cada campo comece com uma letra minúscula ("caso de camelo"), pode dizer ao SDK para mapear os nomes da propriedade para o caso do camelo automaticamente com o `[SerializePropertyNamesAsCamelCase]` atributo na classe. Este cenário é comum em aplicações .NET que realizam ligações de dados onde o esquema-alvo está fora do controlo do desenvolvedor de aplicações sem ter que violar as diretrizes de nomeação "Pascal" em .NET.

> [!NOTE]
> O Azure Cognitive Search .NET SDK utiliza a biblioteca [newtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) para serializar e deserizar os seus objetos de modelo personalizados de e para json. Se necessário, pode personalizar esta serialização. Para mais informações, consulte [Serialização Personalizada com JSON.NET](#JsonDotNet).
> 
> 

A segunda coisa a notar é que cada propriedade está decorada com atributos `IsFilterable` `IsSearchable` como, `Key` `Analyzer` e. Estes atributos mapeiam diretamente aos [atributos de campo correspondentes num índice de Pesquisa Cognitiva Azure](/rest/api/searchservice/create-index). A `FieldBuilder` classe usa estas propriedades para construir definições de campo para o índice.

A terceira coisa importante sobre a `Hotel` classe são os tipos de dados das propriedades públicas. Os tipos .NET destas propriedades mapeiam para os tipos de campo equivalentes na definição do índice. Por exemplo, a propriedade da cadeia `Category` mapeia para o campo `category`, que é do tipo `Edm.String`. Existem mapeamentos de tipo semelhante entre `bool?` , , e assim por `Edm.Boolean` `DateTimeOffset?` `Edm.DateTimeOffset` diante. As regras específicas para o mapeamento do tipo são documentadas com o `Documents.Get` método na [referência Azure Cognitive Search .NET SDK](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get). A `FieldBuilder` classe cuida deste mapeamento para si, mas ainda pode ser útil entender caso precise de resolver problemas de serialização.

Por acaso reparou na `SmokingAllowed` propriedade?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

O `JsonIgnore` atributo nesta propriedade diz para não `FieldBuilder` serializá-lo para o índice como um campo.  Esta é uma ótima maneira de criar propriedades calculadas do lado do cliente que você pode usar como ajudantes na sua aplicação.  Neste caso, a `SmokingAllowed` propriedade reflete se algum na coleção permite `Room` `Rooms` fumar.  Se todos forem falsos, indica que todo o hotel não permite fumar.

Algumas propriedades, como `Address` e `Rooms` são exemplos de classes .NET.  Estas propriedades representam estruturas de dados mais complexas e, como resultado, requerem campos com um [tipo de dados complexo](./search-howto-complex-data-types.md) no índice.

A `Address` propriedade representa um conjunto de múltiplos valores na `Address` classe, definido abaixo:

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

Esta classe contém os valores padrão usados para descrever endereços nos Estados Unidos ou canadá. Você pode usar tipos como este para agrupar campos lógicos juntos no índice.

A `Rooms` propriedade representa uma variedade de `Room` objetos:

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

O seu modelo de dados em .NET e o seu esquema de índice correspondente devem ser concebidos para suportar a experiência de pesquisa que gostaria de dar ao seu utilizador final. Cada objeto de nível superior em .NET, ou seja, documento no índice, corresponde a um resultado de pesquisa que apresentaria na sua interface de utilizador. Por exemplo, numa aplicação de pesquisa de hotel os seus utilizadores finais podem querer pesquisar pelo nome do hotel, funcionalidades do hotel ou as características de um quarto em particular. Cobriremos alguns exemplos de consulta um pouco mais tarde.

Esta capacidade de utilizar as suas próprias aulas para interagir com documentos no índice funciona em ambas as direções; Também pode obter resultados de pesquisa e fazer com que o SDK os deserialize automaticamente para um tipo à sua escolha, como veremos na secção seguinte.

> [!NOTE]
> O Azure Cognitive Search .NET SDK também suporta documentos de tipo dinâmico usando a `Document` classe, que é um mapeamento chave/valor de nomes de campo para valores de campo. Isto é útil em cenários onde não sabe qual o esquema de índice no momento da conceção, nem onde seria inconveniente discretizar as classes do modelo específico. Todos os métodos no SDK que lidam com documentos têm sobrecargas que funcionam com a classe `Document`, bem como as sobrecargas de tipo seguro que assumem um parâmetro do tipo genérico. Apenas estes últimos são utilizados no código de amostra neste tutorial. A [ `Document` classe](/dotnet/api/microsoft.azure.search.models.document) herda `Dictionary<string, object>` de.
> 
>

**Por que deve utilizar tipos de dados anuláveis**

Ao desenhar as suas próprias classes de modelo para mapear para um índice de Pesquisa Cognitiva Azure, recomendamos declarar propriedades de tipos de valor como `bool` e `int` ser anulados (por exemplo, `bool?` em vez `bool` de). Se utilizar uma propriedade não anulável, terá de **garantir** que não existem documentos no seu índice de contenham um valor nulo para o campo correspondente. Nem o SDK nem o serviço de Pesquisa Cognitiva Azure o ajudarão a impor isto.

Esta não é apenas uma preocupação hipotética: imagine um cenário onde adiciona um novo campo a um índice existente do tipo `Edm.Int32`. Após a atualização da definição de índice, todos os documentos terão um valor nulo para esse novo campo (uma vez que todos os tipos são anulados na Pesquisa Cognitiva Azure). Se, em seguida, utilizar uma classe de modelo com uma propriedade `int` não anulável para esse campo, obterá uma `JsonSerializationException` assim ao tentar obter documentos:

```output
Error converting value {null} to type 'System.Int32'. Path 'IntValue'.
```

Por este motivo, recomendamos que utilize tipos anuláveis nas suas classes de modelos como uma melhor prática.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Serialização personalizada com JSON.NET
O SDK usa JSON.NET para serializar e deserizar documentos. Pode personalizar a serialização e a deserialização, se necessário, definindo a sua `JsonConverter` própria ou `IContractResolver` . Para mais informações, consulte a [documentação JSON.NET.](https://www.newtonsoft.com/json/help/html/Introduction.htm) Isto pode ser útil quando pretende adaptar uma classe de modelo existente a partir da sua aplicação para uso com Azure Cognitive Search, e outros cenários mais avançados. Por exemplo, com serialização personalizada pode:

* Incluir ou excluir certas propriedades da sua classe modelo de ser armazenado como campos de documento.
* Mapear entre os nomes de propriedade no seu código e nomes de campo no seu índice.
* Crie atributos personalizados que podem ser usados para mapear propriedades para documentar campos.

Pode encontrar exemplos de implementação de serialização personalizada nos testes de unidade para o Azure Cognitive Search .NET SDK no GitHub. Um bom ponto de partida é [esta pasta.](https://github.com/Azure/azure-sdk-for-net/tree/4f6f4e4c90200c1b0621c4cead302a91e89f2aba/sdk/search/Microsoft.Azure.Search/tests/Tests/Models) Contém aulas que são usadas pelos testes de serialização personalizados.

### <a name="searching-for-documents-in-the-index"></a>Procura de documentos no índice
O último passo no pedido de amostragem é procurar alguns documentos no índice:

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

Cada vez que executa uma consulta, este método cria primeiro um novo `SearchParameters` objeto. Este objeto é utilizado para especificar opções adicionais para a consulta, tais como triagem, filtragem, paging e faceting. Neste método, estamos a definir a `Filter` propriedade e a propriedade para `Select` `OrderBy` `Top` diferentes consultas. Todas as `SearchParameters` propriedades estão documentadas [aqui.](/dotnet/api/microsoft.azure.search.models.searchparameters)

O próximo passo é executar a consulta de pesquisa. A execução da pesquisa é feita usando o `Documents.Search` método. Para cada consulta, passamos o texto de pesquisa para usar como cadeia (ou `"*"` se não existe texto de pesquisa), além dos parâmetros de pesquisa criados anteriormente. Também especificamos `Hotel` como parâmetro de tipo para , que diz ao `Documents.Search` SDK para desseializar documentos na pesquisa resulta em objetos do tipo `Hotel` .

> [!NOTE]
> Pode encontrar mais informações sobre a sintaxe da expressão de consulta de pesquisa [aqui.](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)
> 
> 

Finalmente, após cada consulta este método itera através de todos os jogos nos resultados da pesquisa, imprimindo cada documento à consola:

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

Vamos ver mais de perto cada uma das consultas por sua vez. Aqui está o código para executar a primeira consulta:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

Neste caso, estamos procurando todo o índice para a palavra "motel" em qualquer campo pescandável e só queremos recuperar os nomes do hotel, conforme especificado pelo `Select` parâmetro. Aqui estão os resultados:

```output
Name: Secret Point Motel

Name: Twin Dome Motel
```

A próxima consulta é um pouco mais interessante.  Queremos encontrar qualquer hotel que tenha um quarto com uma diária de menos de $100 e devolva apenas a iD do hotel e descrição:

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

Esta consulta utiliza uma `$filter` expressão OData, para `Rooms/any(r: r/BaseRate lt 100)` filtrar os documentos do índice. Isto utiliza [qualquer operador](./search-query-odata-collection-operators.md) para aplicar o 'BaseRate lt 100' a todos os artigos da coleção Rooms. Pode saber mais sobre a sintaxe OData que a Azure Cognitive Search suporta [aqui.](./query-odata-filter-orderby-syntax.md)

Aqui estão os resultados da consulta:

```output
HotelId: 1
Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

HotelId: 2
Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...
```

Em seguida, queremos encontrar os dois melhores hotéis que foram recentemente renovados, e mostrar o nome do hotel e última data de renovação. Apresentamos o código a seguir: 

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

Neste caso, voltamos a utilizar a sintaxe OData para especificar o `OrderBy` parâmetro como `lastRenovationDate desc` . Também definimos `Top` para 2 para garantir que só obtemos os dois primeiros documentos. Como antes, definimos `Select` para especificar quais os campos devem ser devolvidos.

Aqui estão os resultados:

```output
Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00
```

Finalmente, queremos encontrar todos os nomes de hotéis que correspondam à palavra "hotel":

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

E aqui estão os resultados, que incluem todos os campos uma vez que não especificamos a `Select` propriedade:

```output
    HotelId: 3
    Name: Triple Landscape Hotel
    ...
```

Este passo completa o tutorial, mas não pare aqui. **Os próximos passos fornecem recursos adicionais para aprender mais sobre a Pesquisa Cognitiva Azure.

## <a name="next-steps"></a>Passos seguintes
* Procure as referências para o [SDK do .NET](/dotnet/api/microsoft.azure.search) e a [API REST](/rest/api/searchservice/).
* [Reveja as convenções de nomeação](/rest/api/searchservice/Naming-rules) para aprender as regras para nomear vários objetos.
* Reveja [os tipos de dados suportados](/rest/api/searchservice/Supported-data-types) na Pesquisa Cognitiva Azure.