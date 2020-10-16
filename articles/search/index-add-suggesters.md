---
title: Criar um fornecedor de sugestões
titleSuffix: Azure Cognitive Search
description: Ativar ações de consulta de tipo-a-frente na Azure Cognitive Search criando sugestivos e formulando pedidos que invoquem termos de consulta autocompletos ou auto-cortados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/21/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e439f7d2b0232a2e1c36517f24723e4e16f7e6bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537604"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Crie um sugestivo para permitir resultados autocompletos e sugeridos numa consulta

Na Pesquisa Cognitiva Azure, "search-as-you-type" é ativado através de uma construção **sugestiva** adicionada a um [índice de pesquisa](search-what-is-an-index.md). Um sugestivo suporta duas experiências: *autocomplete,* que completa uma entrada parcial para uma consulta de todo o termo, e *sugestões* que convidam a clicar para uma determinada correspondência. O autocomplete produz uma consulta. As sugestões produzem um documento correspondente.

A imagem a seguir da Criação da [sua primeira aplicação em C#](tutorial-csharp-type-ahead-and-suggestions.md) ilustra ambas. O autocomplete antecipa um termo potencial, terminando "tw" com "in". As sugestões são resultados de mini pesquisa, onde um campo como o nome do hotel representa um documento de pesquisa de hotel correspondente a partir do índice. Para sugestões, pode emergir qualquer campo que forneça informações descritivas.

![Comparação visual de consultas auto-completas e sugeridas](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Comparação visual de consultas auto-completas e sugeridas")

Pode utilizar estas funcionalidades separadamente ou em conjunto. Para implementar estes comportamentos na Azure Cognitive Search, existe um componente de índice e consulta. 

+ No índice, adicione um sugestivo a um índice. Pode utilizar o portal REST [API,](/rest/api/searchservice/create-index)ou [.NET SDK](/dotnet/api/microsoft.azure.search.models.suggester). O restante deste artigo está focado em criar um sugestivo.

+ No pedido de consulta, ligue para uma das [APIs listadas abaixo](#how-to-use-a-suggester).

O suporte do tipo search-as-you é ativado numa base por campo para campos de cordas. Pode implementar ambos os comportamentos de tipa dentro da mesma solução de pesquisa se quiser uma experiência semelhante à indicada na imagem. Ambos os pedidos visam a recolha de *documentos* de índice específico e as respostas são devolvidas depois de um utilizador ter fornecido pelo menos uma cadeia de entrada de três caracteres.

## <a name="what-is-a-suggester"></a>O que é um sugestivo?

Um sugestivo é uma estrutura de dados interna que suporta comportamentos de pesquisa como você-tipo, armazenando prefixos para combinar em consultas parciais. Tal como acontece com os termos simbólicos, os prefixos são armazenados em índices invertidos, um para cada campo especificado numa recolha de campos sugestivos.

## <a name="define-a-suggester"></a>Defina um sugestivo

Para criar um sugestivo, adicione um a um [esquema de índice](/rest/api/searchservice/create-index) e [desemote cada propriedade](#property-reference). A melhor altura para criar um sugestivo é quando também está a definir o campo que o utilizará.

+ Use apenas campos de cordas

+ Utilize o analisador padrão padrão Lucene `"analyzer": null` () ou um [analisador de idioma](index-add-language-analyzers.md) (por exemplo, `"analyzer": "en.Microsoft"` ) no campo

### <a name="choose-fields"></a>Escolha campos

Embora um sugestivo tenha várias propriedades, é principalmente uma coleção de campos de cordas para os quais você está permitindo uma experiência de pesquisa como você-você-tipo. Há um sugestivo para cada índice, pelo que a lista de sugestivos deve incluir todos os campos que contribuam com conteúdo tanto para sugestões como para o preconto automático.

O autocompleto beneficia de um conjunto maior de campos para extrair porque o conteúdo adicional tem mais potencial de conclusão a prazo.

As sugestões, por outro lado, produzem melhores resultados quando a sua escolha de campo é seletiva. Lembre-se que a sugestão é um proxy para um documento de pesquisa para que você queira campos que melhor representam um único resultado. Nomes, títulos ou outros campos únicos que distinguem entre vários jogos funcionam melhor. Se os campos consistem em valores repetitivos, as sugestões consistem em resultados idênticos e um utilizador não saberá em que clicar.

Para satisfazer ambas as experiências de pesquisa como você, adicione todos os campos que você precisa para preencher automaticamente, mas depois use **$select**, **$top**, **$filter**, e **searchFields** para controlar resultados para sugestões.

### <a name="choose-analyzers"></a>Escolha analisadores

A sua escolha de um analisador determina como os campos são tokenizados e posteriormente pré-fixados. Por exemplo, para uma corda hifenizada como "sensível ao contexto", usar um analisador de linguagem resultará nestas combinações simbólicas: "contexto", "sensível", "sensível ao contexto". Se tivesses usado o analisador padrão lucene, a corda hifenizada não existiria. 

Ao avaliar os analisadores, considere a utilização da [API de Texto de Análise](/rest/api/searchservice/test-analyzer) para obter uma visão de como os termos são tokenizados e posteriormente pré-fixados. Uma vez que você constrói um índice, você pode experimentar vários analisadores em uma cadeia para visualizar a saída de token.

Os campos que utilizam [analisadores personalizados](index-add-custom-analyzers.md) ou [analisadores predefinidos](index-add-custom-analyzers.md#predefined-analyzers-reference) (com exceção do Lucene padrão) são explicitamente proibidos para evitar maus resultados.

> [!NOTE]
> Se precisar de trabalhar em torno da restrição do analisador, por exemplo, se precisar de uma palavra-chave ou de um analisador de ngram para determinados cenários de consulta, deve utilizar dois campos separados para o mesmo conteúdo. Isto permitirá que um dos campos tenha um sugestivo, enquanto o outro pode ser configurado com uma configuração de analisador personalizado.

### <a name="when-to-create-a-suggester"></a>Quando criar um sugestivo

A melhor altura para criar um sugestivo é quando também está a criar a própria definição de campo.

Se tentar criar um sugestivo utilizando campos pré-existentes, a API não o permitirá. Os prefixos são gerados durante a indexação, quando os termos parciais em duas ou mais combinações de caracteres são tokenizados ao lado de termos inteiros. Dado que os campos existentes já estão tokenizados, terá de reconstruir o índice se quiser adicioná-los a um sugestivo. Para obter mais informações, consulte [Como reconstruir um índice de Pesquisa Cognitiva Azure.](search-howto-reindex.md)

## <a name="create-using-rest"></a>Criar usando REST

Na API REST, adicione sugestivos através do [Índice de Criação](/rest/api/searchservice/create-index) ou [do Índice de Atualização](/rest/api/searchservice/update-index). 

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

## <a name="create-using-net"></a>Criar usando .NET

Em C#, defina um [objeto Suggester](/dotnet/api/microsoft.azure.search.models.suggester). `Suggesters` é uma coleção, mas só pode levar um item. 

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

## <a name="property-reference"></a>Referência de propriedade

|Propriedade      |Descrição      |
|--------------|-----------------|
|`name`        |O nome do sugestivo.|
|`searchMode`  |A estratégia usada para procurar frases de candidato. O único modo atualmente suportado é `analyzingInfixMatching` , que atualmente corresponde ao início de um mandato.|
|`sourceFields`|Uma lista de um ou mais campos que são a fonte do conteúdo para sugestões. Os campos devem ser do tipo `Edm.String` `Collection(Edm.String)` e. Se um analisador for especificado no campo, deve ser um analisador nomeado [desta lista](/dotnet/api/microsoft.azure.search.models.analyzername) (não um analisador personalizado).<p/> Como uma boa prática, especifique apenas os campos que se prestam a uma resposta esperada e apropriada, seja uma corda completa em uma barra de pesquisa ou uma lista de abandono.<p/>Um nome de hotel é um bom candidato porque tem precisão. Campos verbosos como descrições e comentários são demasiado densos. Da mesma forma, os campos repetitivos, como categorias e etiquetas, são menos eficazes. Nos exemplos, incluímos "categoria" de qualquer forma para demonstrar que pode incluir vários campos. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Use um sugestivo

Um sugestivo é usado numa consulta. Depois de criar um sugestivo, ligue para uma das seguintes APIs para uma experiência de pesquisa como você:

+ [Sugestões REST API](/rest/api/searchservice/suggestions) 
+ [API DE REST autocompleto](/rest/api/searchservice/autocomplete) 
+ [SugestionAr Método DeHttpMessagesAsync] (/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?
+ [Método AutocompleteWithHttpMessagesAsync](/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync)

Numa aplicação de pesquisa, o código do cliente deve aproveitar uma biblioteca como [o jQuery UI Autocomplete](https://jqueryui.com/autocomplete/) para recolher a consulta parcial e fornecer a correspondência. Para obter mais informações sobre esta tarefa, consulte [Adicionar resultados autocompletos ou sugeridos ao código do cliente.](search-autocomplete-tutorial.md)

A utilização da API é ilustrada na seguinte chamada para a API autocomplete REST. Este exemplo, há dois takeaways. Em primeiro lugar, como em todas as consultas, a operação é contra a recolha de documentos de um índice e a consulta inclui um parâmetro **de pesquisa,** que neste caso fornece a consulta parcial. Segundo, deve adicionar o nome do **sugestivo** ao pedido. Se um sugestivo não estiver definido no índice, uma chamada para autocomplete ou sugestões falhará.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>Código de exemplo

+ [Crie a sua primeira aplicação em C# (lição 3 - Adicione a amostra de pesquisa-como-o-tipo)](tutorial-csharp-type-ahead-and-suggestions.md) demonstra uma construção sugestiva, consultas sugeridas, autocomplete e navegação frontal. Esta amostra de código funciona num serviço de pesquisa cognitiva Azure e utiliza um índice de Hotéis pré-carregados, por isso tudo o que tem de fazer é pressionar f5 para executar a aplicação. Não é necessária nenhuma subscrição ou inscrição.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) é uma amostra mais antiga que contém código C# e Java. Também demonstra uma construção sugestiva, consultas sugeridas, navegação autocompleta e facetada. Esta amostra de código utiliza os dados da amostra [do NYCJobs hospedados.](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 

## <a name="next-steps"></a>Passos seguintes

Recomendamos o seguinte artigo para saber mais sobre como os pedidos de formulação.

> [!div class="nextstepaction"]
> [Adicionar autocompleto e sugestões ao código do cliente](search-autocomplete-tutorial.md)