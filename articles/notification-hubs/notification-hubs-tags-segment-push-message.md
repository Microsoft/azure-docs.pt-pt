---
title: Expressões de marcação e de roteamento
description: Este tópico explica o roteamento e as expressões de marca para os hubs de notificação do Azure.
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
ms.lastreviewed: 01/23/2019
ms.openlocfilehash: 236e222da9e9a64d4b93002d28c94fa6fe469c08
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972018"
---
# <a name="routing-and-tag-expressions"></a>Expressões de marcação e de roteamento

## <a name="overview"></a>Visão geral

As expressões de marca permitem que você direcione conjuntos específicos de dispositivos ou mais especificamente registros, ao enviar uma notificação por Push por meio de hubs de notificação.

## <a name="targeting-specific-registrations"></a>Direcionando registros específicos

A única maneira de direcionar registros de notificação específicos é associar marcas a eles e, em seguida, direcionar essas marcas. Conforme discutido no [Gerenciamento de registro](notification-hubs-push-notification-registration-management.md), para receber notificações por push, um aplicativo precisa registrar um identificador de dispositivo em um hub de notificação. Depois que um registro é criado em um hub de notificação, o back-end do aplicativo pode enviar notificações por push para ele. O back-end do aplicativo pode escolher os registros para direcionar com uma notificação específica das seguintes maneiras:

1. **Difusão**: todos os registros no Hub de notificação recebem a notificação.
2. **Marca**: todos os registros que contêm a marca especificada recebem a notificação.
3. **Expressão de marca**: todos os registros cujo conjunto de marcas corresponde à expressão especificada recebe a notificação.

## <a name="tags"></a>Etiquetas

Uma marca pode ser qualquer cadeia de caracteres, até 120 caracteres, contendo alfanuméricos e os seguintes caracteres não alfanuméricos: ' _ ', ' @ ', ' # ', '. ', ': ', '-'. O exemplo a seguir mostra um aplicativo do qual você pode receber notificações do sistema sobre grupos de música específicos. Nesse cenário, uma maneira simples de rotear notificações é rotular registros com marcas que representam as diferentes faixas, como na figura a seguir:

![Visão geral de marcas](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

Nesta imagem, a mensagem marcada como **Beatles** atinge apenas o Tablet que se registrou com a marca **Beatles**.

Para obter mais informações sobre como criar registros para marcas, consulte [Gerenciamento de registro](notification-hubs-push-notification-registration-management.md).

Você pode enviar notificações para marcas usando os métodos Send Notifications da classe `Microsoft.Azure.NotificationHubs.NotificationHubClient` no SDK do [hubs de notificações do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) . Você também pode usar o Node. js ou as APIs REST de notificações por push.  Aqui está um exemplo usando o SDK.

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

As marcas não precisam ser previamente provisionadas e podem se referir a vários conceitos específicos do aplicativo. Por exemplo, os usuários deste aplicativo de exemplo podem comentar sobre faixas e querem receber notificações, não apenas para comentários sobre suas bandas favoritas, mas também para todos os comentários de seus amigos, independentemente da banda na qual estão comentando. A imagem a seguir mostra um exemplo desse cenário:

![Marca amigos](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

Nesta figura, Alice está interessada em atualizações para o Beatles e Bob está interessado em atualizações para o Wailers. Bob também está interessado nos comentários de Charlie e Charlie está interessado no Wailers. Quando uma notificação é enviada para o comentário de Charlie sobre o Beatles, Alice e Bob o recebem.

Embora você possa codificar várias preocupações nas marcas (por exemplo, "band_Beatles" ou "follows_Charlie"), as marcas são cadeias de caracteres simples e não propriedades com valores. Um registro é correspondido somente na presença ou ausência de uma marca específica.

Para obter um tutorial passo a passo completo sobre como usar marcas para enviar a grupos de interesse, consulte [últimas notícias](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> Os hubs de notificação do Azure dão suporte a um máximo de 60 marcas por registro.

## <a name="using-tags-to-target-users"></a>Usando marcas para usuários de destino

Outra maneira de usar marcas é identificar todos os dispositivos de um usuário específico. Os registros podem ser marcados com uma marca que contém uma ID de usuário, como na figura a seguir:

![Marcar usuários](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

Nesta imagem, a mensagem marcada UID: Alice atinge todos os registros marcados como "UID: Alice"; Portanto, todos os dispositivos de Alice.

## <a name="tag-expressions"></a>Expressões de marca

Há casos em que uma notificação tem de direcionar um conjunto de registros que é identificado não por uma única marca, mas por uma expressão booliana em marcas.

Considere um aplicativo esportivo que envia um lembrete para todos em Boston sobre um jogo entre o Red Sox e o Cardinals. Se o aplicativo cliente registra marcas sobre interesse em equipes e local, a notificação deve ser direcionada a todos em Boston que estejam interessadas no Red Sox ou no Cardinals. Essa condição pode ser expressa com a seguinte expressão booliana:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![Expressões de marca](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

As expressões de marca podem conter todos os operadores boolianos, como e (& &) ou (| |), e não (!). Eles também podem conter parênteses. As expressões de marca são limitadas a 20 marcas se contiverem somente o ORs; caso contrário, eles serão limitados a 6 marcas.

Aqui está um exemplo para enviar notificações com expressões de marca usando o SDK.

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
