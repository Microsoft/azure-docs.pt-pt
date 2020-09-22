---
title: Serviços de Comunicação Azure chamando a visão geral da biblioteca do cliente
titleSuffix: An Azure Communication Services concept document
description: Fornece uma visão geral da biblioteca do cliente chamador.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 9cc00cfa9f44c69a5880d53c0b7ac623f60be16b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948167"
---
# <a name="calling-client-library-overview"></a>Chamando a biblioteca de clientes visão geral

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Existem duas famílias separadas de bibliotecas de clientes call, para clientes e *serviços.* *services.* Atualmente, as bibliotecas de clientes disponíveis destinam-se a experiências de utilizador final: websites e aplicações nativas.

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
| Geral           | Participe numa chamada de grupo com até 350 participantes                                                       |  ✔️  | ✔️            | ✔️    
|                   | Teste o seu microfone, altifalante e câmara com um serviço de testes de áudio (disponível através do telefone 8:echo123)                   |  ✔️  | ✔️            | ✔️   

## <a name="calling-client-library-browser-support"></a>Chamando o suporte do navegador da biblioteca do cliente

A tabela a seguir representa o conjunto de navegadores e versões suportadas que estão atualmente disponíveis.

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    |
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ |
| **Chamando a biblioteca do cliente** | Chrome*, novo Edge | Chrome *, Safari** | Cromado*  | Cromado* | Cromado* | Safari** |


*Note que a versão mais recente do Chrome é suportada para além das duas versões anteriores.<br/>

**Note que as versões Safari 13.1+ são suportadas. O vídeo de saída para o safari macOS ainda não está suportado, mas é suportado no iOS. A partilha de ecrãs de saída só é suportada no iOS do ambiente de trabalho.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começa com a chamada](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Para obter mais informações, veja os seguintes artigos:
- Familiarize-se com fluxos de [chamadas gerais](../call-flows.md)
- Saiba mais sobre [tipos de chamadas](../voice-video-calling/about-call-types.md)
- [Planeie a sua solução PSTN](../telephony-sms/plan-solution.md)
