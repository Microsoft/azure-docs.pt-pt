---
title: Como enviar notificações agendadas | Documentos da Microsoft
description: Este tópico descreve usando notificações agendadas com Notification Hubs do Azure.
services: notification-hubs
documentationcenter: .net
keywords: notificações push, notificação push, notificações push de agendamento
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
origin.date: 04/14/2018
ms.date: 02/25/2019
ms.author: v-biyu
ms.openlocfilehash: af0de9e8c18644f4ae200f6546c0dd0a41320f9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61457690"
---
# <a name="how-to-send-scheduled-notifications"></a>Como: Enviar notificações agendadas

Se tiver um cenário no qual deseja enviar uma notificação em algum momento no futuro, mas não tem uma forma fácil de reativação seu código de back-end para enviar a notificação. Os hubs de notificação do escalão Standard suportam uma funcionalidade que permite-lhe agendar notificações até sete dias no futuro.


## <a name="schedule-your-notifications"></a>Agendar notificações
Ao enviar uma notificação, basta usar o [ `ScheduledNotification` classe](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) no SDK de Hubs de notificação, conforme mostrado no exemplo a seguir:

```c#
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Cancelar notificações agendadas
Além disso, pode cancelar uma notificação anteriormente agendada com sua identificação:

```c#
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Não há nenhum limite no número de notificações agendadas, que pode enviar.

## <a name="next-steps"></a>Passos Seguintes

Veja os tutoriais seguintes:

 - [Notificações push para todos os dispositivos registados](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Enviar notificações push para dispositivos específicos](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Push localized notifications](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md) (Enviar notificações localizadas)
 - [Push notifications to specific users](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) (Enviar notificações para utilizadores específicos) 
 - [Enviar notificações push com base na localização](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
