---
title: Adicionar notificações por push ao aplicativo iOS com os aplicativos móveis do Azure
description: Saiba como usar os aplicativos móveis do Azure para enviar notificações por push para seu aplicativo iOS.
services: app-service\mobile
documentationcenter: ios
manager: crdun
editor: ''
author: elamalani
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 7a037cfb411eb3c15f60bb6a8763374d9102bc4e
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027316"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Adicionar notificações por push ao seu aplicativo iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> O Visual Studio App Center dá suporte a serviços de ponta a ponta e integrados central ao desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **Compilar**, **testar** e **distribuir** serviços para configurar o pipeline de integração e entrega contínua. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso de seus aplicativos usando os serviços de **análise** e **diagnóstico** e se envolver com os usuários usando o serviço de **envio por push** . Os desenvolvedores também podem aproveitar a **autenticação** para autenticar seus usuários e o serviço de **dados** para manter e sincronizar dados de aplicativos na nuvem.
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com App Center [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje.

## <a name="overview"></a>Descrição geral

Neste tutorial, você adicionará notificações por push ao projeto de [início rápido do IOS] para que uma notificação por push seja enviada ao dispositivo toda vez que um registro for inserido.

Se você não usar o projeto baixado do servidor de início rápido, será necessário o pacote de extensão de notificação por push. Para obter mais informações, consulte [trabalhar com o SDK do servidor de back-end do .net para o guia de aplicativos móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

O [simulador do IOS não oferece suporte a notificações por push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Você precisa de um dispositivo iOS físico e de uma [associação do programa de desenvolvedor da Apple](https://developer.apple.com/programs/ios/).

## <a name="configure-hub"></a>Configurar o Hub de notificação

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Registrar aplicativo para notificações por push

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurar o Azure para enviar notificações por push

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a id="update-server"></a>Atualizar back-end para enviar notificações por push

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Adicionar notificações por push ao aplicativo

[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Testar notificações por push

[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a id="more"></a>Cada

* Os modelos oferecem flexibilidade para enviar envios por push de plataforma cruzada e Pushes localizados. [Como usar a biblioteca de cliente do IOS para aplicativos móveis do Azure](app-service-mobile-ios-how-to-use-client-library.md#templates) mostra como registrar modelos.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[início rápido do iOS]: app-service-mobile-ios-get-started.md
