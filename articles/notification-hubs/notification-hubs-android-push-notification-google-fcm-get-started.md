---
title: Enviar notificações por push para Android usando os hubs de notificação do Azure e o firebase Cloud Messaging | Microsoft Docs
description: Neste tutorial, irá aprender a utilizar os Hubs de Notificação do Azure e o Google Firebase Cloud Messaging para enviar notificações push para dispositivos Android.
services: notification-hubs
documentationcenter: android
keywords: notificações push, notificação push, notificação push para o android, fcm, firebase cloud messaging
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/11/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/11/2019
ms.openlocfilehash: 1af109405118754daa08adf848f1da3f04534455
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387512"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-google-firebase-cloud-messaging"></a>Tutorial: enviar notificações por push para dispositivos Android usando o Google firebase Cloud Messaging

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Este tutorial mostra como usar os hubs de notificação do Azure e o FCM (firebase Cloud Messaging) para enviar notificações por push a um aplicativo Android. Neste tutorial, irá criar uma aplicação Android em branco que recebe notificações push através do Firebase Cloud Messaging (FCM).

O código completo para este tutorial pode ser baixado [do GitHub](https://github.com/Azure/azure-notificationhubs-android/tree/master/samples/FCMTutorialApp).

Neste tutorial, siga os seguintes passos:

> [!div class="checklist"]
> * Criar um projeto do Android Studio.
> * Criar um projeto do Firebase que suporte o Firebase Cloud Messaging.
> * Crie um Hub.
> * Conecte seu aplicativo ao Hub.
> * Testar a aplicação.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/free/). 

Você também precisa dos seguintes itens: 

* A versão mais recente do [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)
* Android 2,3 ou superior para o firebase Cloud Messaging
* Repositório do Google revisão 27 ou superior para firebase Cloud Messaging
* Google Play Services 9.0.2 ou superior para o firebase Cloud Messaging

A conclusão deste tutorial é um pré-requisito para fazer todos os outros tutoriais de hubs de notificação para aplicativos Android.

## <a name="create-an-android-studio-project"></a>Criar um projeto do Android Studio

1. Iniciar Android Studio.
2. Selecione **arquivo**, aponte para **novo**e, em seguida, selecione **novo projeto**. 
2. Na página **escolher seu projeto** , selecione **atividade vazia**e, em seguida, selecione **Avançar**. 
3. Na página **configurar seu projeto** , execute as seguintes etapas: 
    1. Insira um nome para o aplicativo.
    2. Especifique um local no qual salvar os arquivos de projeto. 
    3. Selecione **Concluir**. 

        ![Configurar seu projeto)](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Criar um projeto do Firebase que suporte o FCM

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-hub"></a>Configurar um hub

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Configurar as definições do Firebase Cloud Messaging para o hub

1. No painel esquerdo, em **configurações,** selecione **Google (GCM/FCM)** . 
2. Insira a **chave de servidor** para o projeto FCM que você salvou anteriormente. 
3. Na barra de ferramentas, selecione **salvar**. 

    ![Hub de notificação do Azure – Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. O portal do Azure exibe uma mensagem em alertas de que o Hub foi atualizado com êxito. O botão **salvar** está desabilitado. 

Seu hub agora está configurado para trabalhar com o firebase Cloud Messaging. Você também tem as cadeias de conexão necessárias para enviar notificações para um dispositivo e registrar um aplicativo para receber notificações.

## <a id="connecting-app"></a>Ligar a aplicação ao hub de notificação

### <a name="add-google-play-services-to-the-project"></a>Adicionar serviços do Google Play para o projeto

1. Em Android Studio, selecione **ferramentas** no menu e, em seguida, selecione **Gerenciador do SDK**. 
2. Selecione a versão de destino da SDK do Android que é usada em seu projeto. Em seguida, selecione **Mostrar detalhes do pacote**. 

    ![Gerenciador de SDK do Android-selecione a versão de destino](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Selecione **APIs do Google**, se ainda não estiver instalado.

    ![Gerenciador de SDK do Android-APIs do Google selecionadas](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Alterne para a guia **SDK Tools** . Se você ainda não instalou o Google Play Services, selecione **Google Play Services** , conforme mostrado na imagem a seguir. Em seguida, selecione **aplicar** para instalar. Anote o caminho do SDK, para utilização num passo posterior.

    ![SDK do Android Manager-Google Play Services selecionado](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Se você vir a caixa de diálogo **confirmar alteração** , selecione **OK**. O instalador de componentes instala os componentes solicitados. Selecione **concluir** depois que os componentes forem instalados.
4. Selecione **OK** para fechar a caixa **de diálogo Configurações de novos projetos** .  
1. Abra o arquivo AndroidManifest. xml e, em seguida, adicione a marca a seguir à marca do *aplicativo* .

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```


### <a name="add-azure-notification-hubs-libraries"></a>Adicionar bibliotecas de hubs de notificação do Azure

1. No arquivo Build. gradle para o aplicativo, adicione as linhas a seguir na seção dependências.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    ```

2. Adicione o repositório seguinte depois da secção de dependências.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="add-google-firebase-support"></a>Adicionar suporte do Google Firebase

1. No arquivo Build. gradle para o aplicativo, adicione as linhas a seguir na seção **dependências** , se elas ainda não existirem. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:17.3.4'
    ```

2. Adicione o plug-in a seguir no final do arquivo, se ele ainda não estiver lá. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```
3. Selecione **sincronizar agora** na barra de ferramentas.

### <a name="update-the-androidmanifestxml-file"></a>Atualizar o arquivo AndroidManifest. xml

1. Depois de receber o token de registro do FCM, use-o para [se registrar nos hubs de notificação do Azure](notification-hubs-push-notification-registration-management.md). Você dá suporte a esse registro em segundo plano usando um `IntentService` chamado `RegistrationIntentService`. Esse serviço também atualiza seu token de registro FCM. Você também cria uma classe chamada `FirebaseService` como uma subclasse de `FirebaseMessagingService` e substitui o método `onMessageReceived` para receber e manipular notificações. 

    Adicionar a seguinte definição de serviço ao ficheiro AndroidManifest.xml, no interior da etiqueta `<application>`.

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    <service
        android:name=".FirebaseService"
        android:exported="false">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT" />
        </intent-filter>
    </service>
    ```
3. Adicione as seguintes permissões necessárias relacionadas ao FCM abaixo da marca `</application>`.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="add-code"></a>Adicionar código

1. Na vista de projeto, expanda **app** > **src** > **principal** > **java**. Clique com o botão direito do mouse na pasta do pacote em **Java**, selecione **novo**e, em seguida, selecione **classe Java**. Digite **NotificationSettings** para o nome e, em seguida, selecione **OK**.

    Certifique-se de que atualiza esses três marcadores de posição no seguinte código para a classe `NotificationSettings`:

   * **HubListenConnectionString**: A cadeia de ligação **DefaultListenAccessSignature** do seu hub. Você pode copiar essa cadeia de conexão clicando em **políticas de acesso** em seu hub no [portal do Azure].
   * **HubName**: Use o nome do seu hub que aparece na página hub no [portal do Azure].

     Código `NotificationSettings`:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > Insira o **nome** e o **DefaultListenSharedAccessSignature** do seu hub antes de continuar. 

2. Adicione outra nova classe ao projeto com o nome `RegistrationIntentService`. Essa classe implementa a interface `IntentService`. Ele também manipula [a atualização do token FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) e o [registro com o Hub de notificação](notification-hubs-push-notification-registration-management.md).

    Utilize o seguinte código para esta classe.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;
    import com.google.android.gms.tasks.OnSuccessListener;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.google.firebase.iid.InstanceIdResult;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    import java.util.concurrent.TimeUnit;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";
        String FCM_token = null;

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
                FirebaseInstanceId.getInstance().getInstanceId().addOnSuccessListener(new OnSuccessListener<InstanceIdResult>() { 
                    @Override 
                    public void onSuccess(InstanceIdResult instanceIdResult) { 
                        FCM_token = instanceIdResult.getToken(); 
                        Log.d(TAG, "FCM Registration Token: " + FCM_token); 
                    } 
                }); 
                TimeUnit.SECONDS.sleep(1);

                // Storing the registration ID that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server.
                // Otherwise, your server should have already received the token.
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

                // Check to see if the token has been compromised and needs refreshing.
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
                // If an exception happens while fetching the new token or updating registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

3. Na classe `MainActivity`, adicione as seguintes instruções `import` acima da declaração de classe.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

4. Adicione os membros a seguir na parte superior da classe. Utiliza estes campos para [verificar a disponibilidade dos Serviços do Google Play, tal como recomendado pela Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

5. Na classe `MainActivity`, adicione o método a seguir para verificar a disponibilidade de Google Play Services.

    ```java
    /**
    * Check the device to make sure it has the Google Play Services APK. If
    * it doesn't, display a dialog box that enables  users to download the APK from
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

6. Na classe `MainActivity`, adicione o código a seguir que verifica Google Play Services antes de chamar o `IntentService` para obter o token de registro do FCM e registrá-lo no Hub:

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

7. No método `OnCreate` da classe `MainActivity`, adicione o seguinte código para iniciar o processo de registro quando a atividade for criada:

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        registerWithNotificationHubs();
        FirebaseService.createChannelAndHandleNotifications(getApplicationContext());
    }
    ```

8. Para verificar o estado do aplicativo e o status do relatório em seu aplicativo, adicione estes métodos adicionais a `MainActivity`:

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

9. O `ToastNotify` método utiliza o comando *"Olá, mundo"* `TextView` para comunicar o estado e as notificações de forma permanente na aplicação. Em seu layout **res** > **layout** > **activity_main. xml** , adicione a ID a seguir para esse controle.

    ```java
    android:id="@+id/text_hello"
    ```

    ![Notification Hubs do Azure – Teste de Envio](./media/notification-hubs-android-push-notification-google-fcm-get-started/activity-main-xml.png)

10. Em seguida, você adiciona uma subclasse para o receptor que você definiu em AndroidManifest. xml. Adicione outra nova classe ao projeto com o nome `FirebaseService`.

11. Adicione as seguintes declarações de importação no topo do ficheiro `FirebaseService.java`:

    ```java
    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;
    import android.util.Log;
    import android.app.NotificationChannel;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.media.RingtoneManager;
    import android.net.Uri;
    import android.os.Build;
    import android.os.Bundle;
    import androidx.core.app.NotificationCompat;
    ```

12. Adicione o código a seguir para a classe `FirebaseService`, tornando-o uma subclasse de `FirebaseMessagingService`.

    Esse código substitui o método `onMessageReceived` e relata as notificações que são recebidas. Ele também envia a notificação por push para o Gerenciador de notificações do Android usando o método `sendNotification()`. Chame o método `sendNotification()` quando o aplicativo não estiver em execução e uma notificação for recebida.

    ```java
    public class FirebaseService extends FirebaseMessagingService
    {
        private String TAG = "FirebaseService";
    
        public static final String NOTIFICATION_CHANNEL_ID = "nh-demo-channel-id";
        public static final String NOTIFICATION_CHANNEL_NAME = "Notification Hubs Demo Channel";
        public static final String NOTIFICATION_CHANNEL_DESCRIPTION = "Notification Hubs Demo Channel";
    
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        static Context ctx;
    
        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            // ...
    
            // TODO(developer): Handle FCM messages here.
            // Not getting messages here? See why this may be: https://goo.gl/39bRNJ
            Log.d(TAG, "From: " + remoteMessage.getFrom());
    
            String nhMessage;
            // Check if message contains a notification payload.
            if (remoteMessage.getNotification() != null) {
                Log.d(TAG, "Message Notification Body: " + remoteMessage.getNotification().getBody());
    
                nhMessage = remoteMessage.getNotification().getBody();
            }
            else {
                nhMessage = remoteMessage.getData().values().iterator().next();
            }
    
            // Also if you intend on generating your own notifications as a result of a received FCM
            // message, here is where that should be initiated. See sendNotification method below.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
            sendNotification(nhMessage);
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
            ctx = context;
    
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                NotificationChannel channel = new NotificationChannel(
                        NOTIFICATION_CHANNEL_ID,
                        NOTIFICATION_CHANNEL_NAME,
                        NotificationManager.IMPORTANCE_HIGH);
                channel.setDescription(NOTIFICATION_CHANNEL_DESCRIPTION);
                channel.setShowBadge(true);
    
                NotificationManager notificationManager = context.getSystemService(NotificationManager.class);
                notificationManager.createNotificationChannel(channel);
             }
        }
    }
    ```

13. No Android Studio, na barra de menus, selecione **build** > **Rebuild Project** para certificar-se de que não haja erros em seu código. Se você receber um erro sobre o ícone `ic_launcher`, remova a seguinte instrução do arquivo AndroidManifest. xml: 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
14. Verifique se você tem um dispositivo virtual para executar o aplicativo. Se você não tiver um, adicione um da seguinte maneira:
    1. ![Abrir o Gerenciador de dispositivos](./media/notification-hubs-android-push-notification-google-fcm-get-started/open-device-manager.png)
    2. ![Criar dispositivo virtual](./media/notification-hubs-android-push-notification-google-fcm-get-started/your-virtual-devices.PNG)

15. Execute o aplicativo no dispositivo selecionado e verifique se ele é registrado com êxito com o Hub.

    > [!NOTE]
    > O registro pode falhar durante a inicialização inicial até que o método `onTokenRefresh()` do serviço de ID de instância seja chamado. A atualização deve iniciar um registo com êxito no hub de notificação.

    ![Registro do dispositivo bem-sucedido](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Notificação de envio de teste a partir do hub de notificação

Você pode enviar notificações por push do [portal do Azure] executando as seguintes etapas:

1. Na portal do Azure, na página Hub de notificação do seu hub, selecione **teste enviar** na seção **solução de problemas** .
3. Para **Plataformas**, selecione **Android**.
4. Selecione **Enviar**.  Você ainda não verá uma notificação no dispositivo Android porque não executou o aplicativo móvel nele. Depois de executar o aplicativo móvel, selecione o botão **Enviar** novamente para ver a mensagem de notificação.
5. Consulte o resultado da operação na lista na parte inferior.

    ![Notification Hubs do Azure – Teste de Envio](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. Você vê a mensagem de notificação em seu dispositivo. 

    ![Mensagem de notificação no dispositivo](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>Executar o aplicativo móvel no emulador
Antes de testar as notificações por push dentro de um emulador, verifique se a imagem do emulador dá suporte ao nível de API do Google que você escolheu para seu aplicativo. Se sua imagem não der suporte a APIs nativas do Google, você poderá obter a exceção de **serviço @ no__t-1NOT @ no__t-2AVAILABLE** .

Verifique também se você adicionou sua conta do Google ao emulador em execução em **configurações** > **contas**. Caso contrário, suas tentativas de se registrar com FCM podem resultar na exceção de **autenticação @ no__t-1FAILED** .

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, você usou o firebase Cloud Messaging para transmitir notificações para todos os dispositivos Android que foram registrados com o serviço. Para saber como enviar notificações push para dispositivos específicos, avance para o tutorial seguinte:

> [!div class="nextstepaction"]
>[Tutorial: notificações por push para dispositivos Android específicos](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Portal do Azure]: https://portal.azure.com
