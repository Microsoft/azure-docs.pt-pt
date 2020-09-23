---
title: Visão geral da biblioteca do cliente POR SMS para serviços de comunicação Azure
titleSuffix: An Azure Communication Services concept document
description: Fornece uma visão geral da biblioteca do cliente SMS e suas ofertas.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 2d81749e7023bdbf5353e5c8da633674ea8e8ce9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947404"
---
# <a name="sms-client-library-overview"></a>Visão geral da biblioteca do cliente POR SMS

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

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
| Número de Telefone      | Números gratuitos                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| Regulação        | Manipulação de Opt-Out                                                                      | ✔️   | ✔️    | ✔️    | ✔️      |
| Monitorização        | Monitorize o uso das mensagens enviadas e recebidas                                          | ✔️   | ✔️    | ✔️    | ✔️      |
| Chamada PSTN      | Adicione capacidades de chamada PSTN ao seu número de telefone ativado por SMS                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começar com o envio de SMS](../../quickstarts/telephony-sms/send.md)

Os seguintes documentos podem ser interessantes para si:

- Familiarize-se com [conceitos gerais de SMS](../telephony-sms/concepts.md)
- Obtenha um [número de telefone](../../quickstarts/telephony-sms/get-phone-number.md) capaz de SMS
- [Planeie a sua solução SMS](../telephony-sms/plan-solution.md)