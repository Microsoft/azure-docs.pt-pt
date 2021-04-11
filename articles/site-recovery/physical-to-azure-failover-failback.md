---
title: Configurar failover e failback para servidores físicos com recuperação do site
description: Aprenda a falhar sobre servidores físicos para Azure, e falhe de volta ao local para recuperação de desastres com a Recuperação do Site Azure
services: site-recovery
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2019
ms.openlocfilehash: 68427d264b6d2eea1a38e3b8bc5273409221c126
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579907"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Falha e falha servidores físicos replicados no Azure

Este tutorial descreve como falhar em servidores físicos que estão a replicar-se para Azure com [a Recuperação do Site Azure](site-recovery-overview.md). Depois de ter falhado, falha de Azure para o seu site no local quando estiver disponível.

## <a name="before-you-start"></a>Antes de começar

- [Conheça](failover-failback-overview.md) o processo de falha na recuperação de desastres.
- Se quiser falhar com várias máquinas, [aprenda](recovery-plan-overview.md) a juntar máquinas num plano de recuperação.
- Antes de fazer um fracasso total, faça um [exercício de recuperação de desastres](site-recovery-test-failover-to-azure.md) para garantir que tudo está funcionando como esperado.
- Siga [estas instruções](site-recovery-failover.md#prepare-to-connect-after-failover) para preparar a ligação aos VMs Azure após a falha.



## <a name="run-a-failover"></a>Executar uma ativação pós-falha

### <a name="verify-server-properties"></a>Verificar propriedades do servidor

Verifique as propriedades do servidor e certifique-se de que cumpre os [requisitos do Azure](vmware-physical-azure-support-matrix.md#replicated-machines) para os VMs Azure.

1. Em **Itens Protegidos,** clique em **Itens Replicados** e selecione a máquina.
2. No painel de **artigos replicado,** há um resumo da informação da máquina, estado de saúde e os últimos pontos de recuperação disponíveis. Clique em **Propriedades** para ver mais detalhes.
3. No **Compute and Network,** pode modificar o nome Azure, grupo de recursos, tamanho alvo, [conjunto de disponibilidades](../virtual-machines/windows/tutorial-availability-sets.md)e definições de disco gerido
4. Pode ver e modificar as definições de rede, incluindo a rede/sub-rede na qual a VM do Azure será localizada após a ativação pós-falha e o endereço IP que será atribuído à mesma.
5. Em **Discos,** pode ver informações sobre o sistema operativo da máquina e discos de dados.

### <a name="fail-over-to-azure"></a>Ativação pós-falha para o Azure

1. Em **Definições**  >  **Itens replicados** clique na máquina > **Failover**.
2. Em **Ativação pós-falha**, selecione um **Ponto de Recuperação** para o qual fazer a ativação pós-falha. Pode utilizar uma das opções seguintes:
   - **Mais recente**: esta opção processa primeiro todos os dados enviados para o Site Recovery. Disponibiliza o último RPO (Objetivo de Ponto de Recuperação), porque a VM do Azure criada após a ativação pós-falha tem todos os dados que foram replicados para o Site Recovery quando a ativação pós-falha foi acionada.
   - **Processo mais recente**: Esta opção falha sobre a máquina até ao último ponto de recuperação processado pela Recuperação do Local. Esta opção proporciona um RTO (Objetivo de Tempo de Recuperação) baixo, porque não é despendido tempo ao processar os dados não processados.
   - **Aplicações mais recentes consistentes**: Esta opção falha sobre a máquina até ao mais recente ponto de recuperação consistente da aplicação processado pela Recuperação do Site.
   - **Personalizado**: especifique um ponto de recuperação.

3. Selecione **desligar a máquina antes de começar a falhar** se quiser que a Recuperação do Local tente desligar a máquina de origem antes de ativar a falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode acompanhar o progresso falhado na página **Jobs.**
4. Se preparou para ligar à VM do Azure, ligue para validá-la após a ativação pós-falha.
5. Depois de verificar, **consolide** a ativação pós-falha. São eliminados todos os pontos de recuperação disponíveis.

> [!WARNING]
> Não cancele um fracasso em curso. Antes de começar a falhar, a replicação da máquina para. Se cancelar a falha, para, mas a máquina não se reproduz.
> Para servidores físicos, o processamento adicional de failover pode demorar cerca de 8 a 10 minutos a ser concluído.

## <a name="automate-actions-during-failover"></a>Automatizar ações durante o failover

É melhor automatizar ações durante o fracasso. Para isso, pode utilizar scripts ou runbooks de automação Azure em planos de recuperação.

- [Saiba como](site-recovery-create-recovery-plans.md) criar e personalizar planos de recuperação, incluindo adicionar scripts.
- [Aprenda](site-recovery-runbook-automation.md) a adicionar livros de azure Automation a planos de recuperação.

## <a name="configure-settings-after-failover"></a>Configurações de configuração após falha

Depois do failover, é necessário [configurar as definições de Azure](site-recovery-failover.md#prepare-in-azure-to-connect-after-failover) para ligar aos VMs Azure replicados. Além disso, crie um endereço IP [interno e público.](site-recovery-failover.md#set-up-ip-addressing)

## <a name="prepare-for-reprotection-and-failback"></a>Prepare-se para reproteção e recuo

Depois de falhar com a Azure, reprotete os VMs Azure replicando-os no local. Depois de se replicarem, podes falhá-los de volta ao local, executando uma falha de Azure para o teu local.

1. Os servidores físicos replicados para O Azure usando a Recuperação do Site só podem falhar como VMware VMs. Precisa de uma infraestrutura VMware para falhar. Siga os passos [deste artigo](vmware-azure-prepare-failback.md) para preparar a reproteção e o failback, incluindo a criação de um servidor de processo em Azure, e um servidor-alvo principal no local, e configurar uma VPN site-to-site, ou o peering privado ExpressRoute, para falha.
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
    - VMs que foram migrados.
    - Um VM que foi transferido para outro grupo de recursos.
    - Uma réplica Azure VM que foi apagada.
    - Uma réplica Azure VM que não está protegida (replicando-se no local).
10. [Reveja os tipos de falha](concepts-types-of-failback.md) que pode usar - recuperação original da localização e recuperação de localização alternativa.


## <a name="reprotect-azure-vms-to-an-alternate-location"></a>Reprotectet Azure VMs para um local alternativo

Este procedimento pressupõe que o VM no local não está disponível.

1. No cofre > **Configurações**  >  **Replicadas,** clique com o botão direito na máquina que foi falhada > **Re-Protect**.
2. Em **Voltar a proteger**, confirme que está selecionado **Do Azure para o local**.
3. Especifique o servidor de destino mestre no local e o servidor de processos.
4. Em **Arquivo de dados**, selecione o arquivo de dados de destino mestre para o qual pretende recuperar os discos no local.
       - Utilize esta opção se o VM no local tiver sido eliminado ou não existir, e precisar de criar novos discos.
       - Esta definição é ignorada se os discos já existirem, mas é necessário especificar um valor.
5. Selecione a unidade de retenção de destino mestre. A política de reativação pós-falha é selecionada automaticamente.
6. Clique em **OK** para começar a nova proteção. Um trabalho começa a replicar o Azure VM para o local do local. Pode acompanhar o progresso no separador **Trabalhos**.

> [!NOTE]
> Se quiser recuperar a VM do Azure para uma VM no local já existente, monte o arquivo de dados da máquina virtual no local com acesso de leitura/escrita no anfitrião ESXi do servidor de destino mestre.


## <a name="fail-back-from-azure"></a>Reativação pós-falha a partir do Azure

Execute a ativação pós-falha da seguinte forma:

1. Na página **Itens Replicados**, clique com o botão direito do rato na máquina > **Ativação pós-falha não planeada**.
2. Em **Confirmar Ativação Pós-falha**, confirme que a direção da ativação pós-falha provém do Azure.
3.Selecione o ponto de recuperação que pretende utilizar para a falha.
    - Recomendamos que utilize o último ponto **de** recuperação. O ponto consistente da aplicação está por trás do ponto mais recente no tempo, e causa alguma perda de dados.
    - **O mais recente** é um ponto de recuperação consistente.
    - Quando a ativação pós-falha é executada, o Site Recovery encerra as VMs do Azure e arranca a VM no local. Haverá algum tempo de inatividade, pelo que deve escolher uma hora adequada.
4. Clique com o botão direito do rato na máquina e clique em **Consolidar**. É acionado um trabalho que remove as VMs do Azure.
5. Confirme que as VMs do Azure foram encerradas, conforme esperado.


## <a name="reprotect-on-premises-machines-to-azure"></a>Voltar a proteger máquinas no local para o Azure

Os dados devem estar agora outra vez no site no local, mas não estão a ser replicados para o Azure. Pode iniciar a replicação para o Azure novamente da seguinte forma:

1. Em cofre > **Definições** >**Itens Replicados**, selecione as VMs das quais foi feita reativação pós-falha e clique em **Voltar a Proteger**.
2. Selecione o servidor de processos que é utilizado para enviar os dados replicados para o Azure e clique em **OK**.


## <a name="next-steps"></a>Passos seguintes

Após o fim do trabalho reprotegido, o VM no local está a replicar-se para a Azure. Se necessário, [podes correr outra falha](site-recovery-failover.md) para o Azure.
