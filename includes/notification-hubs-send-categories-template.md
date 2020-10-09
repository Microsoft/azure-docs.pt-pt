---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 11/07/2019
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 48907713082ebb1008ad963121671b36af7f2731
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74228160"
---
Nesta secção, irá enviar notícias de última hora como notificações de modelo etiquetadas a partir da aplicação de consola .NET.

1. No Visual Studio, crie uma nova aplicação da consola Visual C#
    1. No menu, selecione **File**  >  **New**  >  **Project**.
    1. In **Create a new project**, selecione Console App **(.NET Framework)** para C# na lista de modelos e selecione **Next**.
    1. Insira um nome para a aplicação.
    1. Para **solução,** escolha **Adicionar à solução,** e selecione **Criar** para criar o projeto.

1. Selecione **ferramentas**  >  **NuGet Package Manager**Package Manager  >  **Consola** e, em seguida, na janela da consola, executar o seguinte comando:

   ```powershell
   Install-Package Microsoft.Azure.NotificationHubs
   ```

   Esta ação adiciona uma referência ao Azure Notification Hubs SDK utilizando o pacote [Microsoft.Azure.NotificationHubs.]

1. Abra o ficheiro *Program.cs* e adicione a seguinte `using` declaração:

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
[Notification Hubs REST interface]: https://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
