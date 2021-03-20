---
title: Azure Notification Hubs iOS 13 atualiza | Microsoft Docs
description: Saiba mais sobre iOS 13 quebrando alterações em Azure Notification Hubs
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: df8560bec3671a9f05628ee6ed8ea95c31e9b16f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88998055"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>Atualizações do Azure Notification Hubs para iOS 13

A Apple fez recentemente algumas alterações no seu serviço público de push; as alterações alinhadas principalmente com as versões do iOS 13 e Xcode. Este artigo descreve o impacto destas alterações nos Hubs de Notificação Azure.

## <a name="apns-push-payload-changes"></a>APNS empurra alterações de carga útil

### <a name="apns-push-type"></a>Tipo de empurrão APNS

A Apple exige agora que os desenvolvedores identifiquem as notificações como um alerta ou notificações de fundo através do novo `apns-push-type` cabeçalho na APNS API. De acordo com a [documentação da Apple:](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)"O valor deste cabeçalho deve refletir com precisão o conteúdo da carga útil da sua notificação. Se houver uma incompatibilidade, ou se o cabeçalho faltar nos sistemas necessários, as APNs podem devolver um erro, atrasar a entrega da notificação ou largá-la completamente."

Os desenvolvedores devem agora definir este cabeçalho em aplicações que enviam notificações através de Azure Notification Hubs. Devido a uma limitação técnica, os clientes devem utilizar a autenticação baseada em fichas para credenciais APNS com pedidos que incluam este atributo. Se estiver a utilizar a autenticação baseada em certificados para as suas credenciais APNS, deve mudar para a autenticação baseada em fichas.

As seguintes amostras de código mostram como definir este atributo de cabeçalho em pedidos de notificação enviados através de Azure Notification Hubs.

#### <a name="template-notifications---net-sdk"></a>Notificações de modelos - .NET SDK

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

#### <a name="direct-rest-calls"></a>Chamadas diretas DE REST

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

Para o ajudar durante esta transição, quando o Azure Notification Hubs deteta uma notificação que não tem o `apns-push-type` conjunto, o serviço infere o tipo de pressão a partir do pedido de notificação e define o valor automaticamente. Lembre-se de que deve configurar os Hubs de Notificação Azure para utilizar a autenticação baseada em símbolos para definir o cabeçalho necessário; para obter mais informações, consulte [a autenticação baseada em Token (HTTP/2) para a APNS](./notification-hubs-push-notification-http2-token-authentication.md).

## <a name="apns-priority"></a>Prioridade da APNS

Outra pequena alteração, mas que requer uma alteração no pedido de backend que envia notificações, é a exigência de que para notificações de fundo o `apns-priority` cabeçalho deve agora ser definido para 5. Muitas aplicações definem o `apns-priority` cabeçalho para 10 (indicando entrega imediata), ou não o definem e obtêm o valor predefinido (que também é 10).

A definição deste valor para 10 já não é permitida para notificações de fundo, e deve definir o valor de cada pedido. A Apple não fornecerá notificações de fundo se este valor faltar. Por exemplo:

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>Alterações SDK

Durante anos, os desenvolvedores de iOS usaram `description` o atributo dos `deviceToken` dados enviados ao delegado push token para extrair o token push que uma aplicação de backend usa para enviar notificações para o dispositivo. Com o Xcode 11, esse `description` atributo mudou para um formato diferente. O código existente que os desenvolvedores usaram para este atributo está agora quebrado. Atualizámos o Azure Notification Hubs SDK para acomodar esta alteração, por isso, por favor, atualize o SDK utilizado pelas suas aplicações para a versão 2.0.4 ou mais recente dos Hubs de [Notificação Azure iOS SDK](https://github.com/Azure/azure-notificationhubs-ios).
