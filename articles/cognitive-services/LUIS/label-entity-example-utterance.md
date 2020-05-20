---
title: Proclamação do exemplo da entidade do rótulo
description: Aprenda a rotular uma entidade de machine-learning com subcomponentes num exemplo numa página detalhada de intenção do portal LUIS.
ms.topic: conceptual
ms.date: 05/17/2020
ms.openlocfilehash: 0181057bd693280223806e9b5b7cd8c7f7345f79
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683777"
---
# <a name="label-machine-learning-entity-in-an-example-utterance"></a>Rotular entidade de aprendizagem automática num exemplo

Rotular uma entidade num exemplo dá ao LUIS um exemplo do que é a entidade e onde a entidade pode aparecer na expressão.

## <a name="label-example-utterances-from-the-intent-detail-page"></a>Rotule as declarações de exemplo da página de detalhes de intenções

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **De Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. Selecione a Intenção que tem as palavras exemplo que pretende rotular para extração com uma entidade.
1. Selecione o texto que pretende rotular e selecione a entidade.

## <a name="label-with-the-entity-palette-visible"></a>Etiqueta com a Paleta de Entidade visível

Depois de ter planeado o seu esquema com entidades, mantenha visível a **paleta de Entidades** durante a rotulagem. A **paleta Entidade** é um lembrete do que as suas entidades planeou extrair.

Para aceder à Paleta de **Entidades,** selecione o símbolo na barra de **@** ferramentas contextual acima da lista de pronunciamentos de exemplo.

> [!div class="mx-imgBorder"]
> ![Screenshot da paleta de entidades na página de detalhes de Intenções.](media/label-utterances/entity-palette-from-tool-bar.png)

## <a name="label-entity-from-in-place-menu"></a>Entidade de etiquetas do menu no local

Considere a expressão do exemplo, `hi, please I want a cheese pizza in 20 minutes` .

1. Selecione o texto mais à esquerda e, em seguida, selecione o texto mais à direita da entidade, em seguida, a partir do menu no local, escolha a entidade com a qual pretende rotular.

    > [!div class="mx-imgBorder"]
    > ![Rotular entidade completa de aprendizagem automática](media/label-utterances/label-steps-in-place-menu.png)


## <a name="label-entity-from-entity-palette"></a>Entidade de etiquetas da Paleta de Entidades

A paleta de entidades oferece uma alternativa à experiência de rotulagem anterior. Permite-lhe escovar o texto para rotulá-lo instantaneamente com uma entidade.

1. Abra a paleta de entidade seletivano no **@** símbolo na parte superior direita da tabela de expressão.

2. Selecione a entidade a partir da paleta que pretende rotular. Esta ação é indicada visualmente com um novo cursor. O cursor segue o rato enquanto se move no portal LUIS.

3. Na expressão do exemplo, _pinte_ a entidade com o cursor.

    > [!div class="mx-imgBorder"]
    > ![Paleta de entidades para entidade de aprendizagem automática](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="adding-entity-as-a-feature-from-the-entity-palette"></a>Adicionar entidade como recurso da Paleta de Entidades

A secção inferior da Paleta de Entidades permite-lhe adicionar funcionalidades à entidade atualmente selecionada. Pode selecionar de todas as entidades existentes e listas de frases ou criar uma nova lista de frases.

> [!div class="mx-imgBorder"]
> ![Screenshot da paleta de entidade com entidade como recurso](media/label-utterances/entity-palette-entity-as-a-feature.png)

## <a name="labeling-entity-roles"></a>Papel de entidade de rotulagem

As funções da entidade são rotuladas utilizando a **paleta Entidade**.

1. Na página de detalhes de Intenções, selecione a **paleta Entidade** a partir da barra de ferramentas de contexto.
1. Depois da paleta de Entidade abrir, selecione a entidade da lista de entidades.
1. Abaixo da lista de entidades, selecione um papel existente.
1. No texto de expressão de exemplo, rotule o texto com o papel da entidade.

## <a name="review-labeled-text"></a>Rever texto rotulado

Após a rotulagem, reveja a expressão do exemplo e certifique-se de que o período de texto selecionado foi sublinhado com a entidade escolhida. A linha sólida indica que o texto foi rotulado.

> [!div class="mx-imgBorder"]
> ![Entidade completa de aprendizagem automática rotulada](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Confirmar entidade prevista

Se houver uma caixa pontilhada à volta do espaço de texto, indica que o texto está previsto, mas _ainda não está rotulado_. Para transformar a previsão num rótulo, selecione a linha de expressão e, em seguida, selecione **Confirmar entidades** da barra de ferramentas contextual.

## <a name="relabeling-over-existing-entities"></a>Rerotulagem sobre as entidades existentes

Se voltar a rotular o texto já rotulado, o LUIS pode dividir ou fundir os rótulos existentes.

## <a name="labeling-for-punctuation"></a>Rotulagem para pontuação

Não precisa de rotular para pontuação. Utilize _os formulários_ de palavras de definição de [aplicação](luis-reference-application-settings.md) para controlar se a pontuação tiver impacto nas previsões de expressão.

## <a name="unlabel-entities"></a>Entidades de suprimente

> [!NOTE]
> Só entidades aprendidas com máquinas podem ser desmarcadas.

Para desmarcar uma entidade, selecione a entidade e **selecione Unlabel** do menu no local.

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando entidade de não rotulagem](media/label-utterances/unlabel-entity-using-in-place-menu.png)

## <a name="automatic-labeling-for-parent-and-child-entities"></a>Rotulagem automática para entidades parentais e infantis

Se estiver a rotular uma entidade-mãe, qualquer subentidade que possa ser prevista com base na versão atualmente treinada, será rotulada.

Se estiver a rotular uma subentidade, o progenitor será rotulado automaticamente.

## <a name="automatic-labeling-for-non-machine-learned-entities"></a>Rotulagem automática para entidades não-automáticas aprendidas

Entidades não-automáticas incluem entidades pré-construídas, entidades de expressão regular, entidades de lista e padrões.quaisquer entidades. Estes são automaticamente rotulados por LUIS, pelo que não são obrigados a ser rotulados manualmente pelos utilizadores.

## <a name="entity-prediction-errors"></a>Erros de previsão da entidade

Erros de previsão da entidade indicam que a entidade prevista não corresponde à entidade rotulada. Isto é visualizado com um indicador de precaução ao lado da expressão.

> [!div class="mx-imgBorder"]
> ![Paleta de entidades para entidade de aprendizagem automática](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Passos seguintes

Utilize as [expressões finais](luis-how-to-review-endpoint-utterances.md) do [dashboard](luis-how-to-use-dashboard.md) e reveja as palavras finais para melhorar a qualidade de previsão da sua aplicação.