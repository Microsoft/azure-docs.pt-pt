---
title: Envie notificações push para aplicações Xamarin.Android usando Azure Notification Hubs | Microsoft Docs
description: Neste tutorial, irá aprender a utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação Xamarin Android.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/12/2021
ms.author: matthewp
ms.reviewer: jowargo
ms.lastreviewed: 08/01/2019
ms.openlocfilehash: e7d4206de1e097c30e9f5e96bbd935e94892ce0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98221039"
---
# <a name="tutorial-send-push-notifications-to-xamarinandroid-apps-using-notification-hubs"></a>Tutorial: Enviar notificações push para aplicações Xamarin.Android usando Centros de Notificação

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Descrição Geral

Este tutorial mostra como utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação Xamarin Android. Vai criar uma aplicação Xamarin.Android em branco que recebe notificações push através do Firebase Cloud Messaging (FCM). Pode utilizar o hub de notificação para difundir notificações push para todos os dispositivos a executar a sua aplicação. O código concluído está disponível no exemplo [aplicação NotificationHubs](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/Xamarin/GetStartedXamarinAndroid).

Neste tutorial, siga os seguintes passos:

> [!div class="checklist"]
> * Criar um projeto do Firebase e ativar o Firebase Cloud Messaging
> * Criar um hub de notificação
> * Criar uma aplicação Xamarin.Android e ligá-la ao hub de notificação
> * Enviar notificações de teste a partir do portal do Azure

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura Azure**. Se não tiver uma subscrição do Azure, [crie uma conta Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* [Visual Studio com Xamarin] no Windows ou [Visual Studio para Mac] no OS X.
* Conta Google ativa

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Criar um projeto do Firebase e ativar o Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging-xamarin.md)]

## <a name="create-a-notification-hub"></a>Criar um hub de notificação

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcmfcm-settings-for-the-notification-hub"></a>Configurar configurações GCM/FCM para o centro de notificação

1. Selecione **Google (GCM/FCM)/** na secção **Definições** no menu esquerdo.
2. Introduza a **tecla** de servidor que anotado a partir da Consola Google Firebase.
3. Selecione **Guardar** na barra de ferramentas.

    ![Screenshot do Notification Hub em Azure Portal com a opção Google G C M F C M em destaque e delineada a vermelho.](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

O Notification Hub está configurado para trabalhar com FCM e tem as cadeias de ligação para registar a sua aplicação e para receber notificações e enviar notificações push.

## <a name="create-a-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Crie uma aplicação Xamarin.Android e conecte-a ao centro de notificação

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Criar um projeto do Visual Studio e adicionar pacotes NuGet

> [!NOTE]
> Os passos documentados neste tutorial são para o Visual Studio 2017. 

1. No Estúdio Visual, abra o menu **Ficheiro,** selecione **Novo** e, em seguida, selecione **Project**. Na janela do **Novo Projeto,** faça estes passos:
    1. Expandir **Instalado,** **Visual C#** e, em seguida, clicar no **Android**.
    2. Selecione **Aplicativo Android (Xamarin)** da lista.
    3. Introduza um **nome** para o projeto.
    4. Selecione um **local** para o projeto.
    5. Selecione **OK**

        ![Caixa de diálogo Novo Projeto](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog-new.png)
2. Na nova caixa de diálogo **da Aplicação Android,** selecione **Blank App** e selecione **OK**.

    ![Screenshot que destaca o modelo de Aplicação Em Branco.](./media/partner-xamarin-notification-hubs-android-get-started/new-android-app-dialog.png)
3. Na janela **Explorador de Soluções**, expanda **Propriedades** e clique em **AndroidManifest.xml**. Atualize o nome do pacote para corresponder ao nome do pacote que introduziu ao adicionar o Firebase Cloud Messaging ao seu projeto na consola Google Firebase.

    ![Nome do pacote no GCM](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
4. Desaponte a versão android-alvo para o projeto para **o Android 10.0** seguindo estes passos: 
    1. Clique com o botão direito no seu projeto e selecione **Propriedades.** 
    1. Para o Compile utilizando a **versão Android: (Quadro-alvo),** selecione **Android 10.0**. 
    1. Selecione **Sim** na caixa de mensagens para continuar a alterar a estrutura-alvo.
1. Adicione pacotes NuGet necessários ao projeto seguindo estes passos:
    1. Clique com o botão direito do rato no projeto e selecione **Gerir Pacotes NuGet...**.
    1. Mude para o separador **Instalado,** selecione **Xamarin.Android.Support.Design** e selecione **Update** no painel direito para atualizar o pacote para a versão mais recente.
    1. Mude para o separador **Procurar.** Pesquisa por **Xamarin.GooglePlayServices.Base**. Selecione **Xamarin.GooglePlayServices.Base** na lista de resultados. Em seguida, selecione **Instalar**.

        ![NuGet dos Serviços do Google Play](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
    6. Na janela **Gestor de Pacotes NuGet**, procure **Xamarin.Firebase.Messaging**. Selecione **Xamarin.Firebase.Messaging** na lista de resultados. Em seguida, selecione **Instalar**.
    7. Agora, procure **Xamarin.Azure.NotificationHubs.Android**. Selecione **Xamarin.Azure.NotificationHubs.Android** na lista de resultados. Em seguida, selecione **Instalar**.

### <a name="add-the-google-services-json-file"></a>Adicionar o Ficheiro JSON dos Serviços Google

1. Copie o `google-services.json` ficheiro que descarregou da Consola Google Firebase para a pasta do projeto.
2. Adicione `google-services.json` ao projeto.
3. Selecione `google-services.json` na janela **'Solução Explorer'.**
4. No painel **Propriedades**, defina a Ação de Compilação como **GoogleServicesJson**. Se não vir **GoogleServicesJson**, feche o Visual Studio, reinicie-o, reabra o projeto e repita.

    ![Ação de compilação GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Configurar Notification Hubs no seu projeto

#### <a name="registering-with-firebase-cloud-messaging"></a>Registar no Firebase Cloud Messaging

1. Se estiver a migrar do Google Cloud Messaging para o Firebase, o ficheiro do seu projeto `AndroidManifest.xml` pode conter uma configuração de GCM desatualizada, o que pode causar duplicação de notificação. Editar o ficheiro e remover as seguintes linhas dentro da `<application>` secção, se estiver presente:

    ```xml
    <receiver
        android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver"
        android:exported="false" />
    <receiver
        android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver"
        android:exported="true"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

2. Adicione as seguintes declarações antes do elemento **de aplicação.**

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    ```

3. Recolha as seguintes informações para a sua aplicação Android e Notification Hub:

   * **Escutar a cadeia de ligação**: no dashboard, no [portal do Azure], escolha **Ver cadeias de ligação**. Copie a `DefaultListenSharedAccessSignature` cadeia de ligação por este valor.
   * **Nome do hub**: nome do hub a partir do [portal do Azure]. Por exemplo, *mynotificationhub2*.
4. Na janela **'Solução Explorer',** clique à direita no seu **projeto**, selecione **Adicionar** e, em seguida, selecione **Classe**.
5. Crie uma `Constants.cs` aula para o seu projeto Xamarin e defina os seguintes valores constantes na classe. Substitua os marcadores de posição com os seus valores.

    ```csharp
    public static class Constants
    {
        public const string ListenConnectionString = "<Listen connection string>";
        public const string NotificationHubName = "<hub name>";
    }
    ```

6. Adicione as seguintes declarações `MainActivity.cs` utilizando:

    ```csharp
    using Azure.Messaging.NotificationHubs;
    ```

7. Adicione as seguintes propriedades à classe MainActivity:

    ```csharp
    internal static readonly string CHANNEL_ID = "my_notification_channel";

8. In `MainActivity.cs`, add the following code to `OnCreate` after `base.OnCreate(savedInstanceState)`:

    ```csharp
    // Listen for push notifications
    NotificationHub.SetListener(new AzureListener());

    // Start the SDK
    NotificationHub.Start(this.Application, HubName, ConnectionString);
    ```

9. Adicione uma classe nomeada `AzureListener` para o seu projeto.
10. Adicione as seguintes declarações utilizando `AzureListener.cs` .

    ```csharp
    using Android.Content;
    using WindowsAzure.Messaging.NotificationHubs;
    ```

11. Adicione o seguinte acima da sua declaração de classe, e tenha a sua classe herdada `Java.Lang.Object` e implemente o `INotificationListener` :

    ```csharp
    public class AzureListener : Java.Lang.Object, INotificationListener
    ```

12. Adicione o seguinte código dentro `MyFirebaseMessagingService` da classe, para processar mensagens que são recebidas.

    ```csharp
        public void OnPushNotificationReceived(Context context, INotificationMessage message)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);
    
            var notificationBuilder = new NotificationCompat.Builder(this, MainActivity.CHANNEL_ID);
    
            notificationBuilder.SetContentTitle(message.Title)
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(message.Body)
                        .SetAutoCancel(true)
                        .SetShowWhen(false)
                        .SetContentIntent(pendingIntent);
    
            var notificationManager = NotificationManager.FromContext(this);
    
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

1. **Crie** o projeto.
1. **Execute** a sua aplicação no seu dispositivo ou emulador carregado

## <a name="send-test-notification-from-the-azure-portal"></a>Enviar notificação de teste a partir do portal do Azure

Pode testar a receção das notificações na aplicação com a opção **Envio de Teste** no [portal do Azure]. Esta ação envia uma notificação push de teste para o dispositivo.

![Portal do Azure – Envio de Teste](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

Normalmente, as notificações push são enviadas num serviço de back-end, como Mobile Services ou ASP.NET, através de uma biblioteca compatível. Se uma biblioteca não estiver disponível para o seu back-end, também pode utilizar a API REST diretamente para enviar mensagens de notificação.

## <a name="next-steps"></a>Passos seguintes

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
[Estúdio Visual com Xamarin]: /visualstudio/install/install-visual-studio
[Visual Studio para Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Portal do Azure]: https://portal.azure.com/
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Android]: /previous-versions/azure/dn282661(v=azure.100)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-android