---
title: Adicionar notificações por push ao seu aplicativo Xamarin. iOS com o serviço Azure App
description: Saiba como usar Azure App serviço para enviar notificações por push para seu aplicativo Xamarin. iOS
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
ms.openlocfilehash: 5f968de25226528cb7aa016a4c301d07e53f2695
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388397"
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Adicionar notificações por push ao seu aplicativo Xamarin. iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> O Visual Studio App Center suporta serviços de ponto a ponto e integrados, fundamentais para o desenvolvimento de aplicações móveis. Os programadores podem utilizar os serviços de **Compilação**, **Teste** e **Distribuição** para configurar o pipeline de Integração e Entrega Contínuas. Após a implementação da aplicação, os programadores podem monitorizar o estado e a utilização da aplicação através dos serviços de **Análise** e de **Diagnóstico** e interagir com os utilizadores através do serviço **Push**. Os programadores também podem tirar partido da **Autenticação** para autenticar os utilizadores e do serviço de **Dados** para manter e sincronizar os dados da aplicação na cloud.
>
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com o [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão geral

Neste tutorial, você adicionará notificações por push ao projeto de [início rápido do Xamarin. Ios](app-service-mobile-xamarin-ios-get-started.md) para que uma notificação por push seja enviada ao dispositivo toda vez que um registro for inserido.

Se você não usar o projeto baixado do servidor de início rápido, será necessário o pacote de extensão de notificação por push. Consulte [trabalhar com o SDK do servidor de back-end do .net para aplicativos móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter mais informações.

## <a name="prerequisites"></a>Pré-requisitos

* Conclua o tutorial de [início rápido do Xamarin. Ios](app-service-mobile-xamarin-ios-get-started.md) .
* Um dispositivo iOS físico. Notificações por push não são suportadas pelo simulador de iOS.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrar o aplicativo para notificações por push no portal do desenvolvedor da Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Configurar seu aplicativo móvel para enviar notificações por push

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Atualizar o projeto de servidor para enviar notificações por push

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Configurar seu projeto do Xamarin. iOS

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Adicionar notificações push à sua aplicação

1. No **QSTodoService**, adicione a seguinte propriedade para que o **AppDelegate** possa adquirir o cliente móvel:

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

2. Adicione a seguinte instrução `using` à parte superior do arquivo **AppDelegate.cs** .

    ```csharp
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

3. No **AppDelegate**, substitua o evento **FinishedLaunching** :

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

4. No mesmo arquivo, substitua o evento `RegisteredForRemoteNotifications`. Nesse código, você está se registrando para uma notificação de modelo simples que será enviada em todas as plataformas com suporte pelo servidor.

    Para obter mais informações sobre modelos com hubs de notificação, consulte [modelos](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

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

5. Em seguida, substitua o evento **DidReceivedRemoteNotification** :

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

Seu aplicativo agora está atualizado para dar suporte a notificações por push.

## <a name="test"></a>Testar notificações por push em seu aplicativo

1. Pressione o botão **executar** para compilar o projeto e iniciar o aplicativo em um dispositivo com capacidade para IOS e clique em **OK** para aceitar notificações por push.

   > [!NOTE]
   > Você deve aceitar explicitamente as notificações por push do seu aplicativo. Essa solicitação ocorre apenas na primeira vez em que o aplicativo é executado.

2. No aplicativo, digite uma tarefa e, em seguida, clique no ícone de adição ( **+** ).
3. Verifique se uma notificação foi recebida e clique em **OK** para ignorar a notificação.
4. Repita a etapa 2 e feche imediatamente o aplicativo e, em seguida, verifique se uma notificação é mostrada.

Você concluiu este tutorial com êxito.
