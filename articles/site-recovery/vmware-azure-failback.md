---
title: Fazer failback de VMs VMware/servidores físicos do Azure com Azure Site Recovery
description: Saiba como fazer failback para o site local após o failover no Azure, durante a recuperação de desastre de VMs VMware e servidores físicos para o Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 2ec2a4a91f4de0761f631bec393bb90c3feb82b9
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084065"
---
# <a name="fail-back-vmware-vms-and-physical-servers-from-azure-to-an-on-premises-site"></a>Realizar failback de VMs VMware e servidores físicos do Azure para um site local

Este artigo descreve como fazer failback de máquinas virtuais de máquinas virtuais do Azure para um ambiente VMware local. Siga as instruções neste artigo para realizar o failback de suas máquinas virtuais VMware ou de servidores físicos Windows/Linux após a realização do failover do site local para o Azure usando o [Azure site Recovery](site-recovery-failover.md) tutorial do.

## <a name="prerequisites"></a>Pré-requisitos
- Verifique se você leu os detalhes sobre os [diferentes tipos de failback](concepts-types-of-failback.md) e as advertências correspondentes.

> [!WARNING]
> Não é possível realizar o failback depois de [concluir a migração](migrate-overview.md#what-do-we-mean-by-migration), mover uma máquina virtual para outro grupo de recursos ou excluir a máquina virtual do Azure. Se você desabilitar a proteção da máquina virtual, não poderá fazer failback.

> [!WARNING]
> Um servidor físico do Windows Server 2008 R2 SP1, se protegido e com failover no Azure, não poderá ser reprovado.

> [!NOTE]
> Se você tiver feito failover de máquinas virtuais VMware, não poderá fazer failback para um host Hyper-V.


- Antes de continuar, conclua as etapas de nova proteção para que as máquinas virtuais estejam em um estado replicado e você possa iniciar um failover de volta para um site local. Para obter mais informações, consulte [como proteger novamente do Azure para o local](vmware-azure-reprotect.md).

- Verifique se o vCenter está em um estado conectado antes de fazer um failback. Caso contrário, desconectar os discos e anexá-los de volta à máquina virtual falhará.

- Durante o failover para o Azure, o site local pode não estar acessível e o servidor de configuração pode estar indisponível ou desligado. Durante a nova proteção e o failback, o servidor de configuração local deve estar em execução e em um estado OK conectado. 

- Durante o failback, a máquina virtual deve existir no banco de dados do servidor de configuração ou o failback não terá sucesso. Certifique-se de fazer backups agendados regularmente do seu servidor. Se ocorrer um desastre, você precisará restaurar o servidor com o endereço IP original para que o failback funcione.

- O servidor de destino mestre não deve ter nenhum instantâneo antes de disparar a nova proteção/failback.

## <a name="overview-of-failback"></a>Visão geral do failback
Depois de realizar o failover no Azure, você pode fazer failback para o site local executando as seguintes etapas:

1. [Proteja](vmware-azure-reprotect.md) novamente as máquinas virtuais no Azure para que elas comecem a ser replicadas para máquinas virtuais VMware em seu site local. Como parte desse processo, você também precisa:

    * Configure um destino mestre local. Use um destino mestre do Windows para máquinas virtuais do Windows e um [destino mestre do Linux](vmware-azure-install-linux-master-target.md) para máquinas virtuais do Linux.
    * Configurar um [servidor de processo](vmware-azure-set-up-process-server-azure.md).
    * Inicie a nova [proteção](vmware-azure-reprotect.md) para desligar a máquina virtual local e sincronizar os dados da máquina virtual do Azure com os discos locais.

2. Depois que as máquinas virtuais no Azure são replicadas para seu site local, você inicia um failover do Azure para o site local.

3. Após a realização do failback dos dados, você protege novamente as máquinas virtuais locais novamente para que elas comecem a replicar no Azure.

Para obter uma visão geral rápida, Assista ao vídeo a seguir sobre como fazer failback para um site local:
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Etapas para failback

> [!IMPORTANT]
> Antes de iniciar o failback, certifique-se de que você terminou a nova proteção das máquinas virtuais. As máquinas virtuais devem estar em um estado protegido e sua integridade deve estar **OK**. Para proteger novamente as máquinas virtuais, leia [como proteger](vmware-azure-reprotect.md)novamente.

1. Na página itens replicados, selecione a máquina virtual. Clique com o botão direito do mouse para selecionar **failover não planejado**.
2. Em **confirmar failover**, verifique a direção do failover (do Azure). Em seguida, selecione o ponto de recuperação (mais recente ou o aplicativo mais recente consistente) que você deseja usar para o failover. O ponto consistente do aplicativo está atrás do último ponto no tempo e causa perda de dados.
3. Durante o failover, Site Recovery desliga as máquinas virtuais no Azure. Depois de verificar se o failback foi concluído conforme o esperado, você pode verificar se as máquinas virtuais no Azure foram desligadas.
4. A **confirmação** é necessária para remover a máquina virtual com failover do Azure. Clique com o botão direito do mouse no item protegido e selecione **confirmar**. Um trabalho remove as máquinas virtuais com failover no Azure.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Para qual ponto de recuperação posso fazer failback das máquinas virtuais?

Durante o failback, você tem duas opções para fazer failback do plano de recuperação/máquina virtual.

- Se você selecionar o último ponto processado no tempo, todas as máquinas virtuais passarão por failover para o ponto no tempo mais recente disponível. Se houver um grupo de replicação no plano de recuperação, cada máquina virtual do grupo de replicação passará por failover para seu último ponto no tempo independente.

  Não é possível fazer failback de uma máquina virtual até que ela tenha pelo menos um ponto de recuperação. Não é possível executar failback de um plano de recuperação até que todas as suas máquinas virtuais tenham pelo menos um ponto de recuperação.

  > [!NOTE]
  > Um ponto de recuperação mais recente é um ponto de recuperação consistente com falhas.

- Se você selecionar o ponto de recuperação consistente com o aplicativo, um único failback de máquina virtual será recuperado para seu ponto de recuperação consistente com o aplicativo disponível mais recente. No caso de um plano de recuperação com um grupo de replicação, cada grupo de replicação é recuperado para seu ponto de recuperação disponível comum.
Os pontos de recuperação consistentes com o aplicativo podem ser atrasados no tempo e pode haver perda de dados.

## <a name="what-happens-to-vmware-tools-post-failback"></a>O que acontece com as ferramentas do VMware após o failback?

No caso de uma máquina virtual do Windows, Site Recovery desabilita as ferramentas do VMware durante o failover. Durante o failback da máquina virtual do Windows, as ferramentas do VMware são reabilitadas. 


## <a name="reprotect-from-on-premises-to-azure"></a>Proteger novamente do local para o Azure
Depois que o failback for concluído e você tiver iniciado a confirmação, as máquinas virtuais recuperadas no Azure serão excluídas. Agora, a máquina virtual está de volta no site local, mas não será protegida. Para começar a replicar para o Azure novamente, faça o seguinte:

1. Selecione > **configuração** do **cofre** > **itens replicados**, selecione as máquinas virtuais que realizaram failback e, em seguida, selecione **proteger novamente**.
2. Insira o valor do servidor de processo que precisa ser usado para enviar dados de volta ao Azure.
3. Selecione **OK** para iniciar o trabalho de nova proteção.

> [!NOTE]
> Depois que uma máquina virtual local é inicializada, leva algum tempo para que o agente seja registrado no servidor de configuração (até 15 minutos). Durante esse tempo, a nova proteção falhará e retornará uma mensagem de erro informando que o agente não está instalado. Aguarde alguns minutos e tente proteger novamente.

## <a name="next-steps"></a>Passos seguintes

Após a conclusão do trabalho de nova proteção, a máquina virtual é replicada de volta para o Azure e você pode fazer um [failover](site-recovery-failover.md) para mover suas máquinas virtuais para o Azure novamente.


