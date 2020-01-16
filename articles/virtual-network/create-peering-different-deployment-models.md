---
title: Criar um emparelhamento de rede virtual do Azure-modelos de implantação diferentes-mesma assinatura | Microsoft Docs
description: Saiba como criar um emparelhamento de rede virtual entre redes virtuais criadas por meio de diferentes modelos de implantação do Azure que existem na mesma assinatura do Azure.
services: virtual-network
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 6c539121914418f5373b333e9493e24f7769fa5b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978981"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Criar um emparelhamento de rede virtual-modelos de implantação diferentes, mesma assinatura

Neste tutorial, você aprenderá a criar um emparelhamento de rede virtual entre redes virtuais criadas por meio de diferentes modelos de implantação. Ambas as redes virtuais existem na mesma assinatura. O emparelhamento de duas redes virtuais permite que os recursos em diferentes redes virtuais se comuniquem entre si com a mesma largura de banda e latência como se os recursos estivessem na mesma rede virtual. Saiba mais sobre o [emparelhamento de rede virtual](virtual-network-peering-overview.md).

As etapas para criar um emparelhamento de rede virtual são diferentes, dependendo se as redes virtuais estão na mesma ou em diferentes assinaturas e em qual [modelo de implantação do Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) as redes virtuais são criadas. Saiba como criar um emparelhamento de rede virtual em outros cenários clicando no cenário da tabela a seguir:

|Modelo de implementação do Azure  | Subscrição do Azure  |
|--------- |---------|
|[O Gerenciador de recursos](tutorial-connect-virtual-networks-portal.md) |Idêntica|
|[O Gerenciador de recursos](create-peering-different-subscriptions.md) |Outros|
|[Um Gerenciador de recursos, um clássico](create-peering-different-deployment-models-subscriptions.md) |Outros|

Um emparelhamento de rede virtual não pode ser criado entre duas redes virtuais implantadas por meio do modelo de implantação clássico. Se você precisar conectar redes virtuais que foram criadas por meio do modelo de implantação clássico, você pode usar um [Gateway de VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure para conectar as redes virtuais.

Este tutorial emparelha redes virtuais na mesma região. Você também pode emparelhar redes virtuais em diferentes [regiões com suporte](virtual-network-manage-peering.md#cross-region). É recomendável que você se familiarize com os [requisitos e as restrições de emparelhamento](virtual-network-manage-peering.md#requirements-and-constraints) antes de emparelhar redes virtuais.

Você pode usar o portal do Azure, a CLI ( [interface de linha de comando](#cli) ) do Azure, o Azure [PowerShell](#powershell)ou um modelo de Azure Resource Manager para criar um emparelhamento de rede virtual. Clique em qualquer um dos links de ferramentas anteriores para ir diretamente para as etapas para criar um emparelhamento de rede virtual usando sua ferramenta de escolha.

## <a name="create-peering---azure-portal"></a>Criar emparelhamento-portal do Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). A conta com a qual você entra deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Para obter uma lista de permissões, consulte [permissões de emparelhamento de rede virtual](virtual-network-manage-peering.md#requirements-and-constraints).
2. Clique em **+ novo**, em **rede**e em **rede virtual**.
3. Na folha **criar rede virtual** , insira ou selecione valores para as seguintes configurações e clique em **criar**:
    - **Name**: *myVnet1*
    - **Espaço de endereço**: *10.0.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - **Intervalo de endereços da sub-rede**: *10.0.0.0/24*
    - **Assinatura**: selecione sua assinatura
    - **Grupo de recursos**: selecione **criar novo** e digite *MyResource* Group
    - **Local**: *leste dos EUA*
4. Clique em **+ Novo**. Na caixa **Pesquisar no Marketplace** , digite *rede virtual*. Clique em **rede virtual** quando ela aparecer nos resultados da pesquisa.
5. Na folha **rede virtual** , selecione **clássico** na caixa **selecionar um modelo de implantação** e, em seguida, clique em **criar**.
6. Na folha **criar rede virtual** , insira ou selecione valores para as seguintes configurações e clique em **criar**:
    - **Name**: *myVnet2*
    - **Espaço de endereço**: *10.1.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - **Intervalo de endereços da sub-rede**: *10.1.0.0/24*
    - **Assinatura**: selecione sua assinatura
    - **Grupo de recursos**: selecione **usar existente** e selecione *MyResource* Group
    - **Local**: *leste dos EUA*
7. Na caixa **Pesquisar recursos** na parte superior do portal, digite *MyResource*. Clique em **MyResource** e quando ele aparecer nos resultados da pesquisa. Uma folha é exibida para o grupo de recursos **MyResource** Group. O grupo de recursos contém as duas redes virtuais criadas nas etapas anteriores.
8. Clique em **myVNet1**.
9. Na folha **myVnet1** exibida, clique em **emparelhamentos** na lista vertical de opções no lado esquerdo da folha.
10. Na folha **myVnet1-emparelhamentos** exibida, clique em **+ Adicionar**
11. Na folha **Adicionar emparelhamento** que aparece, insira ou selecione as seguintes opções e clique em **OK**:
     - **Name**: *myVnet1ToMyVnet2*
     - **Modelo de implantação de rede virtual**: selecione **clássico**.
     - **Assinatura**: selecione sua assinatura
     - **Rede virtual**: clique em **escolher uma rede virtual**e em **myVnet2**.
     - **Permitir acesso à rede virtual:** Verifique se **habilitado** está selecionado.
    Nenhuma outra configuração é usada neste tutorial. Para saber mais sobre todas as configurações de emparelhamento, leia [gerenciar emparelhamentos de rede virtual](virtual-network-manage-peering.md#create-a-peering).
12. Depois de clicar em **OK** na etapa anterior, a folha **Adicionar emparelhamento** será fechada e você verá a folha **myVnet1-emparelhamentos** novamente. Depois de alguns segundos, o emparelhamento que você criou aparece na folha. **Conectado** é listado na coluna **status de emparelhamento** para o emparelhamento **myVnet1ToMyVnet2** que você criou.

    O emparelhamento agora está estabelecido. Todos os recursos do Azure criados em qualquer rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nome do Azure padrão para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você quiser resolver nomes entre redes virtuais em um emparelhamento, você deve criar seu próprio servidor DNS. Saiba como configurar a [resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
13. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você pode criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual para a outra, para validar a conectividade.
14. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas na seção [excluir recursos](#delete-portal) deste artigo.

## <a name="cli"></a>Criar emparelhamento-CLI do Azure

Conclua as etapas a seguir usando a CLI clássica do Azure e a CLI do Azure. Você pode concluir as etapas da Azure Cloud Shell, selecionando o botão **experimentar** em qualquer uma das etapas a seguir ou instalando a [CLI clássica](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) e a [CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) e executando os comandos em seu computador local.

1. Se estiver usando o Cloud Shell, pule para a etapa 2, porque o Cloud Shell o conecta automaticamente ao Azure. Abra uma sessão de comando e entre no Azure usando o comando `azure login`.
2. Execute a CLI no modo de gerenciamento de serviços inserindo o comando `azure config mode asm`.
3. Digite o seguinte comando para criar a rede virtual (clássica):

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. Execute o script de CLI do bash a seguir usando a CLI, não a CLI clássica. Para obter opções sobre a execução de scripts de CLI do bash no computador com Windows, consulte [instalar o CLI do Azure no Windows](/cli/azure/install-azure-cli-windows).

   ```azurecli-interactive
   #!/bin/bash

   # Create a resource group.
   az group create \
     --name myResourceGroup \
     --location eastus

   # Create the virtual network (Resource Manager).
   az network vnet create \
     --name myVnet1 \
     --resource-group myResourceGroup \
     --location eastus \
     --address-prefix 10.0.0.0/16
   ```

5. Crie um emparelhamento de rede virtual entre as duas redes virtuais criadas por meio de diferentes modelos de implantação usando a CLI. Copie o script a seguir em um editor de texto em seu computador. Substitua `<subscription id>` pela sua ID de assinatura. Se você não souber sua ID de assinatura, insira o comando `az account show`. O valor de **ID** na saída é sua ID de assinatura. Cole o script modificado em na sessão da CLI e, em seguida, pressione `Enter`.

   ```azurecli-interactive
   # Get the ID for VNet1.
   vnet1Id=$(az network vnet show \
     --resource-group myResourceGroup \
     --name myVnet1 \
     --query id --out tsv)

   # Peer VNet1 to VNet2.
   az network vnet peering create \
     --name myVnet1ToMyVnet2 \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --remote-vnet-id /subscriptions/<subscription id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2 \
     --allow-vnet-access
   ```

6. Depois que o script for executado, examine o emparelhamento para a rede virtual (Resource Manager). Copie o comando a seguir, Cole-o na sessão da CLI e, em seguida, pressione `Enter`:

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   A saída mostra **conectada** na coluna **PeeringState** .

   Todos os recursos do Azure criados em qualquer rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nome do Azure padrão para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você quiser resolver nomes entre redes virtuais em um emparelhamento, você deve criar seu próprio servidor DNS. Saiba como configurar a [resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
7. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você pode criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual para a outra, para validar a conectividade.
8. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas em [excluir recursos](#delete-cli) neste artigo.

## <a name="powershell"></a>Criar emparelhamento-PowerShell

1. Instale a versão mais recente dos módulos [Azure](https://www.powershellgallery.com/packages/Azure) e [AZ](https://www.powershellgallery.com/packages/Az/) do PowerShell. Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão do PowerShell.
3. No PowerShell, entre no Azure digitando o comando `Add-AzureAccount`. A conta com a qual você entra deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Para obter uma lista de permissões, consulte [permissões de emparelhamento de rede virtual](virtual-network-manage-peering.md#requirements-and-constraints).
4. Para criar uma rede virtual (clássica) com o PowerShell, você deve criar um novo arquivo de configuração de rede existente ou modificá-lo. Saiba como [exportar, atualizar e importar arquivos de configuração de rede](virtual-networks-using-network-configuration-file.md). O arquivo deve incluir o seguinte elemento **VirtualNetworkSite** para a rede virtual usada neste tutorial:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > A importação de um arquivo de configuração de rede alterado pode causar alterações em redes virtuais existentes (clássicas) em sua assinatura. Certifique-se de adicionar apenas a rede virtual anterior e de que você não altere ou remova as redes virtuais existentes da sua assinatura.
5. Entre no Azure para criar a rede virtual (Resource Manager) inserindo o comando `Connect-AzAccount`. A conta com a qual você entra deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Para obter uma lista de permissões, consulte [permissões de emparelhamento de rede virtual](virtual-network-manage-peering.md#requirements-and-constraints).
6. Crie um grupo de recursos e uma rede virtual (Resource Manager). Copie o script, Cole-o no PowerShell e, em seguida, pressione `Enter`.

    ```powershell
    # Create a resource group.
      New-AzResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. Crie um emparelhamento de rede virtual entre as duas redes virtuais criadas por meio de diferentes modelos de implantação. Copie o script a seguir em um editor de texto em seu computador. Substitua `<subscription id>` pela sua ID de assinatura. Se você não souber sua ID de assinatura, insira o comando `Get-AzSubscription` para exibi-la. O valor de **ID** na saída retornada é sua ID de assinatura. Para executar o script, copie o script modificado do editor de texto, clique com o botão direito do mouse na sessão do PowerShell e pressione `Enter`.

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Depois que o script for executado, examine o emparelhamento para a rede virtual (Resource Manager). Copie o comando a seguir, Cole-o na sessão do PowerShell e, em seguida, pressione `Enter`:

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    A saída mostra **conectada** na coluna **PeeringState** .

    Todos os recursos do Azure criados em qualquer rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nome do Azure padrão para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você quiser resolver nomes entre redes virtuais em um emparelhamento, você deve criar seu próprio servidor DNS. Saiba como configurar a [resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

9. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você pode criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual para a outra, para validar a conectividade.
10. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas em [excluir recursos](#delete-powershell) neste artigo.

## <a name="delete"></a>Excluir recursos

Ao concluir este tutorial, talvez você queira excluir os recursos criados no tutorial, para que não incorra em encargos de uso. A exclusão de um grupo de recursos também exclui todos os recursos que estão no grupo de recursos.

### <a name="delete-portal"></a>Portal do Azure

1. Na caixa de pesquisa do portal, insira **MyResource**. Nos resultados da pesquisa, clique em **MyResource**.
2. Na folha **Myresourceus** , clique no ícone **excluir** .
3. Para confirmar a exclusão, na caixa **digite o nome do grupo de recursos** , insira **MyResource**Group e clique em **excluir**.

### <a name="delete-cli"></a>CLI do Azure

1. Use o CLI do Azure para excluir a rede virtual (Resource Manager) com o seguinte comando:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Use a CLI clássica para excluir a rede virtual (clássica) com os seguintes comandos:

    ```azurecli-interactive
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. Digite o seguinte comando para excluir a rede virtual (Resource Manager):

    ```powershell
    Remove-AzResourceGroup -Name myResourceGroup -Force
    ```

2. Para excluir a rede virtual (clássica) com o PowerShell, você deve modificar um arquivo de configuração de rede existente. Saiba como [exportar, atualizar e importar arquivos de configuração de rede](virtual-networks-using-network-configuration-file.md). Remova o seguinte elemento VirtualNetworkSite para a rede virtual usada neste tutorial:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > A importação de um arquivo de configuração de rede alterado pode causar alterações em redes virtuais existentes (clássicas) em sua assinatura. Verifique se você removeu apenas a rede virtual anterior e se não alterou ou removeu outras redes virtuais existentes da sua assinatura.

## <a name="next-steps"></a>Passos seguintes

- Familiarize-se totalmente com [restrições e comportamentos importantes de emparelhamento de rede virtual](virtual-network-manage-peering.md#requirements-and-constraints) antes de criar um emparelhamento de rede virtual para uso em produção.
- Saiba mais sobre todas [as configurações de emparelhamento de rede virtual](virtual-network-manage-peering.md#create-a-peering).
- Saiba como [criar uma topologia de rede de Hub e spoke com o](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) emparelhamento de rede virtual.
