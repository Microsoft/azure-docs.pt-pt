---
title: Recuperação geo-desastre da Nuvem de primavera de Azure Microsoft Docs
description: Saiba como proteger a sua aplicação Spring Cloud de interrupções regionais
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: 4961e5a63e5bc1933cf19b1f291b521d89cbda0e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76279144"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Recuperação do desastre da Nuvem de primavera de Azure

Este artigo explica algumas estratégias que pode usar para proteger as suas aplicações Azure Spring Cloud de experimentar o tempo de inatividade.  Qualquer região ou centro de dados pode experimentar o tempo de inatividade causado por desastres regionais, mas um planeamento cuidadoso pode mitigar o impacto nos seus clientes.

## <a name="plan-your-application-deployment"></a>Planeie a sua implementação de aplicações

As aplicações Azure Spring Cloud funcionam numa região específica.  O Azure funciona em várias geografias em todo o mundo. Uma geografia Azure é uma área definida do mundo que contém pelo menos uma região azure. Uma região de Azure é uma área dentro de uma geografia, contendo um ou mais centros de dados.  Cada região de Azure é emparelhada com outra região dentro da mesma geografia, fazendo juntos um par regional. O Azure serializa atualizações de plataformas (manutenção planeada) em pares regionais, garantindo que apenas uma região em cada par é atualizada de cada vez. Em caso de paralisação que afete várias regiões, pelo menos uma região de cada par será priorizada para recuperação.

Garantir uma elevada disponibilidade e proteção contra desastres requer que você implemente as suas aplicações de Nuvem de primavera para várias regiões.  O Azure fornece uma lista de [regiões emparelhadas](../best-practices-availability-paired-regions.md) para que possa planear as suas implantações da Nuvem de primavera para pares regionais.  Recomendamos que considere três fatores fundamentais na conceção da sua arquitetura de microserviços: disponibilidade da região, regiões emparelhadas com Azure e disponibilidade de serviço.

*  Disponibilidade da região: Escolha uma área geográfica próxima dos seus utilizadores para minimizar o atraso de rede e o tempo de transmissão.
*  Regiões emparelhadas com Azure: Escolha regiões emparelhadas dentro da sua área geográfica escolhida para garantir atualizações coordenadas da plataforma e priorizar os esforços de recuperação, se necessário.
*  Disponibilidade do serviço: Decida se as suas regiões emparelhadas devem funcionar quentes/quentes, quentes/quentes ou quentes/frios.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Utilize o Gestor de Tráfego Azure para direcionar o tráfego

[O Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) fornece o equilíbrio de carga de tráfego baseado no DNS e pode distribuir o tráfego da rede por várias regiões.  Utilize o Gestor de Tráfego Azure para direcionar os clientes para a instância de serviço azure spring cloud mais próxima.  Para melhor desempenho e redundância, direcione todo o tráfego de aplicações através do Azure Traffic Manager antes de enviá-lo para o seu serviço Azure Spring Cloud.

Se tiver aplicações Azure Spring Cloud em várias regiões, utilize o Gestor de Tráfego Azure para controlar o fluxo de tráfego para as suas aplicações em cada região.  Defina um ponto final do Gestor de Tráfego Azure para cada serviço utilizando o IP de serviço. Os clientes devem ligar-se a um nome DNS do Gestor de Tráfego Azure que aponta para o serviço Azure Spring Cloud.  A carga do Gestor de Tráfego Azure equilibra o tráfego nos pontos finais definidos.  Se um desastre atingir um centro de dados, o Gestor de Tráfego azure direcionará o tráfego daquela região para o seu par, garantindo a continuidade do serviço.