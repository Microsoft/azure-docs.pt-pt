---
title: Falhe em VMs Azure para uma região primária com o serviço de Recuperação do Local Azure.
description: Descreve como falhar os VMs do Azure para a região primária com o serviço de Recuperação do Local Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 432c92bcfa8a2e0df26adf1516f5bdc9ee73d267
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87502380"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Falha num VM Azure entre as regiões de Azure

O serviço [de Recuperação do Local Azure](site-recovery-overview.md) contribui para a sua estratégia de recuperação de desastres gerindo e orquestrando replicação, failover e failback de máquinas no local e máquinas virtuais Azure (VMs).

Este tutorial descreve como falhar um único Azure VM. Depois de ter falhado, deve voltar à região primária quando estiver disponível. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> 
> * Represa o VM na região secundária.
> * Reprotetete o VM primário de volta à região secundária.
> 
> [!NOTE]
> 
> Este tutorial ajuda-o a falhar em alguns VMs para uma região alvo e de volta à região de origem com personalizações mínimas. Para obter instruções mais aprofundadas, reveja os [guias de como fazer em VMs Azure](../virtual-machines/windows/index.yml).

## <a name="before-you-start"></a>Antes de começar

* Certifique-se de que o estado do VM é **comprometido por Failover**.
* Verifique se a região primária está disponível e que é capaz de criar e aceder a novos recursos.
* Certifique-se de que a reproteção está ativada.

## <a name="fail-back-to-the-primary-region"></a>Realizar a reativação pós-falha para a região primária

Depois de os VM serem reprotegidos, podes falhar de volta à região primária, se necessário.

1. No cofre, selecione **itens replicados**e, em seguida, selecione o VM que foi reprotegido.

    ![Screenshot mostrando falha de volta ao primário no portal Azure.](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

2. Em **itens replicados,** selecione o VM e, em seguida, selecione **Failover**.
3. Em **Failover**, selecione um ponto de recuperação para falhar para:
    - **Mais recente (predefinição)**: Processa todos os dados do serviço de Recuperação de Sítio e fornece o objetivo de ponto de recuperação mais baixo (RPO).
    - **Mais recente processo**: Reverte o VM para o último ponto de recuperação que foi processado pela Recuperação do Local.
    - **Costume**: Falha num determinado ponto de recuperação. Esta opção é útil para realizar uma ativação pós-falha de teste.
4. Selecione **desligar a máquina antes de começar a falhar** se quiser que a Recuperação do Local tente o encerramento de VMs na região DR antes de desencadear a falha. A falha continua mesmo que o encerramento falhe. 
5. Siga o progresso da ativação pós-falha na página **Tarefas**.
6. Depois de concluída a falha, valide o VM iniciando sessão. Pode mudar o ponto de recuperação conforme necessário.
7. Depois de verificar o failover, **selecione Cometa o failover**. A commissão elimina todos os pontos de recuperação disponíveis. A opção de ponto de recuperação de alterações já não está disponível.
8. O VM deve mostrar como falhou e falhou de volta.

    ![Screenshot mostrando VM nas regiões primárias e secundárias.](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> Para máquinas que utilizem discos geridos e executem a versão de extensão de recuperação do site 9.28.x.x [em diante, a atualização da rollup 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) Site Recovery limpa as máquinas na região de recuperação de desastres secundários, depois de o failback estar completo e os VMs serem re-protegidos. Não é necessário eliminar manualmente os VM e os NICs na região secundária. Por favor, note que os VMs com discos não geridos não são limpos. Se desativar completamente a replicação depois de ter falhado, a Recuperação do Local limpa os discos na região de recuperação de desastres, além dos VMs e NICs.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) sobre o fluxo de reproteção.
