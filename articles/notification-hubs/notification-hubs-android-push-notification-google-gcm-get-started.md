---
title: Enviar notificações push para aplicações Android com Hubs de Notificação do Microsoft Azure e o Google Cloud Messaging | Microsoft Docs
description: Neste tutorial, vai aprender a utilizar os Hubs de Notificação do Microsoft Azure e o Google Firebase Cloud Messaging para enviar notificações push para dispositivos Android.
services: notification-hubs
documentationcenter: android
keywords: notificações push, notificação push, notificação push android
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 8268c6ef-af63-433c-b14e-a20b04a0342a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 6e82ec9563832c7569fa1cff735a46dad50a8b3b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57887583"
---
# <a name="tutorial-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-cloud-messaging"></a>Tutorial: Notificações push para dispositivos Android com Notification Hubs do Azure e o Google Cloud Messaging

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Descrição geral

Este tutorial mostra como utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação com Android.
Vai criar uma aplicação Android em branco para receber notificações push através do Google Cloud Messaging (GCM).

> [!IMPORTANT]
> O Google Cloud Messaging (GCM) foi preterido e será removida [em breve](https://developers.google.com/cloud-messaging/faq).

> [!IMPORTANT]
> Este tópico demonstra as notificações push com o Google Cloud Messaging (GCM). Se utiliza o Firebase Cloud Messaging (FCM) da Google, veja [Sending push notifications to Android with Azure Notification Hubs and FCM (Enviar notificações push para o Android com Hubs de Notificação do Azure e FCM)](notification-hubs-android-push-notification-google-fcm-get-started.md).

O código de conclusão para este tutorial pode ser transferido [aqui](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStarted) a partir do GitHub.

Neste tutorial, irá realizar as seguintes ações:

> [!div class="checklist"]
> * Criar um projeto que suporte o Google Cloud Messaging.
> * Criar um hub de notificação
> * Ligar a aplicação ao Notification Hub
> * Testar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição do Azure**. Se não tiver uma subscrição do Azure, [criar uma conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797).

## <a name="creating-a-project-that-supports-google-cloud-messaging"></a>Criar um projeto que suporte o Google Cloud Messaging

[!INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## <a name="create-a-notification-hub"></a>Criar um hub de notificação

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-setting-for-the-notification-hub"></a>Configurar a definição do GCM para o hub de notificação

1. Selecione **Google (GCM)** na **definições de notificação**.
2. Introduza a **Chave de API** que recebeu do Google Cloud Console.
3. Selecione **Guardar** na barra de ferramentas.

    ![Notification Hubs do Azure – Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

O Notification Hub está agora configurado para trabalhar com o GCM e tem as cadeias de ligação para registar a sua aplicação para receber e enviar notificações push.

## <a id="connecting-app"></a>Ligar a aplicação ao hub de notificação

### <a name="create-a-new-android-project"></a>Criar um novo projeto Android

1. No Android Studio, inicie um novo projeto de Android Studio.

   ![Android Studio - novo projeto][13]
2. Escolha o tamanho do **Telefone e tablet** e o **SDK mínimo** que pretende suportar. Clique depois em **Seguinte**.

   ![Android Studio – fluxo de trabalho da criação do projeto][14]
3. Escolha **Atividade Vazia** para a atividade principal, clique em **Seguinte** e, em seguida, clique em **Concluir**.

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

### <a name="updating-the-projects-androidmanifestxml"></a>A atualizar o Androidmanifest do projeto

1. Para suportar o GCM, implemente um serviço de escuta de ID da instância no código que é utilizado para [obter os tokens de registo](https://developers.google.com/cloud-messaging/android/client#sample-register), através da [API da ID de instância da Google](https://developers.google.com/instance-id/). Neste tutorial, o nome da classe é `MyInstanceIDService`.

    Adicionar a seguinte definição de serviço ao ficheiro AndroidManifest.xml, no interior da etiqueta `<application>`. Substitua o marcador de posição `<your package>` pelo nome do seu pacote real indicado no topo do ficheiro `AndroidManifest.xml`.
  
    ```xml
    <service android:name="<your package>.MyInstanceIDService" android:exported="false">
        <intent-filter>
            <action android:name="com.google.android.gms.iid.InstanceID"/>
        </intent-filter>
    </service>
    ```
2. Depois de a aplicação receber o token de registo do GCM da API da ID de Instância, o token é utilizado para [efetuar o registo no Hub de Notificação do Azure](notification-hubs-push-notification-registration-management.md). O registo em segundo plano é feito com um `IntentService` com o nome `RegistrationIntentService`. Este serviço também é responsável por [atualizar o token de registo do GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens).

    Adicionar a seguinte definição de serviço ao ficheiro AndroidManifest.xml, no interior da etiqueta `<application>`. Substitua o marcador de posição `<your package>` pelo nome do seu pacote real indicado no topo do ficheiro `AndroidManifest.xml`.

    ```xml
    <service
        android:name="<your package>.RegistrationIntentService"
        android:exported="false">
    </service>
    ```
3. Defina um recetor para receber as notificações. Adicione a seguinte definição do recetor ao ficheiro AndroidManifest.xml, no interior da etiqueta `<application>`. Substitua o marcador de posição `<your package>` pelo nome do seu pacote real indicado no topo do ficheiro `AndroidManifest.xml`.

    ```xml
    <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <category android:name="<your package name>" />
        </intent-filter>
    </receiver>
    ```
4. Adicione as seguintes permissões do GCM por baixo da etiqueta `</application>`. Substitua `<your package>` pelo nome do pacote mostrado no topo do ficheiro `AndroidManifest.xml`.

    Para obter mais informações sobre estas permissões, consulte o artigo [Configurar uma aplicação de cliente do GCM para Android](https://developers.google.com/cloud-messaging/android/client#manifest).

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="android.permission.WAKE_LOCK"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

    <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
    <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>
    ```

### <a name="adding-code"></a>Adicionar código

1. Na vista de projeto, expanda **app** > **src** > **principal** > **java**. Com o botão direito do rato, clique na pasta do pacote por baixo de **java**, clique em **Nova**, e, em seguida, clique em **Classe Java**. Adicione uma nova classe com o nome `NotificationSettings`.

    ![Android Studio - nova classe Java][6]

    Atualize os três marcadores de posição no seguinte código da classe `NotificationSettings`:

   * `SenderId`: O número de projeto que obteve anteriormente no [Google Cloud Console](https://cloud.google.com/console).
   * `HubListenConnectionString`: O `DefaultListenAccessSignature` cadeia de ligação para o seu hub. Pode copiar essa cadeia de ligação ao clicar em **Políticas de Acesso** na página **Definições** do hub no [Portal do Azure].
   * `HubName`: Utilize o nome do notification hub que aparece na página do hub no [portal do Azure].

     Código `NotificationSettings`:

     ```java
     public class NotificationSettings {
        public static String SenderId = "<Your project number>";
        public static String HubName = "<Your HubName>";
        public static String HubListenConnectionString = "<Your default listen connection string>";
     }
     ```
2. Adicione outra nova classe com o nome `MyInstanceIDService`. Esta classe é a implementação do serviço de escuta de ID da Instância.

    O código para esta classe chama o `IntentService` para [atualizar o token do GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) em segundo plano.

    ```java
    import android.content.Intent;
    import android.util.Log;
    import com.google.android.gms.iid.InstanceIDListenerService;

    public class MyInstanceIDService extends InstanceIDListenerService {

        private static final String TAG = "MyInstanceIDService";

        @Override
        public void onTokenRefresh() {

            Log.i(TAG, "Refreshing GCM Registration Token");

            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    };
    ```
3. Adicione outra nova classe ao projeto com o nome `RegistrationIntentService`. Esta classe implementa o `IntentService`, que processa a [atualização do token do GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) e o [registo no hub de notificação](notification-hubs-push-notification-registration-management.md).

    Utilize o seguinte código para esta classe.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;

    import com.google.android.gms.gcm.GoogleCloudMessaging;
    import com.google.android.gms.iid.InstanceID;
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

            try {
                InstanceID instanceID = InstanceID.getInstance(this);
                String token = instanceID.getToken(NotificationSettings.SenderId,
                        GoogleCloudMessaging.INSTANCE_ID_SCOPE);
                Log.i(TAG, "Got GCM Registration Token: " + token);

                // Storing the registration id that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server,
                // otherwise your server should have already received the token.
                if ((regID=sharedPreferences.getString("registrationID", null)) == null) {
                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.i(TAG, "Attempting to register with NH using token : " + token);

                    regID = hub.register(token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1", "tag2").getRegistrationId();

                    resultString = "Registered Successfully - RegId : " + regID;
                    Log.i(TAG, resultString);
                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                } else {
                    resultString = "Previously Registered Successfully - RegId : " + regID;
                }
            } catch (Exception e) {
                Log.e(TAG, resultString="Failed to complete token refresh", e);
                // If an exception happens while fetching the new token or updating the registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```
4. Na classe `MainActivity`, adicione as declarações `import` seguintes no início da classe.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import com.google.android.gms.gcm.*;
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    import android.content.Intent;
    ```
5. Adicione os seguintes membros privados no topo da classe. Este código [verifica a disponibilidade dos serviços do Google Play, tal como recomendado pela Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private GoogleCloudMessaging gcm;
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    private static final String TAG = "MainActivity";
    ```
6. Na sua classe `MainActivity`, adicione o seguinte método para a disponibilidade dos serviços do Google Play.

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
7. Na classe `MainActivity`, adicione o seguinte código que verifica os Serviços do Google Play antes de chamar o `IntentService` para obter o token de registo do GCM e efetuar o registo no hub de notificação.

    ```java
    public void registerWithNotificationHubs()
    {
        Log.i(TAG, " Registering with Notification Hubs");

        if (checkPlayServices()) {
            // Start IntentService to register this application with GCM.
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
        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
        registerWithNotificationHubs();
    }
    ```
9. Adicione estes métodos adicionais a `MainActivity` para verificar o estado da aplicação e do relatório na sua aplicação.

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
10. O `ToastNotify` método utiliza o comando *"Olá, mundo"* `TextView` para comunicar o estado e as notificações de forma permanente na aplicação. No seu ctivity_main.xml layout, adicione a seguinte ID para esse comando.

    ```xml
    android:id="@+id/text_hello"
    ```
11. Adicione uma subclasse do recetor que está definida no AndroidManifest.xml. Adicione outra nova classe ao projeto com o nome `MyHandler`.
12. Adicione as seguintes declarações de importação no topo do ficheiro `MyHandler.java`:

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.os.Bundle;
    import android.support.v4.app.NotificationCompat;
    import com.microsoft.windowsazure.notifications.NotificationsHandler;
    import android.net.Uri;
    import android.media.RingtoneManager;
    ```
13. Adicione o seguinte código à classe `MyHandler`, tornando-a numa subclasse de `com.microsoft.windowsazure.notifications.NotificationsHandler`.

    Este código substitui o método `OnReceive` para que o processador comunique as notificações que são recebidas. O processador também envia a notificação push para o gestor de notificações Android através do método `sendNotification()`. O método `sendNotification()` deve ser executado quando a aplicação não está em execução e deverá receber uma notificação.

    ```java
    public class MyHandler extends NotificationsHandler {
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
            NotificationCompat.Builder mBuilder =
                    new NotificationCompat.Builder(ctx)
                            .setSmallIcon(R.mipmap.ic_launcher)
                            .setContentTitle("Notification Hub Demo")
                            .setStyle(new NotificationCompat.BigTextStyle()
                                    .bigText(msg))
                            .setSound(defaultSoundUri)
                            .setContentText(msg);

            mBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }
    }
    ```
14. No Android Studio, na barra de menus, clique em **Construir** > **Reconstruir projeto** para se certificar de que não existem erros no seu código.

## <a name="testing-your-app"></a>Testar a aplicação

### <a name="run-the-mobile-application"></a>Executar a aplicação móvel

1. Execute a aplicação e repare que a ID de registo é comunicada para um registo de sucesso.

      ![Teste no Android - registo de canal][18]
2. Introduza uma mensagem de notificação que será enviada a todos os dispositivos Android registados no hub.

      ![Teste no Android - enviar uma mensagem][19]

3. Prima **Enviar notificação**. Todos os dispositivos que estejam a executar a aplicação mostram uma `AlertDialog` instância com a mensagem de notificação push. Dispositivos que não estejam a executar a aplicação, mas foram anteriormente registados para as notificações push, recebem uma notificação no gestor de notificações do Android. As mensagens de notificações podem ser visualizadas ao percorrer para baixo a partir do canto superior esquerdo.

      ![Teste no Android - notificações][21]

### <a name="test-send-push-notifications-from-the-azure-portal"></a>Testar o envio de notificações push a partir do portal do Azure

Pode testar a receção das notificações push na aplicação ao enviar notificações através do [portal do Azure].

1. Na secção **Resolução de Problemas**, selecione **Testar Envio**.
2. Em **Plataformas**, selecione **Android**.
3. Selecione **Enviar** para enviar a notificação de teste.
4. Confirme que vê a mensagem de notificação no dispositivo Android.

    ![Notification Hubs do Azure – Teste de Envio](./media/notification-hubs-android-get-started/notification-hubs-test-send.png)

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

#### <a name="push-notifications-in-the-emulator"></a>Notificações push no emulador

Se pretender testar as notificações push no interior de um emulador, certifique-se de que a imagem do emulador suporta o nível da API da Google que escolheu para a sua aplicação. Se a imagem não suportar as APIs nativas da Google, termina com a exceção **SERVIÇO\_NÃO\_DISPONÍVEL**.

Além disso, confirme que adicionou a sua conta Google ao emulador em execução em **Definições** > **Contas**. Caso contrário, as tentativas para registar o GCM podem originar a exceção **FALHA DE\_AUTENTICAÇÃO**.

## <a name="optional-send-push-notifications-directly-from-the-app"></a>(Opcional) Enviar notificações push diretamente a partir da aplicação

Normalmente, enviaria notificações através de um servidor de backend. Em alguns casos, poderia querer enviar notificações push diretamente a partir da aplicação de cliente. Esta secção explica como enviar notificações do cliente utilizando a [API REST do Notification Hub do Azure](https://msdn.microsoft.com/library/azure/dn223264.aspx).

1. Na vista de projeto do Android Studio, expanda **App** > **src** > **main** > **res** > **esquema**. Abra o `activity_main.xml` ficheiro de esquema e clique no separador **Texto** para atualizar o conteúdo de texto do ficheiro. Atualize-o com o código abaixo, que adiciona novos `Button` e comandos `EditText` para o envio de mensagens de notificação push para o Notification Hub. Adicione este código na parte inferior, imediatamente antes de `</RelativeLayout>`.

    ```xml
    <Button
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/send_button"
    android:id="@+id/sendbutton"
    android:layout_centerVertical="true"
    android:layout_centerHorizontal="true"
    android:onClick="sendNotificationButtonOnClick" />

    <EditText
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:id="@+id/editTextNotificationMessage"
    android:layout_above="@+id/sendbutton"
    android:layout_centerHorizontal="true"
    android:layout_marginBottom="42dp"
    android:hint="@string/notification_message_hint" />
    ```
2. Na vista de projeto do Android Studio, expanda **App** > **src** > **main** > **res** > **valores**. Abra o ficheiro `strings.xml` e adicione os valores de cadeia que são referenciados pelo novo `Button` e pelos comandos `EditText`. Adicione as seguintes linhas na parte inferior do ficheiro, imediatamente antes de `</resources>`.

    ```xml
    <string name="send_button">Send Notification</string>
    <string name="notification_message_hint">Enter notification message text</string>
    ```
3. No seu ficheiro `NotificationSetting.java`, adicione a seguinte definição à classe `NotificationSettings`.

    Atualize `HubFullAccess` com a cadeia de ligação **DefaultFullSharedAccessSignature** para o seu hub. Esta cadeia de ligação pode ser copiada do [portal do Azure] ao clicar em **Políticas de Acesso** na página **Definições** do hub de notificação.

    ```java
    public static String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";
    ```
4. No ficheiro `MainActivity.java`, adicione as declarações `import` seguintes no início do ficheiro.

    ```java
    import java.io.BufferedOutputStream;
    import java.io.BufferedReader;
    import java.io.InputStreamReader;
    import java.io.OutputStream;
    import java.net.HttpURLConnection;
    import java.net.URL;
    import java.net.URLEncoder;
    import javax.crypto.Mac;
    import javax.crypto.spec.SecretKeySpec;
    import android.util.Base64;
    import android.view.View;
    import android.widget.EditText;
    ```
5. No seu ficheiro `MainActivity.java`, adicione as seguintes declarações acima da classe `MainActivity`.

    ```java
    private String HubEndpoint = null;
    private String HubSasKeyName = null;
    private String HubSasKeyValue = null;
    ```
6. Crie um token de Assinatura de Acesso ao Software (SaS) para autenticar um pedido POST para enviar mensagens para o hub de notificação. Analise os dados-chave da cadeia de ligação e, em seguida, crie o token SaS, tal como mencionado na referência da API REST dos [Conceitos Comuns](https://msdn.microsoft.com/library/azure/dn495627.aspx). O código seguinte é uma implementação de exemplo.

    No `MainActivity.java`, adicione o método seguinte à classe `MainActivity` para analisar a cadeia de ligação.

    ```java
    /**
        * Example code from https://msdn.microsoft.com/library/azure/dn495627.aspx
        * to parse the connection string so a SaS authentication token can be
        * constructed.
        *
        * @param connectionString This must be the DefaultFullSharedAccess connection
        *                         string for this example.
        */
    private void ParseConnectionString(String connectionString)
    {
        String[] parts = connectionString.split(";");
        if (parts.length != 3)
            throw new RuntimeException("Error parsing connection string: "
                    + connectionString);

        for (int i = 0; i < parts.length; i++) {
            if (parts[i].startsWith("Endpoint")) {
                this.HubEndpoint = "https" + parts[i].substring(11);
            } else if (parts[i].startsWith("SharedAccessKeyName")) {
                this.HubSasKeyName = parts[i].substring(20);
            } else if (parts[i].startsWith("SharedAccessKey")) {
                this.HubSasKeyValue = parts[i].substring(16);
            }
        }
    }
    ```
7. No `MainActivity.java`, adicione o método seguinte à classe `MainActivity` para criar o token de autenticação SaS.

    ```java
    /**
        * Example code from https://msdn.microsoft.com/library/azure/dn495627.aspx to
        * construct a SaS token from the access key to authenticate a request.
        *
        * @param uri The unencoded resource URI string for this operation. The resource
        *            URI is the full URI of the Service Bus resource to which access is
        *            claimed. For example,
        *            "http://<namespace>.servicebus.windows.net/<hubName>"
        */
    private String generateSasToken(String uri) {

        String targetUri;
        String token = null;
        try {
            targetUri = URLEncoder
                    .encode(uri.toString().toLowerCase(), "UTF-8")
                    .toLowerCase();

            long expiresOnDate = System.currentTimeMillis();
            int expiresInMins = 60; // 1 hour
            expiresOnDate += expiresInMins * 60 * 1000;
            long expires = expiresOnDate / 1000;
            String toSign = targetUri + "\n" + expires;

            // Get an hmac_sha1 key from the raw key bytes
            byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
            SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");

            // Get an hmac_sha1 Mac instance and initialize with the signing key
            Mac mac = Mac.getInstance("HmacSHA256");
            mac.init(signingKey);

            // Compute the hmac on input data bytes
            byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));

            // Using android.util.Base64 for Android Studio instead of
            // Apache commons codec
            String signature = URLEncoder.encode(
                    Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");

            // Construct authorization string
            token = "SharedAccessSignature sr=" + targetUri + "&sig="
                    + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
        } catch (Exception e) {
            if (isVisible) {
                ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
            }
        }

        return token;
    }
    ```
8. No `MainActivity.java`, adicione o método seguinte à classe `MainActivity` para premir o botão **Enviar notificação**. Clique e envie a mensagem de notificação push ao hub, utilizando a API REST incorporada.

    ```java
    /**
        * Send Notification button click handler. This method parses the
        * DefaultFullSharedAccess connection string and generates a SaS token. The
        * token is added to the Authorization header on the POST request to the
        * notification hub. The text in the editTextNotificationMessage control
        * is added as the JSON body for the request to add a GCM message to the hub.
        *
        * @param v
        */
    public void sendNotificationButtonOnClick(View v) {
        EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
        final String json = "{\"data\":{\"message\":\"" + notificationText.getText().toString() + "\"}}";

        new Thread()
        {
            public void run()
            {
                try
                {
                    // Based on reference documentation...
                    // https://msdn.microsoft.com/library/azure/dn223273.aspx
                    ParseConnectionString(NotificationSettings.HubFullAccess);
                    URL url = new URL(HubEndpoint + NotificationSettings.HubName +
                            "/messages/?api-version=2015-01");

                    HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();

                    try {
                        // POST request
                        urlConnection.setDoOutput(true);

                        // Authenticate the POST request with the SaS token
                        urlConnection.setRequestProperty("Authorization",
                            generateSasToken(url.toString()));

                        // Notification format should be GCM
                        urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");

                        // Include any tags
                        // Example below targets 3 specific tags
                        // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                        // urlConnection.setRequestProperty("ServiceBusNotification-Tags",
                        //        "tag1 || tag2 || tag3");

                        // Send notification message
                        urlConnection.setFixedLengthStreamingMode(json.length());
                        OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
                        bodyStream.write(json.getBytes());
                        bodyStream.close();

                        // Get response
                        urlConnection.connect();
                        int responseCode = urlConnection.getResponseCode();
                        if ((responseCode != 200) && (responseCode != 201)) {
                            BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                            String line;
                            StringBuilder builder = new StringBuilder("Send Notification returned " +
                                    responseCode + " : ")  ;
                            while ((line = br.readLine()) != null) {
                                builder.append(line);
                            }

                            ToastNotify(builder.toString());
                        }
                    } finally {
                        urlConnection.disconnect();
                    }
                }
                catch(Exception e)
                {
                    if (isVisible) {
                        ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
                    }
                }
            }
        }.start();
    }
    ```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, enviou notificações de difusão para todos os dispositivos Android registados no back-end. Para saber como enviar notificações push para dispositivos Android específicos, avance para o tutorial seguinte:  

 > [!div class="nextstepaction"]
 > [Enviar notificações push para dispositivos específicos](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png
[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png
[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png
[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md 
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Portal do Azure]: https://portal.azure.com
