---
title: Habilitar VMs VMware para recuperação de desastre usando Azure Site Recovery
description: Este artigo descreve como habilitar a replicação de VM do VMware para recuperação de desastre usando o serviço de Azure Site Recovery
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 06/28/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 10b3e572ec61d1eff342f24a6a5a7bcba6276983
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495384"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Habilitar a replicação para o Azure para VMs VMware

Este artigo descreve como habilitar a replicação de VMs VMware locais para o Azure.

## <a name="resolve-common-issues"></a>Resolver problemas comuns

* Cada disco deve ter menos de 4 TB.
* O disco do sistema operacional deve ser um disco básico, não um disco dinâmico.
* Para máquinas virtuais habilitadas para geração 2/UEFI, a família do sistema operacional deve ser Windows e o disco de inicialização deve ter menos de 300 GB.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem:

- [Configure seu ambiente de origem local](vmware-azure-set-up-source.md).
- [Configure seu ambiente de destino no Azure](vmware-azure-set-up-target.md).
- [Verifique os requisitos e os pré-requisitos](vmware-physical-azure-support-matrix.md) antes de começar. As coisas importantes a serem observadas incluem:
    - [Sistemas operacionais com suporte](vmware-physical-azure-support-matrix.md#replicated-machines) para computadores replicados.
    - Suporte a [armazenamento/disco](vmware-physical-azure-support-matrix.md#storage) .
    - [Requisitos do Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements) com os quais os computadores locais devem estar em conformidade.


## <a name="before-you-start"></a>Antes de começar
Quando você estiver replicando máquinas virtuais VMware, lembre-se destas informações:

* Sua conta de usuário do Azure precisa ter determinadas [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para habilitar a replicação de uma nova máquina virtual para o Azure.
* As VMs VMware são descobertas a cada 15 minutos. Pode levar 15 minutos ou mais para que as VMs apareçam no portal do Azure após a descoberta. Da mesma forma, a descoberta pode levar 15 minutos ou mais quando você adiciona um novo servidor vCenter ou host vSphere.
* Pode levar 15 minutos ou mais para que as alterações de ambiente na máquina virtual (como instalação de ferramentas do VMware) sejam atualizadas no Portal.
* Você pode verificar a hora da última descoberta para VMs VMware: consulte o campo **último contato** em na página **servidores de configuração** para o servidor vCenter/host vSphere.
* Para adicionar máquinas virtuais para replicação sem esperar pela descoberta agendada, realce o servidor de configuração (mas não clique nele) e selecione **Atualizar**.
* Quando você habilita a replicação, se a máquina virtual estiver preparada, o servidor de processo instalará automaticamente o serviço de mobilidade Azure Site Recovery nele.

## <a name="enable-replication"></a>Ativar a replicação

Antes de seguir as etapas nesta seção, observe as seguintes informações:
* Agora Azure Site Recovery Replica diretamente para o Managed disks para todas as novas replicações. O servidor de processo grava os logs de replicação em uma conta de armazenamento de cache na região de destino. Esses logs são usados para criar pontos de recuperação em discos gerenciados de réplica que têm Convenção de nomenclatura de asrseeddisk.
* O suporte do PowerShell para replicar para Managed disks está disponível no [módulo AZ. recoveryservices versão 2.0.0 em diante](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) 
* No momento do failover, o ponto de recuperação selecionado é usado para criar o disco gerenciado por destino.
* As VMs que foram configuradas anteriormente para replicar para as contas de armazenamento de destino não são afetadas.
* A replicação para contas de armazenamento para uma nova máquina virtual só está disponível por meio de uma API REST (Representational State Transfer) e do PowerShell. Use a API REST do Azure versão 2016-08-10 ou 2018-01-10 para replicar para contas de armazenamento.

Siga as etapas abaixo para habilitar a replicação:
1. Vá para a **etapa 2: replicar o aplicativo** > **origem**. Depois de habilitar a replicação pela primeira vez, selecione **+ replicar** no cofre para habilitar a replicação para máquinas virtuais adicionais.
2. Na página **origem** > **origem**, selecione o servidor de configuração.
3. Para **tipo de computador**, selecione **máquinas virtuais** ou **máquinas físicas**.
4. Em **vCenter/vSphere Hypervisor**, selecione o servidor vCenter que gere o anfitrião vSphere ou selecione o anfitrião. Essa configuração não será relevante se você estiver replicando computadores físicos.
5. Selecione o servidor de processo. Se não houver servidores de processo adicionais criados, o servidor de processo embutido do servidor de configuração estará disponível na lista suspensa. O status de integridade de cada servidor de processo é indicado de acordo com os limites recomendados e outros parâmetros. Escolha um servidor de processo íntegro. Um servidor de processo [crítico](vmware-physical-azure-monitor-process-server.md#process-server-alerts) não pode ser escolhido. Você pode [solucionar problemas e resolver](vmware-physical-azure-troubleshoot-process-server.md) os erros **ou** configurar um [servidor de processo de expansão](vmware-azure-set-up-process-server-scale.md).
    ![habilitar a janela de origem de replicação](media/vmware-azure-enable-replication/ps-selection.png)

> [!NOTE]
> De [versões 9,24](service-updates-how-to.md#links-to-currently-supported-update-rollups), alertas adicionais são introduzidos para aprimorar os alertas de integridade do servidor de processo. Atualize Site Recovery componentes para as versões 9,24 ou superiores para que todos os alertas sejam gerados.

6. Para **destino**, selecione a assinatura e o grupo de recursos em que você deseja criar as máquinas virtuais com failover. Escolha o modelo de implantação que você deseja usar no Azure para as VMs com failover.
2. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure serão conectadas após o failover. A rede deve estar na mesma região que o cofre do serviço de Site Recovery.

   Selecione **Configurar agora para computadores selecionados** para aplicar a configuração de rede a todas as máquinas virtuais selecionadas para proteção. Selecione **configurar mais tarde** para selecionar a rede do Azure por máquina virtual. Se você não tiver uma rede, precisará criar uma. Para criar uma rede usando Azure Resource Manager, selecione **criar nova**. Selecione uma sub-rede, se aplicável, e selecione **OK**.
   
   ![Janela habilitar destino de replicação](./media/vmware-azure-enable-replication/enable-rep3.png)

1. Para **máquinas virtuais** > **selecionar máquinas virtuais**, selecione cada máquina virtual que você deseja replicar. Você só pode selecionar máquinas virtuais para as quais a replicação pode ser habilitada. Em seguida, selecione **OK**. Se você não puder ver ou selecionar nenhuma máquina virtual específica, consulte [o computador de origem não está listado no portal do Azure](https://aka.ms/doc-plugin-VM-not-showing) para resolver o problema.

    ![Janela habilitar replicação selecionar máquinas virtuais](./media/vmware-azure-enable-replication/enable-replication5.png)

1. Para **propriedades** > **Configurar Propriedades**, selecione a conta que o servidor de processo usa para instalar automaticamente o serviço de mobilidade de site Recovery na máquina virtual. Além disso, escolha o tipo de disco gerenciado de destino para replicar com base em seus padrões de variação de dados.
10. Por padrão, todos os discos de uma máquina virtual de origem são replicados. Para excluir discos da replicação, desmarque a caixa de seleção **incluir** para todos os discos que você não deseja replicar. Em seguida, selecione **OK**. Pode definir as propriedades adicionais mais tarde. Saiba mais sobre como [excluir discos](vmware-azure-exclude-disk.md).

    ![Habilitar a janela de propriedades de configuração de replicação](./media/vmware-azure-enable-replication/enable-replication6.png)

1. Em **configurações de replicação** > **definir configurações de replicação**, verifique se a política de replicação correta está selecionada. Você pode modificar as configurações da política de replicação em **configurações** > **políticas de replicação** > ***nome da política*** > **Editar configurações**. As alterações que você aplica a uma política também se aplicam à replicação e novas máquinas virtuais.
1. Habilite a **consistência de várias VMs** se desejar reunir máquinas virtuais em um grupo de replicação. Especifique um nome para o grupo e, em seguida, selecione **OK**.

    > [!NOTE]
    >    * As máquinas virtuais em um grupo de replicação são replicadas em conjunto e têm pontos de recuperação consistentes com o aplicativo e com falhas compartilhadas quando executam failover.
    >    * Reúna VMs e servidores físicos juntos para que eles espelhem suas cargas de trabalho. Habilitar a consistência de várias VMs pode afetar o desempenho da carga de trabalho. Faça isso somente se as máquinas virtuais estiverem executando a mesma carga de trabalho e você precisar de consistência.

    ![Janela habilitar replicação](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. Selecione **Ativar Replicação**. Você pode acompanhar o progresso do trabalho **habilitar proteção** em **configurações** > **trabalhos** > **trabalhos de site Recovery**. Depois que o trabalho **finalizar proteção** for executado, a máquina virtual estará pronta para failover.

## <a name="view-and-manage-vm-properties"></a>Ver e gerir propriedades da VM

Em seguida, verifique as propriedades da máquina virtual de origem. Lembre-se de que o nome da VM do Azure precisa estar em conformidade com [os requisitos de máquina virtual do Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Vá para **configurações** > **itens replicados**e, em seguida, selecione a máquina virtual. A página **Essentials** mostra informações sobre as configurações e o status da VM.
1. Em **Propriedades**, pode ver as informações de replicação e de ativação pós-falha da VM.
1. Em **computação e rede** > **Propriedades de computação**, você pode alterar várias propriedades da VM. 

    ![Janela Propriedades de computação e rede](./media/vmware-azure-enable-replication/vmproperties.png)

    * Nome da VM do Azure: modifique o nome para atender aos requisitos do Azure, se necessário.
    * Tamanho da VM de destino ou tipo de VM: o tamanho padrão da VM é escolhido com base em alguns parâmetros que incluem contagem de disco, contagem de NIC, contagem de núcleo de CPU, memória e tamanhos de função de VM disponíveis na região do Azure de destino. Azure Site Recovery escolhe o primeiro tamanho de VM disponível que satisfaz todos os critérios. Você pode selecionar um tamanho de VM diferente com base em suas necessidades a qualquer momento antes do failover. Observe que o tamanho do disco da VM também é baseado no tamanho do disco de origem e só pode ser alterado após o failover. Saiba mais sobre tamanhos de disco e taxas de IOPS em [escalabilidade e metas de desempenho para discos de VM no Windows](../virtual-machines/windows/disk-scalability-targets.md).

    *  Grupo de recursos: você pode selecionar um [grupo de recursos](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines), do qual uma máquina virtual se torna parte de um failover posterior. Você pode alterar essa configuração a qualquer momento antes do failover. Após o failover, se você migrar a máquina virtual para um grupo de recursos diferente, as configurações de proteção dessa quebra de máquina virtual.
    * Conjunto de disponibilidade: você pode selecionar um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) se sua máquina virtual precisar fazer parte de um failover posterior. Ao selecionar um conjunto de disponibilidade, tenha em mente as seguintes informações:

        * Somente os conjuntos de disponibilidade que pertencem ao grupo de recursos especificado são listados.  
        * As VMs que estão em redes virtuais diferentes não podem fazer parte do mesmo conjunto de disponibilidade.
        * Somente as máquinas virtuais do mesmo tamanho podem fazer parte de um conjunto de disponibilidade.
1. Você também pode adicionar informações sobre a rede de destino, a sub-rede e o endereço IP atribuído à VM do Azure.
2. Em **discos**, você pode ver o sistema operacional e os discos de dados na VM que serão replicados.

### <a name="configure-networks-and-ip-addresses"></a>Configurar redes e endereços IP

Pode definir o endereço IP de destino. Se você não fornecer um endereço, a máquina virtual com failover usará DHCP. Se você definir um endereço que não está disponível no failover, o failover não funcionará. Se o endereço estiver disponível na rede de failover de teste, você poderá usar o mesmo endereço IP de destino para o failover de teste.

O número de adaptadores de rede é determinado pelo tamanho especificado para a máquina virtual de destino, da seguinte maneira:

- Se o número de adaptadores de rede na máquina virtual de origem for menor ou igual ao número de adaptadores permitidos para o tamanho da VM de destino, o destino terá o mesmo número de adaptadores que a origem.
- Se o número de adaptadores para a máquina virtual de origem exceder o número permitido para o tamanho da VM de destino, o tamanho máximo de destino será usado. Por exemplo, se uma máquina virtual de origem tiver dois adaptadores de rede e o tamanho da VM de destino der suporte a quatro, a máquina virtual de destino terá dois adaptadores. Se a VM de origem tiver dois adaptadores, mas o tamanho de destino oferecer suporte apenas a um, a VM de destino terá apenas um adaptador.
- Se a máquina virtual tiver vários adaptadores de rede, todos eles se conectarão à mesma rede. Além disso, o primeiro adaptador mostrado na lista se torna o adaptador de rede *padrão* na máquina virtual do Azure. 

### <a name="azure-hybrid-benefit"></a>Benefício Híbrido do Azure

Os clientes do Microsoft Software Assurance podem usar Benefício Híbrido do Azure para economizar em custos de licenciamento para computadores Windows Server que são migrados para o Azure. O benefício também se aplica à recuperação de desastre do Azure. Se você estiver qualificado, poderá atribuir o benefício à máquina virtual que Site Recovery cria se houver um failover. Para tal, siga estes passos:
1. Vá para as **Propriedades de computador e rede** da máquina virtual replicada.
2. Responder quando for perguntado se você tem uma licença do Windows Server que o torna elegível para Benefício Híbrido do Azure.
3. Confirme se você tem uma licença do Windows Server qualificada com o Software Assurance que pode ser usada para aplicar o benefício à VM que será criada no failover.
4. Salve as configurações para a máquina virtual replicada.

Saiba mais sobre [benefício híbrido do Azure](https://aka.ms/azure-hybrid-benefit-pricing).



## <a name="next-steps"></a>Passos seguintes

Depois que a máquina virtual atingir um estado protegido, tente um [failover](site-recovery-failover.md) para verificar se seu aplicativo aparece no Azure.

* Saiba como [limpar as configurações de proteção e de registro](site-recovery-manage-registration-and-protection.md) para desabilitar a replicação.
* Saiba como [automatizar a replicação para suas máquinas virtuais usando o PowerShell](vmware-azure-disaster-recovery-powershell.md).
