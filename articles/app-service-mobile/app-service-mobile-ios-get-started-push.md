---
title: Adicionar Notificações push ao iOS
description: Saiba como utilizar as Aplicações Móveis Azure para enviar notificações push para a sua aplicação iOS.
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: ad58ee158a2e1842c1b41db281bdd9f04b9b2ca4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461509"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Adicione notificações push à sua aplicação iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Descrição geral

Neste tutorial, adiciona notificações push ao projeto [quickstart do iOS] para que seja enviada uma notificação push para o dispositivo sempre que um registo é inserido.

Se não utilizar o projeto de servidor quickstart descarregado, necessitará do pacote de extensão de notificação push. Para mais informações, consulte [Trabalhar com o servidor de backend .NET SDK para o guia de aplicações móveis Azure.](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

O [simulador iOS não suporta notificações push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Você precisa de um dispositivo físico iOS e uma subscrição do [Programa de Desenvolvedores da Apple](https://developer.apple.com/programs/ios/).

## <a name="configure-notification-hub"></a><a name="configure-hub"></a>Configure Centro de Notificação

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-app-for-push-notifications"></a><a id="register"></a>Registe a aplicação para notificações push

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configure Azure para enviar notificações push

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-backend-to-send-push-notifications"></a><a id="update-server"></a>Atualizar backend para enviar notificações push

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a name="add-push-notifications-to-app"></a><a id="add-push"></a>Adicione notificações push à app

[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a name="test-push-notifications"></a><a id="test"></a>Notificações push de teste

[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a name="more"></a><a id="more"></a>Mais

* Os modelos dão-lhe flexibilidade para enviar empurrões de plataformas cruzadas e empurrões localizados. [Como utilizar a Biblioteca cliente iOS para aplicações móveis Azure](app-service-mobile-ios-how-to-use-client-library.md#templates) mostra-lhe como registar modelos.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[iOS quickstart]: app-service-mobile-ios-get-started.md
