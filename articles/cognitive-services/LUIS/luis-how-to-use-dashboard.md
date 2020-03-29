---
title: Dashboard - Compreensão da Linguagem - LUIS
titleSuffix: Azure Cognitive Services
description: Corrija as intenções e entidades com o painel de instrumentos da sua aplicação treinada. O dashboard exibe informações globais da aplicação, com destaques de intenções que devem ser corrigidas.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d9ae126753f55349f9bf3eefd20bc4d222866af1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73888204"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Como utilizar o Dashboard para melhorar a sua aplicação

Encontre e corrija problemas com as intenções da sua app treinada quando estiver a usar expressões de exemplo. O dashboard exibe informações globais da aplicação, com destaques de intenções que devem ser corrigidas. 

Rever a análise do Dashboard é um processo iterativo, repita à medida que muda e melhora o seu modelo.

Esta página não terá análises relevantes para apps que não tenham nenhum exemplo nas intenções, conhecidas como aplicações _apenas de padrões._ 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Que problemas podem ser corrigidos a partir do painel de instrumentos?

Os três problemas abordados no painel de instrumentos são:

|Problema|Cor do gráfico|Explicação|
|--|--|--|
|Desequilíbrio de dados|-|Isto ocorre quando a quantidade de declarações de exemplo varia significativamente. Todas as intenções devem ter _aproximadamente_ o mesmo número de declarações de exemplo - exceto a intenção de Nenhum. Deve ter apenas 10%-15% da quantidade total de expressões na app.<br><br> Se os dados forem desequilibrados, mas a precisão das intenções for superior a determinado limiar, este desequilíbrio não é reportado como um problema.<br><br>**Comece com esta questão - pode ser a causa principal das outras questões.**|
|Previsões pouco claras|Orange|Isto ocorre quando a intenção máxima e as pontuações da próxima intenção estão perto o suficiente para que possam virar o próximo treino, devido a [amostragem negativa](luis-how-to-train.md#train-with-all-data) ou mais declarações de exemplo adicionadas à intenção. |
|Previsões incorretas|Vermelho|Isto ocorre quando não se prevê um exemplo para a intenção rotulada (a intenção em que se encontra).|

As previsões corretas são representadas com a cor azul.

O dashboard mostra estes problemas e diz-lhe quais as intenções que são afetadas e sugere o que deve fazer para melhorar a aplicação. 

## <a name="before-app-is-trained"></a>Antes da aplicação ser treinada 

Antes de treinar a aplicação, o painel de instrumentos não contém sugestões de correções. Treine a sua aplicação para ver estas sugestões.  

## <a name="check-your-publishing-status"></a>Verifique o seu estado de publicação

O cartão de **estado de publicação** contém informações sobre a última publicação da versão ativa. 

Verifique se a versão ativa é a versão que pretende corrigir. 

![O Dashboard mostra os serviços externos da app, regiões publicadas e sucessos agregados de pontofinal.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Isto também mostra quaisquer serviços externos, regiões publicadas e acessos agregados de pontos finais. 

## <a name="review-training-evaluation"></a>Avaliação de formação de revisão

O cartão de avaliação de **formação** contém o resumo agregado da precisão global da sua aplicação por área. A pontuação indica qualidade de intenção. 

![O cartão de avaliação de formação contém a primeira área de informação sobre a precisão geral da sua aplicação.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

O gráfico indica as intenções corretamente previstas e as áreas problemáticas com cores diferentes. À medida que melhora a app com as sugestões, esta pontuação aumenta. 

As correções sugeridas são separadas por tipo de problema e são as mais significativas para a sua aplicação. Se preferir rever e corrigir problemas por intenção, utilize o cartão **[De Intenções com erros](#intents-with-errors)** na parte inferior da página. 

Cada área problemática tem intenções que precisam de ser corrigidas. Quando seleciona o nome da intenção, a página **Intent** abre-se com um filtro aplicado às palavras. Este filtro permite-lhe focar-se nas expressões que estão a causar o problema.

### <a name="compare-changes-across-versions"></a>Comparar alterações entre versões

Crie uma nova versão antes de fazer alterações na aplicação. Na nova versão, faça as alterações sugeridas às declarações de exemplo da intenção e, em seguida, treine novamente. No cartão de avaliação de **formação** da página do Dashboard, utilize a **alteração do Show da versão treinada** para comparar as alterações. 

![Comparar alterações entre versões](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Corrigir versão adicionando ou editando expressões de exemplo e retreinando

O principal método de fixação da sua aplicação será adicionar ou editar expressões de exemplo e retreinar. As novas ou alteradas expressões têm de seguir as orientações para [as expressões variadas.](luis-concept-utterance.md)

A adição de declarações de exemplo deve ser feita por alguém que:

* tem um alto grau de compreensão do que as expressões estão nas diferentes intenções.
* sabe como as declarações numa intenção podem ser confundidas com outra intenção.
* é capaz de decidir se duas intenções, que são frequentemente confundidas uma com a outra, devem ser colapsadas numa única intenção. Se for esse o caso, os diferentes dados devem ser retirados com entidades.

### <a name="patterns-and-phrase-lists"></a>Padrões e listas de frases

A página de análise não indica quando usar padrões ou [listas](luis-concept-patterns.md) de [frases](luis-concept-feature.md). Se as adicionar, pode ajudar com previsões incorretas ou pouco claras, mas não ajudará com o desequilíbrio de dados. 

### <a name="review-data-imbalance"></a>Rever o desequilíbrio de dados

Comece com esta questão - pode ser a causa principal das outras questões.

A lista de intenções de **desequilíbrio de dados** mostra intenções que precisam de mais expressões para corrigir o desequilíbrio de dados. 

**Para corrigir esta questão:**

* Adicione mais declarações à intenção e treine novamente. 

Não adicione declarações à intenção "Nenhum", a menos que isso seja sugerido no painel de instrumentos.

> [!Tip]
> Utilize a terceira secção da página, **Utterances por intenção** com a definição **de Utterances (número),** como um guia visual rápido de que as intenções precisam de mais expressões.  
    ![Utilize 'Utterances (número)' para encontrar intenções com desequilíbrio de dados.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Rever previsões incorretas

A lista de intenções **de previsão incorreta** mostra intenções que têm expressões, que são usadas como exemplos para uma intenção específica, mas são previstas para diferentes intenções. 

**Para corrigir esta questão:**

* Editar as palavras para ser mais específico da intenção e treinar novamente.
* Combine as intenções se as palavras estiverem muito alinhadas e treine novamente.

### <a name="review-unclear-predictions"></a>Rever previsões pouco claras

A lista de intenções **de previsão pouco clara** mostra intenções com declarações com pontuações de previsão que não estão longe o suficiente do seu rival mais próximo, que a principal intenção para a expressão pode mudar no próximo treino, devido à [amostragem negativa.](luis-how-to-train.md#train-with-all-data)

**Para corrigir esta questão;**

* Editar as palavras para ser mais específico da intenção e treinar novamente.
* Combine as intenções se as palavras estiverem muito alinhadas e treine novamente.

## <a name="utterances-per-intent"></a>Expressões por intenção

Este cartão mostra a saúde geral da aplicação através das intenções. À medida que corrige as intenções e retreina, continue a olhar para este cartão para problemas.

O gráfico seguinte mostra uma aplicação bem equilibrada, quase sem problemas para corrigir.

![O gráfico seguinte mostra uma aplicação bem equilibrada, quase sem problemas para corrigir.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

O gráfico seguinte mostra uma aplicação mal equilibrada, com muitos problemas para corrigir.

![O gráfico seguinte mostra uma aplicação bem equilibrada, quase sem problemas para corrigir.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Passe sobre o bar de cada intenção para obter informações sobre a intenção. 

![O gráfico seguinte mostra uma aplicação bem equilibrada, quase sem problemas para corrigir.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Utilize o **recurso Sort por** recurso para organizar as intenções por tipo de edição para que possa focar-se nas intenções mais problemáticas com esse problema. 

## <a name="intents-with-errors"></a>Intenções com erros

Este cartão permite-lhe rever questões para uma intenção específica. A visão padrão deste cartão é a intenção mais problemática para que saiba onde concentrar os seus esforços.

![O cartão Intents com erros permite-lhe rever questões para uma intenção específica. O cartão é filtrado para as intenções mais problemáticas, por padrão, para que saiba onde concentrar os seus esforços.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

O gráfico superior de donuts mostra os problemas com a intenção em todos os três tipos de problemas. Se houver problemas nos três tipos de problemas, cada tipo tem o seu próprio gráfico abaixo, juntamente com quaisquer intenções rivais. 

### <a name="filter-intents-by-issue-and-percentage"></a>Filtrar intenções por emissão e percentagem

Esta secção do cartão permite-lhe encontrar declarações de exemplo que estão a cair fora do limiar de erro. Idealmente, quer que as previsões corretas sejam significativas. Essa percentagem é orientada para os negócios e para o cliente. 

Determine as percentagens de limiar com as quais se sente confortável para o seu negócio. 

O filtro permite-lhe encontrar intenções com problemaespecífico:

|Filtro|Percentagem sugerida|Objetivo|
|--|--|--|
|As intenções mais problemáticas|-|**Comece aqui** - A fixação das declarações nesta intenção melhorará a aplicação mais do que outras correções.|
|Corrigir previsões abaixo|60%|Esta é a percentagem de declarações na intenção selecionada que estão corretas, mas têm uma pontuação de confiança abaixo do limiar. |
|Previsões pouco claras acima|15%|Esta é a percentagem de declarações na intenção selecionada que são confundidas com a intenção rival mais próxima.|
|Previsões incorretas acima|15%|Esta é a percentagem de declarações na intenção selecionada que são incorretamente previstas. |

### <a name="correct-prediction-threshold"></a>Limite de previsão correto

O que é uma previsão confiante de confiança para si? No início do desenvolvimento de apps, 60% pode ser o seu alvo. Utilize as **previsões corretas abaixo** com a percentagem de 60% para encontrar quaisquer declarações na intenção selecionada que precisam de ser corrigidas.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Limiar de previsão pouco claro ou incorreto

Estes dois filtros permitem-lhe encontrar expressões na intenção selecionada para além do seu limiar. Pode pensar nestas duas percentagens como percentagens de erro. Se se sentir confortável com uma taxa de erro de 10-15% para previsões, detete te o limiar do filtro para 15% para encontrar todas as expressões acima deste valor. 

## <a name="next-steps"></a>Passos seguintes

* [Gerir os seus recursos do Azure](luis-how-to-azure-subscription.md)
