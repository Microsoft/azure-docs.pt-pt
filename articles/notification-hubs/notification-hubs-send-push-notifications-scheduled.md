---
title: Como enviar notificações agendadas | Microsoft Docs
description: Este tópico descreve o uso de notificações agendadas com os hubs de notificação do Azure.
services: notification-hubs
documentationcenter: .net
keywords: notificações por push, notificação por push, agendando notificações por push
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: ef5eedaa903480ae670f9bc48d0af89744a99d22
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213013"
---
# <a name="how-to-send-scheduled-notifications"></a>Como: Enviar notificações agendadas

Se você tiver um cenário no qual deseja enviar uma notificação em algum momento no futuro, mas não tenha uma maneira fácil de ativar seu código de back-end para enviar a notificação. Os hubs de notificação de camada Standard dão suporte a um recurso que permite agendar notificações até sete dias no futuro.


## <a name="schedule-your-notifications"></a>Agendar suas notificações
Ao enviar uma notificação, basta usar a [ `ScheduledNotification` classe](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) no SDK dos hubs de notificação, conforme mostrado no exemplo a seguir:

```csharp
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Cancelar notificações agendadas
Além disso, você pode cancelar uma notificação agendada anteriormente usando sua notificationId:

```csharp
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Não há limites para o número de notificações agendadas que você pode enviar.

## <a name="next-steps"></a>Passos seguintes

Veja os tutoriais seguintes:

 - [Notificações por push para todos os dispositivos registrados](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Enviar notificações push para dispositivos específicos](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Push localized notifications](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md) (Enviar notificações localizadas)
 - [Push notifications to specific users](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) (Enviar notificações para utilizadores específicos) 
 - [Enviar notificações push com base na localização](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
