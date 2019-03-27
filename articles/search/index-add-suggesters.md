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
ms.openlocfilehash: 5a46575f6e8a0b05b65dbf49c70bddb570b514b2
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497438"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Adicionar sugestores para um índice para typeahead no Azure Search

R **sugestor** é uma construção num [índice da Azure Search](search-what-is-an-index.md) que oferece suporte a uma experiência de "pesquisa-como--type". Ela contém uma lista de campos para o qual pretende ativar typeahead entradas de consulta. Há duas variantes de typeahead: *preenchimento automático* conclui o termo ou frase escrever, *sugestões* fornece uma lista de resultados. 

Na página de pesquisa nesta Xbox, os itens de preenchimento automático levá-lo para uma nova página de resultados de pesquisa para essa consulta, ao passo que as sugestões são os resultados reais que levam-o para uma página para esse jogo específico. Pode limitar o preenchimento automático para um item numa barra de pesquisa ou forneça uma lista semelhante à mostrada aqui. Para obter sugestões, podem surgir a qualquer parte de um documento que melhor descreve o resultado.

![Comparação visual de preenchimento automático e consultas sugeridas](./media/index-add-suggesters/visual-comparison-suggest-complete.png "comparação Visual de preenchimento automático e consultas sugeridas")

Para implementar esses comportamentos no Azure Search, há um componente de índice e a consulta. 

+ Num índice, adicione um sugestor. Pode utilizar o portal, a REST API ou o SDK de .NET para criar um sugestor. 

+ Numa consulta, especifique a ação de uma sugestão ou sutocomplete. 

> [!Important]
> Preenchimento automático está atualmente em pré-visualização, disponível em pré-visualização de REST APIs e SDK do .NET e não é suportada para aplicações de produção. 

Suporte de pesquisa-como--type está ativado numa base por campo. Pode implementar ambos os comportamentos de typeahead dentro da mesma solução de pesquisa se pretender que uma experiência semelhante à indicado na captura de ecrã. Destino de ambos os pedidos a *documentos* coleção de índice específico e as respostas são devolvidas depois de um utilizador forneceu, pelo menos, uma cadeia de caracteres de entrada de três caracteres.

## <a name="create-a-suggester"></a>Criar um fornecedor de sugestões

Embora um sugestor tem várias propriedades, é principalmente uma coleção de campos para o qual pretende ativar uma experiência de typeahead. Por exemplo, uma aplicação de viagens poderá querer ativar pesquisa typeahead sobre destinos, cidades e attractions. Como tal, todos os três campos iria na coleção de campos.

Para criar um sugestor, adicione uma para um esquema de índice. Pode ter um sugestor num índice (especificamente, um sugestor na coleção sugestores). 

### <a name="use-the-rest-api"></a>Utilizar a API REST

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
Depois de um sugestor é criada, adicione a [sugestões de API](https://docs.microsoft.com/rest/api/searchservice/suggestions) ou [API de conclusão automática](https://docs.microsoft.com/rest/api/searchservice/autocomplete) na sua lógica de consulta para invocar a funcionalidade.

### <a name="use-the-net-sdk"></a>Utilizar o .NET SDK

No C#, definir um [classe de Sugestor](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Sugestor é uma coleção, mas apenas pode demorar um item.

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

Pontos-chave a ser observado sobre sugestores é que há um nome (sugestores são referenciados por nome de um pedido), um searchMode (atualmente apenas uma, "analyzingInfixMatching") e a lista de campos para o qual typeahead está ativada. 

Propriedades que definem um sugestor incluem o seguinte:

|Propriedade      |Descrição      |
|--------------|-----------------|
|`name`        |O nome do sugestor. Utilize o nome do sugestor ao chamar o [API do REST de sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions) ou [API de REST de preenchimento automático (pré-visualização)](https://docs.microsoft.com/rest/api/searchservice/autocomplete).|
|`searchMode`  |A estratégia utilizada para pesquisar expressões candidatas. O único modo suportado atualmente é `analyzingInfixMatching`, que executa a correspondência de expressões no início ou no meio de frases flexível.|
|`sourceFields`|Uma lista de um ou mais campos que são a origem do conteúdo para sugestões. Apenas os campos do tipo `Edm.String` e `Collection(Edm.String)` pode dar origem a sugestões. Podem ser usados apenas os campos que não têm um analisador de idioma personalizado definido.<p/>Especifique apenas esses campos que servem para uma resposta esperada e se é apropriada, quer se trate de uma cadeia de caracteres concluída numa barra de pesquisa ou uma lista suspensa.<p/>Um nome de hotel é um bom candidato porque tem precisão. Campos verbosos como descrições e seus comentários são muito densos. Da mesma forma, os campos repetitivos, como categorias e etiquetas, são menos eficazes. Nos exemplos, podemos incluir "category" de qualquer forma, para demonstrar que pode incluir vários campos. |

## <a name="when-to-create-a-suggester"></a>Quando criar um sugestor

Para evitar uma recompilação de índice, uma sugestor e os campos especificados em `sourceFields` tem de ser criado ao mesmo tempo.

Se adicionar um sugestor a um índice existente, onde os campos existentes estão incluídos no `sourceFields`, fundamentalmente altera a definição de campo e uma recompilação é necessária. Para obter mais informações, consulte [como reconstruir um índice da Azure Search](search-howto-reindex.md).

## <a name="how-to-use-a-suggester"></a>Como utilizar um sugestor

Como mencionado anteriormente, pode usar um sugestor para consultas sugeridas, preenchimento automático ou ambos. 

Um sugestor é referenciado no pedido, juntamente com a operação. Por exemplo, numa chamada GET REST, especifique `suggest` ou `autocomplete` na coleção de documentos. Para o resto, depois um sugestor é criada, utilize o [sugestões de API](https://docs.microsoft.com/rest/api/searchservice/suggestions) ou o [preenchimento automático de API (pré-visualização)](https://docs.microsoft.com/rest/api/searchservice/autocomplete) na sua lógica de consulta.

Para o .NET, utilize [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) ou [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet-preview&viewFallbackFrom=azure-dotnet).

Para obter um exemplo que demonstram ambos os pedidos, consulte [exemplo para adicionar o preenchimento automático e sugestões no Azure Search](search-autocomplete-tutorial.md).

## <a name="sample-code"></a>Código de exemplo

O [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) exemplo contém os C# e o código de Java e demonstra uma construção composta por sugestor, consultas sugeridas, preenchimento automático e navegação de faceta. 

Ele usa um sandbox do serviço Azure Search e um índice pré-carregados para que tudo o que precisa fazer é pressione F5 para executá-lo. Nenhuma subscrição ou o início de sessão necessário.

## <a name="next-steps"></a>Passos Seguintes

Recomendamos que o exemplo a seguir para ver como os pedidos são formulados.

> [!div class="nextstepaction"]
> [Exemplo de consulta Autocompleted (pré-visualização)](search-autocomplete-tutorial.md) 
