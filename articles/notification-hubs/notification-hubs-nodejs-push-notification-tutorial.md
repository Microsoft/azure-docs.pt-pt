---
title: Enviar notificações push com Notification Hubs do Azure e node. js
description: Saiba como utilizar os Notification Hubs para enviar notificações push a partir de uma aplicação node. js.
keywords: notificação push, push notifications,node.js push, push de ios
services: notification-hubs
documentationcenter: nodejs
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: d90f23f52ca4c0cce3d853114acf673aa085d3c5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57889793"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Enviar notificações push com Notification Hubs do Azure e node. js

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Descrição geral

> [!IMPORTANT]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, criar uma conta de avaliação gratuita em apenas alguns minutos, através da [avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

Este guia mostra-lhe como enviar notificações push com a ajuda dos Hubs de notificação do Azure diretamente a partir de um [node. js](https://nodejs.org) aplicação.

Os cenários abrangidos incluem o envio de notificações push para aplicações nas seguintes plataformas:

- Android
- iOS
- Plataforma Universal do Windows
- Windows Phone

## <a name="notification-hubs"></a>Hubs de Notificação

Os Hubs de notificação do Azure fornecem uma infraestrutura de fácil de usar, várias plataforma e escalável para enviar notificações push para dispositivos móveis. Para obter detalhes sobre a infraestrutura de serviço, consulte a [os Hubs de notificação do Azure](https://msdn.microsoft.com/library/windowsazure/jj927170.aspx) página.

## <a name="create-a-nodejs-application"></a>Criar uma aplicação node. js

Neste tutorial, a primeira etapa é criar uma nova aplicação node. js em branco. Para obter instruções sobre como criar uma aplicação node. js, consulte [criar e implementar uma aplicação node. js para o Site de Web do Azure][nodejswebsite], [serviço de nuvem de node. js] [ Node.js Cloud Service] com o Windows PowerShell, ou [Web Site com o WebMatrix][webmatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Configurar a sua aplicação para utilizar os Hubs de notificação

Para utilizar os Hubs de notificação do Azure, terá de transferir e utilizar o node. js [pacote do azure](https://www.npmjs.com/package/azure), que inclui um conjunto interno de bibliotecas de programa auxiliar que comunicam com os serviços de REST de notificação push.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilize o nó Package Manager (NPM) para obter o pacote

1. Utilize uma interface de linha de comandos, como **PowerShell** (Windows), **Terminal** (Mac), ou **Bash** (Linux) e navegue para a pasta onde criou a sua aplicação em branco.
2. Executar `npm install azure-sb` na janela de comando.
3. Pode executar manualmente a `ls` ou `dir` comando para verificar se um `node_modules` pasta foi criada.
4. Dentro dessa pasta, localize a **azure** pacote, que contém as bibliotecas que precisa acessar o Hub de notificação.

> [!NOTE]
> Pode saber mais sobre a instalação de NPM no oficial [NPM blog](https://blog.npmjs.org/post/85484771375/how-to-install-npm).

### <a name="import-the-module"></a>Importe o módulo

Com um editor de texto, adicione o seguinte na parte superior do `server.js` arquivo do aplicativo:

    ```javascript
    var azure = require('azure');
    ```

### <a name="set-up-an-azure-notification-hub-connection"></a>Configurar uma ligação do Hub de notificação do Azure

O `NotificationHubService` objeto permite-lhe trabalhar com os hubs de notificação. O código seguinte cria um `NotificationHubService` objeto para o hub de notificação com o nome `hubname`. Adicione-o junto à parte superior do `server.js` arquivo, após a instrução para importar o módulo do azure:

    ```javascript
    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
    ```

Obter a ligação `connectionstring` valor a partir de [portal do Azure] , efetuando os seguintes passos:

1. No painel de navegação esquerdo, clique em **procurar**.
2. Selecione **os Hubs de notificação**e, em seguida, localize o hub de que pretende utilizar para o exemplo. Pode consultar o [Windows Store introdução ao tutorial](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) se precisar de ajuda a criar um novo Notification Hub.
3. Selecione **definições**.
4. Clique em **políticas de acesso**. Ver as duas cadeias de ligação de acesso partilhado e completa.

![Portal do Azure – os Hubs de notificação](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Também pode obter a cadeia de ligação utilizando o **Get-AzureSbNamespace** cmdlet fornecido pelo [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou o **show de espaço de nomes do sb do azure** comando com o [Interface de linha de comandos do azure (CLI do Azure)](../cli-install-nodejs.md).

## <a name="general-architecture"></a>Arquitetura geral

O `NotificationHubService` objeto expõe as seguintes instâncias de objeto para enviar notificações push para aplicações e dispositivos específicos:

- **Android** -utilize o `GcmService` objeto, que está disponível em `notificationHubService.gcm`
- **iOS** -utilize o `ApnsService` objeto, o que é acessível em `notificationHubService.apns`
- **Windows Phone** -utilize o `MpnsService` objeto, que está disponível em `notificationHubService.mpns`
- **Plataforma universal do Windows** -utilize o `WnsService` objeto, que está disponível em `notificationHubService.wns`

### <a name="how-to-send-push-notifications-to-android-applications"></a>Como: Enviar notificações push para aplicações Android

O `GcmService` objeto fornece um `send` método que pode ser utilizado para enviar notificações push para aplicações Android. O `send` método aceita os seguintes parâmetros:

- **Etiquetas** -o identificador de marca. Se nenhuma etiqueta for fornecida, a notificação é enviada a todos os clientes.
- **Payload** -a mensagem JSON ou payload de cadeia de caracteres bruta.
- **Retorno de chamada** -a função de retorno de chamada.

Para obter mais informações sobre o formato do payload, consulte a **Payload** secção a [implementação de servidor do GCM](https://developer.android.com/google/gcm/server.html#payload) documento.

O seguinte código utiliza a `GcmService` instância exposto pelo `NotificationHubService` para enviar uma notificação push para todos os clientes registrados.

    ```javascript
    var payload = {
      data: {
        message: 'Hello!'
      }
    };
    notificationHubService.gcm.send(null, payload, function(error){
      if(!error){
        //notification sent
      }
    });
    ```

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Como: Enviar notificações push para aplicações iOS

Mesmo que com as aplicações Android descritas acima, o `ApnsService` objeto fornece um `send` método que pode ser utilizado para enviar notificações push para aplicações iOS. O `send` método aceita os seguintes parâmetros:

- **Etiquetas** -o identificador de marca. Se nenhuma etiqueta for fornecida, a notificação é enviada a todos os clientes.
- **Payload** -payload JSON ou cadeia de caracteres da mensagem.
- **Retorno de chamada** -a função de retorno de chamada.

Para obter mais informações o formato do payload, consulte a **Payload de notificação** secção a [guia de programação de notificação de Push e Local](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) documento.

O seguinte código utiliza a `ApnsService` instância exposto pelo `NotificationHubService` para enviar uma mensagem de alerta para todos os clientes:

    ```javascript
    var payload={
        alert: 'Hello!'
      };
    notificationHubService.apns.send(null, payload, function(error){
      if(!error){
         // notification sent
      }
    });
    ```

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Como: Enviar notificações push para aplicações do Windows Phone

O `MpnsService` objeto fornece um `send` método que pode ser utilizado para enviar notificações push para aplicações do Windows Phone. O `send` método aceita os seguintes parâmetros:

- **Etiquetas** -o identificador de marca. Se nenhuma etiqueta for fornecida, a notificação é enviada a todos os clientes.
- **Payload** -payload do XML da mensagem.
- **TargetName**  -  `toast` para notificações de alerta. `token` Para obter notificações de mosaico.
- **NotificationClass** -a prioridade da notificação. Consulte a **elementos de cabeçalho de HTTP** secção a [notificações Push a partir de um servidor](https://msdn.microsoft.com/library/hh221551.aspx) documento para obter valores válidos.
- **Opções de** - opcional cabeçalhos de pedido.
- **Retorno de chamada** -a função de retorno de chamada.

Para obter uma lista de válido `TargetName`, `NotificationClass` e opções de cabeçalho, confira o [notificações Push a partir de um servidor](https://msdn.microsoft.com/library/hh221551.aspx) página.

O seguinte exemplo de código utiliza a `MpnsService` instância exposto pelo `NotificationHubService` para enviar uma notificação push de alerta:

    ```javascript
    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });
    ```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Como: Enviar notificações push para aplicações de plataforma Universal do Windows (UWP)

O `WnsService` objeto fornece um `send` método que pode ser utilizado para enviar notificações push para aplicações de plataforma Universal do Windows.  O `send` método aceita os seguintes parâmetros:

- **Etiquetas** -o identificador de marca. Se nenhuma etiqueta for fornecida, a notificação é enviada para todos os clientes registrados.
- **Payload** -o payload XML de mensagem.
- **Tipo de** -o tipo de notificação.
- **Opções de** - opcional cabeçalhos de pedido.
- **Retorno de chamada** -a função de retorno de chamada.

Para obter uma lista de tipos válidos e cabeçalhos de pedido, consulte [cabeçalhos de solicitação e resposta do serviço de notificação de Push](https://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

O seguinte código utiliza a `WnsService` instância exposto pelo `NotificationHubService` para enviar uma alerta de notificação push para uma aplicação UWP:

    ```javascript
    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
         // notification sent
      }
    });
    ```

## <a name="next-steps"></a>Próximos Passos

Os trechos de código de exemplo acima permitem-lhe criar facilmente a infraestrutura de serviço para enviar notificações push para uma grande variedade de dispositivos. Agora que aprendeu as noções básicas de como utilizar os Hubs de notificação com node. js, siga estas ligações para saber mais sobre como é possível estender ainda mais esses recursos.

- Consulte a referência do MSDN para [os Hubs de notificação do Azure](https://msdn.microsoft.com/library/azure/jj927170.aspx).
- Visite o [Azure SDK para o nó] repositório no GitHub para obter mais exemplos e detalhes de implementação.

[Azure SDK para o nó]: https://github.com/WindowsAzure/azure-sdk-for-node
[Next Steps]: #nextsteps
[What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
[Create a Service Namespace]: #create-a-service-namespace
[Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
[Create a Node.js Application]: #Create_a_Nodejs_Application
[Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
[How to: Create a Topic]: #How_to_Create_a_Topic
[How to: Create Subscriptions]: #How_to_Create_Subscriptions
[How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
[How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
[How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
[How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
[1]: #Next_Steps
[Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
[image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
[2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
[3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
[4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
[5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Azure Service Bus Notification Hubs]: https://msdn.microsoft.com/library/windowsazure/jj927170.aspx
[SqlFilter]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
[Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs
[webmatrix]: https://docs.microsoft.com/aspnet/web-pages/videos/introduction/create-a-website-using-webmatrix
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Portal do Azure]: https://portal.azure.com
