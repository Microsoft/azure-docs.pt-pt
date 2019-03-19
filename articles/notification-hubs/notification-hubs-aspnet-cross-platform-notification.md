---
title: Enviar notificações multiplataformas para os utilizadores com os Hubs de notificação do Azure (ASP.NET)
description: Saiba como utilizar modelos dos Hubs de notificação para enviar, numa única solicitação, uma notificação de independente de plataforma que se destina a todas as plataformas.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 0f92b49c9d77029a9624782b49eb23f7083c49aa
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863105"
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Enviar notificações multiplataformas para os utilizadores com os Hubs de notificação

Num tutorial anterior, [notificar os utilizadores com os Hubs de notificação], ficou a saber como enviar notificações push para todos os dispositivos que estão registados para um utilizador autenticado específico. Esse tutorial, vários pedidos eram necessário para enviar uma notificação para cada plataforma de cliente suportada. Os Hubs de notificação do Azure suporta modelos, com a qual pode especificar como quer receber notificações de um dispositivo específico. Esse método simplifica o envio de notificações para várias plataformas.

Este artigo demonstra como tirar proveito dos modelos para enviar, numa única solicitação, uma notificação de independente de plataforma que se destina a todas as plataformas. Para obter mais informações sobre modelos, consulte [descrição geral de Hubs de notificação do Azure][Templates].

> [!IMPORTANT]
> Projetos do Windows Phone 8.1 e anteriores não são suportados no Visual Studio 2017. Para obter mais informações, veja [Visual Studio 2017 Platform Targeting and Compatibility](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) (Segmentação e Compatibilidade de Plataformas do Visual Studio 2017)

> [!NOTE]
> Com os Hubs de notificação, um dispositivo pode registrar vários modelos com a mesma etiqueta. Neste caso, uma mensagem de entrada que tenha como destino os resultados de marca em várias notificações fornecido ao dispositivo, um para cada modelo. Este processo permite-lhe apresentar a mesma mensagem em várias notificações visual, por exemplo, ambos como um destaque e como uma notificação de alerta num aplicativo da Windows Store.

## <a name="send-cross-platform-notifications-using-templates"></a>Enviar notificações para várias plataformas através de modelos

Para enviar notificações entre plataformas com os modelos, faça o seguinte:

1. No Explorador de soluções no Visual Studio, expanda o **controladores** e, em seguida, abra o ficheiro de RegisterController.cs.

2. Localize o bloco de código a `Put` método que cria um novo registo e, em seguida, substitua o `switch` conteúdo com o código a seguir:

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

    Esse código chama o método de específicas da plataforma para criar um registo de modelo em vez de um registo nativo. Porque os registos de modelos derivam de registos nativos, não precisa de modificar registos existentes.

3. Na `Notifications` controlador, substitua o `sendNotification` método com o código a seguir:

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

    Este código envia uma notificação para todas as plataformas, ao mesmo tempo, sem tiver de especificar um payload nativo. Notification Hubs baseia-se e oferece o payload correto para todos os dispositivos com o fornecido *marca* valor, conforme especificado nos modelos de registados.

4. Voltar a publicar o projeto de back-end de WebApi.

5. Execute novamente a aplicação de cliente e, em seguida, certifique-se de que o registo foi concluída com êxito.

6. (Opcional) Implementar a aplicação de cliente para um segundo dispositivo e, em seguida, execute a aplicação.
    É apresentada uma notificação em cada dispositivo.

## <a name="next-steps"></a>Passos Seguintes

Agora que concluiu este tutorial, saiba mais sobre os Hubs de notificação e modelos nos seguintes tópicos:

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* [Descrição geral dos Hubs de notificação do Azure][Templates]: Contém informações mais detalhadas sobre os modelos.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Notificar os utilizadores com os Hubs de notificação]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
