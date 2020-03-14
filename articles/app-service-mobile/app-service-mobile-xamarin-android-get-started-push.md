---
title: Adicionar notificações push à aplicação Xamarin.Android
description: Saiba como utilizar o Azure App Service e o Azure Notification Hubs para enviar notificações push para a sua aplicação Xamarin.Android.
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 5657be0dbaeb46f8f899a9b4a2f8ba9b4fe9ebaa
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249311"
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Adicionar notificações push à aplicação Xamarin.Android

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Descrição geral

Neste tutorial, adiciona notificações push ao projeto [de arranque rápido Xamarin.Android](app-service-mobile-windows-store-dotnet-get-started.md) para que seja enviada uma notificação push para o dispositivo sempre que um registo é inserido.

Se não utilizar o projeto de servidor quickstart descarregado, necessitará do pacote de extensão de notificação push. Para mais informações, consulte o [Trabalho com o servidor de backend .NET SDK para o guia de aplicações móveis Azure.](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer a configuração:

* Uma conta ativa do Google. Pode inscrever-se numa conta do Google em [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).
* Componente do cliente do [Google Cloud Messaging Client](https://components.xamarin.com/view/GCMClient/).

## <a name="configure-hub"></a>Configure um Centro de Notificação

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Ativar mensagens cloud base de fogo

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Configure Azure para enviar pedidos de push

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a id="update-server"></a>Atualize o projeto do servidor para enviar notificações push

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a id="configure-app"></a>Configure o projeto do cliente para notificações push

[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>Adicione código de notificações push à sua aplicação

[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Teste notificações push na sua aplicação

Pode testar a aplicação utilizando um dispositivo virtual no emulador. Existem passos de configuração adicionais necessários quando se executa um emulador.

1. O dispositivo virtual deve ter as APIs do Google definidas como o alvo no gestor de Dispositivo Virtual Android (AVD).

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Adicione uma conta google ao dispositivo Android clicando em **Apps** > **Definições** > **Adicionar conta,** em seguida, siga as indicações.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Execute a aplicação todolista como antes e insira um novo item todoo. Desta vez, é apresentado um ícone de notificação na área de notificação. Pode abrir a gaveta de notificação para ver o texto completo da notificação.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: https://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: https://components.xamarin.com/view/azure-mobile-services/
