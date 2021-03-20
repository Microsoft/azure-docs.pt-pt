---
title: Configurar o failover de Hiper-VMs para Azure na Recuperação do Local de Azure
description: Aprenda a falhar sobre Os VMs hiper-V para Azure com a recuperação do site Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b46a2ea12a697afde8223cc3595365c1286512c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86132457"
---
# <a name="fail-over-hyper-v-vms-to-azure"></a>Falha em VMs Hiper-V para Azure

Este tutorial descreve como falhar sobre VMs Hiper-V para Azure com [Azure Site Recovery](site-recovery-overview.md). Depois de feita a ativação pós-falha, vai fazer a reativação pós-falha para o seu site no local quando estiver disponível. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Verifique as propriedades Hyper-V VM para verificar conforme os requisitos do Azure.
> * Falha em VMs específicos para Azure.


Este é o quinto tutorial de uma série. Este tutorial parte do princípio de que já concluiu as tarefas dos tutoriais anteriores.    

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Preparar Hyper-V no local](./hyper-v-prepare-on-premises-tutorial.md)
3. Configurar recuperação após desastre para as [ VM de Hyper-V](./hyper-v-azure-tutorial.md) ou para as [VM de Hyper-V geridas nas System Center VMM clouds](./hyper-v-vmm-azure-tutorial.md)
4. [Executar um teste de recuperação após desastre](tutorial-dr-drill-azure.md)

[Saiba mais sobre](failover-failback-overview.md#types-of-failover) diferentes tipos de falha. Se quiser falhar em vários VMs num plano de recuperação, [reveja este artigo](site-recovery-failover.md).

## <a name="prepare-for-failover"></a>Preparar para ativação pós-falha 
Certifique-se de que não existem instantâneos na VM e de que a VM no local está desativada durante a reativação pós-falha. Desta forma, garante-se a consistência dos dados durante a replicação. Não ative a VM no local durante a reativação pós-falha. 

A ativação pós-falha e a reativação pós-falha têm quatro fases:

1. **Ativação pós-falha para o Azure**: VM de Hyper-V de ativação pós-falha do site no local para o Azure.
2. **Reativação pós-falha para o local**: as VM do Azure de ativação pós-falha para o seu site no local quando o site no local está disponível. Começa a sincronização de dados a partir do Azure para o local e, após a conclusão, apresenta as VM no local.  
3. **Replicação inversa das VM no local**: Depois de terem sido reativadas pós-falha para o local, efetue a replicação inversa das VM no local para iniciar a replicação das mesmas para o Azure.

## <a name="verify-vm-properties"></a>Verificar as propriedades da VM

Antes da ativação pós-falha verifique as propriedades da VM e certifique-se de que a VM está em conformidade com os [requisitos do Azure](hyper-v-azure-support-matrix.md#replicated-vms).

Em **Itens Protegidos**, clique em **Itens Replicados** > VM.

1. No painel **Item replicado**, existe um resumo das informações, do estado de funcionamento e dos pontos de recuperação mais recentes disponíveis da VM. Clique em **Propriedades** para ver mais detalhes.

1. Em **Computação e Rede**, pode modificar o nome do Azure, o grupo de recursos, o tamanho de destino, o [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) e as definições do disco gerido.

1. Pode ver e modificar as definições de rede, incluindo a rede/sub-rede na qual a VM do Azure será localizada após a ativação pós-falha e o endereço IP que será atribuído à mesma.

1. Em **Discos**, pode ver informações sobre o sistema operativo e os discos de dados na VM.

## <a name="fail-over-to-azure"></a>Ativação pós-falha para o Azure

1. Em **Definições**  >  **Itens Replicados,** clique no VM > **Failover**.
2. Na **Ativação pós-falha**, selecione o ponto de recuperação **Mais recente**. 
3. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. O Site Recovery tenta encerrar as VM de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode acompanhar o progresso falhado na página **Jobs.**
4. Depois de verificar a ativação pós-falha, clique em **Consolidar**. Todos os pontos de recuperação disponíveis são eliminados.

> [!WARNING]
> **Não cancele uma ativação pós-falha em curso**: Se cancelar uma ativação pós-falha que esteja em curso, esta é interrompida, mas a VM não volta a ser replicada.

## <a name="connect-to-failed-over-vm"></a>Ligue-se a VM falhado

1. Se pretender ligar-se aos VMs Azure após o failover utilizando o Remote Desktop Protocol (RDP) e o Secure Shell (SSH), [verifique se os requisitos foram cumpridos](failover-failback-overview.md#connect-to-azure-after-failover).
2. Depois de falhar, vá ao VM e valide [ligando-o.](../virtual-machines/windows/connect-logon.md)
3. Utilize **o ponto de recuperação de alteração** se quiser utilizar um ponto de recuperação diferente após a falha. Depois de cometer o failover no próximo passo, esta opção deixará de estar disponível.
4. Após validação, **selecione Comprometa-se** a finalizar o ponto de recuperação do VM após o failover.
5. Depois de se comprometer, todos os outros pontos de recuperação disponíveis são eliminados. Este passo completa o fracasso.

>[!TIP]
> Se encontrar problemas de conectividade após o failover, siga o [guia de resolução de problemas](site-recovery-failover-to-azure-troubleshoot.md).


## <a name="next-steps"></a>Passos seguintes

Após o failover, reprotete os VMs Azure para que se reproduzam de Azure para o local. Depois de os VM serem reprotegidos e replicarem-se no local, recuem do Azure quando estiverem prontos.
