---
title: Semelhança e visão geral de pontuação
titleSuffix: Azure Cognitive Search
description: Explica os conceitos de semelhança e pontuação, e o que um desenvolvedor pode fazer para personalizar o resultado da pontuação.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 4b02039c86f43e6bebed58dfff475816f09a3da1
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890149"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Semelhança e pontuação na Pesquisa Cognitiva Azure

A pontuação refere-se à computação de uma pontuação de pesquisa para cada item devolvido em resultados de pesquisa para consultas completas de pesquisa de texto. A classificação é um indicador da relevância do item no contexto da operação de pesquisa atual. Quanto maior a classificação, mais relevante será o item. Nos resultados da pesquisa, os itens são ordenados de alto a baixo, com base nas pontuações de pesquisa calculadas para cada item. 

Por padrão, os 50 melhores são devolvidos na resposta, mas pode usar o parâmetro **$top** para devolver um número menor ou maior de itens (até 1000 numa única resposta), e **$skip** obter o próximo conjunto de resultados.

A pontuação de pesquisa é calculada com base em propriedades estatísticas dos dados e na consulta. A Azure Cognitive Search encontra documentos que correspondem em termos de pesquisa (alguns ou todos, dependendo do Modo de [Pesquisa),](https://docs.microsoft.com/rest/api/searchservice/search-documents#searchmodeany--all-optional)favorecendo documentos que contenham muitas instâncias do termo de pesquisa. A pontuação de pesquisa sobe ainda mais se o termo for raro em todo o índice de dados, mas comum dentro do documento. A base para esta abordagem à relevância da computação é conhecida como *TF-IDF ou* frequência de documentos inverso de frequência a termo.

Os valores de pontuação de pesquisa podem ser repetidos ao longo de um conjunto de resultados. Quando vários hits têm a mesma pontuação de pesquisa, a encomenda dos mesmos itens pontuados não é definida, e não é estável. Faça a consulta novamente e poderá ver a posição de mudança de itens, especialmente se estiver a utilizar o serviço gratuito ou um serviço de faturação com múltiplas réplicas. Tendo em conta dois itens com uma pontuação idêntica, não há garantia de qual aparece primeiro.

Se quiser quebrar o empate entre as pontuações repetidas, pode adicionar uma **cláusula $orderby** à primeira `$orderby=search.score() desc,Rating desc`ordem por pontuação, em seguida, encomende por outro campo ordenado (por exemplo, ). Para mais informações, consulte [$orderby](https://docs.microsoft.com/azure/search/search-query-odata-orderby).

> [!NOTE]
> A `@search.score = 1.00` indica um conjunto de resultados não marcados ou não classificados. A pontuação é uniforme em todos os resultados. Os resultados não pontuados ocorrem quando o formulário de consulta é pesquisa difusa, perguntas wildcard ou regex, ou uma expressão **$filter.** 

## <a name="scoring-profiles"></a>Perfis de classificação

Você pode personalizar a forma como diferentes campos são classificados definindo um perfil de *pontuação*personalizado . Os perfis de pontuação dão-lhe um maior controlo sobre o ranking dos itens nos resultados da pesquisa. Por exemplo, é possível que queira impulsionar itens com base no seu potencial de receitas, promover itens mais recentes ou talvez impulsionar itens que estão há demasiado tempo no inventário. 

Um perfil de pontuação faz parte da definição de índice, composta por campos ponderados, funções e parâmetros. Para obter mais informações sobre a definição de um, consulte [Perfis de Pontuação](index-add-scoring-profiles.md).

## <a name="scoring-statistics"></a>Estatísticas de pontuação

Para a escalabilidade, a Pesquisa Cognitiva Azure distribui cada índice horizontalmente através de um processo de sharding, o que significa que partes de um índice são fisicamente separadas.

Por padrão, a pontuação de um documento é calculada com base nas propriedades estatísticas dos dados *dentro de um fragmento*. Esta abordagem geralmente não é um problema para um grande corpus de dados, e fornece um melhor desempenho do que ter que calcular a pontuação com base em informações em todos os fragmentos. Dito isto, usar esta otimização de desempenho poderia fazer com que dois documentos muito semelhantes (ou mesmo documentos idênticos) acabassem com diferentes pontuações de relevância se acabassem em fragmentos diferentes.

Se preferir calcular a pontuação com base nas propriedades estatísticas em todos os fragmentos, pode fazê-lo *adicionando pontuaçãoStatistics=global* como um parâmetro de [consulta](https://docs.microsoft.com/rest/api/searchservice/search-documents) (ou adicionar "estatísticas de *pontuação": "global"* como parâmetro corporal do pedido de [consulta).](https://docs.microsoft.com/rest/api/searchservice/search-documents)

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?scoringStatistics=global
  Content-Type: application/json
  api-key: [admin key]  
```

> [!NOTE]
> É necessária uma chave de api-key para o `scoringStatistics` parâmetro.

## <a name="similarity-ranking-algorithms"></a>Algoritmos de classificação de semelhança

A Azure Cognitive Search suporta dois algoritmos de classificação de semelhançadiferentes diferentes: um algoritmo *de semelhança clássico* e a implementação oficial do algoritmo *Okapi BM25* (atualmente em pré-visualização). O algoritmo de semelhança clássica é o algoritmo padrão, mas a partir de 15 de julho, quaisquer novos serviços criados após essa data usam o novo algoritmo BM25. Será o único algoritmo disponível em novos serviços.

Por enquanto, pode especificar qual o algoritmo de classificação de semelhança que gostaria de usar. Para mais informações, consulte [algoritmo de ranking.](index-ranking-similarity.md)

O segmento de vídeo seguinte avança para uma explicação dos algoritmos de classificação usados na Pesquisa Cognitiva Azure. Pode ver o vídeo completo para mais informações.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=322&end=643]

## <a name="see-also"></a>Consulte também

 [Pontuação perfis](index-add-scoring-profiles.md) [REST API Referência](https://docs.microsoft.com/rest/api/searchservice/)   
 [Documentos de pesquisa API](https://docs.microsoft.com/rest/api/searchservice/search-documents)   
 [Pesquisa Cognitiva Azure .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
