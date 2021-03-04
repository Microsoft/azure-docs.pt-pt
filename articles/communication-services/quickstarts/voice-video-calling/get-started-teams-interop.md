---
title: Quickstart - Equipas interop nos Serviços de Comunicação Azure
titleSuffix: An Azure Communication Services quickstart
description: Neste arranque rápido, você vai aprender a se juntar a uma reunião de equipas com o Azure Communication Calling SDK.
author: matthewrobertson
ms.author: chpalm
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: f8daed736f1d7f5ecf77a5b44f95d191eb5930f8
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120458"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>Quickstart: Junte-se à sua app de chamadas para uma reunião de equipas

> [!IMPORTANT]
> Para permitir/desativar [a interoperabilidade do inquilino das equipas,](../../concepts/teams-interop.md)preencha [este formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Inicie-se com os Serviços de Comunicação Azure ligando a sua solução de chamada às Equipas Microsoft utilizando a biblioteca de clientes JavaScript.

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/teams-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/teams-interop-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/teams-interop-ios.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma assinatura de Serviços de Comunicação, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados. Saiba mais sobre [a limpeza de recursos.](../create-communication-resource.md#clean-up-resources)

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, veja os seguintes artigos:

- Veja a nossa [amostra de herói chamando](../../samples/calling-hero-sample.md)
- Saiba mais [sobre chamar as capacidades da biblioteca do cliente](./calling-client-samples.md)
- Saiba mais sobre [como funciona a chamada](../../concepts/voice-video-calling/about-call-types.md)
