---
title: O que é a integridade de serviço do Azure?
description: Informações personalizadas sobre como seus aplicativos do Azure são afetados por problemas e manutenção atuais e futuros do serviço do Azure.
ms.topic: article
ms.date: 05/10/2019
ms.openlocfilehash: 039583d9243f8ce76b33afcee098e71a670b5285
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76939206"
---
# <a name="what-is-azure-service-health"></a>O que é a integridade de serviço do Azure?

O Azure oferece um conjunto de experiências para manter você informado sobre a integridade dos seus recursos de nuvem. Essas informações incluem problemas atuais e futuros, como eventos de impacto de serviço, manutenção planejada e outras alterações que podem afetar sua disponibilidade.

A integridade do serviço do Azure é uma combinação de três serviços menores separados.

O [status do Azure](azure-status-overview.md) informa você sobre interrupções de serviço no Azure na **[página de status do Azure](https://status.azure.com)** . A página é uma exibição global da integridade de todos os serviços do Azure em todas as regiões do Azure. A página de status é uma boa referência para incidentes com impacto generalizado, mas é altamente recomendável que os usuários atuais do Azure aproveitem a integridade do serviço do Azure para se manter informado sobre os incidentes e a manutenção do Azure.

A [integridade do serviço do Azure](service-health-overview.md) fornece uma exibição personalizada da integridade dos serviços e regiões do Azure que você está usando. É aqui que deve procurar comunicações sobre falhas que afetam os serviços, atividades de manutenção planeada e outras informações sobre o estado de funcionamento, pois a experiência autenticada do Azure Service Health sabe quais são os recursos e serviços que utiliza atualmente. A melhor forma de utilizar o Service Health é configurar alertas do Service Health no sentido de o notificar através dos seus canais de comunicação preferenciais quando ocorrem problemas de serviço, manutenção planeada ou outras alterações que possam afetar os serviços e as regiões do Azure que utiliza.

O [Azure Resource Health](resource-health-overview.md) fornece informações sobre a integridade de seus recursos de nuvem individuais, como uma instância de máquina virtual específica. Com o Azure Monitor, também pode configurar alertas para ser notificado de alterações de disponibilidade dos seus recursos da cloud. O Azure Resource Health, juntamente com as notificações do Azure Monitor, irá ajudá-lo a estar informado ao minuto da disponibilidade dos seus recursos e a avaliar rapidamente se um problema se deve a um problema no seu lado ou se está relacionado com um evento de plataforma do Azure.

No seu conjunto, estas experiências dão-lhe uma vista abrangente sobre o estado de funcionamento do Azure, com a granularidade mais relevante para si.

**Assista a uma visão geral da página de status do Azure, da integridade do serviço do Azure e do Azure Resource Health**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]