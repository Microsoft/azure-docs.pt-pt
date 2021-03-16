---
title: Serviços de Comunicação Azure chamando a visão geral da biblioteca do cliente
titleSuffix: An Azure Communication Services concept document
description: Fornece uma visão geral da biblioteca do cliente chamador.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 110607e9da68cf131a43651b97c599873ce33cb8
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495271"
---
# <a name="calling-client-library-overview"></a>Descrição geral da biblioteca de cliente de chamadas

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


Existem duas famílias separadas de bibliotecas de clientes call, para clientes e *serviços.*  Atualmente, as bibliotecas de clientes disponíveis destinam-se a experiências de utilizador final: websites e aplicações nativas.

As bibliotecas de clientes do Serviço ainda não estão disponíveis e fornecem acesso aos aviões de dados de voz e vídeo brutos, adequados para a integração com bots e outros serviços.

## <a name="calling-client-library-capabilities"></a>Chamar capacidades de biblioteca de clientes

A lista a seguir apresenta o conjunto de funcionalidades que estão atualmente disponíveis nas bibliotecas de clientes dos Serviços de Comunicação Azure.

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



## <a name="javascript-calling-client-library-support-by-os-and-browser"></a>JavaScript chamando suporte à biblioteca de clientes por OS e browser

A tabela seguinte representa o conjunto de navegadores suportados que estão atualmente disponíveis. Apoiamos as três versões mais recentes do navegador, salvo indicação em contrário.

|                                  | Chrome | Safari*  | Borda (Cromo) | 
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

## <a name="calling-client-library-streaming-support"></a>Chamando o suporte de streaming da biblioteca do cliente
Os Serviços de Comunicação que ligam para a biblioteca de clientes suportam as seguintes configurações de streaming:

|           |Web | Android/iOS|
|-----------|----|------------|
|**# de fluxos de saída que podem ser enviados simultaneamente** |1 vídeo + 1 partilha de ecrã | 1 vídeo + 1 partilha de ecrã|
|**# de fluxos de entrada que podem ser renderizados simultaneamente** |1 vídeo + 1 partilha de ecrã| 6 vídeo + 1 partilha de ecrã |


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começa com a chamada](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Para obter mais informações, veja os seguintes artigos:
- Familiarize-se com fluxos de [chamadas gerais](../call-flows.md)
- Saiba mais sobre [tipos de chamadas](../voice-video-calling/about-call-types.md)
- [Planeie a sua solução PSTN](../telephony-sms/plan-solution.md)
