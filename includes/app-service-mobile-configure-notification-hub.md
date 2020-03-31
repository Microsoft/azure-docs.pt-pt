---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: da3793c428c624ce3a224cbd7606ab26c4a50803
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184762"
---
A funcionalidade de Aplicações Móveis do Azure App Service utiliza hubs de [notificação Azure] para enviar pushs, pelo que estará a configurar um hub de notificação para a sua aplicação móvel.

1. No [portal Azure,]vá aos Serviços de **Aplicações**e, em seguida, selecione a sua app de volta. Em **Definições,** **selecione 'Empurrar**' .
2. Para adicionar um recurso de centro de notificação à aplicação, selecione **Connect**. Pode criar um hub ou ligar-se a um existente.

    ![Configurar um hub](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Agora ligou um centro de notificação ao seu projeto back-end mobile Apps. Mais tarde, configura este centro de notificação para se ligar a um sistema de notificação da plataforma (PNS) para empurrar para os dispositivos.

[Portal Azure]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
