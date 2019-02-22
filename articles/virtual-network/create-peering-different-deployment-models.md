---
title: Criar um peering de rede virtual do Azure - diferentes modelos de implementação - mesma subscrição | Documentos da Microsoft
description: Saiba como criar uma rede virtual peering entre redes virtuais criadas com modelos de implementação do Azure diferentes que estejam na mesma subscrição do Azure.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: jdial;anavin
ms.openlocfilehash: 4aa6fe901a49f4618b4844f9f5d2a94903d49cbd
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652370"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Criar um peering de rede virtual - diferentes modelos de implementação, a mesma subscrição

Neste tutorial, vai aprender a criar uma rede virtual peering entre redes virtuais criadas com modelos de implementação diferentes. Ambas as redes virtuais existem na mesma subscrição. Peering dois recursos de permite de redes virtuais em redes virtuais diferentes para comunicar entre si com o mesmo da largura de banda e latência, como se os recursos estivessem na mesma rede virtual. Saiba mais sobre [peering de rede Virtual](virtual-network-peering-overview.md).

Os passos para criar um peering de rede virtual são diferentes, dependendo se as redes virtuais estão no mesmo, ou em diferentes, subscrições e qual [modelo de implementação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) as redes virtuais forem criadas com. Saiba como criar uma rede virtual peering em outros cenários ao clicar o cenário da tabela seguinte:

|Modelo de implementação do Azure  | Subscrição do Azure  |
|--------- |---------|
|[Ambas com Resource Manager](tutorial-connect-virtual-networks-portal.md) |Mesmo|
|[Ambas com Resource Manager](create-peering-different-subscriptions.md) |Diferente|
|[Uma com Resource Manager, outra com clássica](create-peering-different-deployment-models-subscriptions.md) |Diferente|

Não é possível criar um peering de rede virtual entre duas redes virtuais implementadas através do modelo de implementação clássica. Se precisar de ligar redes virtuais que foram ambas criadas por meio do modelo de implementação clássica, pode utilizar do Azure [Gateway de VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para ligar as redes virtuais.

Este tutorial elementos redes virtuais na mesma região. Pode também configurar o peering entre redes virtuais em diferentes [regiões suportadas](virtual-network-manage-peering.md#cross-region). É recomendável que se familiarizar com o [peering requisitos e restrições](virtual-network-manage-peering.md#requirements-and-constraints) antes peering de redes virtuais.

Pode utilizar o portal do Azure, do Azure [interface de linha de comandos](#cli) (CLI), Azure [PowerShell](#powershell), ou um modelo do Azure Resource Manager para criar um peering de rede virtual. Clique em qualquer uma das ligações de ferramenta anterior para ir diretamente para os passos para criar um peering de rede virtual com a sua ferramenta preferencial.

## <a name="create-peering---azure-portal"></a>Criar peering - portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com). A conta que iniciar sessão com tem de ter as permissões necessárias para criar um peering de rede virtual. Para obter uma lista de permissões, consulte [permissões de peering de rede Virtual](virtual-network-manage-peering.md#requirements-and-constraints).
2. Clique em **+ novo**, clique em **redes**, em seguida, clique em **rede Virtual**.
3. Na **criar rede virtual** painel, introduza ou selecione os valores para as seguintes definições e clique em **criar**:
    - **Name**: *myVnet1*
    - **Espaço de endereços**: *10.0.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - **Intervalo de endereços da sub-rede**: *10.0.0.0/24*
    - **Subscrição**: Selecione a sua subscrição
    - **Grupo de recursos**: Selecione **criar novo** e introduza *myResourceGroup*
    - **Localização**: *E.U.A. leste*
4. Clique em **+ Novo**. Na **pesquisar no Marketplace** , escreva *rede Virtual*. Clique em **rede Virtual** quando for apresentada nos resultados da pesquisa.
5. Na **rede Virtual** painel, selecione **clássico** no **selecionar um modelo de implementação** caixa e, em seguida, clique em **criar**.
6. Na **criar rede virtual** painel, introduza ou selecione os valores para as seguintes definições e clique em **criar**:
    - **Name**: *myVnet2*
    - **Espaço de endereços**: *10.1.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - **Intervalo de endereços da sub-rede**: *10.1.0.0/24*
    - **Subscrição**: Selecione a sua subscrição
    - **Grupo de recursos**: Selecione **utilizar existente** e selecione *myResourceGroup*
    - **Localização**: *E.U.A. leste*
7. Na **recursos de pesquisa** caixa na parte superior do portal, tipo *myResourceGroup*. Clique em **myResourceGroup** quando for apresentada nos resultados da pesquisa. É apresentado um painel para o **myresourcegroup** grupo de recursos. O grupo de recursos contém as duas redes virtuais, criadas nos passos anteriores.
8. Clique em **myVNet1**.
9. Na **myVnet1** painel que aparece, clique em **Peerings** na vertical lista de opções no lado esquerdo do painel.
10. Na **myVnet1 - Peerings** painel que será exibida, clique em **+ adicionar**
11. Na **adicionar peering** painel apresentado, introduza ou selecione as seguintes opções e clique em **OK**:
     - **Name**: *myVnet1ToMyVnet2*
     - **Modelo de implementação de rede virtual**:  Selecione **clássico**.
     - **Subscrição**: Selecione a sua subscrição
     - **Rede virtual**:  Clique em **escolher uma rede virtual**, em seguida, clique em **myVnet2**.
     - **Permitir o acesso de rede virtual:** Certifique-se de que **ativado** está selecionada.
    Não existem outras definições são utilizadas neste tutorial. Para saber mais sobre todas as definições de peering, leia [gerir peerings de rede virtual](virtual-network-manage-peering.md#create-a-peering).
12. Depois de clicar em **OK** no passo anterior, o **adicionar peering** painel fecha-se e ver o **myVnet1 - Peerings** painel novamente. Após alguns segundos, o peering que criou é apresentada no painel. **Ligado** é listado na **estado de PEERING** coluna para o **myVnet1ToMyVnet2** peering é criado.

    Agora é estabelecido o peering. Todos os recursos do Azure que cria em qualquer rede virtual agora são capazes de comunicar entre si através de seus endereços IP. Se estiver a utilizar a resolução de nomes do Azure do padrão para as redes virtuais, os recursos em redes virtuais não são capazes de resolver nomes entre as redes virtuais. Se pretender resolver nomes entre redes virtuais num modo de peering, tem de criar seu próprio servidor DNS. Saiba como configurar [resolução de nomes através de seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
13. **Opcional**: Embora a criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conectividade.
14. **Opcional**: Para eliminar os recursos que criar neste tutorial, conclua os passos a [eliminar recursos](#delete-portal) seção deste artigo.

## <a name="cli"></a>Criar peering - CLI do Azure

Conclua os passos seguintes a utilizar a CLI clássica do Azure e a CLI do Azure. Pode seguir os passos do Azure Cloud Shell, ao selecionar apenas os **experimentá-la** botão em qualquer um dos seguintes passos ou instalando o [CLI clássica](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) e [CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) e executar os comandos no seu computador local.

1. Se utilizar o Cloud Shell, avance para o passo 2, uma vez que o Cloud Shell automaticamente iniciada no Azure. Abra uma sessão de comando e inicie sessão no Azure com o `azure login` comando.
2. Executar a CLI no modo de gestão de serviço ao introduzir o `azure config mode asm` comando.
3. Introduza o seguinte comando para criar a rede virtual (clássico):

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. Execute o seguinte script CLI de bash com a CLI, não a CLI clássica. Para opções de execução de bash de scripts da CLI no computador Windows, consulte [instalar a CLI do Azure no Windows](/cli/azure/install-azure-cli-windows).

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

5. Crie uma peering entre as duas redes virtuais criadas através de modelos de implementação diferentes com a CLI de rede virtual. Copie o seguinte script para um editor de texto no seu PC. Substitua `<subscription id>` com o ID da subscrição. Se não souber o ID de subscrição, introduza o `az account show` comando. O valor para **id** no resultado é o ID da subscrição. Cole o script modificado à sua sessão CLI e, em seguida, prima `Enter`.

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

6. Depois do script for executado, reveja o peering da rede virtual (Resource Manager). Copie o seguinte comando, cole-o na sua sessão CLI e, em seguida, prima `Enter`:

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   Mostra a saída **ligado** no **PeeringState** coluna.

   Todos os recursos do Azure que cria em qualquer rede virtual agora são capazes de comunicar entre si através de seus endereços IP. Se estiver a utilizar a resolução de nomes do Azure do padrão para as redes virtuais, os recursos em redes virtuais não são capazes de resolver nomes entre as redes virtuais. Se pretender resolver nomes entre redes virtuais num modo de peering, tem de criar seu próprio servidor DNS. Saiba como configurar [resolução de nomes através de seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
7. **Opcional**: Embora a criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conectividade.
8. **Opcional**: Para eliminar os recursos que criar neste tutorial, conclua os passos em [eliminar recursos](#delete-cli) neste artigo.

## <a name="powershell"></a>Criar peering - PowerShell

1. Instalar a versão mais recente do PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) e [Az](https://www.powershellgallery.com/packages/Az/) módulos. Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão do PowerShell.
3. No PowerShell, inicie sessão no Azure ao introduzir o `Add-AzureAccount` comando. A conta que iniciar sessão com tem de ter as permissões necessárias para criar um peering de rede virtual. Para obter uma lista de permissões, consulte [permissões de peering de rede Virtual](virtual-network-manage-peering.md#requirements-and-constraints).
4. Para criar uma rede virtual (clássico) com o PowerShell, tem de criar um novo ou modificar um arquivo de configuração de rede existente. Saiba como [exportar, atualizar e importar ficheiros de configuração de rede](virtual-networks-using-network-configuration-file.md). O ficheiro deve incluir o seguinte procedimento **VirtualNetworkSite** elemento para a rede virtual utilizado neste tutorial:

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
    > Importar um ficheiro de configuração de rede alterada pode fazer com que as alterações às redes virtuais existentes (clássicas) na sua subscrição. Certifique-se de que só é adicionar a rede virtual anterior e que não alterar ou remover quaisquer redes virtuais existentes da sua subscrição.
5. Inicie sessão no Azure para criar a rede virtual (Resource Manager) ao introduzir o `Connect-AzAccount` comando. A conta que iniciar sessão com tem de ter as permissões necessárias para criar um peering de rede virtual. Para obter uma lista de permissões, consulte [permissões de peering de rede Virtual](virtual-network-manage-peering.md#requirements-and-constraints).
6. Crie um grupo de recursos e uma rede virtual (Resource Manager). Copie o script, cole-o no PowerShell e, em seguida, prima `Enter`.

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

7. Crie uma rede virtual peering entre as duas redes virtuais criadas com modelos de implementação diferentes. Copie o seguinte script para um editor de texto no seu PC. Substitua `<subscription id>` com o ID da subscrição. Se não souber o ID de subscrição, introduza o `Get-AzSubscription` comando para exibi-la. O valor para **Id** no resultado retornado é o ID da subscrição. Para executar o script, copie o script modificado do seu editor de texto, então o botão direito do mouse na sua sessão do PowerShell e, em seguida, prima `Enter`.

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Depois do script for executado, reveja o peering da rede virtual (Resource Manager). Copie o seguinte comando, colá-lo na sua sessão do PowerShell e, em seguida, prima `Enter`:

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Mostra a saída **ligado** no **PeeringState** coluna.

    Todos os recursos do Azure que cria em qualquer rede virtual agora são capazes de comunicar entre si através de seus endereços IP. Se estiver a utilizar a resolução de nomes do Azure do padrão para as redes virtuais, os recursos em redes virtuais não são capazes de resolver nomes entre as redes virtuais. Se pretender resolver nomes entre redes virtuais num modo de peering, tem de criar seu próprio servidor DNS. Saiba como configurar [resolução de nomes através de seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

9. **Opcional**: Embora a criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conectividade.
10. **Opcional**: Para eliminar os recursos que criar neste tutorial, conclua os passos em [eliminar recursos](#delete-powershell) neste artigo.

## <a name="delete"></a>Eliminar recursos

Quando tiver concluído este tutorial, pode querer eliminar os recursos que criou no tutorial, pelo que não a incorrer em custos de utilização. Eliminar um grupo de recursos também elimina a todos os recursos que estão no grupo de recursos.

### <a name="delete-portal"></a>Portal do Azure

1. Na caixa de pesquisa de portal, introduza **myResourceGroup**. Nos resultados da pesquisa, clique em **myResourceGroup**.
2. Sobre o **myResourceGroup** painel, clique nas **eliminar** ícone.
3. Para confirmar a eliminação, no **tipo o nome de grupo de recursos** , introduza **myResourceGroup**e, em seguida, clique em **eliminar**.

### <a name="delete-cli"></a>CLI do Azure

1. Utilize a CLI do Azure para eliminar a rede virtual (Resource Manager) com o seguinte comando:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Utilize a CLI clássica para eliminar a rede virtual (clássico) com os seguintes comandos:

    ```azurecli-interactive
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. Introduza o seguinte comando para eliminar a rede virtual (Resource Manager):

    ```powershell
    Remove-AzResourceGroup -Name myResourceGroup -Force
    ```

2. Para eliminar a rede virtual (clássico) com o PowerShell, tem de modificar um ficheiro de configuração de rede existente. Saiba como [exportar, atualizar e importar ficheiros de configuração de rede](virtual-networks-using-network-configuration-file.md). Remova o seguinte elemento de VirtualNetworkSite para a rede virtual utilizado neste tutorial:

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
    > Importar um ficheiro de configuração de rede alterada pode fazer com que as alterações às redes virtuais existentes (clássicas) na sua subscrição. Certifique-se de que apenas remover a rede virtual anterior e que não alterar ou remover quaisquer outras redes virtuais existentes da sua subscrição.

## <a name="next-steps"></a>Passos Seguintes

- Familiarize-se totalmente com importantes [restrições de peering de rede virtual e comportamentos](virtual-network-manage-peering.md#requirements-and-constraints) antes de criar uma rede virtual peering para produção utilizar.
- Saiba tudo sobre [as definições de peering de rede virtual](virtual-network-manage-peering.md#create-a-peering).
- Saiba como [criar um hub- and -spoke topologia de rede](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) com peering de rede virtual.
