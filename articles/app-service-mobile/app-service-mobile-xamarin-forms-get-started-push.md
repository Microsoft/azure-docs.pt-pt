---
title: Adicionar notificações por push ao seu aplicativo Xamarin. Forms | Microsoft Docs
description: Saiba como usar os serviços do Azure para enviar notificações por push de várias plataformas para seus aplicativos Xamarin. Forms.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 8ce307df954575b3204f7a4b3f46af1f4a9c3089
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027454"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Adicionar notificações por push ao seu aplicativo Xamarin. Forms

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> O Visual Studio App Center dá suporte a serviços de ponta a ponta e integrados central ao desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **Compilar**, **testar** e **distribuir** serviços para configurar o pipeline de integração e entrega contínua. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso de seus aplicativos usando os serviços de **análise** e **diagnóstico** e se envolver com os usuários usando o serviço de **envio por push** . Os desenvolvedores também podem aproveitar a **autenticação** para autenticar seus usuários e o serviço de **dados** para manter e sincronizar dados de aplicativos na nuvem.
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com App Center [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje.

## <a name="overview"></a>Descrição geral

Neste tutorial, você adicionará notificações por push a todos os projetos que resultaram do [início rápido do Xamarin. Forms](app-service-mobile-xamarin-forms-get-started.md). Isso significa que uma notificação por push é enviada para todos os clientes de plataforma cruzada toda vez que um registro é inserido.

Se você não usar o projeto baixado do servidor de início rápido, será necessário o pacote de extensão de notificação por push. Para obter mais informações, consulte [trabalhar com o SDK do servidor de back-end do .net para aplicativos móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

Para o iOS, você precisará de uma [associação do programa de desenvolvedor da Apple](https://developer.apple.com/programs/ios/) e um dispositivo IOS físico. O [simulador do IOS não oferece suporte a notificações por push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Configurar um hub de notificação

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Atualizar o projeto de servidor para enviar notificações por push

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Configurar e executar o projeto do Android (opcional)

Conclua esta seção para habilitar notificações por push para o projeto Droid do Xamarin. Forms para Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Habilitar o firebase Cloud Messaging (FCM)

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Configurar o back-end dos aplicativos móveis para enviar solicitações por push usando FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Adicionar notificações por push ao projeto do Android

Com o back-end configurado com FCM, você pode adicionar componentes e códigos ao cliente para se registrarem com o FCM. Você também pode se registrar para notificações por push com os hubs de notificação do Azure por meio do back-end dos aplicativos móveis e receber notificações.

1. No projeto **Droid** , clique com o botão direito do mouse em **referências > gerenciar pacotes NuGet...** .
1. Na janela Gerenciador de pacotes NuGet, procure o pacote **Xamarin. firebase. Messaging** e adicione-o ao projeto.
1. Nas propriedades do projeto do projeto **Droid** , defina o aplicativo para compilar usando a versão 7,0 ou superior do Android.
1. Adicione o arquivo **Google-Services. JSON** , baixado do console do firebase, para a raiz do projeto **Droid** e defina sua ação de Build como **GoogleServicesJson**. Para obter mais informações, consulte [Adicionar o arquivo JSON dos serviços do Google](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File).

#### <a name="registering-with-firebase-cloud-messaging"></a>Registar no Firebase Cloud Messaging

1. Abra o ficheiro **AndroidManifest.xml** e insira os seguintes elementos `<receiver>` no elemento `<application>`:

    ```xml
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

#### <a name="implementing-the-firebase-instance-id-service"></a>Implementando o serviço de ID de instância firebase

1. Adicione uma nova classe ao projeto **Droid** chamado `FirebaseRegistrationService` e verifique se as seguintes instruções `using` estão presentes na parte superior do arquivo:

    ```csharp
    using System.Threading.Tasks;
    using Android.App;
    using Android.Util;
    using Firebase.Iid;
    using Microsoft.WindowsAzure.MobileServices;
    ```

1. Substitua a classe vazia `FirebaseRegistrationService` pelo seguinte código:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class FirebaseRegistrationService : FirebaseInstanceIdService
    {
        const string TAG = "FirebaseRegistrationService";

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "Refreshed token: " + refreshedToken);
            SendRegistrationTokenToAzureNotificationHub(refreshedToken);
        }

        void SendRegistrationTokenToAzureNotificationHub(string token)
        {
            // Update notification hub registration
            Task.Run(async () =>
            {
                await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
            });
        }
    }
    ```

    A classe `FirebaseRegistrationService` é responsável pela geração de tokens de segurança que autorizam o aplicativo a acessar o FCM. O método `OnTokenRefresh` é invocado quando o aplicativo recebe um token de registro de FCM. O método recupera o token da propriedade `FirebaseInstanceId.Instance.Token`, que é atualizada de forma assíncrona pelo FCM. O método `OnTokenRefresh` é invocado frequentemente, porque o token só é atualizado quando o aplicativo é instalado ou desinstalado, quando o usuário exclui dados do aplicativo, quando o aplicativo apaga a ID da instância ou quando a segurança do token foi afetados. Além disso, o serviço de ID de instância FCM solicitará que o aplicativo atualize seu token periodicamente, normalmente a cada 6 meses.

    O método `OnTokenRefresh` também invoca o método `SendRegistrationTokenToAzureNotificationHub`, que é usado para associar o token de registro do usuário ao Hub de notificação do Azure.

#### <a name="registering-with-the-azure-notification-hub"></a>Registrando com o Hub de notificação do Azure

1. Adicione uma nova classe ao projeto **Droid** chamado `AzureNotificationHubService` e verifique se as seguintes instruções `using` estão presentes na parte superior do arquivo:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Android.Util;
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

1. Substitua a classe vazia `AzureNotificationHubService` pelo seguinte código:

    ```csharp
    public class AzureNotificationHubService
    {
        const string TAG = "AzureNotificationHubService";

        public static async Task RegisterAsync(Push push, string token)
        {
            try
            {
                const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBody}
                };

                await push.RegisterAsync(token, templates);
                Log.Info("Push Installation Id: ", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
            }
        }
    }
    ```

    O método `RegisterAsync` cria um modelo de mensagem de notificação simples como JSON e registra para receber notificações de modelo do hub de notificação, usando o token de registro firebase. Isso garante que todas as notificações enviadas do hub de notificação do Azure sejam direcionadas ao dispositivo representado pelo token de registro.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Exibindo o conteúdo de uma notificação por push

1. Adicione uma nova classe ao projeto **Droid** chamado `FirebaseNotificationService` e verifique se as seguintes instruções `using` estão presentes na parte superior do arquivo:

    ```csharp
    using Android.App;
    using Android.Content;
    using Android.Media;
    using Android.Support.V7.App;
    using Android.Util;
    using Firebase.Messaging;
    ```

1. Substitua a classe vazia `FirebaseNotificationService` pelo seguinte código:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class FirebaseNotificationService : FirebaseMessagingService
    {
        const string TAG = "FirebaseNotificationService";

        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);

            // Pull message body out of the template
            var messageBody = message.Data["message"];
            if (string.IsNullOrWhiteSpace(messageBody))
                return;

            Log.Debug(TAG, "Notification message body: " + messageBody);
            SendNotification(messageBody);
        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new NotificationCompat.Builder(this)
                .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                .SetContentTitle("New Todo Item")
                .SetContentText(messageBody)
                .SetContentIntent(pendingIntent)
                .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
                .SetAutoCancel(true);

            var notificationManager = NotificationManager.FromContext(this);
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    }
    ```

    O método `OnMessageReceived`, que é invocado quando um aplicativo recebe uma notificação de FCM, extrai o conteúdo da mensagem e chama o método `SendNotification`. Esse método converte o conteúdo da mensagem em uma notificação local que é iniciada enquanto o aplicativo está em execução, com a notificação aparecendo na área de notificação.

Agora, você está pronto testar notificações por push no aplicativo em execução em um dispositivo Android ou no emulador.

### <a name="test-push-notifications-in-your-android-app"></a>Testar notificações por push em seu aplicativo Android

As duas primeiras etapas são necessárias apenas quando você está testando em um emulador.

1. Certifique-se de que você está implantando ou Depurando em um dispositivo ou emulador configurado com Google Play Services. Isso pode ser verificado verificando se os aplicativos de **reprodução** estão instalados no dispositivo ou emulador.
2. Adicione uma conta do Google ao dispositivo Android clicando em **aplicativos** > **configurações** > **adicionar conta**. Em seguida, siga os prompts para adicionar uma conta existente do Google ao dispositivo ou para criar uma nova.
3. No Visual Studio ou Xamarin Studio, clique com o botão direito do mouse no projeto **Droid** e clique em **definir como projeto de inicialização**.
4. Clique em **executar** para compilar o projeto e iniciar o aplicativo em seu dispositivo Android ou emulador.
5. No aplicativo, digite uma tarefa e, em seguida, clique no ícone de adição ( **+** ).
6. Verifique se uma notificação é recebida quando um item é adicionado.

## <a name="configure-and-run-the-ios-project-optional"></a>Configurar e executar o projeto do iOS (opcional)

Esta secção destina-se à execução do projeto iOS Xamarin para dispositivos iOS. Pode ignorar esta secção se não estiver a trabalhar com dispositivos iOS.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Configurar o Hub de notificação para APNS

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Em seguida, você configurará a configuração do projeto do iOS no Xamarin Studio ou no Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Adicionar notificações por push ao seu aplicativo iOS

1. No projeto do **Ios** , abra AppDelegate.cs e adicione a instrução a seguir à parte superior do arquivo de código.

    ```csharp
    using Newtonsoft.Json.Linq;
    ```

2. Na classe **AppDelegate** , adicione uma substituição para o evento **RegisteredForRemoteNotifications** registrar para notificações:

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application,
        NSData deviceToken)
    {
        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

        // Register for push with your mobile app
        Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

3. No **AppDelegate**, adicione também a seguinte substituição para o manipulador de eventos **DidReceiveRemoteNotification** :

    ```csharp
    public override void DidReceiveRemoteNotification(UIApplication application,
        NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps[new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

    Esse método trata as notificações de entrada enquanto o aplicativo está em execução.

4. Na classe **AppDelegate** , adicione o seguinte código ao método **FinishedLaunching** :

    ```csharp
    // Register for push notifications.
    var settings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert
        | UIUserNotificationType.Badge
        | UIUserNotificationType.Sound,
        new NSSet());

    UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications();
    ```

    Isso habilita o suporte para notificações remotas e solicitações de registro por push.

Seu aplicativo agora está atualizado para dar suporte a notificações por push.

#### <a name="test-push-notifications-in-your-ios-app"></a>Testar notificações por push em seu aplicativo iOS

1. Clique com o botão direito do mouse no projeto do iOS e clique em **definir como projeto de inicialização**.
2. Pressione o botão **executar** ou **F5** no Visual Studio para compilar o projeto e iniciar o aplicativo em um dispositivo IOS. Em seguida, clique em **OK** para aceitar notificações por push.

   > [!NOTE]
   > Você deve aceitar explicitamente as notificações por push do seu aplicativo. Essa solicitação ocorre apenas na primeira vez em que o aplicativo é executado.

3. No aplicativo, digite uma tarefa e, em seguida, clique no ícone de adição ( **+** ).
4. Verifique se uma notificação é recebida e clique em **OK** para ignorar a notificação.

## <a name="configure-and-run-windows-projects-optional"></a>Configurar e executar projetos do Windows (opcional)

Esta seção destina-se à execução dos projetos WinApp e WinPhone81 do Xamarin. Forms para dispositivos Windows. Essas etapas também dão suporte a projetos Plataforma Universal do Windows (UWP). Pode ignorar esta secção se não estiver a trabalhar com dispositivos Windows.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Registrar seu aplicativo do Windows para notificações por push com o serviço de notificação do Windows (WNS)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Configurar o Hub de notificação para WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Adicionar notificações por push ao seu aplicativo do Windows

1. No Visual Studio, abra **app.XAML.cs** em um projeto do Windows e adicione as instruções a seguir.

    ```csharp
    using Newtonsoft.Json.Linq;
    using Microsoft.WindowsAzure.MobileServices;
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    using <your_TodoItemManager_portable_class_namespace>;
    ```

    Substitua `<your_TodoItemManager_portable_class_namespace>` pelo namespace do seu projeto portátil que contém a classe `TodoItemManager`.

2. Em App.xaml.cs, adicione o seguinte método **InitNotificationsAsync** :

    ```csharp
    private async Task InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager
            .CreatePushNotificationChannelForApplicationAsync();

        const string templateBodyWNS =
            "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        JObject headers = new JObject();
        headers["X-WNS-Type"] = "wns/toast";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyWNS},
            {"headers", headers} // Needed for WNS.
        };

        await TodoItemManager.DefaultManager.CurrentClient.GetPush()
            .RegisterAsync(channel.Uri, templates);
    }
    ```

    Esse método obtém o canal de notificação por push e registra um modelo para receber notificações de modelo do hub de notificação. Uma notificação de modelo que dá suporte a *messageParam* será entregue a esse cliente.

3. No App.xaml.cs, atualize a definição do método de manipulador de eventos **Onlaunched** adicionando o modificador `async`. Em seguida, adicione a seguinte linha de código ao final do método:

    ```csharp
    await InitNotificationsAsync();
    ```

    Isso garante que o registro de notificação por push seja criado ou atualizado toda vez que o aplicativo for iniciado. É importante fazer isso para garantir que o canal de push WNS esteja sempre ativo.  

4. No Gerenciador de Soluções para Visual Studio, abra o arquivo **Package. appxmanifest** e defina **compatível com notificação do sistema** como **Sim** em **notificações**.
5. Compile o aplicativo e verifique se você não tem erros. Seu aplicativo cliente agora deve se registrar para as notificações de modelo do back-end dos aplicativos móveis. Repita esta seção para cada projeto do Windows em sua solução.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testar notificações por push em seu aplicativo do Windows

1. No Visual Studio, clique com o botão direito do mouse em um projeto do Windows e clique em **definir como projeto de inicialização**.
2. Prima o botão **Executar** para compilar o projeto e iniciar a aplicação.
3. No aplicativo, digite um nome para um novo todoitem e, em seguida, clique no ícone de adição ( **+** ) para adicioná-lo.
4. Verifique se uma notificação é recebida quando o item é adicionado.

## <a name="next-steps"></a>Passos seguintes

Você pode saber mais sobre as notificações por push:

* [Enviando notificações por push de aplicativos móveis do Azure](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Mensagens de nuvem firebase](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Notificações remotas com o firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Diagnosticar problemas de notificação por push](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Há várias razões pelas quais as notificações podem ser descartadas ou não terminam em dispositivos. Este tópico mostra como analisar e descobrir a causa raiz das falhas de notificação por push.

Você também pode continuar com um dos seguintes tutoriais:

* [Adicionar autenticação à aplicação](app-service-mobile-xamarin-forms-get-started-users.md)  
  Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.
* [Permitir sincronização offline para a sua aplicação](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Saiba como adicionar suporte offline à aplicação com um back-end de Aplicações Móveis. Com a sincronização offline, os usuários podem interagir com um aplicativo móvel @ no__t-0viewing, adicionando ou modificando dados @ no__t-1even quando não há conexão de rede.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: https://go.microsoft.com/fwlink/p/?LinkId=272333
