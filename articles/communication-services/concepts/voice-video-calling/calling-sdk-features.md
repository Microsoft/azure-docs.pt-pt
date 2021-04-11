---
title: Serviços de comunicação Azure chamando a visão geral da SDK
titleSuffix: An Azure Communication Services concept document
description: Fornece uma visão geral do Call SDK.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: ac9cef77569dffe461f7711195c5638e831aa218
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110109"
---
# <a name="calling-sdk-overview"></a>Chamando a visão geral da SDK

O Call SDK permite que os dispositivos de utilizador final conduzam experiências de comunicação de voz e vídeo. Esta página fornece descrições detalhadas das funcionalidades de Chamada, incluindo informações de suporte à plataforma e ao navegador. Para começar imediatamente, por favor, confira [chamando quickstarts](../../quickstarts/voice-video-calling/getting-started-with-calling.md) ou [chamando a amostra de herói.](../../samples/calling-hero-sample.md) 

Assim que começar o desenvolvimento, consulte a [página de assuntos conhecidos](../known-issues.md) para encontrar bugs em que estamos a trabalhar.

Principais características do Call SDK:

- **Endereçar** - Os Serviços de Comunicação Azure fornecem [identidades genéricas](../identity-model.md) que são usadas para endpoints de comunicação. Os clientes utilizam estas identidades para autenticar ao serviço e comunicar entre si. Estas identidades são usadas em APIs de chamada que dá visibilidade aos clientes sobre quem está ligado a uma chamada (o plantel).
- **Encriptação** - O Call SDK encripta o tráfego e impede a adulteração do fio. 
- **Gestão de Dispositivos e Meios** - O Call SDK fornece instalações para a ligação a dispositivos de áudio e vídeo, codifica conteúdos para uma transmissão eficiente através do plano de dados de comunicações e torna o conteúdo em dispositivos de saída e visualizações que especifica. As APIs também estão previstas para a partilha de ecrãs e aplicações.
- **PSTN** - O Call SDK pode receber e iniciar chamadas de voz com o sistema tradicional de telefonia comutada publicamente, [utilizando números de telefone que adquire no portal Azure](../../quickstarts/telephony-sms/get-phone-number.md) ou programáticamente.
- **Teams Meetings** - O Call SDK pode [juntar-se às reuniões das Equipas](../../quickstarts/voice-video-calling/get-started-teams-interop.md) e interagir com o plano de dados de voz e vídeo das Equipas. 
- **Notificações** - A SDK de chamada fornece APIs permitindo que os clientes sejam notificados de uma chamada recebida. Nas situações em que a sua aplicação não está em primeiro plano, existem padrões disponíveis para [disparar notificações pop-up](../notifications.md) ("brindes") para informar os utilizadores finais de uma chamada recebida. 

## <a name="detailed-capabilities"></a>Capacidades detalhadas 

A lista a seguir apresenta o conjunto de funcionalidades que estão atualmente disponíveis nos Serviços de Comunicação Azure Que chamam SDKs.

| Grupo de funcionalidades | Funcionalidade                                                                                                          | JS  | Java (Android) | Objective-C (iOS)
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Capacidades do Núcleo | Coloque uma chamada de um para um entre dois utilizadores                                                                           | ✔️   | ✔️            | ✔️
|                   | Coloque uma chamada de grupo com mais de dois utilizadores (até 350 utilizadores)                                                       | ✔️   | ✔️            | ✔️
|                   | Promover uma chamada de um para um com dois utilizadores numa chamada de grupo com mais de dois utilizadores                                 | ✔️   | ✔️            | ✔️
|                   | Junte-se a uma chamada de grupo depois de ter começado                                                                              | ✔️   | ✔️            | ✔️
|                   | Convide outro participante do VoIP para se juntar a uma chamada de grupo em curso                                                       | ✔️   | ✔️            | ✔️
|  Controlo de chamadas médias | Ligue/desligue o vídeo                                                                                              | ✔️   | ✔️            | ✔️
|                   | Microfone mudo/unmute                                                                                                     | ✔️   | ✔️            | ✔️
|                   | Alternar entre câmaras                                                                                              | ✔️   | ✔️            | ✔️
|                   | Porão local/des-hold                                                                                                  | ✔️   | ✔️            | ✔️
|                   | Altifalante ativo                                                                                                      | ✔️   | ✔️            | ✔️
|                   | Escolha o orador para chamadas                                                                                            | ✔️   | ✔️            | ✔️
|                   | Escolha o microfone para chamadas                                                                                         | ✔️   | ✔️            | ✔️
|                   | Mostrar estado de um participante<br/>*Idle, Early media, Connecting, Connected, On hold, In Lobby, Disconnected*         | ✔️   | ✔️            | ✔️
|                   | Mostrar estado de uma chamada<br/>*Meios iniciais, entrada, ligação, toque, conectado, seguro, desligado, desligado* | ✔️   | ✔️            | ✔️
|                   | Mostrar se um participante é silenciado                                                                                      | ✔️   | ✔️            | ✔️
|                   | Mostre a razão pela qual um participante deixou uma chamada                                                                       | ✔️   | ✔️            | ✔️
| Partilha de tela    | Partilhe todo o ecrã a partir da aplicação                                                                 | ✔️   | ❌            | ❌
|                   | Partilhar uma aplicação específica (da lista de aplicações em execução)                                                | ✔️   | ❌            | ❌
|                   | Partilhe um separador de navegador na lista de separadores abertos                                                                  | ✔️   | ❌            | ❌
|                   | O participante pode ver a partilha de ecrã remoto                                                                            | ✔️   | ✔️            | ✔️
| Plantel            | Listar participantes                                                                                                   | ✔️   | ✔️            | ✔️
|                   | Remover um participante                                                                                                | ✔️   | ✔️            | ✔️
| PSTN              | Coloque uma chamada de um para um com um participante pstn                                                                     | ✔️   | ✔️            | ✔️
|                   | Coloque uma chamada de grupo com os participantes do PSTN                                                                           | ✔️   | ✔️            | ✔️
|                   | Promover uma chamada um-para-um com um participante pstn em uma chamada de grupo                                                 | ✔️   | ✔️            | ✔️
|                   | Marcação de uma chamada de grupo como participante da PSTN                                                                    | ✔️   | ✔️            | ✔️
| Geral           | Teste o seu microfone, altifalante e câmara com um serviço de testes de áudio (disponível através do telefone 8:echo123)                   | ✔️   | ✔️            | ✔️
| Gestão de Dispositivos | Pedir permissão para usar áudio e/ou vídeo                                                                       | ✔️   | ✔️            | ✔️
|                   | Obtenha a lista de câmeras                                                                                                     | ✔️   | ✔️            | ✔️
|                   | Definir câmera                                                                                                          | ✔️   | ✔️            | ✔️
|                   | Obtenha uma câmera selecionada                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Obtenha a lista de microfones                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Definir microfone                                                                                                      | ✔️   | ✔️            | ✔️
|                   | Obtenha o microfone selecionado                                                                                             | ✔️   | ✔️            | ✔️
|                   | Obtenha a lista de oradores                                                                                                   | ✔️   | ✔️            | ✔️
|                   | Definir alto-falante                                                                                                         | ✔️   | ✔️            | ✔️
|                   | Obtenha o orador selecionado                                                                                                | ✔️   | ✔️            | ✔️
| Renderização de vídeo   | Rendere um único vídeo em muitos lugares (câmara local ou fluxo remoto)                                                  | ✔️   | ✔️            | ✔️
|                   | Definir / atualizar o modo de escala                                                                                           | ✔️   | ✔️            | ✔️
|                   | Renderizar fluxo de vídeo remoto                                                                                          | ✔️   | ✔️            | ✔️

## <a name="calling-sdk-streaming-support"></a>Chamando suporte de streaming SDK
Os Serviços de Comunicação Que Ligam A SDK suporta as seguintes configurações de streaming:

| Limite          |Web | Android/iOS|
|-----------|----|------------|
|**# de fluxos de saída que podem ser enviados simultaneamente** |1 partilha de vídeo ou 1 ecrã | 1 vídeo + 1 partilha de ecrã|
|**# de fluxos de entrada que podem ser renderizados simultaneamente** |1 partilha de vídeo ou 1 ecrã| 6 vídeo + 1 partilha de ecrã |

## <a name="calling-sdk-timeouts"></a>Chamando os intervalos da SDK

Aplicam-se os seguintes intervalos de tempo aos Serviços de Comunicação que chamam os SDKs:

| Ação           | Tempo limite em segundos |
| -------------- | ---------- |
| Religação/participante da remoção | 120 |
| Adicione ou remova nova modalidade de uma chamada (Iniciar/parar de vídeo ou partilha de ecrã) | 40 |
| Tempo limite de operação de transferência de chamadas | 60 |
| 1:1 chamada de estabelecimento tempo limite | 85 |
| Tempo limite de estabelecimento de chamada de grupo | 85 |
| PSTN chamada tempo limite de estabelecimento | 115 |
| Promover 1:1 chamada para um tempo limite de chamada em grupo | 115 |

## <a name="javascript-calling-sdk-support-by-os-and-browser"></a>Suporte SDK de chamada javaScript por OS e browser

A tabela seguinte representa o conjunto de navegadores suportados que estão atualmente disponíveis. Apoiamos as três versões mais recentes do navegador, salvo indicação em contrário.

| Plataforma                         | Chrome | Safari*  | Borda (Cromo) |
| -------------------------------- | -------| ------  | --------------  |
| Android                          |  ✔️    | ❌     | ❌             |
| iOS                              |  ❌    | ✔️**** | ❌             |
| macOS***                         |  ✔️    | ✔️**   | ❌             |
| Janelas***                       |  ✔️    | ❌     | ✔️             |
| Ubuntu/Linux                     |  ✔️    | ❌     | ❌             |

*As versões Safari 13.1+ são suportadas, chamadas 1:1 não são suportadas no Safari.

**Safari 14+/macOS 11+ necessário para suporte de vídeo de saída.

A partilha de ecrãs de saída é suportada apenas em plataformas de desktop (Windows, macOS e Linux), independentemente da versão do navegador, e não é suportada em nenhuma plataforma móvel (Android, iOS, iPad e tablets).

Uma aplicação iOS no Safari não consegue enumerar/selecionar dispositivos de microfone e altifalante (por exemplo, Bluetooth); esta é uma limitação do SO, e há sempre apenas um dispositivo.


## <a name="calling-client---browser-security-model"></a>Cliente chamado - modelo de segurança do navegador

### <a name="user-webrtc-over-https"></a>WebRTC do utilizador sobre HTTPS

As APIs da WebRTC `getUserMedia` exigem que a aplicação que chama estas APIs seja servida em HTTPS.

Para o desenvolvimento local, pode `http://localhost` utilizar.

### <a name="embed-the-communication-services-calling-sdk-in-an-iframe"></a>Incorporar os serviços de comunicação chamando SDK em um iframe

Uma nova [política de permissões (também chamada de política de recursos)](https://www.w3.org/TR/permissions-policy-1/#iframe-allow-attribute) está a ser adotada por vários navegadores. Esta política afeta os cenários de chamada, controlando como as aplicações podem aceder à câmara e microfone de um dispositivo através de um elemento de iframe de origem cruzada.

Se pretender utilizar um iframe para hospedar parte da app a partir de um domínio diferente, deve adicionar o `allow` atributo com o valor correto ao seu iframe.

Por exemplo, este iframe permite o acesso tanto à câmara como ao microfone:

```html
<iframe allow="camera *; microphone *">
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começa com a chamada](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Para obter mais informações, veja os seguintes artigos:
- Familiarize-se com fluxos de [chamadas gerais](../call-flows.md)
- Saiba mais sobre [tipos de chamadas](../voice-video-calling/about-call-types.md)
- [Planeie a sua solução PSTN](../telephony-sms/plan-solution.md)
