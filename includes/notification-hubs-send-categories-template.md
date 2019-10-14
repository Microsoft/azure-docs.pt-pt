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
ms.openlocfilehash: 299f92484000cb5c59291a5af87f24d89a771fee
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296720"
---
Nesta secção, irá enviar notícias de última hora como notificações de modelo etiquetadas a partir da aplicação de consola .NET.

1. No Visual Studio, crie uma nova aplicação da consola Visual C#
    1. No menu, selecione **arquivo** > **novo** **projeto** > .
    1. Em **criar um novo projeto**, selecione **aplicativo de console (.NET Framework)** para C# na lista de modelos e selecione **Avançar**.
    1. Insira um nome para o aplicativo.
    1. Para **solução**, escolha **Adicionar à solução**e selecione **criar** para criar o projeto.

1. Selecione **ferramentas** > **Gerenciador de pacotes NuGet** > **console do Gerenciador de pacotes** e, em seguida, na janela do console, execute o seguinte comando:

   ```powershell
   Install-Package Microsoft.Azure.NotificationHubs
   ```

   Essa ação adiciona uma referência ao SDK dos hubs de notificação do Azure usando o pacote [Microsoft. Azure. NotificationHubs] .

1. Abra o arquivo *Program.cs* e adicione a seguinte instrução `using`:

   ```csharp
   using Microsoft.Azure.NotificationHubs;
   ```

1. Na classe `Program`, adicione o seguinte método ou substitua-o se já existir:

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

1. No código anterior, substitua os marcadores de posição `<hub name>` e `<connection string with full access>` pelo nome do seu hub de notificação e pela cadeia de ligação para *DefaultFullSharedAccessSignature* do dashboard do seu hub de notificação.

1. No método `Main()`, adicione as seguintes linhas:

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
[Microsoft. Azure. NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
