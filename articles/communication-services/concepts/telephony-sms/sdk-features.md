---
title: Visão geral da biblioteca do cliente POR SMS para serviços de comunicação Azure
titleSuffix: An Azure Communication Services concept document
description: Fornece uma visão geral da biblioteca do cliente SMS e suas ofertas.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e4518fb41c839d83c59cd245b39c547ddb02b68e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658219"
---
# <a name="sms-client-library-overview"></a>Descrição geral da biblioteca de cliente de SMS

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

As bibliotecas de clientes de serviços de comunicação Azure podem ser usadas para adicionar mensagens SMS às suas aplicações.

## <a name="sms-client-library-capabilities"></a>Capacidades de biblioteca de clientes POR SMS

A lista a seguir apresenta o conjunto de funcionalidades que estão atualmente disponíveis nas bibliotecas dos nossos clientes.

| Grupo de funcionalidades | Funcionalidade                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| Capacidades do Núcleo | Enviar e receber mensagens SMS </br> *Emojis unicode suportados*                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Receber Relatórios de Entrega para mensagens enviadas                                            | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Todos os conjuntos de caracteres (suporte linguístico/unicode)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Suporte para mensagens longas (até 2048 char)                                           | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Auto-concatenação de mensagens longas                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
| Eventos            | Use a Grelha de Eventos para configurar webhooks para receber mensagens de entrada e relatórios de entrega | ✔️   | ✔️    | ✔️    | ✔️      |
| Número de Telefone      | Toll-Free números                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| Regulação        | Opt-Out Manuseamento                                                                      | ✔️   | ✔️    | ✔️    | ✔️      |
| Monitorização        | Monitorize o uso das mensagens enviadas e recebidas                                          | ✔️   | ✔️    | ✔️    | ✔️      |
| Chamada PSTN      | Adicione as capacidades de chamada PSTN ao seu número gratuito com sms                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começar com o envio de SMS](../../quickstarts/telephony-sms/send.md)

Os seguintes documentos podem ser interessantes para si:

- Familiarize-se com [conceitos gerais de SMS](../telephony-sms/concepts.md)
- Obtenha um [número de telefone](../../quickstarts/telephony-sms/get-phone-number.md) capaz de SMS
- [Tipos de números de telefone nos Serviços de Comunicação Azure](../telephony-sms/plan-solution.md)
