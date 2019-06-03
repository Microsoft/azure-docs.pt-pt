---
title: Preparar o Azure para recuperação após desastre de máquinas no local com o Azure Site Recovery | Microsoft Docs
description: Saiba como preparar o Azure para a recuperação após desastre de máquinas no local com o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: cb2ca7229524cf8d84041140129c7b9ca6876ea3
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417805"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Preparar os recursos do Azure para a recuperação após desastre de máquinas no local

Este artigo descreve como preparar os recursos do Azure e componentes para que pode configurar a recuperação após desastre de VMs de VMware, VMs de Hyper-V ou Windows/Linux físicos servidores locais para o Azure, utilizando o [do Azure Site Recovery](site-recovery-overview.md) serviço.

Este artigo é o primeiro tutorial de uma série que lhe mostra como configurar a recuperação após desastre para VMs no local. 


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Certifique-se de que a conta do Azure tem permissões de replicação.
> * Crie um cofre dos Serviços de Recuperação. Um cofre contém metadados e informações de configuração para as VMs e outros componentes de replicação.
> * Configure uma rede virtual do Azure (VNet). Quando as VMs do Azure são criadas após a ativação pós-falha, são associadas a esta rede.

> [!NOTE]
> Tutoriais mostram-lhe o caminho de implantação mais simples para um cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, consulte o artigo na seção como a do Site Recovery sumário.

## <a name="before-you-start"></a>Antes de começar

- Analise a arquitetura do [VMware](vmware-azure-architecture.md), [Hyper-V](hyper-v-azure-architecture.md), e [servidor físico](physical-azure-architecture.md) recuperação após desastre.
- Leia as perguntas mais comuns para [VMware](vmware-azure-common-questions.md) e [Hyper-V](hyper-v-azure-common-questions.md)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar. Em seguida, inicie sessão para o [portal do Azure](https://portal.azure.com).


## <a name="verify-account-permissions"></a>Verificar permissões de conta

Se acabou de criar sua conta gratuita do Azure, é o administrador da sua subscrição e tem as permissões que necessárias. Se não for o administrador da subscrição, combine com o administrador para lhe atribuir as permissões de que precisa. Para ativar a replicação para uma nova máquina virtual, tem de ter permissões para:

- Criar uma VM no grupo de recursos selecionado.
- Criar uma VM na rede virtual selecionada.
- Escreva numa conta de armazenamento do Azure.
- Escrever a Azure disco gerido.

Para concluir estas tarefas, a conta deve estar atribuída à função incorporada de Contribuidor de Máquina Virtual. Além disso, para gerir as operações do Site Recovery num cofre, a conta deve estar atribuída à função incorporada de Contribuidor do Site Recovery.


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

1. No portal do Azure, clique em **+ criar um recurso**e pesquisar no Marketplace **recuperação**.
2. Clique em **cópia de segurança e Site Recovery (OMS)** e na página de cópia de segurança e recuperação de Site, clique em **criar**. 
1. Na **cofre dos serviços de recuperação** > **nome**, introduza um nome amigável para identificar o cofre. Neste conjunto de tutoriais, estamos a utilizar **ContosoVMVault**.
2. Na **grupo de recursos**, selecione um grupo de recursos existente ou crie um novo. Para este tutorial vamos utilizar **contosoRG**.
3. Na **localização**, selecione a região em que o cofre deve estar localizado. estamos a utilizar **Europa Ocidental**.
4. Para aceder rapidamente ao cofre a partir do dashboard, selecione **Afixar ao dashboard** > **Criar**.

   ![Criar um novo cofre](./media/tutorial-prepare-azure/new-vault-settings.png)

   O novo cofre é apresentado em **Dashboard** > **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.

## <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

As máquinas são replicadas para o Azure no local discos geridos. Quando ocorre a ativação pós-falha, as VMs do Azure são criadas a partir destes discos geridos e associadas à rede do Azure que especificar neste procedimento.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Redes** > **Rede virtual**.
2. Manter **Resource Manager** selecionado como o modelo de implementação.
3. Em **Nome**, introduza um nome de rede. O nome tem de ser exclusivo dentro do grupo de recursos do Azure. Estamos a utilizar **ContosoASRnet** neste tutorial.
4. Especifique o grupo de recursos no qual será criada a rede. Estamos a utilizar o grupo de recursos **contosoRG** existente.
5. Na **intervalo de endereços**, introduza o intervalo para a rede. Estamos a utilizar **10.1.0.0/24**e não a utilizar uma sub-rede.
6. Em **Subscrição**, selecione a subscrição na qual vai criar a rede.
7. Na **localização**, selecione a mesma região que em que o Cofre dos serviços de recuperação foi criado. No nosso tutorial tem **Europa Ocidental**. A rede tem de estar na mesma região que o cofre.
8. Estamos a deixar as opções predefinidas da proteção DDoS básica, sem nenhum ponto final de serviço na rede.
9. Clique em **Criar**.

   ![Criar uma rede virtual](media/tutorial-prepare-azure/create-network.png)

A rede virtual demora alguns segundos a ser criada. Depois de estar criada, pode vê-la no dashboard do portal do Azure.




## <a name="next-steps"></a>Passos Seguintes

- Para a recuperação de desastre do VMware, [preparar a infraestrutura de VMware no local](tutorial-prepare-on-premises-vmware.md).
- Para recuperação após desastre de Hyper-V, [preparar no local, servidores de Hyper-V](hyper-v-prepare-on-premises-tutorial.md).
- Para a recuperação de desastres do servidor físico, [configurar o ambiente de origem e de servidor de configuração](physical-azure-disaster-recovery.md)
- [Saiba mais](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sobre as redes Azure.
- [Saiba mais sobre](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) discos geridos.
