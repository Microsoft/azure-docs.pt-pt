---
title: Grupos de segurança com o Azure Site Recovery de rede | Documentos da Microsoft
description: Descreve como utilizar grupos de segurança de rede com o Azure Site Recovery para recuperação após desastre e migração
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 0c06283080a4ee51f863714e4c515672299b420d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799291"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Grupos de segurança de rede com o Azure Site Recovery

Grupos de segurança de rede são utilizados para limitar o tráfego de rede aos recursos numa rede virtual. R [grupo de segurança de rede (NSG)](../virtual-network/security-overview.md#network-security-groups) contém uma lista de regras de segurança que permitem ou negam o tráfego de rede de entrada ou saída com base na origem ou endereço IP de destino, portas e protocolos.

No modelo de implementação Resource Manager, os NSGs podem ser associados a sub-redes ou a interfaces de rede individuais. Quando um NSG é associado a uma sub-rede, as regras são aplicadas a todos os recursos ligados à mesma. Tráfego adicional pode ser restringido ao associar também um NSG a interfaces de rede individuais dentro de uma sub-rede que já tenha um NSG associado.

Este artigo descreve como pode utilizar grupos de segurança de rede com o Azure Site Recovery.

## <a name="using-network-security-groups"></a>Utilizar grupos de segurança de rede

Uma sub-rede individual pode ter zero ou um NSG de associados. Uma interface de rede individuais também pode ter zero ou um NSG de associados. Por isso, pode ter efetivamente restrição de tráfego dupla para uma máquina virtual ao associar um NSG pela primeira vez a uma sub-rede e, em seguida, outra NSG à interface de rede da VM. A aplicação de regras NSG em vez disso depende da direção do tráfego e a prioridade de regras de segurança aplicadas.

Considere um exemplo simples com uma máquina virtual da seguinte forma:
-   A máquina virtual é colocada dentro da **Contoso sub-rede**.
-   **Sub-rede de contoso** é associado **NSG de sub-rede**.
-   A interface de rede VM é associada adicionalmente **NSG de VM**.

![NSG com o Site Recovery](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

Neste exemplo, para tráfego de entrada, o NSG de sub-rede é avaliado primeiro. Qualquer tráfego permitido através do NSG de sub-rede, em seguida, é avaliado por NSG de VM. O inverso é aplicável para tráfego de saída, com o NSG de VM que está a ser avaliada em primeiro lugar. Qualquer tráfego permitido através do NSG de VM, em seguida, é avaliado por NSG de sub-rede.

Isso permite que aplicativos de regra de segurança granular. Por exemplo, pode querer permitir o acesso de internet de entrada para algumas VMs de aplicação (por exemplo, o front-end VMs) numa sub-rede, mas restringir o acesso de internet de entrada para outras VMs (por exemplo, a base de dados e outra VMs de back-end). Nesse caso pode ter uma regra mais permissiva no NSG de sub-rede, a permitir o tráfego de internet e restringir o acesso a VMs específicas por negar o acesso no NSG de VM. O mesmo pode ser aplicado para tráfego de saída.

Quando configurar essas configurações de NSG, certifique-se de que as prioridades corretas são aplicadas para o [regras de segurança](../virtual-network/security-overview.md#security-rules). As regras são processadas por ordem de prioridade, sendo os números mais baixos processados antes dos mais elevados, uma vez que têm uma prioridade superior. Quando o tráfego corresponder a uma regra, o processamento para. Como resultado, qualquer regra que exista com prioridades inferiores (números mais elevados) e que tenham os mesmos atributos das regras com prioridades superiores não é processada.

É possível que não esteja sempre ciente de que os grupos de segurança de rede estão aplicados, quer à interface de rede, quer à sub-rede. Pode verificar as regras agregadas que aplicado a uma interface de rede, visualizando o [regras de segurança efetivas](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) para uma interface de rede. Também pode utilizar o [fluxo de IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) capacidade no [observador de rede do Azure](../network-watcher/network-watcher-monitoring-overview.md) para determinar se a comunicação é permitida de ou para uma interface de rede. A ferramenta indica se é permitida a comunicação e que regra de segurança de rede permite ou nega o tráfego.

## <a name="on-premises-to-azure-replication-with-nsg"></a>No local para replicação do Azure com o NSG

O Azure Site Recovery permite a recuperação após desastre e migração para o Azure para on-premises [máquinas de virtuais de Hyper-V](hyper-v-azure-architecture.md), [máquinas virtuais VMware](vmware-azure-architecture.md), e [servidores físicos](physical-azure-architecture.md). Para todos os locais para cenários do Azure, os dados de replicação são enviados e armazenados numa conta de armazenamento do Azure. Durante a replicação, não tenha despesas quaisquer custos de máquina virtual. Quando executar uma ativação pós-falha para o Azure, o Site Recovery cria automaticamente máquinas de virtuais de IaaS do Azure.

Assim que as VMs foram criadas após a ativação pós-falha para o Azure, podem ser utilizados NSGs para limitar o tráfego de rede para a rede virtual e VMs. Recuperação de sites não cria NSGs como parte da operação de ativação pós-falha. Recomendamos que crie os NSGs necessários do Azure antes de iniciar a ativação pós-falha. Pode, em seguida, associar NSGs para efetuar a ativação pós-falha de VMs automaticamente durante a ativação pós-falha, através de scripts de automatização com o Site Recovery poderosas [planos de recuperação](site-recovery-create-recovery-plans.md).

Por exemplo, se a configuração da VM após a ativação pós-falha é semelhante para o [cenário de exemplo](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) detalhado acima:
-   Pode criar **Contoso VNet** e **Contoso sub-rede** como parte do planejamento no destino região do Azure de DR.
-   Também pode criar e configurar ambos **NSG de sub-rede** , bem como **NSG de VM** como parte do mesmo DR planejamento.
-   **NSG de sub-rede** pode, em seguida, ser imediatamente associadas **Contoso sub-rede**, como o NSG e a sub-rede já estão disponíveis.
-   **O NSG de VM** podem ser associados a VMs durante a ativação pós-falha com planos de recuperação.

Assim que os NSGs são criados e configurados, recomendamos a execução um [ativação pós-falha de teste](site-recovery-test-failover-to-azure.md) verificar com script associações do NSG e conectividade de VMS após a ativação pós-falha.

## <a name="azure-to-azure-replication-with-nsg"></a>Replicação do Azure para o Azure com o NSG

O Azure Site Recovery permite a recuperação após desastre de [máquinas virtuais do Azure](azure-to-azure-architecture.md). Quando ativar a replicação das VMs do Azure, Site Recovery pode criar redes virtuais (incluindo sub-redes e sub-redes do gateway) de réplica na região de destino e criar os mapeamentos necessários entre a origem e redes virtuais de destino. Também pode criar previamente as sub-redes e redes de lado de destino e utilizar o mesmo ao ativar a replicação. Recuperação de sites não cria todas as VMs na antes de região do Azure de destino [ativação pós-falha](azure-to-azure-tutorial-failover-failback.md).

Para a replicação de VM do Azure, certifique-se de que as regras do NSG na origem da região do Azure permitem [conectividade de saída](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) para o tráfego de replicação. Também pode testar e verifique se estas regras necessárias através deste [exemplo de configuração de NSG](azure-to-azure-about-networking.md#example-nsg-configuration).

Recuperação de sites não criam nem replicar NSGs como parte da operação de ativação pós-falha. Recomendamos que crie os NSGs necessários no destino região do Azure antes de iniciar a ativação pós-falha. Pode, em seguida, associar NSGs para efetuar a ativação pós-falha de VMs automaticamente durante a ativação pós-falha, através de scripts de automatização com o Site Recovery poderosas [planos de recuperação](site-recovery-create-recovery-plans.md).

Considerando a [cenário de exemplo](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) descrito anteriormente:
-   Recuperação de sites pode criar réplicas dos **Contoso VNet** e **Contoso sub-rede** sobre a região do Azure quando a replicação está ativada para a VM de destino.
-   Pode criar as réplicas desejadas dos **NSG de sub-rede** e **NSG de VM** (com o nome, por exemplo, **NSG de sub-rede de destino** e **NSG de VM de destino**, respectivamente) no destino região do Azure, permitindo a quaisquer regras adicionais necessárias na região de destino.
-   **O NSG de sub-rede de destino** , em seguida, pode ser imediatamente associado à sub-rede de região de destino, como o NSG e a sub-rede já estão disponíveis.
-   **O NSG de VM de destino** podem ser associados a VMs durante a ativação pós-falha com planos de recuperação.

Assim que os NSGs são criados e configurados, recomendamos a execução um [ativação pós-falha de teste](azure-to-azure-tutorial-dr-drill.md) verificar com script associações do NSG e conectividade de VMS após a ativação pós-falha.

## <a name="next-steps"></a>Passos Seguintes
-   Saiba mais sobre [grupos de segurança de rede](../virtual-network/security-overview.md#network-security-groups).
-   Saiba mais sobre o NSG [regras de segurança](../virtual-network/security-overview.md#security-rules).
-   Saiba mais sobre [regras de segurança efetivas](../virtual-network/diagnose-network-traffic-filter-problem.md) para um NSG.
-   Saiba mais sobre [planos de recuperação](site-recovery-create-recovery-plans.md) para automatizar a ativação pós-falha do aplicativo.
