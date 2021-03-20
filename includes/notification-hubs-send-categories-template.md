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
ms.openlocfilehash: bfd5d42d83046c9c5b0bc3a78fabec08da5da646
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96028349"
---
Nesta secção, irá enviar notícias de última hora como notificações de modelo etiquetadas a partir da aplicação de consola .NET.

1. No Visual Studio, crie uma nova aplicação da consola Visual C#
    1. No menu, selecione **File**  >  **New**  >  **Project**.
    1. In **Create a new project**, selecione Console App **(.NET Framework)** para C# na lista de modelos e selecione **Next**.
    1. Insira um nome para a aplicação.
    1. Para **solução,** escolha **Adicionar à solução,** e selecione **Criar** para criar o projeto.

1. Selecione **ferramentas**  >  **NuGet Package Manager** Package Manager  >  **Consola** e, em seguida, na janela da consola, executar o seguinte comando:

   ```powershell
   Install-Package Microsoft.Azure.NotificationHubs
   ```

   Esta ação adiciona uma referência ao Azure Notification Hubs SDK utilizando o pacote [Microsoft.Azure.NotificationHubs.]

1. Abra o ficheiro *.cs Programa* e adicione a seguinte `using` declaração:

   ```csharp
   using Microsoft.Azure.NotificationHubs;
   ```

1. Na classe `Program`, adicione o seguinte método ou substitua-o se já existir:

    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Apple requires the apns-push-type header for all requests
        var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM/FCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```

   Este código envia uma notificação de modelo para cada uma das seis etiquetas na matriz de cadeias. A utilização de etiquetas assegura que os dispositivos recebem notificações apenas para as categorias registadas.

1. No código anterior, substitua os marcadores de posição `<hub name>` e `<connection string with full access>` pelo nome do seu hub de notificação e pela cadeia de ligação para *DefaultFullSharedAccessSignature* do dashboard do seu hub de notificação.

1. No `Main()` método, adicione as seguintes linhas:

   ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

1. Crie a aplicação de consola.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: /previous-versions/azure/reference/dn223264(v=azure.100)
[Add push notifications for Mobile Apps]: /previous-versions/azure/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/