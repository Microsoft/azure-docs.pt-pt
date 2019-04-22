---
title: Migrar as máquinas no local para o Azure com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como migrar as máquinas no local para o Azure, com o Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: fc15db91b8f4cc6dbdecd0e7321abdbf81744f08
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59357972"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrar máquinas no local para o Azure


Este artigo descreve como migrar máquinas no local para o Azure, utilizando o [do Azure Site Recovery](site-recovery-overview.md). Em geral, o Site Recovery é utilizado para gerir e orquestrar a recuperação após desastre de máquinas no local e VMs do Azure. No entanto, também pode ser utilizado para a migração. Migração utiliza os mesmos passos, como a recuperação após desastre com uma exceção. Na migração, o failover máquinas do seu site no local é a etapa final. Ao contrário da recuperação após desastre, não é possível efetuar a ativação para o local num cenário de migração.


Este tutorial mostra como migrar VMs no local e servidores físicos para o Azure. Saiba como:

> [!div class="checklist"]
> * Configurar o ambiente de origem e de destino para migração
> * Configurar uma política de replicação
> * Ativar a replicação
> * Executar uma migração de teste para confirmar que está tudo a funcionar conforme o previsto
> * Executar uma ativação pós-falha única para o Azure


> [!TIP]
> O serviço Azure Migrate oferecem atualmente uma pré-visualização para uma experiência nova e sem agente para migrar VMs de VMware para o Azure. [Saiba mais](https://aka.ms/migrateVMs-signup).


## <a name="before-you-start"></a>Antes de começar

Tenha em atenção que os dispositivos exportados por controladores paravirtualizados não são suportados.


## <a name="prepare-azure-and-on-premises"></a>Preparar o Azure e no local

1. Preparar o Azure conforme descrito em [este artigo](tutorial-prepare-azure.md). Embora este artigo descreve os passos de preparação para a recuperação após desastre, os passos também são válidos para a migração.
2. Preparar servidores [VMware](vmware-azure-tutorial-prepare-on-premises.md) ou [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) no local. Se estiver a migrar máquinas físicas, não precisa de preparar nada. Apenas Certifique-se a [matriz de suporte](vmware-physical-azure-support-matrix.md).


## <a name="select-a-replication-goal"></a>Selecionar um objetivo de replicação

Selecione o que pretende replicar e para onde.
1. Clique em **Cofres dos Serviços de Recuperação** > cofre.
2. No Menu de Recursos, clique em **Site Recovery** > **Preparar Infraestrutura** > **Objetivo de proteção**.
3. Em **Objetivo de proteção**, selecione o que pretende migrar.
    - **VMware**: Selecione **para o Azure** > **Sim, com o VMWare vSphere Hypervisor**.
    - **Máquina física**: Selecione **para o Azure** > **não virtualizado/outro**.
    - **Hyper-V**: Selecione **para o Azure** > **Sim, com o Hyper-V**. Se as VMs de Hyper-V forem geridas pelo VMM, selecione **Sim**.


## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

**Cenário** | **Detalhes**
--- | --- 
VMware | Configurar o [ambiente de origem](vmware-azure-set-up-source.md)e configurar o [servidor de configuração](vmware-azure-deploy-configuration-server.md).
Máquina física | [Configurar](physical-azure-set-up-source.md) o servidor de configuração e de ambiente de origem.
Hyper-V | Configurar o [ambiente de origem](hyper-v-azure-tutorial.md#set-up-the-source-environment)<br/><br/> Configurar o [ambiente de origem](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment) para Hyper-V implementado com o System Center VMM.

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

Selecione e verifique os recursos de destino.

1. Clique em **Preparar a infraestrutura** > **Destino** e selecione a subscrição do Azure que pretende utilizar.
2. Especifique o modelo de implementação do Resource Manager.
3. Recuperação de sites verifica os recursos do Azure.
    - Se estiver a migrar VMs de VMware ou servidores físicos, a recuperação de sites verifica que tem uma rede do Azure na qual as VMs do Azure estarão localizadas quando forem criadas após a ativação pós-falha.
    - Se estiver a migrar VMs de Hyper-V, o Site Recovery verifica se que tiver uma conta de armazenamento do Azure compatível e rede.
4. Se estiver a migrar VMs de Hyper-V geridas pelo System Center VMM, configure [mapeamento da rede](hyper-v-vmm-azure-tutorial.md#configure-network-mapping).

## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

**Cenário** | **Detalhes**
--- | --- 
VMware | Configurar uma [política de replicação](vmware-azure-set-up-replication.md) para VMs de VMware.
Máquina física | Configurar uma [política de replicação](physical-azure-disaster-recovery.md#create-a-replication-policy) para máquinas físicas.
Hyper-V | Configurar um [política de replicação](hyper-v-azure-tutorial.md#set-up-a-replication-policy)<br/><br/> Configurar uma [política de replicação](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy) para Hyper-V implementado com o System Center VMM.

## <a name="enable-replication"></a>Ativar a replicação

**Cenário** | **Detalhes**
--- | --- 
VMware | [Ative a replicação](vmware-azure-enable-replication.md) para as VMs VMware.
Máquina física | [Ativar a replicação](physical-azure-disaster-recovery.md#enable-replication) para máquinas físicas.
Hyper-V | [Ativar replicação](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> [Ativar a replicação](hyper-v-vmm-azure-tutorial.md#enable-replication) para Hyper-V implementado com o System Center VMM.


## <a name="run-a-test-migration"></a>Executar uma migração de teste

Execute uma [ativação pós-falha de teste](tutorial-dr-drill-azure.md) para o Azure para confirmar que está tudo a funcionar conforme o previsto.


## <a name="migrate-to-azure"></a>Migrar para o Azure

Execute a ativação pós-falha nos computadores que quer migrar.

1. Em **Definições** > **Itens replicados**, clique no computador > **Ativação Pós-falha**.
2. Em **Ativação pós-falha**, selecione um **Ponto de Recuperação** para o qual fazer a ativação pós-falha. Selecione o ponto de recuperação mais recente.
3. A definição da chave de encriptação não é relevante para este cenário.
4. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. O Site Recovery tentará fazer um encerramento das máquinas virtuais antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode seguir o progresso da ativação pós-falha na página **Tarefas**.
5. Certifique-se de que a VM do Azure é apresentada no Azure conforme esperado.
6. Em **Itens replicados**, clique com o botão direito do rato na VM > **Concluir a Migração**. Isso faz o seguinte:

   - O processo de migração é concluído, para a replicação para a VM no local e interrompe a faturação do Site Recovery para a VM.
   - Este passo limpa os dados de replicação. Não elimina as VMs migradas.

     ![Concluir a migração](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Não cancelar uma ativação pós-falha em curso**: Antes de o início da ativação pós-falha, a replicação de VM é parada. Se cancelar uma ativação pós-falha que esteja em curso, a mesma para, mas a VM não será replicada outra vez.

Em alguns cenários, a ativação pós-falha requer processamento adicional, que demora cerca de oito a dez minutos a concluir. Poderá reparar em tempos de ativação pós-falha superiores Para servidores físicos, máquinas do Linux do VMware, VMs do VMware que não têm o serviço DHCP ativado e VMs do VMware que não têm os controladores de arranque storvsc, vmbus, storflt, intelide e atapi.

## <a name="after-migration"></a>Após a migração

Depois de migrar os computadores para o Azure, tem de concluir uma série de passos.

Alguns passos podem ser automatizados como parte do processo de migração através da funcionalidade de scripts de automatização incorporados nos [planos de recuperação](site-recovery-runbook-automation.md).   


### <a name="post-migration-steps-in-azure"></a>Passos de pós-migração no Azure

- Execute otimizações de aplicação pós-migração, tais como atualizar cadeias de ligação de base de dados e configurações de servidor Web. 
- Execute testes de aplicação final e de aceitação da migração na aplicação migrada em execução no Azure.
- O [agente de VM do Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) gere a interação da VM com o Controlador de Recursos de Infraestrutura do Azure. É necessário para alguns serviços do Azure, como o Azure Backup, o Site Recovery e a Segurança do Azure.
    - Se estiver a migrar as máquinas do VMware e os servidores físicos, o instalador do Serviço de Mobilidade instala o agente de VM do Azure em computadores Windows. Nas VMs do Linux, recomendamos que instale o agente após a ativação pós-falha.
    - Se estiver a migrar VMs do Azure para uma região secundária, o agente de VM do Azure tem de ser aprovisionado na VM antes da migração.
    - Se estiver a migrar VMs do Hyper-V para o Azure, instale o agente de VM do Azure na VM do Azure após a migração.
- Remova manualmente qualquer fornecedor/agente do Site Recovery da VM. Se migrar VMs de VMware ou servidores físicos, desinstale o serviço de mobilidade da VM.
- Para uma maior resiliência:
    - Mantenha os dados seguros ao fazer uma cópia de segurança das VMs do Azure através do serviço Azure Backup. [Saiba mais]( https://docs.microsoft.com/azure/backup/quick-backup-vm-portal).
    - Mantenha as cargas de trabalho em execução e continuamente disponíveis ao replicar VMs do Azure para uma região secundária com o Site Recovery. [Saiba mais](azure-to-azure-quickstart.md).
- Para uma maior segurança:
    - Bloqueie e limite o acesso ao tráfego de entrada com a [administração just-in-time]( https://docs.microsoft.com/azure/security-center/security-center-just-in-time) do Centro de Segurança do Azure.
    - Restrinja o tráfego de rede para os pontos finais de gestão com os [Grupos de Segurança de Rede](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Implemente o [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) para ajudar a proteger discos e a manter os dados protegidos contra roubo e acesso não autorizado.
    - Leia mais sobre como [proteger recursos de IaaS]( https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/ ) e aceda ao [Centro de Segurança do Azure](https://azure.microsoft.com/services/security-center/ ).
- Para monitorização e gestão:
    - Considere implementar o [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) para monitorizar a utilização e as despesas do recurso.

### <a name="post-migration-steps-on-premises"></a>Passos de pós-migração no local

- Mova o tráfego da aplicação para a aplicação em execução na instância da VM do Azure migrada.
- Remova as VMs no local do seu inventário de VMs locais.
- Remova as VMs no local das cópias de segurança locais.
- Atualize qualquer documentação interna para mostrar a nova localização e endereço IP das VMs do Azure.




## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, migrou VMs no local para VMs do Azure. Agora

> [!div class="nextstepaction"]
> [Configurar a recuperação após desastre](azure-to-azure-replicate-after-migration.md) para uma região secundária do Azure para as VMs do Azure.

  
