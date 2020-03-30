---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 852a3b00e8513f9ce68c5aae54c974505d0c9af6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184757"
---
1. No seu Mac, lance **o Keychain Access**. Na barra de navegação esquerda, na **categoria,** abra **os meus certificados.** Encontre o certificado SSL que descarregou na secção anterior e, em seguida, divulgue o seu conteúdo. Selecione apenas o certificado (não selecione a chave privada). Em [seguida, exportá-lo.](https://support.apple.com/kb/PH20122?locale=en_US)
2. No [portal Azure,](https://portal.azure.com/)selecione **Browse todos os** > serviços de**aplicações.** Em seguida, selecione as suas Aplicações Móveis na parte de trás. 
3. Em **Definições,** selecione **Impulso do serviço de aplicações**. Em seguida, selecione o nome do seu centro de notificação. 
4. Vá ao Certificado de > **Upload**de Serviços de **Notificação push da Apple**. Faça upload do ficheiro .p12, selecionando o **modo** correto (dependendo se o certificado SSL do seu cliente anteriormente é produção ou caixa de areia). Guarde quaisquer alterações.

O seu serviço está agora configurado para trabalhar com notificações push no iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
