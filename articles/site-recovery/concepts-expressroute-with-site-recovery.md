---
title: Sobre a utilização do ExpressRoute com a Recuperação do Site Azure
description: Descreve como usar o Azure ExpressRoute com o serviço de recuperação do local de Azure para recuperação de desastres e migração.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: 12bec5af95e7da595d5af09fe9020992b1cf839d
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367997"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure ExpressRoute com recuperação do local de Azure

O Microsoft Azure ExpressRoute permite-lhe expandir as redes no local para a cloud da Microsoft através de uma ligação privada facilitada por um fornecedor de conectividade. Com o ExpressRoute, pode estabelecer ligação aos serviços cloud da Microsoft, como o Microsoft Azure, o Microsoft 365 e o Dynamics 365.

Este artigo descreve como pode usar a Azure ExpressRoute com Azure Site Recovery para recuperação de desastres e migração.

## <a name="expressroute-circuits"></a>Circuitos do ExpressRoute

Um circuito ExpressRoute representa uma ligação lógica entre a sua infraestrutura no local e os serviços de cloud da Microsoft através de um fornecedor de conectividade. Pode encomendar vários circuitos ExpressRoute. Cada circuito pode estar nas mesmas regiões ou diferentes, e pode ser conectado às suas instalações através de diferentes fornecedores de conectividade. Saiba mais sobre os circuitos ExpressRoute [aqui.](../expressroute/expressroute-circuit-peerings.md)

Um circuito ExpressRoute tem vários domínios de encaminhamento associados. Saiba mais sobre e compare os domínios de encaminhamento ExpressRoute [aqui.](../expressroute/expressroute-circuit-peerings.md#peeringcompare)

## <a name="on-premises-to-azure-replication-with-expressroute"></a>No local para a replicação do Azure com o ExpressRoute

A recuperação do site Azure permite a recuperação de desastres e migração para Azure para [máquinas virtuais Hiper-V](hyper-v-azure-architecture.md)no local, [máquinas virtuais VMware](vmware-azure-architecture.md)e [servidores físicos](physical-azure-architecture.md). Para todos os cenários do Azure, os dados de replicação são enviados e armazenados numa conta de Armazenamento Azure. Durante a replicação, não se paga qualquer carga de máquina virtual. Quando corre uma falha no Azure, a Recuperação do Site cria automaticamente máquinas virtuais Azure IaaS.

A Recuperação do Site replica dados numa conta de Armazenamento Azure ou réplica do Disco Gerido na região de Azure alvo sobre um ponto final público. Para utilizar o Tráfego de Replicação do ExpressRoute para recuperação de locais, pode utilizar [o espreitamento](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) da Microsoft ou um [espremo público](../expressroute/about-public-peering.md) existente (precotado para novas criações). O espreitamento da Microsoft é o domínio de encaminhamento recomendado para replicação. Note que a replicação não é suportada sobre o espreitamento privado.

Certifique-se de que os [requisitos de rede](vmware-azure-configuration-server-requirements.md#network-requirements) para o servidor de configuração também estão cumpridos. A conectividade com URLs específicos é necessária pelo Servidor de Configuração para orquestração da replicação da recuperação do local. O ExpressRoute não pode ser utilizado para esta conectividade. 

Caso utilize procuração no local e deseje utilizar o ExpressRoute para tráfego de replicação, tem de configurar a lista de bypass Proxy nos Servidores de Configuração e servidores de processos. Siga os passos abaixo:

- Descarregue a ferramenta PsExec [daqui](/sysinternals/downloads/psexec) para aceder ao contexto do utilizador do Sistema.
- Abra o Internet Explorer no contexto do utilizador do sistema executando a seguinte linha de comando psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- Adicionar definições de procuração em IE
- Na lista de bypass, adicione o URL de armazenamento Azure *.blob.core.windows.net

Isto garantirá que apenas o tráfego de replicação flui através do ExpressRoute enquanto a comunicação pode passar por procuração.

Depois de máquinas virtuais ou servidores falharem numa rede virtual Azure, pode aceder-lhes utilizando [o espreitamento privado](../expressroute/expressroute-circuit-peerings.md#privatepeering). 

O cenário combinado está representado no seguinte diagrama: ![ On-to-Azure com ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Azure to Azure replicação com ExpressRoute

A recuperação do local de azure permite a recuperação de desastres de [máquinas virtuais Azure](azure-to-azure-architecture.md). Dependendo se as suas máquinas virtuais Azure utilizam [discos geridos Azure,](../virtual-machines/managed-disks-overview.md)os dados de replicação são enviados para uma conta de Armazenamento Azure ou réplica de Disco Gerido na região de Azure alvo. Embora os pontos finais de replicação sejam públicos, o tráfego de replicação para a replicação de VM Azure, por padrão, não atravessa a Internet, independentemente da região de Azure em que a rede virtual de origem existe. Pode sobrepor a rota do sistema padrão da Azure para o prefixo de endereço 0.0.0.0/0 com uma [rota personalizada](../virtual-network/virtual-networks-udr-overview.md#custom-routes) e desviar o tráfego VM para um aparelho virtual de rede no local (NVA), mas esta configuração não é recomendada para a replicação da Recuperação do Local. Se estiver a utilizar rotas personalizadas, deverá [criar um ponto final de serviço de rede virtual](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) na sua rede virtual para "Armazenamento" para que o tráfego de replicação não saia do limite Azure.

Para a recuperação de desastres Azure VM, por padrão, o ExpressRoute não é necessário para a replicação. Depois de as máquinas virtuais falharem na região de Azure alvo, pode aceder-lhes através de [um esprevamento privado.](../expressroute/expressroute-circuit-peerings.md#privatepeering) Note que os preços de transferência de dados se aplicam independentemente do modo de replicação de dados em todas as regiões do Azure.

Se já está a utilizar o ExpressRoute para ligar do seu datacenter no local aos VMs Azure na região de origem, pode planear restabelecer a conectividade ExpressRoute na região alvo de failover. Pode utilizar o mesmo circuito ExpressRoute para ligar à região alvo através de uma nova ligação de rede virtual ou utilizar um circuito e ligação ExpressRoute separados para a recuperação de desastres. Os diferentes cenários possíveis são descritos [aqui.](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute)

Você pode replicar máquinas virtuais Azure para qualquer região de Azure dentro do mesmo cluster geográfico que detalhado [aqui.](../site-recovery/azure-to-azure-support-matrix.md#region-support) Se a região de Azure, alvo escolhida, não estiver dentro da mesma região geopolítica que a fonte, poderá ser necessário viabilizar o ExpressRoute Premium. Para mais detalhes, consulte as [localizações do ExpressRoute](../expressroute/expressroute-locations.md) e [os preços do ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [os circuitos ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Saiba mais sobre [os domínios de encaminhamento ExpressRoute](../expressroute/expressroute-circuit-peerings.md#peeringcompare).
- Saiba mais sobre [as localizações do ExpressRoute.](../expressroute/expressroute-locations.md)
- Saiba mais sobre a recuperação de desastres das [máquinas virtuais Azure com o ExpressRoute.](azure-vm-disaster-recovery-with-expressroute.md)