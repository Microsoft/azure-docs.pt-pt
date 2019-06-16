---
title: Adicionar notificações push à aplicação plataforma Universal do Windows (UWP) | Documentos da Microsoft
description: Saiba como utilizar aplicações de Mobile do serviço de aplicações do Azure e os Hubs de notificação do Azure para enviar notificações push à aplicação plataforma Universal do Windows (UWP).
services: app-service\mobile,notification-hubs
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: 7efd853e7b66933cac811625d7510139864f41f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128037"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Adicionar notificações push à aplicação Windows

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Descrição geral

Neste tutorial, adicionar notificações push para o [guia de introdução do Windows](app-service-mobile-windows-store-dotnet-get-started.md) do projeto para que uma notificação push é enviada para o dispositivo sempre que um registro é inserido.

Se não utilizar o projeto de servidor de início rápido transferido, terá do pacote de extensão de notificação push. Ver [trabalhar com o SDK do servidor de back-end de .NET para aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter mais informações.

## <a name="configure-hub"></a>Configurar um Hub de notificação

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Registar a aplicação para notificações push

Tem de submeter a sua aplicação para a Microsoft Store, em seguida, configurar seu projeto de servidor para integrar com o [serviços de notificação Windows (WNS)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) para enviar push.

1. No Explorador de soluções do Visual Studio, clique no projeto de aplicação UWP, clique em **Store** > **associar aplicação a Store...** .

    ![Associar a aplicação à Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)

2. No assistente, clique em **próxima**, inicie sessão com a sua conta Microsoft, escreva um nome para a sua aplicação na **reservar um novo nome de aplicação**, em seguida, clique em **reserva**.
3. Quando o registo da aplicação é criado com êxito, selecione o novo nome de aplicação, clique em **próxima**e, em seguida, clique em **associar**. Esta ação adiciona as informações de registo necessárias do Microsoft Store para o manifesto do aplicativo.
4. Navegue para o [Portal de registo de aplicação](https://apps.dev.microsoft.com/) e inicie sessão com a sua conta Microsoft. Clique na aplicação da Windows Store associados no passo anterior.
5. Na página de registro, tome nota do valor sob **segredos da aplicação** e o **SID do pacote**, que em seguida irá utilizar para configurar o seu back-end de aplicação móvel.

    ![Associar a aplicação à Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > O segredo de cliente e o SID do pacote são credenciais de segurança importantes. Não partilhe estes valores com ninguém e não os distribua com a aplicação. O **Id da aplicação** é utilizado com o segredo para configurar a autenticação de Account Microsoft.

[App Center](https://docs.microsoft.com/appcenter/sdk/push/uwp#prerequisite---register-your-app-for-windows-notification-services-wns) também tem instruções para configurar as aplicações UWP para notificações push.

## <a name="configure-the-backend-to-send-push-notifications"></a>Configurar o back-end para enviar notificações push

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>Atualizar o servidor para enviar notificações push

Utilize o procedimento abaixo que corresponda ao seu tipo de projeto de back-end&mdash;ambos [back-end .NET](#dotnet) ou [back-end de node. js](#nodejs).

### <a name="dotnet"></a>Projeto de back-end de .NET

1. No Visual Studio, o projeto de servidor com o botão direito e clique em **gerir pacotes NuGet**, procure notificationhubs, em seguida, clique em **instalar**. Esta ação instala a biblioteca de cliente dos Hubs de notificação.
2. Expanda **controladores**, abra TodoItemController.cs e adicione as seguintes instruções "using":

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. Na **PostTodoItem** método, adicione o seguinte código após a chamada para **InsertAsync**:

    ```csharp
    // Get the settings for the server project.
    HttpConfiguration config = this.Configuration;
    MobileAppSettingsDictionary settings =
        this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

    // Get the Notification Hubs credentials for the Mobile App.
    string notificationHubName = settings.NotificationHubName;
    string notificationHubConnection = settings
        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

    // Create the notification hub client.
    NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

    // Define a WNS payload
    var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                            + item.Text + @"</text></binding></visual></toast>";
    try
    {
        // Send the push notification.
        var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

        // Write the success result to the logs.
        config.Services.GetTraceWriter().Info(result.State.ToString());
    }
    catch (System.Exception ex)
    {
        // Write the failure result to the logs.
        config.Services.GetTraceWriter()
            .Error(ex.Message, null, "Push.SendAsync Error");
    }
    ```

    Esse código diz ao hub de notificação para enviar uma notificação push depois de um novo item é a inserção.

4. Voltar a publicar o projeto de servidor.

### <a name="nodejs"></a>Projeto de back-end de node. js
1. Se ainda não fez isso, [transfira o projeto de início rápido](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) ou outra utilização a [editor online no portal do Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Substitua o código existente no ficheiro todoitem.js com o seguinte:

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see https://aka.ms/nodejshubs
    logger.info('Running TodoItem.insert');

    // Define the WNS payload that contains the new item Text.
    var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
                                + context.item.text + "</text></binding></visual></toast>";

    // Execute the insert.  The insert returns the results as a Promise,
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured
            if (context.push) {
                // Send a WNS native toast notification.
                context.push.wns.sendToast(null, payload, function (error) {
                    if (error) {
                        logger.error('Error while sending push notification: ', error);
                    } else {
                        logger.info('Push notification sent successfully!');
                    }
                });
            }
            // Don't forget to return the results from the context.execute()
            return results;
        })
        .catch(function (error) {
            logger.error('Error while running context.execute: ', error);
        });
    });

    module.exports = table;
    ```

    Esta ação envia uma notificação de alerta do WNS que contém o item.text Quando um novo item é inserido.

3. Ao editar o ficheiro no seu computador local, voltar a publicar o projeto de servidor.

## <a id="update-app"></a>Adicionar notificações push à sua aplicação
Em seguida, a aplicação tem de se registar para as notificações push no arranque. Quando já tiver ativado a autenticação, certifique-se de que o utilizador inicia sessão antes de tentar se registrar para notificações push.

1. Abra o **App.xaml.cs** ficheiro de projeto e adicione o seguinte `using` instruções:

    ```csharp
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    ```

2. No mesmo ficheiro, adicione as seguintes **InitNotificationsAsync** definição de método para o **aplicação** classe:

    ```csharp
    private async Task InitNotificationsAsync()
    {
        // Get a channel URI from WNS.
        var channel = await PushNotificationChannelManager
            .CreatePushNotificationChannelForApplicationAsync();

        // Register the channel URI with Notification Hubs.
        await App.MobileService.GetPush().RegisterAsync(channel.Uri);
    }
    ```

    Este código obtém o ChannelURI para a aplicação a partir do WNS e, em seguida, regista esse ChannelURI com a sua aplicação de Mobile do serviço de aplicações.

3. Na parte superior a **OnLaunched** manipulador de eventos **App.xaml.cs**, adicionar o **async** modificador para a definição de método e adicione a seguinte chamada para o novo  **InitNotificationsAsync** método, como no exemplo seguinte:

    ```csharp
    protected async override void OnLaunched(LaunchActivatedEventArgs e)
    {
        await InitNotificationsAsync();

        // ...
    }
    ```

    Isso garante que o ChannelURI de curta duração é registrado sempre que a aplicação for iniciada.

4. Recrie seu projeto de aplicação UWP. A aplicação já está pronta para receber notificações de alerta.

## <a id="test"></a>Notificações de push de teste na sua aplicação

[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Passos seguintes

Saiba mais sobre as notificações push:

* [Como utilizar o cliente gerido para Mobile Apps do Azure](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications) modelos dão-lhe flexibilidade para enviar pushes entre plataformas e de push localizadas. Saiba como registar modelos.
* [Diagnosticar problemas de notificação push](../notification-hubs/notification-hubs-push-notification-fixer.md) há vários motivos por que notificações podem obter removidas ou não chegarem nos dispositivos. Este tópico mostra-lhe como analisar e descobrir a causa de falhas de notificação push.

Considere continuar em uma das seguintes tutoriais:

* [Adicione autenticação à sua aplicação](app-service-mobile-windows-store-dotnet-get-started-users.md) Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.
* [Permitir sincronização offline para a sua aplicação](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Saiba como adicionar suporte offline à aplicação utilizando um back-end de aplicação móvel. A sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel &mdash; visualizar, adicionar ou modificar dados &mdash;, mesmo quando não existe qualquer ligação de rede.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
