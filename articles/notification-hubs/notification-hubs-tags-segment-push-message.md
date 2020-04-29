---
title: Expressões de encaminhamento e etiqueta em Centros de Notificação Azure
description: Saiba como encaminhar e marcar expressões para centros de notificação Azure.
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
ms.openlocfilehash: 2432ac41645e373ea3a87ff7e69ef02a4e30c81d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062303"
---
# <a name="routing-and-tag-expressions"></a>Expressões de encaminhamento e etiqueta

## <a name="overview"></a>Descrição geral

As expressões de etiquetas permitem-lhe direcionar conjuntos específicos de dispositivos, ou mais especificamente registos, ao enviar uma notificação push através de Centros de Notificação.

## <a name="targeting-specific-registrations"></a>Direcionar registos específicos

A única forma de direcionar registos específicos de notificação é associar as etiquetas com elas e, em seguida, direcionar essas etiquetas. Tal como discutido na [Gestão de Registos,](notification-hubs-push-notification-registration-management.md)para receber notificações push, uma aplicação deve registar um cabo de dispositivo num centro de notificação. Assim que a aplicação criar um registo num centro de notificação, o backend da aplicação pode enviar notificações push para o mesmo. O backend da aplicação pode escolher os registos para o alvo com uma notificação específica das seguintes formas:

1. **Transmissão**: todos os registos no centro de notificação recebem a notificação.
2. **Etiqueta**: todos os registos que contenham a etiqueta especificada recebem a notificação.
3. **Expressão**da etiqueta : todas as inscrições cujo conjunto de etiquetas corresponda à expressão especificada recebem a notificação.

## <a name="tags"></a>Etiquetas

Uma etiqueta pode ser qualquer corda, até 120 caracteres, contendo caracteres alfanuméricos e os`#`seguintes`.`caracteres não`:`alfanuméricos: ''`-``_`''`@`'' '' '' '' '' '' '' '' '' '' ', '', ''. O exemplo seguinte mostra uma aplicação a partir da qual pode receber notificações de torradas sobre grupos musicais específicos. Neste cenário, uma forma simples de encaminhar notificações é rotular registos com etiquetas que representem as diferentes bandas, como na figura seguinte:

![Visão geral das etiquetas](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

Na figura, a mensagem marcada com **Beatles** atinge apenas o tablet que se registou com a tag **Beatles**.

Para mais informações sobre a criação de registos para etiquetas, consulte [Gestão de Registos.](notification-hubs-push-notification-registration-management.md)

Pode enviar notificações para tags utilizando os `Microsoft.Azure.NotificationHubs.NotificationHubClient` métodos de notificações de envio da classe no [Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK. Também pode utilizar o Node.js, ou as APIs DE REPOUSO DE NOTIFICAÇÕES Push.  Aqui está um exemplo usando o SDK.

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

As etiquetas não devem ser pré-aprovisionadas e podem consultar vários conceitos específicos de aplicações. Por exemplo, os utilizadores desta aplicação de exemplo podem comentar bandas e querer receber brindes, não só pelos comentários sobre as suas bandas favoritas, mas também por todos os comentários dos seus amigos, independentemente da banda em que estão a comentar. A figura que se segue destaca um exemplo deste cenário:

![Tags amigos](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

Neste exemplo, Alice está interessada em atualizações para os Beatles, e Bob está interessado em atualizações para os Wailers. O Bob também está interessado nos comentários do Charlie, e o Charlie está interessado nos Wailers. Quando uma notificação é enviada para o comentário de Charlie sobre os Beatles, o Notification Hubs envia-a tanto para alice como bob.

Enquanto pode codificar múltiplas preocupações em `band_Beatles` `follows_Charlie`tags (por exemplo, ou ), as tags são cordas simples e não propriedades com valores. Uma inscrição corresponde apenas à presença ou ausência de uma etiqueta específica.

Para um tutorial passo a passo completo sobre como usar etiquetas para enviar para grupos de interesses, consulte [Breaking News](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> O Azure Notification Hubs suporta um máximo de 60 tags por registo.

## <a name="using-tags-to-target-users"></a>Utilização de tags para utilizadores-alvo

Outra forma de usar as etiquetas é identificar todos os dispositivos associados a um determinado utilizador. Pode marcar um Registo com uma etiqueta que contenha o ID do utilizador, como na seguinte figura:

![Utilizadores de etiquetas](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

Na figura, a mensagem `user_Alice` marcada atinge todos os `user_Alice`dispositivos marcados com .

## <a name="tag-expressions"></a>Expressões de etiquetas

Há casos em que as notificações devem visar um conjunto de registos identificados não por uma única etiqueta, mas por uma expressão Boolean utilizando etiquetas.

Considere uma aplicação desportiva que envie um lembrete a todos em Boston sobre um jogo entre os Red Sox e cardinals. Se a aplicação do cliente registar etiquetas sobre interesse em equipas e localização, então a notificação deve ser direcionada a todos os bostonquee estão interessados nos Red Sox ou nos Cardinals. Esta condição pode ser expressa da seguinte expressão booleana:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![Expressões de etiquetas](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

As expressões de etiquetas `AND` suportam operadores comuns da Boolean, tais como`&&`, `OR` ( (`||`), e `NOT` (`!`); também podem conter parênteses. As expressões `OR` de etiquetas que utilizem apenas os operadores podem fazer referência a 20 tags; expressão `AND` com os `OR` operadores, mas nenhum operador pode fazer referência a 10 tags; caso contrário, as expressões de etiquetas estão limitadas a 6 tags.

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
