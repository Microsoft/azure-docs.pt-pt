---
title: Migrar computadores locais com Azure Site Recovery
description: Este artigo descreve como migrar as máquinas no local para o Azure, com o Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 24015810a295ef88b7d3e63bfc464ddddef6b55f
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73939625"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrar máquinas no local para o Azure


Este artigo descreve como migrar computadores locais para o Azure, usando o [Azure site Recovery](site-recovery-overview.md). Em geral, Site Recovery é usado para gerenciar e orquestrar a recuperação de desastres de máquinas locais e VMs do Azure. No entanto, ele também pode ser usado para a migração. A migração usa as mesmas etapas que a recuperação de desastres com uma exceção. Em uma migração, computadores com falha do site local é a etapa final. Diferentemente da recuperação de desastres, não é possível fazer failback para local em um cenário de migração.


Este tutorial mostra como migrar VMs no local e servidores físicos para o Azure. Saiba como:

> [!div class="checklist"]
> * Configurar o ambiente de origem e de destino para migração
> * Configurar uma política de replicação
> * Ativar a replicação
> * Executar uma migração de teste para confirmar que está tudo a funcionar conforme o previsto
> * Executar uma ativação pós-falha única para o Azure


> [!TIP]
> Agora você pode migrar servidores locais para o Azure usando o serviço migrações para Azure. [Saber mais](../migrate/migrate-services-overview.md)

## <a name="before-you-start"></a>Antes de começar

Observe que os dispositivos exportados por drivers paravirtualizados não têm suporte.


## <a name="prepare-azure-and-on-premises"></a>Preparar o Azure e o local

1. Prepare o Azure conforme descrito neste [artigo](tutorial-prepare-azure.md). Embora este artigo Descreva as etapas de preparação para a recuperação de desastres, as etapas também são válidas para a migração.
2. Preparar servidores [VMware](vmware-azure-tutorial-prepare-on-premises.md) ou [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) no local. Se você estiver migrando computadores físicos, não precisará preparar nada. Apenas verifique a [matriz de suporte](vmware-physical-azure-support-matrix.md).


## <a name="select-a-protection-goal"></a>Selecionar um objetivo de proteção

Selecione o que pretende replicar e para onde.
1. Clique em **Cofres dos Serviços de Recuperação** > cofre.
2. No Menu de Recursos, clique em **Site Recovery** > **Preparar Infraestrutura** > **Objetivo de proteção**.
3. Em **Objetivo de proteção**, selecione o que pretende migrar.
    - **VMware**: selecione **Para o Azure** > **Sim, com o VMware vSphere Hypervisor**.
    - **Computador físico**: selecione **Para o Azure** > **Não virtualizado/Outro**.
    - **Hyper-V**: selecione **Para o Azure** > **Sim, com o Hyper-V**. Se as VMs de Hyper-V forem geridas pelo VMM, selecione **Sim**.


## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

**Cenário** | **Detalhes**
--- | --- 
VMware | Configure o [ambiente de origem](vmware-azure-set-up-source.md)e configure o servidor de [configuração](vmware-azure-deploy-configuration-server.md).
Computador físico | [Configure](physical-azure-set-up-source.md) o ambiente de origem e o servidor de configuração.
Hyper-V | Configurar o [ambiente de origem](hyper-v-azure-tutorial.md#set-up-the-source-environment)<br/><br/> Configure o [ambiente de origem](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment) para o Hyper-V implantado com o System Center VMM.

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

Selecione e verifique os recursos de destino.

1. Clique em **Preparar a infraestrutura** > **Destino** e selecione a subscrição do Azure que pretende utilizar.
2. Especifique o modelo de implementação do Resource Manager.
3. Site Recovery verifica os recursos do Azure.
    - Se você estiver migrando VMs VMware ou servidores físicos, Site Recovery verificará se você tem uma rede do Azure na qual as VMs do Azure serão localizadas quando forem criadas após o failover.
    - Se você estiver migrando VMs do Hyper-V, Site Recovery verificar se você tem uma conta de armazenamento do Azure e uma rede compatíveis.
4. Se você estiver migrando VMs Hyper-V gerenciadas pelo System Center VMM, configure o [mapeamento de rede](hyper-v-vmm-azure-tutorial.md#configure-network-mapping).

## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

**Cenário** | **Detalhes**
--- | --- 
VMware | Configure uma [política de replicação](vmware-azure-set-up-replication.md) para VMs VMware.
Computador físico | Configure uma [política de replicação](physical-azure-disaster-recovery.md#create-a-replication-policy) para computadores físicos.
Hyper-V | Configurar uma [política de replicação](hyper-v-azure-tutorial.md#set-up-a-replication-policy)<br/><br/> Configure uma [política de replicação](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy) para o Hyper-V implantada com o System Center VMM.

## <a name="enable-replication"></a>Ativar a replicação

**Cenário** | **Detalhes**
--- | --- 
VMware | [Ative a replicação](vmware-azure-enable-replication.md) para as VMs VMware.
Computador físico | [Habilite a replicação](physical-azure-disaster-recovery.md#enable-replication) para computadores físicos.
Hyper-V | [Ativar replicação](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> [Habilite a replicação](hyper-v-vmm-azure-tutorial.md#enable-replication) para o Hyper-V implantado com o System Center VMM.


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

   - Conclui o processo de migração, interrompe a replicação para a VM local e interrompe Site Recovery cobrança para a VM.
   - Esta etapa limpa os dados de replicação. Ele não exclui as VMs migradas.

     ![Concluir a migração](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Não cancele uma ativação pós-falha em curso**: antes do início da ativação pós-falha, a replicação da VM é parada. Se cancelar uma ativação pós-falha que esteja em curso, a mesma para, mas a VM não será replicada outra vez.

Em alguns cenários, a ativação pós-falha requer processamento adicional, que demora cerca de oito a dez minutos a concluir. Você pode notar tempos de failover de teste mais longos para servidores físicos, máquinas VMware Linux, VMs VMware que não têm o serviço DHCP habilitado e VMs VMware que não têm os seguintes drivers de inicialização: storvsc, VMBus, storflt, intelide, ATAPI.

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
- Remova manualmente qualquer fornecedor/agente do Site Recovery da VM. Se você migrar VMs VMware ou servidores físicos, desinstale o serviço de mobilidade da VM.
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




## <a name="next-steps"></a>Passos seguintes

Neste tutorial, migrou VMs no local para VMs do Azure. Seguida

> [!div class="nextstepaction"]
> [Configure a recuperação de desastre](azure-to-azure-replicate-after-migration.md) para uma região secundária do Azure para as VMs do Azure.

  
