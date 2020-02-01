---
title: Adicione notificações push à sua aplicação Xamarin.Forms
description: Saiba como utilizar os serviços Azure para enviar notificações push multiplataforma para as suas aplicações Xamarin.Forms.
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: fc05763046da365e7770a9b208100e0366062f25
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898867"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Adicione notificações push à sua aplicação Xamarin.Forms

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> O Visual Studio App Center suporta serviços de ponto a ponto e integrados, fundamentais para o desenvolvimento de aplicações móveis. Os programadores podem utilizar os serviços de **Compilação**, **Teste** e **Distribuição** para configurar o pipeline de Integração e Entrega Contínuas. Após a implementação da aplicação, os programadores podem monitorizar o estado e a utilização da aplicação através dos serviços de **Análise** e de **Diagnóstico** e interagir com os utilizadores através do serviço **Push**. Os programadores também podem tirar partido da **Autenticação** para autenticar os utilizadores e do serviço de **Dados** para manter e sincronizar os dados da aplicação na cloud.
>
> Se quiser integrar serviços cloud na sua aplicação móvel, inscreva-se no [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão geral

Neste tutorial, adiciona notificações push a todos os projetos que resultaram do arranque rápido de [Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Isto significa que uma notificação push é enviada a todos os clientes de plataformas cruzadas sempre que um registo é inserido.

Se não utilizar o projeto de servidor de arranque rápido descarregado, necessitará do pacote de extensão de notificação push. Para mais informações, consulte [Trabalhar com o servidor de backend .NET SDK para aplicações móveis Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

Para iOS, você precisará de uma subscrição do [Programa de Desenvolvedores](https://developer.apple.com/programs/ios/) da Apple e um dispositivo físico iOS. O [simulador iOS não suporta notificações push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Configurar um centro de notificação

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Atualize o projeto do servidor para enviar notificações push

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Configure e execute o projeto Android (opcional)

Complete esta secção para permitir notificações push para o projeto Xamarin.Forms Droid para Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Ativar mensagens em nuvem de base de fogo (FCM)

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Configure as Aplicações Móveis para trás para enviar pedidos de push usando a FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Adicione notificações push ao projeto Android

Com a parte traseira configurada com a FCM, pode adicionar componentes e códigos ao cliente para se registar na FCM. Também pode registar-se para notificações push com Hubs de Notificação Azure através do back end das Aplicações Móveis e receber notificações.

1. No projeto **Droid,** clique à direita **Referências > Gerencie pacotes NuGet...** .
1. Na janela NuGet Package Manager, procure o pacote **Xamarin.Firebase.Messaging** e adicione-o ao projeto.
1. Nas propriedades do projeto **Droid,** detetou a app para compilar utilizando a versão Android 7.0 ou superior.
1. Adicione o ficheiro **google-services.json,** descarregado da consola Firebase, para a raiz do projeto **Droid** e defina a sua ação de construção para o **GoogleServicesJson**. Para mais informações, consulte [Adicionar o Ficheiro JSON dos Serviços do Google](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File).

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

#### <a name="implementing-the-firebase-instance-id-service"></a>Implementação do Serviço de ID da Base de Fogo

1. Adicione uma nova classe ao projeto **Droid** chamado `FirebaseRegistrationService`, e certifique-se de que as seguintes declarações `using` estão presentes no topo do ficheiro:

    ```csharp
    using System.Threading.Tasks;
    using Android.App;
    using Android.Util;
    using Firebase.Iid;
    using Microsoft.WindowsAzure.MobileServices;
    ```

1. Substitua a classe `FirebaseRegistrationService` vazia pelo seguinte código:

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

    A classe `FirebaseRegistrationService` é responsável por gerar fichas de segurança que autorizam o pedido de acesso à FCM. O método `OnTokenRefresh` é invocado quando o pedido recebe um sinal de registo da FCM. O método recupera o símbolo da propriedade `FirebaseInstanceId.Instance.Token`, que é assincronicamente atualizada pela FCM. O método `OnTokenRefresh` é frequentemente invocado, porque o token só é atualizado quando a aplicação é instalada ou desinstalada, quando o utilizador elimina os dados da aplicação, quando a aplicação apaga o ID da instância, ou quando a segurança do token foi comprometida. Além disso, o serviço DE ID da FCM Instance solicitará que a aplicação adote periodicamente o seu token, tipicamente a cada 6 meses.

    O método `OnTokenRefresh` também invoca o método `SendRegistrationTokenToAzureNotificationHub`, que é utilizado para associar o símbolo de registo do utilizador ao Hub de Notificação Azure.

#### <a name="registering-with-the-azure-notification-hub"></a>Registo no Centro de Notificação Azure

1. Adicione uma nova classe ao projeto **Droid** chamado `AzureNotificationHubService`, e certifique-se de que as seguintes declarações `using` estão presentes no topo do ficheiro:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Android.Util;
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

1. Substitua a classe `AzureNotificationHubService` vazia pelo seguinte código:

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

    O método `RegisterAsync` cria um modelo de mensagem de notificação simples como JSON, e regista-se para receber notificações de modelo do centro de notificação, utilizando o símbolo de registo da Firebase. Isto garante que quaisquer notificações enviadas do Centro de Notificação Azure terão como alvo o dispositivo representado pelo sinal de registo.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Exibindo o conteúdo de uma notificação push

1. Adicione uma nova classe ao projeto **Droid** chamado `FirebaseNotificationService`, e certifique-se de que as seguintes declarações `using` estão presentes no topo do ficheiro:

    ```csharp
    using Android.App;
    using Android.Content;
    using Android.Media;
    using Android.Support.V7.App;
    using Android.Util;
    using Firebase.Messaging;
    ```

1. Substitua a classe `FirebaseNotificationService` vazia pelo seguinte código:

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
            //Unique request code to avoid PendingIntent collision.
            var requestCode = new Random().Next();

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

    O método `OnMessageReceived`, que é invocado quando uma aplicação recebe uma notificação da FCM, extrai o conteúdo da mensagem e chama o método `SendNotification`. Este método converte o conteúdo da mensagem numa notificação local que é lançada enquanto a aplicação está em execução, com a notificação a aparecer na área de notificação.

Agora, está pronto para testar notificações push na aplicação que está a funcionar num dispositivo Android ou no emulador.

### <a name="test-push-notifications-in-your-android-app"></a>Teste de notificações push na sua aplicação Android

Os dois primeiros passos só são necessários quando se está a testar um emulador.

1. Certifique-se de que está a implementar ou a depurar um dispositivo ou emulador que esteja configurado com os Serviços google Play. Isto pode ser verificado verificando se as aplicações **Play** estão instaladas no dispositivo ou emulador.
2. Adicione uma conta Google ao dispositivo Android clicando em **Apps** > **Definições** > **Adicionar conta**. Em seguida, siga as instruções para adicionar uma conta Google existente ao dispositivo, ou para criar uma nova.
3. No Estúdio Visual ou no Estúdio Xamarin, clique no projeto **Droid** e clique em **set como projeto de arranque**.
4. Clique em **Correr** para construir o projeto e iniciar a aplicação no seu dispositivo Android ou emulador.
5. Na aplicação, digite uma tarefa e clique no ícone plus **(+).**
6. Verifique se uma notificação é recebida quando um item é adicionado.

## <a name="configure-and-run-the-ios-project-optional"></a>Configure e execute o projeto iOS (opcional)

Esta secção destina-se à execução do projeto iOS Xamarin para dispositivos iOS. Pode ignorar esta secção se não estiver a trabalhar com dispositivos iOS.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Configure o centro de notificação para APNS

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Em seguida, irá configurar a definição do projeto iOS no Estúdio Xamarin ou no Estúdio Visual.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Adicione notificações push à sua aplicação iOS

1. No projeto **iOS,** abra AppDelegate.cs e adicione a seguinte declaração ao topo do ficheiro de código.

    ```csharp
    using Newtonsoft.Json.Linq;
    ```

2. Na classe **AppDelegate,** adicione uma sobreposição para o evento **RegisteredForRemoteNotifications** para se registar para notificações:

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

3. No **AppDelegate,** adicione também o seguinte sobreposição para o manipulador de eventos **DidReceiveRemoteNotification:**

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

    Este método lida com as notificações recebidas enquanto a aplicação está em execução.

4. Na classe **AppDelegate,** adicione o seguinte código ao método **DeLançamento Final:**

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

    Isto permite o suporte para notificações remotas e solicita o registo de push.

A sua aplicação está agora atualizada para suportar notificações push.

#### <a name="test-push-notifications-in-your-ios-app"></a>Teste de notificações push na sua aplicação iOS

1. Clique no projeto do iOS e clique em **Definir como StartUp Project**.
2. Prima o botão **Run** ou **O F5** no Estúdio Visual para construir o projeto e iniciar a aplicação num dispositivo iOS. Em seguida, clique em **OK** para aceitar notificações push.

   > [!NOTE]
   > Deve aceitar explicitamente notificações push da sua aplicação. Este pedido ocorre apenas na primeira vez que a aplicação corre.

3. Na aplicação, digite uma tarefa e clique no ícone plus **(+).**
4. Verifique se uma notificação é recebida e, em seguida, clique em **OK** para descartar a notificação.

## <a name="configure-and-run-windows-projects-optional"></a>Configurar e executar projetos Windows (opcional)

Esta secção destina-se a executar os projetos Xamarin.Forms WinApp e WinPhone81 para dispositivos Windows. Estes passos também suportam projetos da Universal Windows Platform (UWP). Pode ignorar esta secção se não estiver a trabalhar com dispositivos Windows.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Registe a sua aplicação Windows para notificações push com o Windows Notification Service (WNS)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Configure o centro de notificação para wns

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Adicione notificações push à sua aplicação Windows

1. No Visual Studio, abra **App.xaml.cs** num projeto Windows e adicione as seguintes declarações.

    ```csharp
    using Newtonsoft.Json.Linq;
    using Microsoft.WindowsAzure.MobileServices;
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    using <your_TodoItemManager_portable_class_namespace>;
    ```

    Substitua `<your_TodoItemManager_portable_class_namespace>` pelo espaço de nome do seu projeto portátil que contém a classe `TodoItemManager`.

2. Em App.xaml.cs, adicione o seguinte método **InitNotificationsAsync:**

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

    Este método obtém o canal de notificação push e regista um modelo para receber notificações de modelo do seu centro de notificação. Uma notificação de modelo que suporta *mensagemParam* será entregue a este cliente.

3. Em App.xaml.cs, atualize a definição do método de manipulador de eventos **OnLaunched** adicionando o modificador `async`. Em seguida, adicione a seguinte linha de código no final do método:

    ```csharp
    await InitNotificationsAsync();
    ```

    Isto garante que o registo de notificação push é criado ou atualizado sempre que a aplicação é lançada. É importante fazer isto para garantir que o canal de pressão WNS está sempre ativo.  

4. No Solution Explorer for Visual Studio, abra o ficheiro **Package.appxmanifest** e coloque **torradas capazes** de **sim** sob **notificações**.
5. Construa a aplicação e verifique se não tem erros. A sua aplicação de cliente deve agora registar-se para as notificações do modelo a partir do final das Aplicações Móveis. Repita esta secção para todos os projetos windows na sua solução.

#### <a name="test-push-notifications-in-your-windows-app"></a>Teste de notificações push na sua aplicação Windows

1. No Estúdio Visual, clique num projeto windows e clique em **set como projeto de arranque**.
2. Prima o botão **Executar** para compilar o projeto e iniciar a aplicação.
3. Na aplicação, digite um nome para um novo todoitem e, em seguida, clique no ícone plus **(+** ) para adicioná-lo.
4. Verifique se uma notificação é recebida quando o artigo é adicionado.

## <a name="next-steps"></a>Passos seguintes

Pode saber mais sobre notificações push:

* [Envio de Notificações push de aplicações móveis Azure](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Mensagens cloud da base de fogo](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Notificações remotas com mensagens em nuvem de base de fogo](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Diagnosticar problemas de notificação push](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Existem várias razões pelas quais as notificações podem ser retiradas ou não acabam em dispositivos. Este tópico mostra-lhe como analisar e descobrir a causa principal das falhas de notificação push.

Você também pode continuar para um dos seguintes tutoriais:

* [Adicionar autenticação à aplicação](app-service-mobile-xamarin-forms-get-started-users.md)  
  Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.
* [Permitir sincronização offline para a sua aplicação](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Saiba como adicionar suporte offline à aplicação com um back-end de Aplicações Móveis. Com sincronização offline, os utilizadores podem interagir com uma aplicação móvel&mdash;visualização, adição ou modificação de dados&mdash;mesmo quando não há ligação de rede.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: https://go.microsoft.com/fwlink/p/?LinkId=272333
