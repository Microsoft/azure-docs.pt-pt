---
title: Enviar notificações para aplicações específicas do Android usando hubs de notificação do Azure
description: Saiba como enviar notificações push para utilizadores android específicos utilizando os Hubs de Notificação Azure.
documentationcenter: android
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc, devx-track-java
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: c137cbf0c2290bdbe228f7902e616e7f79cb459e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016986"
---
# <a name="tutorial-send-push-notification-to-specific-android-users-using-azure-notification-hubs-and-google-cloud-messaging-deprecated"></a>Tutorial: Enviar notificações push para utilizadores android específicos usando hubs de notificação Azure e Mensagens Google Cloud (depreciada)

> [!WARNING]
> A partir de 10 de abril de 2018, a Google depreifou o Google Cloud Messaging (GCM). O servidor GCM e as APIs do cliente são depreciadas e serão removidas logo a 29 de maio de 2019. Para obter mais informações, consulte [GCM e FCM Perguntas Frequentes.](https://developers.google.com/cloud-messaging/faq)

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

Este tutorial mostra-lhe como utilizar os Hubs de Notificação do Azure para enviar notificações push para um utilizador específico da aplicação num dispositivo específico. Um back-end WebAPI de ASP.NET é utilizado para autenticar clientes e gerar notificações, conforme mostrado no artigo de documentação de orientação [Registar-se a partir do back-end da aplicação](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend). Este tutorial baseia-se no hub de notificação que criou no [Tutorial: Enviar notificações push para dispositivos Android com os Hubs de Notificação do Azure e o Google Cloud Messaging](notification-hubs-android-push-notification-google-gcm-get-started.md).

Neste tutorial, siga os seguintes passos:

> [!div class="checklist"]
> * Criar o projeto de back-end WebAPI que autentica os utilizadores.  
> * Atualizar a aplicação Android.
> * Testar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

Conclua o [Tutorial: Enviar notificações push para dispositivos Android com os Hubs de Notificação do Azure e o Google Cloud Messaging](notification-hubs-android-push-notification-google-gcm-get-started.md) antes de levar a cabo este tutorial.

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="create-the-android-project"></a>Criar o Projeto para Android

O passo seguinte consiste em atualizar a aplicação Android criada no [Tutorial: Enviar notificações push para dispositivos Android com os Hubs de Notificação do Azure e o Google Cloud Messaging](notification-hubs-android-push-notification-google-gcm-get-started.md).

1. Abra o seu `res/layout/activity_main.xml` ficheiro, substitua as seguintes definições de conteúdo:

    Adiciona novos controlos EditText para iniciar sessão como um utilizador. Também é adicionado um campo para uma etiqueta username que irá fazer parte de notificações que enviar:

    ```xml
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".MainActivity">

    <EditText
        android:id="@+id/usernameText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="@string/usernameHint"
        android:layout_above="@+id/passwordText"
        android:layout_alignParentEnd="true" />
    <EditText
        android:id="@+id/passwordText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="@string/passwordHint"
        android:inputType="textPassword"
        android:layout_above="@+id/buttonLogin"
        android:layout_alignParentEnd="true" />
    <Button
        android:id="@+id/buttonLogin"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/loginButton"
        android:onClick="login"
        android:layout_above="@+id/toggleButtonGCM"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="24dp" />
    <ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textOn="WNS on"
        android:textOff="WNS off"
        android:id="@+id/toggleButtonWNS"
        android:layout_toLeftOf="@id/toggleButtonGCM"
        android:layout_centerVertical="true" />
    <ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textOn="GCM on"
        android:textOff="GCM off"
        android:id="@+id/toggleButtonGCM"
        android:checked="true"
        android:layout_centerHorizontal="true"
        android:layout_centerVertical="true" />
    <ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textOn="APNS on"
        android:textOff="APNS off"
        android:id="@+id/toggleButtonAPNS"
        android:layout_toRightOf="@id/toggleButtonGCM"
        android:layout_centerVertical="true" />
    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessageTag"
        android:layout_below="@id/toggleButtonGCM"
        android:layout_centerHorizontal="true"
        android:hint="@string/notification_message_tag_hint" />
    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_below="@+id/editTextNotificationMessageTag"
        android:layout_centerHorizontal="true"
        android:hint="@string/notification_message_hint" />
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:onClick="sendNotificationButtonOnClick"
        android:layout_below="@+id/editTextNotificationMessage"
        android:layout_centerHorizontal="true" />
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        android:id="@+id/text_hello"
    />  
    </RelativeLayout>
    ```
2. Abra o `res/values/strings.xml` seu ficheiro e substitua a `send_button` definição pelas seguintes linhas que redefinem a cadeia para o `send_button` e adicione cordas para os outros controlos:

    ```xml
    <string name="usernameHint">Username</string>
    <string name="passwordHint">Password</string>
    <string name="loginButton">1. Sign in</string>
    <string name="send_button">2. Send Notification</string>
    <string name="notification_message_hint">Notification message</string>
    <string name="notification_message_tag_hint">Recipient username</string>
    ```

    O seu `main_activity.xml` layout gráfico deve agora parecer-se com a seguinte imagem:

    ![Screenshot de uma aplicação com caixas para o nome de utilizador, palavra-passe, destinatário e mensagem, e com botões para iniciar e enviar notificações.][A1]
3. Crie uma nova classe chamada `RegisterClient` no mesmo pacote que a sua `MainActivity` turma. Utilize o código abaixo para o ficheiro da nova classe.

    ```java
    import java.io.IOException;
    import java.io.UnsupportedEncodingException;
    import java.util.Set;

    import org.apache.http.HttpResponse;
    import org.apache.http.HttpStatus;
    import org.apache.http.client.ClientProtocolException;
    import org.apache.http.client.HttpClient;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.client.methods.HttpPut;
    import org.apache.http.client.methods.HttpUriRequest;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.DefaultHttpClient;
    import org.apache.http.util.EntityUtils;
    import org.json.JSONArray;
    import org.json.JSONException;
    import org.json.JSONObject;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.util.Log;

    public class RegisterClient {
        private static final String PREFS_NAME = "ANHSettings";
        private static final String REGID_SETTING_NAME = "ANHRegistrationId";
        private String Backend_Endpoint;
        SharedPreferences settings;
        protected HttpClient httpClient;
        private String authorizationHeader;

        public RegisterClient(Context context, String backendEndpoint) {
            super();
            this.settings = context.getSharedPreferences(PREFS_NAME, 0);
            httpClient =  new DefaultHttpClient();
            Backend_Endpoint = backendEndpoint + "/api/register";
        }

        public String getAuthorizationHeader() {
            return authorizationHeader;
        }

        public void setAuthorizationHeader(String authorizationHeader) {
            this.authorizationHeader = authorizationHeader;
        }

        public void register(String handle, Set<String> tags) throws ClientProtocolException, IOException, JSONException {
            String registrationId = retrieveRegistrationIdOrRequestNewOne(handle);

            JSONObject deviceInfo = new JSONObject();
            deviceInfo.put("Platform", "gcm");
            deviceInfo.put("Handle", handle);
            deviceInfo.put("Tags", new JSONArray(tags));

            int statusCode = upsertRegistration(registrationId, deviceInfo);

            if (statusCode == HttpStatus.SC_OK) {
                return;
            } else if (statusCode == HttpStatus.SC_GONE){
                settings.edit().remove(REGID_SETTING_NAME).commit();
                registrationId = retrieveRegistrationIdOrRequestNewOne(handle);
                statusCode = upsertRegistration(registrationId, deviceInfo);
                if (statusCode != HttpStatus.SC_OK) {
                    Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                    throw new RuntimeException("Error upserting registration");
                }
            } else {
                Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                throw new RuntimeException("Error upserting registration");
            }
        }

        private int upsertRegistration(String registrationId, JSONObject deviceInfo)
                throws UnsupportedEncodingException, IOException,
                ClientProtocolException {
            HttpPut request = new HttpPut(Backend_Endpoint+"/"+registrationId);
            request.setEntity(new StringEntity(deviceInfo.toString()));
            request.addHeader("Authorization", "Basic "+authorizationHeader);
            request.addHeader("Content-Type", "application/json");
            HttpResponse response = httpClient.execute(request);
            int statusCode = response.getStatusLine().getStatusCode();
            return statusCode;
        }

        private String retrieveRegistrationIdOrRequestNewOne(String handle) throws ClientProtocolException, IOException {
            if (settings.contains(REGID_SETTING_NAME))
                return settings.getString(REGID_SETTING_NAME, null);

            HttpUriRequest request = new HttpPost(Backend_Endpoint+"?handle="+handle);
            request.addHeader("Authorization", "Basic "+authorizationHeader);
            HttpResponse response = httpClient.execute(request);
            if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                Log.e("RegisterClient", "Error creating registrationId: " + response.getStatusLine().getStatusCode());
                throw new RuntimeException("Error creating Notification Hubs registrationId");
            }
            String registrationId = EntityUtils.toString(response.getEntity());
            registrationId = registrationId.substring(1, registrationId.length()-1);

            settings.edit().putString(REGID_SETTING_NAME, registrationId).commit();

            return registrationId;
        }
    }
    ```

    Este componente implementa as chamadas REST necessárias para contactar o back-end da aplicação, de modo a registar-se para receber notificações push. Também armazena localmente os *registrationIds* criados pelo Hub de Notificação conforme detalhado em [Registar-se a partir do back-end da aplicação](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend). Utiliza um token de autorização armazenado no armazenamento local quando clica no botão **Sinal no** botão.
4. Na sua classe, remova ou acrescente delimitadores de comentário ao campo privado para `NotificationHub` e adicione um campo para a classe `RegisterClient` e uma cadeia para o ponto final do back-end de ASP.NET. Certifique-se de que substitui o `<Enter Your Backend Endpoint>` pelo ponto final de back-end real obtido anteriormente. Por exemplo, `http://mybackend.azurewebsites.net`.

    ```java
    //private NotificationHub hub;
    private RegisterClient registerClient;
    private static final String BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
    ```

5. Na sua classe `MainActivity`, no método `onCreate`, remova ou acrescente delimitadores de comentário à inicialização do campo `hub` e à chamada para o método `registerWithNotificationHubs`. Em seguida, adicione código para inicializar uma instância da classe `RegisterClient`. O método deverá conter as seguintes linhas:

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mainActivity = this;
        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
        gcm = GoogleCloudMessaging.getInstance(this);

        //hub = new NotificationHub(HubName, HubListenConnectionString, this);
        //registerWithNotificationHubs();

        registerClient = new RegisterClient(this, BACKEND_ENDPOINT);

        setContentView(R.layout.activity_main);
    }
    ```
6. Na sua classe `MainActivity`, elimine ou acrescente delimitadores de comentário ao método `registerWithNotificationHubs` completo. Não será utilizado neste tutorial.
7. Adicione as seguintes instruções `import` ao ficheiro `MainActivity.java`:

    ```java
    import android.util.Base64;
    import android.view.View;
    import android.widget.EditText;

    import android.widget.Button;
    import android.widget.ToggleButton;
    import java.io.UnsupportedEncodingException;
    import android.content.Context;
    import java.util.HashSet;
    import android.widget.Toast;
    import org.apache.http.client.ClientProtocolException;
    import java.io.IOException;
    import org.apache.http.HttpStatus;

    import android.os.AsyncTask;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.DefaultHttpClient;

    import android.app.AlertDialog;
    import android.content.DialogInterface;
    ```
8. Substitua o código no método onStart pelo seguinte código:

    ```java
    super.onStart();
    Button sendPush = (Button) findViewById(R.id.sendbutton);
    sendPush.setEnabled(false);
    ```
9. Em seguida, adicione os seguintes métodos para manusear o evento de clique do botão **Sign in** e enviar notificações de pressão.

    ```java
    public void login(View view) throws UnsupportedEncodingException {
        this.registerClient.setAuthorizationHeader(getAuthorizationHeader());

        final Context context = this;
        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {
                    String regid = gcm.register(NotificationSettings.SenderId);
                    registerClient.register(regid, new HashSet<String>());
                } catch (Exception e) {
                    DialogNotify("MainActivity - Failed to register", e.getMessage());
                    return e;
                }
                return null;
            }

            protected void onPostExecute(Object result) {
                Button sendPush = (Button) findViewById(R.id.sendbutton);
                sendPush.setEnabled(true);
                Toast.makeText(context, "Logged in and registered.",
                        Toast.LENGTH_LONG).show();
            }
        }.execute(null, null, null);
    }

    private String getAuthorizationHeader() throws UnsupportedEncodingException {
        EditText username = (EditText) findViewById(R.id.usernameText);
        EditText password = (EditText) findViewById(R.id.passwordText);
        String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
        basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
        return basicAuthHeader;
    }

    /**
        * This method calls the ASP.NET WebAPI backend to send the notification message
        * to the platform notification service based on the pns parameter.
        *
        * @param pns     The platform notification service to send the notification message to. Must
        *                be one of the following ("wns", "gcm", "apns").
        * @param userTag The tag for the user who will receive the notification message. This string
        *                must not contain spaces or special characters.
        * @param message The notification message string. This string must include the double quotes
        *                to be used as JSON content.
        */
    public void sendPush(final String pns, final String userTag, final String message)
            throws ClientProtocolException, IOException {
        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {

                    String uri = BACKEND_ENDPOINT + "/api/notifications";
                    uri += "?pns=" + pns;
                    uri += "&to_tag=" + userTag;

                    HttpPost request = new HttpPost(uri);
                    request.addHeader("Authorization", "Basic "+ getAuthorizationHeader());
                    request.setEntity(new StringEntity(message));
                    request.addHeader("Content-Type", "application/json");

                    HttpResponse response = new DefaultHttpClient().execute(request);

                    if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                        DialogNotify("MainActivity - Error sending " + pns + " notification",
                            response.getStatusLine().toString());
                        throw new RuntimeException("Error sending notification");
                    }
                } catch (Exception e) {
                    DialogNotify("MainActivity - Failed to send " + pns + " notification ", e.getMessage());
                    return e;
                }

                return null;
            }
        }.execute(null, null, null);
    }
    ```

    O `login` botão **'Sinal'** gera um token de autenticação básica utilizando o nome de utilizador de entrada e a palavra-passe (representa qualquer símbolo que o seu esquema de autenticação utiliza), e depois utiliza `RegisterClient` para ligar para o backend para registo.

    O método `sendPush` chama o back-end para acionar uma notificação segura para o utilizador com base na etiqueta do mesmo. O serviço de notificação de plataforma visado por `sendPush` depende da cadeia `pns` transmitida.

10. Adicione o seguinte método `DialogNotify` à classe `MainActivity`.

    ```java
    protected void DialogNotify(String title, String message)
    {
        AlertDialog alertDialog = new AlertDialog.Builder(MainActivity.this).create();
        alertDialog.setTitle(title);
        alertDialog.setMessage(message);
        alertDialog.setButton(AlertDialog.BUTTON_NEUTRAL, "OK",
                new DialogInterface.OnClickListener() {
                    public void onClick(DialogInterface dialog, int which) {
                        dialog.dismiss();
                    }
                });
        alertDialog.show();
    }
    ```
11. Na sua classe `MainActivity`, atualize o método `sendNotificationButtonOnClick` para chamar o método `sendPush` com os serviços de notificação de plataforma selecionados do utilizador do seguinte modo.

    ```java
    /**
    * Send Notification button click handler. This method sends the push notification
    * message to each platform selected.
    *
    * @param v The view
    */
    public void sendNotificationButtonOnClick(View v)
            throws ClientProtocolException, IOException {

        String nhMessageTag = ((EditText) findViewById(R.id.editTextNotificationMessageTag))
                .getText().toString();
        String nhMessage = ((EditText) findViewById(R.id.editTextNotificationMessage))
                .getText().toString();

        // JSON String
        nhMessage = "\"" + nhMessage + "\"";

        if (((ToggleButton)findViewById(R.id.toggleButtonWNS)).isChecked())
        {
            sendPush("wns", nhMessageTag, nhMessage);
        }
        if (((ToggleButton)findViewById(R.id.toggleButtonGCM)).isChecked())
        {
            sendPush("gcm", nhMessageTag, nhMessage);
        }
        if (((ToggleButton)findViewById(R.id.toggleButtonAPNS)).isChecked())
        {
            sendPush("apns", nhMessageTag, nhMessage);
        }
    }
    ```
12. No `build.gradle` ficheiro, adicione a seguinte linha à `android` secção após a `buildTypes` secção.

    ```java
    useLibrary 'org.apache.http.legacy'
    ```
13. Crie o projeto.

## <a name="test-the-app"></a>Testar a aplicação

1. Execute a aplicação num dispositivo ou num emulador com o Android Studio.
2. Na aplicação Android, introduza um nome de utilizador e uma palavra-passe. Ambos têm de ter o mesmo valor de cadeia e nenhum deles pode conter espaços ou carateres especiais.
3. Na aplicação Android, clique **em Iniciar sção.** Aguarde por uma mensagem de alerta com a indicação **Logged in and registered**. Esta ativa o botão **Send Notification**.

    ![Screenshot de uma aplicação. Uma mensagem de torrada confirmando que o utilizador está inscrito e registado está visível e o botão de Notificação de Envio é ligado.][A2]
4. Clique nos botões de alternar para ativar todas as plataformas em que executou a aplicação e registou um utilizador.
5. Introduza o nome do utilizador que recebe a mensagem de notificação. Esse utilizador tem de estar registado para receber notificações nos dispositivos de destino.
6. Introduza uma mensagem para o utilizador receber como uma mensagem de notificação push.
7. Clique em **Send Notification**.  Cada dispositivo que tiver um registo com a etiqueta de nome de utilizador correspondente receberá a notificação push.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a enviar notificações push para utilizadores específicos que têm etiquetas associadas aos respetivos registos. Para saber como enviar notificações push com base na localização, avance para o seguinte tutorial:

> [!div class="nextstepaction"]
>[Enviar notificações push baseadas na localização](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[A1]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users.png
[A2]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users-enter-password.png
