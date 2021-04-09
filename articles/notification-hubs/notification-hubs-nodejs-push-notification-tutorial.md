---
title: Envio de notificações push com a Azure Notification Hubs e Node.js
description: Saiba como usar os Centros de Notificação para enviar notificações push a partir de uma aplicação Node.js.
keywords: notificações push,notificações push,node.js push,ios push
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
ms.date: 04/29/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-js
ms.openlocfilehash: eb41593938c670199be38140118f276142ceed43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102453228"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Envio de notificações push com a Azure Notification Hubs e Node.js

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Descrição Geral

> [!IMPORTANT]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, crie uma conta de teste gratuita em apenas alguns minutos através do [Azure Free Trial](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

Este guia mostra-lhe como enviar notificações push com a ajuda de Azure Notification Hubs diretamente de uma aplicação [Node.js.](https://nodejs.org)

Os cenários abrangidos incluem o envio de notificações push para aplicações nas seguintes plataformas:

- Android
- iOS
- Plataforma Universal do Windows
- Windows Phone

## <a name="notification-hubs"></a>Hubs de Notificação

Os Hubs de Notificação Azure fornecem uma infraestrutura de fácil utilização, multi-plataforma e escalável para o envio de notificações push para dispositivos móveis. Para mais detalhes sobre a infraestrutura de serviço, consulte a página [Azure Notification Hubs.](/previous-versions/azure/azure-services/jj927170(v=azure.100))

## <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js

O primeiro passo neste tutorial é criar uma nova aplicação de Node.js em branco. Para obter instruções sobre a criação de uma aplicação Node.js, consulte [Criar e implementar uma aplicação Node.js no Web Site Azure][nodejswebsite],Node.js Cloud [ Service][Node.js Cloud Service] utilizando o Windows PowerShell ou Web Site [com WebMatrix][webmatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Configure a sua aplicação para utilizar os centros de notificação

Para utilizar os Hubs de Notificação Azure, é necessário descarregar e utilizar o pacote Node.js [azul](https://www.npmjs.com/package/azure), que inclui um conjunto incorporado de bibliotecas auxiliares que comunicam com os serviços de notificação push REST.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Use o Gestor de Pacotes de Nó (NPM) para obter o pacote

1. Utilize uma interface de linha de comando como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Linux) e navegue para a pasta onde criou a sua aplicação em branco.
2. Execute `npm install azure-sb` na janela de comando.
3. Pode executar manualmente o `ls` ou o comando para verificar se uma pasta foi `dir` `node_modules` criada.
4. Dentro dessa pasta, encontre o pacote **azul,** que contém as bibliotecas que precisa para aceder ao Centro de Notificação.

> [!NOTE]
> Pode saber mais sobre a instalação do NPM no blog oficial da [NPM.](https://blog.npmjs.org/post/85484771375/how-to-install-npm)

### <a name="import-the-module"></a>Importar o módulo
Utilizando um editor de texto, adicione o seguinte ao topo do `server.js` ficheiro da aplicação:

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>Criar uma ligação Azure Notification Hub

O `NotificationHubService` objeto permite-lhe trabalhar com centros de notificação. O código que se segue cria um `NotificationHubService` objeto para o centro de notificação denominado `hubname` . Adicione-o perto da parte superior do `server.js` ficheiro, após a declaração de importação do módulo azul:

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

Obtenha o valor de ligação `connectionstring` a partir do portal [Azure] realizando os seguintes passos:

1. No painel de navegação esquerdo, clique em **Procurar**.
2. Selecione **Os Centros de Notificação** e, em seguida, encontre o hub que deseja utilizar para a amostra. Pode consultar o tutorial da [Windows Store Getting Started](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) se precisar de ajuda para criar um novo Hub de Notificação.
3. Selecione **Definições**.
4. Clique em **Políticas de Acesso.** Vê cordas de ligação partilhadas e de acesso total.

![Portal Azure - Centros de Notificação](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Também pode recuperar a cadeia de ligação utilizando o cmdlet **Get-AzureSbNamespace** fornecido pela [Azure PowerShell](/powershell/azure/) ou o comando **de show de nomes azure sb** com a [Interface de Command-Line Azure (Azure CLI)](/cli/azure/install-classic-cli).

## <a name="general-architecture"></a>Arquitetura geral

O `NotificationHubService` objeto expõe as seguintes instâncias de objeto para o envio de notificações push para dispositivos e aplicações específicas:

- **Android** - use o `GcmService` objeto, que está disponível em `notificationHubService.gcm`
- **iOS** - use o `ApnsService` objeto, que é acessível em `notificationHubService.apns`
- **Windows Phone** - use o `MpnsService` objeto, que está disponível em `notificationHubService.mpns`
- **Universal Windows Platform** - use o `WnsService` objeto, que está disponível em `notificationHubService.wns`

### <a name="how-to-send-push-notifications-to-android-applications"></a>Como: Enviar notificações push para aplicações Android

O `GcmService` objeto fornece um método que pode ser usado para enviar `send` notificações push para aplicações Android. O `send` método aceita os seguintes parâmetros:

- **Etiquetas** - o identificador de etiquetas. Se não for fornecida qualquer etiqueta, a notificação é enviada a todos os clientes.
- **Carga útil** - a mensagem é JSON ou carga de carga de corda bruta.
- **Callback** - a função de retorno.

Para obter mais informações sobre o formato de carga útil, consulte a [documentação da carga útil.](https://payload.readthedocs.io/en/latest/)

O código que se segue utiliza o `GcmService` caso exposto pelo enviado uma `NotificationHubService` notificação push a todos os clientes registados.

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

Tal como acontece com as aplicações Android descritas acima, o `ApnsService` objeto fornece um método que pode ser usado para enviar `send` notificações push para aplicações iOS. O `send` método aceita os seguintes parâmetros:

- **Etiquetas** - o identificador de etiquetas. Se não for fornecida qualquer etiqueta, a notificação é enviada a todos os clientes.
- **Carga útil** - a carga de mensagem é JSON ou carga útil de cordas.
- **Callback** - a função de retorno.

Para obter mais informações sobre o formato de carga útil, consulte a secção de Conteúdo de **Notificação** do [Guia deNotificaçãos do Utilizador](https://developer.apple.com/documentation/usernotifications).

O seguinte código utiliza o `ApnsService` caso exposto pelo para enviar uma `NotificationHubService` mensagem de alerta a todos os clientes:

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

O `MpnsService` objeto fornece um método que pode ser usado para enviar `send` notificações push para aplicações do Windows Phone. O `send` método aceita os seguintes parâmetros:

- **Etiquetas** - o identificador de etiquetas. Se não for fornecida qualquer etiqueta, a notificação é enviada a todos os clientes.
- **Carga útil** - a carga xml da mensagem.
- **Nome do alvo**  -  `toast` para notificações de torradas. `token` para notificações de azulejos.
- **NotificationClass** - A prioridade da notificação. Consulte a secção **elementos do cabeçalho HTTP** das [notificações Push de um](/previous-versions/windows/xna/bb200104(v=xnagamestudio.41)) documento do servidor para obter valores válidos.
- **Opções** - cabeçalhos de pedido opcionais.
- **Callback** - a função de retorno.

Para obter uma lista de `TargetName` `NotificationClass` opções [válidas](/previous-versions/windows/xna/bb200104(v=xnagamestudio.41)) e de cabeçalho, consulte as notificações Push a partir de uma página do servidor.

O seguinte código de amostra utiliza o `MpnsService` caso exposto pelo para enviar uma `NotificationHubService` notificação de tosta push:

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Como: Enviar notificações push para aplicações da Universal Windows Platform (UWP)

O `WnsService` objeto fornece um método que pode ser usado para enviar `send` notificações push para aplicações da Plataforma Universal Windows.  O `send` método aceita os seguintes parâmetros:

- **Etiquetas** - o identificador de etiquetas. Se não for fornecida qualquer etiqueta, a notificação é enviada a todos os clientes registados.
- **Carga útil** - a carga útil da mensagem XML.
- **Tipo** - o tipo de notificação.
- **Opções** - cabeçalhos de pedido opcionais.
- **Callback** - a função de retorno.

Para obter uma lista de tipos válidos e cabeçalhos de pedido, consulte [o pedido de serviço de notificação Push e os cabeçalhos de resposta](/previous-versions/windows/apps/hh465435(v=win.10)).

O seguinte código utiliza o `WnsService` caso exposto pelo para enviar uma `NotificationHubService` notificação push torrada a uma aplicação UWP:

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>Passos Seguintes

Os fragmentos de amostra acima permitem-lhe construir facilmente infraestruturas de serviço para fornecer notificações push a uma grande variedade de dispositivos. Agora que aprendeu o básico de usar Os Centros de Notificação com node.js, siga estas ligações para saber mais sobre como pode alargar ainda mais estas capacidades.

- Consulte a Referência MSDN para [centros de notificação Azure](/previous-versions/azure/azure-services/jj927170(v=azure.100)).
- Visite o [Azure SDK para] repositório de nó no GitHub para mais amostras e detalhes de implementação.

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
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter_sqlexpression
[Azure Service Bus Notification Hubs]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter
[Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: ../app-service/quickstart-nodejs.md
[webmatrix]: /aspnet/web-pages/videos/introduction/create-a-website-using-webmatrix
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Portal do Azure]: https://portal.azure.com
