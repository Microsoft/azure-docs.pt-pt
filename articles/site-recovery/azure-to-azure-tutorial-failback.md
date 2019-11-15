---
title: Faça failback de VMs do Azure para uma região primária com o serviço de Azure Site Recovery.
description: Descreve como executar failback de VMs do Azure para a região primária com o serviço Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c27b7bf29e5f124fdcfb886b658fd8e9d4cc48fe
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091351"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Fazer failback de uma VM do Azure entre regiões do Azure

O serviço de [Azure site Recovery](site-recovery-overview.md) contribui para sua estratégia de recuperação de desastres Gerenciando e orquestrando a replicação, o failover e o failback de máquinas locais e VMs (máquinas virtuais) do Azure.

Este tutorial descreve como executar failback de uma única VM do Azure. Após o failover, você deve fazer failback para a região primária quando ela estiver disponível. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> 
> * Faça failback da VM na região secundária.
> * Proteja novamente a VM primária de volta para a região secundária.
> 
> [!NOTE]
> 
> Este tutorial ajuda você a fazer failover de algumas VMs para uma região de destino e de volta para a região de origem com personalizações mínimas. Para obter instruções mais detalhadas, examine os guias de instruções [em VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/).

## <a name="before-you-start"></a>Antes de começar

* Verifique se o status da VM está com o **failover confirmado**.
* Verifique se a região primária está disponível e se você pode criar e acessar novos recursos nela.
* Certifique-se de que a nova proteção esteja habilitada.

## <a name="fail-back-to-the-primary-region"></a>Realizar a reativação pós-falha para a região primária

Depois que as VMs são protegidas novamente, você pode fazer failback para a região primária, conforme necessário.

1. No cofre, selecione **itens replicados**e, em seguida, selecione a VM que foi protegida novamente.

    ![Failback para primário](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

2. Em **itens replicados**, selecione a VM e, em seguida, selecione **failover**.
3. Em **failover**, selecione um ponto de recuperação para o qual fazer failover:
    - **Mais recente (padrão)** : processa todos os dados no serviço de site Recovery e fornece o RPO (objetivo de ponto de recuperação) mais baixo.
    - **Mais recente processado**: reverte a VM para o último ponto de recuperação que foi processado pelo site Recovery.
    - **Personalizado**: faz failover para um ponto de recuperação específico. Esta opção é útil para realizar uma ativação pós-falha de teste.
4. Selecione **desligar o computador antes do início do failover** se desejar que site Recovery tente um desligamento das VMs na região de Dr antes de disparar o failover. O failover continuará mesmo se o desligamento falhar. 
5. Siga o progresso da ativação pós-falha na página **Tarefas**.
6. Após a conclusão do failover, valide a VM fazendo logon nela. Você pode alterar o ponto de recuperação conforme necessário.
7. Depois de verificar o failover, selecione **confirmar o failover**. Confirmar exclui todos os pontos de recuperação disponíveis. A opção alterar ponto de recuperação não está mais disponível.
8. A VM deve aparecer como failover e failback.

    ![VM em regiões primárias e secundárias](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> Para computadores que executam a extensão de Site Recovery versão 9.28. x. x em diante, o [pacote cumulativo de atualizações 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) site Recovery limpa as máquinas na região de recuperação de desastre secundária, após o failback ser concluído e as VMs serem protegidas novamente. Não é necessário excluir manualmente as VMs e NICs na região secundária. Se você desabilitar completamente a replicação após o failback, Site Recovery limpará os discos na região de recuperação de desastre, além das VMs e NICs.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) sobre o fluxo de nova proteção.
