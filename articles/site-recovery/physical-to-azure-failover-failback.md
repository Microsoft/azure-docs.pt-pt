---
title: Configurar o failover e o failback para servidores físicos com recuperação do site
description: Saiba como falhar os servidores físicos para o Azure, e não volte ao local para recuperação de desastres com a Recuperação do Site Azure
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2019
ms.author: raynew
ms.openlocfilehash: ea5893f45962d67f4b6f3e9a261c65aa0ec926bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75497868"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Falhar e falhar servidores físicos replicados ao Azure

Este tutorial descreve como falhar no local servidores físicos que estão replicando para Azure com [Azure Site Recovery](site-recovery-overview.md). Depois de ter falhado, recue de Azure para o seu site no local quando está disponível.

## <a name="before-you-start"></a>Antes de começar

- [Conheça](failover-failback-overview.md) o processo de failover na recuperação de desastres.
- Se quiser falhar em várias máquinas, [aprenda](recovery-plan-overview.md) a juntar máquinas num plano de recuperação.
- Antes de fazer uma falha completa, faça um exercício de recuperação de [desastres](site-recovery-test-failover-to-azure.md) para garantir que tudo esteja funcionando como esperado.
- Siga [estas instruções](site-recovery-failover.md#prepare-to-connect-after-failover) para se preparar para ligar aos VMs Azure após a falha.



## <a name="run-a-failover"></a>Executar uma ativação pós-falha

### <a name="verify-server-properties"></a>Verificar as propriedades do servidor

Verifique as propriedades do servidor e certifique-se de que cumpre os [requisitos do Azure](vmware-physical-azure-support-matrix.md#replicated-machines) para Os VMs Azure.

1. Em **itens protegidos,** clique **em itens replicados**e selecione a máquina.
2. No painel de **itens replicado,** há um resumo da informação da máquina, estado de saúde e os últimos pontos de recuperação disponíveis. Clique em **Propriedades** para ver mais detalhes.
3. No **Compute and Network,** pode modificar o nome Azure, grupo de recursos, tamanho do alvo, [conjunto de disponibilidade,](../virtual-machines/windows/tutorial-availability-sets.md)e configurações de disco geridos
4. Pode ver e modificar as definições de rede, incluindo a rede/sub-rede na qual a VM do Azure será localizada após a ativação pós-falha e o endereço IP que será atribuído à mesma.
5. Nos **Discos,** pode ver informações sobre o sistema operativo da máquina e os discos de dados.

### <a name="fail-over-to-azure"></a>Ativação pós-falha para o Azure

1. Em **Definições,** > **os itens replicados** clique na máquina > **Failover**.
2. Em **Ativação pós-falha**, selecione um **Ponto de Recuperação** para o qual fazer a ativação pós-falha. Pode utilizar uma das opções seguintes:
   - **Mais recente**: esta opção processa primeiro todos os dados enviados para o Site Recovery. Disponibiliza o último RPO (Objetivo de Ponto de Recuperação), porque a VM do Azure criada após a ativação pós-falha tem todos os dados que foram replicados para o Site Recovery quando a ativação pós-falha foi acionada.
   - **Mais recente processado**: Esta opção falha sobre a máquina até ao mais recente ponto de recuperação processado pela Recuperação do Site. Esta opção proporciona um RTO (Objetivo de Tempo de Recuperação) baixo, porque não é despendido tempo ao processar os dados não processados.
   - **Mais recente aplicação consistente**: Esta opção falha sobre a máquina para o mais recente ponto de recuperação consistente de aplicações processado pela Recovery do Site.
   - **Personalizado**: especifique um ponto de recuperação.

3. Selecione **desligar a máquina antes** de iniciar a falha se pretender que a Recuperação do Site tente desligar a máquina de origem antes de acionar a falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode acompanhar o progresso da falha na página **Jobs.**
4. Se preparou para ligar à VM do Azure, ligue para validá-la após a ativação pós-falha.
5. Depois de verificar, **consolide** a ativação pós-falha. São eliminados todos os pontos de recuperação disponíveis.

> [!WARNING]
> Não cancele uma falha em andamento. Antes do início da falha, a replicação da máquina para. Se cancelares a falha, para, mas a máquina não se volta a replicar.
> Para servidores físicos, o processamento adicional de falhas pode demorar cerca de 8 a 10 minutos para ser concluído.

## <a name="automate-actions-during-failover"></a>Automatizar ações durante a failover

Talvez queira automatizar ações durante a falha. Para isso, pode utilizar scripts ou livros de automação Azure em planos de recuperação.

- [Aprenda](site-recovery-create-recovery-plans.md) sobre a criação e personalização de planos de recuperação, incluindo a adição de scripts.
- [Aprenda](site-recovery-runbook-automation.md) a adicionar livros de execução da Azure Automation aos planos de recuperação.

## <a name="configure-settings-after-failover"></a>Configurar as definições após a falha

Depois da falha, é necessário [configurar as definições do Azure](site-recovery-failover.md#prepare-in-azure-to-connect-after-failover) para se ligar aos VMs Azure replicados. Além disso, criar endereços IP [internos e públicos.](site-recovery-failover.md#set-up-ip-addressing)

## <a name="prepare-for-reprotection-and-failback"></a>Prepare-se para a reproteção e recuo

Depois de falhar com o Azure, reprotege os VMs Azure replicando-os para o local no local. Depois de se replicarem, podes revê-los no local, executando uma falha de Azure para o teu local no local.

1. Os servidores físicos replicados para o Azure utilizando a Recuperação do Site só podem falhar como VMware VMs. Precisa de uma infraestrutura VMware para falhar. Siga os passos [deste artigo](vmware-azure-prepare-failback.md) para se preparar para a reproteção e falha, incluindo a criação de um servidor de processo sintetizador no Azure, e um servidor-alvo principal no local, e configurar uma VPN site-to-site, ou peering privado ExpressRoute, para failback.
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
    - VMs que [foram migrados.](migrate-overview.md#what-do-we-mean-by-migration)
    - Um VM que foi transferido para outro grupo de recursos.
    - Uma réplica Azure VM que foi apagada.
    - Uma réplica Azure VM que não está protegida (replicando-se para o local no local).
10. [Reveja os tipos de failback](concepts-types-of-failback.md) que pode utilizar - recuperação original da localização e recuperação alternativa de localização.


## <a name="reprotect-azure-vms-to-an-alternate-location"></a>Reproteja os VMs Azure para um local alternativo

Este procedimento pressupõe que o VM no local não está disponível.

1. No cofre > **Configurações** > **Itens replicados,** clique à direita na máquina que foi falhada durante > **Re-Protect**.
2. Em **Voltar a proteger**, confirme que está selecionado **Do Azure para o local**.
3. Especifique o servidor de destino mestre no local e o servidor de processos.
4. Em **Arquivo de dados**, selecione o arquivo de dados de destino mestre para o qual pretende recuperar os discos no local.
       - Utilize esta opção se o VM no local tiver sido apagado ou não existir, e precisa de criar novos discos.
       - Esta definição é ignorada se os discos já existirem, mas é necessário especificar um valor.
5. Selecione a unidade de retenção de destino mestre. A política de reativação pós-falha é selecionada automaticamente.
6. Clique em **OK** para começar a nova proteção. Um trabalho começa a replicar o VM Azure para o local no local. Pode acompanhar o progresso no separador **Trabalhos**.

> [!NOTE]
> Se quiser recuperar a VM do Azure para uma VM no local já existente, monte o arquivo de dados da máquina virtual no local com acesso de leitura/escrita no anfitrião ESXi do servidor de destino mestre.


## <a name="fail-back-from-azure"></a>Reativação pós-falha a partir do Azure

Execute a ativação pós-falha da seguinte forma:

1. Na página **Itens Replicados**, clique com o botão direito do rato na máquina > **Ativação pós-falha não planeada**.
2. Em **Confirmar Ativação Pós-falha**, confirme que a direção da ativação pós-falha provém do Azure.
3.Selecione o ponto de recuperação que pretende utilizar para a falha.
    - Recomendamos que utilize **o** último ponto de recuperação. O ponto consistente com aplicações está por trás do último ponto no tempo, e causa alguma perda de dados.
    - **O mais recente** é um ponto de recuperação consistente.
    - Quando a ativação pós-falha é executada, o Site Recovery encerra as VMs do Azure e arranca a VM no local. Haverá algum tempo de inatividade, pelo que deve escolher uma hora adequada.
4. Clique com o botão direito do rato na máquina e clique em **Consolidar**. É acionado um trabalho que remove as VMs do Azure.
5. Confirme que as VMs do Azure foram encerradas, conforme esperado.


## <a name="reprotect-on-premises-machines-to-azure"></a>Voltar a proteger máquinas no local para o Azure

Os dados devem estar agora outra vez no site no local, mas não estão a ser replicados para o Azure. Pode iniciar a replicação para o Azure novamente da seguinte forma:

1. Em cofre > **Definições** >**Itens Replicados**, selecione as VMs das quais foi feita reativação pós-falha e clique em **Voltar a Proteger**.
2. Selecione o servidor de processos que é utilizado para enviar os dados replicados para o Azure e clique em **OK**.


## <a name="next-steps"></a>Passos seguintes

Após o trabalho de reproteção terminar, o VM no local está a replicar-se para O Azure. Se necessário, [podes passar outra falha](site-recovery-failover.md) para o Azure.
