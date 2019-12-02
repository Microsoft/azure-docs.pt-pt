---
title: Adicionar notificações por push a um aplicativo Apache Cordova
description: Saiba como usar aplicativos móveis para enviar notificações por push para seu aplicativo Apache Cordova.
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 6ec214c0f1a4f8333bf88790de8d2936fce39002
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668917"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Adicionar notificações por push ao seu aplicativo Apache Cordova

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> O Visual Studio App Center suporta serviços de ponto a ponto e integrados, fundamentais para o desenvolvimento de aplicações móveis. Os programadores podem utilizar os serviços de **Compilação**, **Teste** e **Distribuição** para configurar o pipeline de Integração e Entrega Contínuas. Após a implementação da aplicação, os programadores podem monitorizar o estado e a utilização da aplicação através dos serviços de **Análise** e de **Diagnóstico** e interagir com os utilizadores através do serviço **Push**. Os programadores também podem tirar partido da **Autenticação** para autenticar os utilizadores e do serviço de **Dados** para manter e sincronizar os dados da aplicação na cloud.
>
> Se quiser integrar serviços cloud na sua aplicação móvel, inscreva-se no [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão geral

Neste tutorial, você adicionará notificações por push ao projeto de [Apache Cordova início rápido][5] para que uma notificação por push seja enviada ao dispositivo sempre que um registro for inserido.

Se você não usar o projeto baixado do servidor de início rápido, será necessário o pacote de extensão de notificação por push. Para obter mais informações, consulte [trabalhar com o SDK do servidor de back-end do .net para aplicativos móveis][1].

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que você tenha um aplicativo Apache Cordova que foi desenvolvido com o Visual Studio 2015. Este dispositivo deve ser executado no Google Android Emulator, um dispositivo Android, um dispositivo Windows ou um dispositivo iOS.

Para concluir este tutorial, precisa de:

* Um PC com o [Visual Studio Community 2015][2] ou posterior
* [Visual Studio Tools para Apache Cordova][4]
* Uma [conta ativa do Azure][3]
* Um projeto de [início rápido de Apache Cordova][5] concluído
* Android Uma [conta do Google][6] com um endereço de email verificado
* Cisco Uma [associação do programa de desenvolvedor da Apple][7] e um dispositivo IOS (o simulador do IOS não dá suporte a notificações por push)
* Windows Uma [conta de desenvolvedor Microsoft Store][8] e um dispositivo Windows 10

## <a name="configure-hub"></a>Configurar um hub de notificação

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Assista a um vídeo mostrando as etapas nesta seção][9].

## <a name="update-the-server-project"></a>Atualizar o projeto do servidor

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Modificar seu aplicativo Cordova

Para garantir que seu projeto de aplicativo Apache Cordova esteja pronto para lidar com notificações por push, instale o plug-in Cordova push e todos os serviços de envio específicos da plataforma.

#### <a name="update-the-cordova-version-in-your-project"></a>Atualize a versão do Cordova no seu projeto.

Se o seu projeto usar uma versão do Apache Cordova anterior à versão 6.1.1, atualize o projeto do cliente. Para atualizar o projeto, execute as seguintes etapas:

* Para abrir o designer de configuração, clique com o botão direito do mouse em `config.xml`.
* Selecione a guia **plataformas** .
* Na caixa de texto **Cordova CLI** , selecione **6.1.1**. 
* Para atualizar o projeto, selecione **Compilar**e, em seguida, selecione **Compilar solução**.

#### <a name="install-the-push-plugin"></a>Instalar o plug-in de push

Apache Cordova aplicativos não manipulam nativamente os recursos de dispositivo ou de rede.  Esses recursos são fornecidos por plug-ins que são publicados no [NPM][10] ou no github. O plug-in `phonegap-plugin-push` manipula notificações por push de rede.

Você pode instalar o plug-in de push de uma das seguintes maneiras:

**No prompt de comando:**

Execute o seguinte comando:

    cordova plugin add phonegap-plugin-push

**De dentro do Visual Studio:**

1. Em Gerenciador de Soluções, abra o arquivo `config.xml`. Em seguida, selecione **plug-ins** > **personalizado**. Em seguida, selecione **git** como a origem da instalação.

2. Insira `https://github.com/phonegap/phonegap-plugin-push` como a origem.

    ![Abra o arquivo config. xml no Gerenciador de Soluções][img1]

3. Selecione a seta ao lado da origem da instalação.

4. Em **SENDER_ID**, se você já tiver uma ID de projeto numérica para o projeto do console do desenvolvedor do Google, poderá adicioná-la aqui. Caso contrário, insira um valor de espaço reservado, como 777777. Se você estiver direcionando para o Android, poderá atualizar esse valor no arquivo config. xml mais tarde.

    >[!NOTE]
    >A partir da versão 2.0.0, o Google-Services. JSON precisa ser instalado na pasta raiz do seu projeto para configurar a ID do remetente. Para obter mais informações, consulte a [documentação de instalação.](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)

5. Selecione **Adicionar**.

O plug-in de push agora está instalado.

#### <a name="install-the-device-plugin"></a>Instalar o plug-in do dispositivo

Siga o mesmo procedimento usado para instalar o plug-in de push. Adicione o plug-in do dispositivo da lista de plug-ins principais. (Para encontrá-lo, selecione **Plugins** > **Core**.) Você precisa desse plug-in para obter o nome da plataforma.

#### <a name="register-your-device-when-the-application-starts"></a>Registrar seu dispositivo quando o aplicativo for iniciado 

Inicialmente, incluímos alguns códigos mínimos para o Android. Posteriormente, você pode modificar o aplicativo para ser executado no iOS ou no Windows 10.

1. Adicione uma chamada para **registerForPushNotifications** durante o retorno de chamada para o processo de entrada. Como alternativa, você pode adicioná-lo na parte inferior do método **onDeviceReady** :

    ```javascript
    // Log in to the service.
    client.login('google')
        .then(function () {
            // Create a table reference.
            todoItemTable = client.getTable('todoitem');

            // Refresh the todoItems.
            refreshDisplay();

            // Wire up the UI Event Handler for the Add Item.
            $('#add-item').submit(addItemHandler);
            $('#refresh').on('click', refreshDisplay);

                // Added to register for push notifications.
            registerForPushNotifications();

        }, handleError);
    ```

    Este exemplo mostra a chamada de **registerForPushNotifications** após a autenticação ser realizada com sucesso. Você pode chamar `registerForPushNotifications()` sempre que necessário.

2. Adicione o novo método **registerForPushNotifications** da seguinte maneira:

    ```javascript
    // Register for push notifications. Requires that phonegap-plugin-push be installed.
    var pushRegistration = null;
    function registerForPushNotifications() {
        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

    // Handle the registration event.
    pushRegistration.on('registration', function (data) {
        // Get the native platform of the device.
        var platform = device.platform;
        // Get the handle returned during registration.
        var handle = data.registrationId;
        // Set the device-specific message template.
        if (platform == 'android' || platform == 'Android') {
            // Register for GCM notifications.
            client.push.register('gcm', handle, {
                mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
            });
        } else if (device.platform === 'iOS') {
            // Register for notifications.
            client.push.register('apns', handle, {
                mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
            });
        } else if (device.platform === 'windows') {
            // Register for WNS notifications.
            client.push.register('wns', handle, {
                myTemplate: {
                    body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                    headers: { 'X-WNS-Type': 'wns/toast' } }
            });
        }
    });

    pushRegistration.on('notification', function (data, d2) {
        alert('Push Received: ' + data.message);
    });

    pushRegistration.on('error', handleError);
    }
    ```
3. Android No código anterior, substitua `Your_Project_ID` pela ID numérica do projeto do seu aplicativo no console do [desenvolvedor do Google][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>Adicional Configurar e executar o aplicativo no Android

Conclua esta seção para habilitar notificações por push para Android.

#### <a name="enable-gcm"></a>Habilitar o firebase Cloud Messaging

Como você está direcionando inicialmente para a plataforma Google Android, você deve habilitar o firebase Cloud Messaging.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>Configurar o back-end do aplicativo móvel para enviar solicitações por push usando o FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Configurar seu aplicativo Cordova para Android

Em seu aplicativo Cordova, abra **config. xml**. Em seguida, substitua `Your_Project_ID` pela ID de projeto numérica do seu aplicativo no [console do desenvolvedor do Google][18].

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

Abra o **index. js**. Em seguida, atualize o código para usar sua ID de projeto numérica.

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-device"></a>Configurar seu dispositivo Android para depuração de USB

Antes de implantar seu aplicativo em seu dispositivo Android, você precisa habilitar a depuração de USB. Execute as seguintes etapas no seu telefone Android:

1. Vá para **configurações** > **sobre telefone**. Em seguida, toque no **número de Build** até que o modo de desenvolvedor seja habilitado (cerca de sete vezes).
2. De volta **às configurações** > **Opções do desenvolvedor**, habilite a **depuração de USB**. Em seguida, conecte seu telefone Android ao seu PC de desenvolvimento com um cabo USB.

Testamos isso usando um dispositivo Google Nexus 5X executando Android 6,0 (marshmallow). No entanto, as técnicas são comuns em qualquer versão moderna do Android.

#### <a name="install-google-play-services"></a>Instalar Google Play Services

O plug-in de push depende do Android Google Play Services para notificações por push.

1. No Visual Studio, selecione **ferramentas** > **Android** > o **Gerenciador de SDK do Android**. Em seguida, expanda a pasta **extras** . Verifique as caixas apropriadas para garantir que cada um dos SDKs a seguir esteja instalado:

   * Android 2,3 ou superior
   * Repositório do Google revisão 27 ou superior
   * Google Play Services 9.0.2 ou superior

2. Selecione **instalar pacotes**. Em seguida, aguarde a conclusão da instalação.

As bibliotecas necessárias atuais são listadas na [documentação de instalação do PhoneGap-plugin-Push][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Testar notificações por push no aplicativo no Android

Agora você pode testar notificações por push executando o aplicativo e inserindo itens na tabela TodoItem. Você pode testar a partir do mesmo dispositivo ou de um segundo dispositivo, desde que esteja usando o mesmo back-end. Teste seu aplicativo Cordova na plataforma Android de uma das seguintes maneiras:

* *Em um dispositivo físico:* Anexe seu dispositivo Android ao seu computador de desenvolvimento com um cabo USB.  Em vez do **Google Android Emulator**, selecione **dispositivo**. O Visual Studio implanta o aplicativo no dispositivo e executa o aplicativo. Em seguida, você pode interagir com o aplicativo no dispositivo.

  Aplicativos de compartilhamento de tela, como o [Mobizen][20] , podem ajudá-lo a desenvolver aplicativos Android. Mobizen projeta sua tela do Android para um navegador da Web em seu computador.

* *Em um emulador do Android:* Há etapas de configuração adicionais que são necessárias quando você está usando um emulador.

    Verifique se você está implantando em um dispositivo virtual que tem APIs do Google definidas como o destino, conforme mostrado no Gerenciador de dispositivo virtual Android (AVD).

    ![Device Manager virtuais do Android](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Se você quiser usar um emulador x86 mais rápido, [Instale o driver HAXM][11]e configure o emulador para usá-lo.

    Adicione uma conta do Google ao dispositivo Android selecionando **aplicativos** > **configurações** > **adicionar conta**. Em seguida, siga os prompts.

    ![Adicionar uma conta do Google ao dispositivo Android](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Execute o aplicativo ToDoList como antes e insira um novo item de tarefas pendentes. Desta vez, um ícone de notificação é exibido na área de notificação. Você pode abrir a gaveta de notificação para exibir o texto completo da notificação.

    ![Exibir notificação](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>Adicional Configurar e executar no iOS

Esta seção destina-se à execução do projeto Cordova em dispositivos iOS. Se você não estiver trabalhando com dispositivos iOS, poderá ignorar esta seção.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Instalar e executar o agente de Build remoto do iOS em um Mac ou serviço de nuvem

Antes de executar um aplicativo Cordova no iOS usando o Visual Studio, percorra as etapas no [Guia de instalação do IOS][12] para instalar e executar o agente de Build remoto.

Verifique se você pode criar o aplicativo para iOS. As etapas no guia de instalação são necessárias para a criação do aplicativo para iOS do Visual Studio. Se você não tiver um Mac, poderá compilar para o iOS usando o agente de Build remoto em um serviço como o MacInCloud. Para obter mais informações, consulte [executar seu aplicativo Ios na nuvem][21].

> [!NOTE]
> O Xcode 7 ou superior é necessário para usar o plug-in de push no iOS.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Localizar a ID a ser usada como a ID do aplicativo

Antes de registrar seu aplicativo para notificações por push, abra o arquivo config. xml em seu aplicativo Cordova, localize o valor do atributo `id` no elemento widget e, em seguida, copie-o para uso posterior. No XML a seguir, a ID é `io.cordova.myapp7777777`.

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

Posteriormente, use esse identificador ao criar uma ID do aplicativo no portal do desenvolvedor da Apple. Se você criar uma ID de aplicativo diferente no portal do desenvolvedor, deverá executar algumas etapas adicionais posteriormente neste tutorial. A ID no elemento widget deve corresponder à ID do aplicativo no portal do desenvolvedor.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrar o aplicativo para notificações por push no portal do desenvolvedor da Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Veja um vídeo que mostra os passos semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Configurar o Azure para enviar notificações por push

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Verifique se a ID do aplicativo corresponde ao seu aplicativo Cordova

Se a ID do aplicativo que você criou em sua conta de desenvolvedor da Apple já corresponder à ID do elemento widget no arquivo config. xml, você poderá ignorar esta etapa. No entanto, se as IDs não corresponderem, execute as seguintes etapas:

1. Exclua a pasta plataformas do seu projeto.
2. Exclua a pasta plugins do seu projeto.
3. Exclua a pasta node_modules do seu projeto.
4. Atualize o atributo ID do elemento widget no arquivo config. xml para usar a ID do aplicativo que você criou em sua conta de desenvolvedor da Apple.
5. Recompile seu projeto.

##### <a name="test-push-notifications-in-your-ios-app"></a>Testar notificações por push em seu aplicativo iOS

1. No Visual Studio, verifique se **Ios** está selecionado como o destino de implantação. Em seguida, selecione **dispositivo** para executar as notificações por push em seu dispositivo IOS conectado.

    Você pode executar as notificações por push em um dispositivo iOS que está conectado ao seu PC com o iTunes. O simulador do iOS não oferece suporte a notificações por push.

2. Selecione o botão **executar** ou **F5** no Visual Studio para compilar o projeto e iniciar o aplicativo em um dispositivo IOS. Em seguida, selecione **OK** para aceitar notificações por push.

   > [!NOTE]
   > O aplicativo solicita confirmação para notificações por push durante a primeira execução.

3. No aplicativo, digite uma tarefa e, em seguida, selecione o ícone de adição **(+)** .
4. Verifique se uma notificação foi recebida. Em seguida, selecione **OK** para ignorar a notificação.

## <a name="optional-configure-and-run-on-windows"></a>Adicional Configurar e executar no Windows

Esta seção descreve como executar o projeto de aplicativo Apache Cordova em dispositivos Windows 10 (o plug-in de push PhoneGap tem suporte no Windows 10). Se você não estiver trabalhando com dispositivos Windows, poderá ignorar esta seção.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registrar seu aplicativo do Windows para notificações por push com WNS

Para usar as opções de armazenamento no Visual Studio, selecione um destino do Windows na lista plataformas de solução, como **Windows-x64** ou **Windows-x86**. (Evite o **Windows-anycpu** para notificações por push.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Veja um vídeo que mostra os passos semelhantes][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Configurar o Hub de notificação para WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Configurar seu aplicativo Cordova para dar suporte a notificações por push do Windows

Abra o designer de configuração clicando com o botão direito do mouse em **config. xml**. Em seguida, selecione **Designer de exibição**. Em seguida, selecione a guia **Windows** e, em seguida, selecione **Windows 10** na **versão de destino do Windows**.

Para dar suporte a notificações por push em suas compilações padrão (depuração), abra o arquivo **Build. JSON** . Em seguida, copie a configuração de "versão" para a configuração de depuração.

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

Após a atualização, o arquivo **Build. JSON** deve conter o seguinte código:

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
        },
    "debug": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
        }
    }
```

Compile o aplicativo e verifique se você não tem erros. Seu aplicativo cliente agora deve se registrar para as notificações do back-end dos aplicativos móveis. Repita esta seção para cada projeto do Windows em sua solução.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testar notificações por push em seu aplicativo do Windows

No Visual Studio, certifique-se de que uma plataforma Windows esteja selecionada como o destino de implantação, como **Windows-x64** ou **Windows-x86**. Para executar o aplicativo em um computador com Windows 10 que hospeda o Visual Studio, escolha **computador local**.

1. Selecione o botão **executar** para compilar o projeto e iniciar o aplicativo.

2. No aplicativo, digite um nome para um novo todoitem e, em seguida, selecione o ícone de adição **(+)** para adicioná-lo.

Verifique se uma notificação é recebida quando o item é adicionado.

## <a name="next-steps"></a>Passos seguintes

* Leia sobre os [hubs de notificação][17] para saber mais sobre as notificações por push.
* Se você ainda não tiver feito isso, continue o tutorial [adicionando a autenticação][14] ao seu aplicativo Apache Cordova.

Saiba como usar os seguintes SDKs:

* [SDK do Apache Cordova][15]
* [SDK do Servidor ASP.NET][1]
* [SDK do Servidor Node.js][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: https://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: https://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: https://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: https://taco.visualstudio.com/en-us/docs/build_ios_cloud/
