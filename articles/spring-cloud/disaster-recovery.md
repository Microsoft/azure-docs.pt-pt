---
title: Recuperação de desastre geográfica do Azure Spring Cloud | Microsoft Docs
description: Saiba como proteger seu aplicativo Spring Cloud de interrupções regionais
services: spring-cloud
author: jpconnock
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jeconnoc
ms.openlocfilehash: 4752cba6081d376bd006406a154b9b182c6a3a72
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164968"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Recuperação de desastres na nuvem do Azure Spring

Este artigo explica algumas estratégias que você pode usar para proteger seus aplicativos do Azure Spring Cloud de sofrerem tempo de inatividade.  Qualquer região ou data center pode enfrentar tempo de inatividade causado por desastres regionais, mas o planejamento cuidadoso pode reduzir o impacto sobre os clientes.

## <a name="plan-your-application-deployment"></a>Planejar a implantação do aplicativo

Os aplicativos de nuvem Spring do Azure são executados em uma região específica.  O Azure opera em várias regiões geográficas em todo o mundo. Uma geografia do Azure é uma área definida do mundo que contém pelo menos uma região do Azure. Uma região do Azure é uma área em uma geografia, que contém um ou mais data centers.  Cada região do Azure é emparelhada com outra região na mesma geografia, juntos criando um par regional. O Azure serializa atualizações de plataforma (manutenção planejada) entre pares regionais, garantindo que apenas uma região em cada par seja atualizada de cada vez. No caso de uma interrupção que afete várias regiões, pelo menos uma região em cada par será priorizada para recuperação.

Garantir a alta disponibilidade e a proteção contra desastres exige que você implante seus aplicativos Spring Cloud em várias regiões.  O Azure fornece uma lista de [regiões emparelhadas](../best-practices-availability-paired-regions.md) para que você possa planejar suas implantações de nuvem Spring em pares regionais.  Recomendamos que você considere três fatores principais ao projetar sua arquitetura de micro serviço: disponibilidade de região, regiões emparelhadas do Azure e disponibilidade de serviço.

*  Disponibilidade da região: escolha uma área geográfica perto de seus usuários para minimizar o tempo de transmissão e a latência da rede.
*  Regiões emparelhadas do Azure: escolha regiões emparelhadas dentro de sua área geográfica escolhida para garantir atualizações de plataforma coordenadas e esforços de recuperação priorizados, se necessário.
*  Disponibilidade do serviço: decida se suas regiões emparelhadas devem ser executadas em alta/quente, quente/quente ou quente/frio.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Usar o Gerenciador de tráfego do Azure para rotear o tráfego

O [Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-overview.md) fornece balanceamento de carga de tráfego baseado em DNS e pode distribuir o tráfego de rede entre várias regiões.  Use o Gerenciador de tráfego do Azure para direcionar os clientes para a instância mais próxima do serviço de nuvem Spring do Azure para eles.  Para obter melhor desempenho e redundância, direcione todo o tráfego do aplicativo por meio do Gerenciador de tráfego do Azure antes de enviá-lo para o serviço de nuvem do Azure Spring.

Se você tiver aplicativos do Azure Spring Cloud em várias regiões, use o Gerenciador de tráfego do Azure para controlar o fluxo de tráfego para seus aplicativos em cada região.  Defina um ponto de extremidade do Gerenciador de tráfego do Azure para cada serviço usando o IP do serviço. Os clientes devem se conectar a um nome DNS do Gerenciador de tráfego do Azure apontando para o serviço de nuvem Spring do Azure.  O Gerenciador de tráfego do Azure equilibra o tráfego entre os pontos de extremidade definidos.  Se um desastre ocorrer um data center, o Gerenciador de tráfego do Azure direcionará o tráfego dessa região para seu par, garantindo a continuidade do serviço.