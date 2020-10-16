---
title: Grupos de Segurança de Rede com Recuperação do Site Azure Microsoft Docs
description: Descreve como usar grupos de segurança de rede com recuperação do site Azure para recuperação de desastres e migração
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: harshacs
ms.openlocfilehash: 904bc63ed2a135cdcadad75e96acd6fe3ca39039
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90069684"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Grupos de Segurança de Rede com o Azure Site Recovery

Os Grupos de Segurança da Rede são utilizados para limitar o tráfego de rede a recursos numa rede virtual. Um [Grupo de Segurança de Rede (NSG)](../virtual-network/security-overview.md#network-security-groups) contém uma lista de regras de segurança que permitem ou negam o tráfego de rede de entrada ou saída com base no endereço IP de origem ou destino, porta e protocolo.

Sob o modelo de implementação do Gestor de Recursos, os NSGs podem ser associados a sub-redes ou interfaces de rede individuais. Quando um NSG é associado a uma sub-rede, as regras são aplicadas a todos os recursos ligados à mesma. O tráfego pode ainda ser restringido associando também um NSG a interfaces de rede individuais dentro de uma sub-rede que já tem um NSG associado.

Este artigo descreve como pode utilizar grupos de segurança de rede com recuperação do site Azure.

## <a name="using-network-security-groups"></a>Utilização de grupos de segurança de rede

Uma sub-rede individual pode ter zero, ou um, NSG associado. Uma interface de rede individual também pode ter zero, ou um, NSG associado. Assim, você pode efetivamente ter dupla restrição de tráfego para uma máquina virtual associando um NSG primeiro a uma sub-rede, e depois outro NSG à interface de rede do VM. A aplicação das regras do NSG neste caso depende da direção do tráfego e da prioridade das regras de segurança aplicadas.

Considere um exemplo simples com uma máquina virtual da seguinte forma:
-    A máquina virtual é colocada dentro da **Sub-rede Contoso.**
-    **A Subnet Contoso** está associada à **Subnet NSG.**
-    A interface de rede VM está adicionalmente associada à **VM NSG**.

![NSG com recuperação do local](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

Neste exemplo, para o tráfego de entrada, o Subnet NSG é avaliado primeiro. Qualquer tráfego permitido através da Subnet NSG é então avaliado pela VM NSG. O inverso é aplicável ao tráfego de saída, com vM NSG sendo avaliado primeiro. Qualquer tráfego permitido através do VM NSG é então avaliado pela Subnet NSG.

Isto permite a aplicação da regra de segurança granular. Por exemplo, é melhor permitir o acesso à Internet de entrada a alguns VMs de aplicação (como VMs frontend) numa sub-rede, mas restringir o acesso à Internet de entrada a outros VMs (como base de dados e outros VMs de backend). Neste caso, pode ter uma regra mais branda sobre o Subnet NSG, permitindo o tráfego de internet, e restringir o acesso a VMs específicos, negando o acesso em VM NSG. O mesmo pode ser aplicado para o tráfego de saída.

Ao configurar estas configurações de NSG, certifique-se de que as prioridades corretas são aplicadas às [regras de segurança](../virtual-network/security-overview.md#security-rules). As regras são processadas por ordem de prioridade, sendo os números mais baixos processados antes dos mais elevados, uma vez que têm uma prioridade superior. Quando o tráfego corresponder a uma regra, o processamento para. Como resultado, qualquer regra que exista com prioridades inferiores (números mais elevados) e que tenham os mesmos atributos das regras com prioridades superiores não é processada.

É possível que não esteja sempre ciente de que os grupos de segurança de rede estão aplicados, quer à interface de rede, quer à sub-rede. Pode verificar as regras agregadas aplicadas a uma interface de rede visualizando as [regras de segurança eficazes](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) para uma interface de rede. Também pode utilizar a capacidade de [verificação](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) do fluxo IP no [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) para determinar se a comunicação é permitida ou a partir de uma interface de rede. A ferramenta indica se é permitida a comunicação e que regra de segurança de rede permite ou nega o tráfego.

## <a name="on-premises-to-azure-replication-with-nsg"></a>No local para a replicação do Azure com a NSG

A recuperação do site Azure permite a recuperação de desastres e migração para Azure para [máquinas virtuais Hiper-V](hyper-v-azure-architecture.md)no local, [máquinas virtuais VMware](vmware-azure-architecture.md)e [servidores físicos](physical-azure-architecture.md). Para todos os cenários do Azure, os dados de replicação são enviados e armazenados numa conta de Armazenamento Azure. Durante a replicação, não se paga qualquer carga de máquina virtual. Quando corre uma falha no Azure, a Recuperação do Site cria automaticamente máquinas virtuais Azure IaaS.

Uma vez criados VMs após a falha no Azure, os NSGs podem ser usados para limitar o tráfego de rede à rede virtual e VMs. A Recuperação do Site não cria NSGs como parte da operação de failover. Recomendamos a criação dos NSGs Azure necessários antes de iniciar o failover. Em seguida, pode associar os NSGs a falhass automáticas sobre VMs durante o failover, utilizando scripts de automação com os poderosos planos de [recuperação](site-recovery-create-recovery-plans.md)do Site Recovery .

Por exemplo, se a configuração VM pós-falha for semelhante ao cenário de [exemplo](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) acima descrito:
-    Você pode criar **Contoso VNet** e **Contoso Subnet** como parte do planeamento de DR na região de Azure alvo.
-    Também pode criar e configurar tanto **a Subnet NSG** como a **VM NSG** como parte do mesmo planeamento dr.
-    **A subnet NSG** pode então ser imediatamente associada à **Subnet Contoso,** uma vez que tanto o NSG como a sub-rede já estão disponíveis.
-    **O VM NSG** pode ser associado com VMs durante o failover usando planos de recuperação.

Uma vez criados e configurados os NSGs, recomendamos a execução de um [teste falhado](site-recovery-test-failover-to-azure.md) para verificar as associações NSG scripted e a conectividade VM pós-failover.

## <a name="azure-to-azure-replication-with-nsg"></a>Azure to Azure replicação com NSG

A recuperação do local de azure permite a recuperação de desastres de [máquinas virtuais Azure](azure-to-azure-architecture.md). Ao permitir a replicação de VMs Azure, a Recuperação do Site pode criar as redes virtuais réplicas (incluindo sub-redes e sub-redes de gateway) na região alvo e criar os mapeamentos necessários entre as redes virtuais de origem e alvo. Também pode pré-criar as redes e sub-redes laterais-alvo e utilizar o mesmo, permitindo a replicação. A Recuperação do Sítio não cria quaisquer VMs na região de Azure alvo antes [de falhar](azure-to-azure-tutorial-failover-failback.md).

Para a replicação do Azure VM, certifique-se de que as regras NSG na região de Azure de origem permitem [a conectividade de saída](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) para o tráfego de replicação. Também pode testar e verificar estas regras necessárias através desta [configuração NSG exemplo](azure-to-azure-about-networking.md#example-nsg-configuration).

A Recuperação do Site não cria ou replica os NSGs como parte da operação de falha. Recomendamos a criação dos NSGs necessários na região de Azure alvo antes de iniciar o failover. Em seguida, pode associar os NSGs a falhass automáticas sobre VMs durante o failover, utilizando scripts de automação com os poderosos planos de [recuperação](site-recovery-create-recovery-plans.md)do Site Recovery .

Considerando o [cenário de exemplo](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) descrito anteriormente:
-    A Recuperação do Site pode criar réplicas da Subnet **Contoso VNet** e **Contoso** na região alvo de Azure quando a replicação está ativada para o VM.
-    Pode criar as réplicas desejadas da **Subnet NSG** e **VM NSG** (nomeadas, por exemplo, **Subnet NSG alvo** e **Target VM NSG,** respectivamente) na região de Azure alvo, permitindo quaisquer regras adicionais necessárias na região alvo.
-    **O Subnet NSG alvo** pode então ser imediatamente associado à sub-rede da região alvo, uma vez que tanto o NSG como a sub-rede já estão disponíveis.
-    **O VM NSG-alvo** pode ser associado com VMs durante o failover usando planos de recuperação.

Uma vez criados e configurados os NSGs, recomendamos a execução de um [teste falhado](azure-to-azure-tutorial-dr-drill.md) para verificar as associações NSG scripted e a conectividade VM pós-failover.

## <a name="next-steps"></a>Passos seguintes
-    Saiba mais sobre [grupos de segurança de rede.](../virtual-network/security-overview.md#network-security-groups)
-    Saiba mais sobre [as regras de segurança](../virtual-network/security-overview.md#security-rules)da NSG.
-    Saiba mais sobre [regras de segurança eficazes](../virtual-network/diagnose-network-traffic-filter-problem.md) para um NSG.
-    Saiba mais sobre [planos de recuperação](site-recovery-create-recovery-plans.md) para automatizar o failover da aplicação.
