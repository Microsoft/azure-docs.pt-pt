---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 79459be30a5a2018dc82486a84895b1a954941bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184758"
---
1. No [portal Azure,](https://portal.azure.com/)selecione **Browse todos os** > serviços de**aplicações.** Em seguida, selecione as suas Aplicações Móveis na parte de trás. Em **Definições,** selecione **Impulso do serviço de aplicações**. Em seguida, selecione o nome do seu centro de notificação.
2. Ir ao **Windows (WNS)**. Em seguida, introduza a **chave de segurança** (segredo do cliente) e o pacote **SID** que obteve no site dos Serviços Ao Vivo. Em seguida, selecione **Guardar**.

    ![Desloque a tecla WNS no portal](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

A sua parte traseira está agora configurada para utilizar a WNS para enviar notificações push.
