---
title: Arquitetura de cliente e servidor
titleSuffix: An Azure Communication Services concept document
description: Conheça a arquitetura dos Serviços de Comunicação.
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 74a48b05e7e3b00d331da4fcf66092490ae70374
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103490675"
---
# <a name="client-and-server-architecture"></a>Arquitetura de Clientes e Servidores

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed:
> - Need to add security best practices for token management here
> - Reference docs:
> - https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/create-a-token-object
> - https://docs.microsoft.com/azure/aks/operator-best-practices-identity
> - https://docs.microsoft.com/cloud-app-security/api-tokens?view=gestures-1.0-->

Todas as aplicações dos Serviços de Comunicação Azure terão aplicações de **clientes** que utilizam **serviços** para facilitar a conectividade pessoa-a-pessoa. Esta página ilustra elementos arquitetónicos comuns em vários cenários.

## <a name="user-access-management"></a>Gestão de acesso ao utilizador

As bibliotecas de clientes dos Serviços de Comunicação Azure exigem `user access tokens` o acesso aos recursos dos Serviços de Comunicação de forma segura. `User access tokens` deve ser gerado e gerido por um serviço de confiança devido à natureza sensível do token e à cadeia de ligação necessária para os gerar. A não gestão adequada dos tokens de acesso pode resultar em encargos adicionais devido ao uso indevido de recursos. É altamente recomendado fazer uso de um serviço de confiança para a gestão do utilizador. O serviço de confiança gerará os tokens e os devolverá ao cliente usando encriptação adequada. Um fluxo de arquitetura de amostra pode ser encontrado abaixo:

:::image type="content" source="../media/scenarios/archdiagram-access.png" alt-text="Diagrama mostrando o acesso do utilizador à arquitetura simbólica.":::

Para análise de informação [adicional, as melhores práticas de gestão de identidade](../../security/fundamentals/identity-management-best-practices.md)

## <a name="browser-communication"></a>Comunicação do navegador

As bibliotecas de clientes Azure Communications JavaScript podem permitir aplicações web com rica interação de texto, voz e vídeo. A aplicação interage diretamente com os Serviços de Comunicação Azure através da biblioteca do cliente para aceder ao plano de dados e fornecer comunicação de texto, voz e vídeo em tempo real. Um fluxo de arquitetura de amostra pode ser encontrado abaixo:

:::image type="content" source="../media/scenarios/archdiagram-browser.png" alt-text="Diagrama mostrando o navegador para navegador Arquitetura para Serviços de Comunicação.":::

## <a name="native-app-communication"></a>Comunicação de aplicativos nativo

Muitos cenários são melhor servidos com aplicações nativas. O Azure Communication Services suporta tanto a comunicação de navegador-para-app como a comunicação app-to-app.  Ao construir uma experiência de aplicação nativa, ter notificações push permitirá que os utilizadores recebam chamadas mesmo quando a aplicação não está em execução. Os Serviços de Comunicação Azure facilitam as notificações de push integradas ao Google Firebase, ao Serviço de Notificações Apple Push e às Notificações push do Windows. Um fluxo de arquitetura de amostra pode ser encontrado abaixo:

:::image type="content" source="../media/scenarios/archdiagram-app.png" alt-text="Diagrama mostrando Arquitetura de Serviços de Comunicação para comunicação de aplicações nativas.":::

## <a name="voice-and-sms-over-the-public-switched-telephony-network-pstn"></a>Voz e SMS sobre a rede pública de telefonia comutado (PSTN)

Comunicar através do sistema telefónico pode aumentar drasticamente o alcance da sua aplicação. Para suportar cenários de voz e SMS pstn, os Serviços de Comunicação Azure ajudam-no a [adquirir números](../quickstarts/telephony-sms/get-phone-number.md) de telefone diretamente do portal Azure ou a utilizar APIs de REST e bibliotecas de clientes. Uma vez adquiridos os números de telefone, podem ser utilizados para chegar a clientes usando tanto a chamada PSTN como a SMS em cenários de entrada e saída. Um fluxo de arquitetura de amostra pode ser encontrado abaixo:

> [!Note]
> Durante a pré-visualização pública, o fornecimento de números de telefone dos EUA está disponível para clientes com endereços de faturação localizados nos EUA e Canadá.

:::image type="content" source="../media/scenarios/archdiagram-pstn.png" alt-text="Diagrama mostrando a arquitetura PSTN dos Serviços de Comunicação.":::

Para obter mais informações sobre os números de telefone pstn, consulte [os tipos de números de telefone](../concepts/telephony-sms/plan-solution.md)

## <a name="humans-communicating-with-bots-and-other-services"></a>Humanos comunicando com bots e outros serviços

A Azure Communication Services suporta a comunicação humano-sistema apesar dos canais de texto e voz, com serviços que acedem diretamente ao plano de dados dos Serviços de Comunicação Azure. Por exemplo, você pode ter um bot atender chamadas telefónicas recebidas ou participar em um chat web. A Azure Communication Services fornece bibliotecas de clientes que permitem estes cenários para ligar e conversar. Um fluxo de arquitetura de amostra pode ser encontrado abaixo:

:::image type="content" source="../media/scenarios/archdiagram-bot.png" alt-text="Diagrama mostrando a arquitetura bot dos Serviços de Comunicação.":::

## <a name="networking"></a>Rede

Pode querer trocar dados arbitrários entre os utilizadores, por exemplo, para sincronizar uma realidade mista partilhada ou uma experiência de jogo. O plano de dados em tempo real utilizado para comunicação de texto, voz e vídeo está disponível diretamente para si de duas formas:

- **Chamar a biblioteca de clientes** - Os dispositivos numa chamada têm acesso a APIs para envio e receção de dados pelo canal de chamadas. Esta é a forma mais fácil de adicionar comunicação de dados a uma interação existente.
- **STUN/TURN** - Azure Communication Services disponibiliza os serviços STUN e TURN compatíveis com as normas. Isto permite-lhe construir uma camada de transporte fortemente personalizada em cima destes primitivos padronizados. Pode autoriar o seu próprio cliente em conformidade com padrões ou utilizar bibliotecas de código aberto, como [o WinRTC.](https://github.com/microsoft/winrtc)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criação de fichas de acesso ao utilizador](../quickstarts/access-tokens.md)

Para obter mais informações, veja os seguintes artigos:

- Saiba mais [sobre a autenticação](../concepts/authentication.md)
- Saiba mais sobre [os tipos de números de telefone](../concepts/telephony-sms/plan-solution.md)

- [Adicionar o chat à aplicação](../quickstarts/chat/get-started.md)
- [Adicionar chamadas de voz à sua aplicação](../quickstarts/voice-video-calling/getting-started-with-calling.md)
