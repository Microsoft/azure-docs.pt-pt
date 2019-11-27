---
title: incluir ficheiro
description: incluir ficheiro
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 11/07/2019
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 520a0b4ec42b9a32fbd30c28c7ce311b5445f23d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74260843"
---
Ao enviar notificações de modelo, você só precisa fornecer um conjunto de propriedades. Nesse cenário, o conjunto de propriedades contém a versão localizada das notícias atuais.

```json
{
    "News_English": "World News in English!",
    "News_French": "World News in French!",
    "News_Mandarin": "World News in Mandarin!"
}
```

### <a name="send-notifications-using-a-c-console-app"></a>Enviar notificações usando um C# aplicativo de console

Esta seção mostra como enviar notificações usando um aplicativo de console. O código transmite notificações para dispositivos Windows Store e iOS. Modifique o método `SendTemplateNotificationAsync` na aplicação de consola que criou anteriormente com o seguinte código:

```csharp
private static async void SendTemplateNotificationAsync()
{
    // Define the notification hub.
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(
            "<connection string with full access>", "<hub name>");

    // Apple requires the apns-push-type header for all requests
    var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};

    // Sending the notification as a template notification. All template registrations that contain 
    // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
    // This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string, string> templateParams = new Dictionary<string, string>();

    // Create an array of breaking news categories.
    var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
    var locales = new string[] { "English", "French", "Mandarin" };

    foreach (var category in categories)
    {
        templateParams["messageParam"] = "Breaking " + category + " News!";

        // Sending localized News for each tag too...
        foreach( var locale in locales)
        {
            string key = "News_" + locale;

            // Your real localized news content would go here.
            templateParams[key] = "Breaking " + category + " News in " + locale + "!";
        }

        await hub.SendTemplateNotificationAsync(templateParams, category);
    }
}
```

O método SendTemplateNotificationAsync entrega a parte de notícias localizada para **todos os** seus dispositivos, independentemente da plataforma. O Hub de notificação cria e entrega a carga nativa correta para todos os dispositivos inscritos em uma marca específica.

### <a name="sending-notification-with-mobile-services"></a>Enviando notificação com os serviços móveis

No Agendador de serviços móveis, use o seguinte script:

```csharp
var azure = require('azure');
var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
var notification = {
        "News_English": "World News in English!",
        "News_French": "World News in French!",
        "News_Mandarin", "World News in Mandarin!"
}
notificationHubService.send('World', notification, function(error) {
    if (!error) {
        console.warn("Notification successful");
    }
});
```
