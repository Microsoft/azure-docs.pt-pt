---
title: Gestor de Tráfego do Azure | Microsoft Docs
description: Este artigo mostra uma descrição geral do Gestor de Tráfego do Azure. Saiba se esta é a escolha certa para fazer o balanceamento de carga do tráfego dos utilizadores para a sua aplicação.
services: traffic-manager
author: duongau
manager: twooley
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2019
ms.author: duau
ms.openlocfilehash: e2a4db1404709dadb2500df29f3f7acf8787c2b2
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185736"
---
# <a name="what-is-traffic-manager"></a>O que é o Gestor de Tráfego?
O Gestor de Tráfego do Azure é um balanceador de carga de tráfego baseado em DNS que lhe permite distribuir o tráfego de forma otimizada para serviços nas regiões globais do Azure, proporcionando, ao mesmo tempo, elevada disponibilidade e capacidade de resposta.

O Gestor de Tráfego utiliza o DNS para direcionar os pedidos do cliente para o ponto final de serviço mais adequado com base num método de encaminhamento de tráfego e no estado de funcionamento dos pontos finais. Os pontos finais são serviços com acesso à Internet alojados dentro ou fora do Azure. O Gestor de Tráfego proporciona vários [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md) e [opções de monitorização de pontos finais](traffic-manager-monitoring.md) para satisfazer diferentes necessidades das aplicações e modelos de ativação pós-falha automática. O Gestor de Tráfego é resiliente a falhas, incluindo a falhas numa região do Azure inteira.

>[!NOTE]
> O Azure oferece um conjunto de soluções de balanceamento de carga totalmente geridas para os seus cenários. Se estiver à procura de terminação de protocolo TLS (Transport Layer Security) (“descarga de SSL”) ou de processamento de camada de aplicação por pedido HTTP/HTTPS, reveja [Gateway de Aplicação](../application-gateway/overview.md). Se procura o equilíbrio regional da carga, reveja [o Balanceador de Carga](../load-balancer/load-balancer-overview.md). Combinar estas soluções conforme necessário poderá trazer benefícios aos seus cenários completos.
>
> Para uma comparação de opções de equilíbrio de carga Azure, consulte [a visão geral das opções de equilíbrio de carga em Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

O Traffic Manager oferece as seguintes funcionalidades:

## <a name="increase-application-availability"></a>Aumentar a disponibilidade das aplicações

O Gestor de Tráfego proporciona elevada disponibilidade às suas aplicações críticas ao monitorizar os seus pontos finais e disponibilizar a ativação pós-falha automática quando um daqueles falha.
    
## <a name="improve-application-performance"></a>Melhorar o desempenho das aplicações

O Azure permite-lhe executar serviços cloud ou sites em datacenters em todo o mundo. O Gestor de Tráfego melhora a capacidade de resposta das aplicações ao direcionar o tráfego para o ponto final com a latência de rede mais baixa para o cliente.

## <a name="perform-service-maintenance-without-downtime"></a>Realizar a manutenção de serviços sem tempo de inatividade

Pode realizar operações de manutenção planeada nas suas aplicações sem tempo de inatividade. O Gestor de Tráfego pode direcionar o tráfego para pontos finais alternativos enquanto a manutenção está em andamento.

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