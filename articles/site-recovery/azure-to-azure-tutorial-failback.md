---
title: Recue os VMs Azure para uma região primária com o serviço de recuperação do site Azure.
description: Descreve como reaver os VMs Azure para a região primária com o serviço de recuperação do site Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c27b7bf29e5f124fdcfb886b658fd8e9d4cc48fe
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74091351"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Falhar um VM Azure entre as regiões de Azure

O serviço de recuperação de [sítios Azure](site-recovery-overview.md) contribui para a sua estratégia de recuperação de desastres, gerindo e orquestrando replicação, failover e failback de máquinas no local e máquinas virtuais Azure (VMs).

Este tutorial descreve como falhar um único VM Azure. Depois de ter falhado, deve falhar de volta à região primária quando estiver disponível. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> 
> * Falhar o VM na região secundária.
> * Reproteja o VM primário de volta para a região secundária.
> 
> [!NOTE]
> 
> Este tutorial ajuda-o a falhar alguns VMs numa região-alvo e a regressar à região de origem com personalizações mínimas. Para obter instruções mais aprofundadas, reveja os guias de [como fazer em VMs Azure](https://docs.microsoft.com/azure/virtual-machines/windows/).

## <a name="before-you-start"></a>Antes de começar

* Certifique-se de que o estado do VM está comprometido com a **Failover**.
* Verifique se a região primária está disponível e que é capaz de criar e aceder a novos recursos.
* Certifique-se de que a reprotecção está ativada.

## <a name="fail-back-to-the-primary-region"></a>Realizar a reativação pós-falha para a região primária

Depois de os VMs serem reprotegidos, pode falhar de volta à região primária, se necessário.

1. No cofre, selecione **itens replicados**e, em seguida, selecione o VM que foi reprotegido.

    ![Falha de volta às primárias](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

2. Em **itens replicados,** selecione o VM e, em seguida, selecione **Failover**.
3. Em **Failover,** selecione um ponto de recuperação para falhar:
    - **Mais recente (padrão)**: Processa todos os dados do serviço de Recuperação do Site e fornece o objetivo de ponto de recuperação mais baixo (RPO).
    - **Mais recente processado**: Reverte o VM para o mais recente ponto de recuperação que foi processado pela Recuperação do Site.
    - **Costume**: Falha num determinado ponto de recuperação. Esta opção é útil para realizar uma ativação pós-falha de teste.
4. Selecione **a máquina de desligar antes** de iniciar a falha se pretender que a Recuperação do Site tente o encerramento de VMs na região DR antes de desencadear a falha. A falha continua mesmo que o encerramento falhe. 
5. Siga o progresso da ativação pós-falha na página **Tarefas**.
6. Depois de concluída a falha, valide o VM fazendo login nele. Pode mudar o ponto de recuperação conforme necessário.
7. Depois de verificar a falha, selecione **Cometer a falha**. Cometer elimina todos os pontos de recuperação disponíveis. A opção de ponto de recuperação de alterações já não está disponível.
8. O VM deve mostrar como falhou e falhou.

    ![VM nas regiões primária e secundária](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> Para as máquinas que executam a versão de extensão de recuperação do site 9.28.x.x em diante [A atualização do rollup 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) Site Recovery limpa as máquinas na região de recuperação de desastres secundários, depois de o failback estar completo e os VMs serem reprotegidos. Não há necessidade de eliminar manualmente vMs e NICs na região secundária. Se desativar completamente a replicação depois de ter falhado, a Recuperação do Local limpa os discos na região de recuperação de desastres, além dos VMs e NICs.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) sobre o fluxo de reproteção.
