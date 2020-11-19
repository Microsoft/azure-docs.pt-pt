---
title: Utilize Azure.Search.Documents (v11) em .NET
titleSuffix: Azure Cognitive Search
description: Saiba como criar e gerir objetos de pesquisa numa aplicação .NET utilizando C# e a biblioteca de clientes Azure.Search.Doc(v11). Os snippets de código demonstram a ligação ao serviço, criando índices e consultas.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 3ceead297ea726e256d806c08c22810b39296793
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917176"
---
# <a name="how-to-use-azuresearchdocuments-in-a-c-net-application"></a>Como utilizar Azure.Search.Documents numa aplicação C# .NET

Este artigo explica como criar e gerir objetos de pesquisa usando C# e aAzure.Search.Doca biblioteca [**de clientes**](/dotnet/api/overview/azure/search) (versão 11).

## <a name="about-version-11"></a>Sobre a versão 11

A Azure SDK para .NET adiciona uma nova biblioteca [**de clientesAzure.Search.Docda**](/dotnet/api/overview/azure/search) equipa Azure SDK que é funcionalmente equivalente às bibliotecas de clientes [microsoft.Azure.Search,](/dotnet/api/overview/azure/search/client10) mas utiliza abordagens e convenções comuns quando aplicável. Alguns exemplos incluem [`AzureKeyCredential`](/dotnet/api/azure.azurekeycredential) a autenticação chave, e [System.Text.Js. Serialização](/dotnet/api/system.text.json.serialization) para a serialização do JSON.

Tal como nas versões anteriores, pode utilizar esta biblioteca para:

+ Criar e gerir índices de pesquisa, fontes de dados, indexadores, skillsets e mapas de sinónimo
+ Carregar e gerir documentos de pesquisa num índice
+ Executar consultas, tudo sem ter que lidar com os detalhes de HTTP e JSON

A biblioteca é distribuída como um pacote [ NuGet deAzure.Search.Docúnico,](https://www.nuget.org/packages/Azure.Search.Documents/)que inclui todas as APIs utilizadas para o acesso programático a um serviço de pesquisa.

A biblioteca de clientes define aulas `SearchIndex` `SearchField` como, `SearchDocument` e, bem como operações como `SearchIndexClient.CreateIndex` e sobre as `SearchClient.Search` `SearchIndexClient` `SearchClient` aulas. Estas aulas são organizadas nos seguintes espaços de nome:

+ [`Azure.Search.Documents`](/dotnet/api/azure.search.documents)
+ [`Azure.Search.Documents.Indexes`](/dotnet/api/azure.search.documents.indexes)
+ [`Azure.Search.Documents.Indexes.Models`](/dotnet/api/azure.search.documents.indexes.models)
+ [`Azure.Search.Documents.Models`](/dotnet/api/azure.search.documents.models)

Azure.Search.Documents (versão 11) visa a versão [ `2020-06-30` da Azure Cognitive Search REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/data-plane/Azure.Search/preview/2020-06-30). 

A biblioteca do cliente não presta [operações de gestão de serviços,](/rest/api/searchmanagement/)tais como a criação e dimensionamento de serviços de pesquisa e gestão de chaves API. Se precisar de gerir os seus recursos de pesquisa a partir de uma aplicação .NET, utilize a biblioteca [Microsoft.Azure.Management.Search](/dotnet/api/overview/azure/search/management) no Azure SDK para .NET.

## <a name="upgrade-to-v11"></a>Upgrade para v11

Se tiver usado a versão anterior do .NET SDK e quiser fazer upgrade para a versão geralmente disponível, consulte [a versão 11 do Upgrade para Azure Cognitive Search .NET SDK](search-dotnet-sdk-migration-version-11.md)

## <a name="sdk-requirements"></a>Requisitos do SDK

+ Visual Studio 2019 ou mais tarde.

+ O seu próprio serviço de Pesquisa Cognitiva Azure. Para utilizar o SDK, necessitará do nome do seu serviço e de uma ou mais teclas API. [Crie um serviço no portal](search-create-service-portal.md) se não tiver um.

+ Descarregue o [pacoteAzure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents) usando o Gestor de Pacotes **De Ferramentas**  >  **NuGet**  >  **Gere pacotes nuget para solução** em estúdio visual. Procure o nome do `Azure.Search.Documents` pacote.

Azure SDK para .NET está em conformidade com [a Norma .NET 2.0,](/dotnet/standard/net-standard#net-implementation-support)que significa .NET Framework 4.6.1 e .NET Core 2.0 como requisitos mínimos.

## <a name="example-application"></a>Aplicação de exemplo

Este artigo "ensina por exemplo", baseando-se no exemplo de código [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) no GitHub para ilustrar conceitos fundamentais na Pesquisa Cognitiva Azure - especificamente, como criar, carregar e consultar um índice de pesquisa.

Para o resto deste artigo, assuma um novo índice denominado "hotéis", povoado com alguns documentos, com várias consultas que coincidem com os resultados.

Abaixo está o programa principal, mostrando o fluxo geral:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchIndexClient indexClient = CreateSearchIndexClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, indexClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, indexClient);

    SearchClient searchClient = indexClient.GetSearchClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(searchClient);

    SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

    Console.WriteLine("{0}", "Run queries...\n");
    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

Em seguida, é uma imagem parcial da saída, assumindo que execute esta aplicação com um nome de serviço válido e teclas API:

:::image type="content" source="media/search-howto-dotnet-sdk/console-output.png" alt-text="Saída Console.WriteLine do programa de amostragem":::

### <a name="client-types"></a>Tipos de clientes

A biblioteca de clientes utiliza três tipos de clientes para várias operações: [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) criar, atualizar ou apagar índices, [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) carregar ou consultar um índice, e trabalhar com [`SearchIndexerClient`](/dotnet/api/azure.search.documents.indexes.searchindexerclient) indexadores e skillsets. Este artigo centra-se nos dois primeiros. 

No mínimo, todos os clientes requerem o nome de serviço ou ponto final, e uma chave API. É comum fornecer esta informação num ficheiro de configuração, semelhante ao que se encontra no `appsettings.json` ficheiro da aplicação de [amostra DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo). Para ler a partir do ficheiro de configuração, adicione `using Microsoft.Extensions.Configuration;` ao seu programa.

A seguinte declaração cria o cliente indexa utilizado para criar, atualizar ou eliminar índices. É preciso um ponto final de pesquisa e uma chave API de administração.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(new Uri(searchServiceEndPoint), new AzureKeyCredential(adminApiKey));
    return indexClient;
}
```

A próxima declaração cria o cliente de pesquisa usado para carregar documentos ou executar consultas. `SearchClient` requer um índice. Você precisará de uma chave API de administração para carregar documentos, mas você pode usar uma chave API de consulta para executar consultas. 

```csharp
string indexName = configuration["SearchIndexName"];

private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

> [!NOTE]
> Se fornecer uma chave inválida para a operação de importação (por exemplo, uma chave de consulta onde era necessária uma chave de administração), `SearchClient` lançará uma `CloudException` mensagem de erro "Proibida" na primeira vez que ligar para um método de operação. Se isto lhe acontecer, verifique duas vezes a chave API.
>

### <a name="deleting-the-index"></a>Apagar o índice

Nas fases iniciais de desenvolvimento, é possível incluir uma declaração para eliminar um índice de trabalho em progresso para [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) que possa recriá-lo com uma definição atualizada. O código de amostra para Azure Cognitive Search inclui frequentemente um passo de eliminação para que possa re-executar a amostra.

As seguintes chamadas de `DeleteIndexIfExists` linha:

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, indexClient);
```

Este método utiliza o dado `SearchIndexClient` para verificar se o índice existe e, em caso afirmativo, elimina-o:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchIndexClient indexClient)
{
    try
    {
        if (indexClient.GetIndex(indexName) != null)
        {
            indexClient.DeleteIndex(indexName);
        }
    }
    catch (RequestFailedException e) when (e.Status == 404)
    {
        // Throw an exception if the index name isn't found
        Console.WriteLine("The index doesn't exist. No deletion occurred.");
```

> [!NOTE]
> O código de exemplo neste artigo utiliza os métodos sincronizados para a simplicidade, mas deve utilizar os métodos assíncronos nas suas próprias aplicações para os manter escaláveis e responsivos. Por exemplo, no método acima pode ser utilizado [`DeleteIndexAsync`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindexasync) em vez de [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) .
>

## <a name="create-an-index"></a>Criar um índice

Pode usar [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) para criar um índice. 

O método abaixo cria um novo [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) objeto com uma lista de [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) objetos que definem o esquema do novo índice. Cada campo tem um nome, tipo de dados e vários atributos que definem o seu comportamento de pesquisa. 

Os campos podem ser definidos a partir de uma classe modelo utilizando [`FieldBuilder`](/dotnet/api/azure.search.documents.indexes.fieldbuilder) . A `FieldBuilder` classe usa a reflexão para criar uma lista de `SearchField` objetos para o índice examinando as propriedades públicas e atributos da `Hotel` classe modelo dada. Vamos ver mais de perto a `Hotel` aula mais tarde.

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    indexClient.CreateOrUpdateIndex(definition);
}
```

Além dos campos, também pode adicionar perfis de pontuação, sugestivos ou opções DE CORS ao índice (estes parâmetros são omitidos da amostra para a brevidade). Pode encontrar mais informações sobre o objeto SearchIndex e as suas partes constituintes na [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) lista de propriedades, bem como na [referência REST API.](/rest/api/searchservice/)

> [!NOTE]
> Pode sempre criar a lista de `Field` objetos diretamente em vez de utilizar `FieldBuilder` se necessário. Por exemplo, pode não querer usar uma classe de modelo ou pode precisar de usar uma classe de modelo existente que não pretende modificar adicionando atributos.
>

### <a name="call-createindex-in-main"></a>Chamada CreateIndex em Main()

`Main` cria um novo índice de "hotéis", chamando o método acima:

```csharp
Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, indexClient);
```

## <a name="use-a-model-class-for-data-representation"></a>Use uma classe modelo para representação de dados

A amostra DotNetHowTo utiliza classes-modelo para as estruturas de dados [do Hotel,](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Hotel.cs) [Endereço](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Address.cs)e [Quarto.](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Room.cs) `Hotel` referências `Address` , um tipo complexo de nível único (um campo multi-partes) e `Room` (uma coleção de campos multi-partes).

Pode utilizar estes tipos para criar e carregar o índice e estruturar a resposta a partir de uma consulta:

```csharp
// Use-case: <Hotel> in a field definition
FieldBuilder fieldBuilder = new FieldBuilder();
var searchFields = fieldBuilder.Build(typeof(Hotel));

// Use-case: <Hotel> in a response
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Uma abordagem alternativa é adicionar campos a um índice diretamente. O exemplo que se segue mostra apenas alguns campos.

   ```csharp
    SearchIndex index = new SearchIndex(indexName)
    {
        Fields =
            {
                new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
                new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
                new SearchableField("hotelCategory") { IsFilterable = true, IsSortable = true },
                new SimpleField("baseRate", SearchFieldDataType.Int32) { IsFilterable = true, IsSortable = true },
                new SimpleField("lastRenovationDate", SearchFieldDataType.DateTimeOffset) { IsFilterable = true, IsSortable = true }
            }
    };
   ```

### <a name="field-definitions"></a>Definições de campo

O seu modelo de dados em .NET e o seu esquema de índice correspondente devem suportar a experiência de pesquisa que gostaria de dar ao seu utilizador final. Cada objeto de nível superior em .NET, como um documento de pesquisa num índice de pesquisa, corresponde a um resultado de pesquisa que apresentaria na sua interface de utilizador. Por exemplo, numa aplicação de pesquisa de hotel os seus utilizadores finais podem querer pesquisar pelo nome do hotel, funcionalidades do hotel ou as características de um quarto em particular. 

Dentro de cada classe, um campo é definido com um tipo de dado e atributos que determinam como é usado. O nome de cada propriedade pública em cada mapa de classe para um campo com o mesmo nome na definição de índice. 

Veja o seguinte corte que retira várias definições de campo da classe Hotel. Note que endereços e quartos são tipos C# com as suas próprias definições de classe (consulte o código de amostra se quiser vê-los). Ambos são tipos complexos. Para obter mais informações, consulte [Como modelar tipos complexos.](search-howto-complex-data-types.md)

```csharp
public partial class Hotel
{
    [SimpleField(IsKey = true, IsFilterable = true)]
    public string HotelId { get; set; }

    [SearchableField(IsSortable = true)]
    public string HotelName { get; set; }

    [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
    public string Description { get; set; }

    [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
    public string Category { get; set; }

    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [SearchableField]
    public Address Address { get; set; }

    public Room[] Rooms { get; set; }
```

#### <a name="choosing-a-field-class"></a>Escolher uma aula de campo

Ao definir campos, pode utilizar a [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) classe base, ou pode utilizar modelos de ajuda derivada que servem como "modelos", com propriedades pré-configuradas.

Exatamente um campo no seu índice deve servir como a chave do documento `IsKey = true` (). Deve ser uma corda, e deve identificar exclusivamente cada documento. Também é necessário `IsHidden = true` ter, o que significa que não pode ser visível nos resultados da pesquisa.

| Tipo de campo | Descrição e utilização |
|------------|-----------------------|
| [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) | Classe base, com a maioria das propriedades definidas para nulo, exceto `Name` o que é necessário, e que `AnalyzerName` incumprimentos para Lucene padrão. |
| [`SimpleField`](/dotnet/api/azure.search.documents.indexes.models.simplefield) | Modelo de ajudante. Pode ser qualquer tipo de dados, é sempre não pesmável (é ignorado para consultas completas de pesquisa de texto), e é recuperável (não está escondido). Outros atributos estão desligados por padrão, mas podem ser ativados. Pode utilizar um `SimpleField` para identificação de documentos ou campos utilizados apenas em filtros, facetas ou perfis de pontuação. Em caso afirmativo, certifique-se de aplicar quaisquer atributos necessários para o cenário, como `IsKey = true` para um documento ID. Para mais informações, consulte [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) no código fonte. |
| [`SearchableField`](/dotnet/api/azure.search.documents.indexes.models.searchablefield) | Modelo de ajudante. Deve ser uma corda, e é sempre pescaizável e recuperável. Outros atributos estão desligados por padrão, mas podem ser ativados. Como este tipo de campo é pes pes pes pesjável, suporta sinónimos e o complemento completo das propriedades do analisador. Para mais informações, consulte a [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) no código fonte. |

Quer utilize a `SearchField` API básica ou qualquer um dos modelos auxiliares, tem de ativar explicitamente os atributos de filtro, faceta e classificação. Por exemplo, [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable)e [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) devem ser explicitamente atribuídos, como mostrado na amostra acima.

#### <a name="adding-field-attributes"></a>Adicionar atributos de campo

Repare como cada campo é decorado com atributos `IsFilterable` `IsSortable` como, `IsKey` e `AnalyzerName` . Estes atributos mapeiam diretamente aos [atributos de campo correspondentes num índice de Pesquisa Cognitiva Azure](/rest/api/searchservice/create-index). A `FieldBuilder` classe usa estas propriedades para construir definições de campo para o índice.

#### <a name="field-type-mapping"></a>Mapeamento de tipo de campo

Os tipos .NET do mapa de propriedades para os seus tipos de campo equivalentes na definição de índice. Por exemplo, a propriedade da cadeia `Category` mapeia para o campo `category`, que é do tipo `Edm.String`. Existem mapeamentos de tipo semelhante entre `bool?` , , e assim por `Edm.Boolean` `DateTimeOffset?` `Edm.DateTimeOffset` diante. 

Por acaso reparou na `SmokingAllowed` propriedade?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

O `JsonIgnore` atributo nesta propriedade diz para não `FieldBuilder` serializá-lo para o índice como um campo.  Esta é uma ótima maneira de criar propriedades calculadas do lado do cliente que você pode usar como ajudantes na sua aplicação.  Neste caso, a `SmokingAllowed` propriedade reflete se algum na coleção permite `Room` `Rooms` fumar. Se todos forem falsos, indica que todo o hotel não permite fumar.

## <a name="load-an-index"></a>Carregar um índice

O próximo passo em `Main` povoar o recém-criado índice de "hotéis". Esta população de índice é feita no seguinte método: (Algum código substituído por "..." para fins de ilustração. Consulte a solução de amostra completa para o código completo da população de dados.)

```csharp
private static void UploadDocuments(SearchClient searchClient)
{
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
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
            }),
        IndexDocumentsAction.Upload(
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
            }),
        IndexDocumentsAction.Upload(
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

    try
    {
        IndexDocumentsResult result = searchClient.IndexDocuments(batch);
    }
    catch (Exception)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine("Failed to index some of the documents: {0}");
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
```

Este método tem quatro partes. O primeiro cria uma matriz de 3 `Hotel` objetos cada um com 3 `Room` objetos que servirão como os nossos dados de entrada para carregar para o índice. Estes dados são codificados para a simplicidade. Numa aplicação real, os dados provavelmente virão de uma fonte de dados externa, como uma base de dados SQL.

A segunda parte cria um [`IndexDocumentsBatch`](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) contendo os documentos. Especifica a operação que pretende aplicar ao lote no momento em que o cria, neste caso, ligando [`IndexDocumentsAction.Upload`](/dotnet/api/azure.search.documents.models.indexdocumentsaction.upload) . O lote é então enviado para o índice de Pesquisa Cognitiva Azure pelo [`IndexDocuments`](/dotnet/api/azure.search.documents.searchclient.indexdocuments) método.

> [!NOTE]
> Neste exemplo, estamos apenas a enviar documentos. Se quiser fundir alterações em documentos existentes ou apagar documentos, poderá criar lotes chamando `IndexDocumentsAction.Merge` , ou em vez `IndexDocumentsAction.MergeOrUpload` `IndexDocumentsAction.Delete` disso. Também pode misturar diferentes operações num único lote, `IndexBatch.New` chamando, que leva uma coleção de `IndexDocumentsAction` objetos, cada um dos quais diz à Azure Cognitive Search para realizar uma determinada operação num documento. Pode criar cada um `IndexDocumentsAction` com o seu próprio funcionamento, chamando o método `IndexDocumentsAction.Merge` `IndexAction.Upload` correspondente, como, e assim por diante.
> 

A terceira parte deste método é um bloco de captura que lida com um caso de erro importante para a indexação. Se o seu serviço de pesquisa não indexar alguns dos documentos do lote, um `IndexBatchException` é atirado por `IndexDocuments` . Esta exceção pode acontecer se estiver a indexar documentos enquanto o seu serviço estiver sob carga pesada. **Recomendamos vivamente que processe explicitamente este caso no seu código.** Pode atrasar e, em seguida, repetir a indexação dos documentos que falharam, pode iniciar sessão e continuar como no exemplo ou pode fazer algo diferente dependendo dos requisitos de consistência de dados da aplicação.

Finalmente, o `UploadDocuments` método atrasa-se por dois segundos. A indexação ocorre de forma assíncronea no seu serviço de pesquisa, pelo que a aplicação da amostra precisa de esperar um pouco para garantir que os documentos estão disponíveis para pesquisa. Este género de atrasos são normalmente necessários apenas para demonstrações gratuitas, testes e aplicações de exemplo.

### <a name="call-uploaddocuments-in-main"></a>Ligue para uploadDocuments em Main()

O seguinte corte de código estabelece uma instância de [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) utilização do [`GetSearchClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient.getsearchclient) método de indexaçãoClient. O indexClient utiliza uma chave API de administração nos seus pedidos, que é necessária para carregar ou refrescar documentos.

Uma abordagem alternativa é ligar `SearchClient` diretamente, passando por uma chave API de administração em `AzureKeyCredential` .

```csharp
SearchClient searchClient = indexClient.GetSearchClient(indexName);

Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(searchClient);
```

## <a name="run-queries"></a>Executar consultas

Em primeiro lugar, crie um `SearchClient` que leia a chave de pesquisa e consulta da API a partir de **appsettings.jsem**:

```csharp
private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

Segundo, defina um método que envia um pedido de consulta. 

Cada vez que o método executa uma consulta, cria um novo [`SearchOptions`](/dotnet/api/azure.search.documents.searchoptions) objeto. Este objeto é utilizado para especificar opções adicionais para a consulta, tais como triagem, filtragem, paging e faceting. Neste método, estamos a definir o `Filter` , e propriedade para `Select` `OrderBy` diferentes consultas. Para obter mais informações sobre a sintaxe de expressão de consulta de pesquisa, [sintaxe de consulta simples](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).

O próximo passo é executar a consulta de pesquisa. A execução da pesquisa é feita usando o `SearchClient.Search` método. Para cada consulta, passe o texto de pesquisa para usar como uma cadeia (ou `"*"` se não houver texto de pesquisa), além das opções de pesquisa criadas anteriormente. Também especificamos `Hotel` como parâmetro de tipo para , que diz ao `SearchClient.Search` SDK para desseializar documentos na pesquisa resulta em objetos do tipo `Hotel` .

```csharp
private static void RunQueries(SearchClient searchClient)
{
    SearchOptions options;
    SearchResults<Hotel> results;

    Console.WriteLine("Query 1: Search for 'motel'. Return only the HotelName in results:\n");

    options = new SearchOptions();
    options.Select.Add("HotelName");

    results = searchClient.Search<Hotel>("motel", options);

    WriteDocuments(results);

    Console.Write("Query 2: Apply a filter to find hotels with rooms cheaper than $100 per night, ");
    Console.WriteLine("returning the HotelId and Description:\n");

    options = new SearchOptions()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)"
    };
    options.Select.Add("HotelId");
    options.Select.Add("Description");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.Write("Query 3: Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    options =
        new SearchOptions()
        {
            Size = 2
        };
    options.OrderBy.Add("LastRenovationDate desc");
    options.Select.Add("HotelName");
    options.Select.Add("LastRenovationDate");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.WriteLine("Query 4: Search the HotelName field for the term 'hotel':\n");

    options = new SearchOptions();
    options.SearchFields.Add("HotelName");

    //Adding details to select, because "Location" is not supported yet when deserialize search result to "Hotel"
    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Category");
    options.Select.Add("Tags");
    options.Select.Add("ParkingIncluded");
    options.Select.Add("LastRenovationDate");
    options.Select.Add("Rating");
    options.Select.Add("Address");
    options.Select.Add("Rooms");

    results = searchClient.Search<Hotel>("hotel", options);

    WriteDocuments(results);
}
```

Em terceiro lugar, defina um método que escreva a resposta, imprimindo cada documento à consola:

```csharp
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

### <a name="call-runqueries-in-main"></a>Ligue para RunQueries em Main()

```csharp
SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

Console.WriteLine("{0}", "Running queries...\n");
RunQueries(indexClientForQueries);
```

### <a name="explore-query-constructs"></a>Explore construções de consultas

Vamos ver mais de perto cada uma das consultas por sua vez. Aqui está o código para executar a primeira consulta:

```csharp
options = new SearchOptions();
options.Select.Add("HotelName");

results = searchClient.Search<Hotel>("motel", options);

WriteDocuments(results);
```

Neste caso, estamos procurando todo o índice para a palavra "motel" em qualquer campo pescandável e só queremos recuperar os nomes do hotel, conforme especificado pela `Select` opção. Aqui estão os resultados:

```output
Name: Secret Point Motel

Name: Twin Dome Motel
```

Na segunda consulta, utilize um filtro para selecionar o quarto com uma diária inferior a $100. Devolva apenas a iD do hotel e a descrição dos resultados:

```csharp
options = new SearchOptions()
{
    Filter = "Rooms/any(r: r/BaseRate lt 100)"
};
options.Select.Add("HotelId");
options.Select.Add("Description");

results = searchClient.Search<Hotel>("*", options);
```

A consulta acima utiliza uma `$filter` expressão OData, `Rooms/any(r: r/BaseRate lt 100)` para filtrar os documentos no índice. Isto utiliza [qualquer operador](./search-query-odata-collection-operators.md) para aplicar o 'BaseRate lt 100' a todos os artigos da coleção Rooms. Para obter mais informações, consulte [a sintaxe do filtro OData](./query-odata-filter-orderby-syntax.md).

Na terceira consulta, encontre os dois melhores hotéis que foram recentemente renovados, e mostre o nome do hotel e a última data de renovação. Apresentamos o código a seguir: 

```csharp
options =
    new SearchOptions()
    {
        Size = 2
    };
options.OrderBy.Add("LastRenovationDate desc");
options.Select.Add("HotelName");
options.Select.Add("LastRenovationDate");

results = searchClient.Search<Hotel>("*", options);

WriteDocuments(results);
```

Na última consulta, encontre todos os nomes de hotéis que correspondam à palavra "hotel":

```csharp
options.Select.Add("HotelId");
options.Select.Add("HotelName");
options.Select.Add("Description");
options.Select.Add("Category");
options.Select.Add("Tags");
options.Select.Add("ParkingIncluded");
options.Select.Add("LastRenovationDate");
options.Select.Add("Rating");
options.Select.Add("Address");
options.Select.Add("Rooms");

results = searchClient.Search<Hotel>("hotel", options);

WriteDocuments(results);
```

Esta secção conclui esta introdução ao .NET SDK, mas não pares aqui. A próxima secção sugere recursos adicionais para aprender mais sobre programação com Azure Cognitive Search.

## <a name="next-steps"></a>Passos seguintes

+ Consulte a documentação de referência da API para [Azure.Search.Documents](/dotnet/api/azure.search.documents) e [REST API](/rest/api/searchservice/)

+ Pesquise outras amostras de código com base em uments de Azure.Search.Docem [amostras de azure-search-dotnet](https://github.com/Azure-Samples/azure-search-dotnet-samples) e [pesquisa-getting-dotnet](https://github.com/Azure-Samples/search-dotnet-getting-started)

+ [Reveja as convenções de nomeação](/rest/api/searchservice/Naming-rules) para aprender as regras para nomear vários objetos

+ Rever [tipos de dados suportados](/rest/api/searchservice/Supported-data-types)