---
title: Migrar VMware VMs sem agente Azure Migrate Server Migração Migrar Migração Migrar Migração
description: Aprenda a executar uma migração sem agente de VMware VMs com Azure Migrate.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: mvc
ms.openlocfilehash: 17d9d3bf787b67716fb2270cd055e30a4fefbe0f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702203"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Migrar VMware VMs para Azure (sem agente)

Este artigo mostra-lhe como migrar vMware VMs para Azure, utilizando a ferramenta [Azure Migrate:Server Migration,](migrate-services-overview.md#azure-migrate-server-migration-tool) com migração sem agentes. Também pode migrar VMware VMs usando migração baseada em agentes. [Compare](server-migrate-overview.md#compare-migration-methods) os métodos.

Este tutorial é o terceiro de uma série que demonstra como avaliar e migrar VMware VMs para Azure. 

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que possa configurar rapidamente uma prova de conceito. Os tutoriais utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. 


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicione a ferramenta Azure Migration:Server Migration.
> * Descubra VMs que quer migrar.
> * Comece a replicar VMs.
> * Faça um teste de migração para garantir que tudo está funcionando como esperado.
> * Executar uma migração completa de VM.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de:

1. [Complete o primeiro tutorial](./tutorial-discover-vmware.md) para preparar Azure e VMware para migração.
2. Recomendamos que complete o segundo tutorial para [avaliar VMware VMs](./tutorial-assess-vmware-azure-vm.md) antes de os migrar para Azure, mas não é preciso. 
3. Vá ao projeto já criado ou [crie um novo projeto](./create-manage-projects.md)
4. Verifique permissões para a sua conta Azure - A sua conta Azure necessita de permissões para criar um VM e escreva para um disco gerido azure.

## <a name="set-up-the-azure-migrate-appliance"></a>Configurar o aparelho Azure Migrate

A migração do servidor Azure Migrate é executada por um aparelho VMware VMware leve que é usado para a descoberta, avaliação e migração sem agentes de VMware VMs. Se seguir o [tutorial de avaliação,](./tutorial-assess-vmware-azure-vm.md)já instalou o aparelho. Se não o fez, instale-o agora, usando um destes métodos:

- **Modelo OVA**: [Configurar](how-to-set-up-appliance-vmware.md) num VMware VM usando um modelo OVA descarregado.
- **Script**: [Configurar](deploy-appliance-script.md) num VMware VM ou numa máquina física, utilizando um script instalador PowerShell. Este método deve ser utilizado se não puder configurar um VM usando um modelo OVA, ou se estiver no Governo Azure.

Depois de criar o aparelho, verifique se pode ligar-se ao Azure Migrate:Server Assessment, configurá-lo pela primeira vez e registá-lo com o projeto Azure Migrate.

## <a name="replicate-vms"></a>Replicar VMs

Depois de configurar o aparelho e completar a descoberta, pode começar a replicação de VMware VMs para Azure. 

- Pode executar até 500 replicações simultaneamente.
- No portal, pode selecionar até 10 VMs de uma só vez para migração. Para migrar mais máquinas, adicione-as a grupos em lotes de 10.

Ative a replicação da seguinte forma:

1. No projeto Azure Migrate > **Servers**, **Azure Migrate: Server Migration**, clique em **Replicar**.

    ![Replicar VMs](./media/tutorial-migrate-vmware/select-replicate.png)

2. Em **Replicar**, **Definições de origem** > **Os computadores estão virtualizados?**, selecione **Sim, com o VMware vSphere**.
3. Em **Aplicação no local**, selecione o nome da aplicação do Azure Migrate que configurou > **OK**. 

    ![Definições de origem](./media/tutorial-migrate-vmware/source-settings.png)

4. Em **Máquinas virtuais**, selecione aquelas que quer replicar. Para aplicar o tamanho de VM e o tipo de disco a partir de uma avaliação se executou um, em **definições de migração de importação a partir de uma avaliação de Azure Migrate?** Se não estiver a utilizar as definições de avaliação, selecione **Nº**.
   
    ![Selecionar avaliação](./media/tutorial-migrate-vmware/select-assessment.png)

5. Nas **máquinas Virtuais,** selecione VMs que pretende migrar. Em seguida, clique em **Seguinte: Definições de destino**.

    ![Selecione VMs](./media/tutorial-migrate-vmware/select-vms.png)

6. Nas **definições target**, selecione a região de subscrição e alvo. Especificar o grupo de recursos em que os VMs Azure residem após a migração.
7. Na **Rede Virtual,** selecione o Azure VNet/subnet a que os VMs Azure se juntam após a migração.
8. Nas **opções de Disponibilidade,** selecione:
    -  Zona de Disponibilidade para fixar a máquina migrada a uma zona de disponibilidade específica na região. Utilize esta opção para distribuir servidores que formam um nível de aplicação de vários nós em zonas de disponibilidade. Se selecionar esta opção, terá de especificar a Zona de Disponibilidade para utilizar para cada uma das máquinas selecionadas no separador Compute. Esta opção só está disponível se a região-alvo selecionada para a migração suportar Zonas de Disponibilidade
    -  Disponibilidade Definir para colocar a máquina migrada num Conjunto de Disponibilidade. O Grupo de Recursos-Alvo que foi selecionado deve ter um ou mais conjuntos de disponibilidade para utilizar esta opção.
    - Não é necessária nenhuma opção de redundância de infraestrutura se não precisar de nenhuma destas configurações de disponibilidade para as máquinas migradas.
9. No **tipo de encriptação do disco,** selecione:
    - Encriptação em repouso com chave gerida pela plataforma
    - Encriptação em repouso com chave gerida pelo cliente
    - Dupla encriptação com chaves geridas pela plataforma e geridas pelo cliente

   > [!NOTE]
   > Para replicar VMs com CMK, terá de [criar uma encriptação de disco definida](https://go.microsoft.com/fwlink/?linkid=2151800) no Grupo de Recursos alvo. Um conjunto de conjunto de encriptação de disco mapeia Discos Geridos para um Cofre de Chaves que contém o CMK para usar para SSE.
  
10. Em **Benefício Híbrido do Azure**:

    - Selecione **Não** se não quiser aplicar o Benefício Híbrido do Azure. Em seguida, clique em **Seguinte**.
    - Selecione **Sim** se tiver computadores Windows Server abrangidos com subscrições ativas do Software Assurance ou do Windows Server e quiser aplicar o benefício aos computadores que está a migrar. Em seguida, clique em **Seguinte**.

    ![Definições de destino](./media/tutorial-migrate-vmware/target-settings.png)

11. Em **Compute**, In Compute, reveja o nome VM, tamanho, tipo de disco de SO e configuração de disponibilidade (se selecionado no passo anterior). As VMs devem estar em conformidade com os [requisitos do Azure](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

    - **Tamanho VM**: Se estiver a utilizar recomendações de avaliação, o dropdown do tamanho VM mostra o tamanho recomendado. Caso contrário, o Azure Migrate escolhe um tamanho com base na correspondência mais próxima na subscrição do Azure. Como alternativa, escolha um tamanho manual em **Tamanho de VMs do Azure**. 
    - **Disco de os:** Especifique o disco DE (arranque) para o VM. O disco do SO é o disco que possui o carregador e o instalador do sistema operativo. 
    - **Zona de Disponibilidade**: Especifique a Zona de Disponibilidade a utilizar.
    - **Conjunto de disponibilidade**: Especifique o Conjunto de Disponibilidade a utilizar.

    > [!NOTE]
    > Se quiser selecionar uma opção de disponibilidade diferente para um conjunto de máquinas virtuais, vá ao passo 1 e repita os passos selecionando diferentes opções de disponibilidade após iniciar a replicação para um conjunto de máquinas virtuais.


 ![Definições de cálculo VM](./media/tutorial-migrate-vmware/compute-settings.png)

12. Em **Discos**, especifique se os discos das VMs devem ser replicados para o Azure e selecione o tipo de disco (discos geridos SSD/HDD standard ou premium) no Azure. Em seguida, clique em **Seguinte**.
   
    ![A screenshot mostra o separador Discos da caixa de diálogo Replicate.](./media/tutorial-migrate-vmware/disks.png)

13. Em **Analisar e iniciar a replicação**, analise as definições e clique em **Replicar** para iniciar a replicação inicial para os servidores.

> [!NOTE]
> Pode atualizar as definições de replicação a qualquer momento antes do início da replicação **(Gerir**  >  **máquinas de replicação).** Não é possível alterar as definições após o início da replicação.

### <a name="provisioning-for-the-first-time"></a>Provisionamento pela primeira vez

Se este for o primeiro VM que está a replicar no projeto, a Migração de Servidors fornece automaticamente estes recursos, no mesmo grupo de recursos que o projeto.

- **Autocarro de** serviço : A Migração do Servidor utiliza o autocarro de serviço para enviar mensagens de orquestração de replicação para o aparelho.
- **Conta de armazenamento gateway**: A migração do servidor utiliza a conta de armazenamento de gateway para armazenar informações do Estado sobre os VMs que estão a ser replicados.
- **Conta de armazenamento de** registo : O aparelho Azure Migrate envia registos de replicação de VMs para uma conta de armazenamento de registo. Azure Migrate aplica a informação de replicação aos discos geridos por réplicas.
- **Cofre chave**: O aparelho Azure Migrate utiliza o cofre-chave para gerir as cordas de ligação do autocarro de serviço e as chaves de acesso para as contas de armazenamento utilizadas na replicação.

## <a name="track-and-monitor"></a>Rastreio e monitor

1. Acompanhe o estado do trabalho nas notificações do portal.
2. Monitorize o estado de replicação clicando em **servidores replicantes** em **Azure Migrate: Migração do servidor**.

     ![Monitorizar a replicação](./media/tutorial-migrate-vmware/replicating-servers.png)

A replicação ocorre da seguinte forma:
- Quando o trabalho de Replicação inicial termina com sucesso, as máquinas iniciam a sua replicação inicial ao Azure.
- Durante a replicação inicial, é criado um instantâneo VM. Os dados do disco do instantâneo são replicados em discos geridos por réplica em Azure.
- Após o final da replicação inicial, começa a replicação delta. As alterações incrementais nos discos no local são periodicamente replicadas nos discos de réplica em Azure.

## <a name="run-a-test-migration"></a>Executar uma migração de teste


Quando a replicação delta começar, pode fazer uma migração de teste para os VMs, antes de executar uma migração completa para Azure. Recomendamos vivamente que o faça pelo menos uma vez por cada máquina, antes de a migrar.

- A realização de um teste de controlos de migração de que a migração funcionará como esperado, sem afetar as máquinas no local, que permanecem operacionais, e continuam a replicar-se. 
- A migração de testes simula a migração criando um VM Azure usando dados replicados (normalmente migrando para um VNet não produtivo na sua assinatura Azure).
- Pode utilizar o teste replicado Azure VM para validar a migração, realizar testes de aplicações e resolver quaisquer problemas antes da migração completa.

Faça uma migração de teste da seguinte forma:


1. Nos **objetivos de migração**  >  **Servers**  >  **Azure Migrate: Server Migration**, clique em Test **servidores migrados**.

     ![Testar servidores migrados](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Clique com o botão direito do rato na VM a testar e, em seguida, clique em **Testar migração**.

    ![Testar migração](./media/tutorial-migrate-vmware/test-migrate.png)

3. Em **Testar Migração**, selecione a VNet do Azure na qual a VM do Azure vai estar localizada após a migração. Recomendamos que utilize uma VNet que não seja de produção.
4. A tarefa **Teste de migração** é iniciada. Monitorize a tarefa nas notificações do portal.
5. Após a conclusão da migração, veja a VM do Azure migrada em **Máquinas Virtuais** no portal do Azure. O nome do computador tem um sufixo **-Test**.
6. Após terminar o teste, clique com o botão direito do rato na VM do Azure em **Replicar computadores** e clique em **Limpar teste de migração**.

    ![Limpar a migração](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Migrar VMs

Depois de verificar que a migração do teste funciona como esperado, pode migrar as máquinas no local.

1. No projeto Azure Migrate > **Servers**  >  **Azure Migrate: Server Migration**, clique em **Replicating servidores**.

    ![Replicar servidores](./media/tutorial-migrate-vmware/replicate-servers.png)

2. Em **Replicar computadores**, clique com o botão direito do rato na VM > **Migrar**.
3. Em **Migrar**  >  **Desligue as máquinas virtuais e realize uma migração planeada sem perda de dados**, selecione **Yes**  >  **OK**.
    - Por predefinição, o Azure Migrate desliga a VM no local e executa uma replicação a pedido para sincronizar quaisquer alterações à VM ocorridas desde a última replicação. Tal garante que não haja perda de dados.
    - Se não quiser desligar a VM, selecione **Não**
4. Uma tarefa de migração é iniciada para a VM. Controle a tarefa nas notificações do Azure.
5. Após a conclusão da tarefa, pode ver e gerir a VM na página **Máquinas Virtuais**.

## <a name="complete-the-migration"></a>Complete a migração

1. Depois de terminar a migração, clique com a direita na replicação de > **de paragem VM**. Isto para a replicação para a máquina no local e limpa as informações do estado de replicação para o VM.
2. Instale o agente Azure VM [Linux](../virtual-machines/extensions/agent-linux.md) nas máquinas migradas se a máquina tiver Linux OS. Instalamos automaticamente o agente VM para VMs do Windows durante a migração.
3. Execute otimizações de aplicação pós-migração, tais como atualizar cadeias de ligação de base de dados e configurações de servidor Web.
4. Execute testes de aplicação final e de aceitação da migração na aplicação migrada em execução no Azure.
5. Corte o tráfego para o exemplo de Azure VM migrado.
6. Remova as VMs no local do seu inventário de VMs locais.
7. Remova as VMs no local das cópias de segurança locais.
8. Atualize qualquer documentação interna para mostrar a nova localização e endereço IP das VMs do Azure. 

## <a name="post-migration-best-practices"></a>Boas práticas pós-migração

- Para uma maior resiliência:
    - Mantenha os dados seguros ao fazer uma cópia de segurança das VMs do Azure através do serviço Azure Backup. [Saiba mais](../backup/quick-backup-vm-portal.md).
    - Mantenha as cargas de trabalho em execução e continuamente disponíveis ao replicar VMs do Azure para uma região secundária com o Site Recovery. [Saiba mais](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Para uma maior segurança:
    - Bloqueie e limite o acesso ao tráfego de entrada com [o Azure Security Center - Mesmo a tempo da administração](../security-center/security-center-just-in-time.md).
    - Restrinja o tráfego de rede para os pontos finais de gestão com os [Grupos de Segurança de Rede](../virtual-network/network-security-groups-overview.md).
    - Implemente o [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) para ajudar a proteger discos e a manter os dados protegidos contra roubo e acesso não autorizado.
    - Leia mais sobre como [proteger recursos de IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) e aceda ao [Centro de Segurança do Azure](https://azure.microsoft.com/services/security-center/).
- Para monitorização e gestão:
-  Considere implementar o [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) para monitorizar a utilização e as despesas do recurso.


## <a name="next-steps"></a>Passos seguintes

Investigue a [jornada de migração](/azure/architecture/cloud-adoption/getting-started/migrate) em nuvem no Quadro de Azure Cloud Adopt.