---
title: Atualizações do Azure Notification Hubs iOS 13 Microsoft Docs
description: Saiba mais sobre as mudanças no iOS 13 nos Centros de Notificação do Azure
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 697e8ba9c9f27e8d5644e3a78950ff006290efe7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74228148"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>Atualizações do Azure Notification Hubs para o iOS 13

A Apple fez recentemente algumas alterações no seu serviço público de push; as alterações alinharam-se principalmente com as versões do iOS 13 e do Xcode. Este artigo descreve o impacto destas alterações nos Centros de Notificação do Azure.

## <a name="apns-push-payload-changes"></a>Alterações na carga útil do impulso APNS

### <a name="apns-push-type"></a>Tipo de impulso APNS

A Apple exige agora que os desenvolvedores identifiquem notificações como um alerta ou notificações de fundo através do novo `apns-push-type` cabeçalho na API APNS. De acordo com a [documentação da Apple:](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)"O valor deste cabeçalho deve refletir com precisão o conteúdo da carga útil da sua notificação. Se houver uma incompatibilidade, ou se o cabeçalho estiver em falta nos sistemas necessários, as APNs podem devolver um erro, atrasar a entrega da notificação ou deixá-la completamente."

Os desenvolvedores devem agora definir este cabeçalho em aplicações que enviam notificações através de Hubs de Notificação Azure. Devido a uma limitação técnica, os clientes devem utilizar a autenticação baseada em token para credenciais APNS com pedidos que incluam este atributo. Se estiver a utilizar a autenticação baseada em certificados para as suas credenciais APNS, tem de mudar para a utilização da autenticação baseada em tokens.

As seguintes amostras de código mostram como definir este atributo cabeçalho em pedidos de notificação enviados através de Hubs de Notificação Azure.

#### <a name="template-notifications---net-sdk"></a>Notificações de modelo - .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>Notificações nativas - .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>Chamadas diretas de REST

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

Para o ajudar durante esta transição, quando o Azure Notification `apns-push-type` Hubs deteta uma notificação que não tem o conjunto, o serviço infere o tipo de push do pedido de notificação e define o valor automaticamente. Lembre-se que tem de configurar os Centros de Notificação Azure para utilizar a autenticação baseada em tokens para definir o cabeçalho necessário; para mais informações, consulte a [autenticação baseada em Token (HTTP/2) para APNS](notification-hubs-push-notification-http2-token-authentification.md).

## <a name="apns-priority"></a>Prioridade apns

Outra pequena alteração, mas que requer uma alteração ao pedido de backend que envia `apns-priority` notificações, é a exigência de que, para notificações de fundo, o cabeçalho tenha agora de ser fixado em 5. Muitas aplicações `apns-priority` fixam o cabeçalho para 10 (indicando a entrega imediata), ou não o configuram e obtêm o valor predefinido (que também é 10).

A fixação deste valor para 10 já não é permitida para notificações de fundo, e deve definir o valor para cada pedido. A Apple não entregará notificações de fundo se este valor estiver em falta. Por exemplo:

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>Alterações sdk

Durante anos, os desenvolvedores do iOS usaram o `description` atributo dos `deviceToken` dados enviados ao delegado push token para extrair o token push que uma aplicação de backend usa para enviar notificações para o dispositivo. Com o Xcode `description` 11, esse atributo mudou para um formato diferente. O código existente que os desenvolvedores usaram para este atributo está agora quebrado. Atualizámos o Azure Notification Hubs SDK para acomodar esta alteração, por isso, por favor atualize o SDK utilizado pelas suas aplicações para a versão 2.0.4 ou mais recente do [Azure Notification Hubs iOS SDK](https://github.com/Azure/azure-notificationhubs-ios).
