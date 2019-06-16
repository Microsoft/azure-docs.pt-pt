---
title: O que é o Azure Service Health?
description: Obter informações personalizadas sobre como as aplicações do Azure são afetadas por problemas de serviço do Azure, atuais e futuras e manutenção.
author: stephbaron
ms.author: stbaron
services: service-health
ms.service: service-health
ms.topic: article
ms.date: 05/10/2019
ms.openlocfilehash: 058a171766680f09eaf4de14ddd25235020b1ba4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079577"
---
# <a name="what-is-azure-service-health"></a>O que é o Azure Service Health?

O Azure oferece um conjunto de experiências mantê-lo informado sobre o estado de funcionamento dos seus recursos na cloud. Estas informações incluem problemas atuais e futuros, como o impacto de eventos, a manutenção planeada e outras alterações que possam afetar a disponibilidade do serviço.

O Azure Service Health é uma combinação de três serviços separados do menores.

[Estado do Azure](azure-status-overview.md) informa-o de interrupções de serviço no Azure no  **[página de estado do Azure](https://status.azure.com)** . A página é uma vista global do Estado de funcionamento de todos os serviços do Azure em todas as regiões do Azure. A página de estado é uma boa referência para incidentes com impacto generalizado, mas é altamente recomendável que os utilizadores do Azure atuais aproveita o estado de funcionamento do serviço do Azure para se manter informados sobre incidentes do Azure e a manutenção.

[Estado de funcionamento do serviço do Azure](service-health-overview.md) fornece uma vista personalizada do Estado de funcionamento dos serviços do Azure e regiões que está a utilizar. É aqui que deve procurar comunicações sobre falhas que afetam os serviços, atividades de manutenção planeada e outras informações sobre o estado de funcionamento, pois a experiência autenticada do Azure Service Health sabe quais são os recursos e serviços que utiliza atualmente. A melhor forma de utilizar o Service Health é configurar alertas do Service Health no sentido de o notificar através dos seus canais de comunicação preferenciais quando ocorrem problemas de serviço, manutenção planeada ou outras alterações que possam afetar os serviços e as regiões do Azure que utiliza.

[Estado de funcionamento de recursos do Azure](resource-health-overview.md) fornece informações sobre o estado de funcionamento dos seus recursos de cloud individuais, como uma instância de máquina virtual específica. Com o Azure Monitor, também pode configurar alertas para ser notificado de alterações de disponibilidade dos seus recursos da cloud. O Azure Resource Health, juntamente com as notificações do Azure Monitor, irá ajudá-lo a estar informado ao minuto da disponibilidade dos seus recursos e a avaliar rapidamente se um problema se deve a um problema no seu lado ou se está relacionado com um evento de plataforma do Azure.

No seu conjunto, estas experiências dão-lhe uma vista abrangente sobre o estado de funcionamento do Azure, com a granularidade mais relevante para si.

**Veja uma descrição geral da página de estado do Azure, Azure Service Health e Azure Resource Health**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]
