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
ms.openlocfilehash: 8c2559315e3bfffc41c138be6826adae95dd7b07
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588110"
---
# <a name="what-is-azure-communication-services"></a>O que é o Azure Communication Services?

Os Serviços de Comunicação Azure permitem-lhe adicionar facilmente voz, vídeo e comunicação telefónica em tempo real às suas aplicações. Os SDKs de Serviços de Comunicação também permitem adicionar a funcionalidade SMS às suas soluções de comunicações. Os Serviços de Comunicação Azure são agnósticos de identidade e você tem controlo total sobre como os utilizadores finais são identificados e autenticados. Pode ligar os humanos ao plano de dados de comunicação ou serviços (bots).

As candidaturas incluem:

- **Negócios para o Consumidor (B2C).** Os colaboradores e serviços de uma empresa podem interagir com os consumidores usando voz, vídeo e rico chat de texto num navegador personalizado ou aplicação móvel. Uma organização pode enviar e receber mensagens SMS, ou operar um sistema interativo de resposta a voz (IVR) utilizando um número de telefone que adquire através do Azure. [A integração com as Equipas microsoft](./quickstarts/voice-video-calling/get-started-teams-interop.md) permite que os consumidores se juntem às reuniões das Equipas organizadas pelos colaboradores; ideal para os cenários de cuidados de saúde remotos, banca e suporte ao produto onde os colaboradores já podem estar familiarizados com as Equipas.
- **Consumidor ao Consumidor.** Construa espaços sociais envolventes para a interação entre o consumidor e o consumidor com voz, vídeo e rico chat de texto. Qualquer tipo de interface de utilizador pode ser construída em SDKs de Serviços de Comunicação Azure, mas amostras completas de aplicações e ativos de UI estão disponíveis para ajudá-lo a começar rapidamente.

Para saber mais, confira o nosso vídeo da Microsoft Mechanics ou os recursos abaixo [ligados.](https://www.youtube.com/watch?v=apBX7ASurgM)

## <a name="common-scenarios"></a>Cenários comuns

<br>

| Recurso                               |Descrição                           |
|---                                    |---                                   |
|**[Criar um recurso do Communication Services](./quickstarts/create-communication-resource.md)**|Comece a utilizar os Serviços de Comunicação Azure utilizando o portal Azure ou serviços de comunicação SDK para forrê o seu primeiro recurso de Serviços de Comunicação. Assim que tiver a sua cadeia de ligação de recursos de serviços de comunicação, pode providenciar os seus primeiros tokens de acesso ao utilizador.|
|**[Obtenha um número de telefone](./quickstarts/telephony-sms/get-phone-number.md)**|Pode utilizar os Serviços de Comunicação Azure para providenciar e libertar números de telefone. Estes números de telefone podem ser usados para iniciar ou receber chamadas telefónicas e construir soluções SMS.|
|**[Envie um SMS da sua aplicação](./quickstarts/telephony-sms/send.md)**|O Azure Communication Services SMS SDK é utilizado enviar e receber mensagens SMS de aplicações de serviço.|

Depois de criar um recurso de Serviços de Comunicação, pode começar a construir cenários de clientes, tais como chamadas de voz e vídeo ou chat de texto:

| Recurso                               |Descrição                           |
|---                                    |---                                   |
|**[Crie o seu primeiro token de acesso ao utilizador](./quickstarts/access-tokens.md)**|Os tokens de acesso ao utilizador são utilizados para autenticar clientes contra o seu recurso Azure Communication Services. Estes tokens são a provisionados e reemitidos utilizando os Serviços de Comunicação SDK.|
|**[Começar com voz e vídeo chamando](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Os Serviços de Comunicação Azure permitem-lhe adicionar chamadas de voz e vídeo ao seu navegador ou aplicações nativas utilizando o Call SDK. |
|**[Associe a sua aplicação de chamadas a uma reunião do Teams](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|Os Serviços de Comunicação Azure podem ser usados para construir experiências de reunião personalizadas que interagem com as Equipas da Microsoft. Os utilizadores da sua solução de Serviços de Comunicação podem interagir com os participantes das Equipas em vez de voz, vídeo, chat e partilha de ecrãs.|
|**[Começa com o chat](./quickstarts/chat/get-started.md)**|O Azure Communication Services Chat SDK é usado para adicionar um chat de texto rico em tempo real nas suas aplicações.|

## <a name="samples"></a>Amostras

As seguintes amostras demonstram uma utilização de ponta a ponta dos Serviços de Comunicação Azure. Utilize estas amostras para arrancar as suas próprias soluções de Serviços de Comunicação.
<br>

| Nome da amostra                               | Description                           |
|---                                    |---                                   |
|**[O Grupo Chamando a Amostra de Herói](./samples/calling-hero-sample.md)**| Descarregue uma amostra de aplicação projetada para dispositivos de grupo que chamem navegadores, iOS e dispositivos Android. |
|**[A amostra de herói de chat do grupo](./samples/chat-hero-sample.md)**| Faça o download de uma amostra de aplicação projetada para chat de texto de grupo para navegadores. |


## <a name="platforms-and-sdk-libraries"></a>Plataformas e bibliotecas SDK

Saiba mais sobre os SDKs dos Serviços de Comunicação Azure com os recursos abaixo. Rest APIs estão disponíveis para a maioria das funcionalidades se quiser construir os seus próprios clientes ou aceder ao serviço através da Internet.

| Recurso                               | Descrição                           |
|---                                    |---                                   |
|**[Bibliotecas SDK e APIs REST](./concepts/sdk-options.md)**|As capacidades dos Serviços de Comunicação Azure são conceptualmente organizadas em seis áreas, cada uma representada por um SDK. Pode decidir quais bibliotecas SDK usar com base nas suas necessidades de comunicação em tempo real.|
|**[Chamando a visão geral da SDK](./concepts/voice-video-calling/calling-sdk-features.md)**|Reveja a visão geral dos Serviços de Comunicação da SDK.|
|**[Visão geral do Chat SDK](./concepts/chat/sdk-features.md)**|Reveja a visão geral do Chat SDK dos Serviços de Comunicação.|
|**[Visão geral do SMS SDK](./concepts/telephony-sms/sdk-features.md)**|Reveja a visão geral dos Serviços de Comunicação SMS SDK.|

## <a name="other-microsoft-communication-services"></a>Outros serviços de comunicação da Microsoft

Existem outros dois produtos de comunicação da Microsoft que pode considerar usar que não são diretamente interoperáveis com os Serviços de Comunicação neste momento:

 - [As APIs de Comunicação cloud](/graph/cloud-communications-concept-overview) do Microsoft permitem que as organizações construam experiências de comunicação ligadas a utilizadores do Azure Ative Directory com licenças Microsoft 365. Isto é ideal para aplicações ligadas ao Azure Ative Directory ou onde pretende alargar experiências de produtividade em Equipas microsoft. Existem também APIs para construir aplicações e personalização dentro da [experiência das Equipas.](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest)

 - [Azure PlayFab Party](/gaming/playfab/features/multiplayer/networking/) simplifica a adição de chat de baixa latência e comunicação de dados aos jogos. Enquanto podes alimentar sistemas de chat e networking com Serviços de Comunicação, o PlayFab é uma opção personalizada e gratuita na Xbox.


## <a name="next-steps"></a>Passos Seguintes

 - [Criar um recurso do Communication Services](./quickstarts/create-communication-resource.md)
