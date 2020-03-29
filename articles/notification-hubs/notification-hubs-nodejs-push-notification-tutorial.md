---
title: Envio de notificações push com Hubs de Notificação Azure e Node.js
description: Saiba como utilizar os Centros de Notificação para enviar notificações push a partir de uma aplicação Node.js.
keywords: notificação push,push notificações,node.js push,ios push
services: notification-hubs
documentationcenter: nodejs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 6e109c5a7f4911893c81c88ae84322fb962fff6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71213186"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Envio de notificações push com Hubs de Notificação Azure e Node.js

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Descrição geral

> [!IMPORTANT]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, crie uma conta de teste gratuita em apenas alguns minutos através do [Teste Livre de Azure.](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs)

Este guia mostra-lhe como enviar notificações push com a ajuda de Hubs de Notificação Azure diretamente de uma aplicação [Node.js.](https://nodejs.org)

Os cenários abrangidos incluem o envio de notificações push para aplicações nas seguintes plataformas:

- Android
- iOS
- Plataforma Universal do Windows
- Windows Phone

## <a name="notification-hubs"></a>Hubs de Notificação

Os Hubs de Notificação Azure fornecem uma infraestrutura de fácil utilização, multiplataforma e escalável para o envio de notificações push para dispositivos móveis. Para mais informações sobre a infraestrutura de serviço, consulte a página Hubs de [Notificação Do Azure.](https://msdn.microsoft.com/library/windowsazure/jj927170.aspx)

## <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js

O primeiro passo neste tutorial é criar uma nova aplicação node.js em branco. Para obter instruções sobre a criação de uma aplicação Node.js, consulte [Criar e implementar uma aplicação Node.js para o Web Site do Azure,][nodejswebsite] [Node.js Cloud Service][Node.js Cloud Service] utilizando o Windows PowerShell ou Web Site com [WebMatrix][webmatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Configure a sua aplicação para usar centros de notificação

Para utilizar os Hubs de Notificação Azure, é necessário descarregar e utilizar o pacote Node.js [azure](https://www.npmjs.com/package/azure), que inclui um conjunto integrado de bibliotecas auxiliares que comunicam com os serviços REST de notificação push.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Use O Gestor de Pacotes do Nó (NPM) para obter o pacote

1. Utilize uma interface de linha de comando como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Linux) e navegue para a pasta onde criou a sua aplicação em branco.
2. Executar `npm install azure-sb` na janela do comando.
3. Pode executar manualmente `ls` `dir` o comando ou `node_modules` o comando manualmente para verificar se foi criada uma pasta.
4. Dentro dessa pasta, encontre o pacote **azul,** que contém as bibliotecas que precisa para aceder ao Centro de Notificação.

> [!NOTE]
> Você pode saber mais sobre a instalação de NPM no blog oficial da [NPM](https://blog.npmjs.org/post/85484771375/how-to-install-npm).

### <a name="import-the-module"></a>Importar o módulo
Utilizando um editor de texto, adicione `server.js` o seguinte ao topo do ficheiro da aplicação:

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>Criar uma ligação Azure Notification Hub

O `NotificationHubService` objeto permite-lhe trabalhar com centros de notificação. O seguinte código `NotificationHubService` cria um objeto `hubname`para o centro de notificação denominado . Adicione-o perto da `server.js` parte superior do ficheiro, após a declaração para importar o módulo azul:

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

Obtenha o `connectionstring` valor de ligação do [portal Azure] executando os seguintes passos:

1. No painel de navegação à esquerda, clique em **Navegar**.
2. Selecione Centros de **Notificação**e, em seguida, encontre o hub que deseja utilizar para a amostra. Pode consultar o [tutorial Windows Store Getting Started](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) se precisar de ajuda para criar um novo Hub de Notificação.
3. Selecione **Definições**.
4. Clique em Políticas de **Acesso**. Vê cordas de ligação partilhadas e de acesso total.

![Portal Azure - Centros de Notificação](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Também pode recuperar a cadeia de ligação utilizando o cmdlet **Get-AzureSbNamespace** fornecido pela [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou pelo comando de espaço de **espaço bluee sb** com a Interface de [Linha de Comando Azure (Azure CLI)](../cli-install-nodejs.md).

## <a name="general-architecture"></a>Arquitetura geral

O `NotificationHubService` objeto expõe as seguintes instâncias de objetos para o envio de notificações push para dispositivos e aplicações específicos:

- **Android** - `GcmService` use o objeto, que está disponível em`notificationHubService.gcm`
- **iOS** - `ApnsService` use o objeto, que é acessível em`notificationHubService.apns`
- **Windows Phone** - `MpnsService` use o objeto, que está disponível em`notificationHubService.mpns`
- **Universal Windows Platform** `WnsService` - use o objeto, que está disponível em`notificationHubService.wns`

### <a name="how-to-send-push-notifications-to-android-applications"></a>Como: Enviar notificações push para aplicações Android

O `GcmService` objeto fornece `send` um método que pode ser usado para enviar notificações push para aplicações Android. O `send` método aceita os seguintes parâmetros:

- **Tags** - o identificador de etiquetas. Se não for fornecida qualquer etiqueta, a notificação é enviada a todos os clientes.
- **Carga útil** - a mensagem é JSON ou carga de corda crua.
- **Callback** - a função de callback.

Para obter mais informações sobre o formato de carga útil, consulte a [documentação da Carga Útil](https://distriqt.github.io/ANE-PushNotifications/m.FCM-GCM%20Payload).

O código seguinte `GcmService` utiliza a `NotificationHubService` instância exposta pelo endereço para enviar uma notificação push a todos os clientes registados.

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

Tal como acontece com as `ApnsService` aplicações Android `send` acima descritas, o objeto fornece um método que pode ser usado para enviar notificações push para aplicações iOS. O `send` método aceita os seguintes parâmetros:

- **Tags** - o identificador de etiquetas. Se não for fornecida qualquer etiqueta, a notificação é enviada a todos os clientes.
- **Carga útil** - a mensagem é JSON ou carga de corda.
- **Callback** - a função de callback.

Para mais informações sobre o formato de carga útil, consulte a secção de Carga Útil da **Notificação** [Local e Push.](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html)

O seguinte código `ApnsService` utiliza a `NotificationHubService` instância exposta pelo para enviar uma mensagem de alerta a todos os clientes:

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

O `MpnsService` objeto fornece `send` um método que pode ser usado para enviar notificações push para aplicações do Windows Phone. O `send` método aceita os seguintes parâmetros:

- **Tags** - o identificador de etiquetas. Se não for fornecida qualquer etiqueta, a notificação é enviada a todos os clientes.
- **Carga útil** - a carga xml da mensagem.
- **Nome-alvo**  -  `toast` para notificações de torradas. `token`para notificações de azulejos.
- **NotificationClass** - A prioridade da notificação. Consulte a secção **http Header Elements** das [notificações Push a partir de um](https://msdn.microsoft.com/library/hh221551.aspx) documento de servidor para valores válidos.
- **Opções** - cabeçalhos de pedido opcional.
- **Callback** - a função de callback.

Para obter uma `TargetName` `NotificationClass` lista de opções válidas e de cabeçalho, consulte as [notificações push a partir de uma](https://msdn.microsoft.com/library/hh221551.aspx) página do servidor.

O seguinte código `MpnsService` de amostra `NotificationHubService` utiliza a instância exposta pelo envio de uma notificação push torrada:

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Como: Enviar notificações push para aplicações da Plataforma Universal Windows (UWP)

O `WnsService` objeto fornece `send` um método que pode ser usado para enviar notificações push para aplicações da Plataforma Universal Windows.  O `send` método aceita os seguintes parâmetros:

- **Tags** - o identificador de etiquetas. Se não for fornecida qualquer etiqueta, a notificação é enviada a todos os clientes registados.
- **Carga útil** - a carga da mensagem XML.
- **Tipo** - o tipo de notificação.
- **Opções** - cabeçalhos de pedido opcional.
- **Callback** - a função de callback.

Para obter uma lista de tipos válidos e cabeçalhos de pedido, consulte o pedido de pedido de [notificação push e os cabeçalhos de resposta](https://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

O seguinte código `WnsService` utiliza a `NotificationHubService` instância exposta pelo enviado para enviar uma notificação push torrada a uma aplicação UWP:

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>Passos Seguintes

A amostra de fragmentos acima permite-lhe construir facilmente infraestruturas de serviço para entregar notificações push a uma grande variedade de dispositivos. Agora que aprendeu o básico de usar Centros de Notificação com node.js, siga estes links para saber mais sobre como pode alargar ainda mais estas capacidades.

- Consulte a referência msdn para centros de [notificação Azure](https://msdn.microsoft.com/library/azure/jj927170.aspx).
- Visite o [SDK Azure para] o repositório do Nó no GitHub para mais amostras e detalhes de implementação.

[SDK do Azure para Node]: https://github.com/WindowsAzure/azure-sdk-for-node
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
