---
title: Enviando notificações por push seguras com os hubs de notificação do Azure
description: Saiba como enviar notificações por push seguras para um aplicativo Android do Azure. Exemplos de código escritos em Java C#e.
documentationcenter: android
keywords: notificação por push, notificações por push, mensagens por push, notificações por push do Android
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: daf3de1c-f6a9-43c4-8165-a76bfaa70893
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 419a9f9b5ce698c7516edd55856cbea9891ba029
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212174"
---
# <a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Enviando notificações por push seguras com os hubs de notificação do Azure

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Descrição geral

> [!IMPORTANT]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

O suporte à notificação por push no Microsoft Azure permite que você acesse uma infraestrutura de mensagens de envio por push fácil de usar, multiplataforma e escalável, que simplifica bastante a implementação de notificações por push para aplicativos de consumidor e empresariais para plataformas móveis.

Devido a restrições normativas ou de segurança, às vezes um aplicativo pode querer incluir algo na notificação que não pode ser transmitido por meio da infraestrutura de notificação por push padrão. Este tutorial descreve como obter a mesma experiência ao enviar informações confidenciais por meio de uma conexão segura e autenticada entre o dispositivo Android cliente e o back-end do aplicativo.

Em um alto nível, o fluxo é o seguinte:

1. O back-end do aplicativo:
   * Armazena a carga segura no banco de dados back-end.
   * Envia a ID dessa notificação para o dispositivo Android (nenhuma informação segura é enviada).
2. O aplicativo no dispositivo, ao receber a notificação:
   * O dispositivo Android entra em contato com o back-end solicitando a carga segura.
   * O aplicativo pode mostrar a carga como uma notificação no dispositivo.

É importante observar que no fluxo anterior (e, neste tutorial), supõe-se que o dispositivo armazene um token de autenticação no armazenamento local, depois que o usuário fizer logon. Essa abordagem garante uma experiência simples, pois o dispositivo pode recuperar a carga segura da notificação usando esse token. Se o seu aplicativo não armazenar tokens de autenticação no dispositivo ou se esses tokens puderem expirar, o aplicativo do dispositivo, após receber a notificação por push, deverá exibir uma notificação genérica solicitando que o usuário inicie o aplicativo. Em seguida, o aplicativo autentica o usuário e mostra a carga de notificação.

Este tutorial mostra como enviar notificações por push seguras. Ele se baseia no tutorial [notificar usuários](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) , portanto, você deve concluir as etapas nesse tutorial primeiro, se ainda não tiver feito isso.

> [!NOTE]
> Este tutorial pressupõe que você criou e configurou seu hub de notificação conforme descrito em [introdução com os hubs de notificação (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Modificar o projeto do Android

Agora que você modificou o back-end do aplicativo para enviar apenas a *ID* de uma notificação por push, você precisa alterar seu aplicativo Android para lidar com essa notificação e retornar o back-end para recuperar a mensagem segura a ser exibida.
Para atingir essa meta, você deve garantir que seu aplicativo Android saiba como se autenticar com seu back-end quando receber as notificações por push.

Agora, modifique o fluxo de *logon* para salvar o valor do cabeçalho de autenticação nas preferências compartilhadas do seu aplicativo. Mecanismos análogos podem ser usados para armazenar qualquer token de autenticação (por exemplo, tokens OAuth) que o aplicativo precisa usar sem exigir credenciais de usuário.

1. Em seu projeto de aplicativo do Android, adicione as seguintes constantes na parte superior `MainActivity` da classe:

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
3. Adicione as seguintes `import` instruções na parte superior `MainActivity` do arquivo:

    ```java
    import android.content.SharedPreferences;
    ```

Agora, altere o manipulador que é chamado quando a notificação é recebida.

1. Na classe, altere o `OnReceive()` método para conter: `MyHandler`

    ```java
    public void onReceive(Context context, Bundle bundle) {
        ctx = context;
        String secureMessageId = bundle.getString("secureId");
        retrieveNotification(secureMessageId);
    }
    ```
2. Em seguida, `retrieveNotification()` adicione o método, substituindo o espaço reservado `{back-end endpoint}` pelo ponto de extremidade de back-end obtido durante a implantação do back-end:

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

Esse método chama o back-end do aplicativo para recuperar o conteúdo de notificação usando as credenciais armazenadas nas preferências compartilhadas e a exibe como uma notificação normal. A notificação procura o usuário do aplicativo exatamente como qualquer outra notificação por push.

É preferível manipular os casos de falta de propriedade de cabeçalho de autenticação ou rejeição pelo back-end. A manipulação específica desses casos depende principalmente da experiência do usuário de destino. Uma opção é exibir uma notificação com um prompt genérico para que o usuário se autentique para recuperar a notificação real.

## <a name="run-the-application"></a>Executar o aplicativo

Para executar o aplicativo, execute as seguintes ações:

1. Verifique se o **AppBackend** está implantado no Azure. Se estiver usando o Visual Studio, execute o aplicativo de API Web **AppBackend** . Uma página da Web do ASP.NET é exibida.
2. No Eclipse, execute o aplicativo em um dispositivo Android físico ou no emulador.
3. Na interface do usuário do aplicativo Android, insira um nome de usuário e senha. Elas podem ser qualquer cadeia de caracteres, mas devem ter o mesmo valor.
4. Na interface do usuário do aplicativo Android, clique em **fazer logon**. Em seguida, clique em **enviar envio por push**.
