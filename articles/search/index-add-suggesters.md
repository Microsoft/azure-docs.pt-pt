---
title: Adicionar consultas tipoà letra a um índice
titleSuffix: Azure Cognitive Search
description: Ative ações de consulta tipo-à-frente na Pesquisa Cognitiva Azure, criando sugestionantes e formulando pedidos que invoquem termos de consulta autocompletos ou auto-sugeridos.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72790110"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-cognitive-search"></a>Adicione sugestionantes a um índice para typeahead em Pesquisa Cognitiva Azure

Na Pesquisa Cognitiva Azure, a funcionalidade "search-as-you-type" ou typeahead baseia-se numa construção **sugestionante** que se adiciona a um índice de [pesquisa](search-what-is-an-index.md). É uma lista de um ou mais campos para os quais você quer dactilografado habilitado.

Um sugestionador suporta duas variantes tipoà letra: *autocomplete,* que completa o termo ou frase que está a escrever, e *sugestões* que devolvem uma pequena lista de documentos correspondentes.  

A seguinte imagem, a partir da primeira app Create na amostra [C#,](tutorial-csharp-type-ahead-and-suggestions.md) ilustra o tipo de letra. A conclusão automática antecipa o que o utilizador pode escrever na caixa de pesquisa. A entrada real é "tw", que termina automaticamente com "in", resolvendo como "twin" como o termo de pesquisa prospetiva. As sugestões são visualizadas na lista de abandono. Para sugestões, pode emergir qualquer parte de um documento que melhor descreva o resultado. Neste exemplo, as sugestões são nomes de hotéis. 

![Comparação visual de consultas autocompletas e sugeridas](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Comparação visual de consultas autocompletas e sugeridas")

Para implementar estes comportamentos na Pesquisa Cognitiva Azure, existe um componente de indexação e consulta. 

+ No índice, adicione um sugestor a um índice. Pode utilizar o portal, [REST API,](https://docs.microsoft.com/rest/api/searchservice/create-index)ou [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). O restante deste artigo está focado na criação de um sugestor. 

+ No pedido de consulta, ligue para uma das [APIs listadas abaixo](#how-to-use-a-suggester).

O suporte de pesquisa como o tipo de pesquisa é ativado numa base por campo. Pode implementar ambos os comportamentos da tipografia dentro da mesma solução de pesquisa se quiser uma experiência semelhante à indicada na imagem. Ambos os pedidos visam a recolha de *documentos* de índice específico e as respostas são devolvidas depois de um utilizador ter fornecido pelo menos uma cadeia de entrada de três caracteres.

## <a name="create-a-suggester"></a>Criar um fornecedor de sugestões

Embora um sugestionista tenha várias propriedades, é principalmente uma coleção de campos para os quais você está permitindo uma experiência de tipo à frente. Por exemplo, uma aplicação de viagem pode querer permitir a pesquisa de tipo em destinos, cidades e atrações. Como tal, os três campos iriam para a coleção de campos.

Para criar um sugestor, adicione um a um esquema de índice. Pode ter um sugestor num índice (especificamente, um sugestor na coleção de sugestionantes). 

### <a name="when-to-create-a-suggester"></a>Quando criar um sugestor

A melhor altura para criar um sugestionista é quando também estás a criar a definição de campo em si.

Se tentar criar um sugestionante utilizando campos pré-existentes, a API irá desafogá-la. O texto typeahead é criado durante a indexação, quando os termos parciais em duas ou mais combinações de caracteres são tokenizados ao lado de termos inteiros. Dado que os campos existentes já estão tokenizados, terá de reconstruir o índice se quiser adicioná-los a um sugestionista. Para obter mais informações sobre a reindexação, consulte Como reconstruir um índice de [pesquisa cognitiva Azure](search-howto-reindex.md).

### <a name="create-using-the-rest-api"></a>Criar usando a API REST

Na API REST, adicione os sugestores através do [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) ou do [Update Index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

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


### <a name="create-using-the-net-sdk"></a>Criar usando o .NET SDK

Em C#, defina um [objeto sugestionador](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters`é uma coleção, mas só pode levar um item. 

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

### <a name="property-reference"></a>Referência imobiliária

|Propriedade      |Descrição      |
|--------------|-----------------|
|`name`        |O nome do sugeridor.|
|`searchMode`  |A estratégia costumava procurar frases candidatas. O único modo atualmente `analyzingInfixMatching`suportado é , que executa uma combinação flexível de frases no início ou no meio das frases.|
|`sourceFields`|Uma lista de um ou mais campos que são a fonte do conteúdo para sugestões. Os campos devem `Edm.String` `Collection(Edm.String)`ser de tipo e . Se um analisador for especificado no campo, deve ser um analisador nomeado [desta lista](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (não um analisador personalizado).<p/>Como uma boa prática, especifique apenas os campos que se prestam a uma resposta esperada e apropriada, seja uma corda completa numa barra de pesquisa ou uma lista de abandono.<p/>Um nome de hotel é um bom candidato porque tem precisão. Campos verbosos como descrições e comentários são muito densos. Da mesma forma, os campos repetitivos, como categorias e tags, são menos eficazes. Nos exemplos, incluímos "categoria" de qualquer maneira para demonstrar que você pode incluir vários campos. |

### <a name="analyzer-restrictions-for-sourcefields-in-a-suggester"></a>Restrições de analisadores para fonteFields em um sugestor

A Pesquisa Cognitiva Azure analisa o conteúdo do campo para permitir a consulta em termos individuais. Os sugestionadores exigem que os prefixos sejam indexados para além dos termos completos, o que requer uma análise adicional sobre os campos de origem. Configurações de analisadores personalizados podem combinar qualquer um dos vários tokenizers e filtros, muitas vezes de forma supor que a produção dos prefixos necessários para sugestões é impossível. Por esta razão, a Pesquisa Cognitiva Azure impede que os campos com analisadores personalizados sejam incluídos num sugestor.

> [!NOTE] 
>  Se precisar de contornar a limitação acima, utilize dois campos separados para o mesmo conteúdo. Isto permitirá que um dos campos tenha um sugestor, enquanto o outro pode ser configurado com uma configuração de analisador personalizado.

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester-in-a-query"></a>Use um sugestor numa consulta

Depois de criado um sugestor, ligue para a API apropriada na sua lógica de consulta para invocar a funcionalidade. 

+ [Sugestões REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [API DE DESCANSO Autocompleto](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SugestãoComHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Método AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

A utilização da API é ilustrada na seguinte chamada para a API DE REPOUSO Automática. Há dois takeaways deste exemplo. Em primeiro lugar, como em todas as consultas, a operação é contra a recolha de documentos de um índice. Segundo, pode adicionar parâmetros de consulta. Embora muitos parâmetros de consulta sejam comuns a ambas as APIs, a lista é diferente para cada um.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

Se um sugestionante não for definido no índice, uma chamada para autocompletar ou sugestões falhará.

## <a name="sample-code"></a>Código de exemplo

+ [Crie a sua primeira aplicação em C#](tutorial-csharp-type-ahead-and-suggestions.md) a amostra demonstra uma construção sugerida, consultas sugeridas, navegação autocompleta e facetada. Esta amostra de código funciona num serviço de pesquisa cognitiva Azure e utiliza um índice de Hotéis pré-carregado, por isso tudo o que tem de fazer é pressionar f5 para executar a aplicação. Não é necessária nenhuma subscrição ou inscrição.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) é uma amostra mais antiga que contém código C# e Java. Também demonstra uma construção sugestionária, consultas sugeridas, autocompleta e navegação facial. Esta amostra de código utiliza os dados da amostra [de NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) hospedados. 


## <a name="next-steps"></a>Passos seguintes

Recomendamos o seguinte exemplo para ver como os pedidos são formulados.

> [!div class="nextstepaction"]
> [Sugestões e exemplos autocompletos](search-autocomplete-tutorial.md) 
