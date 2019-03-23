---
title: Adicionar typeahead consultas para um índice - Azure Search
description: Ative ações de consulta antecipada no Azure Search criando sugestores e formular pedidos que invocar o preenchimento automático ou autosuggested termos de consulta.
ms.date: 03/22/2019
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
ms.openlocfilehash: 877294e80d655ab75be78a5aa57854a03a5f267a
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370657"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Adicionar sugestores para um índice para typeahead no Azure Search

R **sugestor** é uma construção num [índice da Azure Search](search-what-is-an-index.md) que oferece suporte a uma experiência de "pesquisa-como--type". Ela contém uma lista de campos para o qual pretende ativar typeahead entradas de consulta. Há duas variantes de typeahead: [ *preenchimento automático* ](search-autocomplete-tutorial.md) conclui o termo ou frase escrever, [ *sugestão automática* ](search-autosuggest-example.md) fornece um curto período lista de termos ou frases que pode selecionar uma consulta de entrada. Viu, sem dúvida, esses comportamentos antes em mecanismos de pesquisa comercial.

![Comparação visual de conclusão automática e de sugestão automática](./media/index-add-suggesters/visual-comparison-suggest-complete.png "comparação Visual de conclusão automática e de sugestão automática")

Para implementar esses comportamentos no Azure Search, há um componente de índice e a consulta. 

+ Num índice, adicione um sugestor. Pode utilizar o portal, a REST API ou .NET SDK para criar um sugestor. 

+ Numa consulta, especifique a ação de uma sugestão automática ou o preenchimento automático. 

> [!Important]
> Preenchimento automático está atualmente em pré-visualização, disponível em pré-visualização de REST APIs e SDK do .NET e não é suportada para aplicações de produção. 

Suporte de Typeahead está ativado numa base por campo. Pode implementar ambos os comportamentos de typeahead dentro da mesma solução de pesquisa se pretender que uma experiência semelhante à indicado na captura de ecrã. Destino de ambos os pedidos a *documentos* coleção de índice específico e as respostas são devolvidas depois de um utilizador forneceu, pelo menos, uma cadeia de caracteres de entrada de três caracteres.

## <a name="create-a-suggester"></a>Criar um sugestor

Embora um sugestor tem várias propriedades, é principalmente uma coleção de campos para o qual pretende ativar uma experiência de typeahead. Por exemplo, uma aplicação de viagens poderá querer ativar pesquisa typeahead sobre destinos, cidades e attractions. Como tal, todos os três campos iria na coleção de campos.

Para criar um sugestor, adicione uma para um esquema de índice. Pode ter um sugestor num índice (especificamente, um sugestor na coleção sugestores). 

Na API do REST, pode adicionar sugestores através de [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) ou [atualização índice](https://docs.microsoft.com/rest/api/searchservice/update-index). 

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

Pontos-chave a ser observado sobre sugestores é que há um nome (sugestores são referenciados por nome de um pedido), um searchMode (atualmente apenas uma, "analyzingInfixMatching") e a lista de campos para o qual typeahead está ativada. 

Depois de um sugestor é criada, adicione a [sugestões API](https://docs.microsoft.com/rest/api/searchservice/suggestions) na sua lógica de consulta para invocar a funcionalidade.

### <a name="property-reference"></a>Referência de propriedade

Propriedades que definem um sugestor incluem o seguinte:

|Propriedade      |Descrição      |
|--------------|-----------------|
|`name`        |O nome do sugestor. Utilize o nome do sugestor ao chamar o [API do REST de sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions) ou [API de REST de preenchimento automático (pré-visualização)](https://docs.microsoft.com/rest/api/searchservice/autocomplete).|
|`searchMode`  |A estratégia utilizada para pesquisar expressões candidatas. O único modo suportado atualmente é `analyzingInfixMatching`, que executa a correspondência de expressões no início ou no meio de frases flexível.|
|`sourceFields`|Uma lista de um ou mais campos que são a origem do conteúdo para sugestões. Apenas os campos do tipo `Edm.String` e `Collection(Edm.String)` pode dar origem a sugestões. Podem ser usados apenas os campos que não têm um analisador de idioma personalizado definido.<p/>Especifique apenas esses campos que servem para uma resposta esperada e se é apropriada, quer se trate de uma cadeia de caracteres concluída numa barra de pesquisa ou uma lista suspensa.<p/>Um nome de hotel é um bom candidato porque tem precisão. Campos verbosos como descrições e seus comentários são muito densos. Da mesma forma, os campos repetitivos, como categorias e etiquetas, são menos eficazes. Nos exemplos, podemos incluir "category" de qualquer forma, para demonstrar que pode incluir vários campos. |

### <a name="index-rebuilds-for-existing-fields"></a>As recompilações de índice para campos existentes

Sugestores contenham campos e, se estiver a adicionar um sugestor a um índice existente ou alterar a sua composição de campo, provavelmente terá que reconstruir o índice.

| Ação | Impacto |
|--------|--------|
| Criar novos campos e crie um novo sugestor simultaneamente na mesma atualização | Menos impacto. Se um índice contém campos adicionados anteriormente, a adição de novos campos e uma nova sugestor não tem impacto sobre campos existentes. |
| Adicione campos já existentes para um sugestor | Alto impacto. Adicionar um campo altera a definição de campo, para evitar a necessidade de um [reconstrução completa](search-howto-reindex.md).|

## <a name="use-a-suggester"></a>Utilizar um sugestor

Como mencionado anteriormente, pode usar um sugestor sugestão automática, preenchimento automático, ou ambas. 

Um sugestor é referenciado no pedido, juntamente com a operação. Por exemplo, numa chamada GET REST, especifique `suggest` ou `autocomplete` na coleção de documentos. Para o resto, depois um sugestor é criada, utilize o [sugestões de API](https://docs.microsoft.com/rest/api/searchservice/suggestions) ou o [preenchimento automático de API (pré-visualização)](https://docs.microsoft.com/rest/api/searchservice/autocomplete) na sua lógica de consulta.

Para o .NET, utilize [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) ou [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet-preview&viewFallbackFrom=azure-dotnet).

Exemplos que demonstram a cada solicitação:

+ [Adicionar sugestão automática para as seleções de consulta de lista pendente](search-autosuggest-example.md)

+ [Adicionar preenchimento automático para entradas de termo parcial no Azure Search](search-autocomplete-tutorial.md) (em pré-visualização) 

## <a name="sample-code"></a>Código de exemplo

O [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) exemplo contém os C# e o código de Java e demonstra uma construção composta por sugestor, sugestão automática, preenchimento automático e navegação de faceta. 

Ele usa um sandbox do serviço Azure Search e um índice pré-carregados para que tudo o que precisa fazer é pressione F5 para executá-lo. Nenhuma subscrição ou o início de sessão necessário.

## <a name="next-steps"></a>Passos Seguintes

Reveja os exemplos seguintes para ver como os pedidos são formulados:

+ [Exemplo de consulta autosuggested](search-autosuggest-example.md) 
+ [Exemplo de consulta Autocompleted (pré-visualização)](search-autocomplete-tutorial.md) 
