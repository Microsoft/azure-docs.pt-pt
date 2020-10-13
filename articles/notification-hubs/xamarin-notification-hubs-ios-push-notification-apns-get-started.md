---
title: Envie notificações push para Xamarin usando Azure Notification Hubs Microsoft Docs
description: Neste tutorial, irá aprender a utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação Xamarin.iOS.
services: notification-hubs
keywords: notificações push ios, mensagens push, notificações push, mensagem push
documentationcenter: xamarin
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 07/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 05/23/2019
ms.openlocfilehash: 165d6c2578ba9ec0c939e4f1c1eaa497c9dff24d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88998259"
---
# <a name="tutorial-send-push-notifications-to-xamarinios-apps-using-azure-notification-hubs"></a>Tutorial: Enviar notificações push para aplicações Xamarin.iOS usando Hubs de Notificação Azure

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Descrição geral

Este tutorial mostra como utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação iOS. Cria uma aplicação em branco Xamarin.iOS que recebe notificações push utilizando o [serviço de notificação apple push (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Quando tiver terminado, pode utilizar o Hub de Notificação para difundir notificações push para todos os dispositivos a executar a sua aplicação. O código concluído está disponível no exemplo [aplicação NotificationHubs][GitHub].

Neste tutorial, pode criar/atualizar código para efetuar as seguintes tarefas:

> [!div class="checklist"]
> * Gerar o ficheiro de solicitação de assinatura de certificado
> * Registar a aplicação para notificações push
> * Criar um perfil de aprovisionamento para a aplicação
> * Configurar o Hub de Notificação para notificações push do iOS
> * Enviar notificações push de teste

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura Azure**. Se não tiver uma subscrição do Azure, [crie uma conta Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Versão mais recente do [Xcode][Install Xcode]
* Um dispositivo compatível iOS 10 (ou versão posterior)
* Associação ao [Programa de Programador da Apple](https://developer.apple.com/programs/)
* [Visual Studio para Mac]
  
  > [!NOTE]
  > Devido aos requisitos de configuração para as notificações push do iOS, deve implementar e testar a aplicação de exemplo num dispositivo iOS físico (iPhone ou iPad) em vez do simulador.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Hubs de Notificação para aplicações Xamarin.iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-app-to-the-notification-hub"></a>Ligar a aplicação ao Notification Hub

### <a name="create-a-new-project"></a>Criar um novo projeto

1. No Visual Studio, crie um novo projeto do iOS e selecione o modelo **Aplicação de Vista Única** e clique em **Seguinte**

     ![Visual Studio - Selecione o Tipo de Aplicação][31]

2. Insira o seu identificador de Nome de Aplicação e Organização, em seguida, clique em **Seguinte,** em seguida, **Crie**

3. Na vista Solução, faça duplo clique em *Info.plist* e, em **Identidade**, certifique-se de que o Identificador do Pacote corresponde ao utilizado aquando da criação do seu perfil de aprovisionamento. Em **Assinatura** certifique-se de que a sua conta de Programador está selecionada em **Equipa**, que "Gerir assinatura automaticamente" está selecionado e que o certificado de assinatura e perfil de aprovisionamento são automaticamente selecionados.

    ![Configuração de Aplicação do Visual Studio-iOS][32]

4. A partir da vista Solução, clique duas vezes no `Entitlements.plist` e certifique-se de que as notificações push são **verificadas.**

    ![Configuração de Elegibilidade do Visual Studio-iOS][33]

5. Adicione o pacote de Mensagens do Azure. Na vista Solução, clique com o botão direito no projeto e **selecione Adicionar**  >  **Pacotes Add NuGet**. Procure **Xamarin.Azure.NotificationHubs.iOS** e adicione o pacote ao projeto.

6. Adicione um novo ficheiro à sua classe, nomeie-o `Constants.cs` e adicione as seguintes variáveis e substitua os espaços reservados literais de cordas pelo `hubname` e o `DefaultListenSharedAccessSignature` anotado anteriormente.

    ```csharp
    // Azure app-specific connection string and hub path
    public const string ListenConnectionString = "<Azure DefaultListenSharedAccess Connection String>";
    public const string NotificationHubName = "<Azure Notification Hub Name>";
    ```

7. Em `AppDelegate.cs` , adicione a seguinte declaração de utilização:

    ```csharp
    using WindowsAzure.Messaging;
    using UserNotifications
    ```

8. Declarar um exemplo `SBNotificationHub` de:

    ```csharp
    private SBNotificationHub Hub { get; set; }
    ```

9. Em `AppDelegate.cs` , atualizar para corresponder ao seguinte `FinishedLaunching()` código:

    ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
        {
            UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert | UNAuthorizationOptions.Badge | UNAuthorizationOptions.Sound,
                                                                    (granted, error) => InvokeOnMainThread(UIApplication.SharedApplication.RegisterForRemoteNotifications));
        }
        else if (UIDevice.CurrentDevice.CheckSystemVersion(8, 0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
                    UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                    new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
        }
        else
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes);
        }

        return true;
    }
    ```

10. Em `AppDelegate.cs` , sobrepor o `RegisteredForRemoteNotifications()` método:

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
    {
        Hub = new SBNotificationHub(Constants.ListenConnectionString, Constants.NotificationHubName);

        Hub.UnregisterAll (deviceToken, (error) => {
            if (error != null)
            {
                System.Diagnostics.Debug.WriteLine("Error calling Unregister: {0}", error.ToString());
                return;
            }

            NSSet tags = null; // create tags if you want
            Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                if (errorCallback != null)
                    System.Diagnostics.Debug.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
            });
        });
    }
    ```

11. Em `AppDelegate.cs` , sobrepor o `ReceivedRemoteNotification()` método:

    ```csharp
    public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
    {
        ProcessNotification(userInfo, false);
    }
    ```

12. Em `AppDelegate.cs` , criar o `ProcessNotification()` método:

    ```csharp
    void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
    {
        // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
        if (null != options && options.ContainsKey(new NSString("aps")))
        {
            //Get the aps dictionary
            NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;

            //Extract the alert text
            // NOTE: If you're using the simple alert by just specifying
            // "  aps:{alert:"alert msg here"}  ", this will work fine.
            // But if you're using a complex alert with Localization keys, etc.,
            // your "alert" object from the aps dictionary will be another NSDictionary.
            // Basically the JSON gets dumped right into a NSDictionary,
            // so keep that in mind.
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //If this came from the ReceivedRemoteNotification while the app was running,
            // we of course need to manually process things like the sound, badge, and alert.
            if (!fromFinishedLaunching)
            {
                //Manually show an alert
                if (!string.IsNullOrEmpty(alert))
                {
                    var myAlert = UIAlertController.Create("Notification", alert, UIAlertControllerStyle.Alert);
                    myAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
                    UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(myAlert, true, null);
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Pode optar por substituir `FailedToRegisterForRemoteNotifications()` para lidar com situações como a não ligação à rede. Isto é especialmente importante quando o utilizador inicia a aplicação no modo offline (por exemplo, Avião) e pretende processar cenários de mensagens push específicos para a sua aplicação.

13. Execute o aplicativo no seu dispositivo.

## <a name="send-test-push-notifications"></a>Enviar notificações push de teste

Pode testar a receção das notificações na aplicação com a opção *Envio de Teste* no [portal do Azure]. Esta ação envia uma notificação push de teste para o dispositivo.

![Portal do Azure – Envio de Teste][30]

Normalmente, as notificações push são enviadas num serviço de back-end como Mobile Apps ou ASP.NET com uma biblioteca compatível. Caso não esteja disponível uma biblioteca para o back-end, também pode utilizar a API REST diretamente para enviar mensagens de notificação.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, enviou notificações de difusão para todos os dispositivos iOS registados no back-end. Para saber como enviar notificações push para dispositivos iOS específicos, avance para o tutorial seguinte:

> [!div class="nextstepaction"]
>[Enviar notificações push para dispositivos específicos](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png
[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png
[33]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-entitlements-settings.png

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio para Mac]: https://visualstudio.microsoft.com/vs/mac/
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Apple Push Notification Service fwlink]: https://go.microsoft.com/fwlink/p/?LinkId=272584
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Portal do Azure]: https://portal.azure.com
