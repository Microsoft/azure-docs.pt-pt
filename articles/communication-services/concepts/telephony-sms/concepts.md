---
title: Conceitos de SMS nos Serviços de Comunicação Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre os conceitos de SMS dos Serviços de Comunicação.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: c866629677790447d0ed730ae9cc62cb37e338e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103495509"
---
# <a name="sms-concepts"></a>Conceitos de SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Os Serviços de Comunicação Azure permitem-lhe enviar e receber mensagens de texto SMS utilizando as bibliotecas de clientes SMS dos Serviços de Comunicação. Estas bibliotecas de clientes podem ser usadas para suportar cenários de atendimento ao cliente, lembretes de marcação, autenticação de dois fatores e outras necessidades de comunicação em tempo real. O SMS dos Serviços de Comunicação permite-lhe enviar mensagens de forma fiável enquanto expõe insights de entrega e taxa de resposta em torno das suas campanhas.

As principais características das bibliotecas de clientes dos Serviços de Comunicação Azure incluem:

-  **Experiência de** configuração simples para adicionar capacidade de SMS às suas aplicações.
- **Suporte de mensagens de alta velocidade** sobre números gratuitos para A2P (Aplicação a Pessoa) utiliza casos nos Estados Unidos.
- **Conversas bidirecionais** para apoiar cenários como apoio ao cliente, alertas e lembretes de marcação.
- **Entrega fiável** com relatórios de entrega em tempo real para mensagens enviadas a partir da sua aplicação.
- **Analytics** para acompanhar os seus padrões de utilização e envolvimento com o cliente.
- **Opt-Out** handling support para detetar e respeitar automaticamente opt-outs para números gratuitos. Os opt-outs para números gratuitos dos EUA são mandatados e aplicados pelas transportadoras americanas.
  - STOP - Se um destinatário de mensagens de texto quiser optar por não optar, pode enviar 'STOP' para o número gratuito. A transportadora envia a seguinte resposta por defeito para STOP: *"NETWORK MSG: Respondeu com a palavra "stop" que bloqueia todos os textos enviados a partir deste número. Envie um sms para "untop" para receber mensagens novamente."*
  - START/UNSTOP - Se o destinatário pretender reenviar mensagens de texto a partir de um número gratuito, pode enviar 'START' ou 'UNSTOP para o número gratuito. A transportadora envia a seguinte resposta por defeito para START/UNSTOP: *"NETWORK MSG: Respondeu "untop" e começará a receber mensagens novamente a partir deste número."*
  - Os Serviços de Comunicação Azure detetarão a mensagem STOP e bloquearão todas as mensagens adicionais ao destinatário. O relatório de entrega indicará uma entrega falhada com mensagem de estado como "Remetente bloqueado para destinatário dado."
  - As mensagens STOP, UNSTOP e START serão retransmitidas de volta para si. A Azure Communication Services encoraja-o a monitorizar e implementar estes opt-outs para garantir que não são feitas mais tentativas de envio de mensagens aos destinatários que tenham optado por não fazer as suas comunicações.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começar com o envio de SMS](../../quickstarts/telephony-sms/send.md)

Os seguintes documentos podem ser interessantes para si:

- Familiarize-se com a biblioteca do [cliente SMS](../telephony-sms/sdk-features.md)
- Obtenha um [número de telefone](../../quickstarts/telephony-sms/get-phone-number.md) capaz de SMS
- [Tipos de números de telefone nos Serviços de Comunicação Azure](../telephony-sms/plan-solution.md)
