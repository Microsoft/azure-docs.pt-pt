---
title: Pesquisa semântica
titleSuffix: Azure Cognitive Search
description: Saiba como a Cognitive Search está a usar modelos de pesquisa semântica de aprendizagem profunda de Bing para tornar os resultados da pesquisa mais intuitivos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.custom: references_regions
ms.openlocfilehash: 634298952d990cd3639aa1c62592fde534b3e8b8
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232681"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Pesquisa semântica em Pesquisa Cognitiva de Azure

> [!IMPORTANT]
> As funcionalidades de pesquisa semântica estão em pré-visualização pública, disponíveis apenas através da pré-visualização REST API. As funcionalidades de pré-visualização são oferecidas como está, nos [Termos Complementares de Utilização,](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)e não estão garantidas a mesma implementação em disponibilidade geral. Para mais informações, consulte [Disponibilidade e preços.](semantic-search-overview.md#availability-and-pricing)

A busca semântica é uma coleção de funcionalidades relacionadas com consultas que suportam uma experiência de consulta mais natural e de maior qualidade. 

Estas capacidades incluem uma reedição semântica dos resultados da pesquisa, bem como a extração de legendas e respostas, com destaque semântico sobre termos e frases relevantes. Modelos pré-treinados de última geração são utilizados para extração e classificação. Para manter o desempenho rápido que os utilizadores esperam da pesquisa, a resumo semântica e o ranking são aplicados apenas aos 50 melhores resultados, como marcado pelo [algoritmo de pontuação de semelhança padrão](index-similarity-and-scoring.md#similarity-ranking-algorithms). Usando esses resultados como o corpus do documento, o ranking semântico re-pontua esses resultados com base na força semântica da partida.

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

Os componentes da procura semântica estendem o gasoduto de execução de consultas existente em ambas as direções. Se ativar a correção ortográfica, o [soletrador](speller-how-to-add.md) corrige os erros no início, antes que os termos de consulta cheguem ao motor de busca.

:::image type="content" source="media/semantic-search-overview/semantic-workflow.png" alt-text="Componentes semânticos na execução de consultas" border="true":::

A execução de consultas prossegue como de costume, com análises a prazo, análises e digitalizações sobre os índices invertidos. O motor recupera documentos usando a correspondência de token, e obtém os resultados usando o [algoritmo de pontuação de semelhança padrão](index-similarity-and-scoring.md#similarity-ranking-algorithms). As pontuações são calculadas com base no grau de semelhança linguística entre termos de consulta e termos correspondentes no índice. Se os definiu, os perfis de pontuação também são aplicados nesta fase. Os resultados são então transmitidos para o subsistema de pesquisa semântica.

Na etapa de preparação, o corpus do documento devolvido do conjunto de resultados iniciais é analisado ao nível da frase e do parágrafo para encontrar passagens que resumem cada documento. Em contraste com a pesquisa de palavras-chave, este passo utiliza a leitura e compreensão da máquina para avaliar o conteúdo. Como parte da composição do resultado, uma consulta semântica devolve legendas e respostas. Para formular, a pesquisa semântica usa a representação linguística para extrair e destacar passagens-chave que melhor resumem um resultado. Se a consulta de pesquisa for uma pergunta - e as respostas são solicitadas - a resposta incluirá também uma passagem de texto que melhor responda à pergunta, conforme expresso pela consulta de pesquisa. Tanto para as legendas como para as respostas, o texto existente é utilizado na formulação. Os modelos semânticos não compõem novas frases ou frases do conteúdo disponível, nem aplica lógica para chegar a novas conclusões. Em suma, o sistema nunca devolverá conteúdo que já não existe.

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