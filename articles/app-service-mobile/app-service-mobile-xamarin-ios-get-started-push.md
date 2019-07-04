---
title: Adicionar notificações push à sua aplicação xamarin. IOS com o serviço de aplicações do Azure
description: Saiba como utilizar o serviço de aplicações do Azure para enviar notificações push à sua aplicação xamarin. IOS
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 9edd5e755b4a42fe881a0863bb284039299ec713
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446275"
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Adicionar notificações push à aplicação xamarin. IOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center está a investir em serviços de novo e integrados essenciais para o desenvolvimento de aplicações móveis. Os desenvolvedores podem usar **crie**, **teste** e **distribuir** serviços para configurar os pipelines de integração e entrega contínuas. Assim que a aplicação é implementada, os programadores podem monitorizar o estado e a utilização da sua aplicação com o **Analytics** e **diagnóstico** serviços e interaja com os utilizadores que utilizam o **Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados de aplicações na cloud. Confira [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-ios-get-started-push) hoje mesmo.
>

## <a name="overview"></a>Descrição geral

Neste tutorial, adicionar notificações push para o [início rápido do xamarin. IOS](app-service-mobile-xamarin-ios-get-started.md) do projeto para que uma notificação push é enviada para o dispositivo sempre que um registro é inserido.

Se não utilizar o projeto de servidor de início rápido transferido, terá do pacote de extensão de notificação push. Ver [trabalhar com o SDK do servidor de back-end de .NET para aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter mais informações.

## <a name="prerequisites"></a>Pré-requisitos

* Concluir o [guia de introdução do xamarin. IOS](app-service-mobile-xamarin-ios-get-started.md) tutorial.
* Um dispositivo iOS físico. Notificações push não são suportadas pelo simulador do iOS.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registar a aplicação para notificações push no portal de programador da Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Configurar a sua aplicação móveis para enviar notificações push

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Atualizar o projeto de servidor para enviar notificações push

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Configurar o seu projeto xamarin. IOS

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Adicionar notificações push à aplicação

1. Na **QSTodoService**, adicione a seguinte propriedade para que **AppDelegate** pode adquirir o cliente móvel:

    ```csharp
    public MobileServiceClient GetClient {
        get
        {
            return client;
        }
        private set
        {
            client = value;
        }
    }
    ```

2. Adicione as seguintes `using` instrução na parte superior do **AppDelegate.cs** ficheiro.

    ```csharp
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

3. Na **AppDelegate**, substituir o **FinishedLaunching** eventos:

   ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // registers for push for iOS8
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

        return true;
    }
    ```

4. No mesmo ficheiro, substitua o `RegisteredForRemoteNotifications` eventos. Nesse código que está a registar para obter uma notificação de modelo simples que será enviada em todas as plataformas suportadas pelo servidor.

    Para obter mais informações sobre modelos com os Hubs de notificação, consulte [modelos](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
    {
        MobileServiceClient client = QSTodoService.DefaultService.GetClient;

        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyAPNS}
        };

        // Register for push with your mobile app
        var push = client.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

5. Em seguida, substituir a **DidReceivedRemoteNotification** eventos:

   ```csharp
    public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps [new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

A aplicação foi atualizada para suportar notificações push.

## <a name="test"></a>Notificações de push de teste na sua aplicação

1. Prima a **execute** botão para compilar o projeto e iniciar a aplicação no dispositivo iOS com capacidade para, em seguida, clique em **OK** para aceitar notificações push.

   > [!NOTE]
   > Tem de aceitar explicitamente notificações push da sua aplicação. Este pedido ocorre apenas na primeira vez que a aplicação é executada.

2. Na aplicação, escreva uma tarefa e, em seguida, clique no sinal de adição ( **+** ) ícone.
3. Certifique-se de que uma notificação é recebida, em seguida, clique em **OK** para dispensar a notificação.
4. Repita o passo 2 e imediatamente fechar a aplicação, em seguida, certifique-se de que é mostrada uma notificação.

Concluiu com êxito este tutorial.
