---
title: Sobre como usar o Azure ExpressRoute com Azure Site Recovery para recuperação de desastres e migração | Microsoft Docs
description: Descreve como usar o Azure ExpressRoute com o serviço de Azure Site Recovery para recuperação de desastres e migração.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: 97aea824fac60f8bed71971a416f12e8df0e5e64
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333058"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure ExpressRoute com Azure Site Recovery

O Microsoft Azure ExpressRoute permite-lhe expandir as redes no local para a cloud da Microsoft através de uma ligação privada facilitada por um fornecedor de conectividade. Com o ExpressRoute, pode ligar aos serviços cloud da Microsoft, tais como o Microsoft Azure, o Office 365 e o Dynamics 365.

Este artigo descreve como você pode usar o Azure ExpressRoute com Azure Site Recovery para recuperação de desastres e migração.

## <a name="expressroute-circuits"></a>Circuitos do ExpressRoute

Um circuito do ExpressRoute representa uma conexão lógica entre sua infraestrutura local e os serviços de nuvem da Microsoft por meio de um provedor de conectividade. Você pode ordenar vários circuitos do ExpressRoute. Cada circuito pode estar na mesma região ou em regiões diferentes e pode ser conectado a seus locais por meio de provedores de conectividade diferentes. Saiba mais sobre os circuitos do ExpressRoute [aqui](../expressroute/expressroute-circuit-peerings.md).

Um circuito do ExpressRoute tem vários domínios de roteamento associados a ele. Saiba mais sobre e compare os domínios de roteamento do ExpressRoute [aqui](../expressroute/expressroute-circuit-peerings.md#peeringcompare).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Replicação local para o Azure com o ExpressRoute

O Azure Site Recovery permite a recuperação de desastres e a migração para o Azure para [máquinas virtuais Hyper-V](hyper-v-azure-architecture.md)locais, [máquinas virtuais VMware](vmware-azure-architecture.md)e [servidores físicos](physical-azure-architecture.md). Para todos os cenários locais para o Azure, os dados de replicação são enviados e armazenados em uma conta de armazenamento do Azure. Durante a replicação, você não paga nenhuma cobrança de máquina virtual. Quando você executa um failover no Azure, Site Recovery cria automaticamente máquinas virtuais IaaS do Azure.

Site Recovery replica dados para uma conta de armazenamento do Azure ou um disco gerenciado de réplica na região do Azure de destino em um ponto de extremidade público. Para usar o ExpressRoute para Site Recovery tráfego de replicação, você pode utilizar o [emparelhamento da Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) ou um [emparelhamento público](../expressroute/expressroute-circuit-peerings.md#publicpeering) existente (preterido para novas criações). O emparelhamento da Microsoft é o domínio de roteamento recomendado para replicação. Observe que a replicação não é compatível com o emparelhamento privado.

Verifique se os [requisitos de rede](vmware-azure-configuration-server-requirements.md#network-requirements) do servidor de configuração também são atendidos. A conectividade com URLs específicas é exigida pelo servidor de configuração para orquestração da replicação do Site Recovery. O ExpressRoute não pode ser usado para essa conectividade. 

Caso você use proxy no local e deseje usar o ExpressRoute para tráfego de replicação, será necessário configurar a lista de bypass de proxy no servidor de configuração e nos servidores de processo. Siga os passos abaixo:

- Baixe a ferramenta PsExec [aqui](https://aka.ms/PsExec) para acessar o contexto do usuário do sistema.
- Abra o Internet Explorer no contexto do usuário do sistema executando a seguinte linha de comando PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe"
- Adicionar configurações de proxy no IE
- Na lista de bypass, adicione a URL de armazenamento do Azure *. blob.core.windows.net

Isso garantirá que apenas o tráfego de replicação flua por meio do ExpressRoute, enquanto a comunicação pode passar pelo proxy.

Depois que máquinas virtuais ou servidores fizerem failover para uma rede virtual do Azure, você poderá acessá-los usando o [emparelhamento privado](../expressroute/expressroute-circuit-peerings.md#privatepeering). 

O cenário combinado é representado no seguinte diagrama: ![On-local-to-Azure com ExpressRoute @ no__t-1

## <a name="azure-to-azure-replication-with-expressroute"></a>Replicação do Azure para o Azure com o ExpressRoute

Azure Site Recovery permite a recuperação de desastre de [máquinas virtuais do Azure](azure-to-azure-architecture.md). Dependendo se suas máquinas virtuais do Azure usam o [Managed disks do Azure](../virtual-machines/windows/managed-disks-overview.md), os dados de replicação são enviados para uma conta de armazenamento do Azure ou um disco gerenciado de réplica na região do Azure de destino. Embora os pontos de extremidade de replicação sejam públicos, o tráfego de replicação para replicação de VM do Azure, por padrão, não atravessa a Internet, independentemente da região do Azure em que a rede virtual de origem existe. Você pode substituir a rota do sistema padrão do Azure para o prefixo de endereço 0.0.0.0/0 por uma [rota personalizada](../virtual-network/virtual-networks-udr-overview.md#custom-routes) e desviar o tráfego da VM para uma NVA (solução de virtualização de rede) local, mas essa configuração não é recomendada para replicação de site Recovery. Se você estiver usando rotas personalizadas, deverá [criar um ponto de extremidade de serviço de rede virtual](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) em sua rede virtual para "armazenamento" para que o tráfego de replicação não deixe o limite do Azure.

Para a recuperação de desastre de VM do Azure, por padrão, o ExpressRoute não é necessário para a replicação. Depois que as máquinas virtuais fizerem failover para a região do Azure de destino, você poderá acessá-las usando o [emparelhamento privado](../expressroute/expressroute-circuit-peerings.md#privatepeering). Observe que os preços de transferência de dados se aplicam independentemente do modo de replicação de dados entre regiões do Azure.

Se você já estiver usando o ExpressRoute para se conectar do seu datacenter local às VMs do Azure na região de origem, poderá planejar a redefinição da conectividade do ExpressRoute na região de destino do failover. Você pode usar o mesmo circuito de ExpressRoute para se conectar à região de destino por meio de uma nova conexão de rede virtual ou utilizar um circuito e conexão de ExpressRoute separados para a recuperação de desastre. Os diferentes cenários possíveis são descritos [aqui](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute).

Você pode replicar máquinas virtuais do Azure para qualquer região do Azure dentro do mesmo cluster geográfico, conforme detalhado [aqui](../site-recovery/azure-to-azure-support-matrix.md#region-support). Se a região do Azure de destino escolhida não estiver na mesma região geopolítica que a origem, talvez seja necessário habilitar o ExpressRoute Premium. Para obter mais detalhes, verifique os [locais do expressroute](../expressroute/expressroute-locations.md) e os [preços do expressroute](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre os [circuitos do ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Saiba mais sobre os [domínios de roteamento do ExpressRoute](../expressroute/expressroute-circuit-peerings.md#peeringcompare).
- Saiba mais sobre [locais do ExpressRoute](../expressroute/expressroute-locations.md).
- Saiba mais sobre a recuperação de desastre de [máquinas virtuais do Azure com o ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
