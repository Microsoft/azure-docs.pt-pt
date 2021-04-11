---
title: Pesquisa semântica
titleSuffix: Azure Cognitive Search
description: Saiba como a Cognitive Search está a usar modelos de pesquisa semântica de aprendizagem profunda de Bing para tornar os resultados da pesquisa mais intuitivos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom: references_regions
ms.openlocfilehash: b5b33007f71cfc2a29005ce84f1fedba90dd1bf1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561972"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Pesquisa semântica em Pesquisa Cognitiva de Azure

> [!IMPORTANT]
> A pesquisa semântica está em pré-visualização pública, disponível através da pré-visualização REST API e portal. As funcionalidades de pré-visualização são oferecidas como está, nos [Termos Complementares de Utilização,](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)e não estão garantidas a mesma implementação em disponibilidade geral. Estas características são faturas. Para mais informações, consulte [Disponibilidade e preços.](semantic-search-overview.md#availability-and-pricing)

A pesquisa semântica é uma coleção de capacidades relacionadas com consultas que adicionam relevância semântica e compreensão linguística aos resultados da pesquisa. *O ranking semântico* procura contexto e parentesco entre termos, elevando os jogos que fazem mais sentido dada a consulta. A compreensão linguística encontra *legendas* e *respostas* dentro do seu conteúdo que resumem o documento correspondente ou respondem a uma pergunta, que pode ser prestada numa página de resultados de pesquisa para uma experiência de pesquisa mais produtiva.

Modelos pré-treinados de última geração são usados para resumo e classificação. Para manter o desempenho rápido que os utilizadores esperam da pesquisa, a resumo semântica e o ranking são aplicados apenas aos 50 melhores resultados, como marcado pelo [algoritmo de pontuação de semelhança padrão](index-similarity-and-scoring.md#similarity-ranking-algorithms). Usando esses resultados como o corpus do documento, o ranking semântico re-pontua esses resultados com base na força semântica da partida.

A tecnologia subjacente é da Bing e da Microsoft Research, e integrada na infraestrutura de Pesquisa Cognitiva como uma funcionalidade de complemento. Para obter mais informações sobre a pesquisa e investimentos em IA que apoiam a pesquisa semântica, veja [como a IA de Bing está a alimentar a Azure Cognitive Search (Microsoft Research Blog)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/).

O vídeo a seguir fornece uma visão geral das capacidades.

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

## <a name="components-and-workflow"></a>Componentes e fluxo de trabalho

A procura semântica melhora a precisão e a recuperação com a adição das seguintes capacidades:

| Funcionalidade | Descrição |
|---------|-------------|
| [Verificação de Ortografia](speller-how-to-add.md) | Corrige os erros antes que os termos de consulta cheguem ao motor de busca. |
| [Ranking semântico](semantic-ranking.md) | Usa o contexto ou significado semântico para calcular uma nova pontuação de relevância. |
| [Legendas e destaques semânticos](semantic-how-to-query-request.md) | Frases e frases de um documento que melhor resume o conteúdo, com destaques sobre passagens-chave para uma fácil digitalização. As legendas que resumem um resultado são úteis quando os campos de conteúdo individuais são demasiado densos para a página de resultados. O texto realçado eleva os termos e frases mais relevantes para que os utilizadores possam rapidamente determinar por que uma correspondência foi considerada relevante. |
| [Respostas semânticas](semantic-answers.md) | Uma subestrutura opcional e adicional devolvida de uma consulta semântica. Dá uma resposta direta a uma pergunta que parece uma pergunta. |

### <a name="order-of-operations"></a>Ordem de operações

Os componentes da procura semântica estendem o gasoduto de execução de consultas existente em ambas as direções. Se ativar a correção ortográfica, o [soletrador](speller-how-to-add.md) corrige os tipografias no início da consulta, antes que os termos cheguem ao motor de busca.

:::image type="content" source="media/semantic-search-overview/semantic-workflow.png" alt-text="Componentes semânticos na execução de consultas" border="true":::

A execução de consultas prossegue como de costume, com análises a prazo, análises e digitalizações sobre os índices invertidos. O motor recupera documentos usando a correspondência de token, e obtém os resultados usando o [algoritmo de pontuação de semelhança padrão](index-similarity-and-scoring.md#similarity-ranking-algorithms). As pontuações são calculadas com base no grau de semelhança linguística entre termos de consulta e termos correspondentes no índice. Se os definiu, os perfis de pontuação também são aplicados nesta fase. Os resultados são então transmitidos para o subsistema de pesquisa semântica.

Na etapa de preparação, o corpus do documento devolvido do conjunto de resultados iniciais é analisado ao nível da frase e do parágrafo para encontrar passagens que resumem cada documento. Em contraste com a pesquisa de palavras-chave, este passo utiliza a leitura e compreensão da máquina para avaliar o conteúdo. Através desta fase de processamento de conteúdos, uma consulta semântica devolve [legendas](semantic-how-to-query-request.md) e [respostas](semantic-answers.md). Para formular, a pesquisa semântica usa a representação linguística para extrair e destacar passagens-chave que melhor resumem um resultado. Se a consulta de pesquisa for uma pergunta - e as respostas são solicitadas - a resposta incluirá também uma passagem de texto que melhor responda à pergunta, conforme expresso pela consulta de pesquisa. 

Tanto para as legendas como para as respostas, o texto existente é utilizado na formulação. Os modelos semânticos não compõem novas frases ou frases do conteúdo disponível, nem aplica lógica para chegar a novas conclusões. Em suma, o sistema nunca devolverá conteúdo que já não existe.

Os resultados são então re-pontuados com base na [semelhança conceptual](semantic-ranking.md) dos termos de consulta.

Para utilizar capacidades semânticas em consultas, terá de fazer pequenas modificações no [pedido de pesquisa](semantic-how-to-query-request.md), mas não é necessária nenhuma configuração extra ou reindexação.

## <a name="availability-and-pricing"></a>Disponibilidade e preços

As capacidades semânticas estão disponíveis através do [registo de inscrição,](https://aka.ms/SemanticSearchPreviewSignup)em serviços de pesquisa criados a nível Standard (S1, S2, S3), localizados numa dessas regiões: North Central US, West US 2, East US 2, North Europe, West Europe. 

A correção ortográfica está disponível nas mesmas regiões, mas não tem restrições de nível. Se tiver um serviço existente que satisfaça os critérios de nível e região, apenas é necessário inscrever-se.

Entre o lançamento de pré-estreia de 2 de março a 1 de abril, a correção ortográfica e o ranking semântico são oferecidos gratuitamente. Depois de 1 de abril, os custos computacionais de execução desta funcionalidade tornar-se-ão um evento fatural. O custo esperado é de CERCA de USD $500/mês para 250.000 consultas. Pode encontrar informações detalhadas sobre os custos documentados na [página de preços de Pesquisa Cognitiva](https://azure.microsoft.com/pricing/details/search/) e em Estimativa e gerir [custos.](search-sku-manage-costs.md)

## <a name="next-steps"></a>Passos seguintes

Um novo tipo de consulta permite o ranking de relevância e as estruturas de resposta da pesquisa semântica.

[Crie uma consulta semântica](semantic-how-to-query-request.md) para começar. Ou, reveja os seguintes artigos para obter informações relacionadas.

+ [Adicionar verificação ortográfica aos termos de consulta](speller-how-to-add.md)
+ [Devolva uma resposta semântica](semantic-answers.md)
+ [Ranking semântico](semantic-ranking.md)
+ [Introdução de pesquisa semântica (blog post)](https://techcommunity.microsoft.com/t5/azure-ai/introducing-semantic-search-bringing-more-meaningful-results-to/ba-p/2175636)
+ [Encontre insights significativos utilizando capacidades semânticas (vídeo do AI Show)](https://channel9.msdn.com/Shows/AI-Show/Find-meaningful-insights-using-semantic-capabilities-in-Azure-Cognitive-Search)