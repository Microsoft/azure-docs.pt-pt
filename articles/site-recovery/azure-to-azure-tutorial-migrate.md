---
title: Mover VMs IaaS do Azure para outra região do Azure usando o serviço de Azure Site Recovery | Microsoft Docs
description: Use Azure Site Recovery para mover VMs IaaS do Azure de uma região do Azure para outra.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 3790b543a1a6dcbb793dbf661441700e6fa24232
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69611888"
---
# <a name="move-azure-vms-to-another-region"></a>Mover VMs do Azure para outra região

Há vários cenários em que você deseja mover suas VMs (máquinas virtuais) IaaS do Azure existentes de uma região para outra. Por exemplo, você deseja melhorar a confiabilidade e a disponibilidade de suas VMs existentes, para melhorar a capacidade de gerenciamento ou para se mover por motivos de governança. Para obter mais informações, consulte [visão geral da movimentação de VM do Azure](azure-to-azure-move-overview.md). 

Você pode usar o serviço de [Azure site Recovery](site-recovery-overview.md) para gerenciar e orquestrar a recuperação de desastres de máquinas locais e VMs do Azure para continuidade dos negócios e recuperação de desastres (BCDR). Você também pode usar Site Recovery para gerenciar a movimentação de VMs do Azure para uma região secundária.

Neste tutorial, irá:

> [!div class="checklist"]
> 
> * Verificar pré-requisitos para a movimentação
> * Preparar as VMs de origem e a região de destino
> * Copiar os dados e habilitar a replicação
> * Testar a configuração e executar a movimentação
> * Excluir os recursos na região de origem
> 
> [!NOTE]
> Este tutorial mostra como mover as VMs do Azure de uma região para outra como está. Se você precisar melhorar a disponibilidade movendo as VMs em um conjunto de disponibilidade para VMs fixadas de zona em uma região diferente, consulte o [tutorial mover VMs do Azure para zonas de disponibilidade](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Pré-requisitos

- Verifique se as VMs do Azure estão na região do Azure da qual você deseja mover.
- Verifique se [há suporte para a combinação](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)de regiões de região de origem-destino e tome uma decisão informada sobre a região de destino.
- Certifique-se de que compreende a [arquitetura e os componentes do cenário](azure-to-azure-architecture.md).
- Veja os [requisitos e limitações de suporte](azure-to-azure-support-matrix.md).
- Verifique as permissões da conta. Se você criou sua conta gratuita do Azure, você é o administrador da sua assinatura. Se você não for o administrador da assinatura, trabalhe com o administrador para atribuir as permissões necessárias. Para habilitar a replicação para uma VM e, essencialmente, copiar dados usando Azure Site Recovery, você deve ter:

    - Permissões para criar uma VM nos recursos do Azure. A função interna colaborador da máquina virtual tem essas permissões, que incluem:
    - Permissão para criar uma VM no grupo de recursos selecionado
    - Permissão para criar uma VM na rede virtual selecionada
    - Permissão para escrever na conta de armazenamento selecionada
    
    - Permissões para gerenciar operações de Azure Site Recovery. A função colaborador de Site Recovery tem todas as permissões necessárias para gerenciar Site Recovery operações em um cofre dos serviços de recuperação.

- Verifique se todos os certificados raiz mais recentes estão nas VMs do Azure que você deseja mover. Se os certificados raiz mais recentes não estiverem na VM, as restrições de segurança impedirão a cópia de dados para a região de destino.

- Para VMs do Windows, instale todas as atualizações mais recentes do Windows na VM, para que todos os certificados de raiz fidedigna fiquem na máquina. Num ambiente desligado, siga o padrão do Windows Update e os processos de atualização de certificados da sua organização.
    
- Para VMs do Linux, siga as orientações fornecidas pelo seu distribuidor do Linux para obter os certificados raiz confiáveis mais recentes e a lista de certificados revogados na VM.
- Certifique-se de que você não está usando um proxy de autenticação para controlar a conectividade de rede para as VMs que deseja mover.

- Se a VM que você está tentando mover não tiver acesso à Internet ou se estiver usando um proxy de firewall para controlar o acesso de saída, [Verifique os requisitos](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).

- Identifique o layout de rede de origem e todos os recursos que você está usando no momento. Isso inclui, mas não se limita a balanceadores de carga, NSGs (grupos de segurança de rede) e IPs públicos.

- Verifique se sua assinatura do Azure permite que você crie VMs na região de destino que é usada para recuperação de desastre. Contacte o suporte para ativar a quota necessária.

- Certifique-se de que sua assinatura tenha recursos suficientes para dar suporte a VMs com tamanhos que correspondam às suas VMs de origem. Se você estiver usando Site Recovery para copiar dados para o destino, Site Recovery escolherá o mesmo tamanho ou o tamanho mais próximo possível para a VM de destino.

- Certifique-se de criar um recurso de destino para cada componente identificado no layout de rede de origem. Essa etapa é importante para garantir que suas VMs tenham toda a funcionalidade e os recursos na região de destino que você tinha na região de origem.

     > [!NOTE] 
     > Azure Site Recovery descobre e cria automaticamente uma rede virtual quando você habilita a replicação para a VM de origem. Você também pode criar previamente uma rede e atribuí-la à VM no fluxo do usuário para habilitar a replicação. Conforme mencionado posteriormente, você precisa criar manualmente quaisquer outros recursos na região de destino.

    Para criar os recursos de rede usados com mais frequência que são relevantes para você com base na configuração da VM de origem, consulte a seguinte documentação:
    - [Grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Balanceadores de carga](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    -  [IP público](../virtual-network/virtual-network-public-ip-address.md)
    - Para qualquer outro componente de rede, consulte a [documentação de rede](https://docs.microsoft.com/azure/#pivot=products&panel=network).



## <a name="prepare"></a>Preparar
As etapas a seguir mostram como preparar a máquina virtual para a movimentação usando Azure Site Recovery como uma solução. 

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Criar o cofre em qualquer região, exceto a região de origem

1. Inicie sessão no [portal do Azure](https://portal.azure.com) > **Serviços de Recuperação**.
1. Selecione **criar um recurso** > **ferramentas** > de gerenciamento de**backup e site Recovery**.
1. No **Nome**, especifique o nome amigável **ContosoVMVault**. Se tiver mais do que uma subscrição, selecione a que for adequada.
1. Crie o grupo de recursos **ContosoRG**.
1. Selecione uma região do Azure. Para verificar as regiões com suporte, consulte disponibilidade geográfica em [Azure site Recovery detalhes de preços](https://azure.microsoft.com/pricing/details/site-recovery/).
1. Em **cofres dos serviços de recuperação**, selecione **visão geral** > **ContosoVMVault** >  **+ replicar**.
1. Em **Origem**, selecione **Azure**.
1. Em **Localização de origem**, selecione a região do Azure de origem onde as VMs estão atualmente a ser executadas.
1. Selecione o modelo de implementação do Resource Manager. Em seguida, selecione a **assinatura de origem** e o grupo de **recursos de origem**.
1. Selecione **OK** para salvar as configurações.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Habilitar a replicação para VMs do Azure e começar a copiar os dados

Site Recovery recupera uma lista das VMs que estão associadas à assinatura e ao grupo de recursos.

1. Na próxima etapa, selecione a VM que você deseja mover e, em seguida, selecione **OK**.
1. Em **configurações**, selecione **recuperação de desastre**.
1. Em **Configurar** > **região de destino**de recuperação de desastre, selecione a região de destino para a qual você irá replicar.
1. Para este tutorial, aceite as outras predefinições.
1. Selecione **habilitar replicação**. Esta etapa inicia um trabalho para habilitar a replicação para a VM.

    ![Ativar replicação](media/tutorial-migrate-azure-to-azure/settings.png)

## <a name="move"></a>Mover

As etapas a seguir mostram como executar a movimentação para a região de destino.

1. Vá para o cofre. Em **configurações** > **itens replicados**, selecione a VM e, em seguida, selecione **failover**.
2. Em **Ativação pós-falha**, selecione **Mais recente**.
3. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. O Site Recovery tenta encerrar a VM de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode seguir o progresso da ativação pós-falha na página **Trabalhos**.
4. Depois que o trabalho for concluído, verifique se a VM aparece na região de destino do Azure conforme o esperado.


## <a name="discard"></a>Dispensar 

Caso você tenha verificado a VM movida e precise fazer alterações no ponto de failover ou deseja voltar a um ponto anterior, nos **itens replicados**, selecione a VM > **alterar ponto de recuperação**. Esta etapa fornece a opção de especificar um ponto de recuperação diferente e o failover para aquele. 


## <a name="commit"></a>Consolidar 

Depois de verificar a VM movida e estar pronto para confirmar a alteração, nos **itens replicados**, selecione a VM > **confirmar**. Essa etapa conclui o processo de movimentação para a região de destino. Aguarde até que o trabalho de confirmação seja concluído.

## <a name="clean-up"></a>Limpeza

As etapas a seguir irão orientá-lo sobre como limpar a região de origem, bem como os recursos relacionados que foram usados para a movimentação.

Para todos os recursos que foram usados para a movimentação:

- Vá para a VM. Selecione **desabilitar replicação**. Essa etapa interrompe o processo de copiar os dados para a VM.

   > [!IMPORTANT]
   > É importante executar essa etapa para evitar ser cobrado pela replicação de Azure Site Recovery.

Se você não tiver planos para reutilizar nenhum dos recursos de origem, conclua estas etapas adicionais:

1. Exclua todos os recursos de rede relevantes na região de origem que você identificou em [pré-requisitos](#prerequisites).
1. Exclua a conta de armazenamento correspondente na região de origem.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você moveu uma VM do Azure para uma região diferente do Azure. Agora você pode configurar a recuperação de desastre para a VM que você moveu.

> [!div class="nextstepaction"]
> [Configurar a recuperação após desastre após a migração](azure-to-azure-quickstart.md)

