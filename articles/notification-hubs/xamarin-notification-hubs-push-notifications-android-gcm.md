---
title: Enviar notificações push para aplicações Xamarin.Android com Hubs de Notificação do Azure | Microsoft Docs
description: Neste tutorial, irá aprender a utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação Xamarin Android.
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/01/2019
ms.author: jowargo
ms.openlocfilehash: 39fa004b62bf7e2e2a50500f32fa7edcb0c4b6ba
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742405"
---
# <a name="tutorial-push-notifications-to-xamarinandroid-apps-using-azure-notification-hubs"></a>Tutorial: Notificações por push para aplicativos Xamarin. Android usando hubs de notificação do Azure

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Descrição geral

Este tutorial mostra como utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação Xamarin Android. Vai criar uma aplicação Xamarin.Android em branco que recebe notificações push através do Firebase Cloud Messaging (FCM). Pode utilizar o hub de notificação para difundir notificações push para todos os dispositivos a executar a sua aplicação. O código concluído está disponível no exemplo de [aplicativo NotificationHubs](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/Xamarin/GetStartedXamarinAndroid) .

Neste tutorial, siga os passos seguintes:

> [!div class="checklist"]
> * Criar um projeto do Firebase e ativar o Firebase Cloud Messaging
> * Criar um hub de notificação
> * Criar uma aplicação Xamarin.Android e ligá-la ao hub de notificação
> * Enviar notificações de teste a partir do portal do Azure

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição do Azure**. Se você não tiver uma assinatura do Azure, [crie uma conta gratuita do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* [Visual Studio com Xamarin] no Windows ou [Visual Studio para Mac] no OS X.
* Conta Google ativa

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Criar um projeto do Firebase e ativar o Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging-xamarin.md)]

## <a name="create-a-notification-hub"></a>Criar um hub de notificação

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcmfcm-settings-for-the-notification-hub"></a>Definir as configurações do GCM/FCM para o Hub de notificação

1. Selecione **Google (GCM/FCM)/** na seção **configurações** no menu à esquerda.
2. Insira a **chave do servidor** que você anotou no console do Google firebase.
3. Selecione **Guardar** na barra de ferramentas.

    ![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

O Notification Hub está configurado para trabalhar com FCM e tem as cadeias de ligação para registar a sua aplicação e para receber notificações e enviar notificações push.

## <a name="create-a-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Criar um aplicativo Xamarin. Android e conectá-lo ao Hub de notificação

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Criar um projeto do Visual Studio e adicionar pacotes NuGet

> [!NOTE]
> As etapas documentadas neste tutorial são para o Visual Studio 2017. 

1. No Visual Studio, abra o menu **arquivo** , selecione **novo**e, em seguida, selecione **projeto**. Na janela **novo projeto** , siga estas etapas:
    1. Expanda **instalado**, **Visual C#** e clique em **Android**.
    2. Selecione **aplicativo Android (Xamarin)** na lista.
    3. Introduza um **nome** para o projeto.
    4. Selecione um **local** para o projeto.
    5. Selecione **OK**

        ![Caixa de diálogo Novo Projeto](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog-new.png)
2. Na caixa de diálogo **novo aplicativo Android** , selecione **aplicativo em branco**e selecione **OK**.

    ![Caixa de diálogo Novo Projeto](./media/partner-xamarin-notification-hubs-android-get-started/new-android-app-dialog.png)
3. Na janela **Explorador de Soluções**, expanda **Propriedades** e clique em **AndroidManifest.xml**. Atualize o nome do pacote para corresponder ao nome do pacote que introduziu ao adicionar o Firebase Cloud Messaging ao seu projeto na consola Google Firebase.

    ![Nome do pacote no GCM](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
4. Defina a versão de destino do Android para o projeto como **Android 9,0 (pizza)** seguindo estas etapas: 
    1. Clique com o botão direito do mouse no projeto e selecione **Propriedades**. 
    1. Para a **compilação usando a versão do Android: (Estrutura de destino) , selecione **Android 9,0 (pizza).**** 
    1. Selecione **Sim** na caixa de mensagem para continuar com a alteração da estrutura de destino.
1. Adicione os pacotes NuGet necessários ao projeto seguindo estas etapas:
    1. Clique com o botão direito do rato no projeto e selecione **Gerir Pacotes NuGet...** .
    1. Alterne para a guia **instalado** , selecione **Xamarin. Android. support. Design**e selecione **Atualizar** no painel direito para atualizar o pacote para a versão mais recente.
    1. Alterne para a guia **procurar** . Procure **Xamarin.GooglePlayServices.Base**. Selecione **Xamarin.GooglePlayServices.Base** na lista de resultados. Em seguida, selecione **Instalar**.

        ![NuGet dos Serviços do Google Play](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
    6. Na janela **Gestor de Pacotes NuGet**, procure **Xamarin.Firebase.Messaging**. Selecione **Xamarin.Firebase.Messaging** na lista de resultados. Em seguida, selecione **Instalar**.
    7. Agora, procure **Xamarin.Azure.NotificationHubs.Android**. Selecione **Xamarin.Azure.NotificationHubs.Android** na lista de resultados. Em seguida, selecione **Instalar**.

### <a name="add-the-google-services-json-file"></a>Adicionar o Ficheiro JSON dos Serviços Google

1. Copie o `google-services.json` arquivo que você baixou do console do Google firebase para a pasta do projeto.
2. Adicione `google-services.json` ao projeto.
3. Selecione `google-services.json` na janela de **Gerenciador de soluções** .
4. No painel **Propriedades**, defina a Ação de Compilação como **GoogleServicesJson**. Se não vir **GoogleServicesJson**, feche o Visual Studio, reinicie-o, reabra o projeto e repita.

    ![Ação de compilação GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Configurar Notification Hubs no seu projeto

#### <a name="registering-with-firebase-cloud-messaging"></a>Registar no Firebase Cloud Messaging

1. Abra o `AndroidManifest.xml` arquivo e insira os seguintes `<receiver>` elementos no `<application>` elemento:

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

2. Adicione as instruções a seguir **antes do elemento Application** .

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    ```

3. Recolha as seguintes informações para a sua aplicação Android e Notification Hub:

   * **Cadeia de conexão de escuta**: No painel do [Azure portal], escolha **exibir cadeias de conexão**. Copie a `DefaultListenSharedAccessSignature` cadeia de conexão para esse valor.
   * **Nome do Hub**: Nome do seu Hub no [Azure portal]. Por exemplo, *mynotificationhub2*.
4. Na janela **Gerenciador de soluções** , clique com o botão direito do mouse no **projeto**, selecione **Adicionar**e, em seguida, selecione **classe**.
5. Crie uma `Constants.cs` classe para seu projeto Xamarin e defina os valores constantes a seguir na classe. Substitua os marcadores de posição com os seus valores.

    ```csharp
    public static class Constants
    {
        public const string ListenConnectionString = "<Listen connection string>";
        public const string NotificationHubName = "<hub name>";
    }
    ```

6. Adicione as seguintes instruções using para `MainActivity.cs`:

    ```csharp
    using Android.Util;
    using Android.Gms.Common;
    ```

7. Adicione as propriedades a seguir à classe MainActivity. A variável de marca será usada para mostrar uma caixa de diálogo de alerta quando o aplicativo estiver em execução:

    ```csharp
    public const string TAG = "MainActivity";
    internal static readonly string CHANNEL_ID = "my_notification_channel";
    ```

8. Adicione o método a seguir à classe MainActivity. Ele verifica se **Google Play Services** estão disponíveis no dispositivo.

    ```csharp
    public bool IsPlayServicesAvailable()
    {
        int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.Success)
        {
            if (GoogleApiAvailability.Instance.IsUserResolvableError(resultCode))
                Log.Debug(TAG, GoogleApiAvailability.Instance.GetErrorString(resultCode));
            else
            {
                Log.Debug(TAG, "This device is not supported");
                Finish();
            }
            return false;
        }

        Log.Debug(TAG, "Google Play Services is available.");
        return true;
    }
    ```

9. Adicione o método a seguir à classe MainActivity que cria um canal de notificação.

    ```csharp
    private void CreateNotificationChannel()
    {
        if (Build.VERSION.SdkInt < BuildVersionCodes.O)
        {
            // Notification channels are new in API 26 (and not a part of the
            // support library). There is no need to create a notification
            // channel on older versions of Android.
            return;
        }

        var channelName = CHANNEL_ID;
        var channelDescription = string.Empty;
        var channel = new NotificationChannel(CHANNEL_ID, channelName, NotificationImportance.Default)
        {
            Description = channelDescription
        };

        var notificationManager = (NotificationManager)GetSystemService(NotificationService);
        notificationManager.CreateNotificationChannel(channel);
    }
    ```

10. No `MainActivity.cs`, adicione o seguinte código a `OnCreate` após `base.OnCreate(savedInstanceState)`:

    ```csharp
    if (Intent.Extras != null)
    {
        foreach (var key in Intent.Extras.KeySet())
        {
            if(key!=null)
            {
                var value = Intent.Extras.GetString(key);
                Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
            }
        }
    }

    IsPlayServicesAvailable();
    CreateNotificationChannel();
    ```

15. Adicione uma classe chamada `MyFirebaseMessagingService` ao seu projeto. 
16. Adicione as seguintes instruções using ao `MyFirebaseMessagingService.cs`.

    ```csharp
    using Android.Util;
    using Firebase.Messaging;
    using Android.Support.V4.App;    
    using WindowsAzure.Messaging;
    ```

17. Adicione o seguinte acima da declaração de classe e faça com que sua classe `FirebaseMessagingService`herde de:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class MyFirebaseMessagingService : FirebaseMessagingService
    ```

18. Adicione o código a seguir `MyFirebaseMessagingService.cs` ao para processar mensagens que são recebidas. 

    ```csharp
        const string TAG = "MyFirebaseMsgService";
        NotificationHub hub;
    
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            if (message.GetNotification() != null)
            {
                //These is how most messages will be received
                Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
                SendNotification(message.GetNotification().Body);
            }
            else
            {
                //Only used for debugging payloads sent from the Azure portal
                SendNotification(message.Data.Values.First());
    
            }
        }
    
        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);
    
            var notificationBuilder = new NotificationCompat.Builder(this, MainActivity.CHANNEL_ID);
    
            notificationBuilder.SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetShowWhen(false)
                        .SetContentIntent(pendingIntent);
    
            var notificationManager = NotificationManager.FromContext(this);
    
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

19. Adicione os seguintes métodos à classe MyFirebaseMessagingService para receber o token de registro FCM e enviá-lo para a instância de hubs de notificação (Hub). 

    ```csharp
        public override void OnNewToken(string token)
        {
            Log.Debug(TAG, "FCM token: " + token);
            SendRegistrationToServer(token);
        }

        void SendRegistrationToServer(string token)
        {
            // Register with Notification Hubs
            hub = new NotificationHub(Constants.NotificationHubName,
                                        Constants.ListenConnectionString, this);

            var tags = new List<string>() { };
            var regID = hub.Register(token, tags.ToArray()).RegistrationId;

            Log.Debug(TAG, $"Successful registration of ID {regID}");
        }
    ```
1. **Crie** o projeto.
1. **Execute** a aplicação no dispositivo ou emulador carregado

## <a name="send-test-notification-from-the-azure-portal"></a>Enviar notificação de teste a partir do portal do Azure

Pode testar a receção das notificações na aplicação com a opção **Envio de Teste** no [Azure portal]. Esta ação envia uma notificação push de teste para o seu dispositivo.

![Portal do Azure – Envio de Teste](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

Normalmente, as notificações push são enviadas num serviço de back-end, como Mobile Services ou ASP.NET, através de uma biblioteca compatível. Se uma biblioteca não estiver disponível para seu back-end, você também poderá usar a API REST diretamente para enviar mensagens de notificação.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, enviou notificações de difusão para todos os dispositivos Android registados no back-end. Para saber como enviar notificações push para dispositivos Android específicos, avance para o tutorial seguinte:

> [!div class="nextstepaction"]
>[Enviar notificações push para dispositivos específicos](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio com Xamarin]: https://docs.microsoft.com/visualstudio/install/install-visual-studio
[Visual Studio para Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Azure portal]: https://portal.azure.com/
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Android]: https://msdn.microsoft.com/library/dn282661.aspx
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-android
