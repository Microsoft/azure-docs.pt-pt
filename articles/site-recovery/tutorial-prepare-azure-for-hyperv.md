---
title: Prepare Azure para recuperação de desastres hiper-V com recuperação do site Azure
description: Saiba como preparar o Azure para a recuperação de desastres de VMs hiper-V no local, utilizando a Recuperação do Site Azure
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: ef623b95e104b485c6bfc8b2f489afeca436d81e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74084180"
---
# <a name="prepare-azure-resources-for-hyper-v-disaster-recovery"></a>Prepare os recursos do Azure para a recuperação de desastres hiper-V

 [A Azure Site Recovery](site-recovery-overview.md) ajuda a continuidade do negócio e a recuperação de desastres (BCDR), mantendo as aplicações empresariais em funcionamento durante as interrupções planeadas e não planeadas. O Site Recovery gere e orquestra a recuperação após desastre de computadores no local e máquinas virtuais (VMs) do Azure, incluindo replicação, ativação pós-falha e recuperação.

Este tutorial é o primeiro de uma série que descreve como configurar a recuperação de desastres para os VMs hiper-V no local.

> [!NOTE]
> Desenhamos tutoriais para mostrar o caminho de implantação mais simples para um cenário. Estes tutoriais usam opções padrão quando possível, e não mostram todas as configurações e caminhos possíveis. Para mais informações, consulte a secção "Como Fazer" para cada cenário correspondente.

Este tutorial mostra-lhe como preparar componentes Azure quando pretende replicar vMs no local (Hyper-V) para Azure. Vai aprender a:

> [!div class="checklist"]
> * Certifique-se de que a conta do Azure tem permissões de replicação.
> * Crie uma conta de armazenamento Azure, que armazena imagens de máquinas replicadas.
> * Crie um cofre de Serviços de Recuperação, que armazena metadados e informações de configuração para VMs e outros componentes de replicação.
> * Configure uma rede do Azure. Quando os VMs Azure são criados após o fracasso, eles são unidos a esta rede.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="sign-in"></a>Iniciar sessão

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Verificar permissões de conta

Se acabou de criar uma conta Azure gratuita, é o administrador dessa subscrição. Se não for o administrador, trabalhe com o administrador para atribuir as permissões de que necessita. Para ativar a replicação para uma nova máquina virtual, tem de ter permissões para:

- Criar uma VM no grupo de recursos selecionado.
- Criar uma VM na rede virtual selecionada.
- Escrever na conta de armazenamento selecionada.

Para completar estas tarefas, a sua conta deve ser atribuída a função virtual de colaborador da máquina incorporada. Para gerir as operações de recuperação do site num cofre, a sua conta deve ser atribuída a função incorporada do Colaborador de Recuperação do Site.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

As imagens das máquinas replicadas são guardadas no armazenamento do Azure. As VMs do Azure são criadas a partir do armazenamento quando fizer a ativação pós-falha do local para o Azure. A conta de armazenamento tem de estar na mesma região que o cofre dos Serviços de Recuperação.

1. No menu [do portal Azure,](https://portal.azure.com) selecione **Criar uma** > conta de**armazenamento** > de recursos **- blob, file, table, fila**.
2. Em **Criar conta de armazenamento**, introduza um nome para a conta.  O nome que escolher deve ser único dentro do Azure, ter entre 3 e 24 caracteres de comprimento, e usar apenas letras e números minúsculos. Para este tutorial, use **contosovmsacct1910171607**.
3. Em **Modelo de implementação**, selecione **Resource Manager**.
4. Em **tipo conta**, selecione Armazenamento **(v1 de uso geral)**. Não selecione o armazenamento de blobs.
5. Em **Replicação**, selecione o **Armazenamento georredundante com acesso de leitura** predefinido para redundância de armazenamento. Deixe a transferência segura necessária como desativada.
6. Em **Desempenho**, selecione **Padrão**. Em seguida, no **nível de Acesso,** selecione a opção predefinida de **Hot**.
7. Em **Subscrição,** escolha a subscrição na qual pretende criar a nova conta de armazenamento.
8. Em **Grupo de recursos**, introduza um novo grupo de recursos. Um grupo de recursos Azure é um recipiente lógico no qual os recursos azure são implantados e geridos. Para este tutorial, utilize **ContosoRG.**
9. No **Local,** escolha a localização geográfica para a sua conta de armazenamento. Para este tutorial, use a **Europa Ocidental.**
10. Selecione **Criar** para criar a conta de armazenamento.

   ![Criar uma conta de armazenamento](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos serviços de recuperação

1. No portal Azure, selecione **+Criar um recurso**, e depois procurar serviços de recuperação no Azure Marketplace.
2. Selecione Backup e Recuperação do **Site (OMS)**. Em seguida, na página **de Backup e Recuperação** do Site, selecione **Criar**.
1. No **cofre de serviços de recuperação > Nome,** insira um nome amigável para identificar o cofre. Neste tutorial, utilize **ContosoVMVault**.
2. No **grupo Recursos,** selecione um grupo de recursos existente ou crie um novo. Para este tutorial, use **contosoRG.**
3. No **Local**, selecione a região onde o cofre deve ser localizado. Para este tutorial, use a **Europa Ocidental.**
4. Para aceder rapidamente ao cofre a partir do tablier, selecione **Pin para painel** > **criar**.

![Criar um novo cofre](./media/tutorial-prepare-azure/new-vault-settings.png)

O novo cofre aparece no **Dashboard** > **Todos os recursos,** e na página principal dos Serviços de **Recuperação.**

## <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Quando as VMs do Azure são criadas a partir do armazenamento após a ativação pós-falha, são associadas a esta rede.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Rede** > **Rede virtual**. Deixe o Resource Manager selecionado como o modelo de implementação.
2. Em **Nome**, introduza um nome de rede. O nome tem de ser exclusivo dentro do grupo de recursos do Azure. Para este tutorial, utilize **ContosoASRnet.**
3. Especifique o grupo de recursos para criar a rede. Para este tutorial, utilize o grupo de recursos existente **contosoRG**.
4. No **intervalo de endereços,** insira **10.0.0.0/24** como gama para a rede. Não há sub-rede para esta rede.
5. Em **Subscrição**, selecione a subscrição na qual vai criar a rede.
6. No **Local,** escolha europa **ocidental.** A rede tem de estar na mesma região que o cofre de Serviços de Recuperação.
7. Deixe as opções padrão de proteção Básica de DDoS, sem ponto final de serviço na rede.
8. Selecione **Criar**.

![Criar uma rede virtual](media/tutorial-prepare-azure/create-network.png)

A rede virtual demora alguns segundos a ser criada. Depois de criado, verá no portal Azure.

## <a name="useful-links"></a>Ligações úteis

Saiba mais sobre:
- [Redes Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Discos geridos](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Prepare a infraestrutura Hyper-V no local para a recuperação de desastres para Azure](hyper-v-prepare-on-premises-tutorial.md)
