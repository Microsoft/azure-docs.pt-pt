---
title: Preparar o Azure para recuperação após desastre de VMs de Hyper-V no local com o Azure Site Recovery | Documentos da Microsoft
description: Saiba como preparar o Azure para recuperação após desastre de VMs de Hyper-V no local com o Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 48101e49429225018381ed2a3b1e8e4e351c15a6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59362528"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Preparar os recursos do Azure para a recuperação após desastre de máquinas no local

 O [Azure Site Recovery](site-recovery-overview.md) contribui para a sua estratégia de continuidade comercial e recuperação após desastre (BCDR) ao manter as suas aplicações empresariais em funcionamento durante falhas planeadas e não planeadas. O Site Recovery gere e orquestra a recuperação após desastre de computadores no local e máquinas virtuais (VMs) do Azure, incluindo replicação, ativação pós-falha e recuperação.

Este artigo é o primeiro tutorial de uma série que lhe mostra como configurar a recuperação após desastre para VMs no local. Ele é relevante quando estiver a proteger VMs de Hyper-V.

> [!NOTE]
> Os tutoriais são concebidos para mostrar o caminho de implementação mais simples num cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, veja a secção **Procedimentos** para o cenário correspondente.

Este artigo mostra como preparar os componentes do Azure quando pretende replicar VMs no local (Hyper-V) para o Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Certifique-se de que a conta do Azure tem permissões de replicação.
> * Crie uma conta do Storage do Azure. As imagens das máquinas replicadas são guardadas nela.
> * Crie um cofre dos Serviços de Recuperação. Um cofre contém metadados e informações de configuração para as VMs e outros componentes de replicação.
> * Configure uma rede do Azure. Quando as VMs do Azure são criadas após a ativação pós-falha, são associadas a esta rede do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](http://portal.azure.com).

## <a name="verify-account-permissions"></a>Verificar permissões de conta

Se acabou de criar a conta gratuita do Azure, é o administrador da sua subscrição. Se não for o administrador da subscrição, combine com o administrador para lhe atribuir as permissões de que precisa. Para ativar a replicação para uma nova máquina virtual, tem de ter permissões para:

- Criar uma VM no grupo de recursos selecionado.
- Criar uma VM na rede virtual selecionada.
- Escrever na conta de armazenamento selecionada.

Para concluir estas tarefas, a conta deve estar atribuída à função incorporada de Contribuidor de Máquina Virtual. Além disso, para gerir as operações do Site Recovery num cofre, a conta deve estar atribuída à função incorporada de Contribuidor do Site Recovery.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

As imagens das máquinas replicadas são guardadas no armazenamento do Azure. As VMs do Azure são criadas a partir do armazenamento quando fizer a ativação pós-falha do local para o Azure. A conta de armazenamento tem de estar na mesma região que o cofre dos Serviços de Recuperação. Estamos a utilizar Europa Ocidental neste tutorial.

1. No menu [Portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Armazenamento** > **Conta de armazenamento – blob, ficheiro, tabela, fila**.
2. Em **Criar conta de armazenamento**, introduza um nome para a conta. Nestes tutoriais, estamos a utilizar **contosovmsacct1910171607**. O nome que selecionar tem de ser exclusivo no Azure e ter entre 3 e 24 carateres compostos apenas por números e letras minúsculas.
3. Em **Modelo de implementação**, selecione **Resource Manager**.
4. Em **Tipo de conta**, selecione **Armazenamento (fins gerais v1)**. Não selecione o armazenamento de blobs.
5. Em **Replicação**, selecione o **Armazenamento georredundante com acesso de leitura** predefinido para redundância de armazenamento. Estamos a deixar **Transferência segura necessária** como **Desativada**.
6. Em **Desempenho**, selecione **Standard** e em **Camada de acesso** escolha a opção predefinida de **Frequente**.
7. Em **Subscrição**, selecione a subscrição na qual pretende criar a nova conta de armazenamento.
8. Em **Grupo de recursos**, introduza um novo grupo de recursos. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Nestes tutoriais, estamos a utilizar **ContosoRG**.
9. Em **Localização**, selecione a localização geográfica para a conta de armazenamento. 

   ![Criar uma conta de armazenamento](media/tutorial-prepare-azure/create-storageacct.png)

9. Selecione **Criar** para criar a conta de armazenamento.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

1. No portal do Azure, clique em **+ criar um recurso**e pesquisar no Marketplace **dos serviços de recuperação**.
2. Clique em **cópia de segurança e Site Recovery (OMS)** e na página de cópia de segurança e recuperação de Site, clique em **criar**. 
1. Na **cofre dos serviços de recuperação** > **nome**, introduza um nome amigável para identificar o cofre. Neste conjunto de tutoriais, estamos a utilizar **ContosoVMVault**.
2. Na **grupo de recursos**, selecione um grupo de recursos existente ou crie um novo. Para este tutorial vamos utilizar **contosoRG**.
3. Na **localização**, selecione a região em que o cofre deve estar localizado. estamos a utilizar **Europa Ocidental**.
4. Para aceder rapidamente ao cofre a partir do dashboard, selecione **Afixar ao dashboard** > **Criar**.

   ![Criar um novo cofre](./media/tutorial-prepare-azure/new-vault-settings.png)

   O novo cofre é apresentado em **Dashboard** > **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.

## <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Quando as VMs do Azure são criadas a partir do armazenamento após a ativação pós-falha, são associadas a esta rede.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Redes** > **Rede virtual**.
2. Deixamos o **Resource Manager** selecionado como modelo de implementação.
3. Em **Nome**, introduza um nome de rede. O nome tem de ser exclusivo dentro do grupo de recursos do Azure. Estamos a utilizar **ContosoASRnet** neste tutorial.
4. Especifique o grupo de recursos no qual será criada a rede. Estamos a utilizar o grupo de recursos **contosoRG** existente.
5. Em **Intervalo de endereços**, introduza o intervalo para a rede **10.0.0.0/24**. Nesta rede, não estamos a utilizar nenhuma sub-rede.
6. Em **Subscrição**, selecione a subscrição na qual vai criar a rede.
7. Em **Localização**, selecione **Europa Ocidental**. A rede tem de estar na mesma região que o cofre de Serviços de Recuperação.
8. Estamos a deixar as opções predefinidas da proteção DDoS básica, sem nenhum ponto final de serviço na rede.
9. Clique em **Criar**.

   ![Criar uma rede virtual](media/tutorial-prepare-azure/create-network.png)

   A rede virtual demora alguns segundos a ser criada. Depois de estar criada, pode vê-la no dashboard do portal do Azure.

## <a name="useful-links"></a>Ligações úteis

- [Saiba mais](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sobre as redes Azure.
- [Saiba mais sobre](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) discos geridos.



## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Preparar a infraestrutura de Hyper-V no local para a recuperação após desastre para o Azure](hyper-v-prepare-on-premises-tutorial.md)
