---
title: Envie notificações inter-plataformas aos utilizadores com Hubs de Notificação Azure (ASP.NET)
description: Saiba como usar os modelos de Centros de Notificação para enviar, num único pedido, uma notificação agnóstica de plataforma que visa todas as plataformas.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: fc3cec348517244c8a7f54d2d3d17298c58e4262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127037"
---
# <a name="send-cross-platform-notifications-with-azure-notification-hubs"></a>Envie notificações transversais com hubs de notificação do Azure

Este tutorial baseia-se no tutorial anterior, [Enviar notificações a utilizadores específicos utilizando hubs de notificação do Azure.] Este tutorial descreve como empurrar notificações a todos os dispositivos que estão registados num utilizador autenticado específico. Esta abordagem exigia vários pedidos para enviar uma notificação a cada plataforma de clientes suportada. O Azure Notification Hubs suporta modelos, com os quais pode especificar como um dispositivo específico quer receber notificações. Este método simplifica o envio de notificações interplataforma.

Este artigo demonstra como aproveitar os modelos para enviar uma notificação que visa todas as plataformas. Este artigo utiliza um único pedido para enviar uma notificação neutra da plataforma. Para obter informações mais detalhadas sobre os modelos, consulte a visão geral dos Centros de [Notificação][Templates].

> [!IMPORTANT]
> Os projetos windows Phone 8.1 e anteriores não são suportados no Visual Studio 2019. Para mais informações, consulte [Visual Studio 2019 Platform Targeting and Compatibilidade](/visualstudio/releases/2019/compatibility).

> [!NOTE]
> Com Centros de Notificação, um dispositivo pode registar vários modelos utilizando a mesma etiqueta. Neste caso, uma mensagem de entrada que visa a etiqueta resulta em múltiplas notificações a serem entregues no dispositivo, uma para cada modelo. Este processo permite-lhe exibir a mesma mensagem em múltiplas notificações visuais, como um crachá e como uma notificação de torradas numa aplicação da Windows Store.

## <a name="send-cross-platform-notifications-using-templates"></a>Enviar notificações cruzadas usando modelos

Esta secção utiliza o código de amostra que construiu no Envio de notificações a utilizadores específicos utilizando o tutorial [do Azure Notification Hubs.] Pode transferir o exemplo do [GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).

Para enviar notificações transversais utilizando modelos, faça os seguintes passos:

1. No Visual Studio in **Solution Explorer,** expanda a pasta **Controladores** e abra o ficheiro *RegisterController.cs.*

1. Localize o bloco `Put` de código no método que cria `switch` um novo registo e, em seguida, substitua o conteúdo pelo seguinte código:

    ```csharp
    switch (deviceUpdate.Platform)
    {
        case "mpns":
            var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>$(message)</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
            registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "wns":
            toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
            registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "apns":
            var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
            registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
            break;
        case "fcm":
            var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
            registration = new FcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    Este código chama o método específico da plataforma para criar um registo de modelo em vez de um registo nativo. Como as inscrições de modelo saem de registos nativos, não precisa de modificar os registos existentes.

1. No **Solution Explorer**, na pasta **Controladores,** abra o ficheiro *NotificationsController.cs.* Substitua o método `Post` pelo código abaixo:

    ```csharp
    public async Task<HttpResponseMessage> Post()
    {
        var user = HttpContext.Current.User.Identity.Name;
        var userTag = "username:" + user;

        var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
        await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

    Este código envia uma notificação a todas as plataformas ao mesmo tempo. Não se especifica uma carga nativa. O Notification Hubs constrói e entrega a carga útil correta a todos os dispositivos com o valor de etiqueta fornecido, conforme especificado nos modelos registados.

1. Republique o seu projeto Web API.

1. Executar novamente a aplicação do cliente para verificar se o registo foi bem sucedido.

1. Opcionalmente, implemente a aplicação do cliente para um segundo dispositivo e, em seguida, executar a aplicação. É apresentada uma notificação em cada dispositivo.

## <a name="next-steps"></a>Passos seguintes

Agora que completou este tutorial, saiba mais sobre Centros de Notificação e modelos nestes artigos:

* Para um cenário diferente para utilizar modelos, consulte as [notificações push para dispositivos windows específicos que executam][Use Notification Hubs to send breaking news] o tutorial de aplicações da Plataforma Universal Windows.
* Para obter informações mais detalhadas sobre os modelos, consulte a visão geral dos Centros de [Notificação][Templates].

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Enviar notificações a utilizadores específicos utilizando hubs de notificação do Azure]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
