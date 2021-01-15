---
title: Criar uma rede virtual Azure a espreitar - diferentes modelos de implementação - mesma subscrição / Microsoft Docs
description: Saiba como criar uma rede virtual de observação entre redes virtuais criadas através de diferentes modelos de implementação Azure que existem na mesma subscrição do Azure.
services: virtual-network
documentationcenter: ''
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 1ff9fcbb693f7e606c07985f9bce9acd60c5591a
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222977"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Criar um espreitamento de rede virtual - diferentes modelos de implementação, mesma subscrição

Neste tutorial, aprende-se a criar uma rede virtual de observação entre redes virtuais criadas através de diferentes modelos de implementação. Ambas as redes virtuais existem na mesma subscrição. Espreitar duas redes virtuais permite que os recursos em diferentes redes virtuais se comuniquem entre si com a mesma largura de banda e latência como se os recursos estivessem na mesma rede virtual. Saiba mais sobre [o espreitamento da rede Virtual.](virtual-network-peering-overview.md)

Os passos para criar um espreguiçadeira de rede virtual são diferentes, dependendo se as redes virtuais estão nas mesmas subscrições, ou diferentes, e que [modelo de implementação Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) as redes virtuais são criadas através. Saiba como criar uma rede virtual a espreitar outros cenários clicando no cenário a partir da tabela seguinte:

|Modelo de implementação do Azure  | Subscrição do Azure  |
|--------- |---------|
|[Ambas com Resource Manager](tutorial-connect-virtual-networks-portal.md) |Mesma|
|[Ambas com Resource Manager](create-peering-different-subscriptions.md) |Diferente|
|[Uma com Resource Manager, outra com clássica](create-peering-different-deployment-models-subscriptions.md) |Diferente|

Não se pode criar um espreitamento de rede virtual entre duas redes virtuais implantadas através do modelo clássico de implementação. Se precisar de ligar redes virtuais que foram ambas criadas através do modelo de implementação clássico, pode utilizar um Gateway Azure [VPN](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para ligar as redes virtuais.

Este tutorial pares redes virtuais na mesma região. Também pode espreitar redes virtuais em [diferentes regiões apoiadas.](virtual-network-manage-peering.md#cross-region) Recomenda-se que se familiarize com os [requisitos e constrangimentos de espreitar](virtual-network-manage-peering.md#requirements-and-constraints) antes de espreitar as redes virtuais.

Pode utilizar o portal Azure, a [interface de linha de comando](#cli) Azure (CLI), Azure [PowerShell,](#powershell)ou um modelo de Gestor de Recursos Azure para criar um esprevamento de rede virtual. Clique em qualquer uma das ligações de ferramentas anteriores para ir diretamente aos passos para criar uma rede virtual que espreita usando a sua ferramenta de eleição.

## <a name="create-peering---azure-portal"></a>Criar olhando - Portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com). A conta com que iniciar sessão deve ter as permissões necessárias para criar uma rede virtual de observação. Para obter uma lista de permissões, consulte [permissões de espreitar rede virtual](virtual-network-manage-peering.md#requirements-and-constraints).
2. Clique **em + Novo,** clique **em Rede** e, em seguida, clique na rede **Virtual**.
3. Na lâmina **de rede virtual Criar,** introduzir ou selecionar valores para as seguintes definições e, em seguida, clicar em **Criar**:
    - **Nome**: *myVnet1*
    - **Espaço de** endereço : *10.0.0.0/16*
    - **Nome da sub-rede**: *predefinição*
    - **Intervalo de endereços da sub-rede**: *10.0.0.0/24*
    - **Subscrição**: Selecione a sua subscrição
    - **Grupo de recursos**: Selecione **Criar novo** e insira *o myResourceGroup*
    - **Localização**: *Leste dos EUA*
4. Clique em **+ Novo**. Na caixa **'Pesquisar o Mercado',** *digite rede Virtual.* Clique **na rede Virtual** quando aparecer nos resultados da pesquisa.
5. Na lâmina **de rede Virtual,** selecione **Classic** na caixa de modelo **de implementação** e, em seguida, clique em **Criar**.
6. Na lâmina **de rede virtual Criar,** introduzir ou selecionar valores para as seguintes definições e, em seguida, clicar em **Criar**:
    - **Nome**: *myVnet2*
    - **Espaço de** endereço : *10.1.0.0/16*
    - **Nome da sub-rede**: *predefinição*
    - **Intervalo de endereços da sub-rede**: *10.1.0.0/24*
    - **Subscrição**: Selecione a sua subscrição
    - **Grupo de recursos**: Selecione **Utilize o** *myResourceGroup*
    - **Localização**: *Leste dos EUA*
7. Na caixa **de recursos de busca** no topo do portal, digite o *myResourceGroup*. Clique **no myResourceGroup** quando aparecer nos resultados da pesquisa. Aparece uma lâmina para o grupo de recursos **myresourcegroup.** O grupo de recursos detém as duas redes virtuais criadas em etapas anteriores.
8. Clique **no myVNet1**.
9. Na lâmina **myVnet1** que aparece, clique **em Peerings** a partir da lista vertical de opções no lado esquerdo da lâmina.
10. No **myVnet1 - Lâmina de peerings** que apareceu, clique **+ Adicionar**
11. Na lâmina **de espreitar** que aparece, introduza ou selecione as seguintes opções e, em seguida, clique **em OK**:
     - **Nome**: *myVnet1ToMyVnet2*
     - **Modelo de implementação de rede virtual**: Selecione **Classic**.
     - **Subscrição**: Selecione a sua subscrição
     - **Rede virtual**: Clique **Em Escolher uma rede virtual** e, em seguida, clique em **myVnet2**.
     - **Permitir o acesso à rede virtual:** Certifique-se de que **o Enabled** está selecionado.
    Não são utilizadas outras definições neste tutorial. Para saber mais sobre todas as definições de observação, leia [Gerir os olhos de rede virtuais](virtual-network-manage-peering.md#create-a-peering).
12. Depois de clicar **em OK** no passo anterior, a lâmina **de espreitar** fecha-se e vê novamente a **lâmina myVnet1 - Peerings.** Após alguns segundos, o espreitamento que criou aparece na lâmina. **Connected** está listado na coluna **PEERING STATUS** para o **myVnet1ToMyVnet2** que o seu cria.

    O espreitamento está agora estabelecido. Quaisquer recursos Azure que crie em qualquer rede virtual são agora capazes de comunicar uns com os outros através dos seus endereços IP. Se estiver a utilizar a resolução de nomeS Azure padrão para as redes virtuais, os recursos nas redes virtuais não são capazes de resolver nomes através das redes virtuais. Se quiser resolver nomes através de redes virtuais num espreitê-lo, tem de criar o seu próprio servidor DNS. Saiba como configurar a [resolução Name utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
13. **Opcional**: Embora a criação de máquinas virtuais não esteja abrangida por este tutorial, pode criar uma máquina virtual em cada rede virtual e ligar-se de uma máquina virtual à outra, para validar a conectividade.
14. **Opcional**: Para eliminar os recursos que cria neste tutorial, complete os passos na secção [de recursos de Eliminar](#delete-portal) deste artigo.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Criar olhando - Azure CLI

Complete os seguintes passos utilizando o CLI clássico do Azure e o CLI Azure. Pode completar os passos a partir do Azure Cloud Shell, selecionando apenas o botão **Try it** em qualquer um dos passos seguintes, ou instalando o CLI e [CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) [clássicos](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) e executando os comandos no seu computador local.

1. Se utilizar a Cloud Shell, salte para o passo 2, porque a Cloud Shell automaticamente assina-o no Azure. Abra uma sessão de comando e inscreva-se em Azure usando o `azure login` comando.
2. Executar o CLI no modo de Gestão de Serviços inserindo o `azure config mode asm` comando.
3. Introduza o seguinte comando para criar a rede virtual (clássico):

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. Execute o seguinte guião CLI de bash usando o CLI, não o CLI clássico. Para obter opções sobre a execução de scripts CLI bash no computador Windows, consulte [instalar o CLI Azure no Windows](/cli/azure/install-azure-cli-windows).

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

5. Crie uma rede virtual de observação entre as duas redes virtuais criadas através dos diferentes modelos de implementação utilizando o CLI. Copie o seguinte script para um editor de texto no seu PC. `<subscription id>`Substitua-o pelo ID de subscrição. Se não conhece o seu ID de subscrição, insira o `az account show` comando. O valor para **id** na saída é o seu ID de subscrição. Cole o script modificado na sua sessão de CLI e, em seguida, prima `Enter` .

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

6. Após a execução do script, reveja o espreitamento da rede virtual (Gestor de Recursos). Copie o seguinte comando, cole-o na sua sessão de CLI e, em seguida, `Enter` prima:

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   A saída mostra **conectado** na coluna **PeeringState.**

   Quaisquer recursos Azure que crie em qualquer rede virtual são agora capazes de comunicar uns com os outros através dos seus endereços IP. Se estiver a utilizar a resolução de nomeS Azure padrão para as redes virtuais, os recursos nas redes virtuais não são capazes de resolver nomes através das redes virtuais. Se quiser resolver nomes através de redes virtuais num espreitê-lo, tem de criar o seu próprio servidor DNS. Saiba como configurar a [resolução Name utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
7. **Opcional**: Embora a criação de máquinas virtuais não esteja abrangida por este tutorial, pode criar uma máquina virtual em cada rede virtual e ligar-se de uma máquina virtual à outra, para validar a conectividade.
8. **Opcional**: Para eliminar os recursos que cria neste tutorial, complete os passos em [Eliminar recursos](#delete-cli) neste artigo.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Criar olhando - PowerShell

1. Instale a versão mais recente dos módulos PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) e [Az.](https://www.powershellgallery.com/packages/Az/) Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão PowerShell.
3. Em PowerShell, inscreva-se em Azure entrando no `Add-AzureAccount` comando. A conta com que iniciar sessão deve ter as permissões necessárias para criar uma rede virtual de observação. Para obter uma lista de permissões, consulte [permissões de espreitar rede virtual](virtual-network-manage-peering.md#requirements-and-constraints).
4. Para criar uma rede virtual (clássica) com o PowerShell, tem de criar um novo, ou modificar um ficheiro de configuração de rede existente. Saiba como [exportar, atualizar e importar ficheiros de configuração da rede](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file). O ficheiro deve incluir o seguinte elemento **VirtualNetworkSite** para a rede virtual utilizada neste tutorial:

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
    > Importar um ficheiro de configuração de rede alterado pode causar alterações nas redes virtuais existentes (clássicas) na sua subscrição. Certifique-se de que apenas adiciona a rede virtual anterior e que não altera ou remove quaisquer redes virtuais existentes da sua subscrição.
5. Inscreva-se no Azure para criar a rede virtual (Gestor de Recursos) inserindo o `Connect-AzAccount` comando. A conta com que iniciar sessão deve ter as permissões necessárias para criar uma rede virtual de observação. Para obter uma lista de permissões, consulte [permissões de espreitar rede virtual](virtual-network-manage-peering.md#requirements-and-constraints).
6. Criar um grupo de recursos e uma rede virtual (Gestor de Recursos). Copie o guião, cole-o em PowerShell e, em seguida, prima `Enter` .

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

7. Crie uma rede virtual de observação entre as duas redes virtuais criadas através dos diferentes modelos de implementação. Copie o seguinte script para um editor de texto no seu PC. `<subscription id>`Substitua-o pelo ID de subscrição. Se não conhece o ID da subscrição, insira o `Get-AzSubscription` comando para o visualizar. O valor **para id** na saída devolvida é o seu ID de subscrição. Para executar o script, copie o script modificado do seu editor de texto, clique com o botão direito na sessão PowerShell e, em seguida, prima `Enter` .

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Após a execução do script, reveja o espreitamento da rede virtual (Gestor de Recursos). Copie o seguinte comando, cole-o na sessão PowerShell e, em seguida, `Enter` prima:

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    A saída mostra **conectado** na coluna **PeeringState.**

    Quaisquer recursos Azure que crie em qualquer rede virtual são agora capazes de comunicar uns com os outros através dos seus endereços IP. Se estiver a utilizar a resolução de nomeS Azure padrão para as redes virtuais, os recursos nas redes virtuais não são capazes de resolver nomes através das redes virtuais. Se quiser resolver nomes através de redes virtuais num espreitê-lo, tem de criar o seu próprio servidor DNS. Saiba como configurar a [resolução Name utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

9. **Opcional**: Embora a criação de máquinas virtuais não esteja abrangida por este tutorial, pode criar uma máquina virtual em cada rede virtual e ligar-se de uma máquina virtual à outra, para validar a conectividade.
10. **Opcional**: Para eliminar os recursos que cria neste tutorial, complete os passos em [Eliminar recursos](#delete-powershell) neste artigo.

## <a name="delete-resources"></a><a name="delete"></a>Eliminar recursos

Quando terminar este tutorial, talvez queira apagar os recursos que criou no tutorial, para não incorrer em taxas de utilização. A eliminação de um grupo de recursos também elimina todos os recursos que estão no grupo de recursos.

### <a name="azure-portal"></a><a name="delete-portal"></a>Portal do Azure

1. Na caixa de pesquisa do portal, insira **o myResourceGroup**. Nos resultados da pesquisa, clique **no myResourceGroup**.
2. Na lâmina **myResourceGroup,** clique no ícone **Eliminar.**
3. Para confirmar a eliminação, na caixa **TYPE THE RESOURCE GROUP NAME,** introduza o **myResourceGroup** e, em seguida, clique em **Eliminar**.

### <a name="azure-cli"></a><a name="delete-cli"></a>CLI do Azure

1. Utilize o CLI Azure para eliminar a rede virtual (Gestor de Recursos) com o seguinte comando:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Utilize o CLI clássico para eliminar a rede virtual (clássica) com os seguintes comandos:

    ```azurecli-interactive
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. Introduza o seguinte comando para eliminar a rede virtual (Gestor de Recursos):

    ```powershell
    Remove-AzResourceGroup -Name myResourceGroup -Force
    ```

2. Para eliminar a rede virtual (clássica) com o PowerShell, tem de modificar um ficheiro de configuração de rede existente. Saiba como [exportar, atualizar e importar ficheiros de configuração da rede](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file). Remova o seguinte elemento VirtualNetworkSite para a rede virtual utilizada neste tutorial:

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
    > Importar um ficheiro de configuração de rede alterado pode causar alterações nas redes virtuais existentes (clássicas) na sua subscrição. Certifique-se de que remove apenas a rede virtual anterior e que não altera ou remove quaisquer outras redes virtuais existentes da sua subscrição.

## <a name="next-steps"></a>Passos seguintes

- Familiarize-se completamente com [importantes restrições e comportamentos de observação de rede virtuais](virtual-network-manage-peering.md#requirements-and-constraints) antes de criar uma rede virtual que espreita para uso de produção.
- Saiba mais sobre todas as [definições de observação de rede virtual](virtual-network-manage-peering.md#create-a-peering).
- Aprenda a [criar um hub e falou topologia de rede](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) com olhando a rede virtual.