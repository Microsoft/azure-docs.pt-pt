---
title: Prepare o Azure para a recuperação de desastres hiper-V com recuperação do local de Azure
description: Saiba como preparar o Azure para a recuperação de desastres dos Hiper-V VMs no local utilizando a recuperação do local do Azure
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5ca03336fc60abbb458615e922a2556dd21a79a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739683"
---
# <a name="prepare-azure-resources-for-hyper-v-disaster-recovery"></a>Prepare recursos do Azure para a recuperação de desastres hiper-V

 [A Azure Site Recovery](site-recovery-overview.md) ajuda a manter a continuidade do negócio e a recuperação de desastres (BCDR) mantendo as aplicações de negócio em execução durante as paragens planeadas e não planeadas. O Site Recovery gere e orquestra a recuperação após desastre de computadores no local e máquinas virtuais (VMs) do Azure, incluindo replicação, ativação pós-falha e recuperação.

Este tutorial é o primeiro de uma série que descreve como configurar a recuperação de desastres para os Hiper-V VMs no local.

> [!NOTE]
> Desenhamos tutoriais para mostrar o caminho de implantação mais simples para um cenário. Estes tutoriais usam opções padrão quando possível, e não mostram todas as definições e caminhos possíveis. Para mais informações, consulte a secção "Como Fazer" para cada cenário correspondente.

Este tutorial mostra-lhe como preparar componentes Azure quando pretende replicar VMs no local (Hyper-V) para Azure. Vai aprender a:

> [!div class="checklist"]
> * Certifique-se de que a conta do Azure tem permissões de replicação.
> * Crie uma conta de armazenamento Azure, que armazena imagens de máquinas replicadas.
> * Crie um cofre de Serviços de Recuperação, que armazena metadados e informações de configuração para VMs e outros componentes de replicação.
> * Configure uma rede do Azure. Quando os VMs do Azure são criados após o failover, eles são unidos a esta rede.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="sign-in"></a>Iniciar sessão

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Verificar permissões de conta

Se acabou de criar uma conta Azure gratuita, é o administrador dessa subscrição. Se não for o administrador, trabalhe com o administrador para atribuir as permissões de que necessita. Para ativar a replicação para uma nova máquina virtual, tem de ter permissões para:

- Criar uma VM no grupo de recursos selecionado.
- Criar uma VM na rede virtual selecionada.
- Escrever na conta de armazenamento selecionada.

Para completar estas tarefas, a sua conta deve ser atribuída à função de Contribuinte de Máquina Virtual. Para gerir as operações de Recuperação do Local num cofre, a sua conta deve ser atribuída à função incorporada do Colaborador de Recuperação do Local.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

As imagens das máquinas replicadas são guardadas no armazenamento do Azure. As VMs do Azure são criadas a partir do armazenamento quando fizer a ativação pós-falha do local para o Azure. A conta de armazenamento tem de estar na mesma região que o cofre dos Serviços de Recuperação.

1. No menu do [portal Azure,](https://portal.azure.com) selecione **Criar uma** conta de armazenamento de recursos  >    >  **- blob, ficheiro, tabela, fila**.
2. Em **Criar conta de armazenamento**, introduza um nome para a conta.  O nome que escolher deve ser único dentro do Azure, ter de 3 a 24 caracteres de comprimento, e usar apenas letras e números minúsculos. Para este tutorial, use **contosovmsacct1910171607**.
3. Em **Modelo de implementação**, selecione **Resource Manager**.
4. No **tipo Conta,** selecione **Armazenamento (v1 de uso geral)**. Não selecione o armazenamento de blobs.
5. Em **Replicação**, selecione o **Armazenamento georredundante com acesso de leitura** predefinido para redundância de armazenamento.
6. Em **Desempenho**, selecione **Padrão**. Em seguida, no **nível de Acesso**, selecione a opção padrão de **Hot**.
7. Na **Subscrição,** escolha a subscrição na qual pretende criar a nova conta de armazenamento.
8. Em **Grupo de recursos**, introduza um novo grupo de recursos. Um grupo de recursos Azure é um recipiente lógico no qual os recursos da Azure são implantados e geridos. Para este tutorial, utilize **o ContosoRG.**
9. No **Local,** escolha a localização geográfica para a sua conta de armazenamento. Para este tutorial, use **a Europa Ocidental.**
10. Selecione **Criar** para criar a conta de armazenamento.

   ![Screenshot das opções de conta de armazenamento Criar.](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos serviços de recuperação

1. No portal Azure, selecione **+Criar um recurso** e, em seguida, pesse no Mercado Azure para serviços de recuperação.
2. Selecione **Backup and Site Recovery (OMS)**. Em seguida, na página **de Backup e Recuperação do Site,** selecione **Criar**.
1. No **cofre dos serviços de recuperação > Name,** insira um nome amigável para identificar o cofre. Neste tutorial, utilize **ContosoVMVault**.
2. No **grupo De recursos,** selecione um grupo de recursos existente ou crie um novo. Para este tutorial, use **contosoRG**.
3. No **Local,** selecione a região onde o cofre deve ser localizado. Para este tutorial, use **a Europa Ocidental.**
4. Para aceder rapidamente ao cofre a partir do painel de instrumentos, selecione **Pin to dashboard**  >  **Create**.

![Screenshot da página do cofre do Create Recovery Services.](./media/tutorial-prepare-azure/new-vault-settings.png)

O novo cofre aparece no **Dashboard**  >  **Todos os recursos** e na página principal dos **cofres dos Serviços de Recuperação.**

## <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Quando as VMs do Azure são criadas a partir do armazenamento após a ativação pós-falha, são associadas a esta rede.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Rede** > **Rede virtual**. Deixe o Resource Manager selecionado como o modelo de implementação.
2. Em **Nome**, introduza um nome de rede. O nome tem de ser exclusivo dentro do grupo de recursos do Azure. Para este tutorial, utilize **ContosoASRnet.**
3. Especifique o grupo de recursos para criar a rede. Para este tutorial, utilize o grupo de recursos existente **contosoRG**.
4. Na **gama Address**, introduza **10.0.0.0/24** como o intervalo da rede. Não há sub-rede para esta rede.
5. Em **Subscrição**, selecione a subscrição na qual vai criar a rede.
6. Em **Localização**, escolha **Europa Ocidental.** A rede tem de estar na mesma região que o cofre de Serviços de Recuperação.
7. Deixe as opções predefinidos da proteção DDoS básica, sem ponto final de serviço na rede.
8. Selecione **Criar**.

![Screenshot das opções de rede virtual Create.](media/tutorial-prepare-azure/create-network.png)

A rede virtual demora alguns segundos a ser criada. Depois de criado, vai vê-lo no painel do portal Azure.

## <a name="useful-links"></a>Ligações úteis

Saiba mais sobre:
- [Redes Azure](../virtual-network/virtual-networks-overview.md)
- [Discos geridos](../virtual-machines/managed-disks-overview.md)



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Preparar as infraestruturas hiper-V no local para a recuperação de desastres para Azure](hyper-v-prepare-on-premises-tutorial.md)
