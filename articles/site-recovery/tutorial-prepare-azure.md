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
ms.openlocfilehash: 87216317a965e85d83aede468163f43b1716c45a
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827248"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Preparar os recursos do Azure para a recuperação após desastre de máquinas no local

Este artigo descreve como preparar os recursos e componentes do Azure para que você possa configurar a recuperação de desastre de VMs VMware locais, VMs Hyper-V ou servidores físicos Windows/Linux para o Azure, usando o serviço [Azure site Recovery](site-recovery-overview.md) .

Este artigo é o primeiro tutorial de uma série que lhe mostra como configurar a recuperação após desastre para VMs no local. 


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Verifique se a conta do Azure tem permissões de replicação.
> * Crie um cofre dos Serviços de Recuperação. Um cofre contém metadados e informações de configuração para as VMs e outros componentes de replicação.
> * Configure uma VNet (rede virtual) do Azure. Quando as VMs do Azure são criadas após o failover, elas são unidas a essa rede.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, examine o artigo na seção como do Sumário de Site Recovery.

## <a name="before-you-start"></a>Antes de começar

- Examine a arquitetura de recuperação de desastre do [VMware](vmware-azure-architecture.md), [Hyper-V](hyper-v-azure-architecture.md)e [servidor físico](physical-azure-architecture.md) .
- Leia perguntas comuns sobre o [VMware](vmware-azure-common-questions.md) e o [Hyper-V](hyper-v-azure-common-questions.md)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar. Em seguida, entre no [portal do Azure](https://portal.azure.com).


## <a name="verify-account-permissions"></a>Verificar permissões de conta

Se você acabou de criar sua conta gratuita do Azure, você é o administrador da sua assinatura e tem as permissões necessárias. Se não for o administrador da subscrição, combine com o administrador para lhe atribuir as permissões de que precisa. Para ativar a replicação para uma nova máquina virtual, tem de ter permissões para:

- Criar uma VM no grupo de recursos selecionado.
- Criar uma VM na rede virtual selecionada.
- Gravar em uma conta de armazenamento do Azure.
- Gravar em um disco gerenciado do Azure.

Para concluir estas tarefas, a conta deve estar atribuída à função incorporada de Contribuidor de Máquina Virtual. Além disso, para gerir as operações do Site Recovery num cofre, a conta deve estar atribuída à função incorporada de Contribuidor do Site Recovery.


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

1. Na portal do Azure, clique em **+ criar um recurso**e pesquise o Marketplace para **recuperação**.
2. Clique em **backup e site Recovery**e, na página Backup e site Recovery, clique em **criar**. 
1. Em**nome**do >  **cofre dos serviços de recuperação**, insira um nome amigável para identificar o cofre. Neste conjunto de tutoriais, estamos a utilizar **ContosoVMVault**.
2. Em **grupo de recursos**, selecione um grupo de recursos existente ou crie um novo. Para este tutorial, estamos usando o **contosoRG**.
3. Em **local**, selecione a região em que o cofre deve ser localizado. estamos a utilizar **Europa Ocidental**.
4. Para aceder rapidamente ao cofre a partir do dashboard, selecione **Afixar ao dashboard** > **Criar**.

   ![Criar um novo cofre](./media/tutorial-prepare-azure/new-vault-settings.png)

   O novo cofre é apresentado em **Dashboard** > **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.

## <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Os computadores locais são replicados para o Azure Managed disks. Quando ocorre o failover, as VMs do Azure são criadas a partir desses discos gerenciados e Unidas à rede do Azure que você especificar neste procedimento.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Redes** > **Rede virtual**.
2. Mantenha o **Resource Manager** selecionado como o modelo de implantação.
3. Em **Nome**, introduza um nome de rede. O nome tem de ser exclusivo dentro do grupo de recursos do Azure. Estamos a utilizar **ContosoASRnet** neste tutorial.
4. Especifique o grupo de recursos no qual será criada a rede. Estamos a utilizar o grupo de recursos **contosoRG** existente.
5. Em **intervalo de endereços**, insira o intervalo para a rede. Estamos usando **10.1.0.0/24**, e não usando uma sub-rede.
6. Em **Subscrição**, selecione a subscrição na qual vai criar a rede.
7. Em **local**, selecione a mesma região em que o cofre dos serviços de recuperação foi criado. Em nosso tutorial, é **Europa Ocidental**. A rede deve estar na mesma região que o cofre.
8. Estamos a deixar as opções predefinidas da proteção DDoS básica, sem nenhum ponto final de serviço na rede.
9. Clique em **Criar**.

   ![Criar uma rede virtual](media/tutorial-prepare-azure/create-network.png)

A rede virtual demora alguns segundos a ser criada. Depois de estar criada, pode vê-la no dashboard do portal do Azure.




## <a name="next-steps"></a>Passos Seguintes

- Para a recuperação de desastres do VMware, [Prepare a infraestrutura do VMware local](tutorial-prepare-on-premises-vmware.md).
- Para a recuperação de desastres do Hyper-V, [prepare os servidores Hyper-v locais](hyper-v-prepare-on-premises-tutorial.md).
- Para a recuperação de desastres do servidor físico, [Configure o servidor de configuração e o ambiente de origem](physical-azure-disaster-recovery.md)
- [Saiba mais](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sobre as redes Azure.
- [Saiba mais sobre](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) o Managed disks.
