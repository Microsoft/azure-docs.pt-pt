---
title: Faça failover e proteja novamente as VMs do Azure replicadas para uma região do Azure secundária para recuperação de desastre com o serviço de Azure Site Recovery.
description: Saiba como fazer failover e proteger novamente as VMs do Azure replicadas para uma região secundária do Azure para recuperação de desastres, com o serviço de Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9bc0d25e19ad3412e62eb3386b0faf3ae5d2a444
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782599"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>Fazer failover e proteger novamente as VMs do Azure entre regiões

Este tutorial descreve como fazer failover de uma VM (máquina virtual) do Azure para uma região do Azure secundária com o serviço de [Azure site Recovery](site-recovery-overview.md) . Depois de ter feito o failover, proteja novamente a VM. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Realizar a ativação pós-falha da VM do Azure
> * Proteja novamente a VM do Azure secundária, para que ela seja replicada para a região primária.

> [!NOTE]
> Este tutorial contém o caminho mais simples com as configurações padrão e a personalização mínima. Para cenários mais complexos, use os artigos em "como" para VMs do Azure.


## <a name="prerequisites"></a>Pré-requisitos

- Antes de começar, examine as [perguntas](site-recovery-faq.md#failover) frequentes sobre o failover.
- Certifique-se de que concluiu um [teste de recuperação após desastre](azure-to-azure-tutorial-dr-drill.md) para verificar se está tudo a funcionar conforme esperado.
- Verifique as propriedades da VM antes de executar a ativação pós-falha de teste. A VM tem de cumprir os [Requisitos do Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Realize uma ativação pós-falha para a região secundária

1. Nos **Itens replicados**, selecione a VM em que pretende realizar a ativação pós-falha > **Ativação pós-falha**

   ![Ativação pós-falha](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. Em **Ativação pós-falha**, selecione um **Ponto de Recuperação** para o qual irá realizar a ativação pós-falha. Pode utilizar uma das opções seguintes:

   * **Mais recente** (padrão): Processa todos os dados no serviço de Site Recovery e fornece o RPO (objetivo de ponto de recuperação) mais baixo.
   * **Mais recente processado**: Reverte a máquina virtual para o último ponto de recuperação que foi processado pelo serviço Site Recovery.
   * **Personalizado**: Executa o failover para um ponto de recuperação específico. Esta opção é útil para realizar uma ativação pós-falha de teste.

3. Selecione **desligar o computador antes do início do failover** se desejar que site Recovery tente fazer um desligamento das VMs de origem antes de disparar o failover. O desligamento ajuda a garantir que não haja perda de dados. A ativação pós-falha continua, mesmo que o encerramento falhe. Site Recovery não limpa a origem após o failover.

4. Siga o progresso da ativação pós-falha na página **Tarefas**.

5. Após a ativação pós-falha, valide a máquina virtual ao iniciar sessão. Se pretender voltar a outro ponto de recuperação para a máquina virtual, então pode utilizar a opção **Alterar ponto de recuperação**.

6. Quando estiver satisfeito com a ativação pós-falha da máquina virtual, pode **Consolidar** a ativação pós-falha.
   Ao consolidar elimina todos os pontos de recuperação disponíveis com o serviço. Agora você não poderá alterar o ponto de recuperação.

> [!NOTE]
> Quando você faz failover de uma VM para a qual adiciona um disco depois de habilitar a replicação para a VM, os pontos de replicação mostrarão os discos que estão disponíveis para recuperação. Por exemplo, se uma VM tiver um único disco e você adicionar um novo, os pontos de replicação criados antes de adicionar o disco mostrarão que o ponto de replicação consiste em "1 de 2 discos".

![Fazer failover com um disco adicionado](./media/azure-to-azure-tutorial-failover-failback/failover-added.png)

## <a name="reprotect-the-secondary-vm"></a>Voltar a proteger a VM secundária

Após a ativação pós-falha da VM, tem de voltar a protege-la, para que esta replique novamente para a região primária.

1. Certifique-se de que a VM está no estado **Ativação pós-falha consolidada** e verifique se a região primária está disponível, e poderá criar e aceder a novos recursos na mesma.
2. No **Cofre** > **Itens Replicados**, clique com o botão direito do rato na VM em que foi realizada a ativação pós-falha e, em seguida, selecione **Voltar a Proteger**.

   ![Clique com o botão direito do rato para voltar a proteger](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Verifique se a direção da proteção, da região secundária para a primária, já está selecionada.
3. Veja as informações **Grupo de recursos, Rede, Armazenamento e Conjuntos de disponibilidade**. Todos os recursos marcados como novos são criados como parte da operação de nova proteção.
4. Clique em **OK** para acionar uma tarefa de reproteção. Esta tarefa realiza o seeding do site de destino com os dados mais recentes. Em seguida, replica as diferenças para a região primária. A VM está agora num estado protegido.

## <a name="next-steps"></a>Passos seguintes
- Após a nova proteção, [saiba como](azure-to-azure-tutorial-failback.md) fazer failback para a região primária quando ela estiver disponível.
- [Saiba mais](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) sobre o fluxo de nova proteção.
