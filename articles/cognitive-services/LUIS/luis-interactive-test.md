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
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 5736667acfa510d89e549afd4d0f5c61a833dd1c
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932739"
---
<a name="train-your-app"></a>

# <a name="test-your-luis-app-in-the-luis-portal"></a>Testar seu aplicativo LUIS no portal do LUIS

[Testar](luis-concept-test.md) um aplicativo é um processo iterativo. Após a sua aplicação LUIS de treinamento, teste-o com expressões de exemplo para ver se as intenções e entidades são reconhecidas corretamente. Se não estiverem, fazer atualizações para a aplicação LUIS, formação e teste novamente. 

<!-- anchors for H2 name changes -->
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="test-an-utterance"></a>Uma expressão de teste

1. Aceder à sua aplicação, selecionando o respetivo nome na **as minhas aplicações** página. 

1. Para aceder a **teste** deslizantes painel, selecione **teste** no painel superior do seu aplicativo.

    ![Treinar e testar a aplicação de página](./media/luis-how-to-interactive-test/test.png)

1. Introduza uma expressão na caixa de texto e selecione Enter. Pode digitar como expressões de teste que quiser a **testar**, mas apenas uma expressão de cada vez.

1. A expressão, sua intenção superior e pontuação é adicionada à lista de expressões em caixa de texto.

    ![O teste interativo identifica a intenção de errado](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Inspecionar a pontuação

Inspecionar os detalhes do resultado do teste nos **Inspect** painel. 
 
1. Com o **teste** deslizantes painel aberto, selecione **Inspect** para uma expressão que pretende comparar. 

    ![Selecione o botão inspecionar para ver mais detalhes sobre os resultados do teste](./media/luis-how-to-interactive-test/inspect.png)

1. O **inspeção** painel é apresentada. O painel inclui a parte superior de classificação intenção, bem como qualquer entidades identificadas. O painel mostra o resultado da expressão selecionada.

    ![O painel inclui a parte superior de classificação intenção, bem como qualquer entidades identificadas. O painel mostra o resultado da expressão selecionada.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Parte superior correto intenção de classificação

1. Se a parte superior a intenção de classificação estiver incorreto, selecione o **editar** botão.

1.  Na lista pendente, selecione a intenção correta para a expressão.

    ![Selecione a intenção correta](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Ver resultados de sentimentos

Se **a análise de sentimentos** está configurado no **[publicar](luis-how-to-publish-app.md#enable-sentiment-analysis)** página, os resultados do teste incluem o sentimento encontrado na expressão. 

![Imagem do painel de teste com a análise de sentimentos](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Corrija a intenção de padrão correspondente

Se estiver a utilizar [padrões](luis-concept-patterns.md) e a expressão correspondido um padrão, mas foi prever a intenção de errado, selecione a **editar** ligue, o padrão, em seguida, selecione a intenção correta.

## <a name="compare-with-published-version"></a>Comparar com a versão publicada

Pode testar a versão do Active Directory da sua aplicação com o publicados [ponto final](luis-glossary.md#endpoint) versão. Na **Inspect** painel, selecione **Compare com publicado**. Qualquer teste com base no modelo publicado é deduzido do saldo da sua quota de subscrição do Azure. 

![Compare com publicado](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Ver o ponto final JSON no painel de teste
Pode ver o ponto final JSON devolvido para a comparação, selecionando o **vista de mostrar JSON**.

![Resposta JSON publicada](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Definições adicionais no painel de teste

### <a name="luis-endpoint"></a>Ponto final de LUIS

Se tiver vários pontos de extremidade do LUIS, utilize o **definições adicionais** link o teste de publicação do painel para alterar o ponto final utilizado para fins de teste. Se não tiver a certeza de qual ponto final a utilizar, selecione a predefinição **Starter_Key**. 

![Painel de teste com a ligação de definições adicionais realçada](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)


### <a name="view-bing-spell-check-corrections-in-test-panel"></a>Ver as correções de verificação ortográfica do Bing no painel de teste

Requisitos para ver as correções de ortografia: 

* Aplicação publicada
* Verificação ortográfica do Bing [chave de serviço](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api). A chave de serviço não é armazenada e tem de ser reposto para cada sessão de browser. 

Utilize o procedimento seguinte para incluir o [verificação ortográfica do Bing v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) serviço nos resultados de painel de teste. 

1. Na **teste** painel, introduza uma expressão. Quando a expressão é prevista, selecione **[Inspect](#inspect-score)** por baixo da expressão que introduziu. 

1. Quando o **Inspect** painel se abrir, selecione  **[comparar com publicado](#compare-with-published-version)** . 

1. Quando o **publicada** painel se abrir, selecione  **[definições adicionais](#additional-settings-in-test-panel)** .

1. Na caixa de diálogo pop-up, marque **habilitar verificação ortográfica do Bing** e insira a chave e, em seguida, selecione **concluído**. 
    ![Introduza a chave do serviço de verificação ortográfica do Bing](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key-text.png)

1. Introduza uma consulta com um incorreto de ortografia como `book flite to seattle` e selecione introduzir. A ortografia incorreta da palavra `flite` é substituído na consulta enviada para o LUIS e o JSON resultante mostra ambas as a consulta original, como `query`e a ortografia corrigida na consulta, como `alteredQuery`.

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
Consulte o teste do batch [conceitos](luis-concept-batch-test.md) e Aprenda [como](luis-how-to-batch-test.md) testar um lote de expressões.

## <a name="next-steps"></a>Passos Seguintes

Se o teste indica que a sua aplicação LUIS não reconhece o corretas intenções e entidades, pode trabalhar para melhorar a precisão da sua aplicação LUIS, expressões com mais de etiquetagem ou adicionar recursos. 

* [Identifique expressões sugeridas com os LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Utilizar as funcionalidades para melhorar o desempenho da sua aplicação LUIS](luis-how-to-add-features.md) 
