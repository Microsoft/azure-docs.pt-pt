---
title: O que é o Azure Service Health?
description: Informações personalizadas sobre como as suas aplicações Azure são afetadas por problemas de serviço e manutenção atuais e futuros.
ms.topic: overview
ms.date: 05/10/2019
ms.openlocfilehash: cb1eb8eb75a18a73e8f024f02217df37f45981fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87499777"
---
# <a name="what-is-azure-service-health"></a>O que é o Azure Service Health?

O Azure oferece um conjunto de experiências para mantê-lo informado sobre a saúde dos seus recursos em nuvem. Estas informações incluem questões atuais e futuras, tais como eventos de impacto de serviços, manutenção planeada e outras alterações que possam afetar a sua disponibilidade.

A Azure Service Health é uma combinação de três serviços menores separados.

[O estado de Azure](azure-status-overview.md) informa-o das interrupções de serviço em Azure na **[página Azure Status](https://status.azure.com)**. A página é uma visão global da saúde de todos os serviços Azure em todas as regiões de Azure. A página de status é uma boa referência para incidentes com impacto generalizado, mas recomendamos vivamente que os atuais utilizadores do Azure aproveitem a saúde do serviço Azure para se manterem informados sobre os incidentes e manutenção do Azure.

[A saúde](service-health-overview.md) do serviço proporciona uma visão personalizada da saúde dos serviços e regiões de Azure que você está usando. Este é o melhor local para procurar serviços com impacto nas comunicações sobre interrupções, atividades de manutenção planeadas e outros avisos de saúde porque a experiência autenticada do Serviço de Saúde sabe quais os serviços e recursos que utiliza atualmente. A melhor forma de utilizar o Service Health é configurar alertas do Service Health no sentido de o notificar através dos seus canais de comunicação preferenciais quando ocorrem problemas de serviço, manutenção planeada ou outras alterações que possam afetar os serviços e as regiões do Azure que utiliza.

[A saúde dos recursos](resource-health-overview.md) fornece informações sobre a saúde dos seus recursos individuais em nuvem, como uma instância específica da máquina virtual. Com o Azure Monitor, também pode configurar alertas para ser notificado de alterações de disponibilidade dos seus recursos da cloud. A Resource Health juntamente com as notificações do Azure Monitor irão ajudá-lo a manter-se mais informado sobre a disponibilidade dos seus recursos minuto a minuto e a avaliar rapidamente se um problema se deve a um problema do seu lado ou relacionado com um evento da plataforma Azure.

No seu conjunto, estas experiências dão-lhe uma vista abrangente sobre o estado de funcionamento do Azure, com a granularidade mais relevante para si.

**Veja uma descrição geral da página de Estado do Azure, Azure Service Health e Azure Resource Health**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]