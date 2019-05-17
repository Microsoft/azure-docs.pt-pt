---
title: Falhe novamente Azure VMs replicadas para uma região secundária do Azure para recuperação após desastre com o serviço Azure Site Recovery.
description: Saiba como efetuar a ativação de VMs de volta do Azure com o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9bc2bd50fda34b493463898d8ef47ed2c246b7d8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790111"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Reativação pós-falha de uma VM do Azure entre regiões do Azure

O [do Azure Site Recovery](site-recovery-overview.md) serviço contribui para a sua estratégia de recuperação após desastre através da gestão e orquestração de replicação, ativação pós-falha e reativação pós-falha de máquinas no local e máquinas virtuais do Azure (VMs).

Este tutorial descreve como efetuar a reativação pós-falha numa única VM do Azure. Depois que tive a ativação pós-falha, tem reativação pós-falha para a região primária quando estiver disponível. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> 
> * Reativação pós-falha da VM na região secundária.
> * Voltar a proteger a VM primária para a região secundária.
> 
> [!NOTE]
> 
> Este tutorial ajuda-o a efetuar a ativação pós-falha algumas VMs para uma região de destino e voltar para a região de origem com personalizações mínimo. Para obter mais instruções detalhadas, consulte a [guias de procedimentos em VMs do Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/).

## <a name="before-you-start"></a>Antes de começar

* Certifique-se de que o estado da VM é **ativação pós-falha consolidada**.
* Verifique que a região primária está disponível e que pode criar e aceder a novos recursos na mesma.
* Certifique-se de que essa nova proteção está ativada.

## <a name="fail-back-to-the-primary-region"></a>Realizar a reativação pós-falha para a região primária

Depois de voltar a proteger as VMs, pode efetuar a ativação pós-falha para a região primária conforme necessário.

1. No cofre, selecione **itens replicados**e, em seguida, selecione a VM que foi protegida novamente.

    ![Reativação pós-falha para principal](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. Selecione **ativação pós-falha de teste** para executar um teste de ativação pós-falha de volta para a região primária.
4. Selecione o ponto de recuperação e a rede virtual para a ativação pós-falha de teste e, em seguida, selecione **OK**. Pode rever o teste de que VM criada na região primária.
5. Após a conclusão da ativação pós-falha de teste com êxito, selecione **ativação pós-falha de teste de limpeza** para limpar os recursos criados na região de origem para a ativação pós-falha de teste.
6. Na **itens replicados**, selecione a VM e, em seguida, selecione **ativação pós-falha**.
7. Na **ativação pós-falha**, selecione um ponto de recuperação para efetuar a ativação pós-falha:
    - **Versão mais recente (predefinição)**: Processa todos os dados no serviço Site Recovery e fornece o objetivo de ponto de recuperação (RPO) mais baixo.
    - **Processado mais recentemente**: Reverte a VM para o ponto de recuperação mais recente que tenha sido processado pelo Site Recovery.
    - **Custom**: Efetua a ativação pós-falha para um ponto de recuperação específico. Esta opção é útil para realizar uma ativação pós-falha de teste.

8. Selecione **encerrar a máquina antes de iniciar a ativação pós-falha** se pretender que o Site Recovery tente encerrar de VMs de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Tenha em atenção que a recuperação de Site não limpa a origem após a ativação pós-falha.
9. Siga o progresso da ativação pós-falha na página **Tarefas**.
10. Depois de concluída a ativação pós-falha, valide a VM ao iniciar sessão. Pode alterar o ponto de recuperação conforme necessário.
11. Depois de verificar a ativação pós-falha, selecione **consolida a ativação pós-falha**. Ao consolidar elimina todos os pontos de recuperação disponíveis. A opção de ponto de recuperação de alteração já não está disponível.
12. A VM deve mostrar como efetuar a ativação pós-falha e realizarão a reativação pós-falha.

    ![VM em regiões primária e secundária](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> A recuperação após desastre de VMs irá permanecer no estado de encerramento/desalocada. Isto é propositado porque o Site Recovery guarda as informações de VM, que poderão ser úteis para ativação pós-falha do primário para a região secundária mais tarde. Não é cobrada para as VMs desalocadas, pelo que devem ser mantidas como estão.

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) sobre o fluxo de nova proteção.
