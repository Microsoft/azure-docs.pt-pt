---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b4a1891eadf2e36bcb94b9f605d91f227fa83a3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96028119"
---
Esta amostra envolve a utilização do serviço [Twilio](https://www.twilio.com/) para enviar mensagens SMS para um telemóvel. A Azure Functions já conta com suporte para twilio através da [ligação Twilio](../articles/azure-functions/functions-bindings-twilio.md), e a amostra utiliza essa funcionalidade.

A primeira coisa que precisas é de uma conta Twilio. Pode criar um grátis em https://www.twilio.com/try-twilio . Assim que tiver uma conta, adicione as seguintes três **definições de aplicação** à sua aplicação de função.

| Nome de definição de aplicativo | Descrição do valor |
| - | - |
| **TwilioAccountsid**  | O SID para a sua conta Twilio |
| **TwilioAuthToken**   | O símbolo de Auth para a sua conta Twilio |
| **TwilioPhoneNumber** | O número de telefone associado à sua conta Twilio. Isto é usado para enviar mensagens SMS. |