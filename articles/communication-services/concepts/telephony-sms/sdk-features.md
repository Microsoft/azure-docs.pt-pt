---
title: Visão geral da SMS SDK para serviços de comunicação Azure
titleSuffix: An Azure Communication Services concept document
description: Fornece uma visão geral do SMS SDK e suas ofertas.
author: mikben
manager: jken
services: azure-communication-services
ms.author: prakulka
ms.date: 03/26/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: c25dfea077510580daf2c1aab584ab9ff5caa7fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105930447"
---
# <a name="sms-sdk-overview"></a>Visão geral do SMS SDK

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-phone-numbers.md)]

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Os SDKs de Serviços de Comunicação Azure podem ser utilizados para adicionar mensagens SMS às suas aplicações.

## <a name="sms-sdk-capabilities"></a>Capacidades SDK de SMS

A lista a seguir apresenta o conjunto de funcionalidades que estão atualmente disponíveis nos nossos SDKs.

| Grupo de funcionalidades | Funcionalidade                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| Capacidades do Núcleo | Enviar e receber mensagens SMS                                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Ativar relatórios de entrega para mensagens enviadas                                             | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Todos os conjuntos de caracteres (suporte linguístico/unicode)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Suporte para mensagens longas (até 2048 bytes)                                          | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Auto-concatenação de mensagens longas                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Enviar mensagens a vários destinatários de cada vez                                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Apoio à idempotency                                                               | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Etiquetas personalizadas para mensagens.                                                             | ✔️   | ✔️    | ✔️    | ✔️      |
| Eventos            | Use a Grelha de Eventos para configurar webhooks para receber mensagens de entrada e relatórios de entrega | ✔️   | ✔️    | ✔️    | ✔️      |
| Número de Telefone      | Toll-Free números                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| Chamada PSTN      | Adicione as capacidades de chamada PSTN ao seu número gratuito com sms                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começar com o envio de SMS](../../quickstarts/telephony-sms/send.md)

Os seguintes documentos podem ser interessantes para si:

- Familiarize-se com [conceitos gerais de SMS](../telephony-sms/concepts.md)
- Obtenha um [número de telefone](../../quickstarts/telephony-sms/get-phone-number.md) capaz de SMS
- [Tipos de números de telefone nos Serviços de Comunicação Azure](../telephony-sms/plan-solution.md)
