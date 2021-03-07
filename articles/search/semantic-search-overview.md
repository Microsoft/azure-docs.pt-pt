---
title: Pesquisa semântica
titleSuffix: Azure Cognitive Search
description: Saiba como a Cognitive Search está a usar modelos de pesquisa semântica de aprendizagem profunda de Bing para tornar os resultados da pesquisa mais intuitivos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/05/2021
ms.custom: references_regions
ms.openlocfilehash: 19b7f9bc19bec989e524dce7172037025e2fe4fd
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102432984"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Pesquisa semântica em Pesquisa Cognitiva de Azure

> [!IMPORTANT]
> As funcionalidades de pesquisa semântica estão em pré-visualização pública, disponíveis apenas através da pré-visualização REST API. As funcionalidades de pré-visualização são oferecidas como está, nos [Termos Complementares de Utilização.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

A busca semântica é uma coleção de funcionalidades relacionadas com consultas que suportam uma experiência de consulta mais natural e de maior qualidade. As funcionalidades incluem reclasse semântica dos resultados da pesquisa, bem como legendas e respostas geração com destaque semântico. Os 50 melhores resultados devolvidos do [motor de pesquisa de texto completo](search-lucene-query-architecture.md) são reclassiados para encontrar os jogos mais relevantes.

A tecnologia subjacente é da Bing e da Microsoft Research, e está integrada na infraestrutura de Pesquisa Cognitiva. Para obter mais informações sobre a pesquisa e investimentos em IA que apoiam a pesquisa semântica, veja [como a IA de Bing está a alimentar a Azure Cognitive Search (Microsoft Research Blog)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/).

Para utilizar pesquisas semânticas em consultas, terá de fazer pequenas modificações no pedido de pesquisa, mas não é necessária nenhuma configuração extra ou reindexação.

As funcionalidades de pré-visualização do público incluem:

+ Modelo de classificação semântica que usa o contexto ou significado semântico para calcular uma pontuação de relevância
+ Legendas semânticas que resumem passagens chave de um resultado para fácil digitalização
+ Respostas semânticas para a consulta, se a consulta é uma questão
+ Destaques semânticos que trazem foco a frases e termos chave
+ Verificação ortográfica que corrige tipografias antes que os termos de consulta cheguem ao motor de busca

## <a name="availability-and-pricing"></a>Disponibilidade e preços

O ranking semântico está disponível através do [registo de inscrição,](https://aka.ms/SemanticSearchPreviewSignup)em serviços de pesquisa criados a nível Standard (S1, S2, S3), localizado numa dessas regiões: North Central US, West US 2, East US 2, North Europe, West Europe. A correção ortográfica está disponível nas mesmas regiões, mas não tem restrições de nível. Se tiver um serviço existente que satisfaça os critérios de nível e região, apenas é necessário inscrever-se.

Entre o lançamento de pré-estreia de 2 de março a 1 de abril, a correção ortográfica e o ranking semântico são oferecidos gratuitamente. Depois de 1 de abril, os custos computacionais de execução desta funcionalidade tornar-se-ão um evento fatural. O custo esperado é de CERCA de USD $500/mês para 250.000 consultas. Pode encontrar informações detalhadas sobre os custos documentados na [página de preços de Pesquisa Cognitiva](https://azure.microsoft.com/pricing/details/search/) e em Estimativa e gerir [custos.](search-sku-manage-costs.md)

## <a name="semantic-search-architecture"></a>Arquitetura de pesquisa semântica

Os componentes da procura semântica estão em camadas em cima do oleoduto de execução de consultas existente. A correção ortográfica (não mostrada no diagrama) melhora a recuperação corrigindo os erros em termos de consulta individual. Após a análise e análise concluídas, o motor de busca recupera os documentos que combinam com a consulta e pontua-os utilizando o [algoritmo de pontuação padrão](index-similarity-and-scoring.md#similarity-ranking-algorithms), seja BM25 ou clássico, dependendo de quando o serviço foi criado. Os perfis de pontuação também são aplicados nesta fase.

Tendo recebido os 50 melhores jogos, o [modelo semântico](semantic-how-to-query-response.md) do ranking reavalia o corpus do documento. Os resultados podem incluir mais de 50 partidas, mas apenas os primeiros 50 serão reclassiados. Para o ranking, o modelo utiliza aprendizagem automática e aprendizagem de transferência para re-pontuar os documentos com base no quão bem cada um corresponde à intenção da consulta.

Para criar legendas e respostas, a pesquisa semântica utiliza a representação linguística para extrair e destacar passagens-chave que melhor resumem um resultado. Se a consulta de pesquisa for uma pergunta, e as respostas forem solicitadas, a resposta incluirá uma passagem de texto que melhor responda à pergunta, conforme expresso pela consulta de pesquisa.

:::image type="content" source="media/semantic-search-overview/semantic-query-architecture.png" alt-text="Componentes semânticos em um oleoduto de consulta" border="true":::

## <a name="next-steps"></a>Passos seguintes

Um novo tipo de consulta permite o ranking de relevância e as estruturas de resposta da pesquisa semântica.

[Crie uma consulta semântica](semantic-how-to-query-request.md) para começar. Ou, reveja qualquer um dos seguintes artigos para obter informações relacionadas.

+ [Adicionar verificação ortográfica aos termos de consulta](speller-how-to-add.md)
+ [Classificação semântica e respostas (respostas e legendas)](semantic-how-to-query-response.md)