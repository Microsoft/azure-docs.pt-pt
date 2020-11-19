---
title: O que é o Azure Communication Services?
description: Saiba como a Azure Communication Services o ajuda a desenvolver experiências ricas de utilizadores com comunicações em tempo real.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: d680df0ec5e18cca4d7a42edf45dbd6a50be1472
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888611"
---
# <a name="what-is-azure-communication-services"></a>O que é o Azure Communication Services?

[!INCLUDE [Public Preview Notice](./includes/public-preview-include.md)]

Os Serviços de Comunicação Azure permitem-lhe adicionar facilmente funcionalidades de voz multimédia, vídeo e telefonia nas suas aplicações. As bibliotecas de clientes dos Serviços de Comunicação também permitem adicionar funcionalidade de chat e SMS às suas soluções de comunicações.

<br>

> [!VIDEO https://www.youtube.com/embed/49oshhgY6UQ]

<br>
<br>

Pode utilizar os Serviços de Comunicação para comunicação de voz, vídeo, texto e dados em vários cenários:

- Browser-to-browser, browser-to-app e comunicação app-to-app
- Utilizadores interagindo com bots ou outros serviços
- Utilizadores e bots interagindo sobre a rede pública de telefonia switchada

Os cenários mistos são apoiados. Por exemplo, uma aplicação de Serviços de Comunicação pode ter utilizadores falando a partir de navegadores e dispositivos de telefonia tradicionais ao mesmo tempo. Os Serviços de Comunicação também podem ser combinados com o Azure Bot Service para construir sistemas de resposta interativa de voz (IVR) orientados por bots.

## <a name="common-scenarios"></a>Cenários comuns

Os seguintes recursos são um ótimo lugar para começar se você é novo nos Serviços de Comunicação Azure:
<br>

| Recurso                               |Descrição                           |
|---                                    |---                                   |
|**[Criar um recurso do Communication Services](./quickstarts/create-communication-resource.md)**|Pode começar a utilizar os Serviços de Comunicação Azure utilizando o portal Azure ou a biblioteca de clientes da Administração de Serviços de Comunicação para forragem do seu primeiro recurso de Serviços de Comunicação. Assim que tiver a sua cadeia de ligação de recursos de serviços de comunicação, pode providenciar os seus primeiros tokens de acesso ao utilizador.|
|**[Crie o seu primeiro token de acesso ao utilizador](./quickstarts/access-tokens.md)**|Os tokens de acesso ao utilizador são utilizados para autenticar os seus serviços contra o seu recurso Azure Communication Services. Estes tokens são a provisionados e reemitidos utilizando a biblioteca de clientes da Administração de Serviços de Comunicação.|
|**[Obtenha um número de telefone](./quickstarts/telephony-sms/get-phone-number.md)**|Pode utilizar os Serviços de Comunicação Azure para providenciar e libertar números de telefone. Estes números de telefone podem ser usados para iniciar chamadas de saída e construir soluções de comunicações SMS.|
|**[Envie um SMS da sua aplicação](./quickstarts/telephony-sms/send.md)**|A biblioteca de clientes Azure Communication Services permite-lhe enviar e receber mensagens SMS das suas aplicações .NET e JavaScript.|
|**[Começar com voz e vídeo chamando](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Os Serviços de Comunicação Azure permitem-lhe adicionar chamadas de voz e vídeo às suas aplicações utilizando a biblioteca do cliente Call. Esta biblioteca é alimentada pela WebRTC e permite-lhe estabelecer comunicações peer-to-peer, multimédia, em tempo real dentro das suas aplicações.|
|**[Começa com o chat](./quickstarts/chat/get-started.md)**|A biblioteca de clientes Azure Communication Services Chat pode ser usada para integrar o chat em tempo real nas suas aplicações.|


## <a name="samples"></a>Amostras

As seguintes amostras demonstram a utilização de ponta a ponta das bibliotecas dos serviços de comunicação Azure. Sinta-se à vontade para usar estas amostras para arrancar as suas próprias soluções de Serviços de Comunicação.
<br>

| Nome da amostra                               | Descrição                           |
|---                                    |---                                   |
|**[O Grupo Chamando a Amostra de Herói](./samples/calling-hero-sample.md)**|Veja como as bibliotecas de clientes dos Serviços de Comunicação podem ser usadas para construir uma experiência de chamada em grupo.|
|**[A amostra de herói de chat do grupo](./samples/chat-hero-sample.md)**|Veja como as bibliotecas de clientes dos Serviços de Comunicação podem ser usadas para construir uma experiência de chat em grupo.|


## <a name="platforms-and-client-libraries"></a>Plataformas e bibliotecas de clientes

Os seguintes recursos irão ajudá-lo a conhecer as bibliotecas de clientes dos Serviços de Comunicação Azure:

| Recurso                               | Descrição                           |
|---                                    |---                                   |
|**[Bibliotecas de cliente e APIs REST](./concepts/sdk-options.md)**|As capacidades dos Serviços de Comunicação Azure estão conceptualmente organizadas em seis áreas, cada uma representada por uma biblioteca de clientes. Pode decidir quais as bibliotecas de clientes a utilizar com base nas suas necessidades de comunicação em tempo real.|
|**[Descrição geral da biblioteca de cliente de chamadas](./concepts/voice-video-calling/calling-sdk-features.md)**|Reveja os serviços de comunicação Ligando a biblioteca de clientes.|
|**[Descrição geral da biblioteca de cliente de chat](./concepts/chat/sdk-features.md)**|Reveja a visão geral da biblioteca de clientes de Comunicação Chat.|
|**[Descrição geral da biblioteca de cliente de SMS](./concepts/telephony-sms/sdk-features.md)**|Reveja a visão geral da biblioteca de clientes de serviços de comunicação.|

## <a name="compare-azure-communication-services"></a>Comparar serviços de comunicação da Azure

Existem outros dois produtos de comunicação da Microsoft que pode considerar alavancagem que não são diretamente interoperáveis com os Serviços de Comunicação neste momento:

 - [As APIs de Comunicação cloud do Microsoft permitem](/graph/cloud-communications-concept-overview) que as organizações construam experiências de comunicação ligadas a utilizadores do Azure Ative Directory com licenças M365. Isto é ideal para aplicações ligadas ao Azure Ative Directory ou onde pretende alargar experiências de produtividade em Equipas microsoft. Existem também APIs para construir aplicações e personalização dentro da [experiência das Equipas.](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest)

 - [Azure PlayFab Party](/gaming/playfab/features/multiplayer/networking/) simplifica a adição de chat de baixa latência e comunicação de dados aos jogos. Enquanto podes alimentar sistemas de chat e networking com Serviços de Comunicação, o PlayFab é uma opção personalizada e gratuita na Xbox.


## <a name="next-steps"></a>Passos Seguintes

 - [Criar um recurso do Communication Services](./quickstarts/create-communication-resource.md)