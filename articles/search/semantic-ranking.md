---
title: Ranking semântico
titleSuffix: Azure Cognitive Search
description: Descreve o algoritmo de classificação semântica na Pesquisa Cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 01c4d6475ec23b8a55d91e18f49cab27760aa907
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604292"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Ranking semântico em Pesquisa Cognitiva Azure

> [!IMPORTANT]
> As funcionalidades de pesquisa semântica estão em pré-visualização pública, disponíveis apenas através da pré-visualização REST API. As funcionalidades de pré-visualização são oferecidas como está, nos [Termos Complementares de Utilização,](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)e não estão garantidas a mesma implementação em disponibilidade geral. Para mais informações, consulte [Disponibilidade e preços.](semantic-search-overview.md#availability-and-pricing)

O ranking semântico é uma extensão do pipeline de execução de consultas que melhora a precisão e a recuperação, relembr o topo de um conjunto de resultados iniciais. O ranking semântico é apoiado por modelos de compreensão de leitura de máquinas profundas de última geração, treinados para consultas expressas em linguagem natural em oposição à correspondência linguística em palavras-chave. Em contraste com o [algoritmo de classificação de semelhança padrão,](index-ranking-similarity.md)o ranker semântico usa o contexto e significado das palavras para determinar a relevância.

## <a name="how-semantic-ranking-works"></a>Como funciona o ranking semântico

O ranking semântico é tanto de recursos como de tempo intensivo. Para completar o processamento dentro da latência esperada de uma operação de consulta, o modelo toma como entrada apenas os 50 documentos mais altos devolvidos do algoritmo de [classificação](index-ranking-similarity.md)de semelhança padrão . Os resultados do ranking inicial podem incluir mais de 50 partidas, mas apenas os primeiros 50 serão reclassily. 

Para o ranking semântico, o modelo utiliza a compreensão da leitura da máquina e a aprendizagem de transferência para re-pontuar os documentos com base no quão bem cada um corresponde à intenção da consulta.

### <a name="preparation-passage-extraction-phase"></a>Fase de preparação (extração de passagem)

Para cada documento nos resultados iniciais, há um exercício de extração de passagem que identifica passagens-chave. Este é um exercício de redução de tamanho que reduz o conteúdo a uma quantidade que pode ser processada rapidamente.

1. Para cada um dos 50 documentos, cada campo no parâmetro searchFields é avaliado por ordem consecutiva. Os conteúdos de cada campo são consolidados numa longa cadeia. 

1. A corda comprida é então aparada para garantir que o comprimento total não seja superior a 8.000 fichas. Por esta razão, recomenda-se que posicione os campos concisos primeiro para que sejam incluídos na cadeia. Se tiver documentos muito grandes com campos pesados de texto, qualquer coisa depois do limite simbólico é ignorado.

1. Cada documento é agora representado por uma única longa corda que é até 8.000 fichas. Estas cordas são enviadas para o modelo de resumo, o que reduzirá ainda mais a corda. O modelo de resumo avalia a longa cadeia para frases-chave ou passagens que melhor resumem o documento ou respondem à pergunta.

1. A saída desta fase é uma legenda (e opcionalmente, uma resposta). A legenda é, no máximo, 128 fichas por documento, e é considerada a mais representativa do documento.

### <a name="scoring-and-ranking-phases"></a>Fases de pontuação e classificação

Nesta fase, todas as 50 legendas são avaliadas para avaliar a relevância.

1. A pontuação é determinada avaliando cada legenda para relevância conceptual e semântica, em relação à consulta fornecida.

   O diagrama seguinte fornece uma ilustração do que significa "relevância semântica". Considere o termo "capital", que poderia ser usado no contexto financeiro, direito, geografia ou gramática. Se uma consulta inclui termos do mesmo espaço vetorial (por exemplo, "capital" e "investimento"), um documento que também inclui tokens no mesmo cluster irá pontuar mais do que um que não.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Representação vetorial para o contexto" border="true":::

1. A saída desta fase é atribuída @search.rerankerScore a cada documento. Uma vez que todos os documentos são marcados, eles são listados em ordem descendente e incluídos na carga útil de resposta de consulta.

## <a name="next-steps"></a>Passos seguintes

O ranking semântico é oferecido nos níveis Standard, em regiões específicas. Para mais informações e para se inscrever, consulte [Disponibilidade e preços.](semantic-search-overview.md#availability-and-pricing) Um novo tipo de consulta permite o ranking de relevância e as estruturas de resposta da pesquisa semântica. Para começar, [crie uma consulta semântica.](semantic-how-to-query-request.md)

Em alternativa, reveja qualquer um dos seguintes artigos para obter informações relacionadas.

+ [Visão geral da pesquisa semântica](semantic-search-overview.md)
+ [Devolva uma resposta semântica](semantic-answers.md)