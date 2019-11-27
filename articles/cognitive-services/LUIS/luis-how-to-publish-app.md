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
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: f213c1d43930075c78cf81de345f612e46bbfb1c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221709"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publicar seu aplicativo ativo e treinado em um ponto de extremidade de preparo ou de produção

Quando você terminar de criar, treinar e testar seu aplicativo LUIS ativo, disponibilize-o para o aplicativo cliente publicando-o no ponto de extremidade. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="publishing"></a>Publicação

1. Para publicar no ponto de extremidade, selecione **publicar** na parte superior, painel direito. 

    ![Botão publicar na parte superior, barra de navegação direita](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Selecione as configurações para o ponto de extremidade de previsão publicado e, em seguida, selecione **publicar**.

    ![Selecione Publicar configurações e, em seguida, clique no botão publicar](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Slots de publicação

Selecione o slot correto quando a janela pop-up for exibida: 

* Teste
* Produção 

Usando os dois slots de publicação, isso permite que você tenha duas versões diferentes do seu aplicativo disponíveis nos pontos de extremidade publicados ou na mesma versão em dois pontos de extremidade diferentes. 

### <a name="publishing-regions"></a>Regiões de publicação

O aplicativo é publicado em todas as regiões associadas aos recursos de ponto de extremidade de previsão de LUIS adicionados no portal do LUIS da página **gerenciar** ->  **[recursos do Azure](/luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** . 

Por exemplo, para um aplicativo criado em [www.Luis.ai](https://www.luis.ai), se você criar um recurso de Luis em duas regiões, **westus** e **lesteus**, e adicioná-los ao aplicativo como recursos, o aplicativo será publicado em ambas as regiões. Para obter mais informações sobre regiões LUIS, consulte [regiões](luis-reference-regions.md).

> [!TIP]
> Há 3 regiões de criação. Você deve criar a região na qual pretende publicar. Se você precisar publicar em todas as regiões, será necessário gerenciar o processo de criação e o modelo treinado resultante em todas as três regiões de criação. 


## <a name="configuring-publish-settings"></a>Configurar definições de publicação

Depois de selecionar o slot, defina as configurações de publicação para:

* Análise de sentimentos
* Correção ortográfica-somente ponto de extremidade de previsão v2
* Espriming de fala 

Depois de publicar, essas configurações estarão disponíveis para análise na página **gerenciar** configurações de **publicação** da seção. Você pode alterar as configurações com cada publicação. Se você cancelar uma publicação, as alterações feitas durante a publicação também serão canceladas. 

### <a name="when-your-app-is-published"></a>Quando seu aplicativo é publicado

Quando seu aplicativo for publicado com êxito, uma notificação de êxito aparecerá na parte superior do navegador. A notificação também inclui um link para os pontos de extremidade. 

Se precisar do URL de ponto de extremidade, selecione a ligação. Você também pode acessar as URLs do ponto de extremidade selecionando **gerenciar** no menu superior e, em seguida, selecione **recursos do Azure** no menu à esquerda. 

## <a name="sentiment-analysis"></a>Análise de sentimentos

<a name="enable-sentiment-analysis"></a>

A análise de sentimentos permite que o LUIS se integre com [análise de texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) para fornecer opiniões e análise de frases-chave. 

Não é necessário fornecer uma chave de análise de texto e não sem custos de faturação para este serviço para a sua conta do Azure. 

Dados de sentimento são uma pontuação entre 1 e 0 indicando o positivo (mais de perto como 1) nem negativa (mais próximo de 0) sentimentos dos dados. O rótulo de sentimentos de `positive`, `neutral`e `negative` é por cultura com suporte. Atualmente, apenas o inglês dá suporte a rótulos de sentimentos. 

Para obter mais informações sobre a resposta de ponto de extremidade JSON com análise de sentimentos, consulte [análise de sentimentos](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Correção ortográfica

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

As correções de ortografia são feitas antes da previsão de expressão do usuário LUIS. Você pode ver quaisquer alterações no expressão original, incluindo a ortografia, na resposta.

## <a name="speech-priming"></a>Espriming de fala

A desobstrução de fala é o processo de usar o envio do modelo LUIS para os serviços de fala antes da conversão de texto em fala. Isso permite que o serviço de fala forneça a conversão de fala com mais precisão para seu modelo. Isso permite o bot Speech e solicitações LUIS e respostas em uma chamada fazendo uma chamada de fala e voltando uma resposta LUIS. Ele fornece menos latência geral.

## <a name="next-steps"></a>Passos seguintes

* Consulte [gerenciar chaves](./luis-how-to-azure-subscription.md) para adicionar chaves à chave de assinatura do Azure para Luis e como definir a chave de verificação ortográfica do Bing e incluir todas as intenções nos resultados.
* Consulte [treinar e testar seu aplicativo](luis-interactive-test.md) para obter instruções sobre como testar seu aplicativo publicado no console de teste.

