---
title: Criar um peering de rede virtual do Azure - modelos de implementação diferentes - diferentes subscrições
titlesuffix: Azure Virtual Network
description: Saiba como criar uma rede virtual peering entre redes virtuais criadas com modelos de implementação do Azure diferente que existam em diferentes subscrições do Azure.
services: virtual-network
documentationcenter: ''
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: jdial;anavin
ms.openlocfilehash: cd0493d4fd126175acafce050394e65a47e9bda9
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56650024"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Criar um peering de rede virtual - diferentes modelos de implementação e as subscrições

Neste tutorial, vai aprender a criar uma rede virtual peering entre redes virtuais criadas com modelos de implementação diferentes. Existem as redes virtuais em diferentes subscrições. Peering dois recursos de permite de redes virtuais em redes virtuais diferentes para comunicar entre si com o mesmo da largura de banda e latência, como se os recursos estivessem na mesma rede virtual. Saiba mais sobre [peering de rede Virtual](virtual-network-peering-overview.md).

Os passos para criar um peering de rede virtual são diferentes, dependendo se as redes virtuais estão no mesmo, ou em diferentes, subscrições e qual [modelo de implementação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) as redes virtuais forem criadas com. Saiba como criar uma rede virtual peering em outros cenários ao clicar o cenário da tabela seguinte:

|Modelo de implementação do Azure  | Subscrição do Azure  |
|--------- |---------|
|[Ambas com Resource Manager](tutorial-connect-virtual-networks-portal.md) |Mesmo|
|[Ambas com Resource Manager](create-peering-different-subscriptions.md) |Diferente|
|[Uma com Resource Manager, outra com clássica](create-peering-different-deployment-models.md) |Mesmo|

Não é possível criar um peering de rede virtual entre duas redes virtuais implementadas através do modelo de implementação clássica. Este tutorial utiliza as redes virtuais existentes na mesma região. Este tutorial elementos redes virtuais na mesma região. Pode também configurar o peering entre redes virtuais em diferentes [regiões suportadas](virtual-network-manage-peering.md#cross-region). É recomendável que se familiarizar com o [peering requisitos e restrições](virtual-network-manage-peering.md#requirements-and-constraints) antes peering de redes virtuais.

Ao criar uma rede virtual peering entre redes virtuais que existam em subscrições diferentes, as subscrições têm ambos de estar associadas ao mesmo inquilino do Azure Active Directory. Se ainda não tiver um inquilino do Azure Active Directory, pode rapidamente [criá-lo](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant). Pode ligar redes virtuais em subscrições diferentes e diferentes do Azure Active Directory, inquilinos através do Azure [Gateway de VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Pode utilizar o [portal do Azure](#portal), o Azure [interface de linha de comandos](#cli) (CLI), ou do Azure [PowerShell](#powershell) para criar um peering de rede virtual. Clique em qualquer uma das ligações de ferramenta anterior para ir diretamente para os passos para criar um peering de rede virtual com a sua ferramenta preferencial.

## <a name="portal"></a>Criar peering - portal do Azure

Este tutorial utiliza contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todos os passos, ignore os passos para o registo de fora do portal do e ignore os passos para atribuir permissões de outro utilizador para as redes virtuais.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como UserA. A conta, que inicie sessão com tem de ter as permissões necessárias para criar um peering de rede virtual. Para obter uma lista de permissões, consulte [permissões de peering de rede Virtual](virtual-network-manage-peering.md#permissions).
2. Clique em **+ novo**, clique em **redes**, em seguida, clique em **rede Virtual**.
3. Na **criar rede virtual** painel, introduza ou selecione os valores para as seguintes definições e clique em **criar**:
    - **Name**: *myVnetA*
    - **Espaço de endereços**: *10.0.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - **Intervalo de endereços da sub-rede**: *10.0.0.0/24*
    - **Subscrição**: Selecione a subscrição A.
    - **Grupo de recursos**: Selecione **criar novo** e introduza *myResourceGroupA*
    - **Localização**: *E.U.A. leste*
4. Na **recursos de pesquisa** caixa na parte superior do portal, tipo *myVnetA*. Clique em **myVnetA** quando for apresentada nos resultados da pesquisa. É apresentado um painel para o **myVnetA** rede virtual.
5. Na **myVnetA** painel que aparece, clique em **controlo de acesso (IAM)** na vertical lista de opções no lado esquerdo do painel.
6. Na **myVnetA - controlo de acesso (IAM)** painel que aparece, clique em **+ adicionar atribuição de função**.
7. Na **adicionar atribuição de função** painel que aparece, selecione **Contribuidor de rede** no **função** caixa.
8. Na **selecione** caixa, selecione o utilizador b ou escreva o endereço de e-mail do User-b para pesquisá-la. A lista de utilizadores mostrado é a partir do mesmo inquilino do Azure Active Directory que a rede virtual que está a configurar o peering para. Clique em UserB quando aparece na lista.
9. Clique em **Guardar**.
10. Terminar sessão no portal como UserA, em seguida, inicie sessão como utilizador b.
11. Clique em **+ novo**, tipo *rede Virtual* no **procurar no Marketplace** caixa, em seguida, clique em **rede Virtual** nos resultados da pesquisa.
12. Na **rede Virtual** painel que aparece, selecione **clássico** no **selecionar um modelo de implementação** caixa, em seguida, clique em **criar**.
13.   Na criar rede virtual (clássica) caixa que aparece, introduza os seguintes valores:

    - **Name**: *myVnetB*
    - **Espaço de endereços**: *10.1.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - **Intervalo de endereços da sub-rede**: *10.1.0.0/24*
    - **Subscrição**: Selecione a subscrição B.
    - **Grupo de recursos**: Selecione **criar novo** e introduza *myResourceGroupB*
    - **Localização**: *E.U.A. leste*

14. Na **recursos de pesquisa** caixa na parte superior do portal, tipo *myVnetB*. Clique em **myVnetB** quando for apresentada nos resultados da pesquisa. É apresentado um painel para o **myVnetB** rede virtual.
15. Na **myVnetB** painel que aparece, clique em **propriedades** na vertical lista de opções no lado esquerdo do painel. Copiar o **ID de recurso**, que é utilizado num passo posterior. O ID de recurso é semelhante ao seguinte exemplo: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
16. Conclua os passos 5 a 9 para myVnetB, introduzir **UserA** no passo 8.
17. Terminar sessão no portal como UserB e iniciar sessão como utilizador.
18. Na **recursos de pesquisa** caixa na parte superior do portal, tipo *myVnetA*. Clique em **myVnetA** quando for apresentada nos resultados da pesquisa. É apresentado um painel para o **myVnet** rede virtual.
19. Clique em **myVnetA**.
20. Na **myVnetA** painel que aparece, clique em **Peerings** na vertical lista de opções no lado esquerdo do painel.
21. Na **myVnetA - Peerings** painel que será exibida, clique em **+ adicionar**
22. Na **adicionar peering** painel apresentado, introduza ou selecione as seguintes opções e clique em **OK**:
     - **Name**: *myVnetAToMyVnetB*
     - **Modelo de implementação de rede virtual**:  Selecione **clássico**.
     - **Eu sei o ID de recurso**: Esta caixa de verificação.
     - **ID de recurso**: Introduza o ID de recurso de myVnetB do passo 15.
     - **Permitir o acesso de rede virtual:** Certifique-se de que **ativado** está selecionada.
    Não existem outras definições são utilizadas neste tutorial. Para saber mais sobre todas as definições de peering, leia [gerir peerings de rede virtual](virtual-network-manage-peering.md#create-a-peering).
23. Depois de clicar em **OK** no passo anterior, o **adicionar peering** painel fecha-se e ver o **myVnetA - Peerings** painel novamente. Após alguns segundos, o peering que criou é apresentada no painel. **Ligado** é listado na **estado de PEERING** coluna para o **myVnetAToMyVnetB** peering é criado. Agora é estabelecido o peering. Não é necessário configurar o peering entre a rede virtual (clássico) para a rede virtual (Resource Manager).

    Todos os recursos do Azure que cria em qualquer rede virtual agora são capazes de comunicar entre si através de seus endereços IP. Se estiver a utilizar a resolução de nomes do Azure do padrão para as redes virtuais, os recursos as redes virtuais não são capazes de resolver nomes entre as redes virtuais. Se pretender resolver nomes entre redes virtuais num modo de peering, tem de criar seu próprio servidor DNS. Saiba como configurar [resolução de nomes através de seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

24. **Opcional**: Embora a criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conectividade.
25. **Opcional**: Para eliminar os recursos que criar neste tutorial, conclua os passos a [eliminar recursos](#delete-portal) seção deste artigo.

## <a name="cli"></a>Criar peering - CLI do Azure

Este tutorial utiliza contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todos os passos, ignore os passos para o registo do Azure e remover as linhas do script que criar atribuições de funções de utilizador. Substitua UserA@azure.com e UserB@azure.com em todos os scripts seguintes com os nomes de utilizador estiver a utilizar para UserA e UserB. Conclua os passos seguintes a utilizar a CLI clássica do Azure e a CLI do Azure. Pode seguir os passos do Azure Cloud Shell, ao selecionar apenas os **experimentá-la** botão em qualquer um dos seguintes passos ou instalando o [CLI clássica](/cli/azure/install-classic-cli) e [CLI](/cli/azure/install-azure-cli) e executar os comandos no seu computador local.

1. Se utilizar o Cloud Shell, avance para o passo 2, uma vez que o Cloud Shell automaticamente iniciada no Azure. Abra uma sessão de comando e inicie sessão no Azure com o `azure login` comando.
2. Executar a CLI clássica no modo de gestão de serviço ao introduzir o `azure config mode asm` comando.
3. Introduza o seguinte comando da CLI clássico para criar a rede virtual (clássica):

    ```azurecli
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```
4. Os passos restantes devem ser concluídos com uma shell bash com a CLI do Azure (não a CLI clássica).
5. Copie o seguinte script para um editor de texto no seu PC. Substitua `<SubscriptionB-Id>` com o ID da subscrição. Se não souber o Id de subscrição, introduza o `az account show` comando. O valor para **id** no resultado é o seu ID de subscrição. Copie o script modificado, cole-a à sua sessão CLI e, em seguida, prima `Enter`.

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    Quando criou a rede virtual (clássico) no passo 4, o Azure criou a rede virtual no *sistema de rede padrão* grupo de recursos.
6. Inicie sessão UserB fora do Azure e o início de sessão como UserA na CLI.
7. Crie um grupo de recursos e uma rede virtual (Resource Manager). Copie o seguinte script, cole-a à sua sessão CLI e, em seguida, prima `Enter`.

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroupA"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network A (Resource Manager).
    az network vnet create \
      --name myVnetA \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Get the id for myVnetA.
    vNetAId=$(az network vnet show \
      --resource-group $rgName \
      --name myVnetA \
      --query id --out tsv)

    # Assign UserB permissions to myVnetA.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope $vNetAId
    ```

8. Crie uma rede virtual peering entre as duas redes virtuais criadas com modelos de implementação diferentes. Copie o seguinte script para um editor de texto no seu PC. Substitua `<SubscriptionB-id>` com o ID de subscrição. Se não souber o Id de subscrição, introduza o `az account show` comando. O valor para **id** no resultado é o seu ID de subscrição. Azure criou a rede virtual (clássico) que criou no passo 4 num grupo de recursos com o nome *sistema de rede padrão*. Cole o script modificado na sua sessão CLI e, em seguida, prima `Enter`.

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

9. Depois do script for executado, reveja o peering da rede virtual (Resource Manager). Copie o seguinte script e, em seguida, cole-o na sua sessão CLI:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```
    Mostra a saída **ligado** no **PeeringState** coluna.

    Todos os recursos do Azure que cria em qualquer rede virtual agora são capazes de comunicar entre si através de seus endereços IP. Se estiver a utilizar a resolução de nomes do Azure do padrão para as redes virtuais, os recursos as redes virtuais não são capazes de resolver nomes entre as redes virtuais. Se pretender resolver nomes entre redes virtuais num modo de peering, tem de criar seu próprio servidor DNS. Saiba como configurar [resolução de nomes através de seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

10. **Opcional**: Embora a criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conectividade.
11. **Opcional**: Para eliminar os recursos que criar neste tutorial, conclua os passos em [eliminar recursos](#delete-cli) neste artigo.

## <a name="powershell"></a>Criar peering - PowerShell

Este tutorial utiliza contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todos os passos, ignore os passos para o registo do Azure e remover as linhas do script que criar atribuições de funções de utilizador. Substitua UserA@azure.com e UserB@azure.com em todos os scripts seguintes com os nomes de utilizador estiver a utilizar para UserA e UserB. 

1. Instalar a versão mais recente do PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) e [Az](https://www.powershellgallery.com/packages/Az) módulos. Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão do PowerShell.
3. No PowerShell, inicie sessão na subscrição do User-b como UserB introduzindo o `Add-AzureAccount` comando. A conta, que inicie sessão com tem de ter as permissões necessárias para criar um peering de rede virtual. Para obter uma lista de permissões, consulte [permissões de peering de rede Virtual](virtual-network-manage-peering.md#permissions).
4. Para criar uma rede virtual (clássico) com o PowerShell, tem de criar um novo ou modificar um arquivo de configuração de rede existente. Saiba como [exportar, atualizar e importar ficheiros de configuração de rede](virtual-networks-using-network-configuration-file.md). O ficheiro deve incluir o seguinte procedimento **VirtualNetworkSite** elemento para a rede virtual utilizado neste tutorial:

    ```xml
    <VirtualNetworkSite name="myVnetB" Location="East US">
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

5. Inicie sessão na subscrição do User-b como UserB para utilizar comandos do Gestor de recursos ao introduzir o `Connect-AzAccount` comando.
6. Atribuir permissões de utilizador para a rede virtual B. Copie o seguinte script para um editor de texto no seu PC e substitua `<SubscriptionB-id>` com o ID da subscrição B. Se não souber o Id de subscrição, introduza o `Get-AzSubscription` comando para exibi-la. O valor para **Id** no resultado retornado é o ID da subscrição. Azure criou a rede virtual (clássico) que criou no passo 4 num grupo de recursos com o nome *sistema de rede padrão*. Para executar o script, copie o script modificado, cole-a no PowerShell e, em seguida, prima `Enter`.
    
    ```powershell 
    New-AzRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Termine a sessão do Azure como UserB e inicie sessão na subscrição do User-como UserA, introduzindo o `Connect-AzAccount` comando. A conta, que inicie sessão com tem de ter as permissões necessárias para criar um peering de rede virtual. Para obter uma lista de permissões, consulte [permissões de peering de rede Virtual](virtual-network-manage-peering.md#permissions).
8. Criar a rede virtual (Resource Manager) ao copiar o script a seguir, colá-la no PowerShell e, em seguida, premindo `Enter`:

    ```powershell
    # Variables for common values
      $rgName='MyResourceGroupA'
      $location='eastus'

    # Create a resource group.
    New-AzResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network A.
    $vnetA = New-AzVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location
    ```

9. Atribua permissões de utilizador b ao myVnetA. Copie o seguinte script para um editor de texto no seu PC e substitua `<SubscriptionA-Id>` com o ID da subscrição A. Se não souber o Id de subscrição, introduza o `Get-AzSubscription` comando para exibi-la. O valor para **Id** no resultado retornado é o ID da subscrição. Cole a versão modificada do script do PowerShell e, em seguida, prima `Enter` para executá-lo.

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Copie o seguinte script para um editor de texto no seu PC e substitua `<SubscriptionB-id>` com o ID da subscrição B. Configurar o peering entre myVnetA para myVNetB, copie o script modificado, cole-a no PowerShell e, em seguida, prima `Enter`.

    ```powershell
    Add-AzVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Ver o estado do peering de myVnetA, copiar o script a seguir, colá-la na PowerShell e prima `Enter`.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    O estado é **ligado**. Ele é alterado para **ligado** depois de configurar o peering para myVnetA myVnetB.

    Todos os recursos do Azure que cria em qualquer rede virtual agora são capazes de comunicar entre si através de seus endereços IP. Se estiver a utilizar a resolução de nomes do Azure do padrão para as redes virtuais, os recursos as redes virtuais não são capazes de resolver nomes entre as redes virtuais. Se pretender resolver nomes entre redes virtuais num modo de peering, tem de criar seu próprio servidor DNS. Saiba como configurar [resolução de nomes através de seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

12. **Opcional**: Embora a criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conectividade.
13. **Opcional**: Para eliminar os recursos que criar neste tutorial, conclua os passos em [eliminar recursos](#delete-powershell) neste artigo.

## <a name="delete"></a>Eliminar recursos
Quando tiver concluído este tutorial, pode querer eliminar os recursos que criou no tutorial, pelo que não a incorrer em custos de utilização. Eliminar um grupo de recursos também elimina a todos os recursos que estão no grupo de recursos.

### <a name="delete-portal"></a>Portal do Azure

1. Na caixa de pesquisa de portal, introduza **myResourceGroupA**. Nos resultados da pesquisa, clique em **myResourceGroupA**.
2. Sobre o **myResourceGroupA** painel, clique nas **eliminar** ícone.
3. Para confirmar a eliminação, no **tipo o nome de grupo de recursos** , introduza **myResourceGroupA**e, em seguida, clique em **eliminar**.
4. Na **recursos de pesquisa** caixa na parte superior do portal, tipo *myVnetB*. Clique em **myVnetB** quando for apresentada nos resultados da pesquisa. É apresentado um painel para o **myVnetB** rede virtual.
5. Na **myVnetB** painel, clique em **eliminar**.
6. Para confirmar a eliminação, clique em **Sim** no **rede virtual de eliminação** caixa.

### <a name="delete-cli"></a>CLI do Azure

1. Inicie sessão no Azure com a CLI ao eliminar a rede virtual (Resource Manager) com o seguinte comando:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Inicie sessão no Azure com a CLI clássica para eliminar a rede virtual (clássico) com os seguintes comandos:

   ```azurecli-interactive
   azure config mode asm

   azure network vnet delete --vnet myVnetB --quiet
   ```

### <a name="delete-powershell"></a>PowerShell

1. Na linha de comando do PowerShell, introduza o seguinte comando para eliminar a rede virtual (Resource Manager):

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -Force
   ```

2. Para eliminar a rede virtual (clássico) com o PowerShell, tem de modificar um ficheiro de configuração de rede existente. Saiba como [exportar, atualizar e importar ficheiros de configuração de rede](virtual-networks-using-network-configuration-file.md). Remova o seguinte elemento de VirtualNetworkSite para a rede virtual utilizado neste tutorial:

   ```xml
   <VirtualNetworkSite name="myVnetB" Location="East US">
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