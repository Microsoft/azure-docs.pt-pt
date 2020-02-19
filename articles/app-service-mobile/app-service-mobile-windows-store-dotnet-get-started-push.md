---
title: Adicione notificações push à sua aplicação UWP
description: Saiba como utilizar aplicações móveis do Serviço de Aplicações Azure e Hubs de Notificação Azure para enviar notificações push para a sua aplicação Universal Windows Platform (UWP).
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 4438084a9471bf816b371b7e663856205f87f8ba
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461424"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Adicione notificações push à sua aplicação Windows

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Descrição geral

Neste tutorial, adiciona notificações push ao projeto [de arranque rápido](app-service-mobile-windows-store-dotnet-get-started.md) do Windows para que seja enviada uma notificação push para o dispositivo sempre que um registo é inserido.

Se não utilizar o projeto de servidor de arranque rápido descarregado, necessitará do pacote de extensão de notificação push. Consulte [o Trabalho com o servidor de backend .NET SDK para aplicações móveis Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter mais informações.

## <a name="configure-hub"></a>Configure um Centro de Notificação

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Registar a aplicação para notificações push

Tem de submeter a sua aplicação à Microsoft Store e, em seguida, configurar o seu projeto de servidor para se integrar com os Serviços de [Notificação do Windows (WNS)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) para enviar push.

1. No Visual Studio Solution Explorer, clique no projeto da aplicação UWP, clique em **Store** > **Associate App com a Loja...** .

    ![App associada com a Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)

2. No assistente, clique em **Seguinte**, inscreva-se na sua conta Microsoft, digite um nome para a sua aplicação na Reserva de um novo nome de **aplicação**e, em seguida, clique em **Reserve**.
3. Depois de o registo da aplicação ser criado com sucesso, selecione o novo nome da aplicação, clique em **Next**, e, em seguida, clique em **Associate**. Isto adiciona as informações de registo necessárias à Microsoft Store no manifesto de aplicação.
4. Navegue para o Portal de Registo de [Aplicações](https://apps.dev.microsoft.com/) e inscreva-se na sua conta Microsoft. Clique na aplicação Da Loja do Windows que associou no passo anterior.
5. Na página de registo, tome nota do valor em segredo de **Aplicação** e do **Pacote SID**, que utilizará em seguida para configurar o seu backend de aplicação móvel.

    ![App associada com a Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > O segredo de cliente e o SID do pacote são credenciais de segurança importantes. Não partilhe estes valores com ninguém e não os distribua com a aplicação. O Id da **Aplicação** é utilizado com o segredo para configurar a autenticação da Conta Microsoft.

[O App Center](https://docs.microsoft.com/appcenter/sdk/push/uwp#prerequisite---register-your-app-for-windows-notification-services-wns) também tem instruções para configurar aplicações UWP para notificações push.

## <a name="configure-the-backend-to-send-push-notifications"></a>Configure o backend para enviar notificações push

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>Atualize o servidor para enviar notificações push

Utilize o procedimento abaixo que corresponda ao seu tipo de projeto de backend&mdash;[.NET backend](#dotnet) ou [Node.js backend](#nodejs).

### <a name="dotnet"></a>projeto de backend .NET

1. No Estúdio Visual, clique no projeto do servidor e clique em **Gerir pacotes NuGet,** procure microsoft.Azure.NotificationHubs e, em seguida, clique em **Instalar**. Isto instala a biblioteca de clientes Do Centro de Notificação.
2. Expandir **controladores,** abrir TodoItemController.cs e adicionar o seguinte usando declarações:

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. No método **PostTodoItem,** adicione o seguinte código após a chamada para **InsertAsync:**

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

    Este código diz ao centro de notificação para enviar uma notificação push depois de um novo item ser inserção.

4. Republique o projeto do servidor.

### <a name="nodejs"></a>Projeto de backend Node.js
1. Prepara o teu projeto de backend.
2. Substitua o código existente no ficheiro todoitem.js pelo seguinte:

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

    Isto envia uma notificação de torrada WNS que contém o item.text quando um novo item todo o artigo é inserido.

3. Ao editar o ficheiro no seu computador local, republique o projeto do servidor.

## <a id="update-app"></a>Adicione notificações push à sua aplicação
Em seguida, a sua aplicação deve registar-se para notificações push no arranque. Quando já tiver ativado a autenticação, certifique-se de que o utilizador faz o insígnio antes de tentar registar-se para notificações push.

1. Abra o ficheiro do projeto **App.xaml.cs** e adicione as seguintes declarações `using`:

    ```csharp
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    ```

2. No mesmo ficheiro, adicione a seguinte definição de método **InitNotificationsAsync** à classe **App:**

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

    Este código recupera o ChannelURI para a aplicação a partir da WNS e, em seguida, regista esse ChannelURI com a sua App Service Mobile App.

3. No topo do manipulador de eventos **OnLaunched** em **App.xaml.cs,** adicione o modificador **de async** à definição do método e adicione a seguinte chamada ao novo método **InitNotificationsAsync,** como no seguinte exemplo:

    ```csharp
    protected async override void OnLaunched(LaunchActivatedEventArgs e)
    {
        await InitNotificationsAsync();

        // ...
    }
    ```

    Isto garante que o CanalURI de curta duração é registado cada vez que a aplicação é lançada.

4. Reconstrua o seu projeto de aplicação UWP. A aplicação já está pronta para receber notificações de alerta.

## <a id="test"></a>Teste notificações push na sua aplicação

[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Passos seguintes

Saiba mais sobre notificações push:

* [Como utilizar o cliente gerido para aplicações móveis Azure](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications) Os modelos dão-lhe flexibilidade para enviar empurrões de plataformas cruzadas e empurrões localizados. Saiba como registar modelos.
* [Diagnosticar problemas](../notification-hubs/notification-hubs-push-notification-fixer.md) de notificação push Existem várias razões pelas quais as notificações podem ser retiradas ou não acabam em dispositivos. Este tópico mostra-lhe como analisar e descobrir a causa principal das falhas de notificação push.

Considere continuar a um dos seguintes tutoriais:

* [Adicione autenticação à sua aplicação](app-service-mobile-windows-store-dotnet-get-started-users.md) Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.
* [Ativar sincronização offline para a sua aplicação](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Aprenda a adicionar suporte offline à sua aplicação utilizando um backend de Aplicação Móvel. A sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel &mdash; visualizar, adicionar ou modificar dados &mdash;, mesmo quando não existe qualquer ligação de rede.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
