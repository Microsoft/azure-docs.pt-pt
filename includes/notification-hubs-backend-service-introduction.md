---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 98c9d83424e7d347bb6e1166a3b00304d39e0cce
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081524"
---
Um [backend de API core web ASP.NET](https://dotnet.microsoft.com/apps/aspnet/apis) é usado para manusear o registo do [dispositivo](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) para o cliente usando a mais recente e melhor abordagem [de instalação.](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#installations) O serviço também enviará notificações push de forma transversal. 

Estas operações são tratadas utilizando os [Hubs de Notificação SDK para operações de backend](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). Mais detalhes sobre a abordagem global são fornecidos no [Registo da documentação de backend da sua aplicação.](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#registration-management-from-a-backend)
