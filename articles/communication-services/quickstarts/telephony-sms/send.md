---
title: Quickstart - Envie uma mensagem SMS
titleSuffix: An Azure Communication Services quickstart
description: Saiba como enviar uma mensagem SMS utilizando os Serviços de Comunicação Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-js
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 52cec861ba81a804081c4b879ab73333a3281e61
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360297"
---
# <a name="quickstart-send-an-sms-message"></a>Quickstart: Enviar uma mensagem SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

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

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, aprendeu a enviar mensagens SMS utilizando os Serviços de Comunicação Azure.

> [!div class="nextstepaction"]
> [Subscreva os Eventos SMS](./handle-sms-events.md)

> [!div class="nextstepaction"]
> [Planeie a sua Solução PSTN](../../concepts/telephony-sms/plan-solution.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre SMS](../../concepts/telephony-sms/concepts.md)