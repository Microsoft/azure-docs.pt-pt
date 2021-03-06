---
title: Azure DDoS Proteção boas práticas fundamentais
description: Aprenda as melhores práticas de segurança utilizando a proteção DDoS.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: ea5e9e9883c8c1da17cf99dabc72f2339c8dbe1a
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107102982"
---
# <a name="fundamental-best-practices"></a>Melhores práticas fundamentais

As seguintes secções dão orientações prescritivas para a construção de serviços de resiliência do DDoS em Azure.

## <a name="design-for-security"></a>Conceber para segurança

Garantir que a segurança é uma prioridade ao longo de todo o ciclo de vida de uma aplicação, desde a conceção e implementação até à implantação e operações. As aplicações podem ter bugs que permitem um volume relativamente baixo de pedidos para usar uma quantidade excessiva de recursos, resultando numa paragem de serviço. 

Para ajudar a proteger um serviço em execução no Microsoft Azure, deverá ter uma boa compreensão da arquitetura da sua aplicação e focar-se nos [cinco pilares da qualidade do software.](/azure/architecture/guide/pillars)
Deve conhecer os volumes de tráfego típicos, o modelo de conectividade entre a aplicação e outras aplicações, e os pontos finais de serviço que estão expostos à internet pública.

Garantir que uma aplicação é resistente o suficiente para lidar com uma negação de serviço que é direcionada para a aplicação em si é o mais importante. A segurança e a privacidade são integradas na plataforma Azure, a começar pelo Ciclo de [Vida para o Desenvolvimento de Segurança (SDL).](https://www.microsoft.com/sdl/default.aspx) O SDL aborda a segurança em todas as fases de desenvolvimento e garante que o Azure é continuamente atualizado para torná-lo ainda mais seguro.

## <a name="design-for-scalability"></a>Design para escalabilidade

A escalabilidade é o quão bem um sistema pode lidar com o aumento da carga. Desenhe as suas aplicações para [escalar horizontalmente](/azure/architecture/guide/design-principles/scale-out) para satisfazer a procura de uma carga amplificada, especificamente em caso de ataque DDoS. Se a sua aplicação depender de uma única instância de um serviço, cria um único ponto de falha. A provisionar várias instâncias torna o seu sistema mais resistente e escalável.

Para [o Azure App Service](../app-service/overview.md), selecione um plano de Serviço de [Aplicações](../app-service/overview-hosting-plans.md) que oferece várias instâncias. Para a Azure Cloud Services, configuure cada um dos seus papéis para usar [várias instâncias](../cloud-services/cloud-services-choose-me.md). Para [máquinas virtuais Azure,](../virtual-machines/index.yml)certifique-se de que a sua arquitetura de máquina virtual (VM) inclui mais de um VM e que cada VM está incluído num [conjunto de disponibilidade.](../virtual-machines/windows/tutorial-availability-sets.md) Recomendamos a utilização [de conjuntos de escala de máquina virtual](../virtual-machine-scale-sets/overview.md) para capacidades de autoscalagem.

## <a name="defense-in-depth"></a>Defesa em profundidade

A ideia por trás da defesa em profundidade é gerir o risco usando diversas estratégias defensivas. Colocar as defesas de segurança numa aplicação reduz a hipótese de um ataque bem sucedido. Recomendamos que implemente designs seguros para as suas aplicações utilizando as capacidades incorporadas da plataforma Azure.

Por exemplo, o risco de ataque aumenta com o tamanho *(área de superfície)* da aplicação. Pode reduzir a área de superfície utilizando uma lista de aprovação para fechar o espaço de endereço IP exposto e portas de audição que não são necessárias nos equilibros de carga[(Azure Load Balancer](../load-balancer/quickstart-load-balancer-standard-public-portal.md) e [Azure Application Gateway).](../application-gateway/application-gateway-create-probe-portal.md) [Os grupos de segurança da rede (NSGs)](../virtual-network/network-security-groups-overview.md) são outra forma de reduzir a superfície de ataque.
Pode utilizar [etiquetas de serviço](../virtual-network/network-security-groups-overview.md#service-tags) e grupos de segurança de [aplicações](../virtual-network/network-security-groups-overview.md#application-security-groups) para minimizar a complexidade para criar regras de segurança e configurar a segurança da rede, como uma extensão natural da estrutura de uma aplicação.

Deve implementar os serviços Azure numa [rede virtual](../virtual-network/virtual-networks-overview.md) sempre que possível. Esta prática permite que os recursos de serviço se comuniquem através de endereços IP privados. O tráfego de serviço Azure de uma rede virtual utiliza endereços IP públicos como endereços IP de origem por padrão. A utilização [de pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço irá mudar o tráfego de serviço para utilizar endereços privados de rede virtual como endereços IP de origem quando estão a aceder ao serviço Azure a partir de uma rede virtual.

Vemos muitas vezes os recursos dos clientes no local a serem atacados juntamente com os seus recursos em Azure. Se estiver a ligar um ambiente no local ao Azure, recomendamos que minimize a exposição dos recursos no local à internet pública. Pode utilizar a escala e as capacidades avançadas de proteção DDoS da Azure, implantando as suas conhecidas entidades públicas em Azure. Como estas entidades acessíveis ao público são muitas vezes um alvo para ataques DDoS, colocá-los em Azure reduz o impacto nos seus recursos no local.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um plano de proteção DDoS](manage-ddos-protection.md).