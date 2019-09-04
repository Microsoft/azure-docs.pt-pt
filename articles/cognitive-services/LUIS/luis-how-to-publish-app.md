---
title: Publicar aplicativo-LUIS
titleSuffix: Azure Cognitive Services
description: Quando terminar de criar e testar a sua aplicação LUIS Active Directory, torná-lo disponível para a aplicação de cliente, publicá-la para o ponto final.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: f92776072038c5684e9334d2dda1690ebb7bcaa8
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257789"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publicar seu aplicativo ativo e treinado em um ponto de extremidade de preparo ou de produção

Quando terminar de criar e testar a sua aplicação LUIS Active Directory, torná-lo disponível para a aplicação de cliente, publicá-la para o ponto final. 

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>Publicação

1. Para publicar para o ponto final, selecione **publicar** na parte superior, com o botão direito painel. 

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

Por exemplo, para um aplicativo criado em [www.Luis.ai](https://www.luis.ai), se você criar um recurso de Luis em duas regiões , westuse lesteus, e adicioná-los ao aplicativo como recursos, o aplicativo será publicado em ambas as regiões. Para obter mais informações sobre as regiões de LUIS, consulte [regiões](luis-reference-regions.md).


## <a name="configuring-publish-settings"></a>Configurar definições de publicação

Depois de selecionar o slot, defina as configurações de publicação para:

* Análise de sentimentos
* Correção ortográfica
* Espriming de fala 

Depois de publicar, essas configurações estarão disponíveis para análise na página **gerenciar** configurações de **publicação** da seção. Você pode alterar as configurações com cada publicação. Se você cancelar uma publicação, as alterações feitas durante a publicação também serão canceladas. 

### <a name="when-your-app-is-published"></a>Quando seu aplicativo é publicado

Quando a aplicação é publicada com êxito, é apresentada uma notificação de êxito verde na parte superior do navegador. A barra de notificação verde também inclui uma ligação para os pontos de extremidade. 

![Publicando janela pop-up com o link para o ponto de extremidade](./media/luis-how-to-publish-app/publish-success.png)

Se precisar do URL de ponto de extremidade, selecione a ligação. Você também pode acessar as URLs do ponto de extremidade selecionando **gerenciar** no menu superior e, em seguida, selecione **recursos do Azure** no menu à esquerda. 

## <a name="sentiment-analysis"></a>Análise de sentimentos

<a name="enable-sentiment-analysis"></a>

Análise de sentimentos permite o LUIS integrar com o [análise de texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) para fornecer análise de expressão de sentimentos e a chave. 

Não é necessário fornecer uma chave de análise de texto e não sem custos de faturação para este serviço para a sua conta do Azure. 

Dados de sentimento são uma pontuação entre 1 e 0 indicando o positivo (mais de perto como 1) nem negativa (mais próximo de 0) sentimentos dos dados. O rótulo de sentimentos `positive`de `neutral`, e `negative` é por cultura com suporte. Atualmente, apenas o inglês dá suporte a rótulos de sentimentos. 

Para obter mais informações sobre a resposta do ponto final JSON com a análise de sentimentos, consulte [análise de sentimentos](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Correção ortográfica

As correções de ortografia são feitas antes da previsão de expressão do usuário LUIS. Você pode ver quaisquer alterações no expressão original, incluindo a ortografia, na resposta.

## <a name="speech-priming"></a>Espriming de fala

A desobstrução de fala é o processo de usar o envio do modelo LUIS para os serviços de fala antes da conversão de texto em fala. Isso permite que o serviço de fala forneça a conversão de fala com mais precisão para seu modelo. Isso permite o bot Speech e solicitações LUIS e respostas em uma chamada fazendo uma chamada de fala e voltando uma resposta LUIS. Ele fornece menos latência geral.

## <a name="next-steps"></a>Passos Seguintes

* Ver [gerir chaves](./luis-how-to-azure-subscription.md) adicionar chaves de subscrição do Azure chave para LUIS e como definir a verificação ortográfica do Bing da chave em inclua todas as intenções nos resultados.
* Ver [treinar e testar a aplicação](luis-interactive-test.md) para obter instruções sobre como testar a aplicação publicada na consola de teste.

