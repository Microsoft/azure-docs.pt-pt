---
title: Encaminhamento e expressões de etiqueta
description: Este tópico explica as expressões de encaminhamento e a etiqueta para os hubs de notificação do Azure.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2019
ms.author: jowargo
ms.openlocfilehash: 31a22aabc7b0f1d51a673ef8642037103badcc02
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61457826"
---
# <a name="routing-and-tag-expressions"></a>Expressões de encaminhamento e a etiqueta

## <a name="overview"></a>Descrição geral

Expressões de etiqueta permitem a conjuntos específicos do destino de dispositivos ou registros mais especificamente, ao enviar uma notificação push através dos Hubs de notificação.

## <a name="targeting-specific-registrations"></a>Direcionamento registos específicos

A única forma de destino de notificação específica é associar etiquetas a eles, registos de destino, em seguida, essas marcas. Conforme discutido [gestão de registo](notification-hubs-push-notification-registration-management.md), para receber notificações de uma aplicação tem de registar um dispositivo processam num hub de notificação de push. Depois de criar um registo no hub de notificação, o back-end de aplicação pode enviar notificações push para o mesmo. O back-end de aplicação pode escolher os registros de destino com uma notificação específica das seguintes formas:

1. **Difusão**: todos os registos no hub de notificações recebem a notificação.
2. **Etiqueta**: todos os registos que contêm a marca especificada recebem a notificação.
3. **Expressão de etiqueta**: todos os registos cujo conjunto de marcas corresponde a expressão especificada ao recebem a notificação.

## <a name="tags"></a>Tags

Uma etiqueta pode ser qualquer cadeia de caracteres, até 120 carateres, contendo de alfanuméricos e os seguintes carateres não alfanuméricos: '_', ' @', '#', '. ',':', '-'. O exemplo seguinte mostra uma aplicação a partir do qual pode receber notificações de alerta sobre os grupos de música específico. Neste cenário, uma forma simples de notificações de rota é registos de etiqueta com etiquetas que representam as diferentes faixas, como na imagem seguinte:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

Nesta imagem, a mensagem marcada **Beatles** atinge apenas o tablet registado com a marca **Beatles**.

Para obter mais informações sobre a criação de registos para etiquetas, consulte [gestão de registo](notification-hubs-push-notification-registration-management.md).

Pode enviar notificações para etiquetas usando os métodos de notificações de envio do `Microsoft.Azure.NotificationHubs.NotificationHubClient` classe na [Hubs de notificação do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK. Também pode utilizar o node. js ou as APIs de REST de notificações Push.  Eis um exemplo com o SDK.

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

As etiquetas não têm de ser previamente aprovisionado e podem fazer referência a vários conceitos de aplicações específicas. Por exemplo, os utilizadores desta aplicação de exemplo podem comentar bandas e deseja receber circula, não só para comentários nas suas bandas Favoritos, mas também para todos os comentários dos seus amigos, independentemente da banda na qual comentários. A imagem seguinte mostra um exemplo deste cenário:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

Nesta imagem, Alice está interessada em atualizações para dos Beatles e Bob está interessado em atualizações para os Wailers. BOB também está interessado nos comentários do Charlie e Charlie está interessado nos Wailers. Quando uma notificação é enviada para o comentário do Charlie dos Beatles, Alice e Bob recebê-la.

Enquanto pode codificar várias questões em etiquetas (por exemplo, "band_Beatles" ou "follows_Charlie"), as etiquetas são cadeias de caracteres simples e não as propriedades com valores. Um registo é correspondido apenas sobre a presença ou ausência de uma etiqueta específica.

Para obter um tutorial completo passo a passo sobre como utilizar etiquetas para enviar para os grupos de interesses, consulte [notícias de última hora](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> Os Hubs de notificação do Azure suporta um máximo de 60 etiquetas por registo.

## <a name="using-tags-to-target-users"></a>Utilizar etiquetas para os utilizadores de destino

É outra forma de utilizar etiquetas identificar todos os dispositivos de um utilizador específico. Registos podem ser etiquetados com uma etiqueta que contém um ID de utilizador, como na imagem seguinte:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

Nesta imagem, a mensagem marcada uid: Alice atinge todos os registos marcadas "uid:Alice"; Por conseguinte, todos os dispositivos de Alice.

## <a name="tag-expressions"></a>Expressões de etiqueta

Há casos em que tem uma notificação para um conjunto de registos, que é identificado não por uma única etiqueta, mas por uma expressão booleana em etiquetas de destino.

Considere um aplicativo de desportos que envia um lembrete para todas as pessoas em Boston sobre um jogo entre a Red Sox e Cardinals. Se a aplicação de cliente registra etiquetas sobre interesse em equipes e a localização, em seguida, a notificação. deve ser direcionada para todos os utilizadores em Boston que está interessado no Red Sox ou o Cardinals. Esta condição pode ser expresso com a seguinte expressão booleana:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

Expressões de etiqueta podem conter todos os operadores booleanos, tais como AND (& &), ou (|) e não (!!!). Eles também podem conter parênteses. Expressões de etiqueta estão limitadas a 20 etiquetas se contiverem apenas ORs; caso contrário, eles se limitam às 6 etiquetas.

Eis um exemplo para enviar notificações com expressões de etiqueta com o SDK.

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
