---
title: Aplicativo de teste no portal do LUIS
titleSuffix: Azure Cognitive Services
description: Use o Reconhecimento vocal (LUIS) para trabalhar continuamente em seu aplicativo para refiná-lo e melhorar a compreensão da linguagem.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: ebc86d1cf91cf79ab83b0f49d9898a91d8be8a75
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500286"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Testar seu aplicativo LUIS no portal do LUIS

[Testar](luis-concept-test.md) um aplicativo é um processo iterativo. Depois de treinar seu aplicativo LUIS, teste-o com o declarações de exemplo para ver se as intenções e as entidades são reconhecidas corretamente. Se não estiverem, faça atualizações no aplicativo LUIS, treine e teste novamente. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="test-an-utterance"></a>Testar um expressão

1. Acesse seu aplicativo selecionando seu nome na página **meus aplicativos** . 

1. Para acessar o painel de deslizamento de **teste** , selecione **teste** no painel superior do seu aplicativo.

    ![Página treinar & testar aplicativo](./media/luis-how-to-interactive-test/test.png)

1. Insira um expressão na caixa de texto e selecione Enter. Você pode digitar tantos declarações de teste quantos desejar no **teste**, mas apenas um expressão de cada vez.

1. O expressão, sua principal intenção e a pontuação são adicionados à lista de declarações na caixa de texto.

    ![O teste interativo identifica a intenção errada](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Inspecionar Pontuação

Você inspeciona detalhes do resultado do teste no painel **inspecionar** . 
 
1. Com o painel deslizante de **teste** aberto, selecione **inspecionar** para um expressão que você deseja comparar. 

    ![Selecione o botão inspecionar para ver mais detalhes sobre os resultados do teste](./media/luis-how-to-interactive-test/inspect.png)

1. O painel de **inspeção** é exibido. O painel inclui a principal intenção de pontuação, bem como quaisquer entidades identificadas. O painel mostra o resultado do expressão selecionado.

    ![O painel inclui a principal intenção de pontuação, bem como quaisquer entidades identificadas. O painel mostra o resultado do expressão selecionado.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Corrigir a principal tentativa de Pontuação

1. Se a principal tentativa de Pontuação estiver incorreta, selecione o botão **Editar** .

1.  Na lista suspensa, selecione a intenção correta para o expressão.

    ![Selecionar a intenção correta](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Exibir resultados de sentimentos

Se a **análise de sentimentos** estiver configurada na página **[publicar](luis-how-to-publish-app.md#enable-sentiment-analysis)** , os resultados do teste incluirão o sentimentos encontrado no expressão. 

![Imagem do painel de teste com análise de sentimentos](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Corrigir a intenção do padrão correspondente

Se você estiver usando [padrões](luis-concept-patterns.md) e o expressão corresponder a um padrão, mas a intenção errada foi prevista, selecione o link **Editar** pelo padrão e, em seguida, selecione a intenção correta.

## <a name="compare-with-published-version"></a>Comparar com versão publicada

Você pode testar a versão ativa do seu aplicativo com a versão do [ponto de extremidade](luis-glossary.md#endpoint) publicado. No painel **inspecionar** , selecione **comparar com publicado**. Qualquer teste no modelo publicado é deduzido do seu saldo de cota de assinatura do Azure. 

![Comparar com publicado](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Exibir JSON de ponto de extremidade no painel de teste
Você pode exibir o ponto de extremidade JSON retornado para a comparação selecionando a **exibição mostrar JSON**.

![Resposta JSON publicada](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Configurações adicionais no painel de teste

### <a name="luis-endpoint"></a>Ponto de extremidade LUIS

Se você tiver vários pontos de extremidade LUIS, use o link **configurações adicionais** no painel publicado do teste para alterar o ponto de extremidades usado para teste. Se você não tiver certeza de qual ponto de extremidade usar, selecione o **Starter_Key**padrão. 

![Painel de teste com configurações adicionais link realçado](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)


### <a name="view-bing-spell-check-corrections-in-test-panel"></a>Exibir Verificação Ortográfica do Bing correções no painel de teste

Requisitos para exibir as correções ortográficas: 

* Aplicativo publicado
* Verificação Ortográfica do Bing [chave de serviço](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api). A chave de serviço não é armazenada e precisa ser redefinida para cada sessão do navegador. 

Use o procedimento a seguir para incluir o serviço de [verificação ortográfica do Bing v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) nos resultados do painel de teste. 

1. No painel de **teste** , insira um expressão. Quando o expressão for previsto, selecione **[inspecionar](#inspect-score)** abaixo do expressão que você inseriu. 

1. Quando o painel **inspecionar** for aberto, selecione **[comparar com publicado](#compare-with-published-version)** . 

1. Quando o painel **publicado** for aberto, selecione **[configurações adicionais](#additional-settings-in-test-panel)** .

1. Na caixa de diálogo pop-up, marque **habilitar verificação ortográfica do Bing** e insira a chave e, em seguida, selecione **concluído**. 
    ![inserir Verificação Ortográfica do Bing chave de serviço](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key-text.png)

1. Insira uma consulta com uma grafia incorreta, como `book flite to seattle` e selecione Enter. A grafia incorreta da palavra `flite` é substituída na consulta enviada para LUIS e o JSON resultante mostra a consulta original, como `query`e a grafia corrigida na consulta, como `alteredQuery`.

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

## <a name="batch-testing"></a>Testes em lote
Consulte [conceitos](luis-concept-batch-test.md) de teste em lote e saiba [como](luis-how-to-batch-test.md) testar um lote de declarações.

## <a name="next-steps"></a>Passos seguintes

Se o teste indicar que seu aplicativo LUIS não reconhece as intenções e as entidades corretas, você poderá trabalhar para melhorar a precisão do seu aplicativo LUIS ao rotular mais declarações ou adicionar recursos. 

* [Rótulo sugerido declarações com LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Use os recursos para melhorar o desempenho do aplicativo LUIS](luis-how-to-add-features.md) 
