---
title: Ativar VMware VMs para recuperação de desastres usando a recuperação do local de Azure
description: Este artigo descreve como permitir a replicação de VMware VM para recuperação de desastres usando o serviço de recuperação do local de Azure
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 12/07/2020
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: a1f4759bc40c4074f0dd618be8ac66ad088e848c
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587762"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Ativar a replicação no Azure para as VMs VMware

Este artigo descreve como permitir a replicação de máquinas virtuais VMware (VMware) no local para Azure.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que o seu sistema cumpre os seguintes critérios:

- [Instale o seu ambiente de origem no local.](vmware-azure-set-up-source.md)
- [Confiúdo do seu ambiente-alvo em Azure](vmware-azure-set-up-target.md).
- [Verifique os requisitos e pré-requisitos](vmware-physical-azure-support-matrix.md) antes de começar. Coisas importantes a notar incluem:
  - [Sistemas operativos suportados](vmware-physical-azure-support-matrix.md#replicated-machines) para máquinas replicadas.
  - [Suporte de armazenamento/disco.](vmware-physical-azure-support-matrix.md#storage)
  - [Requisitos de azul](vmware-physical-azure-support-matrix.md#azure-vm-requirements) com os quais as máquinas no local devem obedecer.

### <a name="resolve-common-issues"></a>Resolver questões comuns

- Cada disco deve ser menor que 4 TB.
- O disco do sistema operativo deve ser um disco básico, não um disco dinâmico.
- Para máquinas virtuais ativadas pela GERAÇÃO 2, a família do sistema operativo deve ser o Windows, e o disco de arranque deve ser inferior a 300 GB.

## <a name="before-you-start"></a>Antes de começar

Quando replicar máquinas virtuais VMware, tenha esta informação em mente:

- A sua conta de utilizador Azure precisa de ter [certas permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para permitir a replicação de uma nova máquina virtual para o Azure.
- Os VMware VMs são descobertos a cada 15 minutos. Pode levar 15 minutos ou mais para os VM aparecerem no portal Azure após a descoberta. Quando adiciona um novo servidor vCenter ou um hospedeiro vSphere, a descoberta pode demorar 15 minutos ou mais.
- Pode levar 15 minutos ou mais para que as alterações ambientais na máquina virtual sejam atualizadas no portal. Por exemplo, a instalação de ferramentas VMware.
- Pode verificar a última vez descoberta para VMware VMs: Ver o **último contacto no** campo na página **Servidores de Configuração** para o servidor/vSphere do vCenter.
- Para adicionar máquinas virtuais para replicação sem esperar pela descoberta programada, realce o servidor de configuração (mas não clique nele) e selecione **Refresh**.
- Quando ativa a replicação, se a máquina virtual estiver preparada, o servidor de processo instala automaticamente o serviço de Mobilidade de Recuperação do Local Azure no VM.

## <a name="enable-replication"></a>Ativar a replicação

Antes de fazer os passos nesta secção, reveja as seguintes informações:

- A Azure Site Recovery agora se replica diretamente para discos geridos para todas as novas replicações. O servidor de processo escreve registos de replicação numa conta de armazenamento de cache na região alvo. Estes registos são utilizados para criar pontos de recuperação em discos geridos por réplicas que têm convenção de nomeação de `asrseeddisk` .
- O suporte powerShell para replicação de discos geridos está disponível a partir da [versão 2.0.0 do módulo Az.RecoveryServices](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview)
- No momento do failover, o ponto de recuperação que seleciona é utilizado para criar o disco gerido pelo alvo.
- Os VMs que foram previamente configurados para replicar para contas de armazenamento alvo não são afetados.
- A replicação nas contas de armazenamento de uma nova máquina virtual só está disponível através de uma API e PowerShell de Transferência estatal Representativa (REST). Utilize a versão Azure REST API 2016-08-10 ou 2018-01-10 para replicação nas contas de armazenamento.

Para ativar a replicação, siga estes passos:

1. Ir para o **passo 2: Replicar aplicação**  >  **Fonte**. Depois de ativar a replicação pela primeira vez, selecione **+Replicar** no cofre para permitir a replicação de máquinas virtuais adicionais.
1. Na página **'Fonte'** > **Fonte,** selecione o servidor de configuração.
1. Para **o tipo de máquina**, selecione **Máquinas Virtuais** ou **Máquinas Físicas**.
1. Em **vCenter/vSphere Hypervisor**, selecione o servidor vCenter que gere o anfitrião vSphere ou selecione o anfitrião. Esta definição não é relevante se estiver a replicar computadores físicos.
1. Selecione o servidor de processo. Se não houver servidores de processos adicionais criados, o servidor de processo incorporado do servidor de configuração estará disponível no menu suspenso. O estado de saúde de cada servidor de processo é indicado de acordo com os limites recomendados e outros parâmetros. Escolha um servidor de processo saudável. Um servidor de processo [crítico](vmware-physical-azure-monitor-process-server.md#process-server-alerts) não pode ser escolhido. Pode [resolver problemas e resolver](vmware-physical-azure-troubleshoot-process-server.md) os erros **ou** configurar um servidor de processo [de escala](vmware-azure-set-up-process-server-scale.md).

   :::image type="content" source="./media/vmware-azure-enable-replication/ps-selection.png" alt-text="Ativar a janela de fonte de replicação":::

   > [!NOTE]
   > A partir da [versão 9.24,](site-recovery-whats-new.md)são introduzidos alertas adicionais para melhorar os alertas de saúde do servidor de processos. Atualize os componentes de Recuperação do Site para a versão 9.24 ou acima para que todos os alertas sejam gerados.

1. Para **Target**, selecione o grupo de subscrição e recursos onde pretende criar as máquinas virtuais falhadas. Escolha o modelo de implementação que pretende utilizar em Azure para os VMs falhados.
1. Selecione a rede Azure e a sub-rede a que os VMs Azure se ligarão após a falha. A rede deve estar na mesma região que o cofre de serviço de recuperação do local.

   Selecione **Configurar agora para máquinas selecionadas** para aplicar a definição de rede em todas as máquinas virtuais que seleciona para proteção. Selecione **Configurar mais tarde** para selecionar a rede Azure por máquina virtual. Se não tem uma rede, tem de criar uma. Para criar uma rede utilizando o Azure Resource Manager, selecione **Criar novo**. Selecione uma sub-rede se aplicável e, em seguida, selecione **OK**.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-rep3.png" alt-text="Ativar a janela alvo de replicação":::

1. Para **máquinas virtuais**  >  **Selecione máquinas virtuais,** selecione cada máquina virtual que pretende replicar. Só é possível selecionar máquinas virtuais para as quais a replicação pode ser ativada. Em seguida, selecione **OK**. Se não conseguir ver ou selecionar nenhuma máquina virtual em particular, consulte [a máquina Source não está listada no portal Azure](vmware-azure-troubleshoot-replication.md#step-3-troubleshoot-source-machines-that-arent-available-for-replication) para resolver o problema.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication5.png" alt-text="Ativar a replicação Selecione a janela das máquinas virtuais":::

1. Para **propriedades de configuração de**  >  **propriedades**, selecione a conta que o servidor de processo utiliza para instalar automaticamente o serviço de Mobilidade de Recuperação do Site no VM. Além disso, escolha o tipo de disco gerido pelo alvo para usar para replicação com base nos seus padrões de churn de dados.
1. Por predefinição, todos os discos de um VM de origem são replicados. Para excluir discos de replicação, **limpe** a caixa de verificação Incluir para quaisquer discos que não queira replicar. Em seguida, selecione **OK**. Pode definir as propriedades adicionais mais tarde. [Saiba mais](vmware-azure-exclude-disk.md) sobre a exclusão dos discos.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication6.png" alt-text="Ativar a janela de configuração de propriedades de replicação":::

1. A partir das **definições de replicação,**  >  **certifique-se de que** a política de replicação correta está selecionada. Pode modificar as definições de política de replicação nas  >  **definições de políticas de replicação**  >    >  **designar definições de edição**. As alterações aplicadas a uma política também se aplicam à replicação e às novas máquinas virtuais.
1. Se pretender juntar máquinas virtuais num grupo de replicação, ative a **consistência multi-VM**. Especifique um nome para o grupo e, em seguida, selecione **OK**.

   > [!NOTE]
   > - As máquinas virtuais num grupo de replicação replicam-se juntas e partilharam pontos de recuperação consistentes e consistentes com falhas quando falham.
   > - Junte VMs e servidores físicos para que espelham as suas cargas de trabalho. Permitir a consistência multi-VM pode afetar o desempenho da carga de trabalho. Faça isto apenas se as máquinas virtuais estiverem a funcionar da mesma carga de trabalho e precisar de consistência.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication7.png" alt-text="Ativar a janela de replicação":::

1. Selecione **Ativar a replicação**. Pode acompanhar o progresso do trabalho de **Proteção ativa** em **Definições**  >  **de**  >  **Empregos Locais de Recuperação .** Após o funcionaamento do trabalho **de Proteção Finalização,** a máquina virtual está pronta para o failover.

## <a name="monitor-initial-replication"></a>Monitorizar a replicação inicial

Depois de "Permitir a replicação" do item protegido estar concluído, a Azure Site Recovery inicia a replicação (sinónimo de sincronização) de dados da máquina de origem para a região alvo. Durante este período, a réplica dos discos de origem é criada. Só após a conclusão da cópia dos discos originais, as alterações delta são copiadas para a região alvo. O tempo necessário para copiar os discos originais depende de vários parâmetros tais como:

- tamanho dos discos de máquina de origem
- largura de banda disponível para transferir os dados para a Azure (Pode alavancar o planejador de implementação para identificar a largura de banda ideal necessária)
- processar recursos do servidor como memória, espaço de disco gratuito, CPU disponível para cache & processar os dados recebidos de itens protegidos (certifique-se de que o servidor de processo é [saudável)](vmware-physical-azure-monitor-process-server.md#monitor-proactively)

Para acompanhar o progresso da replicação inicial, navegue para o cofre dos serviços de recuperação no portal Azure -> itens replicados -> monitorizar o valor da coluna "Status" do item replicado. O estado mostra a percentagem de conclusão da replicação inicial. Ao pairar sobre o Estado, o "Total de dados transferidos" estaria disponível. Ao clicar no estado, uma página contextual abre e exibe os seguintes parâmetros:

- A última atualização indica o último momento em que a informação de replicação de toda a máquina foi atualizada pelo serviço.
- Percentagem completa - indica a percentagem de replicação inicial concluída para o VM
- Total de dados transferidos - Quantidade de dados transferidos de VM para Azure

:::image type="content" source="media/vmware-azure-enable-replication/initial-replication-state.png" alt-text="estado de replicação" lightbox="media/vmware-azure-enable-replication/initial-replication-state.png":::

- Progresso da sincronização (para acompanhar detalhes a nível de disco)
    - Estado de replicação
      - Se a replicação ainda não tiver começado, então o estado é atualizado como "Em fila". Durante a replicação inicial, apenas 3 discos são replicados de cada vez. Este mecanismo é seguido para evitar estrangulamentos no servidor de processo.
      - Após o início da replicação, o estado é atualizado como "Em curso".
      - Após a conclusão da replicação inicial, o estado é marcado como "Completo".        
   - A Recuperação do Site lê através do disco original, transfere dados para o Azure e captura o progresso a nível do disco. Note que, A Recuperação do Site ignora a replicação do tamanho desocupado do disco e adiciona-o aos dados preenchidos. Assim, a soma dos dados transferidos em todos os discos pode não somar o "total de dados transferidos" ao nível do VM.
   - Ao clicar no balão de informação contra um disco, pode obter detalhes sobre quando a replicação (sinónimo de sincronização) foi desencadeada para o disco, dados transferidos para Azure nos últimos 15 minutos seguidos do último carimbo de tempo renovado. Este carimbo de tempo indica a última hora em que a informação foi recebida pelo serviço Azure a partir da máquina de origem :::image type="content" source="media/vmware-azure-enable-replication/initial-replication-info-balloon.png" alt-text="vídeo-replicação-info-balão-detalhes" lightbox="media/vmware-azure-enable-replication/initial-replication-info-balloon.png":::
   - A saúde de cada disco é exibida
      - Se a replicação for mais lenta do que o esperado, então o estado do disco transforma-se em aviso
      - Se a replicação não está a progredir, então o estado do disco torna-se crítico

Se a saúde estiver em estado crítico/de alerta, certifique-se de que a Saúde de Replicação da máquina e [do Processo Sever](vmware-physical-azure-monitor-process-server.md) estão saudáveis. 

Assim que o trabalho de replicação estiver concluído, o progresso da replicação seria de 0% e os dados totais transferidos seriam NA. Ao clicar, os dados contra cada disco identificado seriam como "NA". Isto indica que a replicação ainda está para começar e a Recuperação do Site Azure ainda está para receber as últimas estatísticas. O progresso é refrescado num intervalo de 30 min.

> [!NOTE]
> Certifique-se de atualizar servidores de configuração, servidores de processos de escala e agentes de mobilidade para versões 9.36 ou superiores para garantir que o progresso preciso é capturado e enviado para os serviços de Recuperação de Locais.


## <a name="view-and-manage-vm-properties"></a>Ver e gerir propriedades da VM

Em seguida, verifique as propriedades da máquina virtual de origem. Lembre-se de que o nome Azure VM precisa de estar em conformidade com os [requisitos da máquina virtual Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Aceda a **Definições**  >  **De itens Replicados** e, em seguida, selecione a máquina virtual. A página **Essentials** mostra informações sobre as definições e o estado do VM.
1. Em **Propriedades,** pode ver a replicação e informações de failover para o VM.
1. Nas propriedades **compute e Network**  >  **Compute,** pode alterar várias propriedades VM.

   :::image type="content" source="./media/vmware-azure-enable-replication/vmproperties.png" alt-text="Janela de propriedades de computação e rede":::

   - **Nome Azure VM**: Modifique o nome para satisfazer os requisitos de Azure, se necessário.
   - **Tamanho VM alvo ou tipo VM**: O tamanho VM predefinido é escolhido com base em parâmetros que incluem contagem de discos, contagem de NIC, contagem de núcleos de CPU, memória e tamanhos de função VM disponíveis na região de Azure alvo. A Azure Site Recovery escolhe o primeiro tamanho VM disponível que satisfaz todos os critérios. Pode selecionar um tamanho VM diferente com base nas suas necessidades a qualquer momento antes de falhar. O tamanho do disco VM também é baseado no tamanho do disco de origem, e só pode ser alterado após a falha. Saiba mais sobre os tamanhos dos discos e as taxas de IOPS no [Scalability e alvos de desempenho para discos VM no Windows](../virtual-machines/windows/disk-scalability-targets.md).
   - **Grupo de recursos**: Pode selecionar um grupo de [recursos,](../azure-resource-manager/management/overview.md#resource-groups)a partir do qual uma máquina virtual se torna parte de um failover pós-failover. Pode alterar esta definição a qualquer momento antes de falhar. Depois de falhar, se migrar a máquina virtual para um grupo de recursos diferente, as definições de proteção para essa falha da máquina virtual quebram..
   - **Conjunto de disponibilidade**: Pode selecionar um [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) se a sua máquina virtual precisar de fazer parte de uma falha de publicação. Quando selecionar um conjunto de disponibilidade, tenha em mente as seguintes informações:
     - Apenas os conjuntos de disponibilidade pertencentes ao grupo de recursos especificados estão listados.
     - Os VMs que estão em diferentes redes virtuais não podem fazer parte do mesmo conjunto de disponibilidade.
     - Apenas máquinas virtuais do mesmo tamanho podem fazer parte de um conjunto de disponibilidade.

1. Também pode adicionar informações sobre a rede alvo, sub-rede e endereço IP que é atribuído ao Azure VM.
1. Em **Discos,** é possível ver o sistema operativo e os discos de dados no VM que serão replicados.

### <a name="configure-networks-and-ip-addresses"></a>Configure redes e endereços IP

Pode definir o endereço IP alvo:

- Se não fornecer uma morada, o falhado sobre vM utiliza DHCP.
- Se definir um endereço que não está disponível no failover, o failover não funciona.
- Se o endereço estiver disponível na rede de failover de teste, pode utilizar o mesmo endereço IP alvo para a falha do teste.

O número de adaptadores de rede é ditado pelo tamanho que especifica para a máquina virtual alvo, da seguinte forma:

- Se o número de adaptadores de rede na máquina virtual de origem for inferior ou igual ao número de adaptadores que são permitidos para o tamanho do VM alvo, o alvo tem o mesmo número de adaptadores que a fonte.
- Se o número de adaptadores para a máquina virtual de origem exceder o número permitido para o tamanho do VM alvo, o tamanho máximo do alvo é usado. Por exemplo, se uma máquina virtual de origem tiver dois adaptadores de rede e o tamanho do VM alvo suportar quatro, a máquina virtual alvo tem dois adaptadores. Se o VM de origem tiver dois adaptadores, mas o tamanho do alvo apenas suporta um, o VM alvo tem apenas um adaptador.
- Se a máquina virtual tiver vários adaptadores de rede, todos eles se ligam à mesma rede. Além disso, o primeiro adaptador que é mostrado na lista torna-se o adaptador de rede padrão na máquina virtual Azure.

### <a name="azure-hybrid-benefit"></a>Benefício Híbrido do Azure

Os clientes da Microsoft Software Assurance podem utilizar o Azure Hybrid Benefit para economizar nos custos de licenciamento para computadores Windows Server que são migrados para a Azure. O benefício aplica-se também à recuperação de desastres de Azure. Se for elegível, pode atribuir o benefício à máquina virtual que a Recuperação do Site cria se houver uma falha.

1. Aceda às **propriedades de Computador e Rede** da máquina virtual replicada.
1. Responda quando lhe perguntarem se tem uma licença do Windows Server que o torna elegível para benefício híbrido Azure.
1. Confirme que tem uma licença elegível do Windows Server com a Software Assurance que pode utilizar para aplicar o benefício ao VM que será criado no failover.
1. Guarde as definições para a máquina virtual replicada.

[Saiba mais](https://azure.microsoft.com/pricing/hybrid-benefit/) sobre o Azure Hybrid Benefit.

## <a name="next-steps"></a>Passos seguintes

Depois de a máquina virtual atingir um estado protegido, experimente um [failover](site-recovery-failover.md) para verificar se a sua aplicação aparece no Azure.

- [Saiba mais](site-recovery-manage-registration-and-protection.md) sobre como limpar as definições de registo e proteção para desativar a replicação.
- [Saiba mais](vmware-azure-disaster-recovery-powershell.md) sobre como automatizar a replicação para as suas máquinas virtuais utilizando o PowerShell.
