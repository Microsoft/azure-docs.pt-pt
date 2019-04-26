---
title: Reativação pós-falha do Azure durante a recuperação após desastre de VMs de VMware para o Azure com o Azure Site Recovery | Documentos da Microsoft
description: Saiba como a falha para o site no local após a ativação pós-falha para o Azure, durante a recuperação após desastre de VMs de VMware e servidores físicos para o Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 7773a2f43eb076075be484d92fde31094a2b584b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60318126"
---
# <a name="fail-back-vmware-vms-and-physical-servers-from-azure-to-an-on-premises-site"></a>Reativação pós-falha de VMs de VMware e servidores físicos do Azure para um site no local

Este artigo descreve como efetuar a ativação de back-máquinas virtuais de máquinas virtuais do Azure para um ambiente de VMware no local. Siga as instruções neste artigo para efetuar a ativação fazer uma cópia suas máquinas de virtuais de VMware ou o Windows/Linux servidores físicos, depois de ter pós-falha os locais de sites para o Azure com o [ativação pós-falha no Azure Site Recovery](site-recovery-failover.md) tutorial.

## <a name="prerequisites"></a>Pré-requisitos
- Certifique-se de que leu os detalhes sobre o [diferentes tipos de reativação pós-falha](concepts-types-of-failback.md) e advertências correspondentes.

> [!WARNING]
> Não é possível efetuar a ativação depois de os tiver algum [concluir a migração](migrate-overview.md#what-do-we-mean-by-migration), mover uma máquina virtual para outro grupo de recursos ou eliminou a máquina virtual do Azure. Se desativar a proteção da máquina virtual, não é possível efetuar a reativação pós-falha.

> [!WARNING]
> Um servidor físico do Windows Server 2008 R2 SP1, se protegido e efetuar a ativação pós-falha para o Azure não realizarão a reativação pós-falha.

> [!NOTE]
> Se o ter houve ativação pós-falha de máquinas virtuais VMware, não é possível reativação pós-falha para um anfitrião Hyper-V.


- Antes de continuar, conclua os passos de voltar a proteger, para que as máquinas virtuais estão num estado replicado e pode iniciar uma ativação pós-falha para um site no local. Para obter mais informações, consulte [como voltar a proteger do Azure para o local](vmware-azure-reprotect.md).

- Certifique-se de que o vCenter está num estado ligado antes de fazer uma reativação pós-falha. Caso contrário, a desligar discos e anexá-las novamente para a máquina virtual irá falhar.

- Durante a ativação pós-falha para o Azure, o site no local pode não estar acessível, e o servidor de configuração pode estar indisponível ou encerrado para baixo. Durante a reproteção e reativação pós-falha, o servidor de configuração no local deve ser em execução e num estado OK ligado. 

- Durante a reativação pós-falha, a máquina virtual tem de existir na base de dados de servidor de configuração ou reativação pós-falha não ter êxito. Certifique-se de que obter agendadas regularmente cópias de segurança do seu servidor. Se ocorrer um desastre, terá de restaurar o servidor com o endereço IP original para a reativação pós-falha trabalhar.

- O servidor de destino mestre não deve ter quaisquer instantâneos antes de acionar a reproteção/reativação pós-falha.

## <a name="overview-of-failback"></a>Descrição geral da reativação pós-falha
Depois de ter a pós-falha para o Azure, pode falhar novamente para o seu site no local ao executar os seguintes passos:

1. [Voltar a proteger](vmware-azure-reprotect.md) as máquinas virtuais no Azure para que comecem a replicar para máquinas de virtuais de VMware no seu site no local. Como parte deste processo, terá também de:

    * Configure um destino mestre no local. Utilizar um destino principal do Windows para máquinas de virtuais do Windows e um [Linux de destino mestre](vmware-azure-install-linux-master-target.md) para máquinas virtuais do Linux.
    * Configurar uma [servidor de processos](vmware-azure-set-up-process-server-azure.md).
    * Inicie [voltar a proteger](vmware-azure-reprotect.md) para desativar a máquina virtual no local e sincronizar dados da máquina virtual do Azure com os discos no local.

2. Depois de replicar as máquinas virtuais no Azure para o seu site no local, inicie uma ativação pós-falha do Azure para o site no local.

3. Após a reativação pós-falha em seus dados, voltar a proteger as máquinas de virtuais no local novamente para que comecem a ser replicadas para o Azure.

Para uma descrição geral, veja o seguinte vídeo sobre como efetuar a ativação pós-falha para um site no local:
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Passos para efetuar a reativação pós-falha

> [!IMPORTANT]
> Antes de começar a reativação pós-falha, certifique-se de que já concluiu a nova proteção das máquinas virtuais. As máquinas virtuais devem estar num estado protegido e deve ser o seu estado de funcionamento **OK**. Para voltar a proteger as máquinas virtuais, leia [como voltar a proteger](vmware-azure-reprotect.md).

1. Na página de itens replicados, selecione a máquina virtual. Faça duplo clique nele para selecionar **ativação pós-falha não planeada**.
2. Na **confirmar ativação pós-falha**, certifique-se a direção da ativação pós-falha (do Azure). Em seguida, selecione o ponto de recuperação (versão mais recente ou a mais recente consistente de aplicação) que pretende utilizar para a ativação pós-falha. O ponto consistente com a aplicação está por trás do ponto mais recente no tempo e provoca alguma perda de dados.
3. Durante a ativação pós-falha, o Site Recovery encerra as máquinas virtuais no Azure. Depois de verificar que a reativação pós-falha concluída conforme esperado, pode verificar que as máquinas virtuais no Azure encerrada.
4. **Consolidar** é necessária para remover a máquina de virtual com ativação pós-falha do Azure. Com o botão direito do item protegido e, em seguida, selecione **consolidar**. Uma tarefa remove as máquinas de virtuais com ativação pós-falha no Azure.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Para o ponto de recuperação pode não consigo novamente as máquinas virtuais?

Durante a reativação pós-falha, tem duas opções para a reativação pós-falha do plano de recuperação da máquina virtual /.

- Se selecionar o último ponto processado no tempo, todas as máquinas virtuais a ativação pós-falha para o ponto mais recente no tempo. Se existir um grupo de replicação no plano de recuperação, cada máquina virtual do grupo de replicação não consegue ao longo do respetivo independente ponto mais recente no tempo.

  Não é possível efetuar a reativação pós-falha uma máquina virtual até que ele tenha, pelo menos, um ponto de recuperação. Não é possível efetuar a reativação pós-falha um plano de recuperação até que todas as suas máquinas virtuais têm pelo menos um ponto de recuperação.

  > [!NOTE]
  > Um ponto de recuperação mais recente é um ponto de recuperação consistentes com falhas.

- Se selecionar o ponto de recuperação consistentes com a aplicação, recupera uma reativação pós-falha de máquina virtual individual para o respetivo ponto de recuperação consistentes com aplicações disponíveis mais recentes. No caso de um plano de recuperação com um grupo de replicação, a cada grupo de replicação recupera para o respetivo ponto de recuperação disponíveis comuns.
Pontos de recuperação consistentes com a aplicação podem estar atrás no tempo e pode haver perda de dados.

## <a name="what-happens-to-vmware-tools-post-failback"></a>O que acontece a reativação pós-falha de postagem de ferramentas do VMware?

No caso de uma máquina virtual do Windows, o Site Recovery desativa as ferramentas VMware durante a ativação pós-falha. Durante a reativação pós-falha da máquina virtual Windows, as ferramentas do VMware são reenabled. 


## <a name="reprotect-from-on-premises-to-azure"></a>Voltar a proteger no local para o Azure
Após a conclusão da reativação pós-falha e começou a consolidação, as máquinas virtuais recuperadas no Azure são eliminadas. Agora, a máquina virtual está de volta no site no local, mas este não ser protegido. Para começar a replicar para o Azure novamente, faça o seguinte:

1. Selecione **cofre** > **definição** > **itens replicados**, selecione as máquinas virtuais que realizarão a reativação pós-falha e, em seguida, selecione  **Voltar a proteger**.
2. Introduza o valor do servidor de processo que precisa ser usado para enviar dados para o Azure.
3. Selecione **OK** para iniciar a tarefa de reproteção.

> [!NOTE]
> Depois de uma máquina de virtual no local arrancar o limite de tempo, demora algum tempo para o agente registar novamente para o servidor de configuração (até 15 minutos). Durante este período, voltar a proteger falha e devolve uma mensagem de erro a indicar que o agente não está instalado. Aguarde alguns minutos e, em seguida, tente voltar a proteger novamente.

## <a name="next-steps"></a>Passos Seguintes

Depois de concluída a tarefa de reproteção, replica a máquina virtual para o Azure e pode fazer uma [ativação pós-falha](site-recovery-failover.md) para mover as máquinas virtuais para o Azure novamente.


