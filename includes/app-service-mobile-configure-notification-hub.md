---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: da3793c428c624ce3a224cbd7606ab26c4a50803
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184762"
---
A funcionalidade de aplicações móveis do serviço de aplicações do Azure utiliza [Hubs de notificação do Azure] para enviar pushes, portanto, será possível configurar um hub de notificação para a sua aplicação móvel.

1. Na [Azure portal], aceda à **dos serviços de aplicações**e, em seguida, selecione o seu back-end de aplicação. Sob **configurações**, selecione **Push**.
2. Para adicionar um recurso do hub de notificação para a aplicação, selecione **Connect**. Pode criar um hub ou ligar a um existente.

    ![Configurar um hub](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Agora ligar um hub de notificação ao seu projeto de back-end de aplicações móveis. Posteriormente, vai configurar este hub de notificação para ligar a um sistema de notificação de plataforma (PNS) para enviar por push para dispositivos.

[Azure portal]: https://portal.azure.com/
[Hubs de notificação do Azure]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
