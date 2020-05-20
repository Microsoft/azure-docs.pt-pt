---
title: App de teste no portal LUIS
description: Use a Compreensão da Linguagem (LUIS) para trabalhar continuamente na sua aplicação para refiná-la e melhorar a sua compreensão linguística.
ms.topic: conceptual
ms.date: 05/17/2020
ms.openlocfilehash: 3608bb17b6d1ea8c9095fa54324993a9e40355b6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653885"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Teste a sua app LUIS no portal LUIS

[Testar](luis-concept-test.md) uma aplicação é um processo iterativo. Depois de treinar a sua app LUIS, teste-a com declarações de amostra para ver se as intenções e entidades são reconhecidas corretamente. Se não forem, faça atualizações para a app LUIS, treine e teste novamente.

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Treinar antes dos testes

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **De Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. Para testar a versão mais recente da aplicação ativa, selecione **Train** a partir do menu superior, antes de testar.

## <a name="test-an-utterance"></a>Teste uma expressão

A expressão do teste não deve ser exatamente a mesma que qualquer exemplo pronuncia na aplicação. A expressão do teste deve incluir escolha de palavras, comprimento de frase e utilização da entidade que espera para um utilizador.

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **De Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**

1. Para aceder ao painel de diapositivos do **Teste,** selecione **Test** no painel superior da sua aplicação.

    > [!div class="mx-imgBorder"]
    > ![Página de aplicativo de teste de & de comboio](./media/luis-how-to-interactive-test/test.png)

1. Introduza uma expressão na caixa de texto e selecione Enter. Pode escrever quantas declarações de teste quiser no **Teste,** mas apenas uma expressão de cada vez.

1. A expressão, a sua principal intenção e pontuação são adicionados à lista de expressões sob a caixa de texto.

    ![Testes interativos identificam a intenção errada](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-the-prediction"></a>Inspecione a previsão

Inspecione os detalhes do resultado do teste no painel **de inspeção.**

1. Com o painel de diapositivos **do Teste** aberto, selecione **Inspecione-se** para obter uma expressão que queira comparar.

    ![Selecione Inspecione o botão para ver mais detalhes sobre os resultados dos testes](./media/luis-how-to-interactive-test/inspect.png)

1. O painel de **inspeção** aparece. O painel inclui a intenção de pontuação superior, bem como quaisquer entidades identificadas. O painel mostra a previsão da expressão selecionada.

## <a name="add-to-example-utterances"></a>Adicione a declarações de exemplo

A partir do painel de inspeção, pode adicionar a expressão do teste a uma intenção selecionando **Adicionar a declarações exemplo .**

## <a name="disable-required-features"></a>Desativar as funcionalidades necessárias

Selecione este alternância para ver qual seria a previsão se a funcionalidade da entidade não fosse necessária.

Este alternância ajuda-o a determinar se a aplicação treinada está a prever corretamente as suas entidades com base nas funcionalidades necessárias. A aplicação treinada pode prever mal uma entidade aprendida com base na rotulagem incorreta de declarações de exemplo ou na funcionalidade necessária não corresponde ao texto.

## <a name="view-sentiment-results"></a>Ver resultados de sentimento

Se a **análise do Sentimento** estiver configurada na página **[Publicar,](luis-how-to-publish-app.md#enable-sentiment-analysis)** os resultados dos testes incluem o sentimento encontrado na expressão.

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
