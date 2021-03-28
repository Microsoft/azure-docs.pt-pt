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
ms.openlocfilehash: 9514f1616c0d70d7d195af2f052c604973709204
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644302"
---
# <a name="quickstart-send-an-sms-message"></a>Quickstart: Enviar uma mensagem SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

> [!IMPORTANT]
> As mensagens SMS podem ser enviadas e recebidas a partir de números de telefone dos Estados Unidos. Os números de telefone localizados noutras geografias ainda não são suportados por SMS dos Serviços de Comunicação.
> Para obter mais informações, consulte **[os tipos de números de telefone.](../../concepts/telephony-sms/plan-solution.md)**

::: zone pivot="programming-language-csharp"
[!INCLUDE [Send SMS with .NET SDK](./includes/send-sms-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Send SMS with JavaScript SDK](./includes/send-sms-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Send SMS with Python SDK](./includes/send-sms-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Send SMS with Java SDK](./includes/send-sms-java.md)]
::: zone-end

## <a name="troubleshooting"></a>Resolução de problemas

Para resolver problemas relacionados com a entrega de SMS, pode permitir que o relatório de entrega com a [Grade de Eventos](./handle-sms-events.md) capte detalhes de entrega.

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma assinatura de Serviços de Comunicação, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados. Saiba mais sobre [a limpeza de recursos.](../create-communication-resource.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a enviar mensagens SMS utilizando os Serviços de Comunicação Azure.

> [!div class="nextstepaction"]
> [Receber SMS e Relatório de Entrega Eventos](./handle-sms-events.md)

> [!div class="nextstepaction"]
> [Tipos de números de telefone](../../concepts/telephony-sms/plan-solution.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre SMS](../../concepts/telephony-sms/concepts.md)
