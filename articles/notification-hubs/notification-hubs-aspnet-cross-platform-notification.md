---
title: Enviar notificações inter-plataformas para utilizadores com Centros de Notificação Azure (ASP.NET)
description: Saiba como utilizar os modelos de Centros de Notificação para enviar, num único pedido, uma notificação agnóstica de plataforma que vise todas as plataformas.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: thsomasu
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 09/14/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 10/02/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 3f8f9357fff4773467dd93046ece8533514eb444
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92313893"
---
# <a name="send-cross-platform-notifications-with-azure-notification-hubs"></a>Enviar notificações inter-plataformas com os Hubs de Notificação Azure

Este tutorial baseia-se no tutorial anterior, [Enviar notificações a utilizadores específicos utilizando os Hubs de Notificação Azure]. Este tutorial descreve como empurrar notificações para todos os dispositivos que estão registados num determinado utilizador autenticado. Esta abordagem exigia vários pedidos para enviar uma notificação a cada plataforma de clientes suportado. O Azure Notification Hubs suporta modelos, com os quais pode especificar como um dispositivo específico pretende receber notificações. Este método simplifica o envio de notificações inter-plataformas.

Este artigo demonstra como aproveitar os modelos para enviar uma notificação que visa todas as plataformas. Este artigo utiliza um único pedido para enviar uma notificação neutra da plataforma. Para obter informações mais detalhadas sobre os modelos, consulte [a visão geral dos Centros de Notificação][Templates].

> [!IMPORTANT]
> Os projetos Windows Phone 8.1 e anteriores não são suportados no Visual Studio 2019. Para mais informações, consulte [a plataforma visual Studio 2019, visando e compatibilidade.](/visualstudio/releases/2019/compatibility)

> [!NOTE]
> Com os Centros de Notificação, um dispositivo pode registar vários modelos utilizando a mesma etiqueta. Neste caso, uma mensagem de entrada que visa a etiqueta resulta em múltiplas notificações a serem entregues no dispositivo, uma para cada modelo. Este processo permite-lhe exibir a mesma mensagem em várias notificações visuais, como tanto como um crachá como como uma notificação de torradas numa aplicação da Windows Store.

## <a name="send-cross-platform-notifications-using-templates"></a>Enviar notificações de plataforma cruzada usando modelos

Esta secção utiliza o código de amostra que construiu nas notificações enviar a utilizadores específicos utilizando o tutorial [de Azure Notification Hubs.] Você pode [baixar a amostra completa do GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).

Para enviar notificações de plataformas cruzadas utilizando modelos, faça o seguinte:

1. No Estúdio Visual no **Solution Explorer,** expanda a pasta **Controladores** e, em seguida, abra o ficheiro *RegisterController.cs.*

1. Localize o bloco de código no `Put` método que cria um novo registo e, em seguida, substitua o conteúdo pelo seguinte `switch` código:

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

    Este código chama o método específico da plataforma para criar um registo de modelo em vez de um registo nativo. Como os registos de modelos derivam de registos nativos, não é necessário modificar os registos existentes.

1. No **Solution Explorer,** na pasta **Controladores,** abra o ficheiro **NotificationsController.cs.** Substitua o método `Post` pelo código abaixo:

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

    Este código envia uma notificação a todas as plataformas ao mesmo tempo. Não se especifica uma carga nativa. Os Centros de Notificação constroem e entregam a carga útil correta a cada dispositivo com o valor da etiqueta fornecida, conforme especificado nos modelos registados.

1. Reemera o seu projeto Web API.

1. Volte a executar a aplicação do cliente para verificar se o registo foi bem sucedido.

1. Opcionalmente, implementar a aplicação do cliente para um segundo dispositivo e, em seguida, executar a aplicação. Em cada dispositivo é apresentada uma notificação.

## <a name="next-steps"></a>Passos seguintes

Agora que completou este tutorial, saiba mais sobre Os Centros de Notificação e modelos nestes artigos:

* Para um cenário diferente de utilização de modelos, consulte as [notificações Push para dispositivos windows específicos que executam o tutorial de aplicações da Plataforma Universal Windows.][Use Notification Hubs to send breaking news]
* Para obter informações mais detalhadas sobre os modelos, consulte [a visão geral dos Centros de Notificação][Templates].

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Enviar notificações a utilizadores específicos utilizando os Hubs de Notificação do Azure]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hub How to for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)