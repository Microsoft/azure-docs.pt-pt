---
title: Prepare o Azure para a recuperação de desastre de computadores locais com Azure Site Recovery
description: Saiba como preparar o Azure para a recuperação após desastre de máquinas no local com o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 32aa2c8f4c97f247bfcff5fc82a3f810b8005591
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488533"
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

Para concluir estas tarefas, a conta deve estar atribuída à função incorporada de Contribuidor de Máquina Virtual. Além disso, para gerenciar as operações de Site Recovery em um cofre, sua conta deve ser atribuída à função interna colaborador de Site Recovery.


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

1. No menu portal do Azure, selecione **criar um recurso**e pesquise o Marketplace para **recuperação**.
2. Selecione **backup e site Recovery** nos resultados da pesquisa e, na página Backup e site Recovery, clique em **criar**. 
3. Na página **criar cofre dos serviços de recuperação** , selecione a **assinatura**. Estamos usando a **assinatura da Contoso**.
4. Em **grupo de recursos**, selecione um grupo de recursos existente ou crie um novo. Para este tutorial, estamos usando o **contosoRG**.
5. Em **nome do cofre**, insira um nome amigável para identificar o cofre. Neste conjunto de tutoriais, estamos a utilizar **ContosoVMVault**.
6. Em **região**, selecione a região na qual o cofre deve ser localizado. estamos a utilizar **Europa Ocidental**.
7. Selecione **Rever + criar**.

   ![Criar um novo cofre](./media/tutorial-prepare-azure/new-vault-settings.png)

   O novo cofre agora será listado no **painel** > **todos os recursos**e na página principais **cofres dos serviços de recuperação** .

## <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Os computadores locais são replicados para o Azure Managed disks. Quando ocorre o failover, as VMs do Azure são criadas a partir desses discos gerenciados e Unidas à rede do Azure que você especificar neste procedimento.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Redes** > **Rede virtual**.
2. Mantenha o **Resource Manager** selecionado como o modelo de implantação.
3. Em **Nome**, introduza um nome de rede. O nome tem de ser exclusivo dentro do grupo de recursos do Azure. Estamos a utilizar **ContosoASRnet** neste tutorial.
4. Em **espaço de endereço**, insira o intervalo de endereços da rede virtual na notação CDR. Estamos usando **10.1.0.0/24**.
5. Em **Subscrição**, selecione a subscrição na qual vai criar a rede.
6. Especifique o **grupo de recursos** no qual a rede será criada. Estamos a utilizar o grupo de recursos **contosoRG** existente.
7. Em **local**, selecione a mesma região em que o cofre dos serviços de recuperação foi criado. Em nosso tutorial, é **Europa Ocidental**. A rede deve estar na mesma região que o cofre.
8. Em **intervalo de endereços**, insira o intervalo para a rede. Estamos usando **10.1.0.0/24**, e não usando uma sub-rede.
9. Estamos deixando as opções padrão de proteção contra DDoS básica, sem nenhum ponto de extremidade de serviço ou firewall na rede.
9. Selecione **Criar**.

   ![Criar uma rede virtual](media/tutorial-prepare-azure/create-network.png)

A rede virtual demora alguns segundos a ser criada. Depois de criado, você o verá no painel de portal do Azure.




## <a name="next-steps"></a>Passos seguintes

- Para a recuperação de desastres do VMware, [Prepare a infraestrutura do VMware local](tutorial-prepare-on-premises-vmware.md).
- Para a recuperação de desastres do Hyper-V, [prepare os servidores Hyper-v locais](hyper-v-prepare-on-premises-tutorial.md).
- Para a recuperação de desastres do servidor físico, [Configure o servidor de configuração e o ambiente de origem](physical-azure-disaster-recovery.md)
- [Saiba mais](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sobre as redes Azure.
- [Saiba mais sobre](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) o Managed disks.
