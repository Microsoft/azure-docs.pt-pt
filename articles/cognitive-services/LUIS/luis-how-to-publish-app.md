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
ms.date: 01/08/2019
ms.author: diberry
ms.openlocfilehash: fdbdb2cd13d131019c4230aa369af31240d25f0d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563645"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publicar seu aplicativo ativo e treinado em um ponto de extremidade de preparo ou de produção

Quando terminar de criar e testar a sua aplicação LUIS Active Directory, torná-lo disponível para a aplicação de cliente, publicá-la para o ponto final. 

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>Publicação

Para publicar para o ponto final, selecione **publicar** na parte superior, com o botão direito painel. 

![Barra de navegação superior, certo](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

Selecione a ranhura correta quando a janela de pop-up apresenta: transição ou produção. Ao utilizar duas ranhuras de publicação, isto permite-lhe ter duas versões diferentes com pontos de extremidade publicados ou a mesma versão em dois pontos de extremidade diferentes. 

A aplicação for publicada para todas as regiões associadas com os recursos de LUIS, adicionados no portal do LUIS. Por exemplo, para uma aplicação criada no [www.luis.ai](https://www.luis.ai), se criar um recurso de LUIS na **westus** e adicioná-lo para a aplicação como um recurso, a aplicação for publicada nessa região. Para obter mais informações sobre as regiões de LUIS, consulte [regiões](luis-reference-regions.md).
 
![Janela de pop-up de publicação](./media/luis-how-to-publish-app/publish-pop-up.png)

Quando a aplicação é publicada com êxito, é apresentada uma notificação de êxito verde na parte superior do navegador. A barra de notificação verde também inclui uma ligação para os pontos de extremidade. 

![Publicando janela pop-up com o link para o ponto de extremidade](./media/luis-how-to-publish-app/publish-success.png)

Se precisar do URL de ponto de extremidade, selecione a ligação. Também pode obter para os URLs de ponto de extremidade, selecionando **Manage** no menu superior, em seguida, selecione **chaves e os pontos finais** no menu à esquerda. 

## <a name="configuring-publish-settings"></a>Configurar definições de publicação

Configurar definições de publicação selecionando **Manage** na parte superior, com o botão direito navegação, em seguida, selecionar **definições de publicação**. 

![Definições de publicação](./media/luis-how-to-publish-app/publish-settings.png)

### <a name="publish-after-enabling-sentiment-analysis"></a>Publicar depois de ativar a análise de sentimentos

<a name="enable-sentiment-analysis"></a>

Análise de sentimentos permite o LUIS integrar com o [análise de texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) para fornecer análise de expressão de sentimentos e a chave. 

Não é necessário fornecer uma chave de análise de texto e não sem custos de faturação para este serviço para a sua conta do Azure. Depois de verificar esta definição, é persistente. 

Dados de sentimento são uma pontuação entre 1 e 0 indicando o positivo (mais de perto como 1) nem negativa (mais próximo de 0) sentimentos dos dados. O rótulo de sentimentos `positive`de `neutral`, e `negative` é por cultura com suporte. Atualmente, apenas o inglês dá suporte a rótulos de sentimentos. 

Para obter mais informações sobre a resposta do ponto final JSON com a análise de sentimentos, consulte [análise de sentimentos](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="next-steps"></a>Passos Seguintes

* Ver [gerir chaves](./luis-how-to-azure-subscription.md) adicionar chaves de subscrição do Azure chave para LUIS e como definir a verificação ortográfica do Bing da chave em inclua todas as intenções nos resultados.
* Ver [treinar e testar a aplicação](luis-interactive-test.md) para obter instruções sobre como testar a aplicação publicada na consola de teste.

