---
title: Exemplo de entidade de rótulo expressão
titleSuffix: Azure Cognitive Services
description: Saiba como rotular uma entidade aprendida por máquina com subcomponentes em um exemplo de expressão em uma página de detalhes da intenção do portal do LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 87b7375b2da0effbc18fff6a7e9d67383c93a403
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74135111"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Rotular entidade aprendida por máquina em um exemplo de expressão

Rotular uma entidade em um exemplo expressão mostra LUIS tem um exemplo da entidade é e onde a entidade pode aparecer no expressão. 

## <a name="labeling-machine-learned-entity"></a>Rotulando a entidade aprendida por máquina

Considere a frase `hi, please I want a cheese pizza in 20 minutes`. 

1. Selecione o texto mais à esquerda e, em seguida, selecione o texto mais à direita da entidade. A _ordem completa_ é rotulada na imagem a seguir.

    > [!div class="mx-imgBorder"]
    > Rótulo de ![completo de entidade aprendida por máquina](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Selecione a entidade na janela pop-up. A entidade rotulada de ordem de pizza completa inclui todas as palavras (da esquerda para a direita em inglês) que são rotuladas. 

> [!TIP]
> As entidades disponíveis na janela pop-up são relativas ao contexto no qual o texto é exibido. Por exemplo, se você tiver uma entidade aprendida por computador de cinco níveis e estiver selecionando o texto no terceiro nível (indicado por um nome de entidade rotulado no exemplo expressão), as entidades disponíveis na janela pop-up serão limitadas ao contexto de subcomponentes do terceiro nível (subcomponentes do 4º nível). 

## <a name="review-labeled-text"></a>Examinar texto rotulado

Depois de rotular, examine o exemplo expressão. LUIS aplica o modelo atual ao expressão de exemplo após a rotulagem. A linha sólida indica que o texto foi rotulado. 

> [!div class="mx-imgBorder"]
> ![rotuladas como entidade conhecida pelo computador](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="when-to-train"></a>Quando treinar

Se o modelo atual deve dar suporte à entidade rotulada, mas o exemplo expressão continua mostrando o texto como previsto, mas não rotulado, treina seu aplicativo.  

## <a name="confirm-predicted-entity"></a>Confirmar entidade prevista

Se o indicador visual estiver acima do expressão, ele indicará que o texto está previsto, mas _não rotulado ainda_. Para transformar a previsão em um rótulo, selecione o expressão e, em seguida, selecione **confirmar previsões de entidade**.

> [!div class="mx-imgBorder"]
> Previsão de ![](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png) entidade aprendida completa pelo computador

## <a name="label-subcomponent-entity-by-painting-with-entity-palette-cursor"></a>Rotular entidade de subcomponente pintando com o cursor da paleta de entidades

1. Para corrigir previsões (entidades, que aparecem acima do exemplo expressão), abra a paleta de entidades. 

    > [!div class="mx-imgBorder"]
    > ![paleta de entidades para entidade aprendida por máquina](media/label-utterances/pizza-entity-palette-with-pizza-type-selected.png)

1. Selecione o subcomponente da entidade. Essa ação é visualmente indicada com um novo cursor. O cursor segue o mouse à medida que você move no Portal. 

    > [!div class="mx-imgBorder"]
    > ![paleta de entidades para entidade aprendida por máquina](media/label-utterances/pizza-type-entity-palette-cursor.png)

1. No exemplo expressão, _pinte_ a entidade com o cursor. 

    > [!div class="mx-imgBorder"]
    > ![paleta de entidades para entidade aprendida por máquina](media/label-utterances/paint-subcomponent-with-entity-palette-cursor.png)

## <a name="labeling-matching-text-entities-to-a-machine-learned-entity"></a>Rotulando entidades de texto correspondentes para uma entidade aprendida por máquina

As entidades de texto correspondentes incluem entidades predefinidas, entidades de expressão regular e entidades de lista. Você os adiciona a uma entidade aprendida por máquina, como restrições a um subcomponente, quando você cria ou edita a entidade aprendida por máquina. 

**Depois que essas restrições forem adicionadas, você não precisará rotular o texto correspondente no exemplo expressão.**

## <a name="entity-prediction-errors"></a>Erros de previsão de entidade

Os erros de previsão de entidade mostram um indicador de cuidado. Isso indica que a entidade prevista não corresponde à entidade rotulada. 

> [!div class="mx-imgBorder"]
> ![paleta de entidades para entidade aprendida por máquina](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Passos seguintes

Use o [painel](luis-how-to-use-dashboard.md) e [examine o ponto de extremidade declarações](luis-how-to-review-endpoint-utterances.md) para melhorar a qualidade de previsão de seu aplicativo.
