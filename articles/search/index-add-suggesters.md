---
title: Adicionar consultas typeahead a um índice
titleSuffix: Azure Cognitive Search
description: Habilite ações de consulta de tipo antecipado no Azure Pesquisa Cognitiva criando sugestores e solicitações formular que invoquem os termos de consulta autocompletados ou autosugeridos.
manager: nitinme
author: Brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: a312068d5c8c574e7b069263cf37e3b855810e4b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790110"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-cognitive-search"></a>Adicionar sugestores a um índice para typeahead no Azure Pesquisa Cognitiva

No Azure Pesquisa Cognitiva, a funcionalidade de "pesquisa conforme o tipo" ou typeahead é baseada em uma construção de **sugestão** que você adiciona a um [índice de pesquisa](search-what-is-an-index.md). É uma lista de um ou mais campos para os quais você deseja que o typeahead seja habilitado.

Um Sugestor dá suporte a duas variantes typeahead: *preenchimento automático*, que conclui o termo ou frase que você está digitando e *sugestões* que retornam uma pequena lista de documentos correspondentes.  

A captura de tela a seguir, do [criar seu primeiro C# aplicativo em](tutorial-csharp-type-ahead-and-suggestions.md) exemplo, ilustra typeahead. O preenchimento automático prevê o que o usuário pode digitar na caixa de pesquisa. A entrada real é "TW", que o preenchimento automático termina com "in", resolvendo como "" "para o termo de pesquisa potencial. As sugestões são visualizadas na lista suspensa. Para obter sugestões, você pode retonar qualquer parte de um documento que melhor descreva o resultado. Neste exemplo, as sugestões são nomes de Hotel. 

![Comparação visual de consultas autocompletas e sugeridas](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Comparação visual de consultas autocompletas e sugeridas")

Para implementar esses comportamentos no Azure Pesquisa Cognitiva, há um componente de índice e consulta. 

+ No índice, adicione um Sugestor a um índice. Você pode usar o portal, a [API REST](https://docs.microsoft.com/rest/api/searchservice/create-index)ou o [SDK do .net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). O restante deste artigo se concentra na criação de um Sugestor. 

+ Na solicitação de consulta, chame uma das [APIs listadas abaixo](#how-to-use-a-suggester).

O suporte de pesquisa conforme o tipo é habilitado em uma base por campo. Você pode implementar ambos os comportamentos do typeahead na mesma solução de pesquisa se desejar uma experiência semelhante à indicada na captura de tela. Ambas as solicitações direcionam a coleção de *documentos* de índice e respostas específicas são retornadas depois que um usuário tiver fornecido pelo menos uma cadeia de caracteres de entrada de três caracteres.

## <a name="create-a-suggester"></a>Criar um fornecedor de sugestões

Embora um Sugestor tenha várias propriedades, ele é basicamente uma coleção de campos para os quais você está habilitando uma experiência typeahead. Por exemplo, um aplicativo de viagem pode querer habilitar a pesquisa typeahead em destinos, cidades e Attractions. Assim, todos os três campos seriam na coleção Fields.

Para criar um Sugestor, adicione um a um esquema de índice. Você pode ter um Sugestor em um índice (especificamente, um Sugestor na coleção de sugestores). 

### <a name="when-to-create-a-suggester"></a>Quando criar um Sugestor

O melhor momento para criar um Sugestor é quando você também está criando a própria definição de campo.

Se você tentar criar um Sugestor usando campos preexistentes, a API não permitirá isso. O texto typeahead é criado durante a indexação, quando os termos parciais em duas ou mais combinações de caracteres são indexados com os termos inteiros. Considerando que os campos existentes já foram indexados, você precisará recompilar o índice se quiser adicioná-los a um Sugestor. Para obter mais informações sobre a reindexação, consulte [como recriar um índice de pesquisa cognitiva do Azure](search-howto-reindex.md).

### <a name="create-using-the-rest-api"></a>Criar usando a API REST

Na API REST, adicione sugestores por meio de [CREATE INDEX](https://docs.microsoft.com/rest/api/searchservice/create-index) ou [Update index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

  ```json
  {
    "name": "hotels",
    "fields": [
      . . .
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["hotelName", "category"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```


### <a name="create-using-the-net-sdk"></a>Criar usando o SDK do .NET

No C#, defina um [objeto de sugestão](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters` é uma coleção, mas só pode pegar um item. 

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

### <a name="property-reference"></a>Referência de propriedade

|Propriedade      |Descrição      |
|--------------|-----------------|
|`name`        |O nome do Sugestor.|
|`searchMode`  |A estratégia usada para pesquisar frases candidatas. O único modo com suporte no momento é `analyzingInfixMatching`, o que executa a correspondência flexível de frases no início ou no meio de frases.|
|`sourceFields`|Uma lista de um ou mais campos que são a origem do conteúdo para sugestões. Os campos devem ser do tipo `Edm.String` e `Collection(Edm.String)`. Se um analisador for especificado no campo, ele deverá ser um analisador Nomeado [desta lista](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (não um analisador personalizado).<p/>Como prática recomendada, especifique somente os campos que se prestam a uma resposta esperada e apropriada, seja uma cadeia de caracteres completa em uma barra de pesquisa ou uma lista suspensa.<p/>Um nome de Hotel é um bom candidato porque tem precisão. Campos detalhados, como descrições e comentários, são muito densos. Da mesma forma, campos repetitivos, como categorias e marcas, são menos eficazes. Nos exemplos, incluímos "category" de qualquer forma para demonstrar que você pode incluir vários campos. |

### <a name="analyzer-restrictions-for-sourcefields-in-a-suggester"></a>Restrições do analisador para sourceFields em um Sugestor

O Azure Pesquisa Cognitiva analisa o conteúdo do campo para habilitar a consulta em termos individuais. Os sugestores exigem que os prefixos sejam indexados além dos termos completos, o que requer análise adicional nos campos de origem. As configurações personalizadas do analisador podem combinar qualquer um dos vários filtros e criadores, geralmente de maneira que tornaria a produção dos prefixos necessários para sugestões impossível. Por esse motivo, o Azure Pesquisa Cognitiva impede que os campos com analisadores personalizados sejam incluídos em um Sugestor.

> [!NOTE] 
>  Se você precisar contornar a limitação acima, use dois campos separados para o mesmo conteúdo. Isso permitirá que um dos campos tenha um Sugestor, enquanto o outro pode ser configurado com uma configuração personalizada do analisador.

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester-in-a-query"></a>Usar um Sugestor em uma consulta

Depois que um Sugestor for criado, chame a API apropriada em sua lógica de consulta para invocar o recurso. 

+ [API REST de sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [API REST de preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [Método SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Método AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

O uso da API é ilustrado na chamada a seguir para a API REST de preenchimento automático. Há dois argumentos neste exemplo. Primeiro, assim como acontece com todas as consultas, a operação é relacionada à coleção de documentos de um índice. Em segundo lugar, você pode adicionar parâmetros de consulta. Embora muitos parâmetros de consulta sejam comuns a ambas as APIs, a lista é diferente para cada uma delas.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

Se um Sugestor não estiver definido no índice, uma chamada para preenchimento automático ou sugestões falhará.

## <a name="sample-code"></a>Código de exemplo

+ [Criar seu primeiro aplicativo em C# ](tutorial-csharp-type-ahead-and-suggestions.md) exemplo demonstra uma construção de sugestão, consultas sugeridas, preenchimento automático e navegação facetada. Este exemplo de código é executado em um serviço de Pesquisa Cognitiva do Azure de área restrita e usa um índice de hotéis pré-carregado para que tudo o que você precisa fazer seja pressionar F5 para executar o aplicativo. Nenhuma assinatura ou entrada é necessária.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) é um exemplo mais antigo que C# contém o e o código Java. Ele também demonstra uma construção de sugestão, consultas sugeridas, preenchimento automático e navegação facetada. Este exemplo de código usa os dados de exemplo hospedados do [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) . 


## <a name="next-steps"></a>Passos seguintes

Recomendamos o exemplo a seguir para ver como as solicitações são formuladas.

> [!div class="nextstepaction"]
> [Sugestões e exemplos de preenchimento automático](search-autocomplete-tutorial.md) 
