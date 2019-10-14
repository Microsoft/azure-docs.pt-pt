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
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 8f4de88ed79ee802866579448681cfe6cee3e654
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293424"
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Enviar notificações entre plataformas para usuários com hubs de notificação

Este tutorial se baseia no tutorial anterior, [Enviar notificações para usuários específicos usando os hubs de notificação do Azure]. Esse tutorial descreve como enviar notificações por push para todos os dispositivos registrados para um usuário autenticado específico. Essa abordagem exigiu várias solicitações para enviar uma notificação para cada plataforma de cliente com suporte. Os hubs de notificação do Azure dão suporte a modelos, com os quais você pode especificar como um dispositivo específico deseja receber notificações. Esse método simplifica o envio de notificações entre plataformas.

Este artigo demonstra como aproveitar os modelos para enviar uma notificação direcionada a todas as plataformas. Este artigo usa uma única solicitação para enviar uma notificação neutra de plataforma. Para obter informações mais detalhadas sobre modelos, consulte [visão geral dos hubs de notificação][Templates].

> [!IMPORTANT]
> Windows Phone projetos 8,1 e anteriores não têm suporte no Visual Studio 2019. Para obter mais informações, consulte [direcionamento e compatibilidade da plataforma Visual Studio 2019](/visualstudio/releases/2019/compatibility).

> [!NOTE]
> Com os hubs de notificação, um dispositivo pode registrar vários modelos usando a mesma marca. Nesse caso, uma mensagem de entrada direcionada à marca resulta em várias notificações entregues ao dispositivo, uma para cada modelo. Esse processo permite que você exiba a mesma mensagem em várias notificações visuais, como um emblema e uma notificação do sistema em um aplicativo da Windows Store.

## <a name="send-cross-platform-notifications-using-templates"></a>Enviar notificações entre plataformas usando modelos

Esta seção usa o código de exemplo que você criou no tutorial [Enviar notificações para usuários específicos usando os hubs de notificação do Azure] . Pode transferir o exemplo do [GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).

Para enviar notificações entre plataformas usando modelos, execute as seguintes etapas:

1. No Visual Studio no **Gerenciador de soluções**, expanda a pasta **controladores** e, em seguida, abra o arquivo *RegisterController.cs* .

1. Localize o bloco de código no método `Put` que cria um novo registro e substitua o conteúdo de `switch` pelo código a seguir:

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

1. No **Gerenciador de soluções**, na pasta **controladores** , abra o arquivo *NotificationsController.cs* . Substitua o método `Post` pelo código abaixo:

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

    Esse código envia uma notificação para todas as plataformas ao mesmo tempo. Você não especifica uma carga nativa. Os hubs de notificação criam e entregam a carga correta para cada dispositivo com o valor de marca fornecido, conforme especificado nos modelos registrados.

1. Republique seu projeto de API Web.

1. Execute o aplicativo cliente novamente para verificar se o registro foi bem-sucedido.

1. Opcionalmente, implante o aplicativo cliente em um segundo dispositivo e, em seguida, execute o aplicativo. Uma notificação é exibida em cada dispositivo.

## <a name="next-steps"></a>Passos seguintes

Agora que você concluiu este tutorial, saiba mais sobre os hubs de notificação e os modelos nestes artigos:

* Para um cenário diferente para usar modelos, consulte o tutorial [notificações por push para dispositivos Windows em execução plataforma universal do Windows aplicativos específicos][Use Notification Hubs to send breaking news] .
* Para obter informações mais detalhadas sobre modelos, consulte [visão geral dos hubs de notificação][Templates].

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Enviar notificações para usuários específicos usando os hubs de notificação do Azure]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
