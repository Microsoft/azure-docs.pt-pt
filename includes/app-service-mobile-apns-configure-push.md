---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 852a3b00e8513f9ce68c5aae54c974505d0c9af6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184757"
---
1. No seu Mac, inicie **acesso Keychain**. Na barra de navegação esquerdo, sob **categoria**, abra **certificados My**. Localize o certificado SSL que transferiu na secção anterior e, em seguida, divulgar seu conteúdo. Selecione apenas o certificado (não selecione a chave privada). Em seguida, [exportá-lo](https://support.apple.com/kb/PH20122?locale=en_US).
2. Na [portal do Azure](https://portal.azure.com/), selecione **Procurar tudo** > **serviços aplicacionais**. Em seguida, selecione as suas aplicações de Mobile back-end. 
3. Sob **configurações**, selecione **Push do serviço de aplicações**. Em seguida, selecione o nome do hub de notificação. 
4. Aceda a **serviços de notificação Push da Apple** > **carregar certificado**. Carregue o ficheiro. p12, selecionar o correto **modo** (dependendo se o certificado de cliente SSL, do anteriormente é produção ou de sandbox). Guarde quaisquer alterações.

O serviço está agora configurado para trabalhar com notificações push em iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
