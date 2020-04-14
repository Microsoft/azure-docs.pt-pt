---
title: Criar um fornecedor de sugestões
titleSuffix: Azure Cognitive Search
description: Ative ações de consulta tipo-à-frente na Pesquisa Cognitiva Azure, criando sugestionantes e formulando pedidos que invoquem termos de consulta autocompletos ou auto-sugeridos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: d40d4cfe1b86448f1e8df307013905d69f203dcd
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261062"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Criar um sugestor para permitir resultados autocompletos e sugeridos numa consulta

Na Pesquisa Cognitiva Azure, o "search-as-you-type" é ativado através de uma construção **de sugestionante** adicionada a um índice de [pesquisa](search-what-is-an-index.md). Um sugestionador suporta duas experiências: *autocomplete,* que completa o termo ou frase, e *sugestões* que devolvem uma pequena lista de documentos correspondentes.  

A seguinte imagem de [Create your first app in C#](tutorial-csharp-type-ahead-and-suggestions.md) ilustra ambos. A Autocomplete antecipa um termo potencial, terminando "tw" com "in". Sugestões são resultados de mini pesquisa, onde um campo como o nome do hotel representa um documento de pesquisa de hotel correspondente do índice. Para sugestões, pode emergir qualquer campo que forneça informações descritivas.

![Comparação visual de consultas autocompletas e sugeridas](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Comparação visual de consultas autocompletas e sugeridas")

Pode utilizar estas funcionalidades separadamente ou em conjunto. Para implementar estes comportamentos na Pesquisa Cognitiva Azure, existe um componente de indexação e consulta. 

+ No índice, adicione um sugestor a um índice. Pode utilizar o portal, [REST API,](https://docs.microsoft.com/rest/api/searchservice/create-index)ou [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). O restante deste artigo está focado na criação de um sugestor.

+ No pedido de consulta, ligue para uma das [APIs listadas abaixo](#how-to-use-a-suggester).

O suporte de pesquisa como o tipo de pesquisa é ativado numa base por campo para campos de cordas. Pode implementar ambos os comportamentos da tipografia dentro da mesma solução de pesquisa se quiser uma experiência semelhante à indicada na imagem. Ambos os pedidos visam a recolha de *documentos* de índice específico e as respostas são devolvidas depois de um utilizador ter fornecido pelo menos uma cadeia de entrada de três caracteres.

## <a name="what-is-a-suggester"></a>O que é um sugeridor?

Um sugestivo é uma estrutura de dados que suporta comportamentos de pesquisa como você tipo, armazenando prefixos para combinar em consultas parciais. Semelhantes a termos tokenizados, os prefixos são armazenados em índices invertidos, um para cada campo especificado numa coleção de campos sugestionantes.

Ao criar prefixos, um sugestor tem a sua própria cadeia de análise, semelhante à utilizada para a pesquisa completa de texto. No entanto, ao contrário da análise em plena pesquisa de texto, um sugestor só pode operar sobre campos que usam o analisador lucene padrão (padrão) ou um [analisador](index-add-language-analyzers.md)de linguagem . Os campos que utilizam [analisadores personalizados](index-add-custom-analyzers.md) ou [analisadores predefinidos](index-add-custom-analyzers.md#predefined-analyzers-reference) (com exceção do Lucene padrão) são explicitamente proibidos de evitar maus resultados.

> [!NOTE]
> Se precisar de contornar a restrição do analisador, utilize dois campos separados para o mesmo conteúdo. Isto permitirá que um dos campos tenha um sugestor, enquanto o outro pode ser configurado com uma configuração de analisador personalizado.

## <a name="define-a-suggester"></a>Defina um sugestionista

Embora um sugestionista tenha várias propriedades, é principalmente uma coleção de campos para os quais você está permitindo uma experiência de pesquisa como você-tipo. Por exemplo, uma aplicação de viagem pode querer permitir a conclusão automática em destinos, cidades e atrações. Como tal, os três campos iriam para a coleção de campos.

Para criar um sugestor, adicione um a um esquema de índice. Pode ter um sugestor num índice (especificamente, um sugestor na coleção de sugestionantes). Um sugestor tem uma lista de campos. 

+ Para sugestões, escolha campos que melhor representem um único resultado. Nomes, títulos ou outros campos únicos que distinguem entre documentos funcionam melhor. Se os campos consistirem em valores semelhantes ou idênticos, as sugestões serão compostas por resultados idênticos e um utilizador não saberá qual clicar.

+ Certifique-se de que `sourceFields` cada campo na lista de sugestionantes utiliza o analisador lucene padrão padrão ()`"analyzer": null`ou um [analisador](index-add-language-analyzers.md) de linguagem (por exemplo). `"analyzer": "en.Microsoft"` 

  A sua escolha de um analisador determina como os campos são tokenizados e subsequentemente pré-fixados. Por exemplo, para uma cadeia hifenizada como "sensível ao contexto", usar um analisador de linguagem resultará nestas combinações simbólicas: "contexto", "sensível", "sensível ao contexto". Se tivesses usado o analisador lucene padrão, a corda hifenizada não existiria.

> [!TIP]
> Considere utilizar a [API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) de Texto analisar para obter informações sobre como os termos são tokenizados e subsequentemente pré-fixados. Uma vez construído um índice, pode experimentar vários analisadores numa corda para ver os tokens que emite.

### <a name="when-to-create-a-suggester"></a>Quando criar um sugestor

A melhor altura para criar um sugestionista é quando também estás a criar a definição de campo em si.

Se tentar criar um sugestionante utilizando campos pré-existentes, a API irá desafogá-la. Os prefixos são gerados durante a indexação, quando os termos parciais em duas ou mais combinações de caracteres são tokenizados ao lado de termos inteiros. Dado que os campos existentes já estão tokenizados, terá de reconstruir o índice se quiser adicioná-los a um sugestionista. Para mais informações, consulte Como reconstruir um índice de [pesquisa cognitiva Azure](search-howto-reindex.md).

### <a name="create-using-the-rest-api"></a>Criar usando a API REST

Na API REST, adicione os sugestores através do [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) ou do [Update Index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
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
        Name = "hotels-sample-index",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelName", "Category" }
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
|`sourceFields`|Uma lista de um ou mais campos que são a fonte do conteúdo para sugestões. Os campos devem `Edm.String` `Collection(Edm.String)`ser de tipo e . Se um analisador for especificado no campo, deve ser um analisador nomeado [desta lista](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (não um analisador personalizado).<p/> Como uma boa prática, especifique apenas os campos que se prestam a uma resposta esperada e apropriada, seja uma corda completa numa barra de pesquisa ou uma lista de abandono.<p/>Um nome de hotel é um bom candidato porque tem precisão. Campos verbosos como descrições e comentários são muito densos. Da mesma forma, os campos repetitivos, como categorias e tags, são menos eficazes. Nos exemplos, incluímos "categoria" de qualquer maneira para demonstrar que você pode incluir vários campos. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Use um sugestor

Um sugestor é usado numa consulta. Depois de criado um sugestor, ligue para a API apropriada na sua lógica de consulta para invocar a funcionalidade. 

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
