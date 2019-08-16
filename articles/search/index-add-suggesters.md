---
title: Adicionar consultas typeahead a um índice-Azure Search
description: Habilite ações de consulta de tipo antecipado em Azure Search criando sugestores e solicitações formular que invoquem os termos de consulta autocompletados ou autosugeridos.
ms.date: 05/02/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: 22b53000fa2eebdd8f9cf7fd9f1a2d00763c035b
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533199"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Adicionar sugestores a um índice para typeahead no Azure Search

Um Sugestor é um constructo em um [índice de Azure Search](search-what-is-an-index.md) que dá suporte a uma experiência de "pesquisa conforme o uso". Ele contém uma lista de campos para os quais você deseja habilitar as entradas de consulta typeahead. Dentro de um índice, o mesmo Sugestor dá suporte a uma ou ambas as duas variantes typeahead: preenchimento automático conclui o termo ou frase que você está digitando, *sugestões* fornece uma breve lista de resultados. 

A captura de tela a seguir ilustra os dois recursos do typeahead. Nesta página de pesquisa do Xbox, os itens de preenchimento automático levam você a uma nova página de resultados da pesquisa para essa consulta, enquanto as sugestões são resultados reais que levam você a uma página para esse jogo específico. Você pode limitar o preenchimento automático a um item em uma barra de pesquisa ou fornecer uma lista como a mostrada aqui. Para obter sugestões, você pode retonar qualquer parte de um documento que melhor descreva o resultado.

![Comparação visual de consultas autocompletas e sugeridas](./media/index-add-suggesters/visual-comparison-suggest-complete.png "Comparação visual de consultas autocompletas e sugeridas")

Para implementar esses comportamentos no Azure Search, há um componente de índice e consulta. 

+ O componente de índice é um Sugestor. Você pode usar o portal, a API REST ou o SDK do .NET para criar um Sugestor. 

+ O componente de consulta é uma ação especificada na solicitação de consulta (uma ação de sugestão ou de preenchimento automático). 

O suporte de pesquisa conforme o tipo é habilitado em uma base por campo. Você pode implementar ambos os comportamentos do typeahead na mesma solução de pesquisa se desejar uma experiência semelhante à indicada na captura de tela. Ambas as solicitações direcionam a coleção de *documentos* de índice e respostas específicas são retornadas depois que um usuário tiver fornecido pelo menos uma cadeia de caracteres de entrada de três caracteres.

## <a name="create-a-suggester"></a>Criar um fornecedor de sugestões

Embora um Sugestor tenha várias propriedades, ele é basicamente uma coleção de campos para os quais você está habilitando uma experiência typeahead. Por exemplo, um aplicativo de viagem pode querer habilitar a pesquisa typeahead em destinos, cidades e Attractions. Assim, todos os três campos seriam na coleção Fields.

Para criar um Sugestor, adicione um a um esquema de índice. Você pode ter um Sugestor em um índice (especificamente, um Sugestor na coleção de sugestores). 

### <a name="use-the-rest-api"></a>Utilizar a API REST

Na API REST, você pode adicionar sugestores por meio de [CREATE INDEX](https://docs.microsoft.com/rest/api/searchservice/create-index) ou [Update index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

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
Depois que um Sugestor for criado, adicione a [API de sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions) ou a [API de preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete) em sua lógica de consulta para invocar o recurso.

### <a name="use-the-net-sdk"></a>Utilizar o .NET SDK

No C#, defina um [objeto de sugestão](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters`é uma coleção, mas só pode pegar um item. 

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

## <a name="property-reference"></a>Referência de propriedade

Os principais pontos a serem observados sobre os sugestores é que há um nome (sugestores são referenciados pelo nome em uma solicitação), um searchmode (atualmente, apenas um, "analyzingInfixMatching") e a lista de campos para os quais typeahead está habilitado. 

As propriedades que definem um Sugestor incluem o seguinte:

|Propriedade      |Descrição      |
|--------------|-----------------|
|`name`        |O nome do Sugestor. Use o nome do Sugestor ao chamar a [API REST de sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions) ou a [API REST de preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete).|
|`searchMode`  |A estratégia usada para pesquisar frases candidatas. O único modo com suporte no `analyzingInfixMatching`momento é, que executa a correspondência flexível de frases no início ou no meio de frases.|
|`sourceFields`|Uma lista de um ou mais campos que são a origem do conteúdo para sugestões. Somente os campos do `Edm.String` tipo `Collection(Edm.String)` e podem ser fontes para sugestões. Somente os campos que não têm um conjunto de analisador de linguagem personalizado podem ser usados.<p/>Especifique somente os campos que se prestam a uma resposta esperada e apropriada, seja uma cadeia de caracteres completa em uma barra de pesquisa ou uma lista suspensa.<p/>Um nome de Hotel é um bom candidato porque tem precisão. Campos detalhados, como descrições e comentários, são muito densos. Da mesma forma, campos repetitivos, como categorias e marcas, são menos eficazes. Nos exemplos, incluímos "category" de qualquer forma para demonstrar que você pode incluir vários campos. |

#### <a name="analysis-of-sourcefields-in-a-suggester"></a>Análise de SourceFields em um Sugestor

Azure Search analisa o conteúdo do campo para habilitar a consulta em termos individuais. Os sugestores exigem que os prefixos sejam indexados além dos termos completos, o que requer análise adicional nos campos de origem. As configurações personalizadas do analisador podem combinar qualquer um dos vários filtros e criadores, geralmente de maneira que tornaria a produção dos prefixos necessários para sugestões impossível. Por esse motivo, **Azure Search impede que os campos com analisadores personalizados sejam incluídos em um**Sugestor.

> [!NOTE] 
>  A abordagem recomendada para contornar a limitação acima é usar dois campos separados para o mesmo conteúdo. Isso permitirá que um dos campos tenha sugestores e o outro possa ser configurado com uma configuração personalizada do analisador.

## <a name="when-to-create-a-suggester"></a>Quando criar um Sugestor

Para evitar uma recompilação de índice, um Sugestor e os `sourceFields` campos especificados em devem ser criados ao mesmo tempo.

Se você adicionar um Sugestor a um índice existente, em que os campos existentes `sourceFields`são incluídos no, a definição de campo é fundamentalmente alterada e uma recompilação é necessária. Para obter mais informações, consulte [como recriar um índice de Azure Search](search-howto-reindex.md).

## <a name="how-to-use-a-suggester"></a>Como usar um Sugestor

Conforme observado anteriormente, você pode usar um Sugestor para consultas sugeridas, preenchimento automático ou ambos. 

Um Sugestor é referenciado na solicitação junto com a operação. Por exemplo, em uma chamada Get REST, especifique `suggest` ou `autocomplete` na coleção documentos. Para REST, depois que um Sugestor for criado, use a [API de sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions) ou a [API de preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete) em sua lógica de consulta.

Para .NET, use [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet) ou [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet).

Para ver um exemplo que demonstra as duas solicitações, consulte o [exemplo para adicionar preenchimento automático e sugestões em Azure Search](search-autocomplete-tutorial.md).

## <a name="sample-code"></a>Código de exemplo

O exemplo [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) contém o C# e o código Java e demonstra uma construção de sugestão, consultas sugeridas, preenchimento automático e navegação de faceta. 

Ele usa um serviço de Azure Search de área restrita e um índice pré-carregado para que tudo o que você precisa fazer seja pressionar F5 para executá-lo. Nenhuma assinatura ou entrada necessária.

## <a name="next-steps"></a>Passos Seguintes

Recomendamos o exemplo a seguir para ver como as solicitações são formuladas.

> [!div class="nextstepaction"]
> [Sugestões e exemplos de preenchimento automático](search-autocomplete-tutorial.md) 
