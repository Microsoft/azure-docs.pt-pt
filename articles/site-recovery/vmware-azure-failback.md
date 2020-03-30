---
title: Fail back VMware VMs/servidores físicos de Azure com recuperação do site Azure
description: Saiba como voltar ao site no local depois de falhar com o Azure, durante a recuperação de vMware vMware e servidores físicos para o Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: cd4cc90fb102d517a47ba458619e22b8921dd498
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495321"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>Fail back VMware VMs s ao site no local

Este artigo descreve como reaver os VMs Azure para um local no local, após a [falha](site-recovery-failover.md) dos VMs no local para Azure com a Recuperação do [Site Azure.](site-recovery-overview.md) Depois de falhar nas instalações, permite a replicação para que os VMs no local comecem a replicar-se para Azure.

## <a name="before-you-start"></a>Antes de começar

1. Saiba sobre o [failback vMware](failover-failback-overview.md#vmwarephysical-reprotectionfailback). 
2. Certifique-se de que reviu e completou os passos para [se preparar para o failback](vmware-azure-prepare-failback.md), e que todos os componentes necessários estão implantados. Os componentes incluem um servidor de processo em Azure, um servidor alvo principal no local, e uma ligação vpn site-to-site (ou peering privado ExpressRoute) para falha.
3. Certifique-se de que preencheu os [requisitos](vmware-azure-reprotect.md#before-you-begin) para a reproteção e recuo, e que [permitiu a reproteção](vmware-azure-reprotect.md#enable-reprotection) de VMs Azure, para que se replicassem de Azure para o local no local. VMs devem estar em estado replicado é ordem para falhar.




## <a name="run-a-failover-to-fail-back"></a>Executar uma falha para falhar

1. Certifique-se de que os VMs Azure estão reprotegidos e replicando-se no local. 
    - Um VM precisa de pelo menos um ponto de recuperação para falhar.
    - Se falharum plano de recuperação, todas as máquinas do plano devem ter pelo menos um ponto de recuperação.
2. No cofre > **itens replicados,** selecione o VM. Clique à direita no VM > **Failover não planeado**.
3. Em **Confirmar Failover,** verifique a direção de failover (de Azure).
4. Selecione o ponto de recuperação que pretende utilizar para a ativação pós-falha.
    - Recomendamos que utilize **o** último ponto de recuperação. O ponto consistente com aplicações está por trás do último ponto no tempo, e causa alguma perda de dados.
    - **O mais recente** é um ponto de recuperação consistente.
    - Com o **Mais Recente,** um VM falha no seu mais recente ponto disponível no tempo. Se tiver um grupo de replicação para a consistência multi-VM dentro de um plano de recuperação, cada VM do grupo falha no seu último ponto independente no tempo.
    - Se utilizar um ponto de recuperação consistente com aplicações, cada VM falha no seu mais recente ponto disponível. Se um plano de recuperação tiver um grupo de replicação, cada grupo recupera para o seu ponto de recuperação comum disponível.
5. A falha começa. A Recuperação do Local encerra os VMs Azure.
6. Depois de concluídos, verifique se está tudo a funcionar como esperado. Verifique se os VMs Azure estão desligados. 
7. Com tudo verificado, clique à direita no VM > **Comprometer,** para terminar o processo de failover. Cometer remove o VM Azure falhado. 

> [!NOTE]
> Para os VMs do Windows, a Recuperação do Site desativa as ferramentas VMware durante a falha. Durante a falha do VM do Windows, as ferramentas VMware voltam a ser ativadas. 




## <a name="reprotect-from-on-premises-to-azure"></a>Reproteger das instalações para Azure

Após cometer o failback, os VMs Azure são eliminados. O VM está de volta ao local, mas não está protegido. Para começar a replicar vMs para Azure novamente, da seguinte forma:

1. No cofre > **itens replicados,** selecione VMs traseiros falhados e, em seguida, selecione **Re-Protect**.
2. Especifique o servidor de processo que é usado para enviar dados de volta para o Azure.
3. Selecione **OK** para iniciar o trabalho de reproteção.

> [!NOTE]
> Depois de um VM no local começar, leva até 15 minutos para o agente se registar de volta ao servidor de configuração. Durante este tempo, a reproteção falha e devolve uma mensagem de erro afirmando que o agente não está instalado. Se isto ocorrer, aguarde alguns minutos e reproteja.

## <a name="next-steps"></a>Passos seguintes

Após o trabalho de reproteção terminar, o VM no local está a replicar-se para O Azure. Se necessário, [podes passar outra falha](site-recovery-failover.md) para o Azure.

