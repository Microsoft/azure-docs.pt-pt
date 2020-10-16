---
title: Expressão de exemplo de entidade de etiqueta
description: Aprenda a rotular uma entidade de aprendizagem automática com subentências numa expressão de exemplo numa página detalhada do portal LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: ffbaa2e40d5924ba61e548398e63295cf7dba2b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91303731"
---
# <a name="label-machine-learning-entity-in-an-example-utterance"></a>Entidade de aprendizagem automática de etiquetas numa expressão de exemplo

Rotular uma entidade num exemplo dá à LUIS um exemplo do que é a entidade e onde a entidade pode aparecer na expressão.

Pode rotular entidades e subentidades aprendidas com máquinas.

Como não é possível rotular a expressão regular, a lista ou as entidades pré-construídas, criar uma entidade ou sub-entidade, em seguida, adicionar estas entidades como funcionalidades, quando aplicável, à entidade ou sub-entidade.

## <a name="label-example-utterances-from-the-intent-detail-page"></a>Palavras de exemplo do rótulo da página de detalhes da Intenção

Para rotular exemplos de entidades dentro da expressão, selecione a intenção da expressão.

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. Selecione a Intenção que tem as palavras de exemplo que pretende rotular para extração com uma entidade.
1. Selecione o texto que pretende rotular e, em seguida, selecione a entidade.

## <a name="two-techniques-to-label-entities"></a>Duas técnicas para rotular entidades

Duas técnicas de rotulagem são suportadas na página de detalhes da Intenção.
* Selecione entidade ou sub-entidade a partir da Paleta de [Entidade](#label-with-the-entity-palette-visible) e, em seguida, selecione dentro do texto de expressão de exemplo. Esta é a técnica recomendada porque pode verificar visualmente que está a trabalhar com a entidade ou sub-entidade correta, de acordo com o seu esquema.
* Selecione dentro do texto de expressão de exemplo primeiro. Quando o fizeres, é apresentado um menu pop-up de escolhas de [rotulagem.](#how-to-label-entity-from-in-place-menu)

## <a name="label-with-the-entity-palette-visible"></a>Etiqueta com a Paleta de Entidade visível

Depois de [ter planeado o seu esquema com entidades,](luis-how-plan-your-app.md)mantenha a **paleta Entity** visível durante a rotulagem. A **paleta de entidades** é um lembrete das entidades que planeou extrair.

Para aceder à **Paleta de Entidades,** selecione o **@** símbolo na barra de ferramentas contextual acima da lista de palavras de exemplo.

> [!div class="mx-imgBorder"]
> ![Screenshot da paleta de entidades na página de detalhes do Intenção.](media/label-utterances/entity-palette-from-tool-bar.png)

## <a name="how-to-label-entity-from-entity-palette"></a>Como rotular a entidade da Paleta de Entidades

A paleta de entidades oferece uma alternativa à experiência de rotulagem anterior. Permite escovar o texto para rotulá-lo instantaneamente com uma entidade.

1. Abra a paleta de entidade selecionando no **@** símbolo no canto superior direito da mesa de expressão.

2. Selecione a entidade a partir da paleta que pretende rotular. Esta ação é indicada visualmente com um novo cursor. O cursor segue o rato à medida que se move no portal LUIS.

3. No exemplo, _pinte_ a entidade com o cursor.

    > [!div class="mx-imgBorder"]
    > ![A screenshot mostra a entidade pintada com o cursor.](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="adding-entity-as-a-feature-from-the-entity-palette"></a>Adicionar a entidade como recurso da Paleta de Entidade

A secção inferior da Paleta de Entidades permite-lhe adicionar funcionalidades à entidade atualmente selecionada. Pode selecionar de todas as entidades existentes e listas de frases ou criar uma nova lista de frases.

> [!div class="mx-imgBorder"]
> ![Screenshot da paleta de entidade com entidade como recurso](media/label-utterances/entity-palette-entity-as-a-feature.png)

## <a name="labeling-entity-roles"></a>Funções de entidade de rotulagem

As funções de entidade são rotuladas utilizando a **paleta Entity**.

1. Na página de detalhes intenção, selecione a **paleta Entity** a partir da barra de ferramentas de contexto.
1. Após a abertura da paleta Entity, selecione a entidade da lista de entidades.
1. Abaixo a lista de entidades, selecione uma função existente.
1. No texto de exemplo, rotular o texto com o papel da entidade.

## <a name="how-to-label-entity-from-in-place-menu"></a>Como rotular a entidade a partir do menu in-place

A rotulagem no local permite-lhe selecionar rapidamente o texto dentro da expressão e rotulá-lo. Também pode criar uma entidade de machine learning ou de listar a partir do texto rotulado.

Considere o exemplo de expressão, `hi, please I want a cheese pizza in 20 minutes` .

Selecione o texto mais à esquerda, em seguida, selecione o texto mais à direita da entidade, em seguida, no menu in-place, escolha a entidade com a quais pretende rotular.

> [!div class="mx-imgBorder"]
> ![Etiquetar entidade completa de aprendizagem automática](media/label-utterances/label-steps-in-place-menu.png)

## <a name="review-labeled-text"></a>Rever texto rotulado

Após a rotulagem, reveja a expressão de exemplo e certifique-se de que o período de texto selecionado foi sublinhado com a entidade escolhida. A linha sólida indica que o texto foi rotulado.

> [!div class="mx-imgBorder"]
> ![Entidade completa de aprendizagem automática rotulada](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Confirmar entidade prevista

Se houver uma caixa pontilhada em torno do período de texto, indica que o texto está previsto, mas _ainda não está rotulado_. Para transformar a previsão numa etiqueta, selecione a linha de expressão e, em seguida, **selecione Confirmar as entidades** da barra de ferramentas contextual.

## <a name="relabeling-over-existing-entities"></a>Remarcação sobre as entidades existentes

Se voltar a rotular textos já rotulados, o LUIS pode dividir ou fundir as etiquetas existentes.

## <a name="labeling-for-punctuation"></a>Rotulagem para pontuação

Não é preciso rotular para pontuação. Utilize [as definições de aplicações](luis-reference-application-settings.md) para controlar como a pontuação impacta as previsões de expressão.

## <a name="unlabel-entities"></a>Entidades unlabel

> [!NOTE]
> Só as entidades aprendidas com máquinas podem não ser identificadas. Não é possível rotular ou desmarcar entidades de expressão regular, entidades de lista ou entidades pré-construídas.

Para desemboar uma entidade, selecione a entidade e selecione **Unlabel** a partir do menu in-place.

> [!div class="mx-imgBorder"]
> ![Screenshot mostrando entidade de desmarcação](media/label-utterances/unlabel-entity-using-in-place-menu.png)

## <a name="automatic-labeling-for-parent-and-child-entities"></a>Rotulagem automática para entidades parentais e infantis

Se estiver a rotular uma entidade-mãe, qualquer subinidade que possa ser prevista com base na versão atualmente treinada, será rotulada.

Se estiver a rotular uma sub-entidade, o progenitor será rotulado automaticamente.

## <a name="automatic-labeling-for-non-machine-learned-entities"></a>Rotulagem automática para entidades não-máquinas aprendidas

As entidades não-machine aprendidas incluem entidades pré-construídas, entidades de expressão regular, entidades de lista e padrão.quaisquer entidades. Estes são automaticamente rotulados pela LUIS, pelo que não são obrigados a ser rotulados manualmente pelos utilizadores.

## <a name="intent-prediction-errors"></a>Erros de previsão de intenção

Um erro de previsão de intenção indica que o exemplo de expressão, dada a atual aplicação treinada, não seria previsto para a intenção.

Saiba como [ver estes erros](luis-how-to-add-intents.md#intent-prediction-errors) na página de detalhes da Intenção.

## <a name="entity-prediction-errors"></a>Erros de previsão de entidades

Erros de previsão de entidade indicam que a entidade prevista não corresponde à entidade rotulada. Isto é visualizado com um indicador de precaução ao lado da expressão.

> [!div class="mx-imgBorder"]
> ![Paleta de entidade para entidade de aprendizagem automática](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Passos seguintes

Use o [painel de instrumentos](luis-how-to-use-dashboard.md) e [reveja as expressões de ponto final](luis-how-to-review-endpoint-utterances.md) para melhorar a qualidade de previsão da sua aplicação.
