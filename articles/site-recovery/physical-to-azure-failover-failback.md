---
title: Configurar failover e failback para servidores físicos com Site Recovery
description: Saiba como fazer failover de servidores físicos para o Azure e failback para o site local para recuperação de desastre com Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2019
ms.author: raynew
ms.openlocfilehash: ea5893f45962d67f4b6f3e9a261c65aa0ec926bf
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75497868"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Fazer failover e failback de servidores físicos replicados para o Azure

Este tutorial descreve como fazer failover de servidores físicos locais que estão replicando para o Azure com o [Azure site Recovery](site-recovery-overview.md). Após o failover, você faz o failback do Azure para seu site local quando ele está disponível.

## <a name="before-you-start"></a>Antes de começar

- [Saiba mais](failover-failback-overview.md) sobre o processo de failover na recuperação de desastre.
- Se você quiser fazer failover de vários computadores, [Aprenda](recovery-plan-overview.md) a reunir computadores em um plano de recuperação.
- Antes de fazer um failover completo, execute uma [análise de recuperação de desastre](site-recovery-test-failover-to-azure.md) para garantir que tudo esteja funcionando conforme o esperado.
- Siga [estas instruções](site-recovery-failover.md#prepare-to-connect-after-failover) para se preparar para se conectar às VMs do Azure após o failover.



## <a name="run-a-failover"></a>Executar uma ativação pós-falha

### <a name="verify-server-properties"></a>Verificar Propriedades do servidor

Verifique as propriedades do servidor e certifique-se de que ele esteja em conformidade com [os requisitos do Azure](vmware-physical-azure-support-matrix.md#replicated-machines) para VMs do Azure.

1. Em **itens protegidos**, clique em **itens replicados**e selecione o computador.
2. No painel **Item replicado** , há um resumo das informações do computador, do status de integridade e dos pontos de recuperação mais recentes disponíveis. Clique em **Propriedades** para ver mais detalhes.
3. Em **computação e rede**, você pode modificar o nome do Azure, o grupo de recursos, o tamanho do destino, o [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md)e as configurações de disco gerenciado
4. Pode ver e modificar as definições de rede, incluindo a rede/sub-rede na qual a VM do Azure será localizada após a ativação pós-falha e o endereço IP que será atribuído à mesma.
5. Em **discos**, você pode ver informações sobre o sistema operacional do computador e os discos de dados.

### <a name="fail-over-to-azure"></a>Fazer failover para o Azure

1. Em **Definições** > **Itens replicados**, clique no computador > **Ativação Pós-falha**.
2. Em **Ativação pós-falha**, selecione um **Ponto de Recuperação** para o qual fazer a ativação pós-falha. Pode utilizar uma das opções seguintes:
   - **Mais recente**: esta opção processa primeiro todos os dados enviados para o Site Recovery. Disponibiliza o último RPO (Objetivo de Ponto de Recuperação), porque a VM do Azure criada após a ativação pós-falha tem todos os dados que foram replicados para o Site Recovery quando a ativação pós-falha foi acionada.
   - **Mais recente processado**: essa opção faz failover do computador para o último ponto de recuperação processado pelo site Recovery. Esta opção proporciona um RTO (Objetivo de Tempo de Recuperação) baixo, porque não é despendido tempo ao processar os dados não processados.
   - **Consistente com o aplicativo mais recente**: essa opção faz failover do computador para o ponto de recuperação consistente com o aplicativo mais recente processado pelo site Recovery.
   - **Personalizado**: especifique um ponto de recuperação.

3. Selecione **desligar o computador antes do início do failover** se desejar que site Recovery tente desligar o computador de origem antes de disparar o failover. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode seguir o progresso da ativação pós-falha na página **Trabalhos**.
4. Se preparou para ligar à VM do Azure, ligue para validá-la após a ativação pós-falha.
5. Depois de verificar, **consolide** a ativação pós-falha. São eliminados todos os pontos de recuperação disponíveis.

> [!WARNING]
> Não cancele um failover em andamento. Antes do início do failover, a replicação da máquina é interrompida. Se você cancelar o failover, ele será interrompido, mas o computador não será replicado novamente.
> Para servidores físicos, o processamento de failover adicional pode levar cerca de oito a dez minutos para ser concluído.

## <a name="automate-actions-during-failover"></a>Automatizar ações durante o failover

Talvez você queira automatizar ações durante o failover. Para fazer isso, você pode usar scripts ou runbooks de automação do Azure em planos de recuperação.

- [Saiba mais](site-recovery-create-recovery-plans.md) sobre como criar e personalizar planos de recuperação, incluindo a adição de scripts.
- [Saiba](site-recovery-runbook-automation.md) está confinado no adicionando Runbooks de automação do Azure aos planos de recuperação.

## <a name="configure-settings-after-failover"></a>Definir configurações após o failover

Após o failover, você precisa [definir as configurações do Azure](site-recovery-failover.md#prepare-in-azure-to-connect-after-failover) para se conectar às VMs do Azure replicadas. Além disso, configure o endereçamento IP [interno e público](site-recovery-failover.md#set-up-ip-addressing) .

## <a name="prepare-for-reprotection-and-failback"></a>Preparar para nova proteção e failback

Após o failover para o Azure, você protege novamente as VMs do Azure replicando-as para o site local. Depois de replicar, você pode fazer failback deles de volta para o local, executando um failover do Azure para seu site local.

1. Os servidores físicos replicados para o Azure usando Site Recovery só podem realizar failback como VMs VMware. Você precisa de uma infraestrutura do VMware para realizar o failback. Siga as etapas neste [artigo](vmware-azure-prepare-failback.md) para se preparar para a nova proteção e o failback, incluindo a configuração de um servidor de processo no Azure e um servidor de destino mestre local e a configuração de uma VPN site a site ou um emparelhamento privado de ExpressRoute para failback.
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
    - VMs que [foram migradas](migrate-overview.md#what-do-we-mean-by-migration).
    - Uma VM que foi movida para outro grupo de recursos.
    - Uma VM do Azure de réplica que foi excluída.
    - Uma VM do Azure de réplica que não está protegida (replicando para o site local).
10. [Examine os tipos de failback](concepts-types-of-failback.md) que você pode usar-recuperação de local original e recuperação de local alternativo.


## <a name="reprotect-azure-vms-to-an-alternate-location"></a>Proteger novamente as VMs do Azure para um local alternativo

Este procedimento pressupõe que a VM local não está disponível.

1. No cofre > **configurações** > **itens replicados**, clique com o botão direito do mouse no computador que passou por failover > **proteger novamente**.
2. Em **Voltar a proteger**, confirme que está selecionado **Do Azure para o local**.
3. Especifique o servidor de destino mestre no local e o servidor de processos.
4. Em **Arquivo de dados**, selecione o arquivo de dados de destino mestre para o qual pretende recuperar os discos no local.
       - Use esta opção se a VM local tiver sido excluída ou não existir, e você precisar criar novos discos.
       - Essa configuração será ignorada se os discos já existirem, mas você precisará especificar um valor.
5. Selecione a unidade de retenção de destino mestre. A política de reativação pós-falha é selecionada automaticamente.
6. Clique em **OK** para começar a nova proteção. Um trabalho começa a replicar a VM do Azure para o site local. Pode acompanhar o progresso no separador **Trabalhos**.

> [!NOTE]
> Se quiser recuperar a VM do Azure para uma VM no local já existente, monte o arquivo de dados da máquina virtual no local com acesso de leitura/escrita no anfitrião ESXi do servidor de destino mestre.


## <a name="fail-back-from-azure"></a>Reativação pós-falha a partir do Azure

Execute a ativação pós-falha da seguinte forma:

1. Na página **Itens Replicados**, clique com o botão direito do rato na máquina > **Ativação pós-falha não planeada**.
2. Em **Confirmar Ativação Pós-falha**, confirme que a direção da ativação pós-falha provém do Azure.
3. Selecione o ponto de recuperação que você deseja usar para o failover.
    - Recomendamos que você use o ponto de recuperação **mais recente** . O ponto consistente com o aplicativo está atrás do último ponto no tempo e causa perda de dados.
    - O **mais recente** é um ponto de recuperação consistente com falhas.
    - Quando a ativação pós-falha é executada, o Site Recovery encerra as VMs do Azure e arranca a VM no local. Haverá algum tempo de inatividade, pelo que deve escolher uma hora adequada.
4. Clique com o botão direito do rato na máquina e clique em **Consolidar**. É acionado um trabalho que remove as VMs do Azure.
5. Confirme que as VMs do Azure foram encerradas, conforme esperado.


## <a name="reprotect-on-premises-machines-to-azure"></a>Voltar a proteger máquinas no local para o Azure

Os dados devem estar agora outra vez no site no local, mas não estão a ser replicados para o Azure. Pode iniciar a replicação para o Azure novamente da seguinte forma:

1. No cofre > **configurações** >**itens replicados**, selecione as VMs com failback que realizaram failback e clique em **proteger novamente**.
2. Selecione o servidor de processos que é utilizado para enviar os dados replicados para o Azure e clique em **OK**.


## <a name="next-steps"></a>Passos seguintes

Após a conclusão do trabalho de nova proteção, a VM local está replicando para o Azure. Conforme necessário, você pode [executar outro failover](site-recovery-failover.md) no Azure.
