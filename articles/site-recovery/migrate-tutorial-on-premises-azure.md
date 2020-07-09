---
title: Migrar máquinas no local com recuperação do local de Azure
description: Este artigo descreve como migrar as máquinas no local para o Azure, com o Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: ccf83bacedb667e52e9865b6d451641faa0ac414
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86131177"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrar máquinas no local para o Azure


Este artigo descreve como migrar máquinas no local para Azure, utilizando a Recuperação do [Local de Azure](site-recovery-overview.md). 

> [!TIP]
> Deve agora utilizar o Azure Migrate para migrar máquinas no local para Azure, em vez do serviço de Recuperação do Local Azure. [Saiba mais](../migrate/migrate-services-overview.md).


Este tutorial mostra como migrar VMs no local e servidores físicos para o Azure. Saiba como:

> [!div class="checklist"]
> * Configurar o ambiente de origem e alvo para a migração
> * Configurar uma política de replicação
> * Ativar a replicação
> * Executar uma migração de teste para confirmar que está tudo a funcionar conforme o previsto
> * Executar uma ativação pós-falha única para o Azure


> [!TIP]
> Agora pode migrar servidores no local para Azure utilizando o serviço Azure Migrate. [Saiba mais](../migrate/migrate-services-overview.md)

## <a name="before-you-start"></a>Antes de começar

Os dispositivos exportados por controladores paravirtualizados não são suportados.


## <a name="prepare-azure-and-on-premises"></a>Preparar Azure e no local

1. Prepare O Azure como descrito [neste artigo](tutorial-prepare-azure.md). Embora este artigo descreva passos de preparação para a recuperação de desastres, os passos também são válidos para a migração.
2. Preparar servidores [VMware](vmware-azure-tutorial-prepare-on-premises.md) ou [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) no local. Se estás a migrar máquinas físicas, não precisas de preparar nada. Basta verificar a [matriz de suporte.](vmware-physical-azure-support-matrix.md)


## <a name="select-a-protection-goal"></a>Selecionar um objetivo de proteção

Selecione o que pretende replicar e para onde.
1. Clique em **Cofres dos Serviços de Recuperação** > cofre.
2. No Menu de Recursos, clique em **'Recuperar o Local Preparar**a  >  **Prepare Infrastructure**  >  **Proteção da Infraestruturas'.**
3. Em **Objetivo de proteção**, selecione o que pretende migrar.
    - **VMware**: selecione **Para o Azure** > **Sim, com o VMware vSphere Hypervisor**.
    - **Computador físico**: selecione **Para o Azure** > **Não virtualizado/Outro**.
    - **Hyper-V**: selecione **Para o Azure** > **Sim, com o Hyper-V**. Se as VMs de Hyper-V forem geridas pelo VMM, selecione **Sim**.


## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

**Cenário** | **Detalhes**
--- | --- 
VMware | Configurar o [ambiente de origem](vmware-azure-set-up-source.md)e configurar o [servidor de configuração](vmware-azure-deploy-configuration-server.md).
Máquina física | [Configurar](physical-azure-set-up-source.md) o ambiente de origem e o servidor de configuração.
Hyper-V | Configurar o [ambiente de origem](hyper-v-azure-tutorial.md#set-up-the-source-environment)<br/><br/> Configurar o ambiente de [origem](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment) para o Hiper-V implantado com o System Center VMM.

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

Selecione e verifique os recursos de destino.

1. Clique **em Preparar a infraestrutura**  >  **Target**e selecione a subscrição Azure que pretende utilizar.
2. Especifique o modelo de implementação do Resource Manager.
3. A Recuperação do Local verifica os recursos do Azure.
    - Se estiver a migrar VMware VMs ou servidores físicos, a Recuperação do Site verifica que tem uma rede Azure na qual os VMs Azure serão localizados quando forem criados após o failover.
    - Se estiver a migrar Hiper-VMs, a Recuperação do Site verifica que tem uma conta e rede de armazenamento Azure compatíveis.
4. Se estiver a migrar VMs Hiper-V geridos pelo System Center VMM, erfunda [o mapeamento da rede](hyper-v-vmm-azure-tutorial.md#configure-network-mapping).

## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

**Cenário** | **Detalhes**
--- | --- 
VMware | Estabeleça uma [política de replicação](vmware-azure-set-up-replication.md) para VMware VMs.
Máquina física | Estabeleça uma [política de replicação](physical-azure-disaster-recovery.md#create-a-replication-policy) para máquinas físicas.
Hyper-V | Criar uma [política de replicação](hyper-v-azure-tutorial.md#set-up-a-replication-policy)<br/><br/> Estabeleça uma política de [replicação](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy) para o Hiper-V implementado com o System Center VMM.

## <a name="enable-replication"></a>Ativar a replicação

**Cenário** | **Detalhes**
--- | --- 
VMware | [Ativar a replicação](vmware-azure-enable-replication.md) de VMware VMs.
Máquina física | [Ativar a replicação](physical-azure-disaster-recovery.md#enable-replication) para máquinas físicas.
Hyper-V | [Ativar a replicação](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> [Ativar a replicação](hyper-v-vmm-azure-tutorial.md#enable-replication) do Hiper-V implantado com o System Center VMM.


## <a name="run-a-test-migration"></a>Executar uma migração de teste

Execute uma [ativação pós-falha de teste](tutorial-dr-drill-azure.md) para o Azure para confirmar que está tudo a funcionar conforme o previsto.


## <a name="migrate-to-azure"></a>Migrar para o Azure

Execute a ativação pós-falha nos computadores que quer migrar.

1. Em **Definições**  >  **Itens replicados** clique na máquina > **Failover**.
2. Em **Ativação pós-falha**, selecione um **Ponto de Recuperação** para o qual fazer a ativação pós-falha. Selecione o ponto de recuperação mais recente.
3. A definição da chave de encriptação não é relevante para este cenário.
4. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. O Site Recovery tentará fazer um encerramento das máquinas virtuais antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode acompanhar o progresso falhado na página **Jobs.**
5. Certifique-se de que a VM do Azure é apresentada no Azure conforme esperado.
6. Em **Itens replicados**, clique com o botão direito do rato na VM > **Concluir a Migração**. Isto faz o seguinte:

   - Termina o processo de migração, para a replicação para o VM no local e para a faturação de Recuperação do Local para o VM.
   - Este passo limpa os dados de replicação. Não apaga os VMs migrados.

     ![Concluir a migração](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Não cancele uma ativação pós-falha em curso**: antes do início da ativação pós-falha, a replicação da VM é parada. Se cancelar uma ativação pós-falha que esteja em curso, a mesma para, mas a VM não será replicada outra vez.

Em alguns cenários, a ativação pós-falha requer processamento adicional, que demora cerca de oito a dez minutos a concluir. Poderá notar tempos de insípare de teste mais longos para servidores físicos, máquinas VMware Linux, VMware VMware que não têm o serviço DHCP ativado, e VMware VMware VMs que não têm os seguintes controladores de arranque: storvsc, vmbus, storflt, intelide, atapi.

## <a name="after-migration"></a>Após a migração

Depois de migrar os computadores para o Azure, tem de concluir uma série de passos.

Alguns passos podem ser automatizados como parte do processo de migração através da funcionalidade de scripts de automatização incorporados nos [planos de recuperação](site-recovery-runbook-automation.md).   


### <a name="post-migration-steps-in-azure"></a>Passos de pós-migração no Azure

- Execute otimizações de aplicação pós-migração, tais como atualizar cadeias de ligação de base de dados e configurações de servidor Web. 
- Execute testes de aplicação final e de aceitação da migração na aplicação migrada em execução no Azure.
- O [agente de VM do Azure](../virtual-machines/extensions/agent-windows.md) gere a interação da VM com o Controlador de Recursos de Infraestrutura do Azure. É necessário para alguns serviços do Azure, como o Azure Backup, o Site Recovery e a Segurança do Azure.
    - Se estiver a migrar as máquinas do VMware e os servidores físicos, o instalador do Serviço de Mobilidade instala o agente de VM do Azure em computadores Windows. Nas VMs do Linux, recomendamos que instale o agente após a ativação pós-falha.
    - Se estiver a migrar VMs Azure para uma região secundária, o agente Azure VM deve ser a provisionado no VM antes da migração.
    - Se estiver a migrar VMs Hiper-V para Azure, instale o agente Azure VM no Azure VM após a migração.
- Remova manualmente qualquer fornecedor/agente do Site Recovery da VM. Se migrar VMs VMware ou servidores físicos, desinstale o serviço de Mobilidade a partir do VM.
- Para uma maior resiliência:
    - Mantenha os dados seguros ao fazer uma cópia de segurança das VMs do Azure através do serviço Azure Backup. [Saiba mais](../backup/quick-backup-vm-portal.md).
    - Mantenha as cargas de trabalho em execução e continuamente disponíveis ao replicar VMs do Azure para uma região secundária com o Site Recovery. [Saiba mais](azure-to-azure-quickstart.md).
- Para uma maior segurança:
    - Bloqueie e limite o acesso ao tráfego de entrada com a [administração just-in-time](../security-center/security-center-just-in-time.md) do Centro de Segurança do Azure.
    - Restrinja o tráfego de rede para os pontos finais de gestão com os [Grupos de Segurança de Rede](../virtual-network/security-overview.md).
    - Implemente o [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) para ajudar a proteger discos e a manter os dados protegidos contra roubo e acesso não autorizado.
    - Leia mais sobre como [proteger recursos de IaaS]( https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/ ) e aceda ao [Centro de Segurança do Azure](https://azure.microsoft.com/services/security-center/ ).
- Para monitorização e gestão:
    - Considere implementar o [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) para monitorizar a utilização e as despesas do recurso.

### <a name="post-migration-steps-on-premises"></a>Passos de pós-migração no local

- Mova o tráfego da aplicação para a aplicação em execução na instância da VM do Azure migrada.
- Remova as VMs no local do seu inventário de VMs locais.
- Remova as VMs no local das cópias de segurança locais.
- Atualize qualquer documentação interna para mostrar a nova localização e endereço IP das VMs do Azure.




## <a name="next-steps"></a>Passos seguintes

Neste tutorial, migrou VMs no local para VMs do Azure. Now

> [!div class="nextstepaction"]
> [Criar a recuperação de desastres](azure-to-azure-replicate-after-migration.md) para uma região secundária de Azure para os VMs de Azure.

  
