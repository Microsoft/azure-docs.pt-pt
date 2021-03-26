---
title: Criar um fornecedor de sugestões
titleSuffix: Azure Cognitive Search
description: Ativar ações de consulta de tipo-a-frente na Azure Cognitive Search criando sugestivos e formulando pedidos que invoquem termos de consulta autocompletos ou auto-cortados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 6bf5e53d9f4a867c146cb01376fcd28d2797819c
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606220"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Crie um sugestivo para permitir resultados autocompletos e sugeridos numa consulta

Em Azure Cognitive Search, typeahead ou "search-as-you-type" é ativado através de um *sugestivo*. Um sugestivo é uma estrutura interna de dados que consiste numa recolha de campos. Os campos são submetidos a tokenização adicional, gerando sequências de prefixos para suportar partidas em termos parciais. Por exemplo, um sugestivo que inclua um campo da Cidade terá combinações de prefixos de "mar", "assento", "seatt" e "seattl" para o termo "Seattle".

Os jogos em termos parciais podem ser uma consulta auto-completa ou uma correspondência sugerida. O mesmo sugestivo apoia ambas as experiências.

## <a name="typeahead-experiences-in-cognitive-search"></a>Experiências typeahead em Pesquisa Cognitiva

Typeahead pode ser *autocompleto*, que completa uma entrada parcial para uma consulta de todo o termo, ou *sugestões* que convidam clicar em um determinado jogo. O autocomplete produz uma consulta. As sugestões produzem um documento correspondente.

A imagem a seguir da Criação da [sua primeira aplicação em C#](tutorial-csharp-type-ahead-and-suggestions.md) ilustra ambas. O autocomplete antecipa um termo potencial, terminando "tw" com "in". As sugestões são resultados de mini pesquisa, onde um campo como o nome do hotel representa um documento de pesquisa de hotel correspondente a partir do índice. Para sugestões, pode emergir qualquer campo que forneça informações descritivas.

![Comparação visual de consultas auto-completas e sugeridas](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Comparação visual de consultas auto-completas e sugeridas")

Pode utilizar estas funcionalidades separadamente ou em conjunto. Para implementar estes comportamentos na Azure Cognitive Search, existe um componente de índice e consulta. 

+ Adicione um sugestivo a uma definição de índice de pesquisa. O restante deste artigo está focado em criar um sugestivo.

+ Ligue para uma consulta habilitada a sugerir, sob a forma de um pedido de sugestão ou de um pedido de auto-preencha, utilizando uma das [APIs listadas abaixo](#how-to-use-a-suggester).

O suporte do tipo search-as-you é ativado numa base por campo para campos de cordas. Pode implementar ambos os comportamentos de tipa dentro da mesma solução de pesquisa se quiser uma experiência semelhante à indicada na imagem. Ambos os pedidos visam a recolha de *documentos* de índice específico e as respostas são devolvidas depois de um utilizador ter fornecido pelo menos uma cadeia de entrada de três caracteres.

## <a name="how-to-create-a-suggester"></a>Como criar um sugestivo

Para criar um sugestivo, adicione um a uma [definição de índice](/rest/api/searchservice/create-index). Um sugestivo tem um nome e uma coleção de campos sobre os quais a experiência typeahead está ativada. A melhor altura para criar um sugestivo é quando também está a definir o campo que o utilizará.

+ Use apenas campos de cordas.

+ Se o campo de cordas fizer parte de um tipo complexo (por exemplo, um campo City dentro do Address), inclua o progenitor no caminho de campo: `"Address/City"` (REST e C# e Python), ou `["Address"]["City"]` (JavaScript).

+ Utilize o analisador padrão padrão Lucene `"analyzer": null` () ou um [analisador de idioma](index-add-language-analyzers.md) (por exemplo, `"analyzer": "en.Microsoft"` ) no campo.

Se tentar criar um sugestivo utilizando campos pré-existentes, a API não o permitirá. Os prefixos são gerados durante a indexação, quando os termos parciais em duas ou mais combinações de caracteres são tokenizados ao lado de termos inteiros. Dado que os campos existentes já estão tokenizados, terá de reconstruir o índice se quiser adicioná-los a um sugestivo. Para obter mais informações, consulte [Como reconstruir um índice de Pesquisa Cognitiva Azure.](search-howto-reindex.md)

### <a name="choose-fields"></a>Escolha campos

Embora um sugestivo tenha várias propriedades, é principalmente uma coleção de campos de cordas para os quais você está permitindo uma experiência de pesquisa como você-você-tipo. Há um sugestivo para cada índice, pelo que a lista de sugestivos deve incluir todos os campos que contribuam com conteúdo tanto para sugestões como para o preconto automático.

O autocompleto beneficia de um conjunto maior de campos para extrair porque o conteúdo adicional tem mais potencial de conclusão a prazo.

As sugestões, por outro lado, produzem melhores resultados quando a sua escolha de campo é seletiva. Lembre-se que a sugestão é um proxy para um documento de pesquisa para que você queira campos que melhor representam um único resultado. Nomes, títulos ou outros campos únicos que distinguem entre vários jogos funcionam melhor. Se os campos consistem em valores repetitivos, as sugestões consistem em resultados idênticos e um utilizador não saberá em que clicar.

Para satisfazer ambas as experiências de pesquisa como você, adicione todos os campos que você precisa para preencher automaticamente, mas depois use "$select", "$top", "$filter" e "searchFields" para controlar os resultados para sugestões.

### <a name="choose-analyzers"></a>Escolha analisadores

A sua escolha de um analisador determina como os campos são tokenizados e posteriormente pré-fixados. Por exemplo, para uma corda hifenizada como "sensível ao contexto", usar um analisador de linguagem resultará nestas combinações simbólicas: "contexto", "sensível", "sensível ao contexto". Se tivesses usado o analisador padrão lucene, a corda hifenizada não existiria. 

Ao avaliar os analisadores, considere a utilização da [API de Texto de Análise](/rest/api/searchservice/test-analyzer) para obter informações sobre a forma como os termos são processados. Uma vez que você constrói um índice, você pode experimentar vários analisadores em uma cadeia para visualizar a saída de token.

Os campos que utilizam [analisadores personalizados](index-add-custom-analyzers.md) ou [analisadores incorporados](index-add-custom-analyzers.md#built-in-analyzers) (com exceção do Lucene padrão) são explicitamente proibidos para evitar maus resultados.

> [!NOTE]
> Se precisar de trabalhar em torno da restrição do analisador, por exemplo, se precisar de uma palavra-chave ou de um analisador de ngram para determinados cenários de consulta, deve utilizar dois campos separados para o mesmo conteúdo. Isto permitirá que um dos campos tenha um sugestivo, enquanto o outro pode ser configurado com uma configuração de analisador personalizado.

## <a name="create-using-the-portal"></a>Criar usando o portal

Ao utilizar **o Add Index** ou o assistente de **dados de importação** para criar um índice, tem a opção de ativar um sugestivo:

1. Na definição de índice, insira um nome para o sugestivo.

1. Em cada definição de campo para novos campos, selecione uma caixa de verificação na coluna Suggester. Uma caixa de verificação está disponível apenas em campos de cordas. 

Como anteriormente notado, a escolha do analisador tem impacto na tokenização e na prefixo. Considere toda a definição de campo ao permitir sugestivos. 

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

Em C#, defina um [objeto SearchSuggester](/dotnet/api/azure.search.documents.indexes.models.searchsuggester). `Suggesters` é uma coleção de um objeto SearchIndex, mas só pode levar um item. Adicione um sugestivo à definição de índice.

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
    definition.Suggesters.Add(suggester);

    indexClient.CreateOrUpdateIndex(definition);
}
```

## <a name="property-reference"></a>Referência de propriedade

|Propriedade      |Descrição      |
|--------------|-----------------|
| name        | Especificado na definição de sugestivo, mas também solicitado por um pedido de Autocomplete ou Sugestões. |
| sourceFields | Especificado na definição de sugestivo. É uma lista de um ou mais campos no índice que são a fonte do conteúdo para sugestões. Os campos devem ser do tipo `Edm.String` `Collection(Edm.String)` e. Se um analisador for especificado no campo, deve ser um analisador lexical nomeado [desta lista](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername) (não um analisador personalizado). </br></br>Como uma boa prática, especifique apenas os campos que se prestam a uma resposta esperada e apropriada, seja uma corda completa em uma barra de pesquisa ou uma lista de abandono. </br></br>Um nome de hotel é um bom candidato porque tem precisão. Campos verbosos como descrições e comentários são demasiado densos. Da mesma forma, os campos repetitivos, como categorias e etiquetas, são menos eficazes. Nos exemplos, incluímos "categoria" de qualquer forma para demonstrar que pode incluir vários campos. |
| searchMode  | Parâmetro apenas para o DESCANSO, mas também visível no portal. Este parâmetro não está disponível no .NET SDK. Indica a estratégia usada para procurar frases de candidato. O único modo atualmente suportado é `analyzingInfixMatching` , que atualmente corresponde ao início de um mandato.|

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Use um sugestivo

Um sugestivo é usado numa consulta. Depois de criar um sugestivo, ligue para uma das seguintes APIs para uma experiência de pesquisa como você:

+ [Sugestões REST API](/rest/api/searchservice/suggestions)
+ [API DE REST autocompleto](/rest/api/searchservice/autocomplete)
+ [Sugerir Método Async](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [Método AutocompleteAsync](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

Numa aplicação de pesquisa, o código do cliente deve aproveitar uma biblioteca como [o jQuery UI Autocomplete](https://jqueryui.com/autocomplete/) para recolher a consulta parcial e fornecer a correspondência. Para obter mais informações sobre esta tarefa, consulte [Adicionar resultados autocompletos ou sugeridos ao código do cliente.](search-add-autocomplete-suggestions.md)

A utilização da API é ilustrada na seguinte chamada para a API autocomplete REST. Este exemplo, há dois takeaways. Em primeiro lugar, como em todas as consultas, a operação é contra a recolha de documentos de um índice e a consulta inclui um parâmetro de "pesquisa", que neste caso fornece a consulta parcial. Em segundo lugar, deve adicionar "nome sugestivo" ao pedido. Se um sugestivo não estiver definido no índice, uma chamada para autocomplete ou sugestões falhará.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>Código de exemplo

+ [Crie a sua primeira aplicação em C# (lição 3 - Adicione a amostra de pesquisa-como-o-tipo)](tutorial-csharp-type-ahead-and-suggestions.md) demonstra consultas sugeridas, navegação autocompleta e frontal. Esta amostra de código funciona num serviço de pesquisa cognitiva Azure e utiliza um índice de Hotéis pré-carregado com um sugestivo já criado, por isso tudo o que tem de fazer é pressionar f5 para executar a aplicação. Não é necessária nenhuma subscrição ou inscrição.

## <a name="next-steps"></a>Passos seguintes

Recomendamos o seguinte artigo para saber mais sobre como os pedidos de formulação.

> [!div class="nextstepaction"]
> [Adicionar autocompleto e sugestões ao código do cliente](search-add-autocomplete-suggestions.md)