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
ms.openlocfilehash: 5f5f1e70d9ae309c90291ccac1e6dd61e7a9d056
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038428"
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

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-getstartedbutton.png" alt-text="Começa o VM único.":::

4. Escolha as definições de auto-produção (Ambiente, Preferências, Conta de Auto-Produção) e **acerte ativar**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-enablepane.png" alt-text="Ative em VM único.":::

## <a name="enable-automanage-for-multiple-vms"></a>Ativar a auto-mutilação para vários VMs

1. Na barra de pesquisa, procure e selecione **Automanage – Azure machine best practices**.

2. **Selecione a Ativação em VM existente**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Ativar em VM existente.":::

3. Na lâmina **'Selecção' de máquinas:**
    1. Filtrar a lista de VMs pelo seu grupo **de subscrição** e **recursos.**
    1. Verifique a caixa de verificação de cada máquina virtual que pretende embarcar.
    1. Clique no botão **Seleção.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Selecione vM existente da lista de VMs disponíveis.":::

4. Em **Ambiente**, selecione o seu tipo de ambiente: **Dev/Test** ou **Production**. 

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Selecione ambientes.":::

   Clique **em Comparar Detalhes do Ambiente** para ver as diferenças entre os ambientes.
    1. Selecione um ambiente sobre o dropdown: *Dev/Test* para testes, *Produção* para produção.
    1. Clique no botão **OK.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Navegue pelo ambiente de produção.":::

5. Por padrão, a preferência **Azure Best Practices** é selecionada para as preferências de configuração. Para alterar isto, crie uma nova preferência ou selecione uma existente. 

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-preference.png" alt-text="Criar preferência.":::

6. Clique no botão **Ativar**.


## <a name="enable-automanage-for-a-new-vm"></a>Permitir a auto-produção para um novo VM

Inscreva-se [aqui](https://aka.ms/AzureAutomanagePreview) no portal Azure para criar um novo VM e ativar a auto-gestão.

1. Preencha o separador Básicos com os seus **dados** VM.

> [!NOTE]
> Verifique as [regiões apoiadas por](automanage-virtual-machines.md#supported-regions) auto-gestão e as [versões de distros](automanage-linux.md#supported-linux-distributions-and-versions) e servidor do Windows suportadas por [auto-gestão](automanage-windows-server.md#supported-windows-server-versions).

2. Navegue no separador **Gestão** e escolha o seu **Ambiente de Auto-gestão.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmcreate-managementtab.png" alt-text="Ativar a auto-gestão no Separador de Gestão.":::

3. Mantenha as restantes predefinições e, em seguida, selecione o botão **Rever + criar** na parte inferior da página.

4. Quando vir a mensagem que a validação passou, **selecione Criar**.

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
> [Azure Automanagem para VMs - Preferências de configuração personalizada](virtual-machines-custom-preferences.md)
