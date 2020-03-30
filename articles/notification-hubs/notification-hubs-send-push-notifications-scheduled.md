---
title: Como enviar notificações agendadas / Microsoft Docs
description: Este tópico descreve a utilização de Notificações Agendadas com Centros de Notificação Azure.
services: notification-hubs
documentationcenter: .net
keywords: notificações push,push notification,agendando notificações push
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71213013"
---
# <a name="how-to-send-scheduled-notifications"></a>Como: Enviar notificações agendadas

Se tiver um cenário em que pretenda enviar uma notificação em algum momento no futuro, mas não tenha uma maneira fácil de acordar o seu código de back-end para enviar a notificação. Os centros de notificação de nível standard suportam uma funcionalidade que lhe permite agendar notificações até sete dias no futuro.


## <a name="schedule-your-notifications"></a>Agende as suas notificações
Ao enviar uma notificação, basta utilizar a [ `ScheduledNotification` classe](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) no SDK dos Centros de Notificação, como mostra o seguinte exemplo:

```csharp
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Cancelar notificações programadas
Além disso, pode cancelar uma notificação previamente agendada através da sua notificaçãoId:

```csharp
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Não existem limites para o número de notificações programadas que pode enviar.

## <a name="next-steps"></a>Passos seguintes

Veja os tutoriais seguintes:

 - [Notificações push a todos os dispositivos registados](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Enviar notificações push para dispositivos específicos](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Push localized notifications](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md) (Enviar notificações localizadas)
 - [Enviar notificações push para utilizadores específicos](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [Enviar notificações push com base na localização](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
