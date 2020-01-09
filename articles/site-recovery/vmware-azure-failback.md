---
title: Fazer failback de VMs VMware/servidores físicos do Azure com Azure Site Recovery
description: Saiba como fazer failback para o site local após o failover no Azure, durante a recuperação de desastre de VMs VMware e servidores físicos para o Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: cd4cc90fb102d517a47ba458619e22b8921dd498
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495321"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>Failback de VMs do VMware para o site local

Este artigo descreve como fazer failback de VMs do Azure para um site local, seguindo o [failover](site-recovery-failover.md) de VMs locais para o Azure com [Azure site Recovery](site-recovery-overview.md). Após o failback para o local, você habilita a replicação para que as VMs locais iniciem a replicação no Azure.

## <a name="before-you-start"></a>Antes de começar

1. Saiba mais sobre o [failback do VMware](failover-failback-overview.md#vmwarephysical-reprotectionfailback). 
2. Verifique se você analisou e concluiu as etapas para [se preparar para o failback](vmware-azure-prepare-failback.md)e se todos os componentes necessários foram implantados. Os componentes incluem um servidor de processo no Azure, um servidor de destino mestre local e uma conexão VPN site a site (ou emparelhamento privado do ExpressRoute) para failback.
3. Verifique se você concluiu os [requisitos](vmware-azure-reprotect.md#before-you-begin) de nova proteção e failback e se [habilitou](vmware-azure-reprotect.md#enable-reprotection) a nova proteção de VMs do Azure, para que elas estejam replicando do Azure para o site local. As VMs devem estar em um estado replicado para fazer failback.




## <a name="run-a-failover-to-fail-back"></a>Executar um failover para failback

1. Verifique se as VMs do Azure são protegidas novamente e replicando para o site local. 
    - Uma VM precisa de pelo menos um ponto de recuperação para realizar o failback.
    - Se você fizer failback de um plano de recuperação, todos os computadores no plano deverão ter pelo menos um ponto de recuperação.
2. No cofre > **itens replicados**, selecione a VM. Clique com o botão direito do mouse na VM > **failover não planejado**.
3. Em **confirmar failover**, verifique a direção do failover (do Azure).
4. Selecione o ponto de recuperação que pretende utilizar para a ativação pós-falha.
    - Recomendamos que você use o ponto de recuperação **mais recente** . O ponto consistente com o aplicativo está atrás do último ponto no tempo e causa perda de dados.
    - O **mais recente** é um ponto de recuperação consistente com falhas.
    - Com o **mais recente**, uma VM faz failover para seu ponto no tempo mais recente disponível. Se você tiver um grupo de replicação para consistência de várias VMs em um plano de recuperação, cada VM no grupo passará por failover para seu último ponto independente no tempo.
    - Se você usar um ponto de recuperação consistente com o aplicativo, cada VM fará failback para o último ponto disponível. Se um plano de recuperação tiver um grupo de replicação, cada grupo será recuperado para seu ponto de recuperação disponível comum.
5. O failover começa. Site Recovery desliga as VMs do Azure.
6. Após a conclusão do failover, verifique se tudo está funcionando conforme o esperado. Verifique se as VMs do Azure estão desligadas. 
7. Com tudo verificado, clique com o botão direito do mouse na VM > **confirmar**para concluir o processo de failover. A confirmação remove a VM do Azure com failover. 

> [!NOTE]
> Para VMs do Windows, Site Recovery desabilita as ferramentas do VMware durante o failover. Durante o failback da VM do Windows, as ferramentas do VMware são habilitadas novamente. 




## <a name="reprotect-from-on-premises-to-azure"></a>Proteger novamente do local para o Azure

Depois de confirmar o failback, as VMs do Azure são excluídas. A VM está de volta no site local, mas não está protegida. Para iniciar a replicação de VMs para o Azure novamente, da seguinte maneira:

1. No cofre > **itens replicados**, selecione VMs com failback e, em seguida, selecione **proteger novamente**.
2. Especifique o servidor de processo que é usado para enviar dados de volta ao Azure.
3. Selecione **OK** para iniciar o trabalho de nova proteção.

> [!NOTE]
> Depois que uma VM local é iniciada, leva até 15 minutos para que o agente seja registrado novamente no servidor de configuração. Durante esse tempo, a nova proteção falhará e retornará uma mensagem de erro informando que o agente não está instalado. Se isso ocorrer, aguarde alguns minutos e proteja novamente.

## <a name="next-steps"></a>Passos seguintes

Após a conclusão do trabalho de nova proteção, a VM local está replicando para o Azure. Conforme necessário, você pode [executar outro failover](site-recovery-failover.md) no Azure.

