---
title: Falha em VMware VMs/servidores físicos de Azure com recuperação do site Azure
description: Aprenda a falhar no local após a falha no Azure, durante a recuperação de VMware VMs e servidores físicos para Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: aed015b67aa36e7678b31d7f2f047cb1e77c6a3d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84485318"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>Falha em VMware VMs para o local do local

Este artigo descreve como ressarçar os VMs do Azure para um site no local, após [a falha](site-recovery-failover.md) dos VMs no local para Azure com [Azure Site Recovery](site-recovery-overview.md). Depois de falhar no local, permite a replicação de modo a que os VMs no local comecem a replicar-se para Azure.

## <a name="before-you-start"></a>Antes de começar

1. Saiba mais sobre [o failback da VMware](failover-failback-overview.md#vmwarephysical-reprotectionfailback). 
2. Certifique-se de que reviu e completou os passos para preparar o [failback](vmware-azure-prepare-failback.md), e que todos os componentes necessários estão implantados. Os componentes incluem um servidor de processo em Azure, um servidor-alvo principal no local e uma ligação site-to-site VPN (ou espreitamento privado ExpressRoute) para falha.
3. Certifique-se de que preencheu os [requisitos](vmware-azure-reprotect.md#before-you-begin) de reproteção e recuo, e que [permitiu a reproteção](vmware-azure-reprotect.md#enable-reprotection) de VMs Azure, para que eles estejam a replicar de Azure para o local no local. Os VMs devem estar num estado replicado, é uma ordem para falhar.




## <a name="run-a-failover-to-fail-back"></a>Executar um falhanço para falhar de volta

1. Certifique-se de que os VMs Azure são reprotegidos e replicados no local.
    - Um VM precisa de pelo menos um ponto de recuperação para falhar.
    - Se falhares num plano de recuperação, todas as máquinas do plano devem ter pelo menos um ponto de recuperação.
2. No cofre > **itens replicados,** selecione o VM. Clique com o botão direito na VM > **Failover Não Planeada**.
3. Em **Confirmação de Falha,** verifique a direção de falha (a partir de Azure).
4. Selecione o ponto de recuperação que pretende utilizar para a ativação pós-falha.
    - Recomendamos que utilize o último ponto **de** recuperação. O ponto consistente da aplicação está por trás do ponto mais recente no tempo, e causa alguma perda de dados.
    - **O mais recente** é um ponto de recuperação consistente.
    - Com **o Mais Recente**, um VM falha no seu ponto mais recente disponível no tempo. Se tiver um grupo de replicação para consistência multi-VM dentro de um plano de recuperação, cada VM do grupo falha no seu ponto mais recente independente no tempo.
    - Se utilizar um ponto de recuperação consistente com aplicações, cada VM falha no seu ponto mais recente disponível. Se um plano de recuperação tiver um grupo de replicação, cada grupo recupera para o seu ponto de recuperação comum disponível.
5. O fracasso começa. A Recuperação do Local encerra os VMs Azure.
6. Depois de falhar, verifique se está tudo funcionando como esperado. Verifique se os VMs Azure estão desligados. 
7. Com tudo verificado, clique à direita no VM > **Commit**, para terminar o processo de failover. O Commit remove o Azure VM falhado. 

> [!NOTE]
> Para os VMs do Windows, a Recuperação do Site desativa as ferramentas VMware durante o failover. Durante o recuo do Windows VM, as ferramentas VMware voltam a ativar-se. 




## <a name="reprotect-from-on-premises-to-azure"></a>Reprotegido de instalações para Azure

Depois de cometer o failback, os VMs Azure são eliminados. O VM está de volta ao local, mas não está protegido. Para começar a replicar VMs para Azure novamente, o seguinte:

1. No cofre > **itens replicados,** selecione VMs ressarntes falhados e, em seguida, selecione **Re-Protect**.
2. Especifique o servidor de processo que é usado para enviar dados de volta para Azure.
3. Selecione **OK** para iniciar o trabalho de reproteção.

> [!NOTE]
> Após o início de um VM no local, o agente demora até 15 minutos para o agente voltar a registar-se no servidor de configuração. Durante este tempo, a reproteção falha e devolve uma mensagem de erro afirmando que o agente não está instalado. Se isto acontecer, aguarde alguns minutos e reproteja.

## <a name="next-steps"></a>Passos seguintes

Após o fim do trabalho reprotegido, o VM no local está a replicar-se para a Azure. Se necessário, [podes correr outra falha](site-recovery-failover.md) para o Azure.

