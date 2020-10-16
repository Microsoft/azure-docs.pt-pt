---
title: Prepare recursos da Azure para a recuperação de desastres da Solução VMware Azure com recuperação do site Azure
description: Saiba como preparar recursos Azure para a recuperação de desastres das máquinas Azure VMware Solution utilizando a Recuperação do Site Azure.
services: site-recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 83e2c46e1ce1977d0dd136e821c90843ce2de481
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91814606"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-azure-vmware-solution-to-azure"></a>Prepare recursos da Azure para a recuperação de desastres da Azure VMware Solution to Azure

Este artigo descreve como preparar recursos e componentes do Azure para que possa configurar a recuperação de desastres dos VMS de Solução VMware Azure utilizando o serviço [de Recuperação do Local Azure.](site-recovery-overview.md) [A Azure VMware Solution](../azure-vmware/introduction.md) fornece nuvens privadas em Azure. Estas nuvens privadas contêm aglomerados vSphere, construídos a partir de uma infraestrutura dedicada de Azure de metal nu.

Este artigo é o primeiro tutorial de uma série que mostra como configurar a recuperação de desastres para VMS de Solução VMware Azure. 


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Verifique se a conta Azure tem permissões de replicação.
> * Crie um cofre dos Serviços de Recuperação. Um cofre contém metadados e informações de configuração para as VMs e outros componentes de replicação.
> * Crie uma rede virtual Azure (VNet). Quando os VMs do Azure são criados após o failover, eles são unidos a esta rede.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, reveja o artigo na secção Como Fazer da Tabela de Recuperação do Site de Conteúdos.

> [!NOTE]
> Alguns dos conceitos de utilização da Recuperação do Site Azure para a Solução VMware Azure sobrepõem-se à recuperação de desastres de VMware em pré-tempo e, portanto, a documentação será cruzada em conformidade.

## <a name="before-you-start"></a>Antes de começar

- [Implementar](../azure-vmware/tutorial-create-private-cloud.md) uma nuvem privada Azure VMware Solution em Azure
- Reveja a arquitetura para a recuperação de desastres [da VMware](vmware-azure-architecture.md)
- Leia perguntas comuns para [VMware](vmware-azure-common-questions.md)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar. Em seguida, inscreva-se no [portal Azure](https://portal.azure.com).


## <a name="verify-account-permissions"></a>Verificar permissões de conta

Se acabou de criar a sua conta Azure gratuita, é o administrador da sua subscrição e tem as permissões de que necessita. Se não for o administrador da subscrição, combine com o administrador para lhe atribuir as permissões de que precisa. Para ativar a replicação para uma nova máquina virtual, tem de ter permissões para:

- Criar uma VM no grupo de recursos selecionado.
- Criar uma VM na rede virtual selecionada.
- Escreva para uma conta de armazenamento Azure.
- Escreva para um disco gerido pelo Azure.

Para concluir estas tarefas, a conta deve estar atribuída à função incorporada de Contribuidor de Máquina Virtual. Além disso, para gerir as operações de Recuperação do Local num cofre, a sua conta deve ser atribuída à função incorporada do Colaborador de Recuperação do Local.


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

1. A partir do menu do portal Azure, **selecione Criar um recurso,** e procure no Marketplace for **Recovery**.
2. Selecione **Backup and Site Recovery** a partir dos resultados da pesquisa, e na página de Backup e Recuperação do Site, clique em **Criar**. 
3. Na página do **cofre do Create Recovery Services,** selecione a **Subscrição**. Estamos a usar a **assinatura Contoso.**
4. No **grupo De recursos,** selecione um grupo de recursos existente ou crie um novo. Para este tutorial estamos a usar **contosoRG.**
5. Em **nome do Cofre,** insira um nome amigável para identificar o cofre. Neste conjunto de tutoriais, estamos a utilizar **ContosoVMVault**.
6. Na **Região,** selecione a região em que o cofre deve ser localizado. estamos a utilizar **Europa Ocidental**.
7. Selecione **Rever + criar**.

   ![Screenshot da página do cofre do Create Recovery Services.](./media/tutorial-prepare-azure/new-vault-settings.png)

   O novo cofre será agora listado no **Dashboard**  >  **Todos os recursos,** e na página principal dos **cofres dos Serviços de Recuperação.**

## <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

 Os VMs de Solução VMware Azure são replicados em discos geridos azure. Quando ocorre uma falha, os VMs Azure são criados a partir destes discos geridos, e unidos à rede Azure que especifica neste procedimento.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Rede** > **Rede virtual**.
2. Mantenha o **Gestor de Recursos** selecionado como modelo de implementação.
3. Em **Nome**, introduza um nome de rede. O nome tem de ser exclusivo dentro do grupo de recursos do Azure. Estamos a utilizar **ContosoASRnet** neste tutorial.
4. No **espaço Address**, insira a gama de endereços da rede virtual na notação CDR. Estamos a usar **10.1.0.0/24.**
5. Em **Subscrição**, selecione a subscrição na qual vai criar a rede.
6. Especifique o **grupo de recursos** no qual a rede será criada. Estamos a utilizar o grupo de recursos **contosoRG** existente.
7. Em **Localização,** selecione a mesma região em que o cofre dos Serviços de Recuperação foi criado. No nosso tutorial é a **Europa Ocidental.** A rede deve estar na mesma região que o cofre.
8. No **intervalo Address,** insira o intervalo para a rede. Estamos a usar **10.1.0.0/24,** e não estamos a usar uma sub-rede.
9. Estamos a deixar as opções padrão de proteção básica do DDoS, sem ponto final de serviço, ou firewall na rede.
9. Selecione **Criar**.

   ![Screenshot das opções de rede virtual Create.](media/tutorial-prepare-azure/create-network.png)

A rede virtual demora alguns segundos a ser criada. Depois de criado, vai vê-lo no painel do portal Azure.




## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Preparar infraestrutura](avs-tutorial-prepare-avs.md)
- [Saiba mais](../virtual-network/virtual-networks-overview.md) sobre as redes Azure.
- [Saiba mais sobre](../virtual-machines/managed-disks-overview.md) discos geridos.
