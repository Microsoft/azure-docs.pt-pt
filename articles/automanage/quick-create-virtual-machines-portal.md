---
title: Quickstart - Ativar a auto-mutilação do Azure para VMs no portal Azure
description: Saiba como ativar rapidamente a auto-produção de máquinas virtuais num VM novo ou existente no portal Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 69f43b626bb50171ceaca1b7a8761bec040d1dd6
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897233"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Quickstart: Ativar a auto-produção do Azure para máquinas virtuais no portal Azure

Começa com a Auto-gestão da Azure para máquinas virtuais utilizando o portal Azure para permitir a auto-gestão numa máquina virtual nova ou existente.


## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de começar.

> [!NOTE]
> As contas de teste gratuitas não têm acesso às máquinas virtuais utilizadas neste tutorial. Por favor, atualize para uma subscrição Pay-As-You-Go.

> [!IMPORTANT]
> É necessário ter a função **de Contribuinte** no grupo de recursos que contém os seus VMs para ativar a Automanage utilizando uma Conta de Automanagem existente. Se estiver a ativar a Automanage com uma nova Conta de Autoadministração, necessita das seguintes permissões: Função **de proprietário** ou **colaborador,** juntamente com as funções **de Administrador de Acesso ao Utilizador** na sua subscrição.


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Ativar a auto-mutilação dos VMs num VM existente

1. Na barra de pesquisa, procure e selecione **Automanage – Azure virtual machine boas práticas.**

2. **Selecione a Ativação em VM existente** .

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Ativar em VM existente.":::

3. Na lâmina **'Selecção' de máquinas:**
    1. Filtrar a lista de VMs pelo seu grupo **de subscrição** e **recursos.**
    1. Verifique a caixa de verificação de cada máquina virtual que pretende embarcar.
    1. Clique no botão **Seleção.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Ativar em VM existente.":::

4. No **perfil de Configuração,** clique **em procurar e altere perfis e preferências.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Ativar em VM existente.":::

5. No **perfil de configuração Select + preferências** lâmina:
    1. Selecione um perfil à esquerda: *Dev/Teste* para testes, *Prod* para produção.
    1. Clique no botão **Seleção.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Ativar em VM existente.":::

6. Clique no botão **Ativar** .


## <a name="enable-automanage-for-vms-on-a-new-vm"></a>Permitir a auto-produção de VMs num novo VM

Inscreva-se [aqui](https://aka.ms/automanageportalnextstep) no portal Azure para criar um novo VM e ativar a auto-gestão.

1. Siga os passos de criação no [Quickstart - crie um Windows VM no portal Azure](..\virtual-machines\windows\quick-create-portal.md).

2. Depois de o seu VM ser implantado, pousará na página de estado de implantação que recomendou os **próximos passos** na parte inferior.

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-next-steps.png" alt-text="Ativar em VM existente.":::

3. Nos **próximos passos** , selecione **Ativar as melhores práticas da máquina virtual de auto-geração** .

4. Na página **de auto-mutilação – Azure, as** **máquinas** virtuais serão automaticamente povoadas pelo seu VM recém-criado.

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-new-enable-overview.png" alt-text="Ativar em VM existente.":::

5. No **perfil de Configuração,** clique **em procurar e altere perfis e preferências.**

6. No **perfil de configuração Select + preferências** lâmina:
    1. Selecione um perfil à esquerda: *Dev/Teste* para testes, *Prod* para produção.
    1. Clique no botão **Seleção.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Ativar em VM existente.":::

7. Clique no botão **Ativar** .

## <a name="disable-automanage-for-vms"></a>Desativar a auto-mutilação para VMs

Pare rapidamente de utilizar a auto-gestão Azure para máquinas virtuais desativando a auto-gestão.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Ativar em VM existente.":::

1. Vá à página **de boas práticas da máquina virtual Automanage – Azure** que lista todos os seus VMs geridos automaticamente.
1. Selecione a caixa de verificação ao lado da máquina virtual que pretende desativar.
1. Clique no botão **de desativação desativar.**
1. Leia atentamente através das mensagens no pop-up resultante antes de concordar com o **Desactivamento** .


## <a name="clean-up-resources"></a>Limpar os recursos

Se criou um novo grupo de recursos para experimentar a Azure Automanage para máquinas virtuais e já não precisa, pode eliminar o grupo de recursos. A eliminação do grupo também elimina o VM e todos os recursos do grupo de recursos.

A Azure Automanage cria grupos de recursos predefinidos para armazenar recursos em. Consulte os grupos de recursos que têm a convenção de nomeação "DefaultResourceGroupRegionName" e "AzureBackupRGRegionName" para limpar todos os recursos.

1. Selecione o **grupo De Recursos** .
1. Na página para o grupo de recursos, **selecione Delete** .
1. Quando solicitado, confirme o nome do grupo de recursos e, em seguida, **selecione Delete** .


## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, permitiu que a Azure Automanage para VMs. 

Descubra como pode criar e aplicar preferências personalizadas ao ativar a auto-produção na sua máquina virtual. 

> [!div class="nextstepaction"]
> [Azure Automanagem para VMS - Perfil de configuração personalizada](virtual-machines-custom-preferences.md)
