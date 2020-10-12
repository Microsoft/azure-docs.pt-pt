---
title: Painel de instrumentos - Compreensão linguística - LUIS
titleSuffix: Azure Cognitive Services
description: Corrija intenções e entidades com o painel de instrumentos da sua aplicação treinada. O dashboard exibe informações gerais da aplicação, com destaque para as intenções que devem ser corrigidas.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/08/2019
ms.openlocfilehash: 4867a065a85fab1e4abc7f19401239e5b76e1da4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541412"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Como utilizar o Dashboard para melhorar a sua aplicação

Encontre e corrija problemas com as intenções da sua aplicação treinada quando estiver a usar palavras de exemplo. O dashboard exibe informações gerais da aplicação, com destaque para as intenções que devem ser corrigidas.

Rever a análise do Dashboard é um processo iterativo, repetir à medida que muda e melhora o seu modelo.

Esta página não terá análises relevantes para apps que não tenham qualquer exemplo de declarações nas intenções, conhecidas como aplicações _apenas para padrões._

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Que problemas podem ser corrigidos a partir do painel de instrumentos?

Os três problemas abordados no painel de instrumentos são:

|Problema|Cor de gráfico|Explicação|
|--|--|--|
|Desequilíbrio de dados|-|Isto ocorre quando a quantidade de palavras de exemplo varia significativamente. Todas as intenções precisam de ter _aproximadamente_ o mesmo número de palavras de exemplo - exceto a intenção de Nenhum. Deve ter apenas 10%-15% da quantidade total de declarações na app.<br><br> Se os dados forem desequilibrados, mas a precisão da intenção estiver acima de determinado limiar, este desequilíbrio não é reportado como um problema.<br><br>**Comece por esta questão - pode ser a causa principal das outras questões.**|
|Previsões pouco claras|Laranja|Isto ocorre quando a intenção superior e as pontuações da próxima intenção estão perto o suficiente para que possam virar no próximo treino, devido a [amostragem negativa](luis-how-to-train.md#train-with-all-data) ou mais palavras de exemplo adicionadas à intenção. |
|Previsões incorretas|Red|Isto ocorre quando não se prevê uma expressão de exemplo para a intenção rotulada (a intenção em que se encontra).|

As previsões corretas são representadas com a cor azul.

O dashboard mostra estes problemas e diz-lhe quais as intenções que são afetadas e sugere o que deve fazer para melhorar a aplicação.

## <a name="before-app-is-trained"></a>Antes da aplicação ser treinada

Antes de treinar a aplicação, o painel de instrumentos não contém quaisquer sugestões para correções. Treine a sua aplicação para ver estas sugestões.

## <a name="check-your-publishing-status"></a>Verifique o seu estado de publicação

O **cartão de estado da publicação** contém informações sobre a última publicação da versão ativa.

Verifique se a versão ativa é a versão que pretende corrigir.

![O Dashboard mostra os serviços externos da app, as regiões publicadas e os acessos agregados ao ponto final.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Isto também mostra quaisquer serviços externos, regiões publicadas e resultados agregados de pontos finais.

## <a name="review-training-evaluation"></a>Avaliação da formação de revisão

O cartão **de avaliação de Formação** contém o resumo agregado da precisão geral da sua aplicação por área. A pontuação indica qualidade de intenção.

![O cartão de avaliação De formação contém a primeira área de informação sobre a precisão geral da sua aplicação.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

O gráfico indica as intenções corretamente previstas e as áreas problemáticas com cores diferentes. À medida que melhora a aplicação com as sugestões, esta pontuação aumenta.

As correções sugeridas são separadas por tipo de problema e são as mais significativas para a sua aplicação. Se preferir rever e corrigir problemas por intenção, use o cartão **[De intenções com erros](#intents-with-errors)** na parte inferior da página.

Cada área problemática tem intenções que precisam de ser corrigidas. Quando seleciona o nome de intenção, a página **Intent** abre com um filtro aplicado às expressões. Este filtro permite-lhe focar-se nas expressões que estão a causar o problema.

### <a name="compare-changes-across-versions"></a>Compare alterações entre versões

Crie uma nova versão antes de fazer alterações na aplicação. Na nova versão, faça as alterações sugeridas para as declarações de exemplo das intenções e, em seguida, treine novamente. No cartão de **avaliação de formação** da página do Dashboard, utilize a **alteração do Show a partir da versão treinada** para comparar as alterações.

![Compare alterações entre versões](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Corrigir versão adicionando ou editando palavras de exemplo e reconversão

O principal método de fixação da sua aplicação será adicionar ou editar palavras de exemplo e treinar. As novas ou alteradas declarações têm de seguir orientações para [as variadas declarações.](luis-concept-utterance.md)

Adicionar palavras de exemplo deve ser feito por alguém que:

* tem um alto grau de compreensão do que as expressões são nas diferentes intenções.
* sabe como as expressões numa intenção podem ser confundidas com outra intenção.
* é capaz de decidir se duas intenções, que são frequentemente confundidas entre si, devem ser colapsadas numa única intenção. Se for esse o caso, os diferentes dados devem ser retirados com entidades.

### <a name="patterns-and-phrase-lists"></a>Padrões e listas de frases

A página de análise não indica quando usar [padrões](luis-concept-patterns.md) ou [listas de frases](luis-concept-feature.md). Se as adicionar, pode ajudar com previsões incorretas ou pouco claras, mas não ajudará com o desequilíbrio de dados.

### <a name="review-data-imbalance"></a>Revisão do desequilíbrio de dados

Comece por esta questão - pode ser a causa principal das outras questões.

A lista de intenções **de desequilíbrio de dados** mostra intenções que precisam de mais declarações para corrigir o desequilíbrio de dados.

**Para resolver esta questão:**

* Adicione mais declarações à intenção e depois treine novamente.

Não adicione declarações à intenção de Zero, a menos que isso seja sugerido no painel de instrumentos.

> [!Tip]
> Utilize a terceira secção na página, **Declarações por intenção** com a definição **de Expressões (número),** como um guia visual rápido de que as intenções precisam de mais expressões.
    ![Utilize 'Expressances (número)' para encontrar intenções com desequilíbrio de dados.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Rever previsões incorretas

A lista de intenções **de previsão incorreta** mostra intenções que têm expressões, que são usadas como exemplos para uma intenção específica, mas são previstas para diferentes intenções.

**Para resolver esta questão:**

* Editar expressões para ser mais específico para a intenção e treinar novamente.
* Combine intenções se as expressões estiverem demasiado alinhadas e treine novamente.

### <a name="review-unclear-predictions"></a>Rever previsões pouco claras

A lista de intenções **de previsão pouco clara** mostra intenções com declarações com notas de previsão que não estão longe o suficiente do seu rival mais próximo, que a intenção máxima para a proclamação pode mudar no próximo treino, devido a uma [amostragem negativa.](luis-how-to-train.md#train-with-all-data)

**Para resolver esta questão;**

* Editar expressões para ser mais específico para a intenção e treinar novamente.
* Combine intenções se as expressões estiverem demasiado alinhadas e treine novamente.

## <a name="utterances-per-intent"></a>Declarações por intenção

Este cartão mostra a saúde geral da aplicação em todas as intenções. À medida que corrige intenções e retreinar, continue a olhar para este cartão para problemas.

O gráfico seguinte mostra uma aplicação bem equilibrada, quase sem problemas para corrigir.

![O gráfico seguinte mostra uma aplicação bem equilibrada, quase sem problemas para corrigir.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

O gráfico seguinte mostra uma aplicação mal equilibrada com muitos problemas para corrigir.

![A screenshot mostra previsões por intenção com vários resultados não claros ou incorretamente previstos.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Passe por cima do bar de cada intenção para obter informações sobre a intenção.

![A screenshot mostra previsões por intenção com detalhes de resultados não claros ou incorretamente previstos.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Utilize o **'Ordenar' por** recurso para organizar as intenções por tipo de problema para que possa focar-se nas intenções mais problemáticas com esse problema.

## <a name="intents-with-errors"></a>Intenções com erros

Este cartão permite-lhe rever questões para uma intenção específica. A visão padrão deste cartão é a intenção mais problemática para que saiba onde concentrar os seus esforços.

![O Cartão De Intenções com erros permite-lhe rever questões para uma intenção específica. O cartão é filtrado para as intenções mais problemáticas, por padrão, para que saiba onde concentrar os seus esforços.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

O gráfico de donuts de topo mostra os problemas com a intenção entre os três tipos de problemas. Se houver problemas nos três tipos de problemas, cada tipo tem o seu próprio gráfico abaixo, juntamente com quaisquer intenções rivais.

### <a name="filter-intents-by-issue-and-percentage"></a>Filtrar intenções por emissão e percentagem

Esta secção do cartão permite-lhe encontrar palavras de exemplo que estão a cair fora do seu limiar de erro. Idealmente, quer que as previsões corretas sejam significativas. Essa percentagem é orientada para os negócios e para o cliente.

Determine as percentagens limiar com as quais se sinta confortável para o seu negócio.

O filtro permite-lhe encontrar intenções com questões específicas:

|Filtrar|Percentagem sugerida|Objetivo|
|--|--|--|
|As intenções mais problemáticas|-|**Comece aqui** - Corrigir as expressões nesta intenção melhorará a aplicação mais do que outras correções.|
|Previsões corretas abaixo|60%|Esta é a percentagem de declarações na intenção selecionada que estão corretas, mas têm uma pontuação de confiança abaixo do limiar. |
|Previsões pouco claras acima|15%|Esta é a percentagem de declarações na intenção selecionada que são confundidas com a intenção rival mais próxima.|
|Previsões incorretas acima|15%|Esta é a percentagem de declarações na intenção selecionada que são incorretamente previstas. |

### <a name="correct-prediction-threshold"></a>Limiar de previsão correto

O que é uma previsão confiante de confiança para si? No início do desenvolvimento de aplicações, 60% pode ser o seu alvo. Utilize as **previsões correctais abaixo** com a percentagem de 60% para encontrar quaisquer expressões na intenção selecionada que precisam de ser corrigidas.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Limiar de previsão pouco claro ou incorreto

Estes dois filtros permitem-lhe encontrar expressões na intenção selecionada para além do seu limiar. Pode pensar nestas duas percentagens como percentagens de erro. Se se sentir confortável com uma taxa de erro de 10-15% para as previsões, desagure o limiar do filtro para 15% para encontrar todas as expressões acima deste valor.

## <a name="next-steps"></a>Passos seguintes

* [Gerir os seus recursos do Azure](luis-how-to-azure-subscription.md)
