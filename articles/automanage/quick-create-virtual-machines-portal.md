---
title: Quickstart - Ativar a auto-mutilação do Azure para VMs no portal Azure
description: Saiba como ativar rapidamente a auto-produção de máquinas virtuais num VM novo ou existente no portal Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: jushiman
ms.openlocfilehash: 6e0e582ed37230ba3f379f193a229cfec06f066c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648038"
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

Inicie sessão no [portal do Azure](https://aka.ms/AutomanagePortal-Ignite21).

## <a name="enable-automanage-for-a-single-vm"></a>Ativar a auto-mutilação para um único VM

1. Navegue pela Máquina Virtual que pretende ativar.

2. Clique na entrada **Auto-gestage (Pré-visualização)** na Tabela de Conteúdos em **Operações**.

3. **Selecione Começar**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\VMManage-GetStarted.png" alt-text="Começa o VM único.":::

4. Escolha as definições de auto-produção (Ambiente, Preferências, Conta de Auto-Produção) e **acerte ativar**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\VMManage-Enable.png" alt-text="Ative em VM único.":::

## <a name="enable-automanage-for-multiple-vms"></a>Ativar a auto-mutilação para vários VMs

1. Na barra de pesquisa, procure e selecione **Automanage – Azure machine best practices**.

2. **Selecione a Ativação em VM existente**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Ativar em VM existente.":::

3. Na lâmina **'Selecção' de máquinas:**
    1. Filtrar a lista de VMs pelo seu grupo **de subscrição** e **recursos.**
    1. Verifique a caixa de verificação de cada máquina virtual que pretende embarcar.
    1. Clique no botão **Seleção.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Selecione vM existente da lista de VMs disponíveis.":::

4. No **perfil de Configuração,** clique **em procurar e altere perfis e preferências.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Navegue e altere perfis e preferências.":::

5. No **perfil de configuração Select + preferências** lâmina:
    1. Selecione um perfil à esquerda: *Dev/Teste* para testes, *Prod* para produção.
    1. Clique no botão **Seleção.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Navegue no perfil de configuração de produção.":::

6. Clique no botão **Ativar**.


## <a name="enable-automanage-for-a-new-vm"></a>Permitir a auto-produção para um novo VM

Inscreva-se [aqui](https://aka.ms/AutomanagePortal-Ignite21) no portal Azure para criar um novo VM e ativar a auto-gestão.

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.

2. Na caixa de pesquisa acima da lista de recursos do Azure Marketplace, procure e selecione a imagem que deseja usar, em seguida, escolha **Criar**.

> [!NOTE]
> Verifique as [versões de distros e](automanage-linux.md#supported-linux-distributions-and-versions) servidor do Windows suportadas por [auto-gestão](automanage-windows-server.md#supported-windows-server-versions).

3. Preencha o separador Básicos com os seus **dados** VM.

> [!NOTE]
> Verifique as [regiões apoiadas pela](automanage-virtual-machines#supported-regions)Automanagem.

4. Navegue no separador **Gestão** e escolha o seu **Ambiente de Auto-gestão.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\VMCreate-Management-Tab.png" alt-text="Ativar a auto-gestão no Separador de Gestão.":::

5. Mantenha as restantes predefinições e, em seguida, selecione o botão **Rever + criar** na parte inferior da página.

6. Quando vir a mensagem que a validação passou, **selecione Criar**.

## <a name="disable-automanage-for-vms"></a>Desativar a auto-mutilação para VMs

Pare rapidamente de utilizar a auto-gestão Azure para máquinas virtuais desativando a auto-gestão.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Desativar a auto-produção numa máquina virtual.":::

1. Vá à página **de boas práticas da máquina virtual Automanage – Azure** que lista todos os seus VMs geridos automaticamente.
1. Selecione a caixa de verificação ao lado da máquina virtual que pretende desativar.
1. Clique no botão **de desativação desativar.**
1. Leia atentamente através das mensagens no pop-up resultante antes de concordar com o **Desactivamento**.


## <a name="clean-up-resources"></a>Limpar os recursos

Se criou um novo grupo de recursos para experimentar a Azure Automanage para máquinas virtuais e já não precisa, pode eliminar o grupo de recursos. A eliminação do grupo também elimina o VM e todos os recursos do grupo de recursos.

A Azure Automanage cria grupos de recursos predefinidos para armazenar recursos em. Consulte os grupos de recursos que têm a convenção de nomeação "DefaultResourceGroupRegionName" e "AzureBackupRGRegionName" para limpar todos os recursos.

1. Selecione o **grupo De Recursos**.
1. Na página para o grupo de recursos, **selecione Delete**.
1. Quando solicitado, confirme o nome do grupo de recursos e, em seguida, **selecione Delete**.


## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, permitiu que a Azure Automanage para VMs.

Descubra como pode criar e aplicar preferências personalizadas ao ativar a auto-produção na sua máquina virtual.

> [!div class="nextstepaction"]
> [Azure Automanagem para VMs - perfil de configuração personalizada](virtual-machines-custom-preferences.md)
