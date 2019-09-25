---
title: Enviando notificações por push com hubs de notificação do Azure e node. js
description: Saiba como usar os hubs de notificação para enviar notificações por push de um aplicativo node. js.
keywords: notificação por push, notificações por push, push do node. js, push do IOS
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
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213186"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Enviando notificações por push com hubs de notificação do Azure e node. js

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Descrição geral

> [!IMPORTANT]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se você não tiver uma conta, crie uma conta de avaliação gratuita em apenas alguns minutos por meio da [avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

Este guia mostra como enviar notificações por push com a ajuda dos hubs de notificação do Azure diretamente de um aplicativo [node. js](https://nodejs.org) .

Os cenários abordados incluem o envio de notificações por push para aplicativos nas seguintes plataformas:

- Android
- iOS
- Plataforma Universal do Windows
- Windows Phone

## <a name="notification-hubs"></a>Hubs de Notificação

Os hubs de notificação do Azure fornecem uma infraestrutura escalonável, de várias plataformas e fácil de usar para o envio de notificações por push para dispositivos móveis. Para obter detalhes sobre a infraestrutura de serviço, consulte a página [hubs de notificação do Azure](https://msdn.microsoft.com/library/windowsazure/jj927170.aspx) .

## <a name="create-a-nodejs-application"></a>Criar um aplicativo node. js

A primeira etapa deste tutorial é criar um novo aplicativo node. js em branco. Para obter instruções sobre como criar um aplicativo node. js, consulte [criar e implantar um aplicativo node. js no site do Azure][nodejswebsite], [serviço de nuvem do node. js][Node.js Cloud Service] usando o Windows PowerShell ou [site com o WebMatrix][webmatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Configurar seu aplicativo para usar hubs de notificação

Para usar os hubs de notificação do Azure, você precisa baixar e usar o [pacote do Azure do](https://www.npmjs.com/package/azure)node. js, que inclui um conjunto interno de bibliotecas auxiliares que se comunicam com os serviços REST de notificação por push.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Usar o Gerenciador de pacotes de nó (NPM) para obter o pacote

1. Use uma interface de linha de comando, como **PowerShell** (Windows), **terminal** (Mac) ou **bash** (Linux), e navegue até a pasta em que você criou o aplicativo em branco.
2. Execute `npm install azure-sb` na janela de comando.
3. Você pode executar manualmente o `ls` comando `dir` ou para verificar se uma `node_modules` pasta foi criada.
4. Dentro dessa pasta, localize o pacote **do Azure** , que contém as bibliotecas necessárias para acessar o Hub de notificação.

> [!NOTE]
> Você pode saber mais sobre como instalar o NPM no [blog](https://blog.npmjs.org/post/85484771375/how-to-install-npm)oficial do NPM.

### <a name="import-the-module"></a>Importar o módulo
Usando um editor de texto, adicione o seguinte à parte superior do `server.js` arquivo do aplicativo:

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>Configurar uma conexão do hub de notificação do Azure

O `NotificationHubService` objeto permite que você trabalhe com os hubs de notificação. O código a seguir cria `NotificationHubService` um objeto para o Hub de `hubname`notificação chamado. Adicione-o próximo à parte superior `server.js` do arquivo, após a instrução para importar o módulo do Azure:

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

Obtenha o valor `connectionstring` de conexão do [portal do Azure] executando as seguintes etapas:

1. No painel de navegação esquerdo, clique em **procurar**.
2. Selecione **hubs de notificação**e localize o Hub que você deseja usar para o exemplo. Você pode consultar o [tutorial de introdução da Windows Store](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) se precisar de ajuda com a criação de um novo hub de notificação.
3. Selecione **definições**.
4. Clique em **políticas de acesso**. Você vê as cadeias de conexão de acesso completo e compartilhado.

![Portal do Azure-hubs de notificação](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Você também pode recuperar a cadeia de conexão usando o cmdlet **Get-AzureSbNamespace** fornecido pelo [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou o comando **namespace do Azure SB show** com a [interface de linha de comando do Azure (CLI do Azure)](../cli-install-nodejs.md).

## <a name="general-architecture"></a>Arquitetura geral

O `NotificationHubService` objeto expõe as seguintes instâncias de objeto para enviar notificações por push a dispositivos e aplicativos específicos:

- **Android** – use o `GcmService` objeto, que está disponível em`notificationHubService.gcm`
- **Ios** -use o `ApnsService` objeto, que pode ser acessado em`notificationHubService.apns`
- **Windows Phone** -use o `MpnsService` objeto, que está disponível em`notificationHubService.mpns`
- **Plataforma universal do Windows** -use o `WnsService` objeto, que está disponível em`notificationHubService.wns`

### <a name="how-to-send-push-notifications-to-android-applications"></a>Como: Enviar notificações push para aplicações Android

O `GcmService` objeto fornece um `send` método que pode ser usado para enviar notificações por push para aplicativos Android. O `send` método aceita os seguintes parâmetros:

- **Marcas** -o identificador de marca. Se nenhuma marca for fornecida, a notificação será enviada a todos os clientes.
- **Payload** -a carga de cadeia de caracteres JSON ou bruta da mensagem.
- **Retorno de chamada** -a função de retorno de chamada.

Para obter mais informações sobre o formato da carga, consulte a [documentação da carga](https://distriqt.github.io/ANE-PushNotifications/m.FCM-GCM%20Payload).

O código a seguir usa `GcmService` a instância exposta `NotificationHubService` pelo para enviar uma notificação por push para todos os clientes registrados.

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

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Como: Enviar notificações por push para aplicativos iOS

Igual aos aplicativos Android descritos acima, o `ApnsService` objeto fornece um `send` método que pode ser usado para enviar notificações por push para aplicativos Ios. O `send` método aceita os seguintes parâmetros:

- **Marcas** -o identificador de marca. Se nenhuma marca for fornecida, a notificação será enviada a todos os clientes.
- **Payload** -a carga JSON ou de cadeia de caracteres da mensagem.
- **Retorno de chamada** -a função de retorno de chamada.

Para obter mais informações sobre o formato da carga, consulte a seção **carga de notificação** do documento do guia de [programação de notificação por push e local](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) .

O código a seguir usa `ApnsService` a instância exposta `NotificationHubService` pelo para enviar uma mensagem de alerta para todos os clientes:

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

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Como: Enviar notificações por push para aplicativos Windows Phone

O `MpnsService` objeto fornece um `send` método que pode ser usado para enviar notificações por push para Windows Phone aplicativos. O `send` método aceita os seguintes parâmetros:

- **Marcas** -o identificador de marca. Se nenhuma marca for fornecida, a notificação será enviada a todos os clientes.
- **Payload** -a carga XML da mensagem.
- **TargetName para notificações do**  - sistema. `toast` `token`para notificações de bloco.
- **NotificationClass** -a prioridade da notificação. Consulte a seção **elementos de cabeçalho http** do documento [notificações por push de um servidor](https://msdn.microsoft.com/library/hh221551.aspx) para obter os valores válidos.
- **Opções** -cabeçalhos de solicitação opcionais.
- **Retorno de chamada** -a função de retorno de chamada.

Para obter uma lista de `TargetName`opções `NotificationClass` de cabeçalho e válidas, confira a página [notificações por push de um servidor](https://msdn.microsoft.com/library/hh221551.aspx) .

O código de exemplo a seguir `MpnsService` usa a instância exposta `NotificationHubService` pelo para enviar uma notificação por push do sistema:

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Como: Enviar notificações por push para aplicativos Plataforma Universal do Windows (UWP)

O `WnsService` objeto fornece um `send` método que pode ser usado para enviar notificações por push para plataforma universal do Windows aplicativos.  O `send` método aceita os seguintes parâmetros:

- **Marcas** -o identificador de marca. Se nenhuma marca for fornecida, a notificação será enviada a todos os clientes registrados.
- **Payload** -a carga da mensagem XML.
- **Tipo** -o tipo de notificação.
- **Opções** -cabeçalhos de solicitação opcionais.
- **Retorno de chamada** -a função de retorno de chamada.

Para obter uma lista de tipos válidos e cabeçalhos de solicitação, consulte [solicitação de serviço de notificação por push e cabeçalhos de resposta](https://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

O código a seguir usa `WnsService` a instância exposta `NotificationHubService` pelo para enviar uma notificação por push do sistema para um aplicativo UWP:

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>Próximos Passos

Os trechos de código de exemplo acima permitem que você crie facilmente a infraestrutura de serviço para fornecer notificações por push a uma grande variedade de dispositivos. Agora que você aprendeu os conceitos básicos do uso de hubs de notificação com o Node. js, siga estes links para saber mais sobre como você pode estender esses recursos ainda mais.

- Consulte a referência do MSDN para os [hubs de notificação do Azure](https://msdn.microsoft.com/library/azure/jj927170.aspx).
- Visite o repositório [SDK do Azure para Node] no GitHub para obter mais exemplos e detalhes de implementação.

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
