---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: c664b089f316255fabc4c8dc36b291d7d63e6280
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66140089"
---
Nesta secção, atualize o código no seu projeto de back-end de aplicações móveis existente para enviar uma notificação push sempre que for adicionado um novo item. Este processo utiliza a tecnologia do [modelo](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) pushes de recurso dos Hubs de notificação do Azure, que permite que várias plataformas. Vários clientes forem registrados para notificações push através de modelos e um único push universal pode obter a todas as plataformas de cliente.

Escolha um dos seguintes procedimentos que corresponde ao seu tipo de projeto de back-end&mdash;ambos [.NET back-end](#dotnet) ou [back-end de node. js](#nodejs).

### <a name="dotnet"></a>Projeto de back-end de .NET

1. No Visual Studio, clique com botão direito do projeto de servidor. Em seguida, selecione **gerir pacotes NuGet**. Procure `Microsoft.Azure.NotificationHubs`e, em seguida, selecione **instalar**. Este processo instala a biblioteca de Hubs de notificação para enviar notificações de back-end.
2. No projeto de servidor, abra **controladores** > **TodoItemController.cs**. Em seguida, adicione as seguintes instruções "using":

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

    Este processo envia uma notificação de modelo que contém o item. Texto quando um novo item é inserido.

4. Voltar a publicar o projeto de servidor.

### <a name="nodejs"></a>Projeto de back-end de node. js

1. Se ainda não fez isso, [transfira o projeto de back-end de início rápido](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart), ou utilize outra a [editor online no portal do Azure](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Substitua o código existente no todoitem.js com o código a seguir:

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

    Este processo envia uma notificação de modelo que contém o item.text Quando um novo item é inserido.

3. Ao editar o ficheiro no seu computador local, voltar a publicar o projeto de servidor.
