---
title: Tutorial para falhar VMs de Azure para uma região primária durante a recuperação de desastres com a Recuperação do Local de Azure.
description: Tutorial para aprender sobre falhar VMs Azure para uma região primária com recuperação do local de Azure.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 5c127010a7988bf08c77340a4fc10bb32dc76f87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93393947"
---
# <a name="tutorial-fail-back-azure-vm-to-the-primary-region"></a>Tutorial: Falha em Azure VM para a região primária

Depois de falhar um VM Azure para uma região secundária de Azure, siga este tutorial para falhar o VM para a região primária de Azure, utilizando [a Recuperação do Sítio Azure.](site-recovery-overview.md)  Neste artigo, vai aprender a:

> [!div class="checklist"]
> 
> * Reveja os pré-requisitos.
> * Represa o VM na região secundária.
> * VMs primários reprotegimos de volta à região secundária.
> 
> [!NOTE]
> Este tutorial mostra-lhe como reagir com os passos mínimos. Se pretender executar um failover com definições completas, saiba mais sobre [a rede](azure-to-azure-about-networking.md)Azure VM, [automação](azure-to-azure-powershell.md)e [resolução de problemas](azure-to-azure-troubleshoot-errors.md).



## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, deve ter:

1. [Confisco para](azure-to-azure-tutorial-enable-replication.md) pelo menos um VM Azure, e experimente um [simulacro de recuperação de desastres](azure-to-azure-tutorial-dr-drill.md) para ele.
2. [Falhou sobre o VM](azure-to-azure-tutorial-failover-failback.md) da região primária para uma região secundária, e reprotegiu-a de modo a que se replicasse da região secundária para a primária. 
3. Verifique se a região primária está disponível e que é capaz de criar e aceder a novos recursos.

## <a name="fail-back-to-the-primary-region"></a>Realizar a reativação pós-falha para a região primária

Depois de os VM serem reprotegidos, podes falhar de volta à região primária, se necessário.

1. No cofre > **itens replicados,** selecione o VM.

2. Na página geral do VM, verifique se o VM está saudável e que a sincronização está completa, antes de executar uma falha. O VM deve estar num estado *protegido.*

    ![Página geral vM mostrando VM em um estado protegido](./media/azure-to-azure-tutorial-failback/protected-state.png)

3. Na página geral, selecione **Failover**. Já que não estamos a fazer um teste falhado desta vez, fomos solicitados a verificar.

    [Página mostrando que concordamos em executar failover sem um teste failover](./media/azure-to-azure-tutorial-failback/no-test.png)

4. Em **Failover**, note a direção do secundário para o primário e selecione um ponto de recuperação. O Azure VM no alvo (região primária) é criado usando dados a partir deste ponto.
   - **Processo mais recente**: Utiliza o último ponto de recuperação processado pela Recuperação do Local. O carimbo de data/hora é apresentado. Não é gasto tempo a processar dados, pelo que fornece um objetivo de tempo de recuperação baixo (RTO).
   -  **Mais recente:** Processa todos os dados enviados para a Recuperação do Local, para criar um ponto de recuperação para cada VM antes de falhar. Fornece o objetivo de ponto de recuperação mais baixo (RPO), porque todos os dados são replicados para a Recuperação do Local quando a falha é desencadeada.
   - **Aplicações mais recentes consistentes**: Esta opção falha em VMs até ao mais recente ponto de recuperação consistente com aplicações. O carimbo de data/hora é apresentado.
   - **Costume**: Falhe no ponto de recuperação particular. O costume só está disponível quando falha num único VM e não usa um plano de recuperação.

    > [!NOTE]
    > Se falhar num VM ao qual adicionou um disco depois de ter ativado a replicação para o VM, os pontos de replicação mostrarão os discos disponíveis para recuperação. Por exemplo, um ponto de replicação que foi criado antes de adicionar um segundo disco mostrará como "1 de 2 discos".

4. Selecione **desligar a máquina antes de começar a falhar** se quiser que a Recuperação do Local tente desligar os VMs de origem antes de iniciar o failover. O encerramento ajuda a garantir que não há perda de dados. A ativação pós-falha continua, mesmo que o encerramento falhe. 

    ![Página de definições de failover](./media/azure-to-azure-tutorial-failback/failover.png)    

3. Para iniciar a falha, selecione **OK**.
4. Monitorize o failover nas notificações.

    ![Notificação para progressos em insusição](./media/azure-to-azure-tutorial-failback/notification-progress.png)  
    ![Notificação para o sucesso do failover](./media/azure-to-azure-tutorial-failback/notification-success.png)   

## <a name="reprotect-vms"></a>Proteger novamente as VMs

Depois de ter falhado os VMs na região primária, é preciso reprotec-los, para que comecem a replicar-se novamente na região secundária.

1. Na página **'Visão Geral'** para o VM, selecione **Re-protect**.

    ![Botão para reprotegir da região primária](./media/azure-to-azure-tutorial-failback/reprotect.png)  

2. Reveja as definições de destino para a região primária. Os recursos marcados como novos são criados pela Recuperação do Local como parte da operação de reproteção.
3. Selecione **OK** para iniciar o processo de reproteção. O processo envia dados iniciais para a localização do alvo e, em seguida, replica informações delta para os VMs para o alvo.

     ![Página mostrando definições de replicação](./media/azure-to-azure-tutorial-failback/replication-settings.png) 

4. Monitorize o progresso reprotegido nas notificações. 

    ![Notificação de progresso reprotetor ](./media/azure-to-azure-tutorial-failback/notification-reprotect-start.png) [Reprotect Notificação de progresso](./media/azure-to-azure-tutorial-failback/notification-reprotect-finish.png)
    
  

## <a name="clean-up-resources"></a>Limpar os recursos

Para os VMs com discos geridos, depois de o failback estar completo e os VMs serem reprotegidos para replicação do primário ao secundário, a Recuperação do Local limpa automaticamente as máquinas na região de recuperação de desastres secundários. Não é necessário eliminar manualmente VMs e NICs na região secundária. VMs com discos não geridos não são limpos.

Se desativar completamente a replicação depois de ter falhado, a Recuperação do Local limpa as máquinas protegidas por ela. Neste caso, também limpa discos para VMs que não usam discos geridos. 
 
## <a name="next-steps"></a>Passos seguintes

Neste tutorial, falhaste dos VMs da região secundária para as primárias. Este é o último passo do processo que inclui permitir a replicação de um VM, experimentar um exercício de recuperação de desastres, falhar da região primária para o secundário, e finalmente falhar.

> [!div class="nextstepaction"]
> Agora, tente a recuperação de [desastres](vmware-azure-tutorial-prepare-on-premises.md) para Azure para um VM no local

