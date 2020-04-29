---
title: Proclamação do exemplo da entidade do rótulo
description: Aprenda a rotular uma entidade aprendida com subcomponentes numa página de detalhes de intenção do portal LUIS.
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: ea5fbea902c9694d9a8a6a8a5bffcf5e7234bbbd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81382409"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Rotular entidade aprendida por máquinas numa expressão de exemplo

Rotular uma entidade num exemplo dá ao LUIS um exemplo do que é a entidade e onde a entidade pode aparecer na expressão.

## <a name="labeling-machine-learned-entity"></a>Entidade aprendida com máquinas de rotulagem

Considere a `hi, please I want a cheese pizza in 20 minutes`frase.

1. Selecione o texto mais à esquerda e, em seguida, selecione o texto mais à direita da entidade e, em seguida, escolha a entidade com a qual pretende rotular, neste caso A Encomenda Completa. A _encomenda completa_ está rotulada na seguinte imagem.

    > [!div class="mx-imgBorder"]
    > ![Rotular entidade completa aprendida com máquinas](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Selecione a entidade a partir da janela pop-up. A entidade de encomenda completa de pizza seletiva inclui todas as palavras (da esquerda para a direita em inglês) que são rotuladas.

## <a name="review-labeled-text"></a>Rever texto rotulado

Após a rotulagem, reveja a expressão do exemplo e certifique-se de que o período de texto selecionado foi sublinhado com a entidade escolhida. A linha sólida indica que o texto foi rotulado.

> [!div class="mx-imgBorder"]
> ![Entidade completa e aprendida com máquinas](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Confirmar entidade prevista

Se houver uma caixa pontilhada em torno do espaço de texto e o nome da entidade estiver acima da expressão, indica que o texto está previsto, mas _ainda não rotulado_. Para transformar a previsão num rótulo, selecione a linha de expressão e, em seguida, selecione **confirmar as previsões da entidade**.

> [!div class="mx-imgBorder"]
> ![Prever entidade completa aprendida com máquinas](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

Em alternativa, pode selecionar o nome da entidade acima do texto e, em seguida, selecionar **confirmar** previsão do menu que aparece.

> [!div class="mx-imgBorder"]
> ![Prever entidade completa aprendida com menu](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted-menu.png)

## <a name="label-entity-by-painting-with-entity-palette-cursor"></a>Entidade de etiqueta sintetizadora por pintura com cursor de paleta de entidade

A paleta de entidades oferece uma alternativa à experiência de rotulagem anterior. Permite-lhe escovar o texto para rotulá-lo instantaneamente com uma entidade.

1. Abra a paleta de entidade seletivano no ícone Highlighter na parte superior direita da tabela de expressão.

    > [!div class="mx-imgBorder"]
    > ![Paleta de entidades para entidade sinuosa](media/label-utterances/example-1-label-machine-learned-entity-palette.png)

2. Selecione o componente da entidade. Esta ação é indicada visualmente com um novo cursor. O cursor segue o rato enquanto se move no portal.

    > [!div class="mx-imgBorder"]
    > ![Paleta de entidades para entidade sinuosa](media/label-utterances/example-1-label-machine-learned-entity-palette-menu.png)

3. Na expressão do exemplo, _pinte_ a entidade com o cursor.

    > [!div class="mx-imgBorder"]
    > ![Paleta de entidades para entidade sinuosa](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="labeling-subcomponents-of-a-machine-learned-entity"></a>Rotulagem subcomponentes de uma entidade aprendida por máquinas

Os subcomponentes nas entidades são rotulados exatamente da mesma forma que as entidades de alto nível. Ao selecionar texto, as entidades disponíveis na janela pop-up são relativas ao contexto em que o texto aparece. Por exemplo, se tiver uma entidade de 5 níveis aprendido com máquinas, e estiver a selecionar texto seleção que tenha sido rotulado com os níveis 1 e 2 (indicado por uma entidade rotulada sob o exemplo da expressão), as entidades disponíveis na janela pop-up estão limitadas ao contexto de componentes do 3º nível. Para rotular o texto com outras entidades, selecione Label como outra opção **de entidade.**

> [!div class="mx-imgBorder"]
> ![Paleta de entidades para entidade sinuosa](media/label-utterances/example-1-label-machine-learned-entity-subcomponent.png)

Os subcomponentes só podem ser rotulados se o progenitor também estiver rotulado.

## <a name="labeling-entity-roles"></a>Papel de entidade de rotulagem

As funções da entidade são rotuladas utilizando a paleta de entidades.

1. Na página de detalhes de Intenções, selecione a **paleta Entidade** a partir da barra de ferramentas de contexto.
1. Depois da paleta de Entidade abrir, selecione a entidade da lista de entidades.
1. Mude-se para o **inspetor da Entidade,** e selecione uma função existente ou crie um novo papel.
1. No texto de expressão de exemplo, rotule o texto com o papel da entidade.

## <a name="labeling-for-punctuation"></a>Rotulagem para pontuação

Não precisa de rotular para pontuação. Utilize [as definições](luis-reference-application-settings.md) de aplicação para controlar se a pontuação tiver impacto nas previsões de expressão.

## <a name="unlabel-entities"></a>Entidades de suprimente

Para desmarcar uma entidade, selecione o nome da entidade por baixo do texto e selecione **Unlabel**. Se a entidade que está a tentar desetiquetar tem subcomponentes rotulados, então os subcomponentes devem ser não rotulados primeiro.

## <a name="editing-labels-using-the-entity-palette"></a>Etiquetas de edição usando a paleta de entidades

Se cometer um erro ao rotular, a paleta de entidades é uma ferramenta fácil que permite edificações rápidas. Por exemplo, se uma etiqueta de entidade abranger uma palavra extra por engano, e já tiver subcomponentes rotulados, então pode usar a paleta de entidades para escovar o período de palavras mais curto necessário.

Por exemplo:

1. Subcomponente tipo pizza abrange "pizza de queijo com" que inclui uma palavra extra incorreta - "com"

    > [!div class="mx-imgBorder"]
    > ![Paleta de entidades para entidade sinuosa](media/label-utterances/edit-label-with-palette-1.png)

2. Use a paleta de entidades para escolher o Tipo de Pizza e escovar a "pizza de queijo". O resultado é que só a pizza de queijo está rotulada como Pizza Type agora.

    > [!div class="mx-imgBorder"]
    > ![Paleta de entidades para entidade sinuosa](media/label-utterances/edit-label-with-palette-2.png)

## <a name="labels-for-matching-text-entities"></a>Etiquetas para entidades de texto correspondente

As entidades de texto-correspondência incluem entidades pré-construídas, entidades de expressão regular, entidades de lista e padrões.quaisquer entidades. Estes são automaticamente rotulados por LUIS, pelo que não são obrigados a ser rotulados manualmente pelos utilizadores.

## <a name="entity-prediction-errors"></a>Erros de previsão da entidade

Erros de previsão da entidade indicam que a entidade prevista não corresponde à entidade rotulada. Isto é visualizado com um indicador de precaução ao lado da expressão.

> [!div class="mx-imgBorder"]
> ![Paleta de entidades para entidade sinuosa](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Passos seguintes

Utilize as [expressões finais](luis-how-to-review-endpoint-utterances.md) do [dashboard](luis-how-to-use-dashboard.md) e reveja as palavras finais para melhorar a qualidade de previsão da sua aplicação.