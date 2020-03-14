---
title: Sobre a utilização da ExpressRoute com a Recuperação do Site Azure
description: Descreve como utilizar o Azure ExpressRoute com o serviço de recuperação de locais do Azure para recuperação e migração de desastres.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: e4525bdc6165e8e736db5f539c764d25250cb248
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79258008"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure ExpressRoute com recuperação do site Azure

O Microsoft Azure ExpressRoute permite-lhe expandir as redes no local para a cloud da Microsoft através de uma ligação privada facilitada por um fornecedor de conectividade. Com o ExpressRoute, pode ligar aos serviços cloud da Microsoft, tais como o Microsoft Azure, o Office 365 e o Dynamics 365.

Este artigo descreve como pode utilizar o Azure ExpressRoute com a Recuperação do Site Azure para recuperação e migração de desastres.

## <a name="expressroute-circuits"></a>Circuitos ExpressRoute

Um circuito do ExpressRoute representa uma conexão lógica entre a infraestrutura no local e serviços cloud da Microsoft através de um fornecedor de conectividade. É possível pedir vários circuitos do ExpressRoute. Cada circuito pode estar as mesmas ou em diferentes regiões e pode ser ligado a local através de fornecedores de conectividade diferentes. Saiba mais sobre os circuitos ExpressRoute [aqui](../expressroute/expressroute-circuit-peerings.md).

Um circuito ExpressRoute tem vários domínios de encaminhamento associados a ele. Saiba mais sobre e compare os domínios de encaminhamento expressRoute [aqui](../expressroute/expressroute-circuit-peerings.md#peeringcompare).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>No local para a replicação de Azure com ExpressRoute

A Recuperação do Site Azure permite a recuperação de desastres e migração para Azure para [máquinas virtuais Hyper-V](hyper-v-azure-architecture.md)no local, [máquinas virtuais VMware](vmware-azure-architecture.md)e [servidores físicos.](physical-azure-architecture.md) Para todos os cenários do Azure, os dados de replicação são enviados e armazenados numa conta de Armazenamento Azure. Durante a replicação, não se paga nenhuma carga virtual de máquina. Quando executa uma falha no Azure, a Recuperação do Site cria automaticamente máquinas virtuais Azure IaaS.

A Recuperação do Site replica dados para uma conta de Armazenamento Azure ou réplica de Disco Gerido na região de Azure alvo sobre um ponto final público. Para utilizar o ExpressRoute para o tráfego de replicação de recuperação do site, pode utilizar o [peering da Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) ou um [público](../expressroute/about-public-peering.md) existente (depreciado para novas criações). O olhar da Microsoft é o domínio de encaminhamento recomendado para replicação. Note que a replicação não é suportada sobre o peering privado.

Certifique-se de que os [requisitos de rede](vmware-azure-configuration-server-requirements.md#network-requirements) para o Servidor de Configuração também estão satisfeitos. A conectividade com URLs específicos é exigida pelo Servidor de Configuração para a orquestração da replicação da Recuperação do Site. A ExpressRoute não pode ser utilizada para esta conectividade. 

Caso utilize procuração no local e deseje utilizar o ExpressRoute para o tráfego de replicação, precisa de configurar a lista de bypass proxy nos Servidores de Configuração e servidores de processos. Siga os passos abaixo:

- Baixe a ferramenta PsExec [daqui](https://aka.ms/PsExec) para aceder ao contexto do utilizador do Sistema.
- Open Internet Explorer no contexto do utilizador do sistema executando a seguinte linha de comando psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- Adicionar definições de procuração em IE
- Na lista de bypass, adicione o URL de armazenamento Azure *.blob.core.windows.net

Isto garantirá que apenas o tráfego de replicação flui através do ExpressRoute enquanto a comunicação pode passar por procuração.

Depois de máquinas virtuais ou servidores falharem numa rede virtual Azure, pode aceder-lhes através de [um epeering privado](../expressroute/expressroute-circuit-peerings.md#privatepeering). 

O cenário combinado está representado no seguinte diagrama: ![On-premises-to-Azure com expressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Réplica de Azure para Azure com ExpressRoute

A Recuperação do Sítio Azure permite a recuperação de desastres de [máquinas virtuais Azure.](azure-to-azure-architecture.md) Dependendo se as suas máquinas virtuais Azure utilizam [discos geridos azure,](../virtual-machines/windows/managed-disks-overview.md)os dados de replicação são enviados para uma conta de Armazenamento Azure ou réplica de Disco Gerido na região do Azure alvo. Embora os pontos finais de replicação sejam públicos, o tráfego de replicação para a replicação do Azure VM, por padrão, não atravessa a Internet, independentemente da região do Azure em que a rede virtual de origem existe. Pode substituir a rota do sistema padrão do Azure para o prefixo de endereço 0.0.0.0/0 com uma [rota personalizada](../virtual-network/virtual-networks-udr-overview.md#custom-routes) e desviar o tráfego de VM para um aparelho virtual de rede no local (NVA), mas esta configuração não é recomendada para a replicação da Recuperação do Local. Se estiver a utilizar rotas personalizadas, deverá [criar um ponto final](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) de serviço de rede virtual na sua rede virtual para "Armazenamento" para que o tráfego de replicação não saia do limite Azure.

Para a recuperação de desastres da VM Azure, por defeito, o ExpressRoute não é necessário para a replicação. Depois de as máquinas virtuais falharem na região-alvo do Azure, pode aceder-lhes através de [um epeering privado](../expressroute/expressroute-circuit-peerings.md#privatepeering). Note que os preços de transferência de dados se aplicam independentemente do modo de replicação de dados nas regiões do Azure.

Se já está a utilizar o ExpressRoute para ligar do seu centro de dados no local aos VMs Azure na região de origem, pode planear restabelecer a conectividade ExpressRoute na região alvo de failover. Pode utilizar o mesmo circuito ExpressRoute para se ligar à região alvo através de uma nova ligação de rede virtual ou utilizar um circuito e ligação expressRoute separados para a recuperação de desastres. Os diferentes cenários possíveis são descritos [aqui.](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute)

Pode replicar máquinas virtuais Azure para qualquer região azure dentro do mesmo aglomerado geográfico que [aqui](../site-recovery/azure-to-azure-support-matrix.md#region-support)descrito. Se a região de Azure alvo escolhida não estiver dentro da mesma região geopolítica que a fonte, poderá ser necessário permitir o ExpressRoute Premium. Para mais detalhes, consulte as [localizações do ExpressRoute](../expressroute/expressroute-locations.md) e [os preços expressRoute.](https://azure.microsoft.com/pricing/details/expressroute/)

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [os circuitos ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Saiba mais sobre os domínios de [encaminhamento ExpressRoute](../expressroute/expressroute-circuit-peerings.md#peeringcompare).
- Saiba mais sobre as localizações do [ExpressRoute.](../expressroute/expressroute-locations.md)
- Saiba mais sobre a recuperação de desastres das [máquinas virtuais Azure com expressRoute.](azure-vm-disaster-recovery-with-expressroute.md)
