---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132538"
---
Este exemplo envolve a utilização a [Twilio](https://www.twilio.com/) serviço para enviar mensagens SMS para um telefone celular. As funções do Azure já incluem suporte para Twilio através do [Twilio enlace](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio), e o exemplo usa esse recurso.

A primeira coisa que precisa é uma conta do Twilio. Pode criar um gratuito em https://www.twilio.com/try-twilio. Assim que tiver uma conta, adicione os seguintes três **as definições da aplicação** à sua aplicação de função.

| Nome da definição de aplicação | Descrição do valor |
| - | - |
| **TwilioAccountSid**  | O SID para a sua conta do Twilio |
| **TwilioAuthToken**   | O token de autenticação para a sua conta do Twilio |
| **TwilioPhoneNumber** | O número de telefone associado à conta do Twilio. Isto é utilizado para enviar mensagens SMS. |