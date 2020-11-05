---
title: Tutorial para falhar sobre os VMs de Azure para uma região secundária para recuperação de desastres com recuperação do local de Azure.
description: Tutorial para aprender a falhar e reprotegir VMs Azure replicados numa região secundária de Azure para recuperação de desastres, com o serviço de Recuperação do Local Azure.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 99263c83d25542073d63c1cba394a147bd5b2170
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392865"
---
# <a name="tutorial-fail-over-azure-vms-to-a-secondary-region"></a>Tutorial: Falha sobre VMs de Azure para uma região secundária

Saiba como falhar sobre os VMs Azure que estão habilitados para a recuperação de desastres com [a Recuperação do Local de Azure,](site-recovery-overview.md)para uma região secundária de Azure. Depois do fracasso, reprotete os VMs na região alvo para que se reproduzam na região primária. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Verificar pré-requisitos
> * Verificar definições de VM
> * Realize uma ativação pós-falha para a região secundária
> * Comece a replicar o VM de volta à região primária.


> [!NOTE]
> Este tutorial mostra-lhe como falhar sobre VMs com passos mínimos. Se pretender executar um failover com definições completas, saiba mais sobre [a rede](azure-to-azure-about-networking.md)Azure VM, [automação](azure-to-azure-powershell.md)e [resolução de problemas](azure-to-azure-troubleshoot-errors.md).



## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, deve ter:

1. Confise a replicação para um ou mais VMs Azure. Se ainda não o fez, [complete o primeiro tutorial](azure-to-azure-tutorial-enable-replication.md) desta série para o fazer.
2. Recomendamos que [faça um exercício de recuperação de desastres](azure-to-azure-tutorial-dr-drill.md) para VMs replicados. Executar um berbequim antes de executar um fracasso completo ajuda a garantir que tudo funciona como esperado, sem afetar o seu ambiente de produção. 


## <a name="verify-the-vm-settings"></a>Verifique as definições de VM

1. No cofre > **itens replicados,** selecione o VM.

    ![Opção de abrir as propriedades VM na página geral](./media/azure-to-azure-tutorial-failover-failback/vm-settings.png)

2. Na página de **Visão Geral** VM, verifique se o VM está protegido e saudável, antes de executar uma falha.
    ![Página para verificar as propriedades e estado de VM](./media/azure-to-azure-tutorial-failover-failback/vm-state.png)

3. Antes de falhar, verifique se:
    - O VM está a executar um sistema operativo [Windows](azure-to-azure-support-matrix.md#windows) ou [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) suportado.
    - O VM cumpre os requisitos [de computação,](azure-to-azure-support-matrix.md#replicated-machines---compute-settings) [armazenamento](azure-to-azure-support-matrix.md#replicated-machines---storage)e [networking.](azure-to-azure-support-matrix.md#replicated-machines---networking)

## <a name="run-a-failover"></a>Executar uma ativação pós-falha


1. Na página **'Visão Geral** VM', selecione **Failover**.

    ![Botão de falha para o item replicado](./media/azure-to-azure-tutorial-failover-failback/failover-button.png)

3. Em **Failover,** escolha um ponto de recuperação. O Azure VM na região alvo é criado usando dados deste ponto de recuperação.
  
   - **Processo mais recente** : Utiliza o último ponto de recuperação processado pela Recuperação do Local. O carimbo de data/hora é apresentado. Não é gasto tempo a processar dados, pelo que fornece um objetivo de tempo de recuperação baixo (RTO).
   -  **Mais recente:** Processa todos os dados enviados para a Recuperação do Local, para criar um ponto de recuperação para cada VM antes de falhar. Fornece o objetivo de ponto de recuperação mais baixo (RPO), porque todos os dados são replicados para a Recuperação do Local quando a falha é desencadeada.
   - **Aplicações mais recentes consistentes** : Esta opção falha em VMs até ao mais recente ponto de recuperação consistente com aplicações. O carimbo de data/hora é apresentado.
   - **Costume** : Falhe no ponto de recuperação particular. O costume só está disponível quando falha num único VM e não usa um plano de recuperação.

    > [!NOTE]
    > Se adicionar um disco a um VM depois de ativar a replicação, os pontos de replicação mostram os discos disponíveis para recuperação. Por exemplo, um ponto de replicação criado antes de adicionar um segundo disco mostrará como "1 de 2 discos".

4. Selecione **desligar a máquina antes de começar a falhar** se quiser que a Recuperação do Local tente desligar os VMs de origem antes de iniciar o failover. O encerramento ajuda a garantir que não há perda de dados. A ativação pós-falha continua, mesmo que o encerramento falhe. 

    ![Página de definições de failover](./media/azure-to-azure-tutorial-failover-failback/failover-settings.png)    

3. Para iniciar a falha, selecione **OK**.
4. Monitorize o failover nas notificações.

    ![Notificação de ](./media/azure-to-azure-tutorial-failover-failback/notification-failover-start.png) ![ progresso Notificação de sucesso](./media/azure-to-azure-tutorial-failover-failback/notification-failover-finish.png)     

5. Após o failover, o Azure VM criado na região alvo aparece em **Máquinas Virtuais**. Certifique-se de que o VM está a funcionar e de tamanho adequado. Se pretender utilizar um ponto de recuperação diferente para o VM, selecione **Alterar o ponto de recuperação** , na página **Essentials.**
6. Quando estiver satisfeito com o falhado sobre o VM, **selecione Comprometa-se** na página geral, para terminar a falha.

    ![Comprometer botão](./media/azure-to-azure-tutorial-failover-failback/commit-button.png) 

7. In **Commit** , selecione **OK** para confirmar. O Commit elimina todos os pontos de recuperação disponíveis para o VM na Recuperação do Site, e não poderá alterar o ponto de recuperação.

8. Monitorize o progresso das notificações.

    ![Comprometer notificação de progresso ](./media/azure-to-azure-tutorial-failover-failback/notification-commit-start.png) ![ Comprometer notificação de sucesso](./media/azure-to-azure-tutorial-failover-failback/notification-commit-finish.png)    

9. A Recuperação do Local não limpa a fonte VM após a falha. Tens de fazer isso manualmente.


## <a name="reprotect-the-vm"></a>Reprotegir o VM

Depois do fracasso, reprotete o VM na região secundária, para que se reproduza de volta à região primária. 

1. Certifique-se de que o **Estado de** *VM* é comprometido antes de começar.
2. Verifique se pode aceder à região primária e que tem permissões para criar VMs na região.
3. Na página **visão geral** do VM, selecione **Re-Protect**.

   ![Botão para ativar a reproteção para um VM para um VM.](./media/azure-to-azure-tutorial-failover-failback/reprotect-button.png)

4. Em **Re-protect,** verifique a direção de replicação (secundária à região primária) e reveja as definições de destino para a região primária. Os recursos marcados como novos são criados pela Recuperação do Local como parte da operação de reproteção.

     ![Página de definições de reproteção](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

6. Selecione **OK** para iniciar o processo de reproteção. O processo envia dados iniciais para a localização do alvo e, em seguida, replica informações delta para os VMs para o alvo.
7. Monitorize o progresso reprotegido nas notificações. 

    ![Reprotect notificação de progresso ](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-start.png) ![ Reprotect notificação de sucesso](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-finish.png)
    

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você falhou da região primária para a secundária, e começou a replicar VMs de volta para a região primária. Agora podes voltar da região secundária para as primárias.

> [!div class="nextstepaction"]
> [Realizar a reativação pós-falha para a região primária](azure-to-azure-tutorial-failback.md)
