---
title: App de teste no portal LUIS
description: Use a Compreensão linguística (LUIS) para trabalhar continuamente na sua aplicação para refiná-la e melhorar a sua compreensão linguística.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 31885eba16d59e2e48a08f84c56271b84e6c565f
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790923"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Teste a sua app LUIS no portal LUIS

[Testar](luis-concept-test.md) uma aplicação é um processo iterativo. Depois de treinar a sua app LUIS, teste-a com expressões de amostra para ver se as intenções e entidades são reconhecidas corretamente. Se não estiverem, faça atualizações para a app LUIS, treine e teste novamente.

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Treinar antes de testar

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. Para testar contra a versão mais recente da aplicação ativa, selecione **Train** a partir do menu superior, antes de testar.

## <a name="test-an-utterance"></a>Teste uma expressão

A expressão de teste não deve ser exatamente a mesma que qualquer palavra de exemplo na app. A expressão de teste deve incluir escolha de palavras, comprimento de frase e utilização de entidade que espera para um utilizador.

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**

1. Para aceder ao painel de desapoia do **Teste,** selecione **Teste** no painel superior da sua aplicação.

    > [!div class="mx-imgBorder"]
    > ![Página de aplicativo de teste de & de comboio](./media/luis-how-to-interactive-test/test.png)

1. Introduza uma expressão na caixa de texto e selecione Enter. Pode escrever quantas palavras de teste quiser no **Teste,** mas apenas uma expressão de cada vez.

1. A expressão, a sua intenção e pontuação são adicionados à lista de expressões sob a caixa de texto.

    > [!div class="mx-imgBorder"]
    > ![Testes interativos identificam a intenção errada](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-the-prediction"></a>Inspecione a previsão

Inspecione os detalhes do resultado do teste no painel **De inspecione.**

1. Com o painel de deslizamento de **teste** aberto, selecione **Inspecione** para obter uma expressão que pretende comparar.

    > [!div class="mx-imgBorder"]
    > ![Selecione Inspecione o botão para ver mais detalhes sobre os resultados dos testes](./media/luis-how-to-interactive-test/inspect.png)

1. O painel **de inspeção** aparece. O painel inclui a intenção de pontuação superior, bem como quaisquer entidades identificadas. O painel mostra a previsão da expressão selecionada.

    > [!div class="mx-imgBorder"]
    > ![Screenshot parcial do painel de inspeção de teste](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="add-to-example-utterances"></a>Adicione a palavras de exemplo

A partir do painel de inspeção, pode adicionar a expressão de teste a uma intenção selecionando **Adicionar a palavras de exemplo**.

## <a name="disable-required-features"></a>Desativar as funcionalidades necessárias

Este alternar ajuda-o a determinar se a aplicação treinada está a prever corretamente as suas entidades com base nas funcionalidades necessárias. A definição predefinida é aplicar a função conforme necessário durante a previsão. Selecione este toggle para ver qual seria a previsão se a funcionalidade da sub-entidade não fosse necessária.

### <a name="when-to-disable-required-features"></a>Quando desativar as funcionalidades necessárias

A aplicação treinada pode prescrever mal uma entidade aprendida com base numa das seguintes:
* Rotulagem incorreta de palavras de exemplo.
* A função requerida não corresponde ao texto.

Um exemplo é uma entidade aprendida com uma sub-entidade com o nome de uma pessoa.

:::image type="content" source="media/luis-how-to-interactive-test/disable-required-feature.png" alt-text="Screenshot do esquema de entidade aprendida com máquina do portal LUIS com recurso necessário":::

Um exemplo de expressão para esta entidade aprendida com máquinas é: `Assign Bob Jones to work on the new security feature` .

A extração deve ser `security feature` como descrição do bilhete e `Bob Jones` como engenheiro, duas subentidades de `Assign ticket` entidade.

Para ajudar a subagúnera a prever com sucesso, adicione à entidade pré-construída [PersonName](luis-reference-prebuilt-person.md) aa uma característica à `engineer` sub-entidade. Se fizer a funcionalidade necessária, isso significa que a sub-entidade só será extraída se a entidade pré-incorporada personName estiver prevista para o texto. Isto significa que qualquer nome no texto que não preveja com a sub-entidade PersonName, não será devolvido como uma sub-entidade rotulada, `engineer` .

Quando utiliza o painel de teste interativo, e vê uma sub-entidade, com uma função necessária, não está a prever, alternar esta definição, para ver se a sub-entidade seria prevista sem que a funcionalidade fosse necessária. A sub-entidade pode ser capaz de ser corretamente prevista sem a funcionalidade necessária devido à correta rotulagem de palavras de exemplo.

## <a name="view-sentiment-results"></a>Ver resultados de sentimento

Se **a análise do sentimento** estiver configurada na página **[publicar,](luis-how-to-publish-app.md#enable-sentiment-analysis)** os resultados dos testes incluem o sentimento encontrado na expressão.

## <a name="correct-matched-patterns-intent"></a>A intenção do padrão correto combina

Se estiver a usar [Padrões](luis-concept-patterns.md) e a expressão corresponder a um padrão, mas a intenção errada foi prevista, selecione a ligação **Editar** pelo padrão e, em seguida, selecione a intenção correta.

## <a name="compare-with-published-version"></a>Compare com versão publicada

Pode testar a versão ativa da sua aplicação com a versão [de ponto final](luis-glossary.md#endpoint) publicada. No painel **De inspeção,** selecione **Compare com a publicação**. Qualquer teste contra o modelo publicado é deduzido do saldo da sua quota de subscrição Azure.

> [!div class="mx-imgBorder"]
> ![Compare com publicado](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Ver ponto final JSON no painel de teste
Pode ver o ponto final que json devolveu para a comparação selecionando a **vista Show JSON**.

> [!div class="mx-imgBorder"]
> ![Resposta JSON publicada](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>Definições adicionais no painel de teste

### <a name="luis-endpoint"></a>Ponto final DO LUIS

Se tiver vários pontos finais LUIS, utilize o link **Definições Adicionais** no painel publicado do Teste para alterar o ponto final utilizado para os testes. Se não tiver a certeza de qual o ponto final a utilizar, selecione o **Starter_Key** predefinido .

> [!div class="mx-imgBorder"]
> ![Painel de teste com ligação de Definições Adicionais realçado](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>Testes em lote
Consulte [conceitos de](./luis-how-to-batch-test.md) teste de lote e [aprenda a](luis-how-to-batch-test.md) testar um lote de expressões.

## <a name="next-steps"></a>Próximos passos

Se os testes indicarem que a sua aplicação LUIS não reconhece as intenções e entidades corretas, pode trabalhar para melhorar a precisão da sua app LUIS, rotulando mais expressões ou adicionando funcionalidades.

* [Rótulo sugeriu declarações com LUIS](luis-how-to-review-endpoint-utterances.md)
* [Utilize funcionalidades para melhorar o desempenho da sua app LUIS](luis-how-to-add-features.md)