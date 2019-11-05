---
title: Publicar aplicativo-LUIS
titleSuffix: Azure Cognitive Services
description: Quando você terminar de criar e testar seu aplicativo LUIS ativo, disponibilize-o para o aplicativo cliente publicando-o no ponto de extremidade.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 0d3a413249cb9058e4098f2836131494670a1727
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491335"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publicar seu aplicativo ativo e treinado em um ponto de extremidade de preparo ou de produção

Quando você terminar de criar e testar seu aplicativo LUIS ativo, disponibilize-o para o aplicativo cliente publicando-o no ponto de extremidade. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>Publicação

1. Para publicar no ponto de extremidade, selecione **publicar** na parte superior, painel direito. 

    ![Botão publicar na parte superior, barra de navegação direita](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Selecione as configurações para o ponto de extremidade de previsão publicado e, em seguida, selecione **publicar**.

    ![Selecione Publicar configurações e, em seguida, clique no botão publicar](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Slots de publicação

Selecione o slot correto quando a janela pop-up for exibida: 

* Preparo.
* Produção. 

Usando os dois slots de publicação, isso permite que você tenha duas versões diferentes do seu aplicativo disponíveis nos pontos de extremidade publicados ou na mesma versão em dois pontos de extremidade diferentes. 

### <a name="publishing-regions"></a>Regiões de publicação

O aplicativo é publicado em todas as regiões associadas aos recursos de ponto de extremidade de previsão LUIS adicionados no portal do LUIS. 

Por exemplo, para um aplicativo criado em [www.Luis.ai](https://www.luis.ai), se você criar um recurso de Luis em duas regiões, **westus** e **lesteus**, e adicioná-los ao aplicativo como recursos, o aplicativo será publicado em ambas as regiões. Para obter mais informações sobre regiões LUIS, consulte [regiões](luis-reference-regions.md).


## <a name="configuring-publish-settings"></a>Definindo configurações de publicação

Depois de selecionar o slot, defina as configurações de publicação para:

* Análise de sentimentos
* Correção ortográfica
* Espriming de fala 

Depois de publicar, essas configurações estarão disponíveis para análise na página **gerenciar** configurações de **publicação** da seção. Você pode alterar as configurações com cada publicação. Se você cancelar uma publicação, as alterações feitas durante a publicação também serão canceladas. 

### <a name="when-your-app-is-published"></a>Quando seu aplicativo é publicado

Quando seu aplicativo for publicado com êxito, uma notificação de êxito verde aparecerá na parte superior do navegador. A barra de notificação verde também inclui um link para os pontos de extremidade. 

![Publicando janela pop-up com o link para o ponto de extremidade](./media/luis-how-to-publish-app/publish-success.png)

Se você precisar da URL do ponto de extremidade, selecione o link. Você também pode acessar as URLs do ponto de extremidade selecionando **gerenciar** no menu superior e, em seguida, selecione **recursos do Azure** no menu à esquerda. 

## <a name="sentiment-analysis"></a>Análise de sentimentos

<a name="enable-sentiment-analysis"></a>

A análise de sentimentos permite que o LUIS se integre com [análise de texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) para fornecer opiniões e análise de frases-chave. 

Você não precisa fornecer uma chave de Análise de Texto e não há cobrança para esse serviço em sua conta do Azure. 

Os dados de sentimentos são uma pontuação entre 1 e 0 que indica a diferenciação positiva (mais de 1) ou negativa (mais de 0) dos dados. O rótulo de sentimentos de `positive`, `neutral`e `negative` é por cultura com suporte. Atualmente, apenas o inglês dá suporte a rótulos de sentimentos. 

Para obter mais informações sobre a resposta de ponto de extremidade JSON com análise de sentimentos, consulte [análise de sentimentos](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Correção ortográfica

As correções de ortografia são feitas antes da previsão de expressão do usuário LUIS. Você pode ver quaisquer alterações no expressão original, incluindo a ortografia, na resposta.

## <a name="speech-priming"></a>Espriming de fala

A desobstrução de fala é o processo de usar o envio do modelo LUIS para os serviços de fala antes da conversão de texto em fala. Isso permite que o serviço de fala forneça a conversão de fala com mais precisão para seu modelo. Isso permite o bot Speech e solicitações LUIS e respostas em uma chamada fazendo uma chamada de fala e voltando uma resposta LUIS. Ele fornece menos latência geral.

## <a name="next-steps"></a>Passos seguintes

* Consulte [gerenciar chaves](./luis-how-to-azure-subscription.md) para adicionar chaves à chave de assinatura do Azure para Luis e como definir a chave de verificação ortográfica do Bing e incluir todas as intenções nos resultados.
* Consulte [treinar e testar seu aplicativo](luis-interactive-test.md) para obter instruções sobre como testar seu aplicativo publicado no console de teste.

