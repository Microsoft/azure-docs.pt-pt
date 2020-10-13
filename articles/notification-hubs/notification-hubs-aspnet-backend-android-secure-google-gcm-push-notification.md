---
title: Enviar notificações push seguras com os hubs de notificação do Azure
description: Saiba como enviar notificações push seguras para uma aplicação Android a partir do Azure. Amostras de código escritas em Java e C#.
documentationcenter: android
keywords: notificações push,notificações push,mensagens push,notificações push android
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 08/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: f2d5d618fabbe7400ce825f984ace1622a524f05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88004032"
---
# <a name="send-secure-push-notifications-with-azure-notification-hubs"></a>Enviar notificações push seguras com os hubs de notificação do Azure

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Descrição geral

> [!IMPORTANT]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

O suporte de notificação push no Microsoft Azure permite-lhe aceder a uma infraestrutura de mensagens push de fácil utilização, multi-plataforma, dimensionada, o que simplifica consideravelmente a implementação de notificações push para aplicações de consumidores e empresas para plataformas móveis.

Devido a restrições regulamentares ou de segurança, por vezes, um pedido pode querer incluir algo na notificação que não pode ser transmitido através da infraestrutura padrão de notificação push. Este tutorial descreve como obter a mesma experiência enviando informações confidenciais através de uma ligação segura e autenticada entre o dispositivo Android cliente e o backend do app.

A um nível elevado, o fluxo é o seguinte:

- O backend da aplicação:
  * As lojas protegem a carga útil na base de dados back-end.
  * Envia o ID desta notificação para o dispositivo Android (não são enviadas informações seguras).
- A aplicação no dispositivo, ao receber a notificação:
  * O dispositivo Android contacta o back-end solicitando a carga útil segura.
  * A aplicação pode mostrar a carga útil como uma notificação no dispositivo.

É importante notar que no fluxo anterior (e neste tutorial), é assumido que o dispositivo armazena um token de autenticação no armazenamento local, após o registo do utilizador. Esta abordagem garante uma experiência perfeita, uma vez que o dispositivo pode recuperar a carga útil segura da notificação utilizando este token. Se a sua aplicação não armazenar fichas de autenticação no dispositivo, ou se estas fichas puderem ser expiradas, a aplicação do dispositivo, ao receber a notificação push, deverá apresentar uma notificação genérica que o levou o utilizador a lançar a aplicação. A aplicação autentica então o utilizador e mostra a carga útil da notificação.

Este tutorial mostra como enviar notificações push seguras. Baseia-se no tutorial de [utilizadores da Notificação,](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) pelo que deve completar primeiro os passos nesse tutorial.

> [!NOTE]
> Este tutorial pressupõe que criou e configura o seu centro de notificações como descrito em [Get start with Notification Hubs (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Modifique o projeto Android

Agora que modificou a sua aplicação para enviar apenas o ID de uma notificação push, tem de alterar a sua aplicação Android para lidar com essa notificação e voltar a ligar para o seu back-end para recuperar a mensagem segura a ser exibida.
Para atingir este objetivo, tem de garantir que a sua aplicação Android sabe autenticar-se com o seu back-end quando recebe as notificações push.

Agora, modifique o fluxo de login de forma a guardar o valor do cabeçalho de autenticação nas preferências partilhadas da sua aplicação. Mecanismos análogos podem ser utilizados para armazenar qualquer token de autenticação (por exemplo, tokens OAuth) que a aplicação tem de usar sem exigir credenciais do utilizador.

1. No seu projeto de aplicativo Android, adicione as seguintes constantes no topo da `MainActivity` classe:

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

3. Adicione as `import` seguintes declarações no topo do `MainActivity` ficheiro:

    ```java
    import android.content.SharedPreferences;
    ```

Agora, mude o manipulador que é chamado quando a notificação é recebida.

1. Na `MyHandler` classe altere o `OnReceive()` método para conter:

    ```java
    public void onReceive(Context context, Bundle bundle) {
        ctx = context;
        String secureMessageId = bundle.getString("secureId");
        retrieveNotification(secureMessageId);
    }
    ```

2. Em seguida, adicione o `retrieveNotification()` método, substituindo o espaço reservado `{back-end endpoint}` pelo ponto final de fundo obtido enquanto implanta o seu back-end:

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

Este método chama a sua aplicação de back-end para recuperar o conteúdo da notificação usando as credenciais armazenadas nas preferências partilhadas e exibe-o como uma notificação normal. A notificação olha para o utilizador da aplicação exatamente como qualquer outra notificação push.

É preferível lidar com os casos de falta de autenticação ou rejeição pelo back-end. O tratamento específico destes casos depende principalmente da experiência do utilizador-alvo. Uma opção é apresentar uma notificação com uma solicitação genérica para que o utilizador autentica para recuperar a notificação real.

## <a name="run-the-application"></a>Executar a Aplicação

Para executar a aplicação, execute as seguintes ações:

1. Certifique-se de que **o AppBackend** está implantado em Azure. Se utilizar o Visual Studio, executar a aplicação **AppBackend** Web API. É apresentada uma página web ASP.NET.
2. Em Eclipse, execute a aplicação num dispositivo Android físico ou no emulador.
3. Na aplicação Android UI, insira um nome de utilizador e senha. Estes podem ser qualquer corda, mas devem ter o mesmo valor.
4. Na aplicação Android UI, clique **em Iniciarões de Sessão**. Em seguida, clique **em Enviar o empurrão**.
