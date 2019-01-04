---
title: Fluxo de controlo de aprendiz de conversação - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Saiba mais sobre o fluxo de controle de aprendiz de conversação.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8007e9e9fe2f404b4d702471610ff76047f7ed86
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790933"
---
## <a name="control-flow"></a>Fluxo de controlo

Este documento descreve o fluxo de controle de aprendiz de conversação (CL), tal como apresentado no diagrama abaixo.

![](media/controlflow.PNG)

1. Usuário insere um termo ou frase no bot, por exemplo, "o que é o clima em Seattle?"
1. CL passa a entrada do usuário para um modelo de aprendizagem automática que extrai entidades
    - Este modelo é compilação pelo Aprendiz de conversação e hospedado por www.luis.ai
1. Qualquer extraídos entidades e a entrada do utilizador texto, são transmitidos para o método de retorno de chamada de detecção de entidade no código do bot.
    - Este código poderá valores conjunto/limpar/manipular entidade
1. CL de rede neural, em seguida, usa a saída a extração de entidades e a entrada do usuário e as pontuações de todas as ações definidas no bot
    - Neste exemplo, a ação de probabilidade mais alta é fornecer a previsão meteorológica:

    ![](media/controlflow_forecast.PNG)

1. A ação selecionada, neste caso, necessita de uma chamada à API para obter a previsão meteorológica. 
1. Esta API, que tinha sido registrado usando CL. Método de AddCallback, em seguida, é invocado.  O resultado desta API é, em seguida, devolvido ao usuário como uma mensagem – por exemplo, "Sunny com uma alta de 67."
1. A chamada, em seguida, é feita para a rede neural para determinar a próxima ação com base no passo anterior.
1. A rede neural prevê, em seguida, o próximo conjunto de ações possíveis, e a ação selecionada é apresentada ao usuário, neste caso, "Qualquer outra coisa?"

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Como apresentar com Aprendiz de conversação ](./how-to-teach-cl.md)
