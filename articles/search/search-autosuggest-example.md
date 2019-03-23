---
title: Sugestão automática de exemplo para adicionar termos de lista pendente para uma caixa de pesquisa - Azure Search
description: Adicione entradas sugeridas consulta criando sugestores e formular pedidos que invocam consultas sugeridas na Azure Search.
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: heidist
ms.openlocfilehash: 1db085d61c60d303aaff5c3b0d16998805fcda90
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373079"
---
# <a name="example-add-autosuggest-for-dropdown-query-selections"></a>Exemplo: Adicionar sugestão automática para as seleções de consulta de lista pendente

Entradas do termo de pesquisa podem incluir uma lista suspensa de termos de consulta sugerida. Já viu esse recurso em mecanismos de pesquisa comerciais, e pode implementar uma experiência semelhante no Azure Search com um [sugestor construção](index-add-suggesters.md) e uma operação de sugestões num pedido de consulta. Este artigo utiliza exemplos para demonstrar a formulação de uma consulta de sugestão automática, usando um sugestor que já definiu. 

## <a name="rest-api"></a>API REST

Pode usar [Postman](search-fiddler.md) ou [PowerShell](search-create-index-rest-api.md) e o [REST API](https://docs.microsoft.com/rest/api/searchservice/) experimentar o neste exemplo, mas os resultados serão retornados como documentos JSON. Uma experiência mais realista e visual pode ser encontrada usando-a [código de conclusão automática de exemplo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

### <a name="1---index-with-a-suggester-construct"></a>1 - indexar com uma construção do sugestor

Sugestão automática começa com uma [sugestor construção](index-add-suggesters.md) adicionada a um índice, compostas por campos que contribuem com valores de lista pendente. Tendo em conta o seguinte esquema, consultas sugeridas irão ser formuladas com valores dos campos hotelName e categoria.

Supondo que os conjuntos de dados de exemplo mínima encontrados no inícios rápidos, nomes de hotel incluem "Mantenha-se especial" e "Motel motéis" e categorias incluem "Luxo" e "Orçamento".

Se já tiver o índice de hotéis, deve remover e recriá-la utilizando o esquema abaixo. Esse esquema adiciona um sugestor. Lembre-se recarregar os dados também.

```json
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ],
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["hotelName", "category"]
    }
  ]
}

```

### <a name="2---query-with-suggestions-operator"></a>2 - consultar com o operador de sugestões

Para utilizar um sugestor e invocar sugestão automática, tem de enviar um [sugestões API](https://docs.microsoft.com/rest/api/searchservice/suggestions) solicite utilizando GET ou POST. Um pedido, o serviço de pesquisa procura correspondências possíveis assim que os três primeiros carateres são recebidos. 

No cabeçalho do pedido, defina **chave de api** para uma chave de consulta ou de administrador, e **Content-Type** como application/json. 

```http
GET https://mydemo.search.windows.net/indexes/hotels/docs/suggest?search=fan&&suggesterName=sg
```

As verificações de pedido todos os campos incluídos no sugestor (hotelName e categoria), retornando a seguinte resposta:

```
{
    "@odata.context": "https://mydemo.search.windows.net/indexes('hotels')/$metadata#docs(*)",
    "value": [
        {
            "@search.text": "Fancy Stay",
            "hotelId": "1"
        }
    ]
}
```

Para fornecer o resultado esperado, o código do cliente teria de processar os resultados como lista suspensa numa barra de pesquisa.

## <a name="net-sdk-c"></a>SDK DO .NET (C#)

### <a name="1---index-with-a-suggester-construct"></a>1 - indexar com uma construção do sugestor

No SDK do .NET, utilizar um [classe Sugestor](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Sugestor é uma coleção, mas apenas pode demorar um item.

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelId", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

### <a name="2---query-with-suggest-method"></a>2 - consultar com o método sugerido

O [DocumentsOperationsExtensions.Suggest método](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet) é utilizado para devolver cadeias de consulta sugerida.

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult suggestResult = _indexClient.Documents.Suggest(term, "sg",sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

## <a name="see-also"></a>Consulte também

+ [Explore a API de REST com o Postman](search-fiddler.md)
+ [Exemplo: Preenchimento automático](search-autocomplete-tutorial.md)
+ [Adicionar sugestores para um índice](index-add-suggesters.md)
+ [Adicionar uma classe de Sugestores usando o .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)
+ [Sugestões de chamada usando GET ou POST (REST API)](https://docs.microsoft.com/rest/api/searchservice/suggestions)
+ [Chamar sugestões com SuggestWithHttpMessagesAsync (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) ou 