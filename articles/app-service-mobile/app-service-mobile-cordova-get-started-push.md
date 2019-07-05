---
title: Adicionar notificações push para uma aplicação Apache Cordova com a funcionalidade de aplicações móveis do serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como utilizar aplicações móveis para enviar notificações push à aplicação Apache Cordova.
services: app-service\mobile
documentationcenter: javascript
manager: crdun
editor: ''
author: elamalani
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: e6755c3fb1fca342d94fdaa96c0dce614d762172
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443562"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Adicionar notificações push à aplicação Apache Cordova

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center está a investir em serviços de novo e integrados essenciais para o desenvolvimento de aplicações móveis. Os desenvolvedores podem usar **crie**, **teste** e **distribuir** serviços para configurar os pipelines de integração e entrega contínuas. Assim que a aplicação é implementada, os programadores podem monitorizar o estado e a utilização da sua aplicação com o **Analytics** e **diagnóstico** serviços e interaja com os utilizadores que utilizam o **Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados de aplicações na cloud. Confira [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-cordova-get-started-push) hoje mesmo.
>

## <a name="overview"></a>Descrição geral

Neste tutorial, adicionar notificações push para o [guia de introdução do Apache Cordova][5] do projeto para que uma notificação push é enviada para o dispositivo sempre que um registro é inserido.

Se não utilizar o projeto de servidor de início rápido transferido, terá do pacote de extensão de notificação push. Para obter mais informações, consulte [trabalhar com o servidor de back-end de .NET SDK para Mobile Apps][1].

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial parte do princípio de que tem uma aplicação Apache Cordova, que foi desenvolvida com o Visual Studio 2015. Este dispositivo deve ser executado no emulador do Google Android, um dispositivo Android, um dispositivo Windows ou um dispositivo iOS.

Para concluir este tutorial, precisa de:

* Um PC com [Visual Studio Community 2015][2] ou posterior
* [Visual Studio Tools para Apache Cordova][4]
* Uma [conta ativa do Azure][3]
* Um concluídos [guia de introdução do Apache Cordova][5] projeto
* (Android) R [conta Google][6] com um endereço de correio eletrónico verificado
* (iOS) Uma [associação ao programa de programador da Apple][7] e um dispositivo iOS (iOS simulador não suporta notificações push)
* (Windows) R [conta de programador do Microsoft Store][8] e um dispositivo Windows 10

## <a name="configure-hub"></a>Configurar um hub de notificação

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Ver um vídeo que mostra os passos nesta secção][9].

## <a name="update-the-server-project"></a>Atualizar o projeto de servidor

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Modificar a aplicação Cordova

Para garantir que seu projeto de aplicação Apache Cordova está pronto para processar as notificações push, instale o plug-in do Cordova push além de quaisquer serviços específicos da plataforma push.

#### <a name="update-the-cordova-version-in-your-project"></a>Atualize a versão do Cordova no seu projeto.

Se o seu projeto usar uma versão do Apache Cordova, que é anterior à versão 6.1.1, atualize o projeto de cliente. Para atualizar o projeto, siga os passos seguintes:

* Para abrir o estruturador de configuração, clique com botão direito `config.xml`.
* Selecione o **plataformas** separador.
* Na **Cordova CLI** caixa de texto, selecione **6.1.1**. 
* Para atualizar o projeto, selecione **crie**e, em seguida, selecione **compilar solução**.

#### <a name="install-the-push-plugin"></a>Instalar o plug-in de push

Aplicações do Cordova no Apache manipula nativamente capacidades de rede ou dispositivo.  Estas capacidades são fornecidas por plug-ins que são publicados qualquer um na [npm][10] ou no GitHub. O `phonegap-plugin-push` Plug-in processa as notificações de push de rede.

Pode instalar o plug-in de push em uma das seguintes formas:

**Na linha de comandos:**

Execute o seguinte comando:

    cordova plugin add phonegap-plugin-push

**De dentro do Visual Studio:**

1. No Explorador de soluções, abra o `config.xml` ficheiro. Em seguida, selecione **plug-ins** > **personalizado**. Em seguida, selecione **Git** como origem de instalação.

2. Introduza `https://github.com/phonegap/phonegap-plugin-push` como origem.

    ![Abra o ficheiro de Config. XML no Explorador de soluções][img1]

3. Selecione a seta junto à origem de instalação.

4. Na **SENDER_ID**, se já tenha um ID de projeto numérico para o projeto do Google Developer Console, pode adicioná-lo aqui. Caso contrário, introduza um valor de marcador de posição, como 777777. Se estiver a filtrar o Android, pode atualizar este valor no arquivo Config. XML, mais tarde.

    >[!NOTE]
    >A partir da versão 2.0.0, google-Services. JSON tem de ser instalado na pasta raiz do seu projeto para configurar o ID de remetente. Para obter mais informações, consulte o [documentação de instalação.](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)

5. Selecione **Adicionar**.

O plug-in de push está agora instalado.

#### <a name="install-the-device-plugin"></a>Instalar o plug-in do dispositivo

Siga o mesmo procedimento utilizado para instalar o plug-in de push. Adicione o plug-in de dispositivo a partir da lista de plug-ins de núcleo. (Para encontrá-lo, selecione **plug-ins** > **Core**.) Precisa este plug-in para obter o nome de plataforma.

#### <a name="register-your-device-when-the-application-starts"></a>Registar o seu dispositivo quando o aplicativo é iniciado 

Inicialmente, incluímos alguns códigos mínimo para Android. Mais tarde pode modificar o aplicativo seja executado no iOS ou Windows 10.

1. Adicione uma chamada para **registerForPushNotifications** durante o retorno de chamada para o processo de início de sessão. Em alternativa, pode adicioná-lo na parte inferior a **onDeviceReady** método:

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

    Este exemplo mostra chamar **registerForPushNotifications** após a autenticação é bem-sucedida. Pode chamar `registerForPushNotifications()` sempre que é necessário.

2. Adicionar a nova **registerForPushNotifications** método da seguinte forma:

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
3. (Android) No código anterior, substitua `Your_Project_ID` com o numérico ID do projeto para a aplicação a partir de [Google Developer Console][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Opcional) Configurar e executar a aplicação no Android

Conclua esta seção para ativar as notificações push para Android.

#### <a name="enable-gcm"></a>Ativar Firebase Cloud Messaging

Uma vez que estiver a filtrar inicialmente a plataforma Google Android, tem de ativar Firebase Cloud Messaging.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>Configurar a aplicação móvel de back-end para enviar pedidos de push com FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Configurar a aplicação Cordova para Android

Na sua aplicação Cordova, abra **Config. XML**. Em seguida, substitua `Your_Project_ID` com o numérico ID do projeto para a aplicação a partir de [Google Developer Console][18].

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

Open **Index**. Em seguida, atualize o código para usar o ID numérico do projeto.

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-device"></a>Configurar o seu dispositivo Android para a depuração de USB

Antes de poder implementar a aplicação para o seu dispositivo Android, tem de ativar a depuração de USB. Siga os passos seguintes no seu telemóvel Android:

1. Aceda a **configurações** > **acerca do telemóvel**. Em seguida, toque a **número de compilação** até que o modo de programador é ativado (cerca de sete vezes).
2. De volta **configurações** > **opções de programador**, ativar **depuração de USB**. Em seguida, ligar o seu telemóvel Android para o desenvolvimento de PC com um cabo USB.

Que fizemos o teste a utilizar um dispositivo de X Google Nexus 5 a executar o Android 6.0 (Marshmallow). No entanto, as técnicas são comuns em qualquer versão Android modernos.

#### <a name="install-google-play-services"></a>Instalar serviços do Google Play

O plug-in de push se baseia em Android serviços do Google Play para as notificações push.

1. No Visual Studio, selecione **ferramentas** > **Android** > **Gestor do Android SDK**. Em seguida, expanda o **Extras** pasta. Verifique as caixas apropriadas para garantir que cada um dos SDKs seguintes está instalada:

   * Android 2.3 ou superior
   * Repositório Google revisão 27 ou superior
   * Serviços do Google Play 9.0.2 ou superior

2. Selecione **instalar pacotes**. Em seguida, aguarde a conclusão da instalação.

As bibliotecas necessárias atuais são apresentadas no [documentação de instalação push de plug-in de phonegap][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Notificações de push de teste na aplicação no Android

Notificações de push de teste agora pode executá-la e inserir itens na tabela TodoItem. Pode testar a partir do mesmo dispositivo ou a partir de um segundo dispositivo, enquanto estiver a utilizar o mesmo back-end. Teste a aplicação Cordova na plataforma Android em uma das seguintes formas:

* *Num dispositivo físico:* Anexe o seu dispositivo Android para o seu computador de desenvolvimento com um cabo USB.  Em vez de **emulador do Google Android**, selecione **dispositivo**. Visual Studio implementa a aplicação no dispositivo e executa o aplicativo. Em seguida, pode interagir com a aplicação no dispositivo.

  Aplicativos de compartilhamento de tela, como [Mobizen][20] pode ajudá-lo a desenvolver aplicações Android. Mobizen projeta sua tela Android para um navegador da web no seu PC.

* *No emulador do Android:* Existem passos de configuração adicionais que são necessários quando estiver a utilizar um emulador.

    Certifique-se de que está a implementar um dispositivo virtual com APIs do Google definida como destino, conforme mostrado no Gestor de dispositivo Virtual Android (AVD).

    ![Gestor de dispositivos Android Virtual](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Se quiser usar um mais rápido x86 emulador [instalar o controlador HAXM][11]e, em seguida, configurar o emulador para utilizá-lo.

    Adicionar uma conta do Google para o dispositivo Android, selecionando **aplicações** > **definições** > **adicionar conta**. Em seguida, siga as instruções.

    ![Adicionar uma conta do Google para o dispositivo Android](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Execute a aplicação de todolist como antes e inserir um novo item de lista de tarefas. Desta vez, é apresentado um ícone de notificação na área de notificação. É possível abrir a gaveta de notificação para ver o texto completo da notificação.

    ![Notificação de vista](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Opcional) Configurar e executar em dispositivos iOS

Esta secção destina-se à execução do projeto de Cordova em dispositivos iOS. Se não estiver a trabalhar com dispositivos iOS, pode ignorar esta secção.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Instalar e executar o agente de compilação remoto do iOS num Mac ou serviço cloud

Antes de poder executar uma aplicação Cordova em dispositivos iOS com o Visual Studio, percorrer os passos a [guia de instalação do iOS][12] para instalar e executar o agente de compilação remoto.

Certifique-se de que pode criar a aplicação para iOS. Os passos no guia de configuração são necessários para compilar a aplicação para iOS a partir do Visual Studio. Se não tiver um Mac, pode criar para iOS, utilizando o agente de compilação remoto num serviço como MacInCloud. Para obter mais informações, consulte [executar a sua aplicação iOS na cloud][21].

> [!NOTE]
> Xcode 7 ou superior, é necessário para utilizar o plug-in de push no iOS.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Localizar o ID para utilizar como o ID da aplicação

Antes de registar a sua aplicação para as notificações push, abra Config. xml na sua aplicação Cordova, encontre o `id` valor do elemento de widget de atributo e, em seguida, copie-o para utilização posterior. O seguinte XML, o ID é `io.cordova.myapp7777777`.

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

Posteriormente, utilize este identificador ao criar um ID de aplicação no portal de programador da Apple. Se criar um ID de aplicação diferente no portal do programador, tem de executar alguns passos adicionais mais tarde neste tutorial. O ID do elemento de widget tem de corresponder ao ID da aplicação no portal do programador.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registar a aplicação para notificações push no portal de programador da Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Veja um vídeo que mostra os passos semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Configurar o Azure para enviar notificações push

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Certifique-se de que o ID da aplicação corresponde à sua aplicação Cordova

Se o ID da aplicação que criou na sua conta de programador Apple já coincidir com o ID do elemento widget no arquivo Config. XML, pode ignorar este passo. No entanto, se os IDs não corresponderem, siga os passos seguintes:

1. Elimine a pasta de plataformas do seu projeto.
2. Elimine a pasta de plug-ins do seu projeto.
3. Elimine a pasta de node_modules do seu projeto.
4. Atualize o atributo de id do elemento widget no arquivo Config. para utilizar o ID da aplicação que criou na sua conta de programador da Apple.
5. Recrie seu projeto.

##### <a name="test-push-notifications-in-your-ios-app"></a>Notificações de push de teste na sua aplicação iOS

1. No Visual Studio, certifique-se de que **iOS** está selecionado como o destino de implementação. Em seguida, selecione **dispositivo** para executar as notificações push no seu dispositivo iOS ligado.

    Pode executar as notificações push num dispositivo iOS que está ligado ao seu PC com o iTunes. O simulador do iOS não suporta notificações push.

2. Selecione o **execute** botão ou **F5** no Visual Studio para compilar o projeto e iniciar a aplicação no dispositivo iOS. Em seguida, selecione **OK** para aceitar notificações push.

   > [!NOTE]
   > A aplicação solicita a confirmação para notificações push durante a primeira execução.

3. Na aplicação, escreva uma tarefa e, em seguida, selecione o sinal de adição **(+)** ícone.
4. Certifique-se de que foi recebida uma notificação. Em seguida, selecione **OK** para dispensar a notificação.

## <a name="optional-configure-and-run-on-windows"></a>(Opcional) Configurar e executar no Windows

Esta secção descreve como executar o projeto de aplicação Apache Cordova em dispositivos Windows 10 (o plug-in de push de PhoneGap é suportado no Windows 10). Se não estiver a trabalhar com dispositivos Windows, pode ignorar esta secção.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registar a sua aplicação do Windows para as notificações push com o WNS

Para utilizar as opções de Store no Visual Studio, selecione um destino do Windows na lista de plataformas de solução, como **Windows x64** ou **Windows x86**. (Evitar **Windows AnyCPU** para as notificações push.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Veja um vídeo que mostra os passos semelhantes][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Configurar o hub de notificação para o WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Configurar a aplicação Cordova para suportar notificações de push do Windows

Abrir o estruturador de configuração clicando **Config. XML**. Em seguida, selecione **estruturador de vistas**. Em seguida, selecione o **Windows** separador e, em seguida, selecione **com o Windows 10** sob **versão do Windows de destino**.

Para suportar notificações push em compilações padrão (depuração), abra a **build.json** ficheiro. Em seguida, copie a configuração da "versão" à sua configuração de depuração.

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

Após a atualização, o **build.json** deve conter o seguinte código:

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

Criar a aplicação e certifique-se de que tem sem erros. A aplicação de cliente deve agora registar-se as notificações de aplicações móveis back-end. Repita esta secção para todos os projetos do Windows na sua solução.

#### <a name="test-push-notifications-in-your-windows-app"></a>Notificações de push de teste na sua aplicação do Windows

No Visual Studio, certifique-se de que uma plataforma Windows está selecionada como o destino de implantação, como **Windows x64** ou **Windows x86**. Para executar a aplicação nos PCs Windows 10 que está a alojar o Visual Studio, escolha **computador Local**.

1. Selecione o **executar** botão para compilar o projeto e iniciar a aplicação.

2. Na aplicação, escreva um nome para um todoitem novo e, em seguida, selecione o sinal de adição **(+)** ícone para adicioná-lo.

Certifique-se de que uma notificação é recebida quando o item é adicionado.

## <a name="next-steps"></a>Passos seguintes

* Leia sobre [os Hubs de notificação][17] para saber mais sobre as notificações push.
* Se ainda não o fez, continue o tutorial ao [adicionar autenticação][14] à sua aplicação Apache Cordova.

Saiba como utilizar os SDKs seguintes:

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
