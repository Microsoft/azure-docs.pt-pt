---
title: Carregar dados no código com o SDK de .NET - Azure Search
description: Saiba como carregar dados para um índice de texto completo pesquisável no Azure Search utilizando C# código e o SDK de .NET de exemplo.
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/13/2017
ms.custom: seodec2018
ms.openlocfilehash: 2b0b9d2f2030346c5201d006459ebe752aa532af
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202032"
---
# <a name="upload-data-to-azure-search-using-the-net-sdk"></a>Importar dados para a Azure Search utilizando o SDK .NET
> [!div class="op_single_selector"]
> * [Descrição geral](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

Este artigo irá mostrar como utilizar o [SDK .NET da Azure Search](https://aka.ms/search-sdk) para importar dados para um índice da Azure Search.

Antes de iniciar estas instruções, já deverá ter [criado um índice de Azure Search](search-what-is-an-index.md). Este artigo também assume que já criou um projeto `SearchServiceClient`, conforme mostrado em [Criar um índice da Azure Search utilizando o SDK .NET](search-create-index-dotnet.md#CreateSearchServiceClient).

> [!NOTE]
> Todo o código de exemplo neste artigo está escrito em C#. Pode localizar o código de origem completo [no GitHub](https://aka.ms/search-dotnet-howto). Também pode ler sobre o [Azure Search .NET SDK (SDK .NET do Azure Search)](search-howto-dotnet-sdk.md) para um percurso mais detalhado através do código de exemplo.

Para enviar documentos para o seu índice utilizando o SDK .NET, é necessário:

1. Criar um objeto `SearchIndexClient` para se ligar ao índice de pesquisa.
2. Criar um `IndexBatch` que contém os documentos a adicionar, modificar ou eliminar.
3. Chamar o método `Documents.Index` do seu `SearchIndexClient` para enviar o `IndexBatch` para o seu índice de pesquisa.

## <a name="create-an-instance-of-the-searchindexclient-class"></a>Criar uma instância da classe SearchIndexClient
Para importar dados para o seu índice através do SDK .NET da Azure Search, terá de criar uma instância da classe `SearchIndexClient`. Pode construir esta instância manualmente, mas é mais fácil se já tiver uma instância `SearchServiceClient` para chamar o seu método `Indexes.GetClient`. Por exemplo, pode obter um `SearchIndexClient` para o índice com o nome "hotéis" a partir de um `SearchServiceClient` com o nome `serviceClient` desta forma:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> Numa aplicação de pesquisa normal, a gestão de índice e a população são processadas por um componente separado das consultas de pesquisa. `SearchCredentials` é útil para preencher um índice, uma vez que lhe poupa o trabalho de proporcionar outras `Indexes.GetClient`. Este é realizado através da transferência da chave de administração que utilizou para criar o `SearchServiceClient` para o novo `SearchIndexClient`. No entanto, na parte da sua aplicação que executa as consultas, é melhor criar o `SearchIndexClient` diretamente para que possa transferir uma chave de consulta em vez de uma chave de administração. Este processo é consistente com o [princípio do menor privilégio](https://en.wikipedia.org/wiki/Principle_of_least_privilege) e irá ajudá-lo a tornar a sua aplicação mais segura. Pode encontrar mais informações sobre chaves de administração e chaves de consulta na [referência da API REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/).
> 
> 

`SearchIndexClient` tem uma propriedade `Documents`. Esta propriedade fornece todos os métodos necessários para adicionar, modificar, eliminar ou consultar documentos de consulta no seu índice.

## <a name="decide-which-indexing-action-to-use"></a>Decidir a ação de indexação a utilizar
Para importar dados utilizando o SDK .NET, terá de empacotar os seus dados para um objeto `IndexBatch`. Um `IndexBatch` encapsula uma coleção de objetos `IndexAction`, cada um deles contendo um documento e uma propriedade que informa a Azure Search qual a ação a realizar nesse documento (carregar, intercalar, eliminar, etc.). Dependendo das ações que escolher abaixo, apenas determinados campos tem de ser incluídos para cada documento:

| Ação | Descrição | Campos necessários para cada documento | Notas |
| --- | --- | --- | --- |
| `Upload` |Um ação `Upload` é semelhante a um "upsert" onde o documento será inserido se for novo e atualizado/substituído se já existir. |chave, juntamente com quaisquer outros campos que pretende definir |Quando atualizar/substituir um documento existente, qualquer campo que não está especificado no pedido terá o respetivo campo definido como `null`. Isto ocorre mesmo quando o campo foi anteriormente definido para um valor não nulo. |
| `Merge` |Atualiza um documento existente com os campos especificados. Se o documento não existe no índice, a intercalação irá falhar. |chave, juntamente com quaisquer outros campos que pretende definir |Qualquer campo que especifique numa intercalação irá substituir o campo existente no documento. Isto inclui campos do tipo `DataType.Collection(DataType.String)`. Por exemplo, se o documento contém um campo `tags` com o valor `["budget"]` e executar uma intercalação com o valor `["economy", "pool"]` para `tags`, o valor final do campo `tags` será `["economy", "pool"]`. Não será `["budget", "economy", "pool"]`. |
| `MergeOrUpload` |Esta ação tem o mesmo comportamento de `Merge` caso um documento com a chave especificada já exista no índice. Se o documento não existir, tem um comportamento semelhante `Upload` a um novo documento. |chave, juntamente com quaisquer outros campos que pretende definir |- |
| `Delete` |Remove o documento especificado do índice. |apenas chave |Quaisquer campos que especificar diferentes do campo de chave serão ignorados. Se pretender remover um campo individual de um documento, utilize `Merge` em vez disso e simplesmente defina o campo explicitamente como nulo. |

Pode especificar que ação pretende utilizar com os vários métodos estáticos das classes `IndexBatch` e `IndexAction`, conforme mostrado na secção seguinte.

## <a name="construct-your-indexbatch"></a>Construir o seu IndexBatch
Agora que já sabe quais as ações a executar nos seus documentos, está pronto para construir o `IndexBatch`. O exemplo abaixo mostra como criar um lote com algumas ações diferentes. Tenha em atenção que o nosso exemplo utiliza uma classe personalizada denominada `Hotel` que mapeia para um documento no índice "hotéis".

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
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
            }),
        IndexAction.Upload(
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
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

Neste caso, estamos a utilizar `Upload`, `MergeOrUpload` e `Delete` como as nossas ações de pesquisa, tal como especificado pelos métodos chamados na classe `IndexAction`.

Partamos do princípio que este índice "hotéis" de exemplo já está preenchido com vários documentos. Observe como não tivemos de especificar todos os campos de documentos possíveis quando utilizamos `MergeOrUpload` e como especificamos apenas a chave do documento (`HotelId`) ao utilizar `Delete`.

Além disso, tenha em atenção que só pode incluir até 1000 documentos num único pedido de indexação.

> [!NOTE]
> Neste exemplo, estamos a aplicar diferentes ações para documentos diferentes. Se pretendia realizar as mesmas ações em todos os documentos no lote, em vez de chamar `IndexBatch.New`, pode utilizar os outros métodos estáticos de `IndexBatch`. Por exemplo, pode criar lotes chamando `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` ou `IndexBatch.Delete`. Estes métodos recolhem uma coleção de documentos (objetos do tipo `Hotel` neste exemplo), ao invés de objetos `IndexAction`.
> 
> 

## <a name="import-data-to-the-index"></a>Importar dados para o índice
Agora que tem um objeto `IndexBatch` inicializado, pode enviá-lo para o índice ao chamar `Documents.Index` no seu objeto `SearchIndexClient` O exemplo seguinte mostra como chamar o `Index`, assim como alguns passos adicionais que terá de realizar:

```csharp
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
```

Tenha em atenção a `try`/`catch` adjacente à chamada para o método `Index`. O bloco catch processa um caso de erro importantes para indexação. Se o serviço Azure Search não consegue indexar alguns dos documentos no lote, uma `IndexBatchException` é emitida pelo `Documents.Index`. Isto pode acontecer se indexar documentos enquanto o seu serviço estiver sobrecarregado. **Recomendamos vivamente que processe explicitamente este caso no seu código.** Pode atrasar e, em seguida, repetir a indexação dos documentos que falharam, pode iniciar sessão e continuar como no exemplo ou pode fazer algo diferente dependendo dos requisitos de consistência de dados da aplicação.

Por fim, o código no exemplo acima tem um atraso de dois segundos. A indexação acontece de forma assíncrona no seu serviço da Azure Search, desta forma, a aplicação de exemplo tem de aguardar alguns momentos para se certificar de que os documentos estão disponíveis para pesquisa. Este género de atrasos são normalmente necessários apenas para demonstrações gratuitas, testes e aplicações de exemplo.

<a name="HotelClass"></a>

### <a name="how-the-net-sdk-handles-documents"></a>Como o SDK .NET processa documentos
Pode estar a pensar como é que o SDK NET da Azure Search consegue carregar instâncias de uma classe definida por utilizador, tal como `Hotel`, para o índice. Para ajudar a responder a essa questão, vamos ver a classe `Hotel`, a qual cria um mapeamento para o esquema de índice definido em [Criar um índice da Azure Search utilizando o SDK .NET](search-create-index-dotnet.md#DefineIndex):

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [Key]
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

    // ToString() method omitted for brevity...
}
```

A primeira coisa a observar é que cada propriedade pública de `Hotel` corresponde a um campo na definição do índice, mas com uma diferença crucial: O nome de cada campo começa com uma letra minúscula ("camel case"), enquanto o nome de cada propriedade pública de `Hotel` começa com uma letra maiúscula ("Pascal case"). Este é um cenário comum em aplicações .NET que realizam enlace de dados no qual o esquema de destino está fora do controlo do programador da aplicação. Em vez de ter de violar as diretrizes de nomenclatura .NET aplicando o estilo camel-case aos nomes de propriedade, pode indicar ao SDK para mapear os nomes das propriedades no estilo camel-case automaticamente com o atributo `[SerializePropertyNamesAsCamelCase]`.

> [!NOTE]
> O SDK .NET da Azure Search utiliza a biblioteca [NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) para serializar e anular a serialização dos objetos do modelo personalizado de e para JSON. Se necessário, pode personalizar esta serialização. Pode encontrar mais detalhes em [Custom Serialization with JSON.NET (Personalizar serialização com JSON.NET)](search-howto-dotnet-sdk.md#JsonDotNet). Um exemplo disto é a utilização do atributo `[JsonProperty]` na propriedade `DescriptionFr` no código de exemplo acima.
> 
> 

O segundo ponto importante sobre a classe `Hotel` são os tipos de dados das propriedades públicas. Os tipos .NET destas propriedades mapeiam para os tipos de campo equivalentes na definição do índice. Por exemplo, a propriedade da cadeia `Category` mapeia para o campo `category`, que é do tipo `DataType.String`. Existem mapeamentos de tipo semelhantes entre `bool?` e `DataType.Boolean`, `DateTimeOffset?` e `DataType.DateTimeOffset`, etc. As regras específicas para o mapeamento do tipo estão documentadas com o método `Documents.Get` em [Azure Search .NET SDK reference (Referência SDK .NET do Azure Search)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get).

Esta capacidade de utilizar as suas próprias classes como documentos funciona em ambas as direções; Também pode obter resultados de pesquisa e colocar o SDK a anular automaticamente a serialização para um tipo à sua escolha, conforme apresentado no [artigo seguinte](search-query-dotnet.md).

> [!NOTE]
> O SDK .NET da Azure Search também suporta documentos dinâmicos utilizando a classe `Document`, que é um mapeamento de chave/valor dos nomes de campo para valores de campo. Isto é útil em cenários onde não sabe qual o esquema de índice no momento da conceção, nem onde seria inconveniente discretizar as classes do modelo específico. Todos os métodos no SDK que lidam com documentos têm sobrecargas que funcionam com a classe `Document`, bem como as sobrecargas de tipo seguro que assumem um parâmetro do tipo genérico. Apenas o último caso é utilizado no código de exemplo neste artigo.
> 
> 

**Por que deve utilizar tipos de dados anuláveis**

Ao conceber as suas próprias classes de modelo para mapear para um índice da Azure Search, recomendamos que declare as propriedades dos tipos de valores, tais como `bool` e `int` para que seja anulável (por exemplo, `bool?` ao invés de `bool`). Se utilizar uma propriedade não anulável, terá de **garantir** que não existem documentos no seu índice de contenham um valor nulo para o campo correspondente. Nem o SDK nem o serviço da Azure Search irão ajudá-lo a impor tais pedidos.

Não é apenas uma preocupação hipotética: Imagine um cenário em que adicionar um novo campo para um índice existente que é do tipo `DataType.Int32`. Depois de atualizar a definição do índice, todos os documentos terão um valor nulo para esse campo novo (uma vez que todos os tipos são anuláveis na Azure Search). Se, em seguida, utilizar uma classe de modelo com uma propriedade `int` não anulável para esse campo, obterá uma `JsonSerializationException` assim ao tentar obter documentos:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por este motivo, recomendamos que utilize tipos anuláveis nas suas classes de modelos como uma melhor prática.

## <a name="next-steps"></a>Passos Seguintes
Depois de preencher o seu índice da Azure Search, estará pronto para começar a emitir consultas para procurar documentos. Consulte o artigo [Consultar o Índice da Azure Search](search-query-overview.md) para obter detalhes.

