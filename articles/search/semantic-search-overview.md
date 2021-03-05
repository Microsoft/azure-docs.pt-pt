---
title: Pesquisa semântica
titleSuffix: Azure Cognitive Search
description: Saiba como a Cognitive Search está a usar modelos de pesquisa semântica de aprendizagem profunda de Bing para tornar os resultados da pesquisa mais intuitivos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: e9cbb7daf61397064bd79f30d851d96fdf63f5a0
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203237"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Pesquisa semântica em Pesquisa Cognitiva de Azure

> [!IMPORTANT]
> As funcionalidades de pesquisa semântica estão em pré-visualização pública, disponíveis apenas através da pré-visualização REST API. As funcionalidades de pré-visualização são oferecidas como está, nos [Termos Complementares de Utilização.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

A busca semântica é uma coleção de funcionalidades relacionadas com consultas que suportam uma experiência de consulta mais natural e de maior qualidade. As funcionalidades incluem reclasse semântica dos resultados da pesquisa, bem como legendas e respostas geração com destaque semântico. Os 50 melhores resultados devolvidos do [motor de pesquisa de texto completo](search-lucene-query-architecture.md) são reclassiados para encontrar os jogos mais relevantes.

A tecnologia subjacente aproveita os investimentos da Bing e da Microsoft Research, e está integrada na infraestrutura de Pesquisa Cognitiva. Para usá-lo, você precisará de pequenas modificações no pedido de pesquisa, mas não é necessária nenhuma configuração extra ou reindexing.

As funcionalidades de pré-visualização do público incluem:

+ Modelo de classificação semântica que obtém resultados com base no contexto ou significado semântico dos termos de consulta de pesquisa
+ Legendas semânticas que destacam passagens relevantes
+ Respostas semânticas à consulta, também formuladas a partir de resultados
+ Verificação ortográfica que corrige tipografias antes que os termos de consulta cheguem ao motor de busca

## <a name="semantic-search-architecture"></a>Arquitetura de pesquisa semântica

Os componentes da procura semântica estão em camadas em cima do oleoduto de execução de consultas existente. A correção ortográfica (não mostrada no diagrama) melhora a recuperação corrigindo os erros em termos de consulta individual. Depois de todas as análises e análises estarem completas, o motor de busca recupera os documentos que combinam com a consulta e pontua-os utilizando o [algoritmo de pontuação padrão](index-similarity-and-scoring.md#similarity-ranking-algorithms), seja O BM25 ou clássico, dependendo de quando o serviço foi criado. Os perfis de pontuação também são aplicados nesta fase. 

Tendo recebido os 50 melhores jogos, o [algoritmo semântico](semantic-how-to-query-response.md) do ranking reavalia o corpus do documento. Os resultados podem incluir mais de 50 partidas, mas apenas os primeiros 50 serão reclassiados. Para o ranking, o algoritmo usa aprendizagem automática e transferência para re-pontuar os documentos com base no quão bem cada um corresponde à intenção da consulta.

Para criar legendas e respostas, utiliza modelos de representação linguística. Dentro de uma legenda, o algoritmo usa um modelo de linguagem desenvolvido por Bing para extrair legendas com destaques que melhor resumem os resultados da consulta. Se a consulta de pesquisa for uma pergunta, e as respostas forem solicitadas, um modelo de linguagem semelhante identifica passagens de texto que melhor respondem à pergunta expressa pela consulta de pesquisa.

:::image type="content" source="media/semantic-search-overview/semantic-query-architecture.png" alt-text="Componentes semânticos em um oleoduto de consulta" border="true":::

## <a name="availability-and-pricing"></a>Disponibilidade e preços

O ranking semântico está disponível através do [registo de inscrição,](https://aka.ms/SemanticSearchPreviewSignup)em serviços de pesquisa criados a nível Standard (S1, S2, S3), localizado numa dessas regiões: North Central US, West US 2, East US 2, North Europe, West Europe. Um serviço de pesquisa existente no S1 ou superior nas regiões indicadas qualifica-se para a pré-visualização (não há necessidade de criar um novo serviço).

A correção ortográfica está disponível nas mesmas regiões, mas não tem restrições de nível nem requisito de inscrição. 

Entre o lançamento de pré-estreia de 2 de março a 1 de abril, a correção ortográfica e o ranking semântico são oferecidos gratuitamente. Depois de 1 de abril, os custos computacionais de execução desta funcionalidade tornar-se-ão um evento fatural. O custo esperado é de CERCA de USD $500/mês para 250.000 consultas. Pode encontrar informações detalhadas sobre os custos documentados na [página de preços de Pesquisa Cognitiva](https://azure.microsoft.com/pricing/details/search/) e em Estimativa e gerir [custos.](search-sku-manage-costs.md)

## <a name="next-steps"></a>Passos seguintes

Um novo tipo de consulta permite o ranking de relevância e as estruturas de resposta da pesquisa semântica. [Crie uma consulta semântica](semantic-how-to-query-request.md) para começar. Ou, reveja qualquer um dos seguintes artigos para obter informações relacionadas.

+ [Adicionar verificação ortográfica aos termos de consulta](speller-how-to-add.md)
+ [Classificação semântica e respostas](semantic-how-to-query-response.md)