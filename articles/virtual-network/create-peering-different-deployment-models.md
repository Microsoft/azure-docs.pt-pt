---
title: Criar uma rede virtual Azure a espreitar - diferentes modelos de implementação - a mesma subscrição [ Microsoft Docs
description: Saiba como criar uma rede virtual de peering entre redes virtuais criadas através de diferentes modelos de implementação Azure que existem na mesma subscrição azure.
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
ms.openlocfilehash: 61df13e78dc7115d4f4d45ab18b9ffdae107dc96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023264"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Criar um peering de rede virtual - diferentes modelos de implementação, mesma subscrição

Neste tutorial, aprende-se a criar uma rede virtual de observação entre redes virtuais criadas através de diferentes modelos de implementação. Ambas as redes virtuais existem na mesma subscrição. Espreitar duas redes virtuais permite que os recursos em diferentes redes virtuais se comuniquem entre si com a mesma largura de banda e latência como se os recursos estivessem na mesma rede virtual. Saiba mais sobre [o peering virtual da rede.](virtual-network-peering-overview.md)

Os passos para criar um peering de rede virtual são diferentes, dependendo se as redes virtuais estão nas mesmas, ou diferentes, subscrições, e em que modelo de [implementação azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) as redes virtuais são criadas através. Saiba como criar uma rede virtual a espreitar noutros cenários clicando no cenário a partir da tabela seguinte:

|Modelo de implementação do Azure  | Subscrição do Azure  |
|--------- |---------|
|[Ambas com Resource Manager](tutorial-connect-virtual-networks-portal.md) |Mesma|
|[Ambas com Resource Manager](create-peering-different-subscriptions.md) |Diferente|
|[Uma com Resource Manager, outra com clássica](create-peering-different-deployment-models-subscriptions.md) |Diferente|

Não é possível criar um epeering de rede virtual entre duas redes virtuais implementadas através do modelo clássico de implementação. Se precisar de ligar redes virtuais que foram criadas através do modelo de implementação clássico, pode utilizar um [Azure VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para ligar as redes virtuais.

Este tutorial identifica redes virtuais na mesma região. Você também pode peer redes virtuais em [diferentes regiões apoiadas](virtual-network-manage-peering.md#cross-region). Recomenda-se que se familiarize com os [requisitos e constrangimentos](virtual-network-manage-peering.md#requirements-and-constraints) de observação antes de espreitar redes virtuais.

Pode utilizar o portal Azure, a [interface de linha de comando](#cli) Azure (CLI), o Azure [PowerShell](#powershell)ou um modelo de Gestor de Recursos Azure para criar um pinvo de rede virtual. Clique em qualquer uma das ligações de ferramentas anteriores para ir diretamente aos passos para criar um peering de rede virtual usando a sua ferramenta de eleição.

## <a name="create-peering---azure-portal"></a>Criar o peering - Portal Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). A conta com a sua inscrição deve ter as permissões necessárias para criar um epeering de rede virtual. Para obter uma lista de permissões, consulte [permissões de verificação](virtual-network-manage-peering.md#requirements-and-constraints)de rede virtual .
2. Clique **+ Novo,** clique em **Rede,** em seguida, clique em **rede virtual**.
3. Na lâmina de **rede virtual Criar,** introduzir ou selecionar valores para as seguintes definições, clique em **Criar:**
    - **Nome**: *myVnet1*
    - **Espaço**de endereço : *10.0.0.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - Gama de **endereços de sub-rede**: *10.0.0.0/24*
    - **Subscrição**: Selecione a sua subscrição
    - **Grupo de recursos**: Selecione **Criar novo** e insira *o myResourceGroup*
    - **Localização**: *Leste dos EUA*
4. Clique em **+ Novo**. Na caixa **De pesquisa do Mercado,** digite *a rede Virtual.* Clique na **rede Virtual** quando aparecer nos resultados da pesquisa.
5. Na lâmina de **rede Virtual,** selecione **Classic** na caixa de **modelo seletiva** e, em seguida, clique em **Criar**.
6. Na lâmina de **rede virtual Criar,** introduzir ou selecionar valores para as seguintes definições, clique em **Criar:**
    - **Nome**: *myVnet2*
    - **Espaço**de endereço : *10.1.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - Gama de **endereços de sub-rede**: *10.1.0.0/24*
    - **Subscrição**: Selecione a sua subscrição
    - **Grupo de recursos**: Selecione **Use existente** e selecione *myResourceGroup*
    - **Localização**: *Leste dos EUA*
7. Na caixa de **recursos de pesquisa** no topo do portal, digite o *myResourceGroup*. Clique no **myResourceGroup** quando aparecer nos resultados da pesquisa. Uma lâmina aparece para o grupo de recursos **myresourcegroup.** O grupo de recursos detém as duas redes virtuais criadas em etapas anteriores.
8. Clique em **myVNet1**.
9. Na lâmina **myVnet1** que aparece, clique em **Peerings** a partir da lista vertical de opções no lado esquerdo da lâmina.
10. Na **lâmina myVnet1 - Peerings** que apareceu, clique **+ Adicionar**
11. Na lâmina **de peering Adicionar** que aparece, introduza ou selecione as seguintes opções, em seguida, clique **OK**:
     - **Nome**: *myVnet1ToMyVnet2*
     - **Modelo de implementação de rede virtual**: Select **Classic**.
     - **Subscrição**: Selecione a sua subscrição
     - **Rede virtual**: Clique **em escolher uma rede virtual**e, em seguida, clique em **myVnet2**.
     - **Permitir o acesso virtual à rede:** Certifique-se de que o **Ativado** está selecionado.
    Não são utilizadas outras definições neste tutorial. Para aprender sobre todas as configurações de peering, leia [Gerir os pares](virtual-network-manage-peering.md#create-a-peering)de rede virtual .
12. Depois de clicar **OK** no passo anterior, a lâmina **de observação Add** fecha-se e vê novamente a lâmina **myVnet1 - Peerings.** Após alguns segundos, o olhar que criou aparece na lâmina. **Connected** está listado na coluna **PEERING STATUS** para o **minet1ToMyVnet2** que criou.

    O olhar está agora estabelecido. Quaisquer recursos Azure que crie em qualquer rede virtual são agora capazes de comunicar uns com os outros através dos seus endereços IP. Se estiver a utilizar a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não são capazes de resolver nomes através das redes virtuais. Se quiser resolver nomes através de redes virtuais num epeering, tem de criar o seu próprio servidor DNS. Aprenda a configurar a resolução de [nome utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
13. **Opcional**: Embora a criação de máquinas virtuais não esteja coberta neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar-se de uma máquina virtual à outra, para validar a conectividade.
14. **Opcional**: Para eliminar os recursos que cria neste tutorial, preencha os passos na secção de [recursos Eliminar](#delete-portal) deste artigo.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Criar o peering - Azure CLI

Complete os seguintes passos utilizando o clássico CLI azure e o Azure CLI. Pode completar os passos da Casca de Nuvem Azure, selecionando apenas o botão **Experimente** em qualquer um dos seguintes passos, ou instalando o CLI e [clI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) [clássicos](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) e executando os comandos no seu computador local.

1. Se utilizar a Cloud Shell, salte para o passo 2, porque a Cloud Shell automaticamente o assina no Azure. Abra uma sessão de comando e `azure login` inscreva-se no Azure usando o comando.
2. Executar o CLI no modo de `azure config mode asm` Gestão de Serviços, entrando no comando.
3. Insira o seguinte comando para criar a rede virtual (clássica):

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. Execute o seguinte script CLI bash usando o CLI, não o CLÁSSICO CLI. Para opções sobre a execução de scripts CLI de bash no computador Windows, consulte [Instalar o CLI Azure no Windows](/cli/azure/install-azure-cli-windows).

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

5. Crie uma rede virtual de observação entre as duas redes virtuais criadas através dos diferentes modelos de implementação utilizando o CLI. Copie o seguinte guião para um editor de texto no seu PC. Substitua-a `<subscription id>` pelo seu ID de subscrição. Se não sabe o seu ID `az account show` de subscrição, insira o comando. O valor para **id** na saída é o seu ID de subscrição. Cola o script modificado na tua sessão CLI e, em seguida, pressione `Enter`.

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

6. Após a execução do script, reveja o epeering para a rede virtual (Gestor de Recursos). Copie o seguinte comando, cole-o na sua `Enter`sessão CLI e, em seguida, prima:

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   A saída mostra **ligado** na coluna **PeeringState.**

   Quaisquer recursos Azure que crie em qualquer rede virtual são agora capazes de comunicar uns com os outros através dos seus endereços IP. Se estiver a utilizar a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não são capazes de resolver nomes através das redes virtuais. Se quiser resolver nomes através de redes virtuais num epeering, tem de criar o seu próprio servidor DNS. Aprenda a configurar a resolução de [nome utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
7. **Opcional**: Embora a criação de máquinas virtuais não esteja coberta neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar-se de uma máquina virtual à outra, para validar a conectividade.
8. **Opcional**: Para eliminar os recursos que cria neste tutorial, complete os passos em [Eliminar os recursos](#delete-cli) neste artigo.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Criar o peering - PowerShell

1. Instale a versão mais recente dos módulos PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) e [Az.](https://www.powershellgallery.com/packages/Az/) Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão powerShell.
3. Na PowerShell, inscreva-se no Azure entrando no `Add-AzureAccount` comando. A conta com a sua inscrição deve ter as permissões necessárias para criar um epeering de rede virtual. Para obter uma lista de permissões, consulte [permissões de verificação](virtual-network-manage-peering.md#requirements-and-constraints)de rede virtual .
4. Para criar uma rede virtual (clássica) com o PowerShell, tem de criar um novo, ou modificar um ficheiro de configuração de rede existente. Saiba como [exportar, atualizar e importar ficheiros de configuração da rede.](virtual-networks-using-network-configuration-file.md) O ficheiro deve incluir o seguinte elemento **VirtualNetworkSite** para a rede virtual utilizada neste tutorial:

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
5. Inscreva-se no Azure para criar a rede virtual `Connect-AzAccount` (Gestor de Recursos) entrando no comando. A conta com a sua inscrição deve ter as permissões necessárias para criar um epeering de rede virtual. Para obter uma lista de permissões, consulte [permissões de verificação](virtual-network-manage-peering.md#requirements-and-constraints)de rede virtual .
6. Criar um grupo de recursos e uma rede virtual (Gestor de Recursos). Copie o script, cole-o no `Enter`PowerShell e, em seguida, pressione .

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

7. Criar uma rede virtual de observação entre as duas redes virtuais criadas através dos diferentes modelos de implementação. Copie o seguinte guião para um editor de texto no seu PC. Substitua-a `<subscription id>` pelo seu ID de subscrição. Se não sabe o seu ID `Get-AzSubscription` de subscrição, insira o comando para vê-lo. O valor para **Id** na saída devolvida é o seu ID de subscrição. Para executar o script, copie o script modificado do seu editor de texto `Enter`e, em seguida, clique à direita na sua sessão PowerShell e, em seguida, prima .

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Após a execução do script, reveja o epeering para a rede virtual (Gestor de Recursos). Copie o seguinte comando, cole-o na sua `Enter`sessão PowerShell e, em seguida, prima:

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    A saída mostra **ligado** na coluna **PeeringState.**

    Quaisquer recursos Azure que crie em qualquer rede virtual são agora capazes de comunicar uns com os outros através dos seus endereços IP. Se estiver a utilizar a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não são capazes de resolver nomes através das redes virtuais. Se quiser resolver nomes através de redes virtuais num epeering, tem de criar o seu próprio servidor DNS. Aprenda a configurar a resolução de [nome utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

9. **Opcional**: Embora a criação de máquinas virtuais não esteja coberta neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar-se de uma máquina virtual à outra, para validar a conectividade.
10. **Opcional**: Para eliminar os recursos que cria neste tutorial, complete os passos em [Eliminar os recursos](#delete-powershell) neste artigo.

## <a name="delete-resources"></a><a name="delete"></a>Eliminar recursos

Quando terminar este tutorial, talvez queira apagar os recursos que criou no tutorial, para que não incorra em taxas de utilização. A eliminação de um grupo de recursos também elimina todos os recursos que estão no grupo de recursos.

### <a name="azure-portal"></a><a name="delete-portal"></a>Portal Azure

1. Na caixa de pesquisa do portal, introduza **o myResourceGroup**. Nos resultados da pesquisa, clique no **myResourceGroup**.
2. Na lâmina **myResourceGroup,** clique no ícone **Delete.**
3. Para confirmar a eliminação, na caixa **TYPE The RESOURCE GROUP NAME,** introduza o **myResourceGroup**, e, em seguida, clique **em Apagar**.

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. Utilize o Azure CLI para eliminar a rede virtual (Gestor de Recursos) com o seguinte comando:

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

2. Para eliminar a rede virtual (clássica) com o PowerShell, tem de modificar um ficheiro de configuração de rede existente. Saiba como [exportar, atualizar e importar ficheiros de configuração da rede.](virtual-networks-using-network-configuration-file.md) Remova o seguinte elemento VirtualNetworkSite para a rede virtual utilizada neste tutorial:

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
    > Importar um ficheiro de configuração de rede alterado pode causar alterações nas redes virtuais existentes (clássicas) na sua subscrição. Certifique-se de que apenas remove a rede virtual anterior e que não altera ou remove quaisquer outras redes virtuais existentes da sua subscrição.

## <a name="next-steps"></a>Passos seguintes

- Familiarize-se completamente com [importantes restrições e comportamentos](virtual-network-manage-peering.md#requirements-and-constraints) de rede virtual antes de criar uma rede virtual que procura o uso da produção.
- Conheça todas as [definições de peering](virtual-network-manage-peering.md#create-a-peering)de rede virtual .
- Aprenda a criar um hub e falou de [topologia](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) de rede com o peering virtual da rede.
