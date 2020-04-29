---
title: Publicar app - LUIS
titleSuffix: Azure Cognitive Services
description: Quando terminar de construir e testar a sua aplicação ATIVE LUIS, disponibilize-a à sua aplicação de cliente publicando-a no ponto final.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: c91a3ca73d70dd5fd2848bed0f43f14a817087d7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80053442"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publique a sua app ativa e treinada para um ponto final de encenação ou produção

Quando terminar de construir, treinar e testar a sua aplicação ATIVE LUIS, disponibilize-a à sua aplicação de cliente publicando-a no ponto final. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="publishing"></a>Publicar

1. Para publicar no ponto final, **selecione Publicar** no painel superior, direito. 

    ![Publique botão em cima, barra de navegação direita](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Selecione as definições para o ponto final de previsão publicado e, em seguida, **selecione Publicar**.

    ![Selecione publicar definições e, em seguida, selecione o botão Publicar](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Slots de publicação

Selecione a ranhura correta quando a janela pop-up aparecer: 

* Teste
* Produção 

Ao utilizar ambas as ranhuras de publicação, isto permite-lhe ter duas versões diferentes da sua aplicação disponíveis nos pontos finais publicados ou a mesma versão em dois pontos finais diferentes. 

### <a name="publishing-regions"></a>Regiões editoriais

A aplicação é publicada em todas as regiões associadas aos recursos finais de previsão do LUIS adicionados no portal LUIS a partir da página **Gerir** -> **[Recursos Azure.](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** 

Por exemplo, para uma aplicação criada na [www.luis.ai](https://www.luis.ai), se criar um recurso LUIS em duas regiões, **westus** e **eastus**- e adicioná-las à app como recursos, a app é publicada em ambas as regiões. Para mais informações sobre as regiões do LUIS, consulte [Regiões.](luis-reference-regions.md)

> [!TIP]
> Existem 3 regiões autorais. Deve ser autor na região a que pretende publicar. Se precisa de publicar em todas as regiões, precisa de gerir o seu processo de autoria e o modelo treinado resultante em todas as 3 regiões autorais. 


## <a name="configuring-publish-settings"></a>Configurar as definições de publicação

Depois de selecionar a ranhura, configure as definições de publicação para:

* Análise de sentimentos
* Correção ortográfica - ponto final de previsão v2 apenas
* Preparação do discurso 

Depois de publicar, estas definições estão disponíveis para revisão a partir da página de **definições** da secção **Gerir.** Pode alterar as definições com cada publicação. Se cancelar uma publicação, as alterações que efizemos durante a publicação também são canceladas. 

### <a name="when-your-app-is-published"></a>Quando a sua aplicação é publicada

Quando a sua aplicação é publicada com sucesso, uma notificação de sucesso aparece no topo do navegador. A notificação também inclui um link para os pontos finais. 

Se precisar do URL do ponto final, selecione o link. Também pode chegar aos URLs de ponto final selecionando **Gerir** no menu superior e, em seguida, selecione **Os Recursos Azure** no menu esquerdo. 

## <a name="sentiment-analysis"></a>Análise de sentimentos

<a name="enable-sentiment-analysis"></a>

A análise de sentimento permite que a LUIS se integre com o [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) para fornecer sentimento e análise de frases-chave. 

Não tem de fornecer uma chave Text Analytics e não existe qualquer taxa de faturação para este serviço na sua conta Azure. 

Os dados de sentimento são uma pontuação entre 1 e 0 indicando o sentimento positivo (mais próximo de 1) ou negativo (mais próximo de 0) dos dados. O rótulo `positive`de `neutral`sentimento `negative` de , e é por cultura apoiada. Atualmente, apenas o inglês suporta rótulos de sentimento. 

Para mais informações sobre a resposta do ponto final da JSON com análise de sentimento, consulte [a análise](luis-concept-data-extraction.md#sentiment-analysis) do sentimento

## <a name="spelling-correction"></a>Correção ortográfica

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

As correções à ortografia são feitas antes da previsão de expressão do utilizador do LUIS. Pode ver quaisquer alterações na expressão original, incluindo ortografia, na resposta.

## <a name="speech-priming"></a>Preparação do discurso

A preparação da fala é o processo de utilização do modelo LUIS para os serviços de Fala antes da conversão do texto para a fala. Isto permite que o serviço de fala forneça a conversão da fala com mais precisão para o seu modelo. Isto permite aos pedidos e respostas do bot Speech e do LUIS num apelo, fazendo uma chamada de discurso e recebendo de volta uma resposta do LUIS. Proporciona menos latência em geral.

## <a name="next-steps"></a>Passos seguintes

* Consulte [As teclas 'Gerir'](./luis-how-to-azure-subscription.md) as chaves para adicionar chaves à chave de subscrição Azure ao LUIS e como definir a tecla Bing Spell Check e incluir todas as intenções nos resultados.
* Consulte [o Train e teste a sua aplicação](luis-interactive-test.md) para obter instruções sobre como testar a sua aplicação publicada na consola de teste.

