---
title: Quickstart - Envie uma mensagem SMS
titleSuffix: An Azure Communication Services quickstart
description: Saiba como enviar uma mensagem SMS utilizando os Serviços de Comunicação Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-js
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: ed246cfc3929e2893087a99b7876138859d4667a
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488336"
---
# <a name="quickstart-send-an-sms-message"></a>Quickstart: Enviar uma mensagem SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

> [!IMPORTANT]
> As mensagens SMS podem ser enviadas e recebidas a partir de números de telefone dos Estados Unidos. Os números de telefone localizados noutras geografias ainda não são suportados por SMS dos Serviços de Comunicação.
> Para obter mais informações, consulte **[os tipos de números de telefone.](../../concepts/telephony-sms/plan-solution.md)**

::: zone pivot="programming-language-csharp"
[!INCLUDE [Send SMS with .NET client library](./includes/send-sms-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Send SMS with JavaScript client library](./includes/send-sms-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Send SMS with Python client library](./includes/send-sms-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Send SMS with Java client library](./includes/send-sms-java.md)]
::: zone-end

## <a name="troubleshooting"></a>Resolução de problemas

Para resolver problemas relacionados com a entrega de SMS, pode permitir que o relatório de entrega com a [Grade de Eventos](./handle-sms-events.md) capte detalhes de entrega.

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma assinatura de Serviços de Comunicação, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados. Saiba mais sobre [a limpeza de recursos.](../create-communication-resource.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a enviar mensagens SMS utilizando os Serviços de Comunicação Azure.

> [!div class="nextstepaction"]
> [Subscreva os Eventos SMS](./handle-sms-events.md)

> [!div class="nextstepaction"]
> [Tipos de números de telefone](../../concepts/telephony-sms/plan-solution.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre SMS](../../concepts/telephony-sms/concepts.md)
