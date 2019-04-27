---
title: Enviar notificações Push seguro com os Hubs de notificação do Azure
description: Saiba como enviar notificações push segura a uma aplicação Android do Azure. Exemplos de códigos escritos em Java e c#.
documentationcenter: android
keywords: notificação push, notificações push, push mensagens, notificações android push
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: daf3de1c-f6a9-43c4-8165-a76bfaa70893
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 27536b0a3d7e0858a5660b4c7b33cb6679b5fbf1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60874563"
---
# <a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Enviar notificações Push seguro com os Hubs de notificação do Azure

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Descrição geral

> [!IMPORTANT]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Suporte de notificação push no Microsoft Azure permite-lhe aceder uma fácil de usar, várias plataformas, infraestrutura de mensagens de push de escalamento horizontal, que simplifica bastante a implementação de notificações push para aplicações de consumidor e empresariais para plataformas móveis.

Devido à regulamentação ou restrições de segurança, por vezes, um aplicativo pode querer incluir algo na notificação que não pode ser transmitida através da infraestrutura de notificação push padrão. Este tutorial descreve como atingir a mesma experiência ao enviar informações confidenciais por meio de uma ligação autenticada e segura entre o dispositivo Android do cliente e o back-end de aplicação.

Num alto nível, o fluxo é o seguinte:

1. O back-end da aplicação:
   * Payload de arquivos seguro na base de dados de back-end.
   * Envia o ID de notificação para o dispositivo Android (não existem informações de seguras são enviadas).
2. A aplicação no dispositivo, ao receber a notificação:
   * O dispositivo Android contacta o back-end solicitando o payload de seguro.
   * A aplicação pode mostrar o payload como uma notificação no dispositivo.

É importante observar que no fluxo anterior (e neste tutorial), presume-se que o dispositivo armazena um token de autenticação no armazenamento local, depois do usuário fizer logon. Essa abordagem garante uma experiência totalmente integrada, como o dispositivo pode obter o payload de seguros a notificação com este token. Se seu aplicativo não armazena os tokens de autenticação do dispositivo, ou se estes tokens podem ter expirados, a aplicação de dispositivo, ao receber a notificação push deve apresentar uma notificação genérica pedir ao utilizador para iniciar a aplicação. A aplicação, em seguida, autentica o utilizador e mostra o payload de notificação.

Este tutorial mostra como enviar notificações push seguro. Ele se baseia no [notificar utilizadores](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) tutorial, pelo que deverá concluir os passos esse tutorial primeiro se ainda não o fez.

> [!NOTE]
> Este tutorial pressupõe que já criou e configurou o notification hub conforme descrito em [introdução aos Hubs de notificação (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Modificar o projeto Android

Agora que modificou o seu back-end da aplicação para enviar apenas o *ID* de uma notificação push, tem de alterar a sua aplicação Android para lidar com que a notificação e o back-end para obter a mensagem segura a apresentar do retorno de chamada.
Para atingir esse objetivo, tem de se certificar de que a aplicação Android sabe como autenticar-se com o back-end ao receber as notificações push.

Agora, modifique o *início de sessão* fluxo para guardar o valor de cabeçalho de autenticação nas preferências partilhadas da sua aplicação. Mecanismos de análogos podem ser utilizados para armazenar qualquer token de autenticação (por exemplo, tokens de OAuth) que a aplicação tem de utilizar sem a necessidade de credenciais de utilizador.

1. No seu projeto de aplicação Android, adicione as seguintes constantes na parte superior do `MainActivity` classe:

    ```java
    public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
    public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
    ```
2. Ainda na `MainActivity` classe, atualize o `getAuthorizationHeader()` método para conter o seguinte código:

    ```java
    private String getAuthorizationHeader() throws UnsupportedEncodingException {
        EditText username = (EditText) findViewById(R.id.usernameText);
        EditText password = (EditText) findViewById(R.id.passwordText);
        String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
        basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

        SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

        return basicAuthHeader;
    }
    ```
3. Adicione as seguintes `import` declarações na parte superior do `MainActivity` ficheiro:

    ```java
    import android.content.SharedPreferences;
    ```

Agora, altere o manipulador de que é chamado quando a notificação é recebida.

1. Na `MyHandler` classe alteração a `OnReceive()` método para conter:

    ```java
    public void onReceive(Context context, Bundle bundle) {
        ctx = context;
        String secureMessageId = bundle.getString("secureId");
        retrieveNotification(secureMessageId);
    }
    ```
2. Em seguida, adicione a `retrieveNotification()` método, substituindo o marcador de posição `{back-end endpoint}` com o ponto final de back-end obtido durante a implementação de back-end:

    ```java
    private void retrieveNotification(final String secureMessageId) {
        SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {
                    HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                    request.addHeader("Authorization", "Basic "+authorizationHeader);
                    HttpResponse response = new DefaultHttpClient().execute(request);
                    if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                        Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                        throw new RuntimeException("Error retrieving secure notification");
                    }
                    String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                    JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                    sendNotification(secureNotification.getString("Payload"));
                } catch (Exception e) {
                    Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                    return e;
                }
                return null;
            }
        }.execute(null, null, null);
    }
    ```

Esse método chama o back-end de aplicação para obter o conteúdo de notificações com as credenciais armazenadas nas preferências partilhadas e apresenta-a como uma notificação normal. A notificação de procura para o utilizador de aplicação exatamente como qualquer outra notificação push.

É preferível para tratar de casos de propriedade de cabeçalho de autenticação em falta ou rejeição pelo back-end. O processamento específico nestes casos depende principalmente de sua experiência de utilizador de destino. É uma opção apresentar uma notificação com uma linha de comandos genérica para o utilizador autenticar para recuperar a notificação real.

## <a name="run-the-application"></a>Executar a aplicação

Para executar a aplicação, execute as seguintes ações:

1. Certifique-se **AppBackend** é implementado no Azure. Se utilizar o Visual Studio, execute o **AppBackend** aplicação Web API. É apresentada uma página da web ASP.NET.
2. No Eclipse, execute a aplicação num dispositivo Android físico ou o emulador.
3. Na aplicação do Android da interface do Usuário, introduza um nome de utilizador e palavra-passe. Estes podem ser qualquer cadeia de caracteres, mas têm de ser o mesmo valor.
4. Na aplicação do Android da interface do Usuário, clique em **iniciar sessão**. Em seguida, clique em **enviar push**.
