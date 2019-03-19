---
title: Enviar notificações push para aplicações Android com Hubs de Notificação do Azure e Firebase Cloud Messaging | Microsoft Docs
description: Neste tutorial, vai aprender a utilizar os Hubs de Notificação do Microsoft Azure e o Google Firebase Cloud Messaging para enviar notificações push para dispositivos Android.
services: notification-hubs
documentationcenter: android
keywords: notificações push, notificação push, notificação push para o android, fcm, firebase cloud messaging
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/05/2019
ms.author: jowargo
ms.openlocfilehash: 2fe448f3ed91f2c6dd242c24aa378c3541eceecc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57857951"
---
# <a name="tutorial-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-firebase-cloud-messaging"></a>Tutorial: Notificações push para dispositivos Android com Notification Hubs do Azure e o Google Firebase Cloud Messaging

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Este tutorial mostra-lhe como utilizar Notification Hubs do Azure e Firebase Cloud Messaging (FCM) para enviar notificações push para uma aplicação Android. Neste tutorial, irá criar uma aplicação Android em branco que recebe notificações push através do Firebase Cloud Messaging (FCM).

O código de conclusão para este tutorial pode ser transferido [aqui](https://github.com/Azure/azure-notificationhubs-android/tree/master/samples/FCMTutorialApp) a partir do GitHub.

Neste tutorial, siga os passos seguintes:

> [!div class="checklist"]
> * Criar um projeto do Android Studio.
> * Criar um projeto do Firebase que suporte o Firebase Cloud Messaging.
> * Criar um hub de notificação.
> * Ligar a aplicação ao hub de notificação.
> * Testar a aplicação.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/free/).

* Para além da conta ativa do Azure mencionada acima, este tutorial requer a versão mais recente do [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797).
* Android 2.3 ou superior para Firebase Cloud Messaging.
* É necessário o Repositório Google revisão 27 ou superior para o Firebase Cloud Messaging.
* Serviços do Google Play 9.0.2 ou superior para Firebase Cloud Messaging.
* A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Notification Hubs para aplicações com Android.

## <a name="create-an-android-studio-project"></a>Criar um projeto do Android Studio

1. Execute o Android Studio.
2. Selecione **arquivo** no menu, aponte para **New**e selecione **novo projeto**. 
2. Sobre o **escolha o seu projeto** página, selecione **atividade vazia**e selecione **seguinte**. 
3. Sobre o **configurar o seu projeto** página, efetue os seguintes passos: 
    1. Introduza um **nome** para a aplicação.
    2. Especifique uma localização para guardar os ficheiros de projeto. 
    3. Selecione **Concluir**. 

        ![Configurar o seu projeto)](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Criar um projeto do Firebase que suporte o FCM

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Configurar um hub de notificação

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Configurar as definições do Firebase Cloud Messaging para o hub

1. Selecione **Google (GCM/FCM)** sob **definições** no menu da esquerda. 
2. Colar o **chave de servidor** para o projeto do FCM que guardou anteriormente. 
3. Selecione **Guardar** na barra de ferramentas. 

    ![Hubs de notificação do Azure – Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Verá uma mensagem nos alertas que o hubs de notificação foi atualizada com êxito. O **guardar** botão está desativado. 

O hub de notificação está agora configurado para trabalhar com o Firebase Cloud Messaging e tem as cadeias de ligação para registar a sua aplicação para receber e enviar notificações push.

## <a id="connecting-app"></a>Ligar a aplicação ao hub de notificação

### <a name="add-google-play-services-to-the-project"></a>Adicionar serviços do Google Play para o projeto

[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>Adicionar bibliotecas dos Notification Hubs do Azure

1. No `Build.Gradle` ficheiro da **aplicação**, adicione as seguintes linhas na secção de **dependências**.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. Adicione o repositório seguinte depois da secção de **dependências**.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="add-google-firebase-support"></a>Adicionar suporte do Google Firebase

1. Na `Build.Gradle` de ficheiros para o **aplicação**, adicione as seguintes linhas no **dependências** secção se já não existir. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.7'
    ```

2. Adicione o plug-in do seguinte no final do ficheiro se ainda não exista. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```

### <a name="updating-the-androidmanifestxml"></a>A atualizar o androidmanifest. XML

1. Para suportar o FCM, temos de implementar um serviço de escuta de ID da Instância no código que é utilizado para [obter os tokens de registo](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register) com a [API FirebaseInstanceId da Google](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId). Neste tutorial, o nome da classe é `MyInstanceIDService`.

    Adicionar a seguinte definição de serviço ao ficheiro AndroidManifest.xml, no interior da etiqueta `<application>`.

    ```xml
    <service android:name=".MyInstanceIDService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
    </service>
    ```

2. Depois de receber o nosso token de registo do FCM a partir da API FirebaseInstanceId, utilize-o para [efetuar o registo no Hub de Notificação do Azure](notification-hubs-push-notification-registration-management.md). Suportará este registo em segundo plano com um `IntentService` com o nome `RegistrationIntentService`. Este serviço também é responsável por atualizar o token de registo do FCM.

    Adicionar a seguinte definição de serviço ao ficheiro AndroidManifest.xml, no interior da etiqueta `<application>`.

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    ```

3. Também necessitará definir um recetor para receber notificações. Adicione a seguinte definição do recetor ao ficheiro AndroidManifest.xml, no interior da etiqueta `<application>`. 

    ```xml
    <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <category android:name="<your package name>" />
        </intent-filter>
    </receiver>
    ```

    > [!IMPORTANT]
    > Substitua o marcador de posição `<your package NAME>` pelo nome do seu pacote real indicado no topo do ficheiro `AndroidManifest.xml`.
4. Selecione **sincronizar agora** na barra de ferramentas.
5. Adicione as seguintes permissões do fcm necessárias **abaixo o** `</application>` marca.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="adding-code"></a>Adicionar código

1. Na vista de projeto, expanda **app** > **src** > **principal** > **java**. Com o botão direito do rato, clique na pasta do pacote por baixo de **java**, clique em **Nova**, e, em seguida, clique em **Classe Java**. Introduza `NotificationSettings` para o nome e selecione **OK**.

    Certifique-se de que atualiza esses três marcadores de posição no seguinte código para a classe `NotificationSettings`:

   * **HubListenConnectionString**: O **DefaultListenAccessSignature** cadeia de ligação para o seu hub. Pode copiar essa cadeia de ligação ao clicar em **Políticas de Acesso** no hub do [portal do Azure].
   * **HubName**: Utilize o nome do notification hub que aparece na página do hub no [portal do Azure].

     Código `NotificationSettings`:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > Introduza o **name** e o **DefaultListenSharedAccessSignature** o hub de notificação antes de proceding adicionais. 
2. Adicione outra classe com o nome `MyInstanceIDService`. Esta classe é a implementação do serviço de escuta de ID da Instância.

    O código para esta classe chama o `IntentService` para [atualizar o token do FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) em segundo plano.

    ```java
    import android.content.Intent;
    import android.util.Log;
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class MyInstanceIDService extends FirebaseInstanceIdService {

        private static final String TAG = "MyInstanceIDService";

        @Override
        public void onTokenRefresh() {

            Log.d(TAG, "Refreshing FCM Registration Token");

            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    };
    ```

3. Adicione outra nova classe ao projeto com o nome `RegistrationIntentService`. Esta classe implementa a interface `IntentService` e processa a [atualização do token do FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) e o [registo no hub de notificação](notification-hubs-push-notification-registration-management.md).

    Utilize o seguinte código para esta classe.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.microsoft.windowsazure.messaging.NotificationHub;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";

        private NotificationHub hub;

        public RegistrationIntentService() {
            super(TAG);
        }

        @Override
        protected void onHandleIntent(Intent intent) {

            SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
            String resultString = null;
            String regID = null;
            String storedToken = null;

            try {
                String FCM_token = FirebaseInstanceId.getInstance().getToken();
                Log.d(TAG, "FCM Registration Token: " + FCM_token);

                // Storing the registration ID that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server,
                // otherwise your server should have already received the token.
                if (((regID=sharedPreferences.getString("registrationID", null)) == null)){

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                // Check if the token may have been compromised and needs refreshing.
                else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                else {
                    resultString = "Previously Registered Successfully - RegId : " + regID;
                }
            } catch (Exception e) {
                Log.e(TAG, resultString="Failed to complete registration", e);
                // If an exception happens while fetching the new token or updating our registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

4. Na sua classe `MainActivity`, adicione as seguintes `import` declarações acima da declaração de classe.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

5. Adicione as seguintes declarações na parte superior da classe. Utiliza estes campos para [verificar a disponibilidade dos Serviços do Google Play, tal como recomendado pela Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

6. No seu `MainActivity` , adicione o seguinte método para verificar a disponibilidade de serviços do Google Play.

    ```java
    /**
    * Check the device to make sure it has the Google Play Services APK. If
    * it doesn't, display a dialog that allows users to download the APK from
    * the Google Play Store or enable it in the device's system settings.
    */

    private boolean checkPlayServices() {
        GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
        int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.SUCCESS) {
            if (apiAvailability.isUserResolvableError(resultCode)) {
                apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                        .show();
            } else {
                Log.i(TAG, "This device is not supported by Google Play Services.");
                ToastNotify("This device is not supported by Google Play Services.");
                finish();
            }
            return false;
        }
        return true;
    }
    ```

7. Na sua classe `MainActivity`, adicione o seguinte código que verifica os Serviços do Google Play antes de chamar o `IntentService` para obter o token de registo do FCM e efetuar o registo no hub de notificação.

    ```java
    public void registerWithNotificationHubs()
    {
        if (checkPlayServices()) {
            // Start IntentService to register this application with FCM.
            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    }
    ```

8. No `OnCreate` método da `MainActivity` classe, adicione o seguinte código para iniciar o processo de registo quando é criada a atividade.

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        registerWithNotificationHubs();
        MyHandler.createChannelAndHandleNotifications(getApplicationContext());
    }
    ```

9. Para verificar o estado da aplicação e do relatório na aplicação, adicione estes métodos adicionais a `MainActivity`.

    ```java
    @Override
    protected void onStart() {
        super.onStart();
        isVisible = true;
    }

    @Override
    protected void onPause() {
        super.onPause();
        isVisible = false;
    }

    @Override
    protected void onResume() {
        super.onResume();
        isVisible = true;
    }

    @Override
    protected void onStop() {
        super.onStop();
        isVisible = false;
    }

    public void ToastNotify(final String notificationMessage) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                TextView helloText = (TextView) findViewById(R.id.text_hello);
                helloText.setText(notificationMessage);
            }
        });
    }
    ```

10. O `ToastNotify` método utiliza o comando *"Olá, mundo"* `TextView` para comunicar o estado e as notificações de forma permanente na aplicação. No seu **res** -> **layout** -> **ctivity_main** layout, adicione a seguinte ID para esse controle.

    ```java
    android:id="@+id/text_hello"
    ```

11. Em seguida, adicionará uma subclasse do recetor que definiu no AndroidManifest.xml. Adicione outra nova classe ao projeto com o nome `MyHandler`.

12. Adicione as seguintes declarações de importação no topo do ficheiro `MyHandler.java`:

    ```java
    import android.app.NotificationChannel;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.media.RingtoneManager;
    import android.net.Uri;
    import android.os.Build;
    import android.os.Bundle;
    import android.support.v4.app.NotificationCompat;
    import com.microsoft.windowsazure.notifications.NotificationsHandler;    
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    ```

13. Adicione o seguinte código à classe `MyHandler`, tornando-a numa subclasse de `com.microsoft.windowsazure.notifications.NotificationsHandler`.

    Este código substitui o método `OnReceive` para que o processador comunique as notificações que são recebidas. O processador também envia a notificação push para o gestor de notificações Android através do método `sendNotification()`. O método `sendNotification()` deve ser executado quando a aplicação não está em execução e deverá receber uma notificação.

    ```java
    public class MyHandler extends NotificationsHandler {
        public static final String NOTIFICATION_CHANNEL_ID = "nh-demo-channel-id";
        public static final String NOTIFICATION_CHANNEL_NAME = "Notification Hubs Demo Channel";
        public static final String NOTIFICATION_CHANNEL_DESCRIPTION = "Notification Hubs Demo Channel";
    
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;
    
        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("message");
            sendNotification(nhMessage);
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
        }
    
        private void sendNotification(String msg) {
    
            Intent intent = new Intent(ctx, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    
            mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                    intent, PendingIntent.FLAG_ONE_SHOT);
    
            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder notificationBuilder = new NotificationCompat.Builder(
                    ctx,
                    NOTIFICATION_CHANNEL_ID)
                    .setContentText(msg)
                    .setPriority(NotificationCompat.PRIORITY_HIGH)
                    .setSmallIcon(android.R.drawable.ic_popup_reminder)
                    .setBadgeIconType(NotificationCompat.BADGE_ICON_SMALL);
    
            notificationBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
        }
    
        public static void createChannelAndHandleNotifications(Context context) {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                NotificationChannel channel = new NotificationChannel(
                        NOTIFICATION_CHANNEL_ID,
                        NOTIFICATION_CHANNEL_NAME,
                        NotificationManager.IMPORTANCE_HIGH);
                channel.setDescription(NOTIFICATION_CHANNEL_DESCRIPTION);
                channel.setShowBadge(true);
    
                NotificationManager notificationManager = context.getSystemService(NotificationManager.class);
                notificationManager.createNotificationChannel(channel);
                NotificationsManager.handleNotifications(context, "", MyHandler.class);
            }
        }
    }
    ```

14. No Android Studio, na barra de menus, clique em **Construir** > **Reconstruir projeto** para se certificar de que não existem erros no seu código. Se receber um erro sobre o `ic_launcher` ícone, remova a seguinte instrução do ficheiro Androidmanifest XML. 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
15. Execute a aplicação no seu dispositivo e verifique se a mesma é registada com êxito no hub de notificação.

    > [!NOTE]
    > O registo pode falhar no lançamento inicial até que o método `onTokenRefresh()` do serviço do ID da instância seja chamado. A atualização deve iniciar um registo com êxito no hub de notificação.

    ![Registo de dispositivos com êxito](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Notificação de envio de teste a partir do hub de notificação

Pode enviar notificações push a partir do [portal do Azure] ao efetuar as seguintes ações:

1. No portal do Azure, sobre o **Hub de notificação** página para o seu hub de notificação, selecione **testar envio** no **resolução de problemas** secção.
3. Em **Plataformas**, selecione **Android**.
4. Selecione **Enviar**.  Ainda não verá uma notificação no dispositivo Android porque não executou a aplicação móvel no mesmo. Depois de executar a aplicação móvel, selecione novamente o botão **Enviar** para ver a mensagem de notificação.
5. Veja o **resultado** da operação na lista na parte inferior.

    ![Notification Hubs do Azure – Teste de Envio](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. Ver a mensagem de notificação no seu dispositivo. 

    ![Mensagem de notificação no dispositivo](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>Executar a aplicação móvel no emulador
Se pretender testar as notificações push no interior de um emulador, certifique-se de que a imagem do emulador suporta o nível da API da Google que escolheu para a sua aplicação. Se a imagem não suportar as APIs nativas da Google, poderá receber a **serviço\_não\_disponível** exceção.

Além disso, confirme que adicionou a sua conta Google ao emulador em execução em **Definições** > **Contas**. Caso contrário, as tentativas para registar o GCM podem originar a exceção **FALHA DE\_AUTENTICAÇÃO**.

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, utilizou o Firebase Cloud Messaging para enviar notificações push para dispositivos Android. Para saber como enviar notificações push com o Google Cloud Messaging, avance para o tutorial seguinte:

> [!div class="nextstepaction"]
>[Enviar notificações push para dispositivos Android com o Google Cloud Messaging](notification-hubs-android-push-notification-google-gcm-get-started.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Portal do Azure]: https://portal.azure.com
