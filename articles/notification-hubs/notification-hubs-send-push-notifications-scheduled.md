---
title: Como enviar notificações agendadas Microsoft Docs
description: Este tópico descreve a utilização de Notificações Agendadas com Centros de Notificação Azure.
services: notification-hubs
documentationcenter: .net
keywords: notificações push,notificações push,agendando notificações push
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 56eedda7f79fedce1e34ad837c92006e5cd8f191
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88998276"
---
# <a name="how-to-send-scheduled-notifications"></a>Como: Enviar notificações agendadas

Se tiver um cenário em que pretende enviar uma notificação em algum momento no futuro, mas não tem uma maneira fácil de acordar o seu código de back-end para enviar a notificação. Os centros de notificação de nível padrão suportam uma funcionalidade que lhe permite agendar notificações até sete dias no futuro.


## <a name="schedule-your-notifications"></a>Agende as suas notificações
Ao enviar uma notificação, basta utilizar a [ `ScheduledNotification` classe](/dotnet/api/microsoft.azure.notificationhubs.schedulednotification?view=azure-dotnet#microsoft_azure_notificationhubs_schedulednotification) nos Centros de Notificação SDK, como mostrado no seguinte exemplo:

```csharp
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Cancelar notificações programadas
Além disso, pode cancelar uma notificação previamente agendada utilizando o seu notificationId:

```csharp
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Não existem limites para o número de notificações agendadas que pode enviar.

## <a name="next-steps"></a>Passos seguintes

Veja os tutoriais seguintes:

 - [Notificações push para todos os dispositivos registados](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Enviar notificações push para dispositivos específicos](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Push localized notifications](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md) (Enviar notificações localizadas)
 - [Enviar notificações push para utilizadores específicos](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [Enviar notificações push baseadas na localização](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
