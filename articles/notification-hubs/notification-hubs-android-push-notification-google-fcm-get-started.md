---
title: Envie notificações push para Android usando Azure Notification Hubs e Firebase SDK versão 0.6 | Microsoft Docs
description: Neste tutorial, aprende a usar os Hubs de Notificação Azure e as Mensagens Cloud do Google Firebase para enviar notificações push para dispositivos Android (versão 0.6).
services: notification-hubs
documentationcenter: android
keywords: notificações push, notificação push, notificação push para o android, fcm, firebase cloud messaging
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc, devx-track-java
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 09/11/2019
ms.openlocfilehash: c5485dacc4d9e3210ad69819caf4e36f96c626da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92428373"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-firebase-sdk-version-06"></a>Tutorial: Enviar notificações push para dispositivos Android usando a versão 0.6 da Firebase SDK

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Este tutorial mostra-lhe como usar os Hubs de Notificação Azure e a versão SDK SDK (FCM) para enviar notificações push para uma aplicação Android. Neste tutorial, irá criar uma aplicação Android em branco que recebe notificações push através do Firebase Cloud Messaging (FCM).

O código completo para este tutorial pode ser descarregado [a partir do GitHub.](https://github.com/Azure/azure-notificationhubs-android/tree/master/FCMTutorialApp)

Neste tutorial, siga os seguintes passos:

> [!div class="checklist"]
> * Criar um projeto do Android Studio.
> * Criar um projeto do Firebase que suporte o Firebase Cloud Messaging.
> * Criar um centro.
> * Ligue a sua aplicação ao centro.
> * Testar a aplicação.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/free/). 

Também precisa dos seguintes itens: 

* A mais recente versão do [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)
* Android 2.3 ou superior para Mensagens Cloud Firebase
* Revisão do Google Repositório 27 ou superior para mensagens em nuvem de base de fogo
* Google Play Services 9.0.2 ou superior para mensagens em nuvem de base de fogo

Completar este tutorial é um pré-requisito para fazer todos os outros tutoriais de Notification Hubs para aplicações Android.

## <a name="create-an-android-studio-project"></a>Criar um projeto do Android Studio

1. Lançamento Do Android Studio.
2. Selecione **'Ficheiro',** aponte para **Novo** e, em seguida, selecione **Novo Projeto**. 
2. Na página Escolha a **página do projeto,** selecione **Atividade Vazia** e, em seguida, selecione **Seguinte**. 
3. Na página **Configure** o seu projeto, tome os seguintes passos: 
    1. Introduza um nome para a aplicação.
    2. Especifique um local para guardar os ficheiros do projeto. 
    3. Selecione **Concluir**. 

        ![Configure o seu projeto)](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Criar um projeto do Firebase que suporte o FCM

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-hub"></a>Configure um hub

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Configurar as definições do Firebase Cloud Messaging para o hub

1. No painel esquerdo, em **Definições,** selecione **Google (GCM/FCM)**. 
2. Introduza a **tecla** do servidor para o projeto FCM que guardou anteriormente. 
3. Na barra de ferramentas, **selecione Guardar**. 

    ![Azure Notification Hub - Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. O portal Azure apresenta uma mensagem em alertas de que o hub foi atualizado com sucesso. O botão **Guardar** está desativado. 

O seu centro está agora configurado para trabalhar com mensagens cloud firebase. Também tem as cadeias de ligação necessárias para enviar notificações para um dispositivo e registar uma aplicação para receber notificações.

## <a name="connect-your-app-to-the-notification-hub"></a><a id="connecting-app"></a>Ligar a aplicação ao hub de notificação

### <a name="add-google-play-services-to-the-project"></a>Adicionar serviços do Google Play para o projeto

1. No Android Studio, selecione **Tools** no menu e, em seguida, selecione **SDK Manager**. 
2. Selecione a versão alvo do Android SDK que é usado no seu projeto. Em seguida, selecione **Mostrar Detalhes do Pacote**. 

    ![Android SDK Manager - selecione versão-alvo](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Selecione **APIs do Google,** se ainda não estiver instalado.

    ![Android SDK Manager - Google APIs selecionado](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Mude para o separador **Ferramentas SDK.** Se ainda não instalou o Google Play Services, selecione **o Google Play Services** como mostrado na imagem seguinte. Em seguida, **selecione Aplicar** para instalar. Anote o caminho do SDK, para utilização num passo posterior.

    ![Android SDK Manager - Google Play Services selecionados](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Se vir a caixa de diálogo **'Alterar'** confirmando,selecione **OK**. O Instalador de Componentes instala os componentes solicitados. **Selecione Acabamento** após a instalação dos componentes.
4. Selecione **OK** para fechar a caixa de diálogo **De Definições para Novos Projetos.**  
1. Abra o ficheiro AndroidManifest.xml e, em seguida, adicione a seguinte etiqueta à etiqueta *de inscrição.*

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```


### <a name="add-azure-notification-hubs-libraries"></a>Adicionar bibliotecas Azure Notification Hubs

1. No ficheiro Build.Gradle para a aplicação, adicione as seguintes linhas na secção dependências.

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

1. No ficheiro Build.Gradle para a aplicação, adicione as **seguintes linhas** na secção de dependências se elas já não existirem. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:17.3.4'
    ```

2. Adicione o seguinte plug-in no final do ficheiro se ainda não estiver lá. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```
3. **Selecione Sync Now** na barra de ferramentas.

### <a name="update-the-androidmanifestxml-file"></a>Atualizar o ficheiro AndroidManifest.xml

1. Depois de receber o seu token de registo FCM, utilize-o para [se registar nos Hubs de Notificação Azure.](notification-hubs-push-notification-registration-management.md) Você apoia este registo em segundo plano usando um `IntentService` nome `RegistrationIntentService` . Este serviço também atualiza o seu token de registo FCM. Também cria uma classe chamada `FirebaseService` como subclasse `FirebaseMessagingService` e substitui o método para receber e lidar com `onMessageReceived` notificações. 

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
3. Adicione as seguintes permissões relacionadas com a FCM abaixo da `</application>` etiqueta.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="add-code"></a>Adicionar código

1. No Project View, expanda **a app**  >  **src**  >  **main**  >  **java.** Clique com o botão direito na pasta do pacote em **java,** selecione **New**, e, em seguida, selecione **Java Class**. Introduza **as definições de notificações** para o nome e, em seguida, selecione **OK**.

    Certifique-se de que atualiza esses três marcadores de posição no seguinte código para a classe `NotificationSettings`:

   * **HubListenConnectionString**: A cadeia de ligação **DefaultListenAccessSignature** do seu hub. Pode copiar essa cadeia de ligação clicando em Políticas de **Acesso** no seu hub no [portal Azure].
   * **HubName**: Use o nome do seu hub que aparece na página do hub no [portal Azure].

     Código `NotificationSettings`:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > Insira o **nome** e o **DefaultListenPartessssature** do seu hub antes de prosseguir. 

2. Adicione outra nova classe ao projeto com o nome `RegistrationIntentService`. Esta classe implementa a `IntentService` interface. Também trata de [refrescar o token da FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) e [registar-se com o centro de notificação.](notification-hubs-push-notification-registration-management.md)

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
               else if (!(storedToken = sharedPreferences.getString("FCMtoken", "")).equals(FCM_token)) {

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

3. Na `MainActivity` classe, adicione as `import` seguintes declarações acima da declaração de classe.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

4. Adicione os seguintes membros no topo da classe. Utiliza estes campos para [verificar a disponibilidade dos Serviços do Google Play, tal como recomendado pela Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

5. Na `MainActivity` aula, adicione o seguinte método para verificar a disponibilidade dos Serviços Google Play.

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

6. Na `MainActivity` classe, adicione o seguinte código que verifica os Serviços google Play antes de ligar para obter o `IntentService` seu token de registo FCM e registar-se no seu hub:

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

7. No `OnCreate` método da `MainActivity` classe, adicione o seguinte código para iniciar o processo de registo quando a atividade for criada:

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

8. Para verificar o estado da aplicação e o estado do relatório na sua aplicação, adicione estes métodos adicionais `MainActivity` para:

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

9. O `ToastNotify` método utiliza o comando *"Olá, mundo"* `TextView` para comunicar o estado e as notificações de forma permanente na aplicação. No seu layout **res**  >    >  **activity_main.xml** layout, adicione o seguinte ID para esse controlo.

    ```java
    android:id="@+id/text_hello"
    ```

    ![Screenshot que mostra o id android:id="@id/text_hello" id aplicado ao controlo TextView.](./media/notification-hubs-android-push-notification-google-fcm-get-started/activity-main-xml.png)

10. Em seguida, adicione uma subclasse para o recetor que definiu em AndroidManifest.xml. Adicione outra nova classe ao projeto com o nome `FirebaseService`.

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

12. Adicione o seguinte código para a `FirebaseService` classe, tornando-o uma subclasse de `FirebaseMessagingService` .

    Este código substitui o `onMessageReceived` método e informa as notificações recebidas. também envia a notificação push para o gestor de notificação Android usando o `sendNotification()` método. Ligue para o `sendNotification()` método quando a aplicação não estiver em execução e uma notificação é recebida.

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

13. No Android Studio, na barra de menus, selecione **Build**  >  **Rebuild Project** para garantir que não há erros no seu código. Se receber um erro sobre o `ic_launcher` ícone, remova a seguinte declaração do ficheiro AndroidManifest.xml: 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
14. Certifique-se de que tem um dispositivo virtual para executar a aplicação. Se não tiver uma, adicione uma da seguinte forma:
    1. ![Gestor de dispositivo aberto](./media/notification-hubs-android-push-notification-google-fcm-get-started/open-device-manager.png)
    2. ![Criar dispositivo virtual](./media/notification-hubs-android-push-notification-google-fcm-get-started/your-virtual-devices.PNG)

15. Execute a aplicação no seu dispositivo selecionado e verifique se se regista com sucesso com o hub.

    > [!NOTE]
    > O registo pode falhar durante o lançamento inicial até que o `onTokenRefresh()` método do serviço de identificação de instância seja chamado. A atualização deve iniciar um registo com êxito no hub de notificação.

    ![Registo do dispositivo bem sucedido](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Notificação de envio de teste a partir do hub de notificação

Pode enviar notificações push a partir do [portal Azure,] tomando os seguintes passos:

1. No portal Azure, na página 'Centro de Notificação' para o seu hub, selecione **Enviar** na secção **resolução de problemas.**
3. Para **Plataformas**, selecione **Android**.
4. Selecione **Enviar**.  Ainda não vai ver uma notificação no dispositivo Android porque ainda não executou a aplicação móvel no mesmo. Depois de executar a aplicação móvel, selecione novamente o botão **Enviar** para ver a mensagem de notificação.
5. Veja o resultado da operação na lista na parte inferior.

    ![Notification Hubs do Azure – Teste de Envio](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. Vê a mensagem de notificação no seu dispositivo. 

    ![Mensagem de notificação no dispositivo](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>Executar a aplicação móvel no emulador

Antes de testar notificações push dentro de um emulador, certifique-se de que a sua imagem emuladora suporta o nível de API do Google que escolheu para a sua aplicação. Se a sua imagem não suportar APIs nativos do Google, poderá obter a exceção **SERVICE \_ NOT \_ AVAILABLE.**

Certifique-se também de que adicionou a sua conta Google ao seu emulador de funcionamento em **Contas de**  >  **Definições.** Caso contrário, as suas tentativas de registo com a FCM podem resultar na **autenticação \_ falhada** da exceção.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, utilizou o Firebase Cloud Messaging para transmitir notificações a todos os dispositivos Android que foram registados no serviço. Para saber como enviar notificações push para dispositivos específicos, avance para o tutorial seguinte:

> [!div class="nextstepaction"]
>[Tutorial: Empurre notificações para dispositivos Android específicos](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Portal do Azure]: https://portal.azure.com
