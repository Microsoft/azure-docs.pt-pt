---
title: Falhar e reproteger os VMs Azure replicados numa região secundária de Azure para recuperação de desastres com o serviço de recuperação de locais do Azure.
description: Aprenda a falhar e reproteja os VMs Azure replicados para uma região secundária de Azure para recuperação de desastres, com o serviço de recuperação de sítios azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9bc0d25e19ad3412e62eb3386b0faf3ae5d2a444
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "68782599"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>Falhar e reproteger VMs Azure entre regiões

Este tutorial descreve como falhar sobre uma máquina virtual Azure (VM) para uma região secundária de Azure com o serviço de Recuperação do [Local Azure.](site-recovery-overview.md) Depois de ter falhado, reprotege o VM. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Realizar a ativação pós-falha da VM do Azure
> * Reproteja o Secundário Azure VM, de modo a replicar-se para a região primária.

> [!NOTE]
> Este tutorial contém o caminho mais simples com definições padrão e personalização mínima. Para cenários mais complexos, utilize os artigos em 'How To' para VMs Azure.


## <a name="prerequisites"></a>Pré-requisitos

- Antes de começar, reveja [frequentemente perguntas](site-recovery-faq.md#failover) sobre o fracasso.
- Certifique-se de que concluiu um [teste de recuperação após desastre](azure-to-azure-tutorial-dr-drill.md) para verificar se está tudo a funcionar conforme esperado.
- Verifique as propriedades da VM antes de executar a ativação pós-falha de teste. A VM tem de cumprir os [Requisitos do Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Realize uma ativação pós-falha para a região secundária

1. Nos **Itens replicados**, selecione a VM em que pretende realizar a ativação pós-falha > **Ativação pós-falha**

   ![Ativação pós-falha](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. Em **Failover,** selecione um Ponto de **Recuperação** para falhar. Pode utilizar uma das opções seguintes:

   * **Mais recente** (padrão): Processa todos os dados do serviço de recuperação do site e fornece o menor Objetivo do Ponto de Recuperação (RPO).
   * **Mais recente processado**: Reverte a máquina virtual para o mais recente ponto de recuperação que foi processado pelo serviço de Recuperação do Site.
   * **Costume**: Falha num determinado ponto de recuperação. Esta opção é útil para realizar uma ativação pós-falha de teste.

3. Selecione **a máquina de desligar antes** de iniciar a falha se pretender que a Recuperação do Site tente fazer uma paragem dos VMs de origem antes de acionar a falha. A paragem ajuda a garantir a não perda de dados. A ativação pós-falha continua, mesmo que o encerramento falhe. A Recuperação do Local não limpa a fonte após a falha.

4. Siga o progresso da ativação pós-falha na página **Tarefas**.

5. Após a ativação pós-falha, valide a máquina virtual ao iniciar sessão. Se pretender voltar a outro ponto de recuperação para a máquina virtual, então pode utilizar a opção **Alterar ponto de recuperação**.

6. Quando estiver satisfeito com a ativação pós-falha da máquina virtual, pode **Consolidar** a ativação pós-falha.
   Ao consolidar elimina todos os pontos de recuperação disponíveis com o serviço. Não poderá agora mudar o ponto de recuperação.

> [!NOTE]
> Quando falhar um VM ao qual adiciona um disco depois de ter ativado a replicação para o VM, os pontos de replicação mostrarão os discos disponíveis para recuperação. Por exemplo, se um VM tiver um único disco e adicionar um novo, os pontos de replicação que foram criados antes de adicionar o disco mostrarão que o ponto de replicação consiste em "1 de 2 discos".

![Falhar com um disco adicionado](./media/azure-to-azure-tutorial-failover-failback/failover-added.png)

## <a name="reprotect-the-secondary-vm"></a>Voltar a proteger a VM secundária

Após a ativação pós-falha da VM, tem de voltar a protege-la, para que esta replique novamente para a região primária.

1. Certifique-se de que a VM está no estado **Ativação pós-falha consolidada** e verifique se a região primária está disponível, e poderá criar e aceder a novos recursos na mesma.
2. Em **vault** > **Replicated itens**, clique à direita no VM que foi falhado e, em seguida, selecione **Re-Protect**.

   ![Clique com o botão direito do rato para voltar a proteger](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Verifique se a direção de proteção, secundária à região primária, já está selecionada.
3. Veja as informações **Grupo de recursos, Rede, Armazenamento e Conjuntos de disponibilidade**. Quaisquer recursos marcados como novos são criados como parte da operação de reproteção.
4. Clique em **OK** para acionar uma tarefa de reproteção. Esta tarefa realiza o seeding do site de destino com os dados mais recentes. Em seguida, replica as diferenças para a região primária. A VM está agora num estado protegido.

## <a name="next-steps"></a>Passos seguintes
- Depois de reproteger, [aprenda a](azure-to-azure-tutorial-failback.md) falhar de volta à região primária quando estiver disponível.
- [Saiba mais](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) sobre o fluxo de reproteção.
