---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 675ad278cb8bdc0ced4eff3bd77572f44c9808fc
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857470"
---
Nesta seção, você atualiza o código em seu projeto de back-end de aplicativos móveis existente para enviar uma notificação por push sempre que um novo item for adicionado. Esse processo é alimentado pelo recurso de [modelo](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) dos hubs de notificação do Azure, que permite envios por push de plataforma cruzada. Os vários clientes são registrados para notificações por push usando modelos e um único Push universal pode chegar a todas as plataformas de cliente.

Escolha um dos procedimentos a seguir que corresponda ao seu tipo&mdash;de projeto de back-end ou back-end do [node. js](#nodejs)do [.net](#dotnet) .

### <a name="dotnet"></a>Projeto de back-end do .NET

1. No Visual Studio, clique com o botão direito do mouse no projeto do servidor. Em seguida, selecione **gerenciar pacotes NuGet**. Procure e selecione **instalar.** `Microsoft.Azure.NotificationHubs` Esse processo instala a biblioteca de hubs de notificação para enviar notificações do back-end.
2. No projeto do servidor, abra **controladores** > **TodoItemController.cs**. Em seguida, adicione as seguintes instruções using:

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

    // Get the Notification Hubs credentials for the mobile app.
    string notificationHubName = settings.NotificationHubName;
    string notificationHubConnection = settings
        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

    // Create a new Notification Hub client.
    NotificationHubClient hub = NotificationHubClient
    .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

    // Send the message so that all template registrations that contain "messageParam"
    // receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string,string> templateParams = new Dictionary<string,string>();
    templateParams["messageParam"] = item.Text + " was added to the list.";

    try
    {
        // Send the push notification and log the results.
        var result = await hub.SendTemplateNotificationAsync(templateParams);

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

    Esse processo envia uma notificação de modelo que contém o item. Texto quando um novo item é inserido.

4. Republique o projeto do servidor.

### <a name="nodejs"></a>Projeto de back-end do node. js

1. Configure seu projeto de back-end.
2. Substitua o código existente em todoitem. js pelo seguinte código:

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see https://aka.ms/nodejshubs.
    logger.info('Running TodoItem.insert');

    // Define the template payload.
    var payload = '{"messageParam": "' + context.item.text + '" }';  

    // Execute the insert. The insert returns the results as a promise.
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured.
            if (context.push) {
                // Send a template notification.
                context.push.send(null, payload, function (error) {
                    if (error) {
                        logger.error('Error while sending push notification: ', error);
                    } else {
                        logger.info('Push notification sent successfully!');
                    }
                });
            }
            // Don't forget to return the results from the context.execute().
            return results;
        })
        .catch(function (error) {
            logger.error('Error while running context.execute: ', error);
        });
    });

    module.exports = table;  
    ```

    Esse processo envia uma notificação de modelo que contém o item. Text quando um novo item é inserido.

3. Quando você editar o arquivo no computador local, Republique o projeto do servidor.
