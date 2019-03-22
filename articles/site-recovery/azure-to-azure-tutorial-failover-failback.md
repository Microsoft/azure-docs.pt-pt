---
title: Efetuar a ativação pós-falha e voltar a proteger VMs do Azure replicadas para uma região secundária do Azure para recuperação após desastre com o serviço Azure Site Recovery.
description: Saiba como efetuar a ativação pós-falha e voltar a proteger VMs do Azure replicadas para uma região secundária do Azure para recuperação após desastre, com o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/18/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c19e554571927efa907350df1f1fbbd8ff491c42
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314761"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>Ativação pós-falha e voltar a proteger as VMs do Azure entre regiões

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para a estratégia de recuperação após desastre, através da gestão e orquestração de replicação, ativação pós-falha e reativação pós-falha de máquinas no local e máquinas virtuais (VMs) do Azure.

Este tutorial descreve como fazer a ativação pós-falha de uma VM do Azure para uma região secundária do Azure. Depois que tive a ativação pós-falha, voltar a proteger a VM. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Realizar a ativação pós-falha da VM do Azure
> * Voltar a proteger a VM do Azure secundária, para que replique para a região primária.

> [!NOTE]
> Este tutorial contém o caminho mais simples com as predefinições e personalização mínima. Para cenários mais complexos, utilize os artigos em "Como para" para VMs do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que concluiu um [teste de recuperação após desastre](azure-to-azure-tutorial-dr-drill.md) para verificar se está tudo a funcionar conforme esperado.
- Verifique as propriedades da VM antes de executar a ativação pós-falha de teste. A VM tem de cumprir os [Requisitos do Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Realize uma ativação pós-falha para a região secundária

1. Nos **Itens replicados**, selecione a VM em que pretende realizar a ativação pós-falha > **Ativação pós-falha**

   ![Ativação pós-falha](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. Em **Ativação pós-falha**, selecione um **Ponto de Recuperação** para o qual irá realizar a ativação pós-falha. Pode utilizar uma das opções seguintes:

   * **Mais recente** (predefinição): Processa todos os dados no serviço Site Recovery e fornece o objetivo de ponto de recuperação (RPO) mais baixo.
   * **Processado mais recentemente**: Reverte a máquina virtual para o ponto de recuperação mais recente que foram processado pelo serviço Site Recovery.
   * **Custom**: Efetua a ativação pós-falha para um ponto de recuperação específico. Esta opção é útil para realizar uma ativação pós-falha de teste.

3. Selecione **encerrar a máquina antes de iniciar a ativação pós-falha** se pretender que o Site Recovery para tentar fazer um encerramento de VMs de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Recuperação de sites não apaga a origem após a ativação pós-falha.

4. Siga o progresso da ativação pós-falha na página **Tarefas**.

5. Após a ativação pós-falha, valide a máquina virtual ao iniciar sessão. Se pretender voltar a outro ponto de recuperação para a máquina virtual, então pode utilizar a opção **Alterar ponto de recuperação**.

6. Quando estiver satisfeito com a ativação pós-falha da máquina virtual, pode **Consolidar** a ativação pós-falha.
   Ao consolidar elimina todos os pontos de recuperação disponíveis com o serviço. Agora não será possível alterar o ponto de recuperação.

## <a name="reprotect-the-secondary-vm"></a>Voltar a proteger a VM secundária

Após a ativação pós-falha da VM, tem de voltar a protege-la, para que esta replique novamente para a região primária.

1. Certifique-se de que a VM está no estado **Ativação pós-falha consolidada** e verifique se a região primária está disponível, e poderá criar e aceder a novos recursos na mesma.
2. No **Cofre** > **Itens Replicados**, clique com o botão direito do rato na VM em que foi realizada a ativação pós-falha e, em seguida, selecione **Voltar a Proteger**.

   ![Clique com o botão direito do rato para voltar a proteger](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Certifique-se de que a direção da proteção, a região secundária para primária, já está selecionada.
3. Veja as informações **Grupo de recursos, Rede, Armazenamento e Conjuntos de disponibilidade**. Quaisquer recursos marcados como novos são criados como parte da operação de reproteção.
4. Clique em **OK** para acionar uma tarefa de reproteção. Esta tarefa realiza o seeding do site de destino com os dados mais recentes. Em seguida, replica as diferenças para a região primária. A VM está agora num estado protegido.

## <a name="next-steps"></a>Passos Seguintes
- Após proteger novamente [Saiba como](azure-to-azure-tutorial-failback.md) efetuar a ativação pós-falha para a região primária quando estiver disponível.
- [Saiba mais](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) sobre o fluxo de nova proteção.
