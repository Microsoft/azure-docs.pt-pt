---
title: VMs reprotetor de VMware para um local no local com recuperação do local de Azure
description: Aprenda a reprotetar VMware VMs depois de falhar em Azure com Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mayg
ms.openlocfilehash: 6a11e3d0cb41383b44b76975ecbd1c2ae2825015
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441498"
---
# <a name="reprotect-from-azure-to-on-premises"></a>Voltar a proteger do Azure para o local

Após [a falha](site-recovery-failover.md) de VMware VMware no local ou servidores físicos para a Azure, o primeiro passo para não voltar ao seu site no local é reprotegir os VMs Azure que foram criados durante o failover. Este artigo descreve como fazer isto. 

## <a name="before-you-begin"></a>Antes de começar

1. Siga os passos [deste artigo](vmware-azure-prepare-failback.md) para preparar a reproteção e o failback, incluindo a criação de um servidor de processo em Azure, e um servidor-alvo principal no local, e configurar uma VPN site-to-site, ou o peering privado ExpressRoute, para falha.
2. Certifique-se de que o servidor de configuração no local está a funcionar e ligado ao Azure. Durante o failover para Azure, o site no local pode não estar acessível, e o servidor de configuração pode estar indisponível ou desligado. Durante o failback, o VM deve existir na base de dados do servidor de configuração. Caso contrário, o fracasso não é bem sucedido.
3. Elimine quaisquer imagens no servidor principal do alvo no local. A reproteção não funcionará se houver fotos.  As imagens do VM são automaticamente fundidas durante um trabalho reprotegido.
4. Se estiver a reprotecer os VMs recolhidos num grupo de replicação para consistência multi-VM, certifique-se de que todos têm o mesmo sistema operativo (Windows ou Linux) e certifique-se de que o servidor alvo principal que implementa tem o mesmo tipo de sistema operativo. Todos os VMs de um grupo de replicação devem usar o mesmo servidor-alvo principal.
5. Abra [as portas necessárias](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) para o failback.
6. Certifique-se de que o servidor vCenter está ligado antes do failback. Caso contrário, desligar os discos e fixá-los de volta à máquina virtual falha.
7. Se um servidor vCenter gerir os VMs aos quais falhará, certifique-se de que tem as permissões necessárias. Se executar uma descoberta vCenter apenas de leitura e proteger máquinas virtuais, a proteção tem sucesso e funciona. No entanto, durante a reproteção, o failover falha porque as datastores não podem ser descobertos, e não estão listados durante a reproteção. Para resolver este problema, pode atualizar as credenciais do vCenter com uma [conta/permissões apropriadas](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)e, em seguida, voltar a tentar o trabalho. 
8. Se usou um modelo para criar as suas máquinas virtuais, certifique-se de que cada VM tem o seu próprio UUID para os discos. Se o VM UUID no local entrar em conflito com o UUID do servidor alvo principal porque ambos foram criados a partir do mesmo modelo, a reproteção falha. Desdobre-se a partir de um modelo diferente.
9. Se estiver a falhar num servidor vCenter alternativo, certifique-se de que o novo servidor vCenter e o servidor alvo principal são descobertos. Normalmente, se não forem as datastores não são acessíveis, ou não são visíveis em **Reprotect.**
10. Verifique os seguintes cenários em que não pode falhar:
    - Se estiver a utilizar a edição gratuita ESXi 5.5 ou a edição gratuita vSphere 6 Hypervisor. Upgrade para uma versão diferente.
    - Se tiver um servidor físico R2 SP1 do Windows Server 2008.
    - VMware VMs não pode voltar a Hiper-V.
    - VMs que foram migrados.
    - Um VM que foi transferido para outro grupo de recursos.
    - Uma réplica Azure VM que foi apagada.
    - Uma réplica Azure VM que não está protegida (replicando-se no local).
10. [Reveja os tipos de falha](concepts-types-of-failback.md) que pode usar - recuperação original da localização e recuperação de localização alternativa.


## <a name="enable-reprotection"></a>Permitir a reproteção

Ative a replicação. Pode reprotegir VMs específicos ou um plano de recuperação:

- Se reprotetar um plano de recuperação, deve fornecer os valores para cada máquina protegida.
- Se os VMs pertencerem a um grupo de replicação para a consistência multi-VM, só podem ser reprotegidos usando um plano de recuperação. VMs em um grupo de replicação deve usar o mesmo servidor alvo principal

>[!NOTE]
>A quantidade de dados enviados de Azure para fonte durante a reproteção, pode ser qualquer coisa entre 0 bytes e soma de tamanho do disco para todas as máquinas protegidas, e não pode ser calculado.

### <a name="before-you-start"></a>Antes de começar

- Depois de umas botas VM em Azure após o failover, o agente demora algum tempo a registar-se de volta ao servidor de configuração (até 15 minutos). Durante este tempo, não poderá reprotegir e uma mensagem de erro indica que o agente não está instalado. Se isto acontecer, espere uns minutos e reproteja.
- Se pretender falhar o VM Azure para um VM existente no local, monte as lojas de dados VM no local com acesso de leitura/escrita no anfitrião ESXi do servidor principal alvo.
- Se pretender falhar de volta a um local alternativo, por exemplo, se o VM no local não existir, selecione a unidade de retenção e a loja de dados configuradas para o servidor alvo principal. Quando falha no local, as máquinas virtuais VMware no plano de proteção contra falhas utilizam a mesma loja de dados que o servidor principal alvo. Um novo VM é então criado no vCenter.

Permitir a reproteção da seguinte forma:

1. Selecione **Vault**  >  **itens replicados do**cofre . Clique com o botão direito na máquina virtual que falhou e, em seguida, **selecione Re-Protect**. Ou, a partir dos botões de comando, selecione a máquina e, em seguida, **selecione Re-Protect**.
2. Verifique se o **Azure para a** direção de proteção no local está selecionado.
3. No **Master Target Server** and Process **Server**, selecione o servidor-alvo principal no local e o servidor de processo.  
4. Para **datastore,** selecione a loja de dados para a qual pretende recuperar os discos no local. Esta opção é utilizada quando a máquina virtual no local é eliminada e é necessário criar novos discos. Esta opção é ignorada se os discos já existirem. Ainda precisa especificar um valor.
5. Selecione a unidade de retenção.
6. A política de reativação pós-falha é selecionada automaticamente.
7. Selecione **OK** para iniciar a reproteção.

    ![Caixa de diálogo reprotetor](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. Um trabalho começa a replicar o Azure VM para o local do local. Pode acompanhar o progresso no separador **Trabalhos**.
    - Quando a reprotecção é bem sucedida, o VM entra num estado protegido.
    - A VM no local é desativada durante a nova proteção. Desta forma, garante-se a consistência dos dados durante a replicação.
    - Não ligue o VM no local depois de terminar a reprotecção.
   

## <a name="next-steps"></a>Passos seguintes

- Se encontrar algum problema, reveja o [artigo de resolução de problemas](vmware-azure-troubleshoot-failback-reprotect.md).
- Depois de os VMs Azure estarem protegidos, pode [executar uma falha](vmware-azure-failback.md). Failback desliga o Azure VM e arranca o VM no local. Espere algum tempo de inatividade para a aplicação, e escolha um tempo de insí bem-estar em conformidade.


