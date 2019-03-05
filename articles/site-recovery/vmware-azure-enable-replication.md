---
title: Ativar a replicação de VMs de VMware para recuperação de desastre do VMware para o Azure com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve como ativar a replicação de VMs de VMware para recuperação após desastre para o Azure com o Azure Site Recovery.
author: mayurigupta13
ms.service: site-recovery
ms.date: 3/3/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 043a49e679f2b8ec48213e28d229121d3f0e070d
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338642"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Ativar a replicação para o Azure para VMs de VMware


Este artigo descreve como ativar a replicação de VMs de VMware no local para o Azure.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem:

1.  [Configurar o ambiente de origem no local](vmware-azure-set-up-source.md).
2.  [Configurar o ambiente de destino no Azure](vmware-azure-set-up-target.md).


## <a name="before-you-start"></a>Antes de começar
Ao replicar máquinas virtuais VMware:

* Sua conta de utilizador do Azure tem de ter determinado [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para ativar a replicação de uma nova máquina virtual para o Azure.
* VMs do VMware são detetadas a cada 15 minutos. Pode demorar 15 minutos ou mais tempo para serem apresentadas no portal do Azure após a deteção. Da mesma forma, deteção pode demorar 15 minutos ou mais quando adiciona um novo vCenter server ou no anfitrião vSphere.
* Alterações de ambiente na máquina virtual (por exemplo, a instalação de ferramentas do VMware) podem demorar 15 minutos ou mais para ser atualizado no portal.
* Pode verificar a última hora de deteção para VMs de VMware no **último contacto em** campo para o vCenter server/anfitrião vSphere, sobre o **servidores de configuração** página.
* Para adicionar máquinas para a replicação sem aguardar a deteção agendada, realce o servidor de configuração (não clique nele) e clique nas **atualizar** botão.
* Quando ativa a replicação, se a máquina fica preparada, o servidor de processos instala automaticamente o serviço de mobilidade no mesmo.


## <a name="enable-replication"></a>Ativar a replicação

1. Clique em **passo 2: Replicar aplicação** > **origem**. Depois de ativar a replicação pela primeira vez, clique em **+Replicar**, no cofre, para ativar a replicação em máquinas adicionais.
2. Na **origem** página > **origem**, selecione o servidor de configuração.
3. Na **tipo de máquina**, selecione **máquinas virtuais** ou **máquinas físicas**.
4. Em **vCenter/vSphere Hypervisor**, selecione o servidor vCenter que gere o anfitrião vSphere ou selecione o anfitrião. Esta definição não é relevante se estiver a replicar máquinas físicas.
5. Selecione o servidor de processos, que será o nome do servidor de configuração se ainda não criou quaisquer servidores de processos adicionais. Em seguida, clique em **OK**.

    ![Ativar a origem de replicação](./media/vmware-azure-enable-replication/enable-replication2.png)

6. Na **destino**, selecione a subscrição e o grupo de recursos onde pretende criar as máquinas de virtuais com ativação pós-falha. Escolha o modelo de implementação que pretende utilizar no Azure para as máquinas de virtuais com ativação pós-falha.

7. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure estabelecerão uma ligação quando se puserem em marcha após a ativação pós-falha. A rede tem de estar na mesma região que o cofre de Serviços de Recuperação. Selecione **Configurar agora para as máquinas selecionadas** para aplicar a definição de rede para todas as máquinas selecionadas para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por máquina. Se não tiver uma rede, terá de criar uma. Para criar uma rede utilizando o Gestor de recursos, clique em **criar novo**. Selecione uma sub-rede, se aplicável e, em seguida, clique em **OK**.

    ![Ativar a definição de destino de replicação](./media/vmware-azure-enable-replication/enable-rep3.png)
8. Em **Máquinas Virtuais** > **Selecionar máquinas virtuais**, selecione cada máquina que pretende replicar. Só pode selecionar máquinas para as quais a replicação pode ser ativada. Em seguida, clique em **OK**. Se não é possível ver/selecionar qualquer máquina virtual específica, clique em [aqui](https://aka.ms/doc-plugin-VM-not-showing) para resolver o problema.

    ![Ativar replicação selecione máquinas de virtuais](./media/vmware-azure-enable-replication/enable-replication5.png)
9. Na **propriedades** > **configurar propriedades**, selecione a conta utilizada pelo servidor de processos para instalar automaticamente o serviço de mobilidade na máquina.  
10. Por predefinição, todos os discos são replicados. Para excluir discos da replicação, clique em **todos os discos** e desmarque quaisquer discos que não pretende replicar.  Em seguida, clique em **OK**. Pode definir as propriedades adicionais mais tarde. [Saiba mais](vmware-azure-exclude-disk.md) informações sobre como excluir discos.

    ![Ativar a replicação de configurar as propriedades](./media/vmware-azure-enable-replication/enable-replication6.png)

11. Em **Definições de replicação** > **Configurar as definições de replicação**, certifique-se de que a política de replicação correta está selecionada. Pode modificar as definições de política de replicação na **configurações** > **políticas de replicação** > (nome da política) > **editar definições de**. As alterações que aplicar a uma política também se aplicam a replicação e novas máquinas.
12. Ativar **consistência multi-VM** se deseja coletar máquinas num grupo de replicação. Especifique um nome para o grupo e, em seguida, clique em **OK**. 

    > [!NOTE]

    >    * As máquinas num grupo de replicação replicar em conjunto e tem partilhado pontos de recuperação consistentes com falhas e consistente com a aplicação quando eles efetuar a ativação pós-falha.
    >    * Reunir VMs e servidores físicos para que eles espelham suas cargas de trabalho. Ativar a consistência multi-VM pode afetar o desempenho da carga de trabalho. Utilize apenas se os computadores estão executando a mesma carga de trabalho e precisar de consistência.

    ![Ativar a replicação](./media/vmware-azure-enable-replication/enable-replication7.png)
13. Clique em **Ativar Replicação**. Pode controlar o progresso da tarefa **Ativar Proteção** em **Definições** > **Tarefas** > **Tarefas do Site Recovery**. Depois de a tarefa **Finalizar Proteção** ser executada, a máquina está preparada para ativação pós-falha.



## <a name="view-and-manage-vm-properties"></a>Ver e gerir propriedades da VM

Em seguida, verifique as propriedades da máquina de origem. Lembre-se de que o nome de VM do Azure tem de estar em conformidade com [requisitos de máquina virtual do Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Clique em **configurações** > **itens replicados** > e, em seguida, selecione a máquina. O **Essentials** página mostra informações sobre as definições da máquina e o estado.
2. Em **Propriedades**, pode ver as informações de replicação e de ativação pós-falha da VM.
3. Na **computação e rede** > **propriedades de computação**, pode alterar vários propoerties VM:
    * VM do Azure nome - modificar o nome para atender aos requisitos do Azure, se necessário
    * Tamanho de VM de destino ou tipo de VM - a predefinição de tamanho da VM é escolhido com base na origem de tamanho da VM. Pode selecionar um tamanho VM diferente com base na necessidade de qualquer altura antes da ativação pós-falha. Tenha em atenção que o tamanho de disco da VM também se baseia no tamanho do disco de origem e apenas pode ser alterado após a ativação pós-falha. Saiba mais sobre tamanhos de disco e IOPS em nosso [destinos de escalabilidade para discos](../virtual-machines/windows/disk-scalability-targets.md) artigo.

    ![Propriedades da rede e computação](./media/vmware-azure-enable-replication/vmproperties.png)

    *  Grupo de recursos - pode selecionar uma [grupo de recursos](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines) do que uma máquina torna-se parte de uma após a ativação pós-falha. Pode alterar esta definição qualquer altura antes da ativação pós-falha. Após a ativação pós-falha, se migrar a máquina a um grupo de recursos diferente, as definições de proteção para essa garantia de reparação de máquina.
    * Conjunto de disponibilidade - pode selecionar uma [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) se seu computador tem de ser parte de uma após a ativação pós-falha. Enquanto estiver a selecionar um conjunto de disponibilidade, tenha em atenção que:

        * São listados apenas conjuntos de disponibilidade que pertencem ao grupo de recursos especificado.  
        * As máquinas com redes virtuais diferentes, não podem ser uma parte do mesmo conjunto de disponibilidade.
        * Apenas as máquinas virtuais do mesmo tamanho pode ser uma parte de um conjunto de disponibilidade.
4. Também pode ver e adicionar informações sobre a rede de destino, a sub-rede e o endereço IP atribuído à VM do Azure.
5. Na **discos**, pode ver o sistema operativo e os discos de dados na VM a replicar.

### <a name="configure-networks-and-ip-addresses"></a>Configurar as redes e endereços IP

- Pode definir o endereço IP de destino. Se não fornecer um endereço, a máquina de efetuada a ativação pós-falha utiliza o DHCP. Se definir um endereço que não está disponível na ativação pós-falha, a ativação pós-falha não funciona. Se o endereço está disponível na rede de ativação pós-falha de teste, o mesmo endereço IP de destino pode ser utilizado para ativação pós-falha de teste.
- O número de adaptadores de rede é ditado pelo tamanho especificado para a máquina virtual de destino, da seguinte forma:
    - Se o número de adaptadores de rede na máquina de origem for menor ou igual ao número de adaptadores permitido para o tamanho de máquina de destino, em seguida, o destino tem o mesmo número de adaptadores de como a origem.
    - Se o número de adaptadores da máquina virtual de origem exceder o número permitido para o tamanho de destino, será utilizado o tamanho máximo de destino.
    Por exemplo, se uma máquina de origem tiver dois adaptadores de rede e o tamanho da máquina de destino suporta quatro, a máquina de destino tem dois adaptadores. Se a máquina de origem tiver dois adaptadores, mas o tamanho de destino só suporta um, o computador de destino tem apenas um adaptador.
    - Se a máquina virtual tem vários adaptadores de rede, todos eles ligam à mesma rede. Além disso, a primeira mostrada na lista torna-se a *predefinido* adaptador de rede na máquina virtual do Azure.

### <a name="azure-hybrid-benefit"></a>Benefício Híbrido do Azure

Os clientes do Software Assurance da Microsoft podem utilizar o benefício híbrido do Azure para reduzir os custos de máquinas do Windows Server que são migradas para o Azure de licenciamento, ou para utilizar o Azure para recuperação após desastre. Se está qualificado para utilizar o benefício híbrido do Azure, pode especificar que a máquina virtual atribuída este benefício é o que Azure Site Recovery cria se houver uma ativação pós-falha. Para efetuar este procedimento:
- Vá para a secção de propriedades de computação e rede da máquina virtual replicada.
- Responda à pergunta que pergunta se tiver uma licença de servidor do Windows que o torna elegível para o benefício híbrido do Azure.
- Selecione a caixa de verificação para confirmar que tem uma licença elegível do Windows Server com Software Assurance, que pode utilizar para aplicar o benefício híbrido do Azure na máquina que será criada na ativação pós-falha.
- Guarde as definições para a máquina replicada.

Saiba mais sobre [benefício híbrido do Azure](https://aka.ms/azure-hybrid-benefit-pricing).

## <a name="common-issues"></a>Problemas comuns

* Cada disco deve ter menos de 4 TB de tamanho.
* O disco do SO deve ser um disco básico e não um disco dinâmico.
* Para 2/preparado para UEFI máquinas virtuais de geração, a família do sistema operativo deve ser o Windows e o disco de arranque deve ser inferior a 300 GB.

## <a name="next-steps"></a>Passos Seguintes

Depois da proteção está concluída e a máquina atingiu um estado protegido, pode experimentar uma [ativação pós-falha](site-recovery-failover.md) para verificar se seu aplicativo é exibido no Azure ou não.

* Saiba como [limpar as definições de registo e a proteção](site-recovery-manage-registration-and-protection.md) para desativar a replicação.
* Saiba como [automatizar a replicação para as suas máquinas virtuais com o Powershell](vmware-azure-disaster-recovery-powershell.md)
