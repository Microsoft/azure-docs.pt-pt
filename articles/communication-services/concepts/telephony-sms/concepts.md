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
ms.openlocfilehash: bdba05dd75b6b6f4e32bf3f536e794e568fa13d7
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642667"
---
# <a name="sms-concepts"></a>Conceitos de SMS
[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Os Serviços de Comunicação Azure permitem-lhe enviar e receber mensagens de texto SMS utilizando os Serviços de Comunicação SMS SDKs. Estes SDKs podem ser usados para suportar cenários de atendimento ao cliente, lembretes de marcação, autenticação de dois fatores e outras necessidades de comunicação em tempo real. O SMS dos Serviços de Comunicação permite-lhe enviar mensagens de forma fiável enquanto expõe as métricas de entrega e resposta.

As principais características dos Serviços de Comunicação Azure, SMS SDKs incluem:

-  **Experiência de** configuração simples para adicionar capacidade de SMS às suas aplicações.
- **Suporte de mensagens de alta velocidade** sobre números gratuitos para A2P (Aplicação a Pessoa) utiliza casos nos Estados Unidos.
- **Mensagens em massa** suportadas para permitir o envio de mensagens a vários destinatários de cada vez.
- **Conversas bidirecionais** para apoiar cenários como apoio ao cliente, alertas e lembretes de marcação.
- **Entrega fiável** com relatórios de entrega em tempo real para mensagens enviadas a partir da sua aplicação.
- **Analytics** para rastrear os seus padrões de utilização de SMS.
- **Opt-Out** handling support para detetar e respeitar automaticamente opt-outs para números gratuitos. Os opt-outs para números gratuitos dos EUA são mandatados e aplicados pelas transportadoras americanas.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começar com o envio de SMS](../../quickstarts/telephony-sms/send.md)

Os seguintes documentos podem ser interessantes para si:

- Familiarize-se com o [SMS SDK](../telephony-sms/sdk-features.md)
- Obtenha um [número de telefone](../../quickstarts/telephony-sms/get-phone-number.md) capaz de SMS
- [Tipos de números de telefone nos Serviços de Comunicação Azure](../telephony-sms/plan-solution.md)
