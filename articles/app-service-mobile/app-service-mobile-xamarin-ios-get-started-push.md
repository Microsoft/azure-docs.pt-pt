---
title: Adicione notificações push à sua aplicação Xamarin.iOS
description: Saiba como utilizar o Serviço de Aplicações Azure para enviar notificações push para a sua aplicação Xamarin.iOS.
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: f9c70491d06f61931ebabda859ff3a86ed035b44
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461356"
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Adicione notificações push à sua App Xamarin.iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Descrição geral

Neste tutorial, adiciona notificações push ao projeto [de arranque rápido Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md) para que seja enviada uma notificação push para o dispositivo sempre que um registo é inserido.

Se não utilizar o projeto de servidor de arranque rápido descarregado, necessitará do pacote de extensão de notificação push. Consulte [o Trabalho com o servidor de backend .NET SDK para aplicações móveis Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter mais informações.

## <a name="prerequisites"></a>Pré-requisitos

* Complete o tutorial [de arranque rápido Xamarin.iOS.](app-service-mobile-xamarin-ios-get-started.md)
* Um dispositivo físico iOS. As notificações push não são suportadas pelo simulador iOS.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registe a aplicação para notificações push no portal de desenvolvimento da Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Configure a sua Aplicação Móvel para enviar notificações push

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Atualize o projeto do servidor para enviar notificações push

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Configure o seu projeto Xamarin.iOS

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Adicionar notificações push à aplicação

1. No **QSTodoService,** adicione o seguinte imóvel para que o **AppDelegate** possa adquirir o cliente móvel:

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

2. Adicione a seguinte declaração `using` ao topo do ficheiro **AppDelegate.cs.**

    ```csharp
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

3. No **AppDelegate,** anule o evento **Delançamento Final:**

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

4. No mesmo ficheiro, anula o evento `RegisteredForRemoteNotifications`. Neste código está a registar-se para uma simples notificação de modelo que será enviada através de todas as plataformas suportadas pelo servidor.

    Para obter mais informações sobre modelos com Centros de Notificação, consulte [Modelos](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

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

5. Em seguida, anular o evento **DidReceivedRemoteNotification:**

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

A sua aplicação está agora atualizada para suportar notificações push.

## <a name="test"></a>Teste notificações push na sua aplicação

1. Pressione o botão **Executar** para construir o projeto e inicie a aplicação num dispositivo capaz de iOS e, em seguida, clique em **OK** para aceitar notificações push.

   > [!NOTE]
   > Deve aceitar explicitamente notificações push da sua aplicação. Este pedido ocorre apenas na primeira vez que a aplicação corre.

2. Na aplicação, digite uma tarefa e clique no ícone plus **(+).**
3. Verifique se uma notificação é recebida e, em seguida, clique em **OK** para rejeitar a notificação.
4. Repita o passo 2 e feche imediatamente a aplicação e verifique se é mostrada uma notificação.

Completou com sucesso este tutorial.
