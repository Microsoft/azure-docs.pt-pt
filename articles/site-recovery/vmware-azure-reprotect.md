---
title: Proteja novamente as VMs VMware para um site local com Azure Site Recovery
description: Saiba como proteger novamente as VMs VMware após o failover para o Azure com o Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mayg
ms.openlocfilehash: 976888f57269cc9fe6107a38e30d78c73eb5c124
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75498123"
---
# <a name="reprotect-from-azure-to-on-premises"></a>Voltar a proteger do Azure para o local

Após o [failover](site-recovery-failover.md) de VMs VMware locais ou de servidores físicos para o Azure, a primeira etapa ao fazer failback para seu site local é proteger novamente as VMs do Azure que foram criadas durante o failover. Este artigo descreve como fazer isso. 

## <a name="before-you-begin"></a>Antes de começar

1. Siga as etapas neste [artigo](vmware-azure-prepare-failback.md) para se preparar para a nova proteção e o failback, incluindo a configuração de um servidor de processo no Azure e um servidor de destino mestre local e a configuração de uma VPN site a site ou um emparelhamento privado de ExpressRoute para failback.
2. Verifique se o servidor de configuração local está em execução e conectado ao Azure. Durante o failover para o Azure, o site local pode não estar acessível e o servidor de configuração pode estar indisponível ou desligado. Durante o failback, a VM deve existir no banco de dados do servidor de configuração. Caso contrário, o failback não será bem-sucedido.
3. Exclua todos os instantâneos no servidor de destino mestre local. A nova proteção não funcionará se houver instantâneos.  Os instantâneos na VM são mesclados automaticamente durante um trabalho de nova proteção.
4. Se você estiver protegendo novamente as VMs coletadas em um grupo de replicação para consistência de várias VMS, verifique se todas elas têm o mesmo sistema operacional (Windows ou Linux) e certifique-se de que o servidor de destino mestre implantado tenha o mesmo tipo de sistema operacional. Todas as VMs em um grupo de replicação devem usar o mesmo servidor de destino mestre.
5. Abra [as portas necessárias para o](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) failback.
6. Verifique se o vCenter Server está conectado antes do failback. Caso contrário, desconectar os discos e anexá-los de volta à máquina virtual falhará.
7. Se um servidor vCenter gerenciar as VMs para as quais você fará failback, verifique se você tem as permissões necessárias. Se você executar uma descoberta de vCenter de usuário somente leitura e proteger as máquinas virtuais, a proteção será realizada com sucesso e o failover funcionará. No entanto, durante a nova proteção, o failover falha porque os repositórios de armazenamento não podem ser descobertos e não são listados durante a nova proteção. Para resolver esse problema, você pode atualizar as credenciais do vCenter com uma [conta/permissões apropriadas](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)e, em seguida, repetir o trabalho. 
8. Se você usou um modelo para criar suas máquinas virtuais, certifique-se de que cada VM tenha seu próprio UUID para os discos. Se o UUID de VM local conflitar com o UUID do servidor de destino mestre porque ambos foram criados a partir do mesmo modelo, a nova proteção falhará. Implantar de um modelo diferente.
9. Se você estiver fazendo failback para um vCenter Server alternativo, certifique-se de que o novo vCenter Server e o servidor de destino mestre sejam descobertos. Normalmente, se não forem, os repositórios de armazenamento não estarão acessíveis ou não estarão visíveis em **proteger**novamente.
10. Verifique os seguintes cenários em que você não pode realizar failback:
    - Se você estiver usando a edição gratuita ESXi 5,5 ou a edição gratuita do vSphere 6 hypervisor. Atualize para uma versão diferente.
    - Se você tiver um servidor físico do Windows Server 2008 R2 SP1.
    - As VMs do VMware não podem fazer failback para o Hyper-V.
    - VMs que [foram migradas](migrate-overview.md#what-do-we-mean-by-migration).
    - Uma VM que foi movida para outro grupo de recursos.
    - Uma VM do Azure de réplica que foi excluída.
    - Uma VM do Azure de réplica que não está protegida (replicando para o site local).
10. [Examine os tipos de failback](concepts-types-of-failback.md) que você pode usar-recuperação de local original e recuperação de local alternativo.


## <a name="enable-reprotection"></a>Habilitar a nova proteção

Ative a replicação. Você pode proteger novamente VMs específicas ou um plano de recuperação:

- Se você proteger novamente um plano de recuperação, deverá fornecer os valores para cada computador protegido.
- Se as VMs pertencerem a um grupo de replicação para consistência de várias VMS, elas só poderão ser protegidas novamente usando um plano de recuperação. As VMs em um grupo de replicação devem usar o mesmo servidor de destino mestre

### <a name="before-you-start"></a>Antes de começar

- Depois que uma VM é inicializada no Azure após o failover, leva algum tempo para que o agente seja registrado no servidor de configuração (até 15 minutos). Durante esse tempo, você não poderá proteger novamente e uma mensagem de erro indicará que o agente não está instalado. Se isso acontecer, aguarde alguns minutos e, em seguida, proteja novamente.
- Se você quiser realizar o failback da VM do Azure para uma VM local existente, monte os repositórios de armazenamento de VM locais com acesso de leitura/gravação no host ESXi do servidor de destino mestre.
- Se você quiser realizar o failback para um local alternativo, por exemplo, se a VM local não existir, selecione a unidade de retenção e o repositório de armazenamento que estão configurados para o servidor de destino mestre. Quando você realiza o failback para o site local, as máquinas virtuais VMware no plano de proteção de failback usam o mesmo repositório de armazenamento que o servidor de destino mestre. Uma nova VM é criada no vCenter.

Habilite a nova proteção da seguinte maneira:

1. Selecione **cofre** > **itens replicados**. Clique com o botão direito do mouse na máquina virtual que passou por failover e, em seguida, selecione **proteger novamente**. Ou, nos botões de comando, selecione o computador e, em seguida, selecione **proteger novamente**.
2. Verifique se a direção do **Azure para local** da proteção está selecionada.
3. Em **servidor de destino mestre** e **servidor de processo**, selecione o servidor de destino mestre local e o servidor de processo.  
4. Para o **repositório de armazenamento**, selecione o repositório de armazenamento no qual você deseja recuperar os discos no local. Essa opção é usada quando a máquina virtual local é excluída e você precisa criar novos discos. Essa opção será ignorada se os discos já existirem. Você ainda precisa especificar um valor.
5. Selecione a unidade de retenção.
6. A política de reativação pós-falha é selecionada automaticamente.
7. Selecione **OK** para iniciar a nova proteção.

    ![Caixa de diálogo proteger novamente](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. Um trabalho começa a replicar a VM do Azure para o site local. Pode acompanhar o progresso no separador **Trabalhos**.
    - Quando a nova proteção for realizada com sucesso, a VM entrará em um estado protegido.
    - A VM no local é desativada durante a nova proteção. Desta forma, garante-se a consistência dos dados durante a replicação.
    - Não ative a VM local após a conclusão da nova proteção.
   

## <a name="next-steps"></a>Passos seguintes

- Se você encontrar problemas, examine o [artigo de solução de problemas](vmware-azure-troubleshoot-failback-reprotect.md).
- Depois que as VMs do Azure estiverem protegidas, você poderá [executar um failback](vmware-azure-failback.md). O failback desliga a VM do Azure e inicializa a VM local. Espere algum tempo de inatividade para o aplicativo e escolha um horário de failback de acordo.


