---
title: incluir ficheiro
description: incluir ficheiro
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/30/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ce8496596f25b85719b8a6dff849ebf0fc3e5dc3
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58405606"
---
Nesta secção, irá enviar notícias de última hora como notificações de modelo etiquetadas a partir da aplicação de consola .NET. 

1. No Visual Studio, crie um novo elemento Visual C# aplicação de consola: um. No menu, selecione **arquivo** > **New** > **projeto**.
    b. Expanda **Visual C#** e selecione **ambiente de trabalho do Windows**. 
    c. Selecione **aplicação de consola (.NET Framework)** na lista de modelos. 
    d. Introduza um **nome** para a aplicação. 
    e. Selecione um **pasta** para a aplicação.
    f. Selecione **OK** para criar o projeto. 
2. No menu principal do Visual Studio, selecione **ferramentas** > **Gestor de pacotes NuGet** > **Package Manager Console** e, em seguida, na janela da consola, Introduza a seguinte cadeia:
   
    ```
    Install-Package Microsoft.Azure.NotificationHubs
    ```
   
3. Prima **Enter**.  
    Esta ação adiciona uma referência ao SDK dos Hubs de Notificação do Azure mediante a utilização do [Pacote NuGet Microsoft.Azure.Notification Hubs].

4. Abra o ficheiro Program.cs e adicione a seguinte instrução `using`:
   
    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

5. Na classe `Program`, adicione o seguinte método ou substitua-o se já existir:
   
    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```   
   
    Este código envia uma notificação de modelo para cada uma das seis etiquetas na matriz de cadeias. A utilização de etiquetas assegura que os dispositivos recebem notificações apenas para as categorias registadas.

5. No código anterior, substitua os marcadores de posição `<hub name>` e `<connection string with full access>` pelo nome do seu hub de notificação e pela cadeia de ligação para *DefaultFullSharedAccessSignature* do dashboard do seu hub de notificação.

6. No método **Principal**, adicione as linhas seguintes:
   
    ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

7. Crie a aplicação de consola.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Pacote NuGet Microsoft.Azure.Notification Hubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
