---
title: Como trabalhar com resultados de pesquisa
titleSuffix: Azure Cognitive Search
description: Estrutura e classifica os resultados da pesquisa, obtenha uma contagem de documentos e adicione navegação de conteúdo aos resultados de pesquisa em Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 0f815003449f0600bce1cb8927b92b85b51b09a1
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641609"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Como trabalhar com os resultados da pesquisa em Pesquisa Cognitiva Azure

Este artigo explica como obter uma resposta de consulta que recue com uma contagem total de documentos correspondentes, resultados paginados, resultados classificados e termos de sucesso.

A estrutura de uma resposta é determinada por parâmetros na consulta: Documento de [Pesquisa](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) na API REST, ou [Classe DocumentSearchResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) no .NET SDK.

## <a name="result-composition"></a>Composição do resultado

Embora um documento de pesquisa possa consistir num grande número de campos, normalmente apenas alguns são necessários para representar cada documento no conjunto de resultados. Num pedido de consulta, `$select=<field list>` anexar para especificar quais os campos que aparecem na resposta. Um campo deve ser atribuído como **Recuperável** no índice a incluir num resultado. 

Os campos que funcionam melhor incluem aqueles que contrastam e diferenciam entre documentos, fornecendo informações suficientes para convidar uma resposta de clique por parte do utilizador. Num site de e-commerce, pode ser um nome de produto, descrição, marca, cor, tamanho, preço e classificação. Para a amostra incorporada do índice hotéis-sample-sample, pode ser um campo no seguinte exemplo:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> Se quiser incluir ficheiros de imagem num resultado, como uma foto ou logotipo do produto, guarde-os fora da Pesquisa Cognitiva Azure, mas inclua um campo no seu índice para fazer referência ao URL de imagem no documento de pesquisa. Os índices de amostra que suportam imagens nos resultados incluem a demonstração de **realestate-sample-us,** apresentada neste [quickstart](search-create-app-portal.md), e a app de [demonstração new york city Jobs](https://aka.ms/azjobsdemo).

## <a name="paging-results"></a>Resultados de paginação

Por predefinição, o motor de busca retorna até aos primeiros 50 jogos, conforme determinado pela pontuação de pesquisa se a consulta for pesquisa completa de texto, ou numa ordem arbitrária para consultas exatas de correspondência.

Para devolver um número diferente `$top` de `$skip` documentos correspondentes, adicione e parâmetros ao pedido de consulta. A lista que se segue explica a lógica.

+ Adicione `$count=true` para obter uma contagem do número total de documentos correspondentes dentro de um índice.

+ Devolva o primeiro conjunto de 15 documentos correspondentes mais uma contagem de correspondências totais:`GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ Volte o segundo set, saltando os primeiros `$top=15&$skip=15`15 para obter os próximos 15: . Faça o mesmo no terceiro conjunto de 15:`$top=15&$skip=30`

Os resultados das consultas paginadas não são garantidos para serem estáveis se o índice subjacente estiver a mudar. A paging altera `$skip` o valor de cada página, mas cada consulta é independente e opera na visão atual dos dados tal como existem no índice em tempo de consulta (por outras palavras, não há cache ou instantâneo de resultados, como os encontrados numa base de dados geral).
 
Segue-se um exemplo de como se pode obter duplicados. Assuma um índice com quatro documentos:

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
    { "id": "4", "rating": 1 }
 
Agora assuma que quer resultados devolvidos dois de cada vez, ordenados pela classificação. Executaria esta consulta para obter a `$top=2&$skip=0&$orderby=rating desc`primeira página dos resultados: , produzindo os seguintes resultados:

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
 
No serviço, assuma que um quinto documento é `{ "id": "5", "rating": 4 }`adicionado ao índice entre chamadas de consulta: .  Pouco depois, executa uma consulta para ir `$top=2&$skip=2&$orderby=rating desc`buscar a segunda página: e obtém estes resultados:

    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
 
Note que o documento 2 é recolhido duas vezes. Isto porque o novo documento 5 tem um valor maior para a classificação, por isso classifica antes do documento 2 e aterra na primeira página. Embora este comportamento possa ser inesperado, é típico de como um motor de busca se comporta.

## <a name="ordering-results"></a>Ordenar resultados

Para consultas completas de pesquisa de texto, os resultados são automaticamente classificados por uma pontuação de pesquisa, calculada com base na frequência de prazo e proximidade de um documento, com pontuações mais altas indo para documentos com mais ou mais fósforos em um termo de pesquisa. 

As pontuações de pesquisa transmitem sentido geral de relevância, refletindo a força do jogo em comparação com outros documentos no mesmo conjunto de resultados. As pontuações nem sempre são consistentes de uma consulta para a outra, por isso, como você trabalha com consultas, você pode notar pequenas discrepâncias na forma como os documentos de pesquisa são encomendados. Há várias explicações para o porquê disto poder ocorrer.

| Causa | Descrição |
|-----------|-------------|
| Volatilidade dos dados | O conteúdo do índice varia à medida que adiciona, modifica ou elimina documentos. As frequências de prazo mudarão à medida que as atualizações de índices são processadas ao longo do tempo, afetando as pontuações de pesquisa de documentos correspondentes. |
| Múltiplas réplicas | Para serviços que utilizam múltiplas réplicas, são emitidas consultas contra cada réplica em paralelo. As estatísticas de índice utilizadas para calcular uma pontuação de pesquisa são calculadas numa base por réplica, com resultados fundidos e encomendados na resposta à consulta. As réplicas são sobretudo espelhos uns dos outros, mas as estatísticas podem diferir devido a pequenas diferenças de estado. Por exemplo, uma réplica poderia ter apagado documentos que contribuem para as suas estatísticas, que foram fundidos a partir de outras réplicas. Tipicamente, as diferenças nas estatísticas por réplica são mais percetíveis em índices mais pequenos. |
| Pontuações idênticas | Se vários documentos tiverem a mesma pontuação, qualquer um deles pode aparecer primeiro.  |

### <a name="consistent-ordering"></a>Ordenação consistente

Dada a flexão na ordem dos resultados, é melhor explorar outras opções se a consistência for um requisito de aplicação. A abordagem mais fácil é classificar por um valor de campo, como classificação ou data. Para cenários em que se queira classificar por um campo específico, como [ `$orderby` ](query-odata-filter-orderby-syntax.md)uma classificação ou data, pode definir explicitamente uma expressão , que pode ser aplicada a qualquer campo indexado como **Sortível**.

Outra opção é usar um perfil de [pontuação personalizado.](index-add-scoring-profiles.md) Os perfis de pontuação dão-lhe mais controlo sobre o ranking de itens nos resultados da pesquisa, com a capacidade de aumentar os jogos encontrados em campos específicos. A lógica adicional de pontuação pode ajudar a superar pequenas diferenças entre réplicas porque as pontuações de pesquisa de cada documento estão mais afastadas. Recomendamos o algoritmo de [classificação](index-ranking-similarity.md) para esta abordagem.

## <a name="hit-highlighting"></a>Detetor de ocorrências

O destaque do hit refere-se à formatação de texto (como destaques arrojados ou amarelos) aplicados a um termo correspondente num resultado, facilitando a identificação da partida. As instruções de destaque do hit são fornecidas no pedido de [consulta](https://docs.microsoft.com/rest/api/searchservice/search-documents). O motor de busca encerra o termo `highlightPreTag` `highlightPostTag`correspondente em etiquetas e, e o seu código trata da resposta (por exemplo, aplicando uma fonte arrojada).

A formatação é aplicada a consultas de termo inteiros. No exemplo seguinte, os termos "areia", "areia", "praias", "praia" encontradas dentro do campo Descrição estão marcados para destaque. As consultas que desencadeiam a expansão da consulta no motor, como a pesquisa fuzzy e wildcard, têm suporte limitado para o destaque do sucesso.

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2019-05-06 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

### <a name="new-behavior-starting-july-15"></a>Novo comportamento (a partir de 15 de julho)

Os serviços criados após 15 de julho de 2020 proporcionarão uma experiência de destaque diferente. Os serviços criados antes dessa data não mudarão no seu comportamento de destaque. 

Com o novo comportamento:

* Só as frases que correspondam à consulta de frasecompleta serão devolvidas. A consulta "super bowl" devolverá destaques como este:

    ```html
    '<em>super bowl</em> is super awesome with a bowl of chips'
    ```
  Note que o termo *tigela de batatas fritas* não tem qualquer destaque porque não corresponde à frase completa.
  
* Será possível especificar o tamanho do fragmento devolvido para o destaque. O tamanho do fragmento é especificado como número de caracteres (máximo é de 1000 caracteres).

Quando estiver a escrever o código do cliente que implementa o destaque, esteja ciente desta mudança. Note que isso não irá impactá-lo a menos que crie um serviço de pesquisa completamente novo.

## <a name="next-steps"></a>Passos seguintes

Para gerar rapidamente uma página de pesquisa para o seu cliente, considere estas opções:

+ [Gerador de Aplicações,](search-create-app-portal.md)no portal, cria uma página HTML com uma barra de pesquisa, navegação faceada e área de resultados que inclui imagens.
+ [Criar a sua primeira aplicação em C#](tutorial-csharp-create-first-app.md) é um tutorial que constrói um cliente funcional. O código da amostra demonstra consultas paginadas, realçação de sucesso e triagem.

Várias amostras de código incluem uma interface frontal web, que você pode encontrar aqui: [new York City Jobs demo app](https://aka.ms/azjobsdemo), Código de amostra [JavaScript com um site de demonstração ao vivo](https://github.com/liamca/azure-search-javascript-samples), e [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).
