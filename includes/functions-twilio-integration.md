---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67184060"
---
Esta amostra envolve a utilização do serviço [Twilio](https://www.twilio.com/) para enviar mensagens SMS para um telemóvel. As Funções Azure já têm suporte para twilio através da [ligação Twilio](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio), e a amostra utiliza essa funcionalidade.

A primeira coisa que precisa é de uma conta Twilio. Pode criar um https://www.twilio.com/try-twiliolivre em . Assim que tiver uma conta, adicione as seguintes três definições de **aplicação** à sua aplicação de função.

| Nome de definição de aplicativo | Descrição do valor |
| - | - |
| **TwilioAccountSid**  | O SID para a sua conta Twilio |
| **TwilioAuthToken**   | O símbolo de Auth para a sua conta Twilio |
| **TwilioPhoneNumber** | O número de telefone associado à sua conta Twilio. Isto é usado para enviar mensagens SMS. |