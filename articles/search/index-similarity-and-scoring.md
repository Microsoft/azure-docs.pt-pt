---
title: Semelhança e pontuação geral
titleSuffix: Azure Cognitive Search
description: Explica os conceitos de semelhança e pontuação, e o que um desenvolvedor pode fazer para personalizar o resultado da pontuação.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 72243f896b2cf7dbab61a42514bee634da28d4c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676332"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Semelhança e pontuação na Pesquisa Cognitiva Azure

Este artigo descreve os dois algoritmos de classificação de semelhança na Pesquisa Cognitiva Azure. Introduz também duas características *relacionadas: perfis de pontuação (critérios* para ajustar uma pontuação de pesquisa) e o parâmetro *de maçamento das funcionalidades* (desembala uma pontuação de pesquisa para mostrar mais detalhes). 

Um terceiro algoritmo semântico de re-ranking está atualmente em pré-visualização pública. Para mais informações, comece com [a visão geral da pesquisa semântica.](semantic-search-overview.md)

## <a name="similarity-ranking-algorithms"></a>Algoritmos de classificação de semelhança

A Azure Cognitive Search suporta dois algoritmos de classificação de semelhança.

| Algoritmo | Resultado | Disponibilidade |
|-----------|-------|--------------|
| ClassicSimilarity | @search.score | Usado por todos os serviços de pesquisa até 15 de julho de 2020. |
| BM25Similaridade | @search.score | Utilizado por todos os serviços de pesquisa criados após 15 de julho. Os serviços mais antigos que utilizam o clássico por padrão podem [optar por BM25](index-ranking-similarity.md). |

Tanto clássicos como BM25 são funções de recuperação semelhantes a TF-IDF que usam o termo frequência (TF) e a frequência de documento inverso (IDF) como variáveis para calcular pontuações de relevância para cada par de consultas de documentos, que é então usado para classificação Enquanto conceptualmente semelhante ao clássico, BM25 toma a sua raiz na recuperação de informação probabilística para melhorar sobre ele. O BM25 também oferece opções avançadas de personalização, tais como permitir que o utilizador decida como a pontuação de relevância escala com o termo frequência de termos combinados.

O segmento de vídeo que se segue avança para uma explicação dos algoritmos de classificação geralmente disponíveis usados na Pesquisa Cognitiva de Azure. Pode ver o vídeo completo para mais informações.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=322&end=643]

## <a name="relevance-scoring"></a>Classificação de relevância

Pontuação refere-se ao cálculo de uma pontuação de pesquisa de cada item devolvido em resultados de pesquisa para consultas completas de pesquisa de texto. A pontuação é um indicador da relevância de um item no contexto da consulta atual. Quanto maior a classificação, mais relevante será o item. Nos resultados da pesquisa, os itens são classificados de alto a baixo, com base nas pontuações de pesquisa calculadas para cada item. A partitura é devolvida na resposta como @search.score " " em cada documento.

Por padrão, os 50 melhores são devolvidos na resposta, mas pode usar o parâmetro **$top** para devolver um número menor ou maior de itens (até 1000 numa única resposta) e **$skip** para obter o próximo conjunto de resultados.

A pontuação de pesquisa é calculada com base nas propriedades estatísticas dos dados e na consulta. A Azure Cognitive Search encontra documentos que correspondem em termos de pesquisa (alguns ou todos, dependendo [do searchMode),](/rest/api/searchservice/search-documents#query-parameters)favorecendo documentos que contêm muitos casos do termo de pesquisa. A pontuação de pesquisa sobe ainda mais se o termo for raro em todo o índice de dados, mas comum dentro do documento. A base para esta abordagem da relevância da computação é conhecida como *TF-IDF ou* frequência de documento inverso de frequência a prazo.

Os valores de pontuação de pesquisa podem ser repetidos ao longo de um conjunto de resultados. Quando vários hits têm a mesma pontuação de pesquisa, a encomenda dos mesmos itens pontuados não é definida, e não é estável. Volte a executar a consulta e poderá ver itens a mudar de posição, especialmente se estiver a utilizar o serviço gratuito ou um serviço de faturação com várias réplicas. Tendo em conta dois itens com uma pontuação idêntica, não há garantias de qual deles aparece primeiro.

Se quiser quebrar o empate entre as pontuações repetidas, pode adicionar uma cláusula **de $orderby** à primeira ordem por pontuação e, em seguida, encomendar por outro campo de classificação (por exemplo, `$orderby=search.score() desc,Rating desc` ). Para mais informações, consulte [$orderby.](search-query-odata-orderby.md)

> [!NOTE]
> A `@search.score = 1.00` indica um conjunto de resultados não marcado ou não classificado. A pontuação é uniforme em todos os resultados. Os resultados não pontuados ocorrem quando o formulário de consulta é pesquisa duvidosa, consultas wildcard ou regex, ou uma expressão **$filter.**

<a name="scoring-statistics"></a>

## <a name="scoring-statistics-and-sticky-sessions"></a>Estatísticas de pontuação e sessões pegajosas

Para a escalabilidade, a Azure Cognitive Search distribui cada índice horizontalmente através de um processo de fragmento, o que significa que [porções de um índice são fisicamente separadas](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards).

Por predefinição, a pontuação de um documento é calculada com base nas propriedades estatísticas dos dados *dentro de um fragmento*. Esta abordagem geralmente não é um problema para um grande corpus de dados, e proporciona um melhor desempenho do que ter que calcular a pontuação com base em informações em todos os fragmentos. Dito isto, usar esta otimização de desempenho poderia fazer com que dois documentos muito semelhantes (ou mesmo documentos idênticos) acabassem com pontuações de relevância diferentes se acabassem em fragmentos diferentes.

Se preferir calcular a pontuação com base nas propriedades estatísticas em todos os fragmentos, pode fazê-lo *adicionando scoringStatistics=global* como parâmetro [de consulta](/rest/api/searchservice/search-documents) (ou adicionar *"scoreingStatistics": "global"* como parâmetro corporal do pedido de [consulta).](/rest/api/searchservice/search-documents)

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?scoringStatistics=global&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```

Utilizando a pontuaçãoStatistics garantirá que todos os fragmentos na mesma réplica fornecem os mesmos resultados. Dito isto, as réplicas diferentes podem ser ligeiramente diferentes umas das outras, uma vez que estão sempre a ser atualizadas com as últimas alterações ao seu índice. Em alguns cenários, pode querer que os seus utilizadores obtenham resultados mais consistentes durante uma "sessão de consulta". Nesses cenários, pode fornecer `sessionId` como parte das suas consultas. É `sessionId` uma corda única que cria para se referir a uma sessão de utilizador única.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?sessionId=[string]&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```

Enquanto o mesmo `sessionId` for usado, será feita uma tentativa de melhor esforço para direcionar a mesma réplica, aumentando a consistência dos resultados que os seus utilizadores irão ver. 

> [!NOTE]
> Reutilizar repetidamente os `sessionId` mesmos valores pode interferir com o equilíbrio de carga dos pedidos através de réplicas e afetar negativamente o desempenho do serviço de pesquisa. O valor utilizado como sessãoId não pode começar com um caráter '_'.

## <a name="scoring-profiles"></a>Perfis de classificação

Pode personalizar a forma como diferentes campos são classificados definindo um *perfil de pontuação.* Os perfis de pontuação dão-lhe um maior controlo sobre o ranking de itens nos resultados de pesquisa. Por exemplo, pode querer aumentar itens com base no seu potencial de receita, promover itens mais recentes ou talvez aumentar itens que estão em inventário há demasiado tempo. 

Um perfil de pontuação faz parte da definição de índice, composto por campos ponderados, funções e parâmetros. Para obter mais informações sobre a definição de um, consulte [Perfis de Pontuação](index-add-scoring-profiles.md).

<a name="featuresMode-param"></a>

## <a name="featuresmode-parameter-preview"></a>featuresMode parâmetro (pré-visualização)

Os pedidos [de Documentos de Pesquisa](/rest/api/searchservice/preview-api/search-documents) têm um novo parâmetro de utilização de [funcionalidades](/rest/api/searchservice/preview-api/search-documents#featuresmode) que pode fornecer detalhes adicionais sobre a relevância a nível de campo. Considerando que o `@searchScore` documento é calculado para o all-up do documento (quão relevante é este documento no contexto desta consulta), através de funcionalidadesMode pode obter informações sobre campos individuais, conforme expresso numa `@search.features` estrutura. A estrutura contém todos os campos utilizados na consulta (ou campos específicos através **de searchFields** numa consulta, ou todos os campos atribuídos como **pesmáveis** num índice). Para cada campo, obtém-se os seguintes valores:

+ Número de fichas únicas encontradas no campo
+ Pontuação de semelhança, ou uma medida de quão semelhante é o conteúdo do campo, em relação ao termo de consulta
+ frequência de prazo, ou o número de vezes que o termo de consulta foi encontrado no campo

Para uma consulta que vise os campos de "descrição" e "título", uma resposta que inclua `@search.features` pode ser assim:

```json
"value": [
 {
    "@search.score": 5.1958685,
    "@search.features": {
        "description": {
            "uniqueTokenMatches": 1.0,
            "similarityScore": 0.29541412,
            "termFrequency" : 2
        },
        "title": {
            "uniqueTokenMatches": 3.0,
            "similarityScore": 1.75451557,
            "termFrequency" : 6
        }
```

Pode consumir estes pontos de dados em [soluções de pontuação personalizadas](https://github.com/Azure-Samples/search-ranking-tutorial) ou utilizar a informação para depurar problemas de relevância de pesquisa.

## <a name="see-also"></a>Ver também

+ [Perfis de pontuação](index-add-scoring-profiles.md)
+ [Referência API DE REPOUSO](/rest/api/searchservice/)
+ [Documentos de Pesquisa API](/rest/api/searchservice/search-documents)
+ [Pesquisa Cognitiva Azure .NET SDK](/dotnet/api/overview/azure/search)