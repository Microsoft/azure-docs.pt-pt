---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184060"
---
Esta amostra envolve a utilização do serviço [Twilio](https://www.twilio.com/) para enviar mensagens SMS para um telemóvel. A Azure Functions já conta com suporte para twilio através da [ligação Twilio](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio), e a amostra utiliza essa funcionalidade.

A primeira coisa que precisas é de uma conta Twilio. Pode criar um grátis em https://www.twilio.com/try-twilio . Assim que tiver uma conta, adicione as seguintes três **definições de aplicação** à sua aplicação de função.

| Nome de definição de aplicativo | Descrição do valor |
| - | - |
| **TwilioAccountsid**  | O SID para a sua conta Twilio |
| **TwilioAuthToken**   | O símbolo de Auth para a sua conta Twilio |
| **TwilioPhoneNumber** | O número de telefone associado à sua conta Twilio. Isto é usado para enviar mensagens SMS. |