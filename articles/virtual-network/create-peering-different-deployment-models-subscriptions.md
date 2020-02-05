---
title: Criar um emparelhamento de rede virtual do Azure-modelos de implantação diferentes-assinaturas diferentes
titlesuffix: Azure Virtual Network
description: Saiba como criar um emparelhamento de rede virtual entre redes virtuais criadas por meio de diferentes modelos de implantação do Azure que existem em diferentes assinaturas do Azure.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 0429bf4968f457e201491db3df16f0004f216f30
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023281"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Criar um emparelhamento de rede virtual-diferentes modelos de implantação e assinaturas

Neste tutorial, você aprenderá a criar um emparelhamento de rede virtual entre redes virtuais criadas por meio de diferentes modelos de implantação. As redes virtuais existem em assinaturas diferentes. O emparelhamento de duas redes virtuais permite que os recursos em diferentes redes virtuais se comuniquem entre si com a mesma largura de banda e latência como se os recursos estivessem na mesma rede virtual. Saiba mais sobre o [emparelhamento de rede virtual](virtual-network-peering-overview.md).

As etapas para criar um emparelhamento de rede virtual são diferentes, dependendo se as redes virtuais estão na mesma ou em diferentes assinaturas e em qual [modelo de implantação do Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) as redes virtuais são criadas. Saiba como criar um emparelhamento de rede virtual em outros cenários clicando no cenário da tabela a seguir:

|Modelo de implementação do Azure  | Subscrição do Azure  |
|--------- |---------|
|[O Gerenciador de recursos](tutorial-connect-virtual-networks-portal.md) |Idêntica|
|[O Gerenciador de recursos](create-peering-different-subscriptions.md) |Outros|
|[Um Gerenciador de recursos, um clássico](create-peering-different-deployment-models.md) |Idêntica|

Um emparelhamento de rede virtual não pode ser criado entre duas redes virtuais implantadas por meio do modelo de implantação clássico. Este tutorial usa redes virtuais que existem na mesma região. Este tutorial emparelha redes virtuais na mesma região. Você também pode emparelhar redes virtuais em diferentes [regiões com suporte](virtual-network-manage-peering.md#cross-region). É recomendável que você se familiarize com os [requisitos e as restrições de emparelhamento](virtual-network-manage-peering.md#requirements-and-constraints) antes de emparelhar redes virtuais.

Ao criar um emparelhamento de rede virtual entre redes virtuais que existem em assinaturas diferentes, as assinaturas devem estar associadas ao mesmo locatário de Azure Active Directory. Se você ainda não tiver um locatário Azure Active Directory, poderá [criar um](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant)rapidamente. Você pode conectar redes virtuais em diferentes assinaturas e diferentes locatários Azure Active Directory usando um [Gateway de VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)do Azure.

Você pode usar o [portal do Azure](#portal), a CLI ( [interface de linha de comando](#cli) ) do Azure ou o Azure [PowerShell](#powershell) para criar um emparelhamento de rede virtual. Clique em qualquer um dos links de ferramentas anteriores para ir diretamente para as etapas para criar um emparelhamento de rede virtual usando sua ferramenta de escolha.

## <a name="portal"></a>Criar emparelhamento-portal do Azure

Este tutorial usa contas diferentes para cada assinatura. Se você estiver usando uma conta que tenha permissões para ambas as assinaturas, poderá usar a mesma conta para todas as etapas, ignorar as etapas para fazer logoff do portal e ignorar as etapas para atribuir outras permissões de usuário para as redes virtuais.

1. Faça logon no [portal do Azure](https://portal.azure.com) como UserA. A conta com a qual você faz logon deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Para obter uma lista de permissões, consulte [permissões de emparelhamento de rede virtual](virtual-network-manage-peering.md#permissions).
2. Clique em **+ novo**, em **rede**e em **rede virtual**.
3. Na folha **criar rede virtual** , insira ou selecione valores para as seguintes configurações e clique em **criar**:
    - **Name**: *myVnetA*
    - **Espaço de endereço**: *10.0.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - **Intervalo de endereços da sub-rede**: *10.0.0.0/24*
    - **Assinatura**: selecione A assinatura A.
    - **Grupo de recursos**: selecione **criar novo** e insira *myResourceGroupA*
    - **Local**: *leste dos EUA*
4. Na caixa **Pesquisar recursos** na parte superior do portal, digite *myVnetA*. Clique em **myVnetA** quando ele aparecer nos resultados da pesquisa. Uma folha é exibida para a rede virtual **myVnetA** .
5. Na folha **myVnetA** exibida, clique em **controle de acesso (iam)** na lista vertical de opções no lado esquerdo da folha.
6. Na folha **myVnetA-controle de acesso (iam)** que aparece, clique em **+ Adicionar atribuição de função**.
7. Na folha **Adicionar atribuição de função** exibida, selecione **colaborador de rede** na caixa **função** .
8. Na caixa **selecionar** , selecione UserB ou digite o endereço de email de UserB para procurá-lo. A lista de usuários mostrada é do mesmo locatário Azure Active Directory que a rede virtual para a qual você está configurando o emparelhamento. Clique em UserB quando ele aparecer na lista.
9. Clique em **Guardar**.
10. Faça logoff do portal como UserA e, em seguida, faça logon como UserB.
11. Clique em **+ novo**, digite *rede virtual* na caixa **Pesquisar no Marketplace** e clique em **rede virtual** nos resultados da pesquisa.
12. Na folha **rede virtual** que aparece, selecione **clássico** na caixa **selecionar um modelo de implantação** e, em seguida, clique em **criar**.
13. Na caixa criar rede virtual (clássica) que aparece, insira os seguintes valores:

    - **Name**: *myVnetB*
    - **Espaço de endereço**: *10.1.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - **Intervalo de endereços da sub-rede**: *10.1.0.0/24*
    - **Assinatura**: selecione a assinatura B.
    - **Grupo de recursos**: selecione **criar novo** e insira *myResourceGroupB*
    - **Local**: *leste dos EUA*

14. Na caixa **Pesquisar recursos** na parte superior do portal, digite *myVnetB*. Clique em **myVnetB** quando ele aparecer nos resultados da pesquisa. Uma folha é exibida para a rede virtual **myVnetB** .
15. Na folha **myVnetB** exibida, clique em **Propriedades** na lista vertical de opções no lado esquerdo da folha. Copie a **ID do recurso**, que é usada em uma etapa posterior. A ID do recurso é semelhante ao exemplo a seguir: `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`
16. Conclua as etapas 5-9 para myVnetB, inserindo **UserA** na etapa 8.
17. Faça logoff do portal como UserB e faça logon como UserA.
18. Na caixa **Pesquisar recursos** na parte superior do portal, digite *myVnetA*. Clique em **myVnetA** quando ele aparecer nos resultados da pesquisa. Uma folha é exibida para a rede virtual **myVnet** .
19. Clique em **myVnetA**.
20. Na folha **myVnetA** exibida, clique em **emparelhamentos** na lista vertical de opções no lado esquerdo da folha.
21. Na folha **myVnetA-emparelhamentos** exibida, clique em **+ Adicionar**
22. Na folha **Adicionar emparelhamento** que aparece, insira ou selecione as seguintes opções e clique em **OK**:
     - **Name**: *myVnetAToMyVnetB*
     - **Modelo de implantação de rede virtual**: selecione **clássico**.
     - **Sei minha ID de recurso**: Marque esta caixa.
     - **ID do recurso**: Insira a ID do recurso de myVnetB da etapa 15.
     - **Permitir acesso à rede virtual:** Verifique se **habilitado** está selecionado.
    Nenhuma outra configuração é usada neste tutorial. Para saber mais sobre todas as configurações de emparelhamento, leia [gerenciar emparelhamentos de rede virtual](virtual-network-manage-peering.md#create-a-peering).
23. Depois de clicar em **OK** na etapa anterior, a folha **Adicionar emparelhamento** será fechada e você verá a folha **myVnetA-emparelhamentos** novamente. Depois de alguns segundos, o emparelhamento que você criou aparece na folha. **Conectado** é listado na coluna **status de emparelhamento** para o emparelhamento **myVnetAToMyVnetB** que você criou. O emparelhamento agora está estabelecido. Não é necessário emparelhar a rede virtual (clássica) com a rede virtual (Resource Manager).

    Todos os recursos do Azure criados em qualquer rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nome do Azure padrão para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você quiser resolver nomes entre redes virtuais em um emparelhamento, você deve criar seu próprio servidor DNS. Saiba como configurar a [resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

24. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você pode criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual para a outra, para validar a conectividade.
25. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas na seção [excluir recursos](#delete-portal) deste artigo.

## <a name="cli"></a>Criar emparelhamento-CLI do Azure

Este tutorial usa contas diferentes para cada assinatura. Se você estiver usando uma conta que tenha permissões para ambas as assinaturas, poderá usar a mesma conta para todas as etapas, ignorar as etapas para fazer logoff do Azure e remover as linhas de script que criam atribuições de função de usuário. Substitua UserA@azure.com e UserB@azure.com em todos os scripts a seguir pelos nomes de acessados que você está usando para UserA e UserB. Conclua as etapas a seguir usando a CLI clássica do Azure e a CLI do Azure. Você pode concluir as etapas da Azure Cloud Shell, selecionando o botão **experimentar** em qualquer uma das etapas a seguir ou instalando a [CLI clássica](/cli/azure/install-classic-cli) e a [CLI](/cli/azure/install-azure-cli) e executando os comandos em seu computador local.

1. Se estiver usando o Cloud Shell, pule para a etapa 2, porque o Cloud Shell o conecta automaticamente ao Azure. Abra uma sessão de comando e entre no Azure usando o comando `azure login`.
2. Execute a CLI clássica no modo de gerenciamento de serviços inserindo o comando `azure config mode asm`.
3. Insira o comando da CLI clássica a seguir para criar a rede virtual (clássica):

    ```azurecli
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```
4. As etapas restantes devem ser concluídas usando um shell bash com o CLI do Azure (não a CLI clássica).
5. Copie o script a seguir em um editor de texto em seu computador. Substitua `<SubscriptionB-Id>` pela sua ID de assinatura. Se você não souber sua ID de assinatura, insira o comando `az account show`. O valor de **ID** na saída é sua ID de assinatura. Copie o script modificado, Cole-o em sua sessão da CLI e, em seguida, pressione `Enter`.

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    Quando você criou a rede virtual (clássica) na etapa 4, o Azure criou a rede virtual no grupo de recursos de *rede padrão* .
6. Faça logon UserB do Azure e faça logon como UserA na CLI.
7. Crie um grupo de recursos e uma rede virtual (Resource Manager). Copie o script a seguir, Cole-o em sua sessão da CLI e, em seguida, pressione `Enter`.

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

8. Crie um emparelhamento de rede virtual entre as duas redes virtuais criadas por meio de diferentes modelos de implantação. Copie o script a seguir em um editor de texto em seu computador. Substitua `<SubscriptionB-id>` pela sua ID de assinatura. Se você não souber sua ID de assinatura, insira o comando `az account show`. O valor de **ID** na saída é sua ID de assinatura. o Azure criou a rede virtual (clássica) que você criou na etapa 4 em um grupo de recursos denominado *Default-Networking*. Cole o script modificado na sessão da CLI e, em seguida, pressione `Enter`.

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

9. Depois que o script for executado, examine o emparelhamento para a rede virtual (Resource Manager). Copie o script a seguir e cole-o na sua sessão da CLI:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```
    A saída mostra **conectada** na coluna **PeeringState** .

    Todos os recursos do Azure criados em qualquer rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nome do Azure padrão para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você quiser resolver nomes entre redes virtuais em um emparelhamento, você deve criar seu próprio servidor DNS. Saiba como configurar a [resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

10. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você pode criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual para a outra, para validar a conectividade.
11. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas em [excluir recursos](#delete-cli) neste artigo.

## <a name="powershell"></a>Criar emparelhamento-PowerShell

Este tutorial usa contas diferentes para cada assinatura. Se você estiver usando uma conta que tenha permissões para ambas as assinaturas, poderá usar a mesma conta para todas as etapas, ignorar as etapas para fazer logoff do Azure e remover as linhas de script que criam atribuições de função de usuário. Substitua UserA@azure.com e UserB@azure.com em todos os scripts a seguir pelos nomes de acessados que você está usando para UserA e UserB. 

1. Instale a versão mais recente dos módulos [Azure](https://www.powershellgallery.com/packages/Azure) e [AZ](https://www.powershellgallery.com/packages/Az) do PowerShell. Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão do PowerShell.
3. No PowerShell, faça logon na assinatura do UserB como UserB digitando o comando `Add-AzureAccount`. A conta com a qual você faz logon deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Para obter uma lista de permissões, consulte [permissões de emparelhamento de rede virtual](virtual-network-manage-peering.md#permissions).
4. Para criar uma rede virtual (clássica) com o PowerShell, você deve criar um novo arquivo de configuração de rede existente ou modificá-lo. Saiba como [exportar, atualizar e importar arquivos de configuração de rede](virtual-networks-using-network-configuration-file.md). O arquivo deve incluir o seguinte elemento **VirtualNetworkSite** para a rede virtual usada neste tutorial:

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
    > A importação de um arquivo de configuração de rede alterado pode causar alterações em redes virtuais existentes (clássicas) em sua assinatura. Certifique-se de adicionar apenas a rede virtual anterior e de que você não altere ou remova as redes virtuais existentes da sua assinatura. 

5. Faça logon na assinatura do UserB como UserB para usar comandos do Resource Manager digitando o comando `Connect-AzAccount`.
6. Atribua permissões UserA à rede virtual B. Copie o script a seguir em um editor de texto em seu computador e substitua `<SubscriptionB-id>` pela ID da assinatura B. Se você não souber a ID da assinatura, insira o comando `Get-AzSubscription` para exibi-la. O valor de **ID** na saída retornada é sua ID de assinatura. O Azure criou a rede virtual (clássica) que você criou na etapa 4 em um grupo de recursos denominado *Default-Networking*. Para executar o script, copie o script modificado, Cole-o no PowerShell e, em seguida, pressione `Enter`.
    
    ```powershell 
    New-AzRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Faça logoff do Azure como UserB e faça logon na assinatura do UserA como UserA digitando o comando `Connect-AzAccount`. A conta com a qual você faz logon deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Para obter uma lista de permissões, consulte [permissões de emparelhamento de rede virtual](virtual-network-manage-peering.md#permissions).
8. Crie a rede virtual (Resource Manager) copiando o script a seguir, colando-o no PowerShell e, em seguida, pressionando `Enter`:

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

9. Atribua permissões UserB a myVnetA. Copie o script a seguir em um editor de texto em seu computador e substitua `<SubscriptionA-Id>` pela ID da assinatura A. Se você não souber a ID da assinatura, insira o comando `Get-AzSubscription` para exibi-la. O valor de **ID** na saída retornada é sua ID de assinatura. Cole a versão modificada do script no PowerShell e, em seguida, pressione `Enter` para executá-la.

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Copie o script a seguir em um editor de texto em seu computador e substitua `<SubscriptionB-id>` pela ID da assinatura B. Para emparelhar myVnetA a myVNetB, copie o script modificado, Cole-o no PowerShell e, em seguida, pressione `Enter`.

    ```powershell
    Add-AzVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Exiba o estado de emparelhamento de myVnetA copiando o script a seguir, colando-o no PowerShell e pressionando `Enter`.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    O estado é **conectado**. Ele muda para **conectado** depois que você configura o emparelhamento para MyVnetA de myVnetB.

    Todos os recursos do Azure criados em qualquer rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nome do Azure padrão para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você quiser resolver nomes entre redes virtuais em um emparelhamento, você deve criar seu próprio servidor DNS. Saiba como configurar a [resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

12. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você pode criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual para a outra, para validar a conectividade.
13. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas em [excluir recursos](#delete-powershell) neste artigo.

## <a name="delete"></a>Excluir recursos
Ao concluir este tutorial, talvez você queira excluir os recursos criados no tutorial, para que não incorra em encargos de uso. A exclusão de um grupo de recursos também exclui todos os recursos que estão no grupo de recursos.

### <a name="delete-portal"></a>Portal do Azure

1. Na caixa de pesquisa do portal, digite **myResourceGroupA**. Nos resultados da pesquisa, clique em **myResourceGroupA**.
2. Na folha **myResourceGroupA** , clique no ícone **excluir** .
3. Para confirmar a exclusão, na caixa **digite o nome do grupo de recursos** , digite **myResourceGroupA**e clique em **excluir**.
4. Na caixa **Pesquisar recursos** na parte superior do portal, digite *myVnetB*. Clique em **myVnetB** quando ele aparecer nos resultados da pesquisa. Uma folha é exibida para a rede virtual **myVnetB** .
5. Na folha **myVnetB** , clique em **excluir**.
6. Para confirmar a exclusão, clique em **Sim** na caixa **excluir rede virtual** .

### <a name="delete-cli"></a>CLI do Azure

1. Faça logon no Azure usando a CLI para excluir a rede virtual (Resource Manager) com o seguinte comando:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Entre no Azure usando a CLI clássica para excluir a rede virtual (clássica) com os seguintes comandos:

   ```azurecli-interactive
   azure config mode asm

   azure network vnet delete --vnet myVnetB --quiet
   ```

### <a name="delete-powershell"></a>PowerShell

1. No prompt de comando do PowerShell, digite o seguinte comando para excluir a rede virtual (Resource Manager):

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -Force
   ```

2. Para excluir a rede virtual (clássica) com o PowerShell, você deve modificar um arquivo de configuração de rede existente. Saiba como [exportar, atualizar e importar arquivos de configuração de rede](virtual-networks-using-network-configuration-file.md). Remova o seguinte elemento VirtualNetworkSite para a rede virtual usada neste tutorial:

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
   > A importação de um arquivo de configuração de rede alterado pode causar alterações em redes virtuais existentes (clássicas) em sua assinatura. Verifique se você removeu apenas a rede virtual anterior e se não alterou ou removeu outras redes virtuais existentes da sua assinatura. 

## <a name="next-steps"></a>Passos seguintes

- Familiarize-se totalmente com [restrições e comportamentos importantes de emparelhamento de rede virtual](virtual-network-manage-peering.md#requirements-and-constraints) antes de criar um emparelhamento de rede virtual para uso em produção.
- Saiba mais sobre todas [as configurações de emparelhamento de rede virtual](virtual-network-manage-peering.md#create-a-peering).
- Saiba como [criar uma topologia de rede de Hub e spoke com o](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) emparelhamento de rede virtual.