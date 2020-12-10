---
title: Como trabalhar com os resultados da pesquisa
titleSuffix: Azure Cognitive Search
description: Estruturar e ordenar resultados de pesquisa, obter uma contagem de documentos e adicionar navegação de conteúdo aos resultados de pesquisa em Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 182ec758a8764a959b39296163e63e800cf5108c
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008490"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Como trabalhar com resultados de pesquisa na Azure Cognitive Search

Este artigo explica como formular uma resposta de consulta na Pesquisa Cognitiva Azure. A estrutura de uma resposta é determinada por parâmetros na consulta: [Documento de Pesquisa](/rest/api/searchservice/Search-Documents) na API REST, ou Classe [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1) no .NET SDK. Os parâmetros da consulta podem ser utilizados para estruturar o resultado definido de forma a seguir:

+ Limitar ou ema pordual o número de documentos nos resultados (50 por padrão)
+ Selecione campos para incluir nos resultados
+ Resultados da encomenda
+ Destacar um termo total ou parcial correspondente no corpo dos resultados da pesquisa

## <a name="result-composition"></a>Composição do resultado

Embora um documento de pesquisa possa consistir num grande número de campos, normalmente apenas alguns são necessários para representar cada documento no conjunto de resultados. Num pedido de consulta, apêndice `$select=<field list>` para especificar quais os campos que aparecem na resposta. Um campo deve ser atribuído como **Recuperável** no índice a ser incluído num resultado. 

Os campos que funcionam melhor incluem aqueles que contrastam e diferenciam entre documentos, fornecendo informações suficientes para convidar uma resposta click-through por parte do utilizador. Num site de e-commerce, pode ser um nome de produto, descrição, marca, cor, tamanho, preço e classificação. Para a amostra incorporada do índice de amostras de hotéis, pode ser um dos campos que se seguem:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> Se quiser incluir ficheiros de imagem como uma foto ou logotipo do produto, guarde-os fora da Pesquisa Cognitiva Azure, mas inclua um campo no seu índice para fazer referência ao URL de imagem no documento de pesquisa. Os índices de amostra que suportam as imagens nos resultados incluem a demonstração **realestate-sample-us,** apresentada neste [quickstart](search-create-app-portal.md), e a [app de demonstração new york city jobs](https://aka.ms/azjobsdemo).

### <a name="tips-for-unexpected-results"></a>Dicas para resultados inesperados

Ocasionalmente, a substância e não a estrutura dos resultados são inesperadas. Quando os resultados da consulta são inesperados, pode experimentar estas modificações de consulta para ver se os resultados melhoram:

+ Alterar **`searchMode=any`** (predefinição) **`searchMode=all`** para exigir correspondências em todos os critérios em vez de qualquer um dos critérios. Isto é especialmente verdade quando os operadores booleans são incluídos na consulta.

+ Experimente diferentes analisadores lexical ou analisadores personalizados para ver se altera o resultado da consulta. O analisador predefinido quebrará palavras hifenizadas e reduzirá as palavras às formas de raiz, o que geralmente melhora a robustez de uma resposta de consulta. No entanto, se precisar de preservar hífens, ou se as cordas incluim caracteres especiais, poderá precisar de configurar analisadores personalizados para garantir que o índice contém fichas no formato certo. Para obter mais informações, consulte [a pesquisa parcial e padrões com caracteres especiais (hífenes, wildcard, regex, padrões)](search-query-partial-matching.md).

## <a name="paging-results"></a>Resultados de paginação

Por predefinição, o motor de busca retorna até aos primeiros 50 jogos, conforme determinado pela pontuação de pesquisa se a consulta for a pesquisa completa por texto ou numa ordem arbitrária para consultas de correspondência exatas.

Para devolver um número diferente de documentos correspondentes, adicione `$top` e `$skip` parâmetros ao pedido de consulta. A seguinte lista explica a lógica.

+ Adicione `$count=true` para obter uma contagem do número total de documentos correspondentes dentro de um índice.

+ Devolva o primeiro conjunto de 15 documentos correspondentes mais uma contagem de correspondências totais: `GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ Volte a jogar o segundo conjunto, saltando os primeiros 15 para obter os próximos 15: `$top=15&$skip=15` . Faça o mesmo para o terceiro conjunto de 15: `$top=15&$skip=30`

Os resultados das consultas paginadas não são garantidos como estáveis se o índice subjacente estiver a mudar. A paging altera o valor de `$skip` cada página, mas cada consulta é independente e opera na visão atual dos dados tal como existe no índice no momento da consulta (por outras palavras, não há caching ou instantâneo de resultados, como os encontrados numa base de dados para fins gerais).
 
Segue-se um exemplo de como se pode obter duplicados. Assuma um índice com quatro documentos:

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
{ "id": "4", "rating": 1 }
```
 
Agora assuma que quer resultados devolvidos dois de cada vez, ordenados por classificação. Executaria esta consulta para obter a primeira página de resultados: `$top=2&$skip=0&$orderby=rating desc` , produzindo os seguintes resultados:

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
```
 
No serviço, assuma que um quinto documento é adicionado ao índice entre chamadas de consulta: `{ "id": "5", "rating": 4 }` .  Pouco depois, executa-se uma consulta para ir buscar a segunda página: `$top=2&$skip=2&$orderby=rating desc` e obter estes resultados:

```text
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
```
 
Note que o documento 2 é recolhido duas vezes. Isto porque o novo documento 5 tem um valor maior para a classificação, por isso classifica antes do documento 2 e aterra na primeira página. Embora este comportamento possa ser inesperado, é típico de como um motor de busca se comporta.

## <a name="ordering-results"></a>Ordenar resultados

Para consultas completas de pesquisa por texto, os resultados são automaticamente classificados por uma pontuação de pesquisa, calculada com base na frequência de prazo e proximidade de um documento (derivado de [TF-IDF),](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)com pontuações mais altas indo para documentos com mais ou mais fósforos num termo de pesquisa. 

As pontuações de pesquisa transmitem sentido geral de relevância, refletindo a força da correspondência em relação a outros documentos no mesmo conjunto de resultados. Mas as pontuações nem sempre são consistentes de uma consulta para a outra, por isso, à medida que trabalha com consultas, poderá notar pequenas discrepâncias na forma como os documentos de pesquisa são encomendados. Há várias explicações para o porquê disto acontecer.

| Causa | Descrição |
|-----------|-------------|
| Volatilidade dos dados | O conteúdo do índice varia à medida que adiciona, modifica ou elimina documentos. As frequências de prazo mudarão à medida que as atualizações de índices forem processadas ao longo do tempo, afetando as pontuações de pesquisa de documentos correspondentes. |
| Várias réplicas | Para serviços que utilizam réplicas múltiplas, as consultas são emitidas contra cada réplica em paralelo. As estatísticas de índice utilizadas para calcular uma pontuação de pesquisa são calculadas numa base por réplica, com resultados fundidos e encomendados na resposta de consulta. As réplicas são sobretudo espelhos uns dos outros, mas as estatísticas podem diferir devido a pequenas diferenças de estado. Por exemplo, uma réplica poderia ter eliminado documentos que contribuíam para as suas estatísticas, que foram fundidos a partir de outras réplicas. Tipicamente, as diferenças nas estatísticas por réplica são mais percetíveis em índices menores. |
| Pontuações idênticas | Se vários documentos tiverem a mesma pontuação, qualquer um deles pode aparecer primeiro.  |

### <a name="how-to-get-consistent-ordering"></a>Como obter encomendas consistentes

Se uma encomenda consistente for um requisito de aplicação, pode definir explicitamente uma **`$orderby`** expressão [consulta-odata-filter-orderby-syntax.md) num campo. Apenas os campos que estão indexados como **`sortable`** podem ser usados para encomendar resultados. Os campos geralmente utilizados num **`$orderby`** campo de classificação, data e localização incluem se especificar o valor do parâmetro para incluir nomes de **`orderby`** campo e chamadas para a [**`geo.distance()` função**](query-odata-filter-orderby-syntax.md) para valores geoespacial.

Outra abordagem que promove a consistência é a utilização de um [perfil de pontuação personalizado.](index-add-scoring-profiles.md) Os perfis de pontuação dão-lhe mais controlo sobre o ranking de itens nos resultados de pesquisa, com a capacidade de aumentar os jogos encontrados em campos específicos. A lógica de pontuação adicional pode ajudar a ultrapassar pequenas diferenças entre réplicas porque as pontuações de pesquisa de cada documento estão mais distantes. Recomendamos o [algoritmo de classificação](index-ranking-similarity.md) para esta abordagem.

## <a name="hit-highlighting"></a>Detetor de ocorrências

O destaque do hit refere-se à formatação de texto (como destaques arrojados ou amarelos) aplicados aos termos correspondentes num resultado, facilitando a visão da partida. As instruções de realce são fornecidas no [pedido de consulta](/rest/api/searchservice/search-documents). 

Para ativar o destaque do impacto, adicione `highlight=[comma-delimited list of string fields]` para especificar quais os campos que utilizarão o realce. O destaque é útil para campos de conteúdo mais longos, como um campo de descrição, onde a partida não é imediatamente óbvia. Apenas as definições de campo que são atribuídas como **pesmáveis** qualificam-se para o destaque de sucesso.

Por padrão, a Azure Cognitive Search retorna até cinco destaques por campo. Pode ajustar este número apingendo para o campo um traço seguido de um inteiro. Por exemplo, `highlight=Description-10` retorna até 10 destaques sobre o conteúdo correspondente no campo Descrição.

A formatação é aplicada a consultas de termo inteiro. O tipo de formatação é determinado por etiquetas `highlightPreTag` `highlightPostTag` e, e o seu código trata da resposta (por exemplo, aplicando um tipo de letra arrojado ou um fundo amarelo).

No exemplo seguinte, os termos "areia", "areia", "praias", "praia" encontrados no campo Descrição estão marcados para destaque. As consultas que desencadeiam a expansão da consulta no motor, como a pesquisa fuzzy e wildcard, têm suporte limitado para o destaque do sucesso.

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2020-06-30 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

### <a name="new-behavior-starting-july-15"></a>Novo comportamento (a partir de 15 de julho)

Os serviços criados após 15 de julho de 2020 proporcionarão uma experiência de destaque diferente. Os serviços criados antes dessa data não mudarão no seu comportamento de destaque. 

Com o novo comportamento:

* Apenas serão devolvidas frases que correspondam à consulta completa da frase. A consulta "super bowl" devolverá destaques como este:

    ```html
    '<em>super bowl</em> is super awesome with a bowl of chips'
    ```
  Note que o termo *tigela de batatas fritas* não tem qualquer destaque porque não corresponde à frase completa.

Quando estiver a escrever o código do cliente que implementa o destaque, esteja ciente desta mudança. Note que isso não irá impactá-lo a menos que crie um serviço de pesquisa completamente novo.

## <a name="next-steps"></a>Passos seguintes

Para gerar rapidamente uma página de pesquisa para o seu cliente, considere estas opções:

+ [O Gerador de Aplicações,](search-create-app-portal.md)no portal, cria uma página HTML com uma barra de pesquisa, navegação com face e área de resultados que inclui imagens.
+ [Crie a sua primeira aplicação em C#](tutorial-csharp-create-first-app.md) é um tutorial que constrói um cliente funcional. O código de amostra demonstra consultas paginadas, o destaque e a triagem.

Várias amostras de código incluem uma interface frontal web, que pode encontrar aqui: [app de demonstração de New York City Jobs,](https://aka.ms/azjobsdemo) [código de amostra JavaScript com um site de demonstração ao vivo](https://github.com/liamca/azure-search-javascript-samples), e [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).