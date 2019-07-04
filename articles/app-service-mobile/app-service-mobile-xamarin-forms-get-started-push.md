---
title: Adicionar notificações push à aplicação xamarin. Forms | Documentos da Microsoft
description: Saiba como utilizar os serviços do Azure para enviar notificações push de várias plataformas para as aplicações xamarin. Forms.
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
ms.openlocfilehash: b7e2ff63211ec5891a48a585e4f69e18116cdeb3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446585"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Adicionar notificações push à aplicação xamarin. Forms

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center está a investir em serviços de novo e integrados essenciais para o desenvolvimento de aplicações móveis. Os desenvolvedores podem usar **crie**, **teste** e **distribuir** serviços para configurar os pipelines de integração e entrega contínuas. Assim que a aplicação é implementada, os programadores podem monitorizar o estado e a utilização da sua aplicação com o **Analytics** e **diagnóstico** serviços e interaja com os utilizadores que utilizam o **Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados de aplicações na cloud. Confira [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-forms-get-started-push) hoje mesmo.
>

## <a name="overview"></a>Descrição geral

Neste tutorial, adicionar notificações push para todos os projetos que resultaram do [início rápido do xamarin. Forms](app-service-mobile-xamarin-forms-get-started.md). Isso significa que é enviada uma notificação push para todos os clientes de várias plataformas, sempre que um registro é inserido.

Se não utilizar o projeto de servidor de início rápido transferido, terá do pacote de extensão de notificação push. Para obter mais informações, consulte [trabalhar com o SDK do servidor de back-end de .NET para aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

Para iOS, precisará de um [associação ao programa de programador da Apple](https://developer.apple.com/programs/ios/) e um dispositivo iOS físico. O [simulador iOS não suporta notificações push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Configurar um hub de notificação

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Atualizar o projeto de servidor para enviar notificações push

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Configurar e executar o projeto Android (opcional)

Conclua esta seção para ativar as notificações push para o projeto Droid xamarin para Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Ativar de Firebase Cloud Messaging (FCM)

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Configurar a aplicações móveis de back-end para enviar pedidos de push através do FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Adicionar notificações push para o projeto Android

Com o back-end configurado com FCM, pode adicionar componentes e os códigos para o cliente para registar com o FCM. Também pode se registrar para notificações push com Notification Hubs do Azure através do back-end de aplicações móveis e receber notificações.

1. Na **Droid** do projeto, faça duplo clique **referências > Gerir pacotes NuGet...** .
1. Na janela do Gestor de pacotes NuGet, procure o **firebase** empacotamento e a adicioná-lo ao projeto.
1. Nas propriedades do projeto para o **Droid** do projeto, defina a aplicação para compilar a utilizar a versão Android 7.0 ou superior.
1. Adicionar a **google-Services. JSON** ficheiro, transferido a partir da consola Firebase, para a raiz do **Droid** do projeto e defina a ação de compilação como **GoogleServicesJson**. Para obter mais informações, consulte [adicione o ficheiro de JSON de serviços do Google](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File).

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

#### <a name="implementing-the-firebase-instance-id-service"></a>Implementação do serviço de ID de instância do Firebase

1. Adicionar uma nova classe para o **Droid** com o nome do projeto `FirebaseRegistrationService`e certifique-se de que o seguinte `using` instruções estão presentes na parte superior do ficheiro:

    ```csharp
    using System.Threading.Tasks;
    using Android.App;
    using Android.Util;
    using Firebase.Iid;
    using Microsoft.WindowsAzure.MobileServices;
    ```

1. Substitua vazio `FirebaseRegistrationService` classe com o código a seguir:

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

    O `FirebaseRegistrationService` classe é responsável por gerar tokens de segurança que autorize a aplicação a aceder FCM. O `OnTokenRefresh` método é invocado quando o aplicativo recebe um token de registo do FCM. O método obtém o token do `FirebaseInstanceId.Instance.Token` propriedade, que é atualizada assincronicamente por FCM. O `OnTokenRefresh` método é invocado com pouca frequência, uma vez que o token é atualizado apenas quando o aplicativo é instalado ou desinstalado, quando o utilizador elimina dados de aplicação, quando o aplicativo apaga o ID de instância, ou quando foi a segurança do token comprometidos. Além disso, o serviço de ID de instância do FCM solicitará que o aplicativo atualiza seu token periodicamente, normalmente cada 6 meses.

    O `OnTokenRefresh` método também invoca o `SendRegistrationTokenToAzureNotificationHub` método, que é utilizado para associar o token de registo do utilizador com o Hub de notificação do Azure.

#### <a name="registering-with-the-azure-notification-hub"></a>Registo no Hub de notificação do Azure

1. Adicionar uma nova classe para o **Droid** com o nome do projeto `AzureNotificationHubService`e certifique-se de que o seguinte `using` instruções estão presentes na parte superior do ficheiro:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Android.Util;
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

1. Substitua vazio `AzureNotificationHubService` classe com o código a seguir:

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

    O `RegisterAsync` método cria um modelo de mensagem de notificação simples como JSON e como registra para receber notificações de modelo do hub de notificação, com o token de registo do Firebase. Isto garante que quaisquer notificações enviadas do Hub de notificação do Azure destina-se o dispositivo representado pelo token de registo.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Exibindo o conteúdo de uma notificação Push

1. Adicionar uma nova classe para o **Droid** com o nome do projeto `FirebaseNotificationService`e certifique-se de que o seguinte `using` instruções estão presentes na parte superior do ficheiro:

    ```csharp
    using Android.App;
    using Android.Content;
    using Android.Media;
    using Android.Support.V7.App;
    using Android.Util;
    using Firebase.Messaging;
    ```

1. Substitua vazio `FirebaseNotificationService` classe com o código a seguir:

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

    O `OnMessageReceived` método, que é invocado quando uma aplicação recebe uma notificação de FCM, extrai a conteúdo de mensagem e chama o `SendNotification` método. Este método converte o conteúdo da mensagem numa notificação de local é iniciada enquanto a aplicação está em execução, com a notificação que aparece na área de notificação.

Agora, está pronto teste as notificações push na aplicação em execução no dispositivo Android ou no emulador.

### <a name="test-push-notifications-in-your-android-app"></a>Notificações de push de teste na sua aplicação Android

Os primeiros dois passos são necessários apenas quando estiver a testar no emulador.

1. Certifique-se de que estão a implementar ou depuração num dispositivo ou emulador que está configurado com serviços do Google Play. Isso pode ser verificado ao confirmar que o **reproduzir** as aplicações são instaladas no dispositivo ou emulador.
2. Adicionar uma conta do Google para o dispositivo Android clicando **aplicações** > **definições** > **adicionar conta**. Em seguida, siga as instruções para adicionar uma conta Google existente no dispositivo ou para criar um novo.
3. No Visual Studio ou no Xamarin Studio, clique com botão direito a **Droid** do projeto e clique em **definir como projeto de arranque**.
4. Clique em **executar** para compilar o projeto e iniciar a aplicação no seu dispositivo Android ou emulador.
5. Na aplicação, escreva uma tarefa e, em seguida, clique no sinal de adição ( **+** ) ícone.
6. Certifique-se de que uma notificação é recebida quando é adicionado um item.

## <a name="configure-and-run-the-ios-project-optional"></a>Configurar e executar o projeto iOS (opcional)

Esta secção destina-se à execução do projeto iOS Xamarin para dispositivos iOS. Pode ignorar esta secção se não estiver a trabalhar com dispositivos iOS.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Configurar o hub de notificação para o APNS

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Em seguida, irá configurar a definição do projeto iOS Xamarin Studio ou o Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Adicionar notificações push à sua aplicação iOS

1. Na **iOS** do projeto, abra o AppDelegate.cs e adicione a seguinte instrução na parte superior do arquivo de código.

    ```csharp
    using Newtonsoft.Json.Linq;
    ```

2. Na **AppDelegate** , adicione uma substituição para o **RegisteredForRemoteNotifications** evento para se registrar para notificações:

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

3. Na **AppDelegate**, adicione também a substituição seguinte para o **DidReceiveRemoteNotification** manipulador de eventos:

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

    Este método manipula notificações recebidas, enquanto a aplicação está em execução.

4. Na **AppDelegate** , adicione o seguinte código para o **FinishedLaunching** método:

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

    Isto permite que o suporte para notificações remotas e pedidos de registo de push.

A aplicação foi atualizada para suportar notificações push.

#### <a name="test-push-notifications-in-your-ios-app"></a>Notificações de push de teste na sua aplicação iOS

1. Com o botão direito no projeto iOS e clique em **Set as StartUp Project**.
2. Prima a **execute** botão ou **F5** no Visual Studio para compilar o projeto e iniciar a aplicação no dispositivo iOS. Em seguida, clique em **OK** para aceitar notificações push.

   > [!NOTE]
   > Tem de aceitar explicitamente notificações push da sua aplicação. Este pedido ocorre apenas na primeira vez que a aplicação é executada.

3. Na aplicação, escreva uma tarefa e, em seguida, clique no sinal de adição ( **+** ) ícone.
4. Certifique-se de que uma notificação é recebida e, em seguida, clique em **OK** para dispensar a notificação.

## <a name="configure-and-run-windows-projects-optional"></a>Configurar e executar projetos do Windows (opcionais)

Esta secção destina-se a executar a WinApp xamarin. Forms e WinPhone81 projetos para dispositivos Windows. Estes passos também dar suporte a projetos de plataforma Universal do Windows (UWP). Pode ignorar esta secção se não estiver a trabalhar com dispositivos Windows.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Registar a sua aplicação do Windows para as notificações push com o serviço de notificação Windows (WNS)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Configurar o hub de notificação para o WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Adicionar notificações push à aplicação Windows

1. No Visual Studio, abra **App.xaml.cs** num Windows do projeto e adicione as seguintes instruções.

    ```csharp
    using Newtonsoft.Json.Linq;
    using Microsoft.WindowsAzure.MobileServices;
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    using <your_TodoItemManager_portable_class_namespace>;
    ```

    Substitua `<your_TodoItemManager_portable_class_namespace>` com o espaço de nomes do seu projeto portátil que contém o `TodoItemManager` classe.

2. No App.xaml.cs, adicione as seguintes **InitNotificationsAsync** método:

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

    Este método obtém o canal de notificação push e regista um modelo para receber notificações de modelo do seu hub de notificação. Uma notificação de modelo que suporta *messageParam* será entregue a este cliente.

3. No App.xaml.cs, atualize o **OnLaunched** definição de método de manipulador de eventos, adicionando o `async` modificador. Em seguida, adicione a seguinte linha de código no final do método:

    ```csharp
    await InitNotificationsAsync();
    ```

    Isto garante que o registo de notificação push é criado ou atualizado sempre que a aplicação é aberta. É importante para fazê-lo para garantir que o canal de push do WNS está sempre ativo.  

4. No Explorador de soluções para o Visual Studio, abra a **Package. appxmanifest** de ficheiros e defina **com capacidade de alerta** para **Sim** em **notificações**.
5. Criar a aplicação e certifique-se de que tem sem erros. A aplicação de cliente agora se deverá registar para obter as notificações de modelo do back-end das aplicações móveis. Repita esta secção para todos os projetos do Windows na sua solução.

#### <a name="test-push-notifications-in-your-windows-app"></a>Notificações de push de teste na sua aplicação do Windows

1. No Visual Studio, um projeto do Windows com o botão direito e clique em **definir como projeto de arranque**.
2. Prima o botão **Executar** para compilar o projeto e iniciar a aplicação.
3. Na aplicação, escreva um nome para um todoitem novo e, em seguida, clique no sinal de adição ( **+** ) ícone para adicioná-lo.
4. Certifique-se de que uma notificação é recebida quando o item é adicionado.

## <a name="next-steps"></a>Passos Seguintes

Pode saber mais sobre as notificações push:

* [Enviar notificações Push a partir das aplicações móveis do Azure](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Notificações remotas com o Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Diagnosticar problemas de notificação push](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Existem vários motivos por que notificações podem obter removidas ou não chegarem nos dispositivos. Este tópico mostra-lhe como analisar e descobrir a causa de falhas de notificação push.

Também pode continuar a sessão em um dos seguintes tutoriais:

* [Adicionar autenticação à aplicação](app-service-mobile-xamarin-forms-get-started-users.md)  
  Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.
* [Permitir sincronização offline para a sua aplicação](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Saiba como adicionar suporte offline à aplicação com um back-end de Aplicações Móveis. Com a sincronização offline, os usuários podem interagir com uma aplicação móvel&mdash;visualizar, adicionar ou modificar dados&mdash;, mesmo quando não existe nenhuma ligação de rede.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: https://go.microsoft.com/fwlink/p/?LinkId=272333
