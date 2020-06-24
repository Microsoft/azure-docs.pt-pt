---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 5c3277394350036b8863185e83b7cfcd4975be1c
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081530"
---
Permite-lhe registar-se e desregistar-se de um centro de notificação através do serviço de backend que criou. 

Um alerta é apresentado quando uma ação é especificada e a aplicação está em primeiro plano. Caso contrário, as notificações aparecem no centro de notificação.

> [!NOTE]
> Normalmente, realizaria as ações de registo (e desregistação) durante o ponto apropriado no ciclo de vida da aplicação (ou como parte da sua experiência de primeira execução, talvez) sem inserirs explícitos de registo/desregista do utilizador. No entanto, este exemplo irá necessitar de uma entrada explícita do utilizador para permitir que esta funcionalidade seja explorada e testada mais facilmente.
