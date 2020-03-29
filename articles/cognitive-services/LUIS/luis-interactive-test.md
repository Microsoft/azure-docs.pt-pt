---
title: App de teste no portal LUIS
description: Use a Compreensão da Linguagem (LUIS) para trabalhar continuamente na sua aplicação para refiná-la e melhorar a sua compreensão linguística.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: ba7e23a72cd308dd4393bf9a581571e2bc9f5fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219821"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Teste a sua app LUIS no portal LUIS

[Testar](luis-concept-test.md) uma aplicação é um processo iterativo. Depois de treinar a sua app LUIS, teste-a com declarações de amostra para ver se as intenções e entidades são reconhecidas corretamente. Se não forem, faça atualizações para a app LUIS, treine e teste novamente.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Treinar antes dos testes

Para testar a versão mais recente da aplicação ativa, selecione **Train** a partir do menu superior, antes de testar.

## <a name="test-an-utterance"></a>Teste uma expressão

A expressão do teste não deve ser exatamente a mesma que qualquer exemplo pronuncia na aplicação. A expressão do teste deve incluir escolha de palavras, comprimento de frase e utilização da entidade que espera para um utilizador.

1. Aceda à sua aplicação selecionando o seu nome na página **My Apps.**

1. Para aceder ao painel de diapositivos do **Teste,** selecione **Test** no painel superior da sua aplicação.

    > [!div class="mx-imgBorder"]
    > ![Página de aplicativo de teste de & de comboio](./media/luis-how-to-interactive-test/test.png)

1. Introduza uma expressão na caixa de texto e selecione Enter. Pode escrever quantas declarações de teste quiser no **Teste,** mas apenas uma expressão de cada vez.

1. A expressão, a sua principal intenção e pontuação são adicionados à lista de expressões sob a caixa de texto.

    ![Testes interativos identificam a intenção errada](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Inspecionar pontuação

Inspecione os detalhes do resultado do teste no painel **de inspeção.**

1. Com o painel de diapositivos **do Teste** aberto, selecione **Inspecione-se** para obter uma expressão que queira comparar.

    ![Selecione Inspecione o botão para ver mais detalhes sobre os resultados dos testes](./media/luis-how-to-interactive-test/inspect.png)

1. O painel de **inspeção** aparece. O painel inclui a intenção de pontuação superior, bem como quaisquer entidades identificadas. O painel mostra o resultado da expressão selecionada.

    ![O painel inclui a intenção de pontuação superior, bem como quaisquer entidades identificadas. O painel mostra o resultado da expressão selecionada.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Correto a intenção de pontuação superior

1. Se a intenção de pontuação superior estiver incorreta, selecione o botão **Editar.**

1.  Na lista de entregas, selecione a intenção correta para a expressão.

    ![Selecione a intenção correta](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Ver resultados de sentimento

Se a **análise do Sentimento** estiver configurada na página **[Publicar,](luis-how-to-publish-app.md#enable-sentiment-analysis)** os resultados dos testes incluem o sentimento encontrado na expressão.

![Imagem de painel de teste com análise de sentimento](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Corretamente a intenção do padrão combinado

Se estiver a usar [Padrões](luis-concept-patterns.md) e a expressão corresponder a um padrão, mas a intenção errada foi prevista, selecione o link **Editar** pelo padrão e, em seguida, selecione a intenção correta.

## <a name="compare-with-published-version"></a>Compare com a versão publicada

Pode testar a versão ativa da sua aplicação com a versão [final](luis-glossary.md#endpoint) publicada. No painel **Inspecionar,** selecione **Compare com publicado**. Qualquer teste contra o modelo publicado é deduzido do seu saldo de quota de subscrição Azure.

![Comparar com publicado](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Ver ponto final JSON no painel de teste
Pode ver o ponto final jSON devolvido para a comparação selecionando a **vista Show JSON**.

![Resposta json publicada](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>Definições adicionais no painel de teste

### <a name="luis-endpoint"></a>Ponto final luis

Se tiver vários pontos finais LUIS, utilize a ligação **Definições Adicionais** no painel Publicado do Teste para alterar o ponto final utilizado para o teste. Se não tiver a certeza de qual ponto final utilizar, selecione o **Starter_Key**predefinido .

> [!div class="mx-imgBorder"]
> ![Painel de teste com ligação de definições adicionais realçado](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>Testes em lote
Consulte [os conceitos](luis-concept-batch-test.md) de teste de lote e aprenda [a](luis-how-to-batch-test.md) testar um lote de expressões.

## <a name="next-steps"></a>Passos seguintes

Se os testes indicarem que a sua aplicação LUIS não reconhece as intenções e entidades corretas, pode trabalhar para melhorar a precisão da sua app LUIS, rotulando mais expressões ou adicionando funcionalidades.

* [Rótulo sugerido expressões com LUIS](luis-how-to-review-endpoint-utterances.md)
* [Utilize funcionalidades para melhorar o desempenho da sua app LUIS](luis-how-to-add-features.md)
