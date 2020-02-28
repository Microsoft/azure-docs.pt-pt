---
title: O que é a Azure Service Health?
description: Informações personalizadas sobre como as suas aplicações Azure são afetadas por problemas e manutenção de serviços atuais e futuros do Azure.
ms.topic: overview
ms.date: 05/10/2019
ms.openlocfilehash: 44b819a88baec383d2faf80ab37edb903b0a5f4d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77654091"
---
# <a name="what-is-azure-service-health"></a>O que é a Azure Service Health?

O Azure oferece um conjunto de experiências para mantê-lo informado sobre a saúde dos seus recursos na nuvem. Estas informações incluem questões atuais e futuras, tais como eventos de impacto de serviços, manutenção planeada e outras alterações que possam afetar a sua disponibilidade.

Azure Service Health é uma combinação de três serviços menores separados.

[O estado do Azure](azure-status-overview.md) informa-o das interrupções de serviço em Azure na **[página Estado do Azure](https://status.azure.com)** . A página é uma visão global da saúde de todos os serviços Azure em todas as regiões do Azure. A página de estado é uma boa referência para incidentes com impacto generalizado, mas recomendamos vivamente que os atuais utilizadores do Azure aproveitem a saúde do serviço Azure para se manterem informados sobre os incidentes e manutenção do Azure.

[A saúde](service-health-overview.md) do serviço Azure proporciona uma visão personalizada da saúde dos serviços ERegies Azure que está a utilizar. É aqui que deve procurar comunicações sobre falhas que afetam os serviços, atividades de manutenção planeada e outras informações sobre o estado de funcionamento, pois a experiência autenticada do Azure Service Health sabe quais são os recursos e serviços que utiliza atualmente. A melhor forma de utilizar o Service Health é configurar alertas do Service Health no sentido de o notificar através dos seus canais de comunicação preferenciais quando ocorrem problemas de serviço, manutenção planeada ou outras alterações que possam afetar os serviços e as regiões do Azure que utiliza.

[A saúde](resource-health-overview.md) dos recursos azure fornece informações sobre a saúde dos seus recursos na nuvem individuais, como uma instância de máquina virtual específica. Com o Azure Monitor, também pode configurar alertas para ser notificado de alterações de disponibilidade dos seus recursos da cloud. O Azure Resource Health, juntamente com as notificações do Azure Monitor, irá ajudá-lo a estar informado ao minuto da disponibilidade dos seus recursos e a avaliar rapidamente se um problema se deve a um problema no seu lado ou se está relacionado com um evento de plataforma do Azure.

No seu conjunto, estas experiências dão-lhe uma vista abrangente sobre o estado de funcionamento do Azure, com a granularidade mais relevante para si.

**Veja uma visão geral da página Do Estado do Azure, da Azure Service Health e da Azure Resource Health**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]