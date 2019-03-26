---
title: Ativar a replicação de VMs de VMware para recuperação após desastre para o Azure com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve como ativar VMs de VMware para replicação para o Azure para recuperação após desastre com o Azure Site Recovery.
author: mayurigupta13
ms.service: site-recovery
ms.date: 3/6/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 472ff7810852bd03ef322cd5eb647c3d61f09b01
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418114"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Ativar a replicação para o Azure para VMs de VMware

Este artigo descreve como ativar a replicação de VMs de VMware no local para o Azure.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem:

- [Configurar o ambiente de origem no local](vmware-azure-set-up-source.md).
- [Configurar o ambiente de destino no Azure](vmware-azure-set-up-target.md).

## <a name="before-you-start"></a>Antes de começar
Quando está a replicar máquinas virtuais VMware, mantenha estas informações em mente:

* Sua conta de utilizador do Azure tem de ter determinado [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para ativar a replicação de uma nova máquina virtual para o Azure.
* VMs do VMware são detetadas a cada 15 minutos. Pode demorar 15 minutos ou mais tempo para as VMs a aparecer no portal do Azure após a deteção. Da mesma forma, a deteção pode demorar 15 minutos ou mais quando adiciona um novo vCenter server ou no anfitrião vSphere.
* Pode demorar 15 minutos ou mais tempo para que as alterações de ambiente na máquina virtual (por exemplo, a instalação de ferramentas do VMware) para ser atualizado no portal.
* Pode verificar o tempo detetados por último para VMs de VMware: Consulte a **último contacto em** campo a **servidores de configuração** página para o vCenter server/anfitrião vSphere.
* Para adicionar máquinas virtuais para a replicação sem aguardar a deteção agendada, realce o servidor de configuração (mas não clique nele) e selecione **atualizar**.
* Quando ativa a replicação, se a máquina virtual é preparada, o servidor de processos instala automaticamente o serviço de mobilidade de recuperação de Site do Azure no mesmo.

## <a name="enable-replication"></a>Ativar a replicação

Antes de seguir os passos nesta secção, tenha em atenção as seguintes informações:
* O Azure Site Recovery replica agora diretamente para os managed disks para todas as replicações de novo. O servidor de processos escreve os registos de replicação para uma conta de armazenamento de cache na região de destino. Estes registos são utilizados para criar pontos de recuperação em discos geridos de réplica.
* No momento da ativação pós-falha, o ponto de recuperação que selecionar é utilizado para criar o disco gerido de destino.
* As VMs que foram anteriormente configuradas para replicar a contas de armazenamento de destino não são afetadas.
* A replicação para contas de armazenamento para uma nova máquina virtual só está disponível através de um Estado REST (Representational Transfer) API e Powershell. Utilize a API de REST do Azure versão 2016 a 08-10 ou 2018-01-10 para replicar a contas de armazenamento.

1. Aceda a **passo 2: Replicar aplicação** > **origem**. Depois de ativar a replicação pela primeira vez, selecione **+ replicar** no cofre para ativar a replicação para máquinas virtuais adicionais.
1. Na **origem** página > **origem**, selecione o servidor de configuração.
1. Para **tipo de máquina**, selecione **máquinas virtuais** ou **máquinas físicas**.
1. Em **vCenter/vSphere Hypervisor**, selecione o servidor vCenter que gere o anfitrião vSphere ou selecione o anfitrião. Esta definição não é relevante se estiver a replicar computadores físicos.
1. Selecione o servidor de processos, que será o servidor de configuração se ainda não criou quaisquer servidores de processos adicionais. Em seguida, selecione **OK**.

    ![Ativar a janela de origem de replicação](./media/vmware-azure-enable-replication/enable-replication2.png)

1. Para **destino**, selecione o grupo de recursos e subscrição onde pretende criar as máquinas de virtuais com ativação pós-falha. Escolha o modelo de implementação que pretende utilizar no Azure para as VMs com ativação pós-falha.

1. Selecione a rede do Azure e a sub-rede que o Azure se ligarão as VMs após a ativação pós-falha. A rede tem de estar na mesma região que o Cofre de serviço de recuperação de sites.

   Selecione **configurar agora para as máquinas selecionadas** para aplicar a definição de rede para todas as máquinas virtuais que selecionou para proteção. Selecione **configurar mais tarde** para selecionar a rede do Azure por máquina virtual. Se não tiver uma rede, terá de criar uma. Para criar uma rede utilizando o Gestor de recursos do Azure, selecione **criar novo**. Selecione uma sub-rede, se aplicável e, em seguida, selecione **OK**.
   
   ![Ativar a janela de destino de replicação](./media/vmware-azure-enable-replication/enable-rep3.png)

1. Para **máquinas virtuais** > **selecionar máquinas virtuais**, selecione cada máquina virtual que pretende replicar. Pode selecionar apenas as máquinas virtuais para o qual os replicação pode ser ativada. Em seguida, selecione **OK**. Se não pode ver ou selecionar qualquer máquina virtual específica, consulte [máquina de origem não estiver listada no portal do Azure](https://aka.ms/doc-plugin-VM-not-showing) para resolver o problema.

    ![Ativar a janela selecione máquinas de virtuais de replicação](./media/vmware-azure-enable-replication/enable-replication5.png)

1. Para **propriedades** > **configurar propriedades**, selecione a conta que utiliza o servidor de processos para instalar automaticamente o serviço de mobilidade do Site Recovery na máquina virtual. Além disso, escolha o tipo de disco gerido de destino para replicar para com base nos seus dados, padrões de alterações a dados.
10. Por predefinição, todos os discos da máquina de virtual de origem são replicados. Para excluir discos da replicação, desmarque a **inclusão** caixa de verificação de quaisquer discos que não pretende replicar. Em seguida, selecione **OK**. Pode definir as propriedades adicionais mais tarde. Saiba mais sobre [excluir discos](vmware-azure-exclude-disk.md).

    ![Ativar a replicação de configurar a janela de propriedades](./media/vmware-azure-enable-replication/enable-replication6.png)

1. Em **as definições de replicação** > **configurar as definições de replicação**, certifique-se de que a política de replicação correta está selecionada. Pode modificar as definições de política de replicação em **configurações** > **políticas de replicação** > ***nome da política***  >  **Editar definições**. As alterações que aplicar a uma política também se aplicam a replicação e novas máquinas virtuais.
1. Ativar **consistência multi-VM** se deseja coletar as máquinas virtuais num grupo de replicação. Especifique um nome para o grupo e, em seguida, selecione **OK**.

    > [!NOTE]
    >    * Máquinas virtuais num grupo de replicação replicar em conjunto e partilhar pontos de recuperação consistentes com falhas e consistente com a aplicação quando eles efetuar a ativação pós-falha.
    >    * Reunir VMs e servidores físicos para que eles espelham suas cargas de trabalho. Ativar a consistência multi-VM pode afetar o desempenho da carga de trabalho. Opte por fazê-lo apenas se as máquinas virtuais estão a executar a mesma carga de trabalho e precisar de consistência.

    ![Ativar a janela de replicação](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. Selecione **Ativar Replicação**. Pode acompanhar o progresso dos **ativar proteção** da tarefa na **definições** > **tarefas** > **tarefas de recuperação de Site**. Depois do **finalizar proteção** tarefa é executada, a máquina virtual está preparada para ativação pós-falha.

## <a name="view-and-manage-vm-properties"></a>Ver e gerir propriedades da VM

Em seguida, verifique se as propriedades da máquina virtual de origem. Lembre-se de que o nome de VM do Azure tem de estar em conformidade com [requisitos de máquina virtual do Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Aceda a **configurações** > **itens replicados**e, em seguida, selecione a máquina virtual. O **Essentials** página mostra informações sobre as definições e o estado da VM.
1. Em **Propriedades**, pode ver as informações de replicação e de ativação pós-falha da VM.
1. Na **computação e rede** > **propriedades de computação**, pode alterar várias propriedades VM. 

    ![Janela de propriedades de computação e rede](./media/vmware-azure-enable-replication/vmproperties.png)

    * Nome da VM do Azure: Modifique o nome para atender aos requisitos do Azure, se necessário.
    * Tamanho de VM de destino ou tipo de VM: O tamanho VM predefinido é escolhido com base na origem de tamanho da VM. Pode selecionar um tamanho VM diferente com base nas suas necessidades em qualquer altura antes da ativação pós-falha. Tenha em atenção que o tamanho de disco da VM também se baseia no tamanho do disco de origem, e só pode ser alterado após a ativação pós-falha. Saiba mais sobre tamanhos de disco e taxas de IOPS em [metas de escalabilidade e desempenho para discos VM no Windows](../virtual-machines/windows/disk-scalability-targets.md).

    *  Grupo de recursos: Pode selecionar uma [grupo de recursos](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines), do que uma máquina virtual se torna parte de uma após a ativação pós-falha. Pode alterar esta definição em qualquer altura antes da ativação pós-falha. Após a ativação pós-falha, se migrar a máquina virtual a um grupo de recursos diferente, interromper as definições de proteção para essa máquina virtual.
    * Conjunto de disponibilidade: Pode selecionar uma [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) se a sua máquina virtual tem de ser uma parte de uma após a ativação pós-falha. Quando seleciona um conjunto de disponibilidade, considere as seguintes informações:

        * São listados apenas conjuntos de disponibilidade que pertencem ao grupo de recursos especificado.  
        * VMs que estão em redes virtuais diferentes, não podem ser uma parte do mesmo conjunto de disponibilidade.
        * Apenas as máquinas virtuais do mesmo tamanho pode ser uma parte de um conjunto de disponibilidade.
1. Também pode adicionar informações sobre a rede de destino, a sub-rede e o endereço IP que é atribuído à VM do Azure.
2. Na **discos**, pode ver o sistema operativo e os discos de dados na VM que vai ser replicada.

### <a name="configure-networks-and-ip-addresses"></a>Configurar as redes e endereços IP

Pode definir o endereço IP de destino. Se não fornecer um endereço, a máquina de virtual com ativação pós-falha utiliza o DHCP. Se definir um endereço que não está disponível na ativação pós-falha, a ativação pós-falha não funciona. Se o endereço está disponível na rede de ativação pós-falha de teste, pode utilizar o mesmo endereço IP de destino para a ativação pós-falha de teste.

O número de adaptadores de rede é ditado pelo tamanho que especificar para a máquina virtual de destino, da seguinte forma:

- Se o número de adaptadores de rede na máquina de virtual de origem for menor ou igual ao número de adaptadores que são permitidos para o tamanho da VM de destino, o destino tem o mesmo número de adaptadores de como a origem.
- Se o número de adaptadores da máquina virtual de origem exceder o número permitido para o tamanho da VM de destino, é utilizado o tamanho máximo de destino. Por exemplo, se uma máquina de virtual de origem tiver dois adaptadores de rede e o tamanho da VM de destino suporta quatro, a máquina virtual de destino tem dois adaptadores. Se a VM de origem tiver dois adaptadores, mas o tamanho de destino só suporta um, a VM de destino tem apenas um adaptador.
- Se a máquina virtual tem vários adaptadores de rede, todos eles ligam à mesma rede. Além disso, o primeiro adaptador, que é apresentado na lista torna-se a *predefinição* adaptador de rede na máquina virtual do Azure. 

### <a name="azure-hybrid-benefit"></a>Benefício Híbrido do Azure

Os clientes do Software Assurance da Microsoft podem utilizar o benefício híbrido do Azure para poupar nos custos de licenciamento para computadores Windows Server que são migradas para o Azure. O benefício também se aplica a recuperação após desastre do Azure. Se for elegível, pode atribuir o benefício para a máquina virtual que o Site Recovery cria se houver uma ativação pós-falha. Para tal, siga estes passos:
1. Vá para o **propriedades do computador e da rede** da máquina virtual replicada.
2. Responder a quando lhe for perguntado se tem uma licença do Windows Server que o torna elegível para o benefício híbrido do Azure.
3. Confirme que tem uma licença elegível do Windows Server com Software Assurance, que pode utilizar para aplicar o benefício para a VM que será criada na ativação pós-falha.
4. Guarde as definições para a máquina virtual replicada.

Saiba mais sobre [benefício híbrido do Azure](https://aka.ms/azure-hybrid-benefit-pricing).

## <a name="resolve-common-issues"></a>Resolver problemas comuns

* Cada disco deve ser inferior a 4 TB.
* O disco do SO deve ser um disco básico, não é um disco dinâmico.
* Para 2/preparado para UEFI máquinas virtuais de geração, a família do sistema operativo deve ser o Windows e o disco de arranque deve ser inferior a 300 GB.

## <a name="next-steps"></a>Passos Seguintes

Depois que a máquina virtual atingir um estado protegido, experimente um [ativação pós-falha](site-recovery-failover.md) para verificar se a sua aplicação é apresentada no Azure.

* Saiba como [limpar as definições de registo e a proteção](site-recovery-manage-registration-and-protection.md) para desativar a replicação.
* Saiba como [automatizar a replicação para as suas máquinas virtuais com o Powershell](vmware-azure-disaster-recovery-powershell.md).
