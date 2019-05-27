---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 51a75ee7bf87c38e3916bdbc8d85abcfb14dca8b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140335"
---
1. Na [portal do Azure](https://portal.azure.com/), clique em **Procurar tudo** > **serviços aplicacionais**e, em seguida, clique em seu back-end de aplicações móveis. Sob **configurações**, clique em **Push do serviço de aplicações**e, em seguida, clique no nome do hub de notificação.
2. Aceda a **Google (GCM)**, introduza o **chave de servidor de** valor obtido a partir do Firebase no procedimento anterior, e, em seguida, clique em **guardar**.

    ![Defina a chave de API no portal](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

O back-end de aplicações móveis está agora configurado para utilizar o Firebase Cloud Messaging. Isto permite-lhe enviar notificações push para a aplicação em execução num dispositivo Android, ao utilizar o hub de notificação.
