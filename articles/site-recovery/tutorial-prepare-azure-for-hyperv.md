---
title: Preparar os recursos do Azure para a recuperação após desastre de máquinas no local
description: Saiba como preparar o Azure para recuperação após desastre de VMs de Hyper-V no local com o Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 07a5ee6ccdaecc78c9a8e61ae9e64a5264e3a875
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418349"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Preparar os recursos do Azure para a recuperação após desastre de máquinas no local

 [O Azure Site Recovery](site-recovery-overview.md) ajuda empresas continuidade e recuperação após desastre (BCDR) ao manter as aplicações empresariais em execução durante falhas planeadas e não planeadas. O Site Recovery gere e orquestra a recuperação após desastre de computadores no local e máquinas virtuais (VMs) do Azure, incluindo replicação, ativação pós-falha e recuperação.

Este tutorial é a primeira de uma série que descreve como configurar a recuperação após desastre para VMs de Hyper-V no local.

> [!NOTE]
> Podemos tutoriais que indicassem o caminho de implantação mais simples para um cenário de design. Estes tutoriais utilizam as opções predefinidas, sempre que possível e não mostram todas as configurações possíveis e caminhos. Para obter mais informações, consulte a secção "Como para" para cada cenário correspondente.

Este tutorial mostra como preparar os componentes do Azure quando pretende replicar VMs no local (Hyper-V) para o Azure. Vai aprender a:

> [!div class="checklist"]
> * Certifique-se de que a conta do Azure tem permissões de replicação.
> * Crie uma conta de armazenamento do Azure, que armazena as imagens das máquinas replicadas.
> * Crie um cofre dos serviços de recuperação, que armazena informações de metadados e configuração para VMs e outros componentes de replicação.
> * Configure uma rede do Azure. Quando as VMs do Azure são criadas após a ativação pós-falha, são associadas a esta rede.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="sign-in"></a>Iniciar sessão

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Verificar permissões de conta

Se acabou de criar uma conta gratuita do Azure, é o administrador para essa subscrição. Se não for o administrador, trabalhe com o administrador para atribuir as permissões que necessárias. Para ativar a replicação para uma nova máquina virtual, tem de ter permissões para:

- Criar uma VM no grupo de recursos selecionado.
- Criar uma VM na rede virtual selecionada.
- Escrever na conta de armazenamento selecionada.

Para concluir estas tarefas, a conta deve ser atribuída a função incorporada de Contribuidor de Máquina Virtual. Para gerir as operações do Site Recovery num cofre, a conta deve ser atribuída a função incorporada contribuinte do Site Recovery.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

As imagens das máquinas replicadas são guardadas no armazenamento do Azure. As VMs do Azure são criadas a partir do armazenamento quando fizer a ativação pós-falha do local para o Azure. A conta de armazenamento tem de estar na mesma região que o cofre dos Serviços de Recuperação.

1. Na [portal do Azure](https://portal.azure.com) menu, selecione **criar um recurso** > **armazenamento** > **conta de armazenamento - blob, ficheiro, tabela, fila** .
2. Em **Criar conta de armazenamento**, introduza um nome para a conta.  O nome que escolher tem de ser exclusivo no Azure, ser a partir de 3 a 24 carateres de comprimento e apenas, utilize letras minúsculas e números. Neste tutorial, utilize **contosovmsacct1910171607**.
3. Em **Modelo de implementação**, selecione **Resource Manager**.
4. Na **tipo de conta**, selecione **(para fins gerais v1) de armazenamento**. Não selecione o armazenamento de blobs.
5. Em **Replicação**, selecione o **Armazenamento georredundante com acesso de leitura** predefinido para redundância de armazenamento. Deixe a transferência segura necessária definir como desativado.
6. Em **Desempenho**, selecione **Padrão**. Em seguida, no **camada de acesso**, selecione a opção predefinida **frequente**.
7. Na **subscrição**, escolha a subscrição na qual pretende criar a nova conta de armazenamento.
8. Em **Grupo de recursos**, introduza um novo grupo de recursos. Um grupo de recursos do Azure é um contentor lógico no qual os recursos são implementados e geridos. Neste tutorial, utilize **ContosoRG**.
9. Na **localização**, escolha a localização geográfica para a sua conta de armazenamento. Neste tutorial, utilize **Europa Ocidental**.
10. Selecione **Criar** para criar a conta de armazenamento.

   ![Criar uma conta de armazenamento](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos serviços de recuperação

1. No portal do Azure, selecione **+ criar um recurso**e, em seguida, procure no Azure Marketplace para serviços de recuperação.
2. Selecione **Backup e Site Recovery (OMS)** . Em seguida, na **Backup e Site Recovery** página, selecione **criar**.
1. Na **cofre dos Recovery services > nome**, introduza um nome amigável para identificar o cofre. Neste tutorial, utilize **ContosoVMVault**.
2. Na **grupo de recursos**, selecione um grupo de recursos existente ou crie um novo. Neste tutorial, utilize **contosoRG**.
3. Na **localização**, selecione a região em que o cofre deve estar localizado. Neste tutorial, utilize **Europa Ocidental**.
4. Para aceder rapidamente ao cofre a partir do dashboard, selecione **Afixar ao dashboard** > **Criar**.

![Criar um novo cofre](./media/tutorial-prepare-azure/new-vault-settings.png)

O novo cofre é apresentado em **Dashboard** > **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.

## <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Quando as VMs do Azure são criadas a partir do armazenamento após a ativação pós-falha, são associadas a esta rede.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Redes** > **Rede virtual**. Deixe selecionado como o modelo de implementação do Resource Manager.
2. Em **Nome**, introduza um nome de rede. O nome tem de ser exclusivo dentro do grupo de recursos do Azure. Neste tutorial, utilize **ContosoASRnet**.
3. Especifique o grupo de recursos no qual pretende criar a rede. Neste tutorial, utilize o grupo de recursos existentes **contosoRG**.
4. Na **intervalo de endereços**, introduza **10.0.0.0/24** como o intervalo para a rede. Não existe nenhuma sub-rede para esta rede.
5. Em **Subscrição**, selecione a subscrição na qual vai criar a rede.
6. Na **localização**, escolha **Europa Ocidental**. A rede tem de estar na mesma região que o cofre de Serviços de Recuperação.
7. Deixe as opções predefinidas de proteção de DDoS básica, com nenhum ponto de final de serviço na rede.
8. Selecione **Criar**.

![Criar uma rede virtual](media/tutorial-prepare-azure/create-network.png)

A rede virtual demora alguns segundos a ser criada. Depois de estar criada, irá vê-la no dashboard do portal do Azure.

## <a name="useful-links"></a>Ligações úteis

Saiba mais sobre:
- [Redes do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Discos geridos](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)



## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Preparar a infraestrutura de Hyper-V no local para a recuperação após desastre para o Azure](hyper-v-prepare-on-premises-tutorial.md)
