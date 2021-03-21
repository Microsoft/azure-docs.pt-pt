---
title: Gestor de Tráfego do Azure | Microsoft Docs
description: Este artigo mostra uma descrição geral do Gestor de Tráfego do Azure. Descubra se é a escolha certa para o tráfego de utilizador de equilíbrio de carga para a sua aplicação.
services: traffic-manager
author: duongau
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2021
ms.author: duau
ms.openlocfilehash: 8c8897218b153c8584c89abab98934268ccd555d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182167"
---
# <a name="what-is-traffic-manager"></a>O que é o Gestor de Tráfego?
Azure Traffic Manager é um equilibrador de carga baseado em DNS. Este serviço permite-lhe distribuir tráfego para as suas aplicações viradas para o público em todas as regiões globais do Azure. O Traffic Manager também fornece aos seus pontos finais públicos uma elevada disponibilidade e uma capacidade de resposta rápida.

O Gestor de Tráfego utiliza o DNS para direcionar os pedidos do cliente para o ponto final de serviço apropriado com base num método de encaminhamento de tráfego. O gestor de tráfego também fornece monitorização de saúde para cada ponto final. O ponto final pode ser qualquer serviço virado para a Internet hospedado dentro ou fora de Azure. O Gestor de Tráfego proporciona vários [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md) e [opções de monitorização de pontos finais](traffic-manager-monitoring.md) para satisfazer diferentes necessidades das aplicações e modelos de ativação pós-falha automática. O Gestor de Tráfego é resiliente a falhas, incluindo a falhas numa região do Azure inteira.

>[!NOTE]
> O Azure oferece um conjunto de soluções de balanceamento de carga totalmente geridas para os seus cenários. 
> * Se pretender carregar o equilíbrio entre os seus servidores numa região na camada de aplicação, reveja o [Gateway de Aplicações](../application-gateway/overview.md).
> * Se precisar de otimizar o encaminhamento global do seu tráfego web e otimizar o desempenho e fiabilidade do utilizador final de topo através de uma rápida falha global, consulte [a Porta da Frente](../frontdoor/front-door-overview.md).
> * Para equilibrar a carga da camada de rede, reveja [o Balanceador de Carga](../load-balancer/load-balancer-overview.md). 
> 
> Os seus cenários de ponta a ponta podem beneficiar de combinar estas soluções conforme necessário.
> Para uma comparação de opções de equilíbrio de carga Azure, consulte [a visão geral das opções de equilíbrio de carga em Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

O Traffic Manager oferece as seguintes funcionalidades:

## <a name="increase-application-availability"></a>Aumentar a disponibilidade das aplicações

O Gestor de Tráfego proporciona elevada disponibilidade às suas aplicações críticas ao monitorizar os seus pontos finais e disponibilizar a ativação pós-falha automática quando um daqueles falha.
    
## <a name="improve-application-performance"></a>Melhorar o desempenho das aplicações

O Azure permite-lhe executar serviços e websites em datacenters localizados em todo o mundo. O Traffic Manager pode melhorar a capacidade de resposta do seu website direcionando o tráfego para o ponto final com a menor latência.

## <a name="service-maintenance-without-downtime"></a>Manutenção de serviço sem tempo de inatividade

Pode ter planeado a manutenção nas suas aplicações sem tempo de inatividade. O Gestor de Tráfego pode direcionar o tráfego para pontos finais alternativos enquanto a manutenção está em andamento.

## <a name="combine-hybrid-applications"></a>Combinar aplicações híbridas

O Gestor de Tráfego suporta pontos finais externos não Azure, o que permite que seja utilizado com implementações de cloud híbrida e no local, incluindo os cenários de "[burst-to-cloud](https://azure.microsoft.com/overview/what-is-cloud-bursting/)", “migrar para a cloud” e “ativação pós-falha para a cloud”.

## <a name="distribute-traffic-for-complex-deployments"></a>Distribuir o tráfego para implementações complexas

Utilizando [perfis aninhados de Traffic Manager,](traffic-manager-nested-profiles.md)podem ser combinados múltiplos métodos de encaminhamento de tráfego para criar regras sofisticadas e flexíveis para escalar às necessidades de implementações maiores e mais complexas.

## <a name="pricing"></a>Preços

Para obter informações sobre preços, veja [Preços de Gestor de Tráfego](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um perfil do Gestor de Tráfego](./quickstart-create-traffic-manager-profile.md).
- Saiba [como funciona o Gestor de Tráfego](traffic-manager-how-it-works.md).
- Veja as [perguntas mais frequentes](traffic-manager-FAQs.md) sobre o Gestor de Tráfego.