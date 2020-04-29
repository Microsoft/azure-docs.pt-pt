---
title: Criar um peering de rede virtual Azure - diferentes modelos de implementação - diferentes subscrições
titlesuffix: Azure Virtual Network
description: Saiba como criar uma rede virtual de peering entre redes virtuais criadas através de diferentes modelos de implementação do Azure que existem em diferentes subscrições do Azure.
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
ms.openlocfilehash: 6823514e284f75f0580578dcabaa1b1bdcbe2f59
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80239845"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Criar uma rede virtual de peering - diferentes modelos de implementação e subscrições

Neste tutorial, aprende-se a criar uma rede virtual de observação entre redes virtuais criadas através de diferentes modelos de implementação. As redes virtuais existem em diferentes subscrições. Espreitar duas redes virtuais permite que os recursos em diferentes redes virtuais se comuniquem entre si com a mesma largura de banda e latência como se os recursos estivessem na mesma rede virtual. Saiba mais sobre [o peering virtual da rede.](virtual-network-peering-overview.md)

Os passos para criar um peering de rede virtual são diferentes, dependendo se as redes virtuais estão nas mesmas, ou diferentes, subscrições, e em que modelo de [implementação azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) as redes virtuais são criadas através. Saiba como criar uma rede virtual a espreitar noutros cenários clicando no cenário a partir da tabela seguinte:

|Modelo de implementação do Azure  | Subscrição do Azure  |
|--------- |---------|
|[Ambas com Resource Manager](tutorial-connect-virtual-networks-portal.md) |Mesma|
|[Ambas com Resource Manager](create-peering-different-subscriptions.md) |Diferente|
|[Uma com Resource Manager, outra com clássica](create-peering-different-deployment-models.md) |Mesma|

Não é possível criar um epeering de rede virtual entre duas redes virtuais implantadas através do modelo clássico de implantação. Este tutorial utiliza redes virtuais que existem na mesma região. Este tutorial identifica redes virtuais na mesma região. Você também pode peer redes virtuais em [diferentes regiões apoiadas](virtual-network-manage-peering.md#cross-region). Recomenda-se que se familiarize com os [requisitos e constrangimentos](virtual-network-manage-peering.md#requirements-and-constraints) de observação antes de espreitar redes virtuais.

Ao criar uma rede virtual de observação entre redes virtuais que existem em diferentes subscrições, as subscrições devem ser associadas ao mesmo inquilino do Azure Ative Directory. Se ainda não tem um inquilino do Azure Ative Directory, pode [rapidamente criar um](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant). Pode ligar redes virtuais em diferentes subscrições e diferentes inquilinos do Azure Ative Directory utilizando um Gateway Azure [VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Pode utilizar o [portal Azure,](#portal)a interface de [linha de comando](#cli) Azure (CLI) ou o Azure [PowerShell](#powershell) para criar um epeering de rede virtual. Clique em qualquer uma das ligações de ferramentas anteriores para ir diretamente aos passos para criar um peering de rede virtual usando a sua ferramenta de eleição.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Criar o peering - Portal Azure

Este tutorial utiliza contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todos os passos, saltar os passos para sair do portal e saltar os passos para atribuir permissões de outro utilizador às redes virtuais.

1. Inicie sessão no [portal Azure](https://portal.azure.com) como UserA. A conta com a sua participação deve ter as permissões necessárias para criar um epeering de rede virtual. Para obter uma lista de permissões, consulte [permissões de verificação](virtual-network-manage-peering.md#permissions)de rede virtual .
2. Clique **+ Novo,** clique em **Rede,** em seguida, clique em **rede virtual**.
3. Na lâmina de **rede virtual Criar,** introduzir ou selecionar valores para as seguintes definições, clique em **Criar:**
    - **Nome**: *myVnetA*
    - **Espaço**de endereço : *10.0.0.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - Gama de **endereços de sub-rede**: *10.0.0.0/24*
    - **Subscrição**: Selecione subscrição A.
    - **Grupo de recursos**: Selecione **Criar novo** e insira *o myResourceGroupA*
    - **Localização**: *Leste dos EUA*
4. Na caixa de **recursos de pesquisa** no topo do portal, digite *myVnetA*. Clique em **myVnetA** quando aparecer nos resultados da pesquisa. Uma lâmina aparece para a rede virtual **myVnetA.**
5. Na lâmina **myVnetA** que aparece, clique no controlo de **acesso (IAM)** a partir da lista vertical de opções no lado esquerdo da lâmina.
6. Na lâmina **myVnetA - Controlo de acesso (IAM)** que aparece, clique + Adicionar atribuição de **funções**.
7. Na lâmina de atribuição de **funções Add** que aparece, selecione **colaborador de rede** na caixa **Role.**
8. Na caixa **Select,** selecione UserB ou escreva o endereço de e-mail do UserB para o procurar. A lista de utilizadores mostrada é do mesmo inquilino do Azure Ative Directory que a rede virtual que está a criar. Clique no UserB quando aparecer na lista.
9. Clique em **Guardar**.
10. Faça login no portal como UserA e, em seguida, inicie sessão como UserB.
11. Clique **em + Nova**rede *virtual* de tipo na caixa De **Mercado e,** em seguida, clique na **rede Virtual** nos resultados da pesquisa.
12. Na lâmina **da Rede Virtual** que aparece, selecione **Classic** na caixa de **modelo seletiva** e, em seguida, clique em **Criar**.
13. Na caixa de rede virtual Create (clássica) que aparece, introduza os seguintes valores:

    - **Nome**: *myVnetB*
    - **Espaço**de endereço : *10.1.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - Gama de **endereços de sub-rede**: *10.1.0.0/24*
    - **Subscrição**: Selecione subscrição B.
    - **Grupo de recursos**: Selecione **Criar novo** e insira *o myResourceGroupB*
    - **Localização**: *Leste dos EUA*

14. Na caixa de **recursos de pesquisa** no topo do portal, digite *myVnetB*. Clique em **myVnetB** quando aparecer nos resultados da pesquisa. Uma lâmina aparece para a rede virtual **myVnetB.**
15. Na lâmina **myVnetB** que aparece, clique em **Propriedades** a partir da lista vertical de opções no lado esquerdo da lâmina. Copie o ID de **RECURSO,** que é utilizado num passo posterior. O ID do recurso é semelhante ao seguinte exemplo:`/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`
16. Complete os passos 5-9 para o myVnetB, entrando **userA** no passo 8.
17. Faça login no portal como UserB e inicie sessão como UserA.
18. Na caixa de **recursos de pesquisa** no topo do portal, digite *myVnetA*. Clique em **myVnetA** quando aparecer nos resultados da pesquisa. Uma lâmina aparece para a rede virtual **myVnet.**
19. Clique em **myVnetA**.
20. Na lâmina **myVnetA** que aparece, clique em **Peerings** a partir da lista vertical de opções no lado esquerdo da lâmina.
21. Na **myVnetA - Lâmina** de pares que apareceu, clique **+ Adicionar**
22. Na lâmina **de peering Adicionar** que aparece, introduza ou selecione as seguintes opções, em seguida, clique **OK**:
     - **Nome**: *myVnetAToMyVnetB*
     - **Modelo de implementação de rede virtual**: Select **Classic**.
     - **Conheço o meu iD**de recursos: verifique esta caixa.
     - **ID de recurso**: Introduza o ID de recurso do myVnetB a partir do passo 15.
     - **Permitir o acesso virtual à rede:** Certifique-se de que o **Ativado** está selecionado.
    Não são utilizadas outras definições neste tutorial. Para aprender sobre todas as configurações de peering, leia [Gerir os pares](virtual-network-manage-peering.md#create-a-peering)de rede virtual .
23. Depois de clicar **OK** no passo anterior, a lâmina **de observação Add** fecha-se e vê novamente a lâmina **myVnetA - Peerings.** Após alguns segundos, o olhar que criou aparece na lâmina. **Connected** está listado na coluna **PEERING STATUS** para o **minetAToMyVnetB** que você criou. O olhar está agora estabelecido. Não há necessidade de peer a rede virtual (clássica) para a rede virtual (Gestor de Recursos).

    Quaisquer recursos Azure que crie em qualquer rede virtual são agora capazes de comunicar uns com os outros através dos seus endereços IP. Se estiver a utilizar a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não são capazes de resolver nomes através das redes virtuais. Se quiser resolver nomes através de redes virtuais num epeering, tem de criar o seu próprio servidor DNS. Aprenda a configurar a resolução de [nome utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

24. **Opcional**: Embora a criação de máquinas virtuais não esteja coberta neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar-se de uma máquina virtual à outra, para validar a conectividade.
25. **Opcional**: Para eliminar os recursos que cria neste tutorial, preencha os passos na secção de [recursos Eliminar](#delete-portal) deste artigo.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Criar o peering - Azure CLI

Este tutorial utiliza contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todos os passos, saltar os passos para sair do Azure e remover as linhas de script que criam atribuições de funções ao utilizador. UserA@azure.com Substitua UserB@azure.com e em todos os seguintes scripts com os nomes de utilizador que está a utilizar para UserA e UserB. Complete os seguintes passos utilizando o clássico CLI azure e o Azure CLI. Pode completar os passos da Casca de Nuvem Azure, selecionando apenas o botão **Experimente** em qualquer um dos seguintes passos, ou instalando o CLI e [clI](/cli/azure/install-azure-cli) [clássicos](/cli/azure/install-classic-cli) e executando os comandos no seu computador local.

1. Se utilizar a Cloud Shell, salte para o passo 2, porque a Cloud Shell automaticamente o assina no Azure. Abra uma sessão de comando e `azure login` inscreva-se no Azure usando o comando.
2. Executar o cli clássico no modo `azure config mode asm` de Gestão de Serviços, entrando no comando.
3. Introduza o seguinte comando clássico do CLI para criar a rede virtual (clássico):

    ```console
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

4. Os passos restantes devem ser concluídos com uma concha de bash com o ClI Azure (não o CLI clássico).
5. Copie o seguinte guião para um editor de texto no seu PC. Substitua-a `<SubscriptionB-Id>` pelo seu ID de subscrição. Se não sabe o seu ID `az account show` de assinatura, insira o comando. O valor para **id** na saída é o id de subscrição. Copie o script `Enter`modificado, cole-o na sua sessão CLI e, em seguida, prima .

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    Quando criou a rede virtual (clássica) no passo 4, o Azure criou a rede virtual no grupo de recursos *Default-Networking.*
6. Faça loga no UserB a partir de Azure e inicie sessão como UserA no CLI.
7. Criar um grupo de recursos e uma rede virtual (Gestor de Recursos). Copie o seguinte script, cole-o na sua `Enter`sessão CLI e, em seguida, prima .

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

8. Criar uma rede virtual de observação entre as duas redes virtuais criadas através dos diferentes modelos de implementação. Copie o seguinte guião para um editor de texto no seu PC. Substitua-a `<SubscriptionB-id>` com o seu Id de subscrição. Se não sabe o seu ID `az account show` de assinatura, insira o comando. O valor para **id** na saída é a sua subscrição Id. Azure criou a rede virtual (clássica) que criou no passo 4 num grupo de recursos chamado *Default-Networking*. Cola o script modificado na tua sessão `Enter`CLI e, em seguida, pressione .

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

9. Após a execução do script, reveja o epeering para a rede virtual (Gestor de Recursos). Copie o seguinte script e, em seguida, cole-o na sua sessão CLI:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```

    A saída mostra **ligado** na coluna **PeeringState.**

    Quaisquer recursos Azure que crie em qualquer rede virtual são agora capazes de comunicar uns com os outros através dos seus endereços IP. Se estiver a utilizar a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não são capazes de resolver nomes através das redes virtuais. Se quiser resolver nomes através de redes virtuais num epeering, tem de criar o seu próprio servidor DNS. Aprenda a configurar a resolução de [nome utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

10. **Opcional**: Embora a criação de máquinas virtuais não esteja coberta neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar-se de uma máquina virtual à outra, para validar a conectividade.
11. **Opcional**: Para eliminar os recursos que cria neste tutorial, complete os passos em [Eliminar os recursos](#delete-cli) neste artigo.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Criar o peering - PowerShell

Este tutorial utiliza contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todos os passos, saltar os passos para sair do Azure e remover as linhas de script que criam atribuições de funções ao utilizador. UserA@azure.com Substitua UserB@azure.com e em todos os seguintes scripts com os nomes de utilizador que está a utilizar para UserA e UserB. 

1. Instale a versão mais recente dos módulos PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) e [Az.](https://www.powershellgallery.com/packages/Az) Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão powerShell.
3. No PowerShell, inicie sessão na subscrição do UserB como UserB, inserindo o `Add-AzureAccount` comando. A conta com a sua participação deve ter as permissões necessárias para criar um epeering de rede virtual. Para obter uma lista de permissões, consulte [permissões de verificação](virtual-network-manage-peering.md#permissions)de rede virtual .
4. Para criar uma rede virtual (clássica) com o PowerShell, tem de criar um novo, ou modificar um ficheiro de configuração de rede existente. Saiba como [exportar, atualizar e importar ficheiros de configuração da rede.](virtual-networks-using-network-configuration-file.md) O ficheiro deve incluir o seguinte elemento **VirtualNetworkSite** para a rede virtual utilizada neste tutorial:

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
    > Importar um ficheiro de configuração de rede alterado pode causar alterações nas redes virtuais existentes (clássicas) na sua subscrição. Certifique-se de que apenas adiciona a rede virtual anterior e que não altera ou remove quaisquer redes virtuais existentes da sua subscrição. 

5. Inicie sessão na subscrição do UserB como UserB para `Connect-AzAccount` utilizar os comandos do Gestor de Recursos, inserindo o comando.
6. Atribuir permissões de UserA à rede virtual B. Copie o `<SubscriptionB-id>` seguinte script a um editor de texto no seu PC e substitua-o pelo ID da subscrição B. Se não conhece o ID de `Get-AzSubscription` subscrição, insira o comando para vê-lo. O valor para **Id** na saída devolvida é o seu ID de subscrição. O Azure criou a rede virtual (clássica) que criaste no passo 4 num grupo de recursos chamado *Default-Networking*. Para executar o script, copie o script modificado, cole-o `Enter`no PowerShell e, em seguida, pressione .

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Inicie sessão no Azure como UserB e inicie sessão na `Connect-AzAccount` subscrição do UserA como UserA, inserindo o comando. A conta com a sua participação deve ter as permissões necessárias para criar um epeering de rede virtual. Para obter uma lista de permissões, consulte [permissões de verificação](virtual-network-manage-peering.md#permissions)de rede virtual .
8. Crie a rede virtual (Gestor de Recursos) copiando o seguinte script, colando-o ao PowerShell e, em seguida, pressionando: `Enter`

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

9. Atribuir permissões de UserB para myVnetA. Copie o seguinte guião para um `<SubscriptionA-Id>` editor de texto no seu PC e substitua-o pelo ID da subscrição A. Se não conhece o ID de `Get-AzSubscription` subscrição, insira o comando para vê-lo. O valor para **Id** na saída devolvida é o seu ID de subscrição. Colar a versão modificada do script no PowerShell e, em seguida, pressioná-lo `Enter` para executá-lo.

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Copie o seguinte guião para um `<SubscriptionB-id>` editor de texto no seu PC e substitua-o pelo ID da subscrição B. Para espreitar myVnetA para myVNetB, copiar o script modificado, colar no `Enter`PowerShell e, em seguida, pressionar .

    ```powershell
    Add-AzVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Veja o estado de observação do myVnetA copiando o seguinte `Enter`script, colando-o no PowerShell e pressionando.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    O estado está **ligado.** Muda para **Connected** assim que configurar o olhar para myVnetA a partir do myVnetB.

    Quaisquer recursos Azure que crie em qualquer rede virtual são agora capazes de comunicar uns com os outros através dos seus endereços IP. Se estiver a utilizar a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não são capazes de resolver nomes através das redes virtuais. Se quiser resolver nomes através de redes virtuais num epeering, tem de criar o seu próprio servidor DNS. Aprenda a configurar a resolução de [nome utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

12. **Opcional**: Embora a criação de máquinas virtuais não esteja coberta neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar-se de uma máquina virtual à outra, para validar a conectividade.
13. **Opcional**: Para eliminar os recursos que cria neste tutorial, complete os passos em [Eliminar os recursos](#delete-powershell) neste artigo.

## <a name="delete-resources"></a><a name="delete"></a>Eliminar recursos
Quando terminar este tutorial, talvez queira apagar os recursos que criou no tutorial, para que não incorra em taxas de utilização. A eliminação de um grupo de recursos também elimina todos os recursos que estão no grupo de recursos.

### <a name="azure-portal"></a><a name="delete-portal"></a>Portal do Azure

1. Na caixa de pesquisa do portal, introduza **o myResourceGroupA**. Nos resultados da pesquisa, clique em **myResourceGroupA**.
2. Na lâmina **myResourceGroupA,** clique no ícone **Delete.**
3. Para confirmar a eliminação, na caixa **TYPE The RESOURCE GROUP NAME,** introduza o **myResourceGroupA**, e, em seguida, clique em **Apagar**.
4. Na caixa de **recursos de pesquisa** no topo do portal, digite *myVnetB*. Clique em **myVnetB** quando aparecer nos resultados da pesquisa. Uma lâmina aparece para a rede virtual **myVnetB.**
5. Na lâmina **myVnetB,** clique em **Apagar**.
6. Para confirmar a eliminação, clique em **Sim** na caixa **de rede virtual Delete.**

### <a name="azure-cli"></a><a name="delete-cli"></a>CLI do Azure

1. Inicie sessão no Azure utilizando o CLI para eliminar a rede virtual (Gestor de Recursos) com o seguinte comando:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Inscreva-se no Azure utilizando o CLI clássico para eliminar a rede virtual (clássica) com os seguintes comandos:

   ```console
   azure config mode asm

   azure network vnet delete --vnet myVnetB --quiet
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. No pedido de comando PowerShell, introduza o seguinte comando para eliminar a rede virtual (Gestor de Recursos):

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -Force
   ```

2. Para eliminar a rede virtual (clássica) com o PowerShell, tem de modificar um ficheiro de configuração de rede existente. Saiba como [exportar, atualizar e importar ficheiros de configuração da rede.](virtual-networks-using-network-configuration-file.md) Remova o seguinte elemento VirtualNetworkSite para a rede virtual utilizada neste tutorial:

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
   > Importar um ficheiro de configuração de rede alterado pode causar alterações nas redes virtuais existentes (clássicas) na sua subscrição. Certifique-se de que apenas remove a rede virtual anterior e que não altera ou remove quaisquer outras redes virtuais existentes da sua subscrição. 

## <a name="next-steps"></a>Passos seguintes

- Familiarize-se completamente com [importantes restrições e comportamentos](virtual-network-manage-peering.md#requirements-and-constraints) de rede virtual antes de criar uma rede virtual que procura o uso da produção.
- Conheça todas as [definições de peering](virtual-network-manage-peering.md#create-a-peering)de rede virtual .
- Aprenda a criar um hub e falou de [topologia](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) de rede com o peering virtual da rede.