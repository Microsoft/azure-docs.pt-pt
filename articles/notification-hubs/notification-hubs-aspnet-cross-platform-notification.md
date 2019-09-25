---
title: Enviar notificações entre plataformas para usuários com hubs de notificação do Azure (ASP.NET)
description: Saiba como usar modelos de hubs de notificação para enviar, em uma única solicitação, uma notificação independente de plataforma que se destina a todas as plataformas.
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
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: cea0d63c20af781fcfc6ba5d7c06061b12992702
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212022"
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Enviar notificações entre plataformas para usuários com hubs de notificação

Em um tutorial anterior, [Notificar usuários com hubs de notificação], você aprendeu a enviar notificações por push para todos os dispositivos que estão registrados para um usuário autenticado específico. Nesse tutorial, foram necessárias várias solicitações para enviar uma notificação para cada plataforma de cliente com suporte. Os hubs de notificação do Azure dão suporte a modelos, com os quais você pode especificar como um dispositivo específico deseja receber notificações. Esse método simplifica o envio de notificações entre plataformas.

Este artigo demonstra como aproveitar os modelos a serem enviados, em uma única solicitação, uma notificação independente de plataforma voltada para todas as plataformas. Para obter informações mais detalhadas sobre modelos, consulte [visão geral dos hubs de notificação do Azure][Templates].

> [!IMPORTANT]
> Windows Phone projetos 8,1 e anteriores não têm suporte no Visual Studio 2017. Para obter mais informações, veja [Visual Studio 2017 Platform Targeting and Compatibility](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) (Segmentação e Compatibilidade de Plataformas do Visual Studio 2017)

> [!NOTE]
> Com os hubs de notificação, um dispositivo pode registrar vários modelos com a mesma marca. Nesse caso, uma mensagem de entrada direcionada à marca resulta em várias notificações entregues ao dispositivo, uma para cada modelo. Esse processo permite que você exiba a mesma mensagem em várias notificações visuais, como um emblema e uma notificação do sistema em um aplicativo da Windows Store.

## <a name="send-cross-platform-notifications-using-templates"></a>Enviar notificações entre plataformas usando modelos

Para enviar notificações entre plataformas usando modelos, faça o seguinte:

1. No Gerenciador de Soluções no Visual Studio, expanda a pasta **controladores** e, em seguida, abra o arquivo RegisterController.cs.

2. Localize o bloco de código no `Put` método que cria um novo registro e, em seguida, substitua o `switch` conteúdo pelo código a seguir:

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

    Esse código chama o método específico da plataforma para criar um registro de modelo em vez de um registro nativo. Como os registros de modelo derivam de registros nativos, você não precisa modificar os registros existentes.

3. No controlador, substitua o `sendNotification` método pelo código a seguir: `Notifications`

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

    Esse código envia uma notificação para todas as plataformas ao mesmo tempo, sem a necessidade de especificar uma carga nativa. Os hubs de notificação criam e entregam a carga correta para cada dispositivo com o valor de *marca* fornecido, conforme especificado nos modelos registrados.

4. Republique seu projeto de back-end WebApi.

5. Execute o aplicativo cliente novamente e verifique se o registro foi bem-sucedido.

6. Adicional Implante o aplicativo cliente em um segundo dispositivo e, em seguida, execute o aplicativo.
    Uma notificação é exibida em cada dispositivo.

## <a name="next-steps"></a>Passos seguintes

Agora que você concluiu este tutorial, saiba mais sobre os hubs de notificação e os modelos nestes tópicos:

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* [Visão geral dos hubs de notificação do Azure][Templates]: Contém informações mais detalhadas sobre modelos.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Notificar usuários com hubs de notificação]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
