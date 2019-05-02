---
title: Enviar notificações push para aplicações Kindle com Hubs de Notificação do Azure | Microsoft Docs
description: Neste tutorial, irá aprender a utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação Kindle.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 346fc8e5-294b-4e4f-9f27-7a82d9626e93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-kindle
ms.devlang: Java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/29/2019
ms.author: jowargo
ms.openlocfilehash: edd0e12460e07cfd2990cc43a9056ed06b84fb1d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927016"
---
# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Introdução aos Notification Hubs para Aplicações Kindle

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Este tutorial mostra como utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação Kindle. Vai criar uma aplicação Kindle que receba notificações push através do Amazon Device Messaging (ADM).

Neste tutorial, pode criar/atualizar código para efetuar as seguintes tarefas:

> [!div class="checklist"]
> * Adicionar uma nova aplicação ao portal de programador
> * Criar uma chave de API
> * Criar e configurar um hub de notificação
> * Configurar a aplicação
> * Criar o processador de mensagens ADM
> * Adicionar a chave de API à aplicação
> * Executar a aplicação
> * Enviar uma notificação de teste

## <a name="prerequisites"></a>Pré-requisitos

- [Android Studio](https://developer.android.com/studio/?gclid=CjwKCAjwwZrmBRA7EiwA4iMzBPZ9YYmR0pbb5LtjnWhWCxe8PWrmjmeaR6ad5ksCr_j2mmkVj_-o6hoCAqwQAvD_BwE).
- Siga os passos em [Configurar o Ambiente de Desenvolvimento](https://developer.amazon.com/docs/fire-tablets/ft-set-up-your-development-environment.html) para configurar o ambiente de desenvolvimento para o Kindle.

## <a name="add-a-new-app-to-the-developer-portal"></a>Adicionar uma nova aplicação ao portal de programador

1. Inicie sessão para o [Portal de programador da Amazon](https://developer.amazon.com/apps-and-games/console/apps/list.html).
2. Selecione **Adicionar nova aplicação**e, em seguida, selecione **Android**.  

    ![Adicionar novo botão de aplicação](./media/notification-hubs-kindle-get-started/add-new-app-button.png)
1. Sobre o **nova submissão do aplicativo** página, siga estes passos para obter o **chave da aplicação**:
    1. Introduza um nome para o **título da aplicação**.
    2. Selecione qualquer **categoria** (por exemplo: education)
    4. Introduza um endereço de e-mail para o **endereço de e-mail de suporte ao cliente** campo. 
    5. Selecione **Guardar**.

        ![Nova página de submissão de aplicação](./media/notification-hubs-kindle-get-started/new-app-submission-page.png) 
2.  Na parte superior, mude para o **Mobile Ads** separador e siga os passos abaixo: 
    1. Especifique se a sua aplicação é direcionada basicamente a crianças menos de 13 anos. Neste tutorial, selecione **não**.
    2. Selecione **submeter**. 

        ![Página de anúncios Mobile](./media/notification-hubs-kindle-get-started/mobile-ads-page.png)
    3. Cópia a **chave da aplicação** partir a **Mobile anúncios** página. 

        ![Chave da aplicação](./media/notification-hubs-kindle-get-started/application-key.png)
3.  Selecione **aplicativos e serviços** menu na parte superior e selecione a aplicação na lista. 

    ![Selecione a sua aplicação da lista](./media/notification-hubs-kindle-get-started/all-apps-select.png)
4. Mude para o **Device Messaging** separador e, siga estes passos: 
    1. Selecione **criar um novo perfil de segurança**.
    2. Introduza um **nome** para o seu perfil de segurança. 
    3. Introduza **Descrição** para o seu perfil de segurança. 
    4. Selecione **Guardar**. 
    5. Selecione **perfil de segurança de modo de exibição** na página de resultados. 
5. Agora, sobre o **perfil de segurança** página, efetue os seguintes passos: 
    1. Mude para o **as definições da Web** separador e copie a **ID de cliente** e **segredo do cliente** valor para utilização posterior. 

        ![Obter o ID de cliente e segredo](./media/notification-hubs-kindle-get-started/client-id-secret.png) 
    2. Mude para o **definições de Android/Kindle** de páginas e mantenha a página aberta. Vai introduzir estes valores na secção seguinte. 

## <a name="create-an-api-key"></a>Criar uma chave de API
1. Abra uma linha de comandos com privilégios de administrador.
2. Navegue para a pasta Android SDK.
3. Introduza o seguinte comando:

    ```shell
    keytool -list -v -alias androiddebugkey -keystore ./debug.keystore
    ```
4. Para a palavra-passe **keystore**, escreva **android**.
5. Copiar o **MD5** e **SHA256** impressões digitais. 
6. Volta no portal do programador, o **definições de Android/Kindle** separador, efetue os seguintes passos: 
    1. Introduza um **nome da chave de API**. 
    2. Introduza o **nome do pacote** para a sua aplicação (por exemplo, **com.fabrikam.mykindleapp**) e o **MD5** valor.
        
        >[!IMPORTANT]
        > Quando cria uma aplicação no Android Studio, utilize o mesmo nome de pacote especificado aqui. 
    1. Colar o **MD5 assinatura** que copiou anteriormente. 
    2. Colar o **SHA256 assinatura** que copiou anteriormente.  
    3. Selecione **gerar a nova chave**.

        ![Definições de Android/Kindle - gerar chave](./media/notification-hubs-kindle-get-started/android-kindle-settings.png)
    4. Agora, selecione **mostrar** na lista para ver a chave de API. 

        ![Definições de Android/Kindle - Mostrar chave de API](./media/notification-hubs-kindle-get-started/show-api-key-button.png) 
    5. Na **detalhes da chave de API** janela, copie a chave de API e guardá-lo em algum lugar. Em seguida, selecione **X** no canto superior direito para fechar a janela. 


## <a name="create-and-configure-a-notification-hub"></a>Criar e configurar um hub de notificação

1. Siga os passos no [criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md) artigo para criar um hub de notificação. 
2. Selecione **Amazon (ADM)** sob **definições** menu.
3. Colar o **ID de cliente** e **segredo do cliente** que guardou anteriormente. 
4. Selecione **Guardar** na barra de ferramentas. 

    ![Configurar as definições do ADM para um hub de notificação](./media/notification-hubs-kindle-get-started/configure-notification-hub.png)
5. Selecione **políticas de acesso** no menu à esquerda e selecione o **cópia** botão para a cadeia de ligação para o **DefaultListenSharedAccessSignature** política. Guarde-a algures. Irá utilizá-lo mais tarde no código-fonte. 

    ![Hub de notificação - cadeia de ligação de escuta](./media/notification-hubs-kindle-get-started/event-hub-listen-connection-string.png)    

## <a name="set-up-your-application"></a>Configurar a aplicação

1. Execute o Android Studio. 
2. Selecione **arquivo**, aponte para **New**e, em seguida, selecione **novo projeto**. 
3. Na **escolha o seu projeto** janela, no **telemóveis e tablets** separador, selecione **atividade vazia**e selecione **seguinte**. 
4. Na **configurar o seu projeto** janela, siga os passos abaixo:
    1. Introduza um **nome para a sua aplicação**. Pode querer fazer sua correspondência com o nome da aplicação que criou no Portal de programador da Amazon. 
    2. Introduza um **nome do pacote**. 
        
        >[!IMPORTANT]
        >O nome do pacote tem de corresponder ao nome do pacote que especificou no Portal de programador da Amazon.
    3. Rever e atualizar os restantes valores conforme adequado. 
    4. Selecione **Concluir**. 

        ![Configurar o projeto Android](./media/notification-hubs-kindle-get-started/new-android-studio-project.png)
5. Baixe [SDK de programador da Amazon para Android](https://developer.amazon.com/sdk-download) biblioteca no seu disco rígido. Extraia o ficheiro zip do SDK.
6. No Android Studio, alterar a estrutura de pastas a partir **Android** ao **projeto** se já não está definido como **projeto**. 

    ![Android Studio - mudar para a estrutura do projeto](./media/notification-hubs-kindle-get-started/android-studio-project-view.png)
7. Expanda **app** para ver a **bibliotecas** pasta na vista de árvore.     
8. Numa janela do Explorador de ficheiros, navegue para a pasta onde transferiu os SDKs de Android da Amazon.
9. Prima **CTRL** e arraste e largue a **amazon-dispositivo-mensagens-1.0.1.jar** ficheiro para o **lib** nó na vista de árvore. 

    ![Android Studio - adicionar dispositivo Amazon JAR de mensagens](./media/notification-hubs-kindle-get-started/drag-drop-amazon-device-messaging-jar.png)
9. Na **cópia** janela, selecione **OK**. Se vir a **mover** janela, em vez de **cópia** janela, feche-a e tente a operação de arrastar e soltar com **CTRL** botão pressionado. 

    ![Android Studio - cópia JAR](./media/notification-hubs-kindle-get-started/copy-jar-window.png)
10. Adicione a seguinte instrução para o **gradle da aplicação** de ficheiros a **dependências** secção: `implementation files('libs/amazon-device-messaging-1.0.1.jar')`. 

    ![Android Studio - adicionar ADM para gradle da aplicação](./media/notification-hubs-kindle-get-started/adm-build-gradle.png)
11. Na `Build.Gradle` de ficheiros para o **aplicação**, adicione as seguintes linhas no **dependências** secção: 

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```
12. Adicione o repositório seguinte **após** a **dependências** secção:

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```
13. Do Editor para o **gradle** de ficheiros para o **app**, selecione **sincronizar agora** na barra de ferramentas. 

    ![Android Studio - gradle de sincronização da aplicação](./media/notification-hubs-kindle-get-started/gradle-sync-now.png)
14. Mude novamente para a estrutura Android na vista de árvore.  Adicione o espaço de nomes Amazon no elemento raiz do manifesto:

    ```xml
    xmlns:amazon="http://schemas.amazon.com/apk/res/android"
    ```
   
    ![Espaço de nomes Amazon no manifesto](./media/notification-hubs-kindle-get-started/amazon-namespace-manifest.png)
2. Adicione permissões como o primeiro elemento sob o elemento manifesto. Substitua **[nome do seu pacote]** com o pacote que utilizou para criar a sua aplicação.

    ```xml
    <permission
        android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
        android:protectionLevel="signature" />

    <uses-permission android:name="android.permission.INTERNET"/>

    <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

    <!-- This permission allows your app access to receive push notifications
    from ADM. -->
    <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

    <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    ```
3. Insira o elemento seguinte como o primeiro subordinado do elemento aplicação. Substitua **[nome do seu pacote]** com o nome do pacote com a qual criou a aplicação. Irá criar a classe MyADMMessageHandler no próximo passo. 

    ```xml
        <amazon:enable-feature
            android:name="com.amazon.device.messaging"
            android:required="true"/>
        <service
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler"
            android:exported="false" />
        <receiver
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler$Receiver"
            android:permission="com.amazon.device.messaging.permission.SEND" >

            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
                <action android:name="com.amazon.device.messaging.intent.RECEIVE" />
                <!-- Replace the name in the category tag with your app's package name. -->
                <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>
    ```

## <a name="create-your-adm-message-handler"></a>Criar o processador de mensagens ADM

1. Adicionar uma nova classe para o `com.fabrikam.mykindleapp` pacote no projeto herdeira de `com.amazon.device.messaging.ADMMessageHandlerBase` e designe- `MyADMMessageHandler`, conforme mostrado na imagem seguinte:

    ![Criar a classe MyADMMessageHandler](./media/notification-hubs-kindle-get-started/create-adm-message-handler.png)

2. Adicione as seguintes `import` instruções para o `MyADMMessageHandler` classe:

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.support.v4.app.NotificationCompat;
    import android.util.Log;
    import com.amazon.device.messaging.ADMMessageReceiver;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    ```

3. Adicione o seguinte código na classe que criou. Lembre-se a `[HUB NAME]` e `[LISTEN CONNECTION STRING]` com o nome da sua cadeia de ligação de hub e escutar notificação: 

    ```java
    public static final int NOTIFICATION_ID = 1;
    private NotificationManager mNotificationManager;
    NotificationCompat.Builder builder;
    private static NotificationHub hub;

    public static NotificationHub getNotificationHub(Context context) {
        Log.v("com.wa.hellokindlefire", "getNotificationHub");
        if (hub == null) {
            hub = new NotificationHub("[HUB NAME]", "[HUB NAMESPACE CONNECTION STRING]", context);
        }
        return hub;
    }

    public MyADMMessageHandler() {
        super("MyADMMessageHandler");
    }

    @Override
    protected void onMessage(Intent intent) {
        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);
    }

    @Override
    protected void onRegistrationError(String s) {

    }

    @Override
    protected void onRegistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).register(s);
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
        }
    }

    @Override
    protected void onUnregistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).unregister();
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
        }
    }

    public static class Receiver extends ADMMessageReceiver
    {
        public Receiver()
        {
            super(MyADMMessageHandler.class);
        }
    }

    private void sendNotification(String msg) {
        Context ctx = getApplicationContext();

        mNotificationManager = (NotificationManager)
                ctx.getSystemService(Context.NOTIFICATION_SERVICE);

        PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);


        NotificationCompat.Builder mBuilder =
                new NotificationCompat.Builder(ctx)
                        .setSmallIcon(R.mipmap.ic_launcher)
                        .setContentTitle("Notification Hub Demo")
                        .setStyle(new NotificationCompat.BigTextStyle()
                                .bigText(msg))
                        .setContentText(msg);

        mBuilder.setContentIntent(contentIntent);
        mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
    }
    ```

## <a name="add-your-api-key-to-your-app"></a>Adicionar a chave de API à aplicação
1. Siga estes passos para adicionar uma pasta de recursos ao projeto. 
    1. Mude para o **projeto** vista. 
    2. Com o botão direito **aplicação**.
    3. Selecione **Novo**.
    4. Selecione **pasta**. 
    5. Em seguida, selecione **pasta ativos**. 

        ![Adicionar o menu de pasta de ativos](./media/notification-hubs-kindle-get-started/add-assets-folder-menu.png)    
    6. Sobre o **configurar o componente** página, efetue os seguintes passos:
        1. Selecione **alterar a localização da pasta**
        2. Confirme se a pasta é definida como: `src/main/assets`.
        3. Selecione **Concluir**. 
        
            ![Configurar a pasta de ativos](./media/notification-hubs-kindle-get-started/configure-asset-folder.png)
2. Adicionar um arquivo chamado **api_key** para o **ativos** pasta. Na vista de árvore, expanda **app**, expanda **src**, expanda **principal**e com o botão direito **ativos**, aponte para **New**e, em seguida, selecione **ficheiro**. Introduza **api_key** para o nome de ficheiro. 3. 
5. Copie a chave de API que gerou no portal do programador do Amazon para o ficheiro de api_key. 
6. Crie o projeto. 

## <a name="run-the-app"></a>Executar a aplicação
1. No dispositivo Kindle, percorra a partir da parte superior e clique em **configurações**e, em seguida, clique em **minha conta** e registe-se com uma conta válida da Amazon.
2. Execute a aplicação num dispositivo Kindle a partir do Android Studio. 

> [!NOTE]
> Se ocorrer um problema, verifique a hora do emulador (ou dispositivo). O valor da hora tem de ser exato. Para alterar a hora do emulador do Kindle, pode executar o seguinte comando no diretório de ferramentas da plataforma Android SDK:

```shell
adb shell  date -s "yyyymmdd.hhmmss"
```

## <a name="send-a-notification-message"></a>Enviar uma mensagem de notificação

Para enviar uma mensagem utilizando o .NET:

```csharp
static void Main(string[] args)
{
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

    hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
}
```

Para o código de exemplo, consulte [neste exemplo no GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/blob/master/Samples/SendPushSample/SendPushSample/Program.cs).

![][7]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, enviou notificações de difusão para todos os dispositivos Kindle registados no back-end. Para saber como enviar notificações push para dispositivos específicos do Kindle, avance para o tutorial seguinte:  O tutorial seguinte mostra como enviar notificações push para dispositivos Android específicos, mas pode usar a mesma lógica para enviar notificações para dispositivos específicos do Kindle.

> [!div class="nextstepaction"]
>[Enviar notificações push para dispositivos específicos](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- URLs. -->
[Amazon developer portal]: https://developer.amazon.com/home.html
[download the SDK]: https://developer.amazon.com/public/resources/development-tools/sdk
[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
