---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 51a75ee7bf87c38e3916bdbc8d85abcfb14dca8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184764"
---
1. No [portal Azure,](https://portal.azure.com/)clique em Navegar em **Todos os** > Serviços de**Aplicações**e, em seguida, clique nas suas Aplicações Móveis de volta. Em **Definições,** clique em **Push do Serviço de Aplicações**e, em seguida, clique no nome do seu centro de notificação.
2. Vá ao **Google (GCM)**, introduza o valor **da Chave** do Servidor que obteve da Firebase no procedimento anterior e, em seguida, clique em **Guardar**.

    ![Desloque a tecla API no portal](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

A parte traseira das Aplicações Móveis está agora configurada para utilizar mensagens cloud firebase. Isto permite-lhe enviar notificações push para a sua aplicação que funciona num dispositivo Android, utilizando o centro de notificação.
