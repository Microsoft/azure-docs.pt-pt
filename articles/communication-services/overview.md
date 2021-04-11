---
title: O que é o Azure Communication Services?
description: Saiba como a Azure Communication Services o ajuda a desenvolver experiências ricas de utilizadores com comunicações em tempo real.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 0efdf48e78d0cc48e288bea354f5de5f9635c760
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106845"
---
# <a name="what-is-azure-communication-services"></a>O que é o Azure Communication Services?

> [!IMPORTANT]
> As aplicações que constrói utilizando os Serviços de Comunicação Azure podem falar com as Equipas da Microsoft. Para saber mais, visite a documentação da nossa [Equipa Interop.](./quickstarts/voice-video-calling/get-started-teams-interop.md)


Os Serviços de Comunicação Azure permitem-lhe adicionar facilmente funcionalidades de voz multimédia, vídeo e telefonia nas suas aplicações. As bibliotecas SDK dos Serviços de Comunicação também permitem adicionar funcionalidade de chat e SMS às suas soluções de comunicações.

<br>

> [!VIDEO https://www.youtube.com/embed/apBX7ASurgM]

<br>
<br>

Pode utilizar os Serviços de Comunicação para comunicação de voz, vídeo, texto e dados em vários cenários:

- Browser-to-browser, browser-to-app e comunicação app-to-app
- Utilizadores interagindo com bots ou outros serviços
- Utilizadores e bots interagindo sobre a rede pública de telefonia switchada

Os cenários mistos são apoiados. Por exemplo, uma aplicação de Serviços de Comunicação pode ter utilizadores falando a partir de navegadores e dispositivos de telefonia tradicionais ao mesmo tempo. Os Serviços de Comunicação também podem ser combinados com o Azure Bot Service para construir sistemas de resposta interativa de voz (IVR) orientados por bots.

## <a name="common-scenarios"></a>Cenários comuns

Os seguintes recursos são um ótimo lugar para começar com os Serviços de Comunicação Azure.
<br>

| Recurso                               |Descrição                           |
|---                                    |---                                   |
|**[Criar um recurso do Communication Services](./quickstarts/create-communication-resource.md)**|Pode começar a utilizar os Serviços de Comunicação Azure utilizando o portal Azure ou a SDK dos Serviços de Comunicação para forrê-lo através do seu primeiro recurso de Serviços de Comunicação. Assim que tiver a sua cadeia de ligação de recursos de serviços de comunicação, pode providenciar os seus primeiros tokens de acesso ao utilizador.|
|**[Obtenha um número de telefone](./quickstarts/telephony-sms/get-phone-number.md)**|Pode utilizar os Serviços de Comunicação Azure para providenciar e libertar números de telefone. Estes números de telefone podem ser usados para iniciar chamadas de saída e construir soluções de comunicações SMS.|

Depois de criar um recurso de Serviços de Comunicação, pode começar a construir cenários de clientes, como chamadas de voz e vídeo ou chat de texto.

| Recurso                               |Descrição                           |
|---                                    |---                                   |
|**[Crie o seu primeiro token de acesso ao utilizador](./quickstarts/access-tokens.md)**|Os tokens de acesso ao utilizador são utilizados para autenticar os seus serviços contra o seu recurso Azure Communication Services. Estes tokens são a provisionados e reemitidos utilizando os Serviços de Comunicação SDK.|
|**[Começar com voz e vídeo chamando](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Os Serviços de Comunicação Azure permitem-lhe adicionar chamadas de voz e vídeo às suas aplicações utilizando o Call SDK. Esta biblioteca é alimentada pela WebRTC e permite-lhe estabelecer comunicações peer-to-peer, multimédia, em tempo real dentro das suas aplicações.|
|**[Associe a sua aplicação de chamadas a uma reunião do Teams](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|Os Serviços de Comunicação Azure podem ser usados para construir experiências de reunião personalizadas que interagem com as Equipas da Microsoft. Os utilizadores da sua solução de Serviços de Comunicação podem interagir com os participantes das Equipas em vez de voz, vídeo, chat e partilha de ecrãs.|
|**[Começa com o chat](./quickstarts/chat/get-started.md)**|O Azure Communication Services Chat SDK pode ser usado para integrar o chat em tempo real nas suas aplicações.|
|**[Envie um SMS da sua aplicação](./quickstarts/telephony-sms/send.md)**|O Azure Communication Services SMS SDK permite-lhe enviar e receber mensagens SMS das suas aplicações .NET e JavaScript.|

## <a name="samples"></a>Amostras

As seguintes amostras demonstram a utilização de ponta a ponta das bibliotecas SDK dos Serviços de Comunicação Azure. Sinta-se à vontade para usar estas amostras para arrancar as suas próprias soluções de Serviços de Comunicação.
<br>

| Nome da amostra                               | Description                           |
|---                                    |---                                   |
|**[O Grupo Chamando a Amostra de Herói](./samples/calling-hero-sample.md)**|Veja como as bibliotecas SDK dos Serviços de Comunicação podem ser usadas para construir uma experiência de chamada de grupo.|
|**[A amostra de herói de chat do grupo](./samples/chat-hero-sample.md)**|Veja como as bibliotecas SDK dos Serviços de Comunicação podem ser usadas para construir uma experiência de chat em grupo.|


## <a name="platforms-and-sdk-libraries"></a>Plataformas e bibliotecas SDK

Os seguintes recursos irão ajudá-lo a aprender sobre as bibliotecas SDK dos Serviços de Comunicação Azure:

| Recurso                               | Descrição                           |
|---                                    |---                                   |
|**[Bibliotecas SDK e APIs REST](./concepts/sdk-options.md)**|As capacidades dos Serviços de Comunicação Azure são conceptualmente organizadas em seis áreas, cada uma representada por um SDK. Pode decidir quais bibliotecas SDK usar com base nas suas necessidades de comunicação em tempo real.|
|**[Chamando a visão geral da SDK](./concepts/voice-video-calling/calling-sdk-features.md)**|Reveja a visão geral dos Serviços de Comunicação da SDK.|
|**[Visão geral do Chat SDK](./concepts/chat/sdk-features.md)**|Reveja a visão geral do Chat SDK dos Serviços de Comunicação.|
|**[Visão geral do SMS SDK](./concepts/telephony-sms/sdk-features.md)**|Reveja a visão geral dos Serviços de Comunicação SMS SDK.|

## <a name="other-microsoft-communication-services"></a>Outros serviços de comunicação da Microsoft

Existem outros dois produtos de comunicação da Microsoft que pode considerar alavancagem que não são diretamente interoperáveis com os Serviços de Comunicação neste momento:

 - [As APIs de Comunicação cloud](/graph/cloud-communications-concept-overview) do Microsoft permitem que as organizações construam experiências de comunicação ligadas a utilizadores do Azure Ative Directory com licenças Microsoft 365. Isto é ideal para aplicações ligadas ao Azure Ative Directory ou onde pretende alargar experiências de produtividade em Equipas microsoft. Existem também APIs para construir aplicações e personalização dentro da [experiência das Equipas.](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest)

 - [Azure PlayFab Party](/gaming/playfab/features/multiplayer/networking/) simplifica a adição de chat de baixa latência e comunicação de dados aos jogos. Enquanto podes alimentar sistemas de chat e networking com Serviços de Comunicação, o PlayFab é uma opção personalizada e gratuita na Xbox.


## <a name="next-steps"></a>Passos Seguintes

 - [Criar um recurso do Communication Services](./quickstarts/create-communication-resource.md)
