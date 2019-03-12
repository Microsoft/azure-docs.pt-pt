---
title: Sobre como utilizar o Azure ExpressRoute com o Azure Site Recovery para a migração e de recuperação após desastre | Documentos da Microsoft
description: Descreve como utilizar o Azure ExpressRoute com o serviço Azure Site Recovery para a migração e de recuperação após desastre.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/8/2019
ms.author: mayg
ms.openlocfilehash: f8179f5e647039737a59afdd04d345bf465acfdf
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726350"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>O Azure ExpressRoute com o Azure Site Recovery

O Microsoft Azure ExpressRoute permite-lhe expandir as redes no local para a cloud da Microsoft através de uma ligação privada facilitada por um fornecedor de conectividade. Com o ExpressRoute, pode ligar aos serviços cloud da Microsoft, tais como o Microsoft Azure, o Office 365 e o Dynamics 365.

Este artigo descreve como pode utilizar o Azure ExpressRoute com o Azure Site Recovery para a migração e de recuperação após desastre.

## <a name="expressroute-circuits"></a>Circuitos do ExpressRoute

Um circuito do ExpressRoute representa uma conexão lógica entre a infraestrutura no local e serviços cloud da Microsoft através de um fornecedor de conectividade. É possível pedir vários circuitos do ExpressRoute. Cada circuito pode estar as mesmas ou em diferentes regiões e pode ser ligado a local através de fornecedores de conectividade diferentes. Saiba mais sobre os circuitos do ExpressRoute [aqui](../expressroute/expressroute-circuit-peerings.md).

## <a name="expressroute-routing-domains"></a>Domínios de encaminhamento do ExpressRoute

Um circuito do ExpressRoute tem vários domínios de encaminhamento associados a ele:
-   [Peering privado do Azure](../expressroute/expressroute-circuit-peerings.md#privatepeering) - serviços, ou seja, as máquinas virtuais (IaaS), de computação do Azure e serviços cloud (PaaS) que são implementados numa rede virtual podem ser ligados através do domínio de peering privado. O domínio de peering privado é considerado numa extensão fidedigna de sua rede principal para o Microsoft Azure.
-   [Peering público do Azure](../expressroute/expressroute-circuit-peerings.md#publicpeering) -serviços como Web sites, bases de dados SQL e armazenamento do Azure são oferecidos em endereços IP públicos. Em privado pode ligar a serviços alojados em endereços IP públicos, incluindo VIPs dos seus serviços cloud, através do domínio de encaminhamento de peering público. Peering público foi preterido para criações novo e Peering da Microsoft deve ser utilizado em vez disso, para serviços PaaS do Azure.
-   [Peering da Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) -conectividade ao Microsoft online services (serviços do Office 365, Dynamics 365 e PaaS do Azure) é através do peering da Microsoft. Peering da Microsoft é o domínio de encaminhamento recomendado para ligar aos serviços PaaS do Azure.

Saiba mais sobre e compare domínios de encaminhamento do ExpressRoute [aqui](../expressroute/expressroute-circuit-peerings.md#peeringcompare).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>No local para replicação do Azure com o ExpressRoute

O Azure Site Recovery permite a recuperação após desastre e migração para o Azure para on-premises [máquinas de virtuais de Hyper-V](hyper-v-azure-architecture.md), [máquinas virtuais VMware](vmware-azure-architecture.md), e [servidores físicos](physical-azure-architecture.md). Para todos os locais para cenários do Azure, os dados de replicação são enviados e armazenados numa conta de armazenamento do Azure. Durante a replicação, não tenha despesas quaisquer custos de máquina virtual. Quando executar uma ativação pós-falha para o Azure, o Site Recovery cria automaticamente máquinas de virtuais de IaaS do Azure.

O site Recovery replica os dados para uma conta de armazenamento do Azure através de um ponto final público. Para utilizar o ExpressRoute para replicação do Site Recovery, pode utilizar [peering público](../expressroute/expressroute-circuit-peerings.md#publicpeering) (preteridas para o novo criações) ou [peering da Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). Peering da Microsoft é o domínio de encaminhamento recomendado para a replicação. Certifique-se de que o [requisitos de sistema de rede](vmware-azure-configuration-server-requirements.md#network-requirements) também foram atendidos para a replicação. Depois de máquinas virtuais ou servidores de ativação pós-falha para uma rede virtual do Azure, pode acessá-los usando [peering privado](../expressroute/expressroute-circuit-peerings.md#privatepeering). Replicação não é suportada por peering privado.

O cenário combinado é representado no diagrama seguinte: ![No local-para-Azure com o ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Replicação do Azure para o Azure com o ExpressRoute

O Azure Site Recovery permite a recuperação após desastre de [máquinas virtuais do Azure](azure-to-azure-architecture.md). Consoante a utilização de máquinas de sua virtuais do Azure de software de se [Managed Disks do Azure](../virtual-machines/windows/managed-disks-overview.md), dados de replicação são enviados para uma conta de armazenamento do Azure ou a réplica do disco gerido no destino região do Azure. Embora os pontos de extremidade de replicação são públicos, o tráfego de replicação para a replicação de VM do Azure, por predefinição, atravessa a Internet, independentemente de qual a região do Azure a rede virtual de origem existe na. Pode substituir a rota de sistema padrão do Azure para o prefixo de endereço 0.0.0.0/0 com um [rota personalizada](../virtual-network/virtual-networks-udr-overview.md#custom-routes) e desviar o tráfego VM para uma aplicação de virtual de rede de no local (NVA), mas esta configuração não é recomendada para o Site Recovery replicação. Se estiver a utilizar rotas personalizadas, deve [criar um ponto de final de serviço de rede virtual](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) no seu virtual de rede para "Armazenamento", para que o tráfego de replicação não deixam o limite do Azure.

Para recuperação após desastre de VM do Azure, por predefinição, o ExpressRoute não é necessário para a replicação. Depois de máquinas virtuais de ativação pós-falha para o destino região do Azure, pode acessá-los usando [peering privado](../expressroute/expressroute-circuit-peerings.md#privatepeering).

Se já estiver a utilizar o ExpressRoute para ligar a partir do seu datacenter no local para as VMs do Azure na região de origem, pode planear para voltar a estabelecer conectividade do ExpressRoute na região de destino de ativação pós-falha. Pode utilizar o mesmo circuito do ExpressRoute para ligar para a região de destino através de uma ligação de rede virtual nova ou utilizar um circuito do ExpressRoute separado e a ligação para recuperação após desastre. Os diferentes cenários possíveis são descritos [aqui](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute).

Pode replicar máquinas virtuais do Azure para qualquer região do Azure dentro do mesmo cluster geográfico conforme detalhado [aqui](../site-recovery/azure-to-azure-support-matrix.md#region-support). Se a região do Azure de destino escolhido não estiver na mesma região geopolítica como a origem, poderá ter ativar o ExpressRoute Premium. Para obter mais detalhes, consulte [localizações do ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) e [preços do ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [circuitos do ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Saiba mais sobre [domínios de encaminhamento do ExpressRoute](../expressroute/expressroute-circuit-peerings.md#peeringcompare).
- Saiba mais sobre [localizações do ExpressRoute](../expressroute/expressroute-locations.md).
- Saiba mais sobre a recuperação após desastre de [máquinas virtuais do Azure com o ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
