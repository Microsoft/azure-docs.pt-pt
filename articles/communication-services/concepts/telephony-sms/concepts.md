---
title: Conceitos de SMS nos Serviços de Comunicação Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre os conceitos de SMS dos Serviços de Comunicação.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e5cfc1e27bae10a1c67e4506afe9db825664785f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947408"
---
# <a name="sms-concepts"></a>Conceitos de SMS

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Os Serviços de Comunicação Azure permitem-lhe enviar e receber mensagens de texto SMS utilizando as bibliotecas de clientes SMS dos Serviços de Comunicação. Estas bibliotecas de clientes podem ser usadas para suportar cenários de atendimento ao cliente, lembretes de marcação, autenticação de dois fatores e outras necessidades de comunicação em tempo real. O SMS dos Serviços de Comunicação permite-lhe enviar mensagens de forma fiável enquanto expõe insights de entrega e taxa de resposta em torno das suas campanhas.

As principais características das bibliotecas de clientes dos Serviços de Comunicação Azure incluem:

-  **Experiência de** configuração simples para adicionar capacidade de SMS às suas aplicações.
- **Suporte de mensagens de alta velocidade** sobre números gratuitos para A2P (Aplicação a Pessoa) utiliza casos nos Estados Unidos.
- **Conversas bidirecionais** para apoiar cenários como apoio ao cliente, alertas e lembretes de marcação.
- **Entrega fiável** com relatórios de entrega em tempo real para mensagens enviadas a partir da sua aplicação.
- **Analytics** para acompanhar os seus padrões de utilização e envolvimento com o cliente.
- **Opt-Out** handling support para detetar e respeitar automaticamente opt-outs para números gratuitos. Os Serviços de Comunicação detetam mensagens STOP e START e enviam as seguintes respostas predefinidos aos utilizadores finais: 
  - STOP - *"Foi desinscrita com sucesso a mensagens deste número. Resposta START para reenviar."*
  - START- *"Foi ressocrito com sucesso a mensagens deste número. Responder STOP a cancelar a subscrição."*
  - As mensagens STOP e START serão retransmitidas de volta para si. A Azure Communication Services encoraja-o a monitorizar e implementar estes opt-outs para garantir que não são enviadas mais mensagens aos destinatários que tenham optado por não fazer as suas comunicações.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começar com o envio de SMS](../../quickstarts/telephony-sms/send.md)

Os seguintes documentos podem ser interessantes para si:

- Familiarize-se com a biblioteca do [cliente SMS](../telephony-sms/sdk-features.md)
- Obtenha um [número de telefone](../../quickstarts/telephony-sms/get-phone-number.md) capaz de SMS
- [Planeie a sua solução SMS](../telephony-sms/plan-solution.md)
