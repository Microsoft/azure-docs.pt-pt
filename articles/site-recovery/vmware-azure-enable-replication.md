---
title: Ativar VMs vMware para recuperação de desastres usando a recuperação do site Azure
description: Este artigo descreve como permitir a replicação vMware VM para recuperação de desastres usando o serviço de recuperação de sites Azure
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 06/28/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 10b3e572ec61d1eff342f24a6a5a7bcba6276983
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363053"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Ativar a replicação ao Azure para VMware VMs

Este artigo descreve como permitir a replicação de VMware VMs no local para Azure.

## <a name="resolve-common-issues"></a>Resolver questões comuns

* Cada disco deve ser inferior a 4 TB.
* O disco osso deve ser um disco básico, não um disco dinâmico.
* Para máquinas virtuais ativadas pela geração 2/UEFI, a família do sistema operativo deve ser o Windows, e o disco de arranque deve ser inferior a 300 GB.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem:

- [Instale o seu ambiente de origem no local.](vmware-azure-set-up-source.md)
- [Instale o seu ambiente alvo em Azure.](vmware-azure-set-up-target.md)
- [Verifique os requisitos e pré-requisitos](vmware-physical-azure-support-matrix.md) antes de começar. Coisas importantes a notar incluem:
    - [Sistemas operativos suportados](vmware-physical-azure-support-matrix.md#replicated-machines) para máquinas replicadas.
    - [Suporte de armazenamento/disco.](vmware-physical-azure-support-matrix.md#storage)
    - [Requisitos do Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements) com os quais as máquinas no local devem cumprir.


## <a name="before-you-start"></a>Antes de começar
Quando estiver a replicar máquinas virtuais VMware, tenha em mente esta informação:

* A sua conta de utilizador Azure precisa de ter [determinadas permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para permitir a replicação de uma nova máquina virtual para o Azure.
* VMware VMs são descobertos a cada 15 minutos. Pode levar 15 minutos ou mais para os VMs aparecerem no portal Azure após a descoberta. Da mesma forma, a descoberta pode demorar 15 minutos ou mais quando adiciona um novo servidor vCenter ou hospedeiro vSphere.
* Pode levar 15 minutos ou mais para que as alterações ambientais na máquina virtual (como a instalação de ferramentas VMware) sejam atualizadas no portal.
* Pode verificar a última hora descoberta para VMware VMs: Consulte o **último contacto no** campo na página de Servidores de **Configuração** para o servidor/vSphere do vCenter.
* Para adicionar máquinas virtuais para replicação sem esperar pela descoberta programada, realce o servidor de configuração (mas não clique nele) e selecione **Refresh**.
* Quando ativa a replicação, se a máquina virtual estiver preparada, o servidor de processos instala automaticamente o serviço de Mobilidade de Recuperação do Sítio Azure.

## <a name="enable-replication"></a>Ativar a replicação

Antes de seguir os passos nesta secção, tenha em atenção as seguintes informações:
* A Recuperação do Site Azure replica-se agora diretamente para discos geridos para todas as novas replicações. O servidor de processos escreve registos de replicação numa conta de armazenamento de cache na região alvo. Estes registos são usados para criar pontos de recuperação em discos geridos por réplicas que têm convenção de nomeação de asrseeddisk.
* O suporte powershell para replicar discos geridos está disponível a partir da [versão 2.0.0 do módulo Az.RecoveryServices](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) 
* No momento da falha, o ponto de recuperação que selecionar é utilizado para criar o disco gerido pelo alvo.
* Os VMs que foram previamente configurados para se replicarem para contas de armazenamento alvo não são afetados.
* A replicação às contas de armazenamento de uma nova máquina virtual só está disponível através de uma API e Powershell de Transferência estatal representativa (REST). Utilize a versão API Azure REST 2016-08-10 ou 2018-01-10 para replicar contas de armazenamento.

Por favor, siga os passos abaixo para ativar a replicação:
1. Ir para o **Passo 2: Replicar a aplicação** > **Fonte**. Depois de ativar a replicação pela primeira vez, selecione **+Replicate** no cofre para permitir a replicação de máquinas virtuais adicionais.
2. Na página **Fonte** > **Source,** selecione o servidor de configuração.
3. Para **o tipo de máquina,** selecione **Máquinas Virtuais** ou **Máquinas Físicas**.
4. Em **vCenter/vSphere Hypervisor**, selecione o servidor vCenter que gere o anfitrião vSphere ou selecione o anfitrião. Esta definição não é relevante se estivera a replicar computadores físicos.
5. Selecione o servidor de processos. Se não houver servidores de processo adicionais criados, o servidor de processo incorporado do servidor de configuração estará disponível no dropdown. O estado de saúde de cada servidor de processo é indicado de acordo com os limites recomendados e outros parâmetros. Escolha um servidor de processo saudável. Um servidor de processo [crítico](vmware-physical-azure-monitor-process-server.md#process-server-alerts) não pode ser escolhido. Pode [resolver problemas e resolver](vmware-physical-azure-troubleshoot-process-server.md) os erros **ou** configurar um servidor de [processo sem escalas](vmware-azure-set-up-process-server-scale.md).
    ![Ativar a janela da fonte de replicação](media/vmware-azure-enable-replication/ps-selection.png)

> [!NOTE]
> A partir de [9.24 versões,](service-updates-how-to.md#links-to-currently-supported-update-rollups)são introduzidos alertas adicionais para melhorar os alertas de saúde do servidor de processos. Upgrade De formação de componentes de recuperação do site para 9.24 versões ou acima para todos os alertas a serem gerados.

6. Paratarget , selecione o grupo de subscrição e recursos onde pretende criar as máquinas virtuais falhadas. Escolha o modelo de implementação que pretende utilizar em Azure para os VMs falhados.
2. Selecione a rede Azure e a sub-rede a que os VMs Azure se ligarão após a falha. A rede deve estar na mesma região que o cofre de serviço de recuperação do local.

   **Selecione configurar agora para máquinas selecionadas** para aplicar a definição de rede em todas as máquinas virtuais que seleciona para proteção. **Selecione Configurar mais tarde** para selecionar a rede Azure por máquina virtual. Se não tem uma rede, tem de criar uma. Para criar uma rede utilizando o Gestor de Recursos Azure, selecione **Criar nova**. Selecione uma sub-rede se aplicável e, em seguida, selecione **OK**.
   
   ![Ativar a janela-alvo de replicação](./media/vmware-azure-enable-replication/enable-rep3.png)

1. Para **máquinas virtuais** > **Selecione máquinas virtuais,** selecione cada máquina virtual que pretende replicar. Só é possível selecionar máquinas virtuais para as quais a replicação pode ser ativada. Em seguida, selecione **OK**. Se não conseguir ver ou selecionar qualquer máquina virtual em particular, consulte que [a máquina Source não está listada no portal Azure](https://aka.ms/doc-plugin-VM-not-showing) para resolver o problema.

    ![Ativar a replicação Selecione a janela das máquinas virtuais](./media/vmware-azure-enable-replication/enable-replication5.png)

1. Para **propriedades** > **Configurar propriedades,** selecione a conta que o servidor de processo utiliza para instalar automaticamente o serviço de Mobilidade de Recuperação do Site na máquina virtual. Além disso, escolha o tipo de disco gerido pelo alvo para replicar com base nos padrões de recolha de dados.
10. Por padrão, todos os discos de uma máquina virtual de origem são replicados. Para excluir discos de replicação, limpe a caixa de verificação **Incluir** quaisquer discos que não queira replicar. Em seguida, selecione **OK**. Pode definir as propriedades adicionais mais tarde. Saiba mais sobre [a exclusão dos discos](vmware-azure-exclude-disk.md).

    ![Ativar a janela de configuração de propriedades de replicação](./media/vmware-azure-enable-replication/enable-replication6.png)

1. Nas definições de **replicação** > **configurar as definições de replicação,** verifique se a política de replicação correta é selecionada. Pode modificar as definições de política de replicação em **Definições** > políticas de **replicação** > nome de ***política*** > **Definições de Edição**. As alterações que se aplicam a uma política aplicam-se também à replicação e às novas máquinas virtuais.
1. Ative a **consistência multi-VM** se quiser reunir máquinas virtuais num grupo de replicação. Especifique um nome para o grupo e, em seguida, selecione **OK**.

    > [!NOTE]
    >    * As máquinas virtuais num grupo de replicação replicam-se em conjunto e têm pontos de recuperação consistentes e consistentes com aplicações quando falham.
    >    * Junte VMs e servidores físicos para que espelhem as suas cargas de trabalho. Permitir a consistência multi-VM pode afetar o desempenho da carga de trabalho. Faça isto apenas se as máquinas virtuais estiverem a funcionar com a mesma carga de trabalho, e você precisa de consistência.

    ![Ativar a janela de replicação](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. Selecione **Ativar Replicação**. Pode acompanhar o progresso do trabalho de **Proteção ativa** em **Definições** > **Empregos** > Empregos de **Recuperação**do Local . Após o trabalho de **Proteção final,** a máquina virtual está pronta para a falha.

## <a name="view-and-manage-vm-properties"></a>Ver e gerir propriedades da VM

Em seguida, verifique as propriedades da máquina virtual de origem. Lembre-se que o nome Azure VM precisa de estar em conformidade com [os requisitos da máquina virtual Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Vá a **Definições** > **itens replicados**e, em seguida, selecione a máquina virtual. A página **Essentials** mostra informações sobre as definições e o estado do VM.
1. Em **Propriedades**, pode ver as informações de replicação e de ativação pós-falha da VM.
1. Nas propriedades **computacionais e** de > **Compute,** pode alterar várias propriedades VM. 

    ![Janela de propriedades computacionais e de rede](./media/vmware-azure-enable-replication/vmproperties.png)

    * Nome Azure VM: Modifique o nome para satisfazer os requisitos do Azure, se necessário.
    * Tamanho vm alvo ou tipo VM: O tamanho padrão de VM é escolhido com base em alguns parâmetros que incluem contagem de discos, contagem de NIC, contagem de núcleos cpu, memória e tamanhos de função VM disponíveis na região de Target Azure. A Recuperação do Site Azure escolhe o primeiro tamanho VM disponível que satisfaz todos os critérios. Pode selecionar um tamanho VM diferente com base nas suas necessidades a qualquer momento antes de falhar. Note que o tamanho do disco VM também é baseado no tamanho do disco de origem, e só pode ser alterado após a falha. Saiba mais sobre tamanhos de disco e taxas de IOPS na [Escalabilidade e metas de desempenho para discos VM no Windows](../virtual-machines/windows/disk-scalability-targets.md).

    *  Grupo de recursos: Pode selecionar um grupo de [recursos](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines), a partir do qual uma máquina virtual se torna parte de uma falha de correio. Pode alterar esta definição a qualquer momento antes de falhar. Após a falha, se migrar a máquina virtual para um grupo de recursos diferente, as definições de proteção para essa rutura virtual da máquina.
    * Conjunto de disponibilidade: Pode selecionar um conjunto de [disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) se a sua máquina virtual precisar de fazer parte de uma falha de publicação. Quando selecionar um conjunto de disponibilidade, tenha em mente as seguintes informações:

        * Apenas estão listados os conjuntos de disponibilidade que pertencem ao grupo de recursos especificado.  
        * VMs que estão em diferentes redes virtuais não podem fazer parte do mesmo conjunto de disponibilidade.
        * Apenas máquinas virtuais do mesmo tamanho podem fazer parte de um conjunto de disponibilidade.
1. Também pode adicionar informações sobre a rede de destino, sub-rede e endereço IP que é atribuído ao VM Azure.
2. Nos **Discos,** pode ver o sistema operativo e os discos de dados no VM que serão replicados.

### <a name="configure-networks-and-ip-addresses"></a>Configure redes e endereços IP

Pode definir o endereço IP de destino. Se não fornecer um endereço, a máquina virtual falhada utiliza DHCP. Se definir um endereço que não está disponível no failover, o failover não funciona. Se o endereço estiver disponível na rede de failover do teste, pode utilizar o mesmo endereço IP do alvo para a falha do teste.

O número de adaptadores de rede é ditado pelo tamanho que especifica para a máquina virtual alvo, da seguinte forma:

- Se o número de adaptadores de rede na máquina virtual de origem for inferior ou igual ao número de adaptadores que são permitidos para o tamanho do VM alvo, o alvo tem o mesmo número de adaptadores que a fonte.
- Se o número de adaptadores para a máquina virtual de origem exceder o número que é permitido para o tamanho do VM alvo, o tamanho máximo do alvo é utilizado. Por exemplo, se uma máquina virtual de origem tiver dois adaptadores de rede e o tamanho do VM alvo suporta quatro, a máquina virtual alvo tem dois adaptadores. Se a Fonte VM tiver dois adaptadores mas o tamanho do alvo apenas suporta um, o VM alvo tem apenas um adaptador.
- Se a máquina virtual tiver vários adaptadores de rede, todos eles se ligam à mesma rede. Além disso, o primeiro adaptador que é mostrado na lista torna-se o adaptador de rede *padrão* na máquina virtual Azure. 

### <a name="azure-hybrid-benefit"></a>Benefício Híbrido do Azure

Os clientes da Microsoft Software Assurance podem utilizar o Azure Hybrid Benefit para economizar nos custos de licenciamento dos computadores Windows Server que estão migrados para o Azure. O benefício aplica-se igualmente à recuperação de desastres de Azure. Se for elegível, pode atribuir o benefício à máquina virtual que a Recuperação do Site cria se houver uma falha. Para tal, siga estes passos:
1. Vá às propriedades do **Computador e da Rede** da máquina virtual replicada.
2. Responda quando lhe perguntarem se tem uma licença do Windows Server que o torna elegível para o Azure Hybrid Benefit.
3. Confirme que tem uma licença de Servidor Windows elegível com Garantia de Software que pode usar para aplicar o benefício ao VM que será criado no failover.
4. Guarde as definições para a máquina virtual replicada.

Saiba mais sobre [o Azure Hybrid Benefit.](https://aka.ms/azure-hybrid-benefit-pricing)



## <a name="next-steps"></a>Passos Seguintes

Depois de a máquina virtual chegar a um estado protegido, tente uma [falha](site-recovery-failover.md) para verificar se a sua aplicação aparece no Azure.

* Aprenda a limpar as definições de [registo e proteção](site-recovery-manage-registration-and-protection.md) para desativar a replicação.
* Aprenda a automatizar a [replicação para as suas máquinas virtuais utilizando powershell](vmware-azure-disaster-recovery-powershell.md).
