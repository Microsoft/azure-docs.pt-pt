---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 675ad278cb8bdc0ced4eff3bd77572f44c9808fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68857470"
---
Nesta secção, atualiza o código no seu projeto back-end das Aplicações Móveis existente para enviar uma notificação push sempre que um novo item é adicionado. Este processo é alimentado pela funcionalidade de [modelo](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) dos Hubs de Notificação Azure, que permite empurrões de plataformas cruzadas. Os vários clientes estão registados para notificações push usando modelos, e um único impulso universal pode chegar a todas as plataformas de clientes.

Escolha um dos seguintes procedimentos que corresponda ao seu tipo&mdash;de projeto de back-end ou [.NET back back back](#dotnet) end ou [Node.js back end](#nodejs).

### <a name="net-back-end-project"></a><a name="dotnet"></a>Projeto de back-end .NET

1. No Estúdio Visual, clique no projeto do servidor. Em seguida, selecione **Gerir pacotes NuGet**. Procure, e depois **selecione Instalar**. `Microsoft.Azure.NotificationHubs` Este processo instala a biblioteca Notification Hubs para envio de notificações a partir da parte de trás.
2. No projeto do servidor, os **Controladores abertos** > **TodoItemController.cs**. Em seguida, adicione as seguintes declarações utilizando:

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

    Este processo envia uma notificação de modelo que contém o item. Envie sMS quando um novo item estiver inserido.

4. Republique o projeto do servidor.

### <a name="nodejs-back-end-project"></a><a name="nodejs"></a>Projeto de back-end node.js

1. Prepara o teu projeto de backend.
2. Substitua o código existente em todoitem.js pelo seguinte código:

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

    Este processo envia uma notificação de modelo que contém o item.text quando um novo item é inserido.

3. Quando editar o ficheiro no seu computador local, republique o projeto do servidor.
