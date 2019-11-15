---
title: Preparar o Azure para a recuperação de desastres do Hyper-V com o Azure Site Recovery
description: Saiba como preparar o Azure para a recuperação de desastre de VMs do Hyper-V locais usando Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: ef623b95e104b485c6bfc8b2f489afeca436d81e
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084180"
---
# <a name="prepare-azure-resources-for-hyper-v-disaster-recovery"></a>Preparar recursos do Azure para recuperação de desastres do Hyper-V

 [Azure site Recovery](site-recovery-overview.md) ajuda a BCDR (continuidade dos negócios e recuperação de desastre) mantendo os aplicativos de negócios em execução durante interrupções planejadas e não planejadas. O Site Recovery gere e orquestra a recuperação após desastre de computadores no local e máquinas virtuais (VMs) do Azure, incluindo replicação, ativação pós-falha e recuperação.

Este tutorial é o primeiro de uma série que descreve como configurar a recuperação de desastre para VMs do Hyper-V locais.

> [!NOTE]
> Criamos tutoriais para mostrar o caminho de implantação mais simples para um cenário. Esses tutoriais usam as opções padrão quando possível e não mostram todas as configurações e caminhos possíveis. Para obter mais informações, consulte a seção "como" para cada cenário correspondente.

Este tutorial mostra como preparar os componentes do Azure quando você deseja replicar VMs locais (Hyper-V) para o Azure. Vai aprender a:

> [!div class="checklist"]
> * Certifique-se de que a conta do Azure tem permissões de replicação.
> * Crie uma conta de armazenamento do Azure, que armazena imagens de máquinas replicadas.
> * Crie um cofre dos serviços de recuperação, que armazena metadados e informações de configuração para VMs e outros componentes de replicação.
> * Configure uma rede do Azure. Quando as VMs do Azure são criadas após o failover, elas são unidas a essa rede.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="sign-in"></a>Iniciar sessão

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Verificar permissões de conta

Se você acabou de criar uma conta gratuita do Azure, você é o administrador dessa assinatura. Se você não for o administrador, trabalhe com o administrador para atribuir as permissões necessárias. Para ativar a replicação para uma nova máquina virtual, tem de ter permissões para:

- Criar uma VM no grupo de recursos selecionado.
- Criar uma VM na rede virtual selecionada.
- Escrever na conta de armazenamento selecionada.

Para concluir essas tarefas, sua conta deve ser atribuída à função interna colaborador de máquina virtual. Para gerenciar as operações de Site Recovery em um cofre, sua conta deve ser atribuída à função interna colaborador de Site Recovery.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

As imagens das máquinas replicadas são guardadas no armazenamento do Azure. As VMs do Azure são criadas a partir do armazenamento quando fizer a ativação pós-falha do local para o Azure. A conta de armazenamento tem de estar na mesma região que o cofre dos Serviços de Recuperação.

1. No menu [portal do Azure](https://portal.azure.com) , selecione **criar um recurso** > **armazenamento** > **conta de armazenamento-BLOB, arquivo, tabela, fila**.
2. Em **Criar conta de armazenamento**, introduza um nome para a conta.  O nome escolhido deve ser exclusivo no Azure, ter de 3 a 24 caracteres e usar apenas letras minúsculas e números. Para este tutorial, use **contosovmsacct1910171607**.
3. Em **Modelo de implementação**, selecione **Resource Manager**.
4. Em **tipo de conta**, selecione **armazenamento (uso geral v1)** . Não selecione o armazenamento de blobs.
5. Em **Replicação**, selecione o **Armazenamento georredundante com acesso de leitura** predefinido para redundância de armazenamento. Deixe a configuração de transferência segura necessária como desabilitada.
6. Em **Desempenho**, selecione **Padrão**. Em seguida, na **camada de acesso**, selecione a opção padrão de **quente**.
7. Em **assinatura**, escolha a assinatura na qual você deseja criar a nova conta de armazenamento.
8. Em **Grupo de recursos**, introduza um novo grupo de recursos. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Para este tutorial, use **ContosoRG**.
9. Em **local**, escolha a localização geográfica da sua conta de armazenamento. Para este tutorial, use **Europa Ocidental**.
10. Selecione **Criar** para criar a conta de armazenamento.

   ![Criar uma conta de armazenamento](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos serviços de recuperação

1. Na portal do Azure, selecione **+ criar um recurso**e pesquise os serviços de recuperação no Azure Marketplace.
2. Selecione **backup e site Recovery (OMS)** . Em seguida, na página **backup e site Recovery** , selecione **criar**.
1. Em **cofre dos serviços de recuperação > nome**, insira um nome amigável para identificar o cofre. Neste tutorial, utilize **ContosoVMVault**.
2. Em **grupo de recursos**, selecione um grupo de recursos existente ou crie um novo. Para este tutorial, use **contosoRG**.
3. Em **local**, selecione a região onde o cofre deve ser localizado. Para este tutorial, use **Europa Ocidental**.
4. Para aceder rapidamente ao cofre a partir do dashboard, selecione **Afixar ao dashboard** > **Criar**.

![Criar um novo cofre](./media/tutorial-prepare-azure/new-vault-settings.png)

O novo cofre é apresentado em **Dashboard** > **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.

## <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Quando as VMs do Azure são criadas a partir do armazenamento após a ativação pós-falha, são associadas a esta rede.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Redes** > **Rede virtual**. Deixe o Gerenciador de recursos selecionado como o modelo de implantação.
2. Em **Nome**, introduza um nome de rede. O nome tem de ser exclusivo dentro do grupo de recursos do Azure. Para este tutorial, use **ContosoASRnet**.
3. Especifique o grupo de recursos no qual criar a rede. Para este tutorial, use o grupo de recursos existente **contosoRG**.
4. Em **intervalo de endereços**, insira **10.0.0.0/24** como o intervalo para a rede. Não há nenhuma sub-rede para esta rede.
5. Em **Subscrição**, selecione a subscrição na qual vai criar a rede.
6. Em **local**, escolha **Europa Ocidental**. A rede tem de estar na mesma região que o cofre de Serviços de Recuperação.
7. Deixe as opções padrão de proteção contra DDoS básica, sem nenhum ponto de extremidade de serviço na rede.
8. Selecione **Criar**.

![Criar uma rede virtual](media/tutorial-prepare-azure/create-network.png)

A rede virtual demora alguns segundos a ser criada. Depois de criado, você o verá no painel de portal do Azure.

## <a name="useful-links"></a>Ligações úteis

Saiba mais sobre:
- [Redes do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Discos geridos](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Preparar a infraestrutura local do Hyper-V para recuperação de desastres no Azure](hyper-v-prepare-on-premises-tutorial.md)
