---
title: Adicionar notificações por push ao seu aplicativo de Plataforma Universal do Windows (UWP) | Microsoft Docs
description: Saiba como usar os aplicativos móveis de serviço Azure App e os hubs de notificação do Azure para enviar notificações por push para seu aplicativo de Plataforma Universal do Windows (UWP).
services: app-service\mobile,notification-hubs
documentationcenter: windows
author: elamalani
manager: crdun
editor: ''
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: e3e82c971fee7f7dd95e6f9ef72631e8e82ebe7f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025260"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Adicionar notificações por push ao seu aplicativo do Windows

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> O Visual Studio App Center dá suporte a serviços de ponta a ponta e integrados central ao desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **Compilar**, **testar** e **distribuir** serviços para configurar o pipeline de integração e entrega contínua. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso de seus aplicativos usando os serviços de **análise** e **diagnóstico** e se envolver com os usuários usando o serviço de **envio por push** . Os desenvolvedores também podem aproveitar a **autenticação** para autenticar seus usuários e o serviço de **dados** para manter e sincronizar dados de aplicativos na nuvem.
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com App Center [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje.

## <a name="overview"></a>Descrição geral

Neste tutorial, você adicionará notificações por push ao projeto de [início rápido do Windows](app-service-mobile-windows-store-dotnet-get-started.md) para que uma notificação por push seja enviada ao dispositivo toda vez que um registro for inserido.

Se você não usar o projeto baixado do servidor de início rápido, será necessário o pacote de extensão de notificação por push. Consulte [trabalhar com o SDK do servidor de back-end do .net para aplicativos móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter mais informações.

## <a name="configure-hub"></a>Configurar um hub de notificação

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Registar a aplicação para notificações push

Você precisa enviar seu aplicativo para a Microsoft Store e, em seguida, configurar o projeto de servidor para integrar com o [Windows Notification Services (WNS)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) para enviar por push.

1. No Visual Studio Gerenciador de Soluções, clique com o botão direito do mouse no projeto de aplicativo UWP, clique em **armazenar** > **associar aplicativo à loja...** .

    ![Associar o aplicativo ao Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)

2. No assistente, clique em **Avançar**, entre com seu conta Microsoft, digite um nome para seu aplicativo em **reservar um novo nome de aplicativo**e clique em **reservar**.
3. Depois que o registro do aplicativo for criado com êxito, selecione o novo nome do aplicativo, clique em **Avançar**e, em seguida, clique em **associar**. Isso adiciona as informações de registro Microsoft Store necessárias ao manifesto do aplicativo.
4. Navegue até o [portal de registro de aplicativos](https://apps.dev.microsoft.com/) e entre com sua conta Microsoft. Clique no aplicativo da Windows Store que você associou na etapa anterior.
5. Na página de registro, anote o valor em **segredos do aplicativo** e o SID do **pacote**, que você usará em seguida para configurar o back-end do aplicativo móvel.

    ![Associar o aplicativo ao Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > O segredo de cliente e o SID do pacote são credenciais de segurança importantes. Não partilhe estes valores com ninguém e não os distribua com a aplicação. A **ID do aplicativo** é usada com o segredo para configurar a autenticação da conta da Microsoft.

[App Center](https://docs.microsoft.com/appcenter/sdk/push/uwp#prerequisite---register-your-app-for-windows-notification-services-wns) também tem instruções para configurar aplicativos UWP para notificações por push.

## <a name="configure-the-backend-to-send-push-notifications"></a>Configurar o back-end para enviar notificações por push

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>Atualizar o servidor para enviar notificações por push

Use o procedimento abaixo que corresponda ao tipo de projeto de back-end @ no__t-0either [.net backend](#dotnet) ou o [back-end do node. js](#nodejs).

### <a name="dotnet"></a>Projeto de back-end .NET

1. No Visual Studio, clique com o botão direito do mouse no projeto do servidor e clique em **gerenciar pacotes NuGet**, procure Microsoft. Azure. NotificationHubs e clique em **instalar**. Isso instala a biblioteca de cliente dos hubs de notificação.
2. Expanda **controladores**, abra TodoItemController.cs e adicione as seguintes instruções using:

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. No método **PostTodoItem** , adicione o seguinte código após a chamada para **InsertAsync**:

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

    Esse código informa ao Hub de notificação para enviar uma notificação por push após a inserção de um novo item.

4. Republique o projeto do servidor.

### <a name="nodejs"></a>Projeto de back-end do node. js
1. Configure seu projeto de back-end.
2. Substitua o código existente no arquivo todoitem. js pelo seguinte:

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

    Isso envia uma notificação do sistema WNS que contém o item. Text quando um novo item de tarefas é inserido.

3. Ao editar o arquivo no computador local, Republique o projeto do servidor.

## <a id="update-app"></a>Adicionar notificações por push ao seu aplicativo
Em seguida, seu aplicativo deve se registrar para notificações por push na inicialização. Quando você já tiver habilitado a autenticação, certifique-se de que o usuário entra antes de tentar se registrar para notificações por push.

1. Abra o arquivo de projeto **app.XAML.cs** e adicione as seguintes instruções `using`:

    ```csharp
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    ```

2. No mesmo arquivo, adicione a seguinte definição de método **InitNotificationsAsync** à classe **app** :

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

    Esse código recupera o ChannelURI para o aplicativo do WNS e, em seguida, registra esse ChannelURI com seu aplicativo móvel do serviço de aplicativo.

3. Na parte superior do manipulador de eventos **Onlaunched** no **app.XAML.cs**, adicione o modificador **Async** à definição do método e adicione a chamada a seguir ao novo método **InitNotificationsAsync** , como no exemplo a seguir:

    ```csharp
    protected async override void OnLaunched(LaunchActivatedEventArgs e)
    {
        await InitNotificationsAsync();

        // ...
    }
    ```

    Isso garante que o ChannelURI de curta duração seja registrado toda vez que o aplicativo for iniciado.

4. Reconstrua seu projeto de aplicativo UWP. A aplicação já está pronta para receber notificações de alerta.

## <a id="test"></a>Testar notificações por push em seu aplicativo

[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Passos seguintes

Saiba mais sobre as notificações por push:

* [Como usar o cliente gerenciado para aplicativos móveis do Azure](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications) Os modelos oferecem flexibilidade para enviar envios por push de plataforma cruzada e Pushes localizados. Saiba como registrar modelos.
* [Diagnosticar problemas de notificação por push](../notification-hubs/notification-hubs-push-notification-fixer.md) Há várias razões pelas quais as notificações podem ser descartadas ou não terminam em dispositivos. Este tópico mostra como analisar e descobrir a causa raiz das falhas de notificação por push.

Considere continuar com um dos seguintes tutoriais:

* [Adicione autenticação à sua aplicação](app-service-mobile-windows-store-dotnet-get-started-users.md) Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.
* [Habilitar sincronização offline para seu aplicativo](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Saiba como adicionar suporte offline ao seu aplicativo usando um back-end de aplicativo móvel. A sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel &mdash; visualizar, adicionar ou modificar dados &mdash;, mesmo quando não existe qualquer ligação de rede.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
