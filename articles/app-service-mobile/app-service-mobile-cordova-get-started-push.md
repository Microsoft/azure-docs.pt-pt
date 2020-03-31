---
title: Adicione notificações push a uma aplicação Apache Cordova
description: Saiba como usar aplicações móveis para enviar notificações push para a sua aplicação Apache Cordova.
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 08260437076728421cb6fa393f481d27b95b1782
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461611"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Adicione notificações push à sua app Apache Cordova

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Descrição geral

Neste tutorial, adiciona notificações push ao projeto [de arranque rápido Apache Cordova][5] para que seja enviada uma notificação push para o dispositivo sempre que um registo é inserido.

Se não utilizar o projeto de servidor quickstart descarregado, necessita do pacote de extensão de notificação push. Para mais informações, consulte Trabalhar com o servidor de [back-end .NET SDK para aplicações móveis][1].

## <a name="prerequisites"></a><a name="prerequisites"></a>Pré-requisitos

Este tutorial assume que você tem uma aplicação Apache Cordova que foi desenvolvida com o Visual Studio 2015. Este dispositivo deverá ser executado no Google Android Emulator, um dispositivo Android, um dispositivo Windows ou um dispositivo iOS.

Para concluir este tutorial, precisa de:

* Um PC com [Visual Studio Community 2015][2] ou mais tarde
* [Ferramentas de estúdio visual para Apache Cordova][4]
* Uma [conta Azure ativa][3]
* Um projeto [de arranque rápido Apache Cordova][5] concluído
* (Android) Uma [conta google][6] com um endereço de e-mail verificado
* (iOS) Um programa de [desenvolvedores da Apple][7] e um dispositivo iOS (o iOS Simulator não suporta notificações push)
* (Janelas) Uma conta de [desenvolvimento da Microsoft Store][8] e um dispositivo Windows 10

## <a name="configure-a-notification-hub"></a><a name="configure-hub"></a>Configurar um hub de notificação

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Veja um vídeo mostrando passos nesta secção][9].

## <a name="update-the-server-project"></a>Atualizar o projeto do servidor

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="modify-your-cordova-app"></a><a name="add-push-to-app"></a>Modifique a sua app Cordova

Para garantir que o seu projeto de aplicação Apache Cordova está pronto para lidar com notificações push, instale o plugin push Cordova e quaisquer serviços de push específicos da plataforma.

#### <a name="update-the-cordova-version-in-your-project"></a>Atualize a versão Cordova no seu projeto.

Se o seu projeto utilizar uma versão do Apache Cordova que seja mais cedo do que a versão 6.1.1, atualize o projeto do cliente. Para atualizar o projeto, tome as seguintes etapas:

* Para abrir o designer de `config.xml`configuração, clique à direita .
* Selecione o separador **Plataformas.**
* Na caixa de texto **Cordova CLI,** selecione **6.1.1**. 
* Para atualizar o projeto, selecione **Build**, e, em seguida, selecione **Build Solution**.

#### <a name="install-the-push-plugin"></a>Instale o plugin push

As aplicações Apache Cordova não lidam de forma nativa com dispositivos ou capacidades de rede.  Estas capacidades são fornecidas por plugins que são publicados no [dia 12 ou][10] no GitHub. O `phonegap-plugin-push` plugin trata das notificações de pressão da rede.

Pode instalar o plugin de pressão de uma das seguintes formas:

**A partir do pedido de comando:**

Execute o seguinte comando:

    cordova plugin add phonegap-plugin-push

**De dentro do Estúdio Visual:**

1. No Solution Explorer, `config.xml` abra o ficheiro. Em seguida, selecione **Plugins** > **Custom**. Em seguida, selecione **Git** como fonte de instalação.

2. Entra `https://github.com/phonegap/phonegap-plugin-push` como fonte.

    ![Abra o ficheiro config.xml no Solution Explorer][img1]

3. Selecione a seta ao lado da fonte de instalação.

4. Em **SENDER_ID**, se já tiver um ID de projeto numérico para o projeto da Consola de Desenvolvimento do Google, pode adicioná-lo aqui. Caso contrário, insira um valor de espaço reservado, como o 777777. Se estiver a direcionar o Android, pode atualizar este valor no ficheiro config.xml mais tarde.

    >[!NOTE]
    >A partir da versão 2.0.0, o google-services.json precisa de ser instalado na pasta raiz do seu projeto para configurar o ID do remetente. Para mais informações, consulte a documentação da [instalação.](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)

5. Selecione **Adicionar**.

O plugin de pressão está agora instalado.

#### <a name="install-the-device-plugin"></a>Instale o plugin do dispositivo

Siga o mesmo procedimento utilizado para instalar o plugin de pressão. Adicione o plugin do Dispositivo da lista de plugins Core. (Para encontrá-lo, selecione **Plugins** > **Core**.) Precisa deste plugin para obter o nome da plataforma.

#### <a name="register-your-device-when-the-application-starts"></a>Registe o seu dispositivo quando a aplicação começar 

Inicialmente, incluímos um código mínimo para Android. Mais tarde poderá modificar a aplicação para funcionar no iOS ou no Windows 10.

1. Adicione uma chamada para **registar ForPushNotifications** durante a chamada para o processo de entrada. Em alternativa, pode adicioná-lo na parte inferior do método **onDeviceReady:**

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

    Este exemplo mostra a chamada de **registoS ForPushNotifications** após o sucesso da autenticação. Pode ligar `registerForPushNotifications()` sempre que for necessário.

2. Adicione o novo **método DeRegistoSPushNotifications** da seguinte forma:

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
3. (Android) No código anterior, `Your_Project_ID` substitua-o pelo ID do projeto numérico para a sua aplicação a partir da Consola de Desenvolvimento do [Google][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Opcional) Configure e execute a app no Android

Complete esta secção para permitir notificações push para Android.

#### <a name="enable-firebase-cloud-messaging"></a><a name="enable-gcm"></a>Ativar Firebase Cloud Messaging

Uma vez que está a ser alvo da plataforma Google Android inicialmente, deve ativar o Firebase Cloud Messaging.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-the-mobile-app-back-end-to-send-push-requests-using-fcm"></a><a name="configure-backend"></a>Configure a aplicação móvel na parte de trás para enviar pedidos de push usando a FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Configure o seu aplicativo Cordova para Android

Na sua aplicação Cordova, abra **config.xml**. Em `Your_Project_ID` seguida, substitua-o pelo ID do projeto numérico para a sua aplicação a partir da Consola de Desenvolvimento do [Google][18].

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

Open **index.js**. Em seguida, atualize o código para utilizar o seu ID do projeto numérico.

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-your-android-device-for-usb-debugging"></a><a name="configure-device"></a>Configure o seu dispositivo Android para depuração USB

Antes de poder implementar a sua aplicação para o seu Dispositivo Android, tem de ativar a depuração USB. Dê os seguintes passos no seu telefone Android:

1. Ir a **Definições** > **Sobre telefone**. Em seguida, toque no **número Build** até que o modo de desenvolvimento esteja ativado (cerca de sete vezes).
2. De volta às**opções**de desenvolvedor de **definições,** > ative **a depuração USB**. Em seguida, ligue o seu telefone Android ao seu PC de desenvolvimento com um cabo USB.

Testámos isto usando um dispositivo Google Nexus 5X que executa o Android 6.0 (Marshmallow). No entanto, as técnicas são comuns em qualquer versão moderna do Android.

#### <a name="install-google-play-services"></a>Instale os Serviços google play

O plugin push conta com o Android Google Play Services para notificações push.

1. No Estúdio Visual, selecione **Tools** > **Android Android** > **SDK Manager**. Em seguida, expanda a pasta **Extras.** Verifique as caixas apropriadas para garantir a instalação de cada um dos seguintes SDKs:

   * Android 2.3 ou superior
   * Revisão do Repositório do Google 27 ou superior
   * Google Play Services 9.0.2 ou superior

2. Selecione **Instalações pacotes**. Em seguida, aguarde que a instalação termine.

As bibliotecas requeridas atuais estão listadas na documentação de [instalação phonegap-plugin-push][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Teste de notificações push na aplicação para Android

Agora pode testar notificações push executando a app e inserindo itens na tabela TodoItem. Pode testar a partir do mesmo dispositivo ou a partir de um segundo dispositivo, desde que esteja a utilizar a mesma parte traseira. Teste a sua aplicação Cordova na plataforma Android de uma das seguintes formas:

* *Num dispositivo físico:* Ligue o seu dispositivo Android ao seu computador de desenvolvimento com um cabo USB.  Em vez do **Google Android Emulator**, selecione **Device**. O Visual Studio implementa a aplicação para o dispositivo e executa a aplicação. Em seguida, pode interagir com a aplicação no dispositivo.

  Aplicações de partilha de ecrã, como [o Mobizen,][20] podem ajudá-lo no desenvolvimento de aplicações Android. A Mobizen projeta o seu ecrã Android para um navegador web no seu PC.

* *Num emulador Android:* Existem passos de configuração adicionais que são necessários quando se está a usar um emulador.

    Certifique-se de que está a implementar para um dispositivo virtual que tem as APIs do Google definidas como o alvo, como mostra o gestor de Dispositivo Virtual Android (AVD).

    ![Gestor de dispositivos virtuais Android](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Se pretender utilizar um emulador x86 mais rápido, [instale o controlador HAXM][11]e, em seguida, configure o emulador para o utilizar.

    Adicione uma conta Google ao dispositivo Android selecionando**definições** > de **apps** > **Adicionar conta**. Em seguida, siga as instruções.

    ![Adicione uma conta Google ao dispositivo Android](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Execute a aplicação todolista como antes e insira um novo item todoo. Desta vez, é apresentado um ícone de notificação na área de notificação. Pode abrir a gaveta de notificação para ver o texto completo da notificação.

    ![Ver notificação](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Opcional) Configure e corra no iOS

Esta secção destina-se a executar o projeto Cordova em dispositivos iOS. Se não estiver a trabalhar com dispositivos iOS, pode saltar esta secção.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Instale e execute o agente de construção remota iOS num serviço Mac ou cloud

Antes de poder executar uma aplicação cordova no iOS utilizando o Visual Studio, passe pelos degraus do guia de [configuração do iOS][12] para instalar e executar o agente de construção remota.

Certifique-se de que pode construir a aplicação para iOS. Os passos no guia de configuração são necessários para a construção da aplicação para iOS do Visual Studio. Se não tiver um Mac, pode construir para iOS utilizando o agente de construção remota num serviço como o MacInCloud. Para mais informações, consulte [Executar a sua aplicação iOS na nuvem][21].

> [!NOTE]
> O Xcode 7 ou superior é necessário para utilizar o plugin de impulso no iOS.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Encontre o ID para usar como o seu ID da aplicação

Antes de registar a sua aplicação para notificações push, abra config.xml na sua aplicação Cordova, encontre o valor do `id` atributo no elemento widget e, em seguida, copie-o para posterior utilização. No XML seguinte, o `io.cordova.myapp7777777`ID é .

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

Mais tarde, utilize este identificador quando criar um ID de aplicação no portal de desenvolvimento da Apple. Se criar um ID de aplicação diferente no portal do desenvolvedor, deve dar alguns passos extra sem mais tempo neste tutorial. O ID no elemento widget deve coincidir com o ID da aplicação no portal do desenvolvedor.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registe a aplicação para notificações push no portal de desenvolvimento da Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Veja um vídeo que mostra os passos semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Configure Azure para enviar notificações push

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Verifique se o seu ID da aplicação corresponde à sua aplicação Cordova

Se o ID da Aplicação que criou na sua Conta de Desenvolvimento da Apple já corresponder ao ID do elemento widget no ficheiro config.xml, pode saltar este passo. No entanto, se os IDs não corresponderem, tome os seguintes passos:

1. Elimine a pasta das plataformas do seu projeto.
2. Elimine a pasta de plugins do seu projeto.
3. Elimine a pasta node_modules do seu projeto.
4. Atualize o atributo id do elemento widget no ficheiro config.xml para utilizar o ID da aplicação que criou na sua conta de desenvolvimento da Apple.
5. Reconstrói o seu projeto.

##### <a name="test-push-notifications-in-your-ios-app"></a>Teste de notificações push na sua aplicação iOS

1. No Estúdio Visual, certifique-se de que o **iOS** é selecionado como alvo de implementação. Em seguida, selecione **Device** para executar as notificações push no seu dispositivo iOS conectado.

    Pode executar as notificações push num dispositivo iOS que esteja ligado ao seu PC com o iTunes. O simulador do iOS não suporta notificações push.

2. Selecione o botão **Executar** ou **F5** no Estúdio Visual para construir o projeto e iniciar a aplicação num dispositivo iOS. Em seguida, selecione **OK** para aceitar notificações push.

   > [!NOTE]
   > A aplicação solicita confirmação para notificações push durante a primeira execução.

3. Na aplicação, escreva uma tarefa e, em seguida, selecione o ícone mais **(+).**
4. Verifique se foi recebida uma notificação. Em seguida, selecione **OK** para rejeitar a notificação.

## <a name="optional-configure-and-run-on-windows"></a>(Opcional) Configure e corra no Windows

Esta secção descreve como executar o projeto da aplicação Apache Cordova nos dispositivos Windows 10 (o plugin de push PhoneGap é suportado no Windows 10). Se não estiver a trabalhar com dispositivos Windows, pode ignorar esta secção.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registe a sua aplicação Windows para notificações push com WNS

Para utilizar as opções da Loja no Estúdio Visual, selecione um alvo windows da lista de Plataformas solution, como **Windows-x64** ou **Windows-x86**. (Evite **o Windows-AnyCPU** para notificações push.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Veja um vídeo que mostra os passos semelhantes][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Configure o centro de notificação para wns

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Configure a sua app Cordova para suportar notificações push do Windows

Abra o designer de configuração clicando **config.xml**. Em seguida, selecione **'Ver Designer.** Em seguida, selecione o separador **Windows** e, em seguida, selecione **o Windows 10** na versão do Windows **Target**.

Para suportar notificações push nas suas construções padrão (depuração), abra o ficheiro **build.json.** Em seguida, copie a configuração de "desbloqueio" para a sua configuração de depuração.

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

Após a atualização, o ficheiro **build.json** deve conter o seguinte código:

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

Construa a aplicação e verifique se não tem erros. A sua aplicação de cliente deverá agora registar-se para as notificações das Aplicações Móveis. Repita esta secção para todos os projetos windows na sua solução.

#### <a name="test-push-notifications-in-your-windows-app"></a>Teste de notificações push na sua aplicação Windows

No Estúdio Visual, certifique-se de que uma plataforma Windows é selecionada como alvo de implementação, como **windows-x64** ou **Windows-x86**. Para executar a aplicação num PC windows 10 que esteja hospedando o Estúdio Visual, escolha **Máquina Local**.

1. Selecione o botão **Executar** para construir o projeto e iniciar a aplicação.

2. Na aplicação, digite um nome para um novo todoitem e, em seguida, selecione o ícone plus **(+)** para adicioná-lo.

Verifique se uma notificação é recebida quando o artigo é adicionado.

## <a name="next-steps"></a><a name="next-steps"></a>Passos seguintes

* Leia sobre centros de [notificação][17] para saber sobre notificações push.
* Se ainda não o fez, continue o tutorial [adicionando autenticação][14] à sua aplicação Apache Cordova.

Saiba como utilizar os seguintes SDKs:

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
