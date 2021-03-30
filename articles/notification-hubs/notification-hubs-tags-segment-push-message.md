---
title: Expressões de encaminhamento e etiqueta em Hubs de Notificação de Azure
description: Saiba como encaminhar e etiquetar expressões para Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 4ff8225522e79e2be40682fb5e4823777dde2aa0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88998308"
---
# <a name="routing-and-tag-expressions"></a>Expressões de encaminhamento e etiqueta

## <a name="overview"></a>Descrição Geral

As expressões de etiquetas permitem-lhe direcionar conjuntos específicos de dispositivos, ou mais especificamente registos, ao enviar uma notificação push através de Centros de Notificação.

## <a name="targeting-specific-registrations"></a>Direcionar registos específicos

A única forma de direcionar registos de notificação específicos é associar etiquetas com eles e, em seguida, direcionar essas etiquetas. Conforme discutido na [Gestão de Registos](notification-hubs-push-notification-registration-management.md), para receber notificações push, uma aplicação deve registar uma pega do dispositivo num hub de notificação. Uma vez que a aplicação cria um registo num centro de notificação, o backend da aplicação pode enviar notificações push para a sua. O backend do pedido pode escolher as inscrições para o destino com uma notificação específica das seguintes formas:

1. **Transmissão:** todos os registos no centro de notificação recebem a notificação.
2. **Tag**: todos os registos que contenham a etiqueta especificada recebam a notificação.
3. **Expressão da etiqueta**: todas as inscrições cujo conjunto de tags correspondam à expressão especificada recebem a notificação.

## <a name="tags"></a>Etiquetas

Uma etiqueta pode ser qualquer corda, até 120 caracteres, contendo caracteres alfanuméricos e os seguintes caracteres não alfanuméricos: ' `_` ' ' ' ' ' ' ' ' ' ' ' ' `@` `#` `.` `:` '' '' `-` ', ', '. O exemplo a seguir mostra uma aplicação a partir da qual pode receber notificações de torradas sobre grupos musicais específicos. Neste cenário, uma forma simples de encaminhar notificações é rotular registos com etiquetas que representem as diferentes bandas, como na seguinte figura:

![Visão geral das etiquetas](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

Na figura, a mensagem marcada com **Beatles** atinge apenas o tablet que se registou com a etiqueta **Beatles.**

Para obter mais informações sobre a criação de inscrições para tags, consulte [Gestão de Registos.](notification-hubs-push-notification-registration-management.md)

Pode enviar notificações para tags utilizando os métodos de notificações de envio da `Microsoft.Azure.NotificationHubs.NotificationHubClient` classe no [Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK. Também pode utilizar Node.js ou as APIs de Push Notifications REST.  Aqui está um exemplo usando o SDK.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You requested a Beatles notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You requested a Wailers notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");
```

As etiquetas não devem ser pré-aprovisionadas e podem referir-se a vários conceitos específicos de aplicações. Por exemplo, os utilizadores desta aplicação de exemplo podem comentar bandas e querer receber brindes, não só pelos comentários nas suas bandas favoritas, mas também por todos os comentários dos seus amigos, independentemente da banda em que comentam. A seguinte figura destaca um exemplo deste cenário:

![Tags amigos](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

Neste exemplo, Alice está interessada em atualizações para os Beatles, e Bob está interessado em atualizações para os Wailers. Bob também está interessado nos comentários de Charlie, e Charlie está interessado nos Wailers. Quando uma notificação é enviada para o comentário de Charlie sobre os Beatles, os Centros de Notificação enviam-na para Alice e Bob.

Embora possa codificar múltiplas preocupações em tags (por exemplo, `band_Beatles` `follows_Charlie` ou), as tags são cordas simples e não propriedades com valores. A inscrição corresponde apenas à presença ou ausência de uma etiqueta específica.

Para um tutorial passo a passo completo sobre como usar tags para enviar para grupos de interesse, consulte [Breaking News](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> O Azure Notification Hubs suporta um máximo de 60 tags por registo.

## <a name="using-tags-to-target-users"></a>Utilização de tags para utilizadores-alvo

Outra forma de utilizar tags é identificar todos os dispositivos associados a um determinado utilizador. Pode marcar um Registo com uma etiqueta que contenha o ID do utilizador, como na seguinte figura:

![Utilizadores de etiquetas](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

Na figura, a mensagem marcada `user_Alice` atinge todos os dispositivos marcados com `user_Alice` .

## <a name="tag-expressions"></a>Expressãos de etiqueta

Existem casos em que as notificações devem visar um conjunto de registos identificados não por uma única etiqueta, mas por uma expressão booleana utilizando tags.

Considere uma aplicação desportiva que envia um lembrete a todos em Boston sobre um jogo entre os Red Sox e os Cardinals. Se a aplicação do cliente registar etiquetas sobre interesse em equipas e localização, então a notificação deve ser direcionada a todos os que em Boston estejam interessados nos Red Sox ou nos Cardinals. Esta condição pode ser expressa com a seguinte expressão booleana:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![Expressãos de etiqueta](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

As expressões de tag suportam operadores boolean comuns como `AND` ( , ) e ( ); eles também podem conter `&&` `OR` `||` `NOT` `!` parênteses. As expressões de etiquetas que utilizam apenas `OR` os operadores podem fazer referência a 20 tags; expressão com `AND` operadores mas nenhum `OR` operador pode referenciar 10 tags; caso contrário, as expressões de tags são limitadas a 6 tags.

Aqui está um exemplo para o envio de notificações com expressões de etiquetas usando o SDK:

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

String userTag = "(location_Boston && !follows_Cardinals)";

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
```
