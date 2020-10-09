---
title: Falha e reprotegido VMs Azure replicados numa região secundária de Azure para recuperação de desastres com o serviço de Recuperação do Local Azure.
description: Aprenda a falhar e reprotetegir VMs Azure replicados numa região secundária de Azure para recuperação de desastres, com o serviço de Recuperação do Local Azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 8d38aa513b0829c2626fcd4a92c40faabff1f83e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87502397"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>Ativação pós-falha e voltar a proteger as VMs do Azure entre regiões

Este tutorial descreve como falhar sobre uma máquina virtual Azure (VM) para uma região secundária de Azure com o serviço [de Recuperação do Local Azure.](site-recovery-overview.md) Depois de ter falhado, reprotete o VM. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Realizar a ativação pós-falha da VM do Azure
> * Reprotete o Azure VM secundário, para que se reproduza para a região primária.

> [!NOTE]
> Este tutorial contém o caminho mais simples com definições padrão e personalização mínima. Para cenários mais complexos, utilize os artigos em 'Como Fazer' para VMs Azure.


## <a name="prerequisites"></a>Pré-requisitos

- Antes de começar, reveja [frequentemente perguntas](site-recovery-faq.md#failover) sobre falhas.
- Certifique-se de que concluiu um [teste de recuperação após desastre](azure-to-azure-tutorial-dr-drill.md) para verificar se está tudo a funcionar conforme esperado.
- Verifique as propriedades da VM antes de executar a ativação pós-falha de teste. A VM tem de cumprir os [Requisitos do Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Realize uma ativação pós-falha para a região secundária

1. Nos **Itens replicados**, selecione a VM em que pretende realizar a ativação pós-falha > **Ativação pós-falha**

   ![Screenshot mostrando as opções failover para um VM.](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. Em **Ativação pós-falha**, selecione um **Ponto de Recuperação** para o qual irá realizar a ativação pós-falha. Pode utilizar uma das opções seguintes:

   * **Mais recente** (predefinição): Processa todos os dados do serviço de Recuperação de Sítio e fornece o menor Objetivo de Ponto de Recuperação (RPO).
   * **Mais recente processo**: Reverte a máquina virtual para o último ponto de recuperação que foi processado pelo serviço de Recuperação do Local.
   * **Costume**: Falha num determinado ponto de recuperação. Esta opção é útil para realizar uma ativação pós-falha de teste.

3. Selecione **desligar a máquina antes de começar a falhar** se quiser que a Recuperação do Local tente fazer uma paragem de VMs de origem antes de ativar a falha. O encerramento ajuda a garantir que não há perda de dados. A ativação pós-falha continua, mesmo que o encerramento falhe. A Recuperação do Local não limpa a fonte após a falha.

4. Siga o progresso da ativação pós-falha na página **Tarefas**.

5. Após a ativação pós-falha, valide a máquina virtual ao iniciar sessão. Se pretender voltar a outro ponto de recuperação para a máquina virtual, então pode utilizar a opção **Alterar ponto de recuperação**.

6. Quando estiver satisfeito com a ativação pós-falha da máquina virtual, pode **Consolidar** a ativação pós-falha.
   Ao consolidar elimina todos os pontos de recuperação disponíveis com o serviço. Não vais conseguir mudar o ponto de recuperação.

> [!NOTE]
> Quando falhar sobre um VM ao qual adiciona um disco depois de ativar a replicação para o VM, os pontos de replicação mostrarão os discos disponíveis para recuperação. Por exemplo, se um VM tiver um único disco e adicionar um novo, os pontos de replicação que foram criados antes de adicionar o disco mostrarão que o ponto de replicação consiste em "1 de 2 discos".

![Screenshot mostrando failover com um disco adicionado.](./media/azure-to-azure-tutorial-failover-failback/failover-added.png)

## <a name="reprotect-the-secondary-vm"></a>Voltar a proteger a VM secundária

Após a ativação pós-falha da VM, tem de voltar a protege-la, para que esta replique novamente para a região primária.

1. Certifique-se de que a VM está no estado **Ativação pós-falha consolidada** e verifique se a região primária está disponível, e poderá criar e aceder a novos recursos na mesma.
2. Em **Vault**  >  **itens replicados**de cofre, clique à direita no VM que foi falhado e, em seguida, selecione **Re-Protect**.

   ![Screenshot da opção de re-protecção para um VM.](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Verifique se a direção de proteção, secundária à região primária, já está selecionada.
3. Veja as informações **Grupo de recursos, Rede, Armazenamento e Conjuntos de disponibilidade**. Todos os recursos marcados como novos são criados como parte da operação de reproteção.
4. Clique em **OK** para acionar uma tarefa de reproteção. Esta tarefa realiza o seeding do site de destino com os dados mais recentes. Em seguida, replica as diferenças para a região primária. A VM está agora num estado protegido.

## <a name="next-steps"></a>Passos seguintes
- Depois de reprotecir, [aprenda a](azure-to-azure-tutorial-failback.md) falhar na região primária quando estiver disponível.
- [Saiba mais](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) sobre o fluxo de reproteção.
