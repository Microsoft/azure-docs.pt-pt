---
title: Fluxo de controle de Conversation Learner-serviços cognitivas da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba mais sobre o fluxo de controle de Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 3ec839c1a930ffbe73989149360f1b02866a3c50
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705316"
---
## <a name="control-flow"></a>Fluxo de controlo

Este documento descreve o fluxo de controle do Conversation Learner (CL), conforme exibido no diagrama abaixo.

![](media/controlflow.PNG)

1. O usuário insere um termo ou frase no bot, por exemplo, ' Qual é o clima em Seattle? '
1. CL passa a entrada do usuário para um modelo de aprendizado de máquina que extrai entidades
   - Esse modelo é compilado por Conversation Learner e hospedado por www.luis.ai
1. Qualquer entidade extraída e o texto de entrada do usuário são passados para o método de retorno de chamada de detecção de entidade no código do bot.
    - Esse código pode definir/limpar/manipular valores de entidade
1. A rede neural CL usa a saída da extração de entidade e a entrada do usuário e pontua todas as ações definidas no bot
   - Neste exemplo, a ação de probabilidade mais alta é fornecer a previsão do tempo:

     ![](media/controlflow_forecast.PNG)

1. A ação selecionada, nesse caso, requer uma chamada à API para recuperar a previsão do tempo. 
1. Essa API, que foi registrada usando o CL. O método AddCallback, é invocado.  O resultado dessa API é retornado ao usuário como uma mensagem--por exemplo, ' ensolarado com um alto de 67. '
1. Em seguida, a chamada é feita à rede neural para determinar a próxima ação com base na etapa anterior.
1. Em seguida, a rede neural prevê o próximo conjunto de ações possíveis e a ação selecionada é apresentada ao usuário, neste caso, ' qualquer outra coisa? '

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Como ensinar com Conversation Learner](./how-to-teach-cl.md)
