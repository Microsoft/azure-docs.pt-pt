---
title: Estruturar uma resposta semântica
titleSuffix: Azure Cognitive Search
description: Descreve o algoritmo de classificação semântica na Pesquisa Cognitiva e como estruturar as "respostas semânticas" e as "legendas semânticas" de um conjunto de resultados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: febbfd0f3def8e681107ef78d9478463b1c2a872
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680404"
---
# <a name="semantic-ranking-and-responses-in-azure-cognitive-search"></a>Ranking semântico e respostas em Azure Cognitive Search

> [!IMPORTANT]
> O algoritmo de classificação semântica e as respostas/legendas semânticas estão em visualização pública, disponíveis apenas através da pré-visualização REST API. As funcionalidades de pré-visualização são oferecidas como está, nos [Termos Complementares de Utilização.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

O ranking semântico melhora a precisão dos resultados de pesquisa, ressocando os melhores jogos usando um modelo de classificação semântica treinado para consultas expressas em linguagem natural em oposição às palavras-chave.

Este artigo descreve o algoritmo semântico e como uma resposta semântica é moldada. Uma resposta inclui legendas, tanto em texto simples como com destaques, e respostas (opcional).

+ As legendas semânticas são passagens de texto relevantes para a consulta extraída dos resultados da pesquisa. Podem ajudar a resumir um resultado quando os campos de conteúdo individuais são demasiado grandes para a página de resultados. As legendas apresentam destaques semânticos, permitindo aos utilizadores desviar rapidamente os resultados da consulta para encontrar os documentos mais relevantes, melhorando assim a experiência geral do utilizador.

+ As respostas semânticas usam modelos de aprendizagem automática de Bing para formular respostas a consultas que parecem perguntas. As respostas são selecionadas a partir de uma lista de passagens mais relevantes para a consulta, como extraídos dos documentos de topo no conjunto de resultados de consulta. As respostas são devolvidas como um objeto independente e de alto nível na carga útil de resposta de consulta que pode optar por render nas páginas de pesquisa, juntamente com os resultados da pesquisa.

## <a name="prerequisites"></a>Pré-requisitos

+ Consultas formuladas utilizando o tipo de consulta semântica. Para obter mais informações, consulte [Criar uma consulta semântica.](semantic-how-to-query-request.md)

## <a name="understanding-a-semantic-response"></a>Compreender uma resposta semântica

Uma resposta semântica inclui novas propriedades para pontuações, legendas e respostas. Uma resposta semântica é construída a partir da resposta padrão, utilizando os 50 melhores resultados devolvidos pelo [motor de pesquisa de texto completo](search-lucene-query-architecture.md), que são então re-classificados usando o ranker semântico. Se forem especificados mais de 50, os resultados adicionais são devolvidos, mas não serão semi-classificados.

Como em todas as consultas, uma resposta é composta por todos os campos marcados como recuperáveis, ou apenas os campos listados na declaração selecionada. Também inclui um campo de "resposta" e "legendas".

+ Para cada resultado semântico, por padrão, existe uma "resposta", devolvida como um campo distinto que pode optar por renderizar numa página de pesquisa. Pode especificar até cinco. A formulação da resposta é automatizada: ler todos os documentos nos resultados iniciais, executar resumos extrativos, seguido de compreensão de leitura de máquinas e, finalmente, promover uma resposta direta à pergunta do utilizador no campo de resposta.

+ Uma "legenda" é uma resumo baseada na extração do conteúdo do documento, devolvido em texto simples ou com destaques. As legendas são incluídas automaticamente e não podem ser suprimidas. Os destaques são aplicados utilizando a compreensão da leitura da máquina para identificar quais as cordas que devem ser enfatizadas. Os destaques atraem a sua atenção para as passagens mais relevantes, para que possa digitalizar rapidamente uma página de resultados para encontrar o documento certo.

Um resultado semânticamente reedição de resultados definidos por @search.rerankerScore valor. Se adicionar uma cláusula OrderBy, um erro HTTP 400 será devolvido porque essa cláusula não é suportada numa consulta semântica.

## <a name="example"></a>Exemplo

O @search.rerankerScore existe ao lado do padrão e é usado para @search.score ree classificar os resultados.

Dada a seguinte consulta:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "answers": "none",
    "searchFields": "HotelName,Category,Description",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

Pode esperar ver o seguinte resultado, representativo de uma resposta semântica. Estes resultados mostram apenas as três melhores respostas, classificadas por " @search.rerankerScore ". Repare como o Oceanside Resort está agora em primeiro lugar. Sob o ranking padrão de " @search.score ", este resultado seria devolvido em segundo lugar, depois de Trails End.

```http
{
    "@odata.count": 31,
    "@search.answers": [],
    "value": [
        {
            "@search.score": 1.8920634,
            "@search.rerankerScore": 1.1091284966096282,
            "@search.captions": [
                {
                    "text": "Oceanside Resort. Budget. New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
                    "highlights": "<em>Oceanside Resort.</em> Budget. New Luxury Hotel.  Be the first to stay.<em> Bay views</em> from every room, location near the piper, rooftop pool, waterfront dining & more."
                }
            ],
            "HotelId": "18",
            "HotelName": "Oceanside Resort",
            "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
            "Category": "Budget"
        },
        {
            "@search.score": 2.5204072,
            "@search.rerankerScore": 1.0731962407007813,
            "@search.captions": [
                {
                    "text": "Trails End Motel. Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
                    "highlights": "<em>Trails End Motel.</em> Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
                }
            ],
            "HotelId": "40",
            "HotelName": "Trails End Motel",
            "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
            "Category": "Luxury"
        },
        {
            "@search.score": 1.4104348,
            "@search.rerankerScore": 1.06992666143924,
            "@search.captions": [
                {
                    "text": "Winter Panorama Resort. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
                    "highlights": "<em>Winter Panorama Resort</em>. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs."
                }
            ],
            "HotelId": "12",
            "HotelName": "Winter Panorama Resort",
            "Description": "Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
            "Category": "Resort and Spa"
        }
```

## <a name="next-steps"></a>Passos seguintes

+ [Visão geral da pesquisa semântica](semantic-search-overview.md)
+ [Algoritmo de semelhança](index-ranking-similarity.md)
+ [Criar uma consulta semântica](semantic-how-to-query-request.md)
+ [Criar uma consulta básica](search-query-create.md)