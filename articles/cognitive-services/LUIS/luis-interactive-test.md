---
title: Testar a aplicação no portal do LUIS
titleSuffix: Azure Cognitive Services
description: Utilize a compreensão de idiomas (LUIS) para trabalhar continuamente na sua aplicação para refiná-la e melhorar a compreensão de idiomas.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 628547e8254bb0055cf1f09af50e79b68311a759
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221746"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Testar seu aplicativo LUIS no portal do LUIS

[Testar](luis-concept-test.md) um aplicativo é um processo iterativo. Após a sua aplicação LUIS de treinamento, teste-o com expressões de exemplo para ver se as intenções e entidades são reconhecidas corretamente. Se não estiverem, fazer atualizações para a aplicação LUIS, formação e teste novamente. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Treinar antes do teste

Para testar a versão mais recente do aplicativo ativo, selecione **treinar** no menu superior, antes de testar. 

## <a name="test-an-utterance"></a>Uma expressão de teste

O expressão de teste não deve ser exatamente o mesmo que qualquer declarações de exemplo no aplicativo. O expressão de teste deve incluir a opção de palavra, o comprimento da frase e o uso da entidade que você espera para um usuário. 

1. Acesse seu aplicativo selecionando seu nome na página **meus aplicativos** . 

1. Para acessar o painel de deslizamento de **teste** , selecione **teste** no painel superior do seu aplicativo.

    > [!div class="mx-imgBorder"]
    > página do aplicativo ![Train & Test](./media/luis-how-to-interactive-test/test.png)

1. Introduza uma expressão na caixa de texto e selecione Enter. Você pode digitar tantos declarações de teste quantos desejar no **teste**, mas apenas um expressão de cada vez.

1. A expressão, sua intenção superior e pontuação é adicionada à lista de expressões em caixa de texto.

    ![O teste interativo identifica a intenção de errado](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Inspecionar a pontuação

Você inspeciona detalhes do resultado do teste no painel **inspecionar** . 
 
1. Com o painel deslizante de **teste** aberto, selecione **inspecionar** para um expressão que você deseja comparar. 

    ![Selecione o botão inspecionar para ver mais detalhes sobre os resultados do teste](./media/luis-how-to-interactive-test/inspect.png)

1. O painel de **inspeção** é exibido. O painel inclui a parte superior de classificação intenção, bem como qualquer entidades identificadas. O painel mostra o resultado da expressão selecionada.

    ![O painel inclui a parte superior de classificação intenção, bem como qualquer entidades identificadas. O painel mostra o resultado da expressão selecionada.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Parte superior correto intenção de classificação

1. Se a principal tentativa de Pontuação estiver incorreta, selecione o botão **Editar** .

1.  Na lista pendente, selecione a intenção correta para a expressão.

    ![Selecione a intenção correta](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Ver resultados de sentimentos

Se a **análise de sentimentos** estiver configurada na página **[publicar](luis-how-to-publish-app.md#enable-sentiment-analysis)** , os resultados do teste incluirão o sentimentos encontrado no expressão. 

![Imagem do painel de teste com a análise de sentimentos](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Corrija a intenção de padrão correspondente

Se você estiver usando [padrões](luis-concept-patterns.md) e o expressão corresponder a um padrão, mas a intenção errada foi prevista, selecione o link **Editar** pelo padrão e, em seguida, selecione a intenção correta.

## <a name="compare-with-published-version"></a>Comparar com a versão publicada

Você pode testar a versão ativa do seu aplicativo com a versão do [ponto de extremidade](luis-glossary.md#endpoint) publicado. No painel **inspecionar** , selecione **comparar com publicado**. Qualquer teste com base no modelo publicado é deduzido do saldo da sua quota de subscrição do Azure. 

![Compare com publicado](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Ver o ponto final JSON no painel de teste
Você pode exibir o ponto de extremidade JSON retornado para a comparação selecionando a **exibição mostrar JSON**.

![Resposta JSON publicada](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Definições adicionais no painel de teste

### <a name="luis-endpoint"></a>Ponto final de LUIS

Se você tiver vários pontos de extremidade LUIS, use o link **configurações adicionais** no painel publicado do teste para alterar o ponto de extremidades usado para teste. Se você não tiver certeza de qual ponto de extremidade usar, selecione o **Starter_Key**padrão. 

> [!div class="mx-imgBorder"]
> ![painel de teste com configurações adicionais link realçado](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)

<!--
###  View Bing Spell Check corrections in test panel

Requirements to view the spelling corrections: 

* Published app
* Bing Spell Check [service key](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api). The service key is not stored and needs to be reset for each browser session. 

Use the following procedure to include the [Bing Spell Check v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) service  in the Test pane results. 

1. In the **Test** pane, enter an utterance. When the utterance is predicted, select **[Inspect](#inspect-score)** underneath the utterance you entered. 

1. When the **Inspect** panel opens, select **[Compare with Published](#compare-with-published-version)**. 

1. When the **Published** panel opens, select **[Additional Settings](#additional-settings-in-test-panel)**.

1. In the pop-up dialog, check **Enable Bing Spell Check** and enter the key, then select **Done**. 
    ![Enter Bing Spell Check service key](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key-text.png)

1. Enter a query with an incorrect spelling such as `book flite to seattle` and select enter. The incorrect spelling of the word `flite` is replaced in the query sent to LUIS and the resulting JSON shows both the original query, as `query`, and the corrected spelling in the query, as `alteredQuery`.

<a name="json-file-with-no-duplicates"></a>
<a name="import-a-dataset-file-for-batch-testing"></a>
<a name="export-rename-delete-or-download-dataset"></a>
<a name="run-a-batch-test-on-your-trained-app"></a>
<a name="access-batch-test-result-details-in-a-visualized-view"></a>
<a name="filter-chart-results-by-intent-or-entity"></a>
<a name="investigate-false-sections"></a>
<a name="view single-point utterance data"></a>
<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>
-->

## <a name="batch-testing"></a>Testes em lote
Consulte [conceitos](luis-concept-batch-test.md) de teste em lote e saiba [como](luis-how-to-batch-test.md) testar um lote de declarações.

## <a name="next-steps"></a>Passos seguintes

Se o teste indica que a sua aplicação LUIS não reconhece o corretas intenções e entidades, pode trabalhar para melhorar a precisão da sua aplicação LUIS, expressões com mais de etiquetagem ou adicionar recursos. 

* [Rótulo sugerido declarações com LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Use os recursos para melhorar o desempenho do aplicativo LUIS](luis-how-to-add-features.md) 
