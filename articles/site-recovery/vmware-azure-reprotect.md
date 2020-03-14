---
title: Reproteja vMware VMs para um local no local com recuperação do site Azure
description: Saiba como reproteger os VMware Depois de falhar com o Azure com a Recuperação do Site Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mayg
ms.openlocfilehash: 976888f57269cc9fe6107a38e30d78c73eb5c124
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79257176"
---
# <a name="reprotect-from-azure-to-on-premises"></a>Voltar a proteger do Azure para o local

Após a [falha](site-recovery-failover.md) dos VM s ou servidores físicos no local para o Azure, o primeiro passo para não voltar ao seu site no local é reproteger os VMs Azure que foram criados durante a failover. Este artigo descreve como fazer isto. 

## <a name="before-you-begin"></a>Antes de começar

1. Siga os passos [deste artigo](vmware-azure-prepare-failback.md) para se preparar para a reproteção e falha, incluindo a criação de um servidor de processo sintetizador no Azure, e um servidor-alvo principal no local, e configurar uma VPN site-to-site, ou peering privado ExpressRoute, para failback.
2. Certifique-se de que o servidor de configuração no local está a funcionar e ligado ao Azure. Durante a falha para o Azure, o site no local pode não estar acessível, e o servidor de configuração pode estar indisponível ou desligado. Durante o retempo de falha, o VM deve existir na base de dados do servidor de configuração. Caso contrário, o recuo não tem sucesso.
3. Elimine quaisquer imagens no servidor-alvo principal no local. A proteção não funcionará se houver fotos.  As imagens do VM são automaticamente fundidas durante um trabalho de reproteção.
4. Se estiver a reproteger os VMs reunidos num grupo de replicação para a consistência multi-VM, certifique-se de que todos têm o mesmo sistema operativo (Windows ou Linux) e certifique-se de que o servidor alvo principal que implementa tem o mesmo tipo de sistema operativo. Todos os VMs de um grupo de replicação devem usar o mesmo servidor alvo principal.
5. Abra [as portas necessárias](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) para o recuo.
6. Certifique-se de que o servidor vCenter está ligado antes da reprodução. Caso contrário, desligar os discos e ligá-los de volta à máquina virtual falha.
7. Se um servidor vCenter gerir os VMs aos quais irá falhar, certifique-se de que tem as permissões necessárias. Se realizar uma descoberta vCenter de utilizador apenas de leitura e proteger máquinas virtuais, a proteção tem sucesso e falha os trabalhos. No entanto, durante a reproteção, falha porque as lojas de dados não podem ser descobertas e não estão listadas durante a reproteção. Para resolver este problema, pode atualizar as credenciais vCenter com uma [conta/permissões apropriadas](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)e, em seguida, voltar a tentar o trabalho. 
8. Se usou um modelo para criar as suas máquinas virtuais, certifique-se de que cada VM tem o seu próprio UUID para os discos. Se o VM UUID no local entrar em conflito com o UUID do servidor alvo principal porque ambos foram criados a partir do mesmo modelo, a reproteção falha. Desdobre-se a partir de um modelo diferente.
9. Se estiver a falhar num vCenter Server alternativo, certifique-se de que o novo servidor vCenter e o servidor alvo principal são descobertos. Normalmente, se não forem as lojas de dados não são acessíveis, ou não são visíveis em **Reprotect**.
10. Verifique os seguintes cenários em que não pode falhar:
    - Se estiver a utilizar a edição gratuita ESXi 5.5 ou a edição gratuita do Hypervisor vSphere 6. Atualize para uma versão diferente.
    - Se tiver um servidor físico R2 SP1 do Windows Server 2008.
    - VMware VMs não pode falhar no Hyper-V.
    - VMs que [foram migrados.](migrate-overview.md#what-do-we-mean-by-migration)
    - Um VM que foi transferido para outro grupo de recursos.
    - Uma réplica Azure VM que foi apagada.
    - Uma réplica Azure VM que não está protegida (replicando-se para o local no local).
10. [Reveja os tipos de failback](concepts-types-of-failback.md) que pode utilizar - recuperação original da localização e recuperação alternativa de localização.


## <a name="enable-reprotection"></a>Ativar a reproteção

Ative a replicação. Pode reproteger VMs específicos, ou um plano de recuperação:

- Se reproteger um plano de recuperação, deve fornecer os valores para cada máquina protegida.
- Se os VMs pertencerem a um grupo de replicação para a consistência multi-VM, só podem ser reprotegidos usando um plano de recuperação. VMs em um grupo de replicação deve usar o mesmo servidor alvo principal

### <a name="before-you-start"></a>Antes de começar

- Depois de uma sesta VM em Azure após a falha, o agente leva algum tempo para o agente voltar a registar-se no servidor de configuração (até 15 minutos). Durante este tempo, não poderá reproteger e uma mensagem de erro indica que o agente não está instalado. Se isto acontecer, espere alguns minutos e, em seguida, reproteja.
- Se quiser relançar o VM Azure para um VM existente no local, monte as lojas de dados VM no local com acesso de leitura/escrita no anfitrião ESXi do servidor alvo principal.
- Se pretender falhar num local alternativo, por exemplo, se o VM no local não existir, selecione a unidade de retenção e a datastore que estão configuradas para o servidor alvo principal. Quando falha no local, as máquinas virtuais VMware no plano de proteção de failback utilizam a mesma loja de dados que o servidor alvo principal. Um novo VM é então criado no vCenter.

Permitir a reproteção da seguinte forma:

1. Selecione **Vault** > **itens replicados**. Clique na máquina virtual que falhou e, em seguida, selecione **Re-Protect**. Ou, a partir dos botões de comando, selecione a máquina e, em seguida, selecione **Re-Protect**.
2. Verifique se a direção de proteção **Azure to On-Local** foi selecionada.
3. No **Master Target Server** e no Servidor de **Processos,** selecione o servidor de alvo principal no local e o servidor de processos.  
4. Para **datastore,** selecione a loja de dados para a qual pretende recuperar os discos no local. Esta opção é utilizada quando a máquina virtual no local é eliminada e é necessário criar novos discos. Esta opção é ignorada se os discos já existirem. Ainda precisa especificar um valor.
5. Selecione a unidade de retenção.
6. A política de reativação pós-falha é selecionada automaticamente.
7. Selecione **OK** para iniciar a reproteção.

    ![Reproteger a caixa de diálogo](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. Um trabalho começa a replicar o VM Azure para o local no local. Pode acompanhar o progresso no separador **Trabalhos**.
    - Quando a reprotecção for bem sucedida, o VM entra num estado protegido.
    - A VM no local é desativada durante a nova proteção. Desta forma, garante-se a consistência dos dados durante a replicação.
    - Não ligue o VM no local depois de a reproteção terminar.
   

## <a name="next-steps"></a>Passos seguintes

- Se encontrar algum problema, reveja o artigo de resolução de [problemas.](vmware-azure-troubleshoot-failback-reprotect.md)
- Depois de proteger os VMs Azure, pode [executar um failback](vmware-azure-failback.md). O failback desliga o VM Azure e arranca o VM no local. Espere algum tempo de inatividade para a aplicação e escolha um tempo de recuo em conformidade.


