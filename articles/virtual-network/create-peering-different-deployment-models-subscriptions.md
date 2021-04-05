---
title: Crie um espreitamento de rede virtual Azure - diferentes modelos de implementação - diferentes subscrições
titlesuffix: Azure Virtual Network
description: Saiba como criar uma rede virtual de observação entre redes virtuais criadas através de diferentes modelos de implementação Azure que existem em diferentes subscrições do Azure.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2020
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 29a64931ac92eacf8948ae067118b6b25198c85d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98223470"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Criar um espreitamento de rede virtual - diferentes modelos de implementação e subscrições

Neste tutorial, aprende-se a criar uma rede virtual de observação entre redes virtuais criadas através de diferentes modelos de implementação. As redes virtuais existem em diferentes subscrições. Espreitar duas redes virtuais permite que os recursos em diferentes redes virtuais se comuniquem entre si com a mesma largura de banda e latência como se os recursos estivessem na mesma rede virtual. Saiba mais sobre [o espreitamento da rede Virtual.](virtual-network-peering-overview.md)

Os passos para criar um espreguiçadeira de rede virtual são diferentes, dependendo se as redes virtuais estão nas mesmas subscrições, ou diferentes, e que [modelo de implementação Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) as redes virtuais são criadas através. Saiba como criar uma rede virtual a espreitar outros cenários clicando no cenário a partir da tabela seguinte:

|Modelo de implementação do Azure  | Subscrição do Azure  |
|--------- |---------|
|[Ambas com Resource Manager](tutorial-connect-virtual-networks-portal.md) |Mesma|
|[Ambas com Resource Manager](create-peering-different-subscriptions.md) |Diferente|
|[Uma com Resource Manager, outra com clássica](create-peering-different-deployment-models.md) |Mesma|

Não é possível criar um espreitamento de rede virtual entre duas redes virtuais implantadas através do modelo clássico de implementação. Este tutorial utiliza redes virtuais que existem na mesma região. Este tutorial pares redes virtuais na mesma região. Também pode espreitar redes virtuais em [diferentes regiões apoiadas.](virtual-network-manage-peering.md#cross-region) Recomenda-se que se familiarize com os [requisitos e constrangimentos de espreitar](virtual-network-manage-peering.md#requirements-and-constraints) antes de espreitar as redes virtuais.

Ao criar uma rede virtual que espreita entre redes virtuais que existem em diferentes subscrições, as subscrições podem associar-se ao mesmo inquilino do Azure Ative Directory. Se ainda não tem um inquilino do Azure Ative Directory, pode criar rapidamente [um.](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant)

Pode utilizar o [portal Azure,](#portal)a [interface de linha de comando](#cli) Azure (CLI) ou Azure [PowerShell](#powershell) para criar um espreitamento de rede virtual. Clique em qualquer uma das ligações de ferramentas anteriores para ir diretamente aos passos para criar uma rede virtual que espreita usando a sua ferramenta de eleição.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Criar olhando - Portal Azure

Este tutorial utiliza contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todas as etapas, saltar os passos para iniciar sessão fora do portal e saltar os passos para atribuir outras permissões do utilizador às redes virtuais.

1. Faça login no [portal Azure](https://portal.azure.com) como UserA. A conta com que inicia sessão deve ter as permissões necessárias para criar uma rede virtual de observação. Para obter uma lista de permissões, consulte [permissões de espreitar rede virtual](virtual-network-manage-peering.md#permissions).
2. Clique **em + Novo,** clique **em Rede** e, em seguida, clique na rede **Virtual**.
3. Na lâmina **de rede virtual Criar,** introduzir ou selecionar valores para as seguintes definições e, em seguida, clicar em **Criar**:
    - **Nome**: *myVnetA*
    - **Espaço de** endereço : *10.0.0.0/16*
    - **Nome da sub-rede**: *predefinição*
    - **Intervalo de endereços da sub-rede**: *10.0.0.0/24*
    - **Assinatura**: Selecione subscrição A.
    - **Grupo de recursos**: Selecione **Criar novo** e insira *o myResourceGroupA*
    - **Localização**: *Leste dos EUA*
4. Na caixa **de recursos de busca** no topo do portal, *digite myVnetA*. Clique **em myVnetA** quando aparecer nos resultados da pesquisa. Aparece uma lâmina para a rede virtual **myVnetA.**
5. Na lâmina **myVnetA** que aparece, clique no **controlo de acesso (IAM)** a partir da lista vertical de opções no lado esquerdo da lâmina.
6. Na lâmina **myVnetA - Controlo de acesso (IAM)** que aparece, clique **+ Adicione a atribuição de função**.
7. Na lâmina **de atribuição de funções Adicionar** que aparece, selecione o contribuinte de **rede** na caixa **Role.**
8. Na caixa **Select,** selecione UserB ou digite o endereço de e-mail do UtilizadorB para o procurar. A lista de utilizadores mostrados é do mesmo inquilino do Azure Ative Directory que a rede virtual que está a configurar. Clique no UserB quando aparecer na lista.
9. Clique em **Guardar**.
10. Faça login no portal como UserA e, em seguida, faça login como UserB.
11. Clique **em + Nova**, *digite rede Virtual* na caixa De Procurar o **Mercado** e, em seguida, clique na rede **Virtual** nos resultados de pesquisa.
12. Na lâmina **de rede virtual** que aparece, selecione **Classic** na caixa de modelo **de implementação** e, em seguida, clique em **Criar**.
13. Na caixa de rede virtual Create (clássica) que aparece, insira os seguintes valores:

    - **Nome**: *myVnetB*
    - **Espaço de** endereço : *10.1.0.0/16*
    - **Nome da sub-rede**: *predefinição*
    - **Intervalo de endereços da sub-rede**: *10.1.0.0/24*
    - **Assinatura**: Selecione subscrição B.
    - **Grupo de recursos**: Selecione **Criar novo** e insira *o myResourceGroupB*
    - **Localização**: *Leste dos EUA*

14. Na caixa **de recursos de busca** no topo do portal, digite *myVnetB*. Clique **no myVnetB** quando aparecer nos resultados da pesquisa. Aparece uma lâmina para a rede virtual **myVnetB.**
15. Na lâmina **myVnetB** que aparece, clique em **Propriedades** a partir da lista vertical de opções no lado esquerdo da lâmina. Copie o **ID recurso,** que é utilizado num passo posterior. O ID de recurso é semelhante ao seguinte exemplo: `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`
16. Passos completos 5-9 para o myVnetB, entrando no **UserA** no passo 8.
17. Faça login no portal como UserB e faça login como UserA.
18. Na caixa **de recursos de busca** no topo do portal, *digite myVnetA*. Clique **em myVnetA** quando aparecer nos resultados da pesquisa. Aparece uma lâmina para a rede virtual **myVnet.**
19. Clique **em myVnetA**.
20. Na lâmina **myVnetA** que aparece, clique **em Peerings** a partir da lista vertical de opções no lado esquerdo da lâmina.
21. Na **myVnetA - Lâmina de peerings** que apareceu, clique **+ Adicionar**
22. Na lâmina **de espreitar** que aparece, introduza ou selecione as seguintes opções e, em seguida, clique **em OK**:
     - **Nome**: *myVnetAToMyVnetB*
     - **Modelo de implementação de rede virtual**: Selecione **Classic**.
     - **Eu sei a minha identificação de recursos:** verifique esta caixa.
     - **ID de recurso**: Introduza o ID de recurso do myVnetB a partir do passo 15.
     - **Permitir o acesso à rede virtual:** Certifique-se de que **o Enabled** está selecionado.
    Não são utilizadas outras definições neste tutorial. Para saber mais sobre todas as definições de observação, leia [Gerir os olhos de rede virtuais](virtual-network-manage-peering.md#create-a-peering).
23. Depois de clicar **em OK** no passo anterior, a lâmina **de espreitar** fecha-se e vê novamente a **lâmina myVnetA - Peerings.** Após alguns segundos, o espreitamento que criou aparece na lâmina. **Connected** está listado na coluna **PEERING STATUS** para o **myVnetAToMyVnetB** que o seu criou. O espreitamento está agora estabelecido. Não há necessidade de espreitar a rede virtual (clássica) para a rede virtual (Gestor de Recursos).

    Quaisquer recursos Azure que crie em qualquer rede virtual são agora capazes de comunicar uns com os outros através dos seus endereços IP. Se estiver a utilizar a resolução de nomeS Azure padrão para as redes virtuais, os recursos nas redes virtuais não são capazes de resolver nomes através das redes virtuais. Se quiser resolver nomes através de redes virtuais num espreitê-lo, tem de criar o seu próprio servidor DNS. Saiba como configurar a [resolução Name utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

24. **Opcional**: Embora a criação de máquinas virtuais não esteja abrangida por este tutorial, pode criar uma máquina virtual em cada rede virtual e ligar-se de uma máquina virtual à outra, para validar a conectividade.
25. **Opcional**: Para eliminar os recursos que cria neste tutorial, complete os passos na secção [de recursos de Eliminar](#delete-portal) deste artigo.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Criar olhando - Azure CLI

Este tutorial utiliza contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todas as etapas, saltar os passos para iniciar sessão fora do Azure e remover as linhas de script que criam atribuições de funções de utilizador. Substitua UserA@azure.com e em todos os UserB@azure.com seguintes scripts pelos nomes de utilizador que está a utilizar para UserA e UserB. Complete os seguintes passos utilizando o CLI clássico do Azure e o CLI Azure. Pode completar os passos a partir do Azure Cloud Shell, selecionando apenas o botão **Try it** em qualquer um dos passos seguintes, ou instalando o CLI e [CLI](/cli/azure/install-azure-cli) [clássicos](/cli/azure/install-classic-cli) e executando os comandos no seu computador local.

1. Se utilizar a Cloud Shell, salte para o passo 2, porque a Cloud Shell automaticamente assina-o no Azure. Abra uma sessão de comando e inscreva-se em Azure usando o `azure login` comando.
2. Executar o CLI clássico no modo de Gestão de Serviços inserindo o `azure config mode asm` comando.
3. Insira o seguinte comando clássico do CLI para criar a rede virtual (clássico):

    ```console
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

4. Os restantes passos devem ser preenchidos com uma casca de bata com o CLI Azure (não o CLI clássico).
5. Copie o seguinte script para um editor de texto no seu PC. `<SubscriptionB-Id>`Substitua-o pelo ID de subscrição. Se não conhece o seu ID de assinatura, insira o `az account show` comando. O valor para **o id** na saída é o seu Id de subscrição. Copiar o script modificado, colá-lo na sua sessão de CLI e, em seguida, premir `Enter` .

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    Quando criou a rede virtual (clássica) no passo 4, o Azure criou a rede virtual no grupo de recursos *de Rede Padrão.*
6. Faça login no UserB do Azure e faça login como UserA no CLI.
7. Criar um grupo de recursos e uma rede virtual (Gestor de Recursos). Copie o seguinte script, cole-o na sua sessão de CLI e, em seguida, prima `Enter` .

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

8. Crie uma rede virtual de observação entre as duas redes virtuais criadas através dos diferentes modelos de implementação. Copie o seguinte script para um editor de texto no seu PC. `<SubscriptionB-id>`Substitua-o pelo ID da subscrição. Se não conhece o seu ID de assinatura, insira o `az account show` comando. O valor para **id** na saída é a sua subscrição Id. Azure criou a rede virtual (clássica) que criou no passo 4 num grupo de recursos chamado *Default-Networking*. Cole o script modificado na sua sessão de CLI e, em seguida, prima `Enter` .

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

9. Após a execução do script, reveja o espreitamento da rede virtual (Gestor de Recursos). Copie o seguinte script e, em seguida, cole-o na sua sessão de CLI:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```

    A saída mostra **conectado** na coluna **PeeringState.**

    Quaisquer recursos Azure que crie em qualquer rede virtual são agora capazes de comunicar uns com os outros através dos seus endereços IP. Se estiver a utilizar a resolução de nomeS Azure padrão para as redes virtuais, os recursos nas redes virtuais não são capazes de resolver nomes através das redes virtuais. Se quiser resolver nomes através de redes virtuais num espreitê-lo, tem de criar o seu próprio servidor DNS. Saiba como configurar a [resolução Name utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

10. **Opcional**: Embora a criação de máquinas virtuais não esteja abrangida por este tutorial, pode criar uma máquina virtual em cada rede virtual e ligar-se de uma máquina virtual à outra, para validar a conectividade.
11. **Opcional**: Para eliminar os recursos que cria neste tutorial, complete os passos em [Eliminar recursos](#delete-cli) neste artigo.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Criar olhando - PowerShell

Este tutorial utiliza contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todas as etapas, saltar os passos para iniciar sessão fora do Azure e remover as linhas de script que criam atribuições de funções de utilizador. Substitua UserA@azure.com e em todos os UserB@azure.com seguintes scripts pelos nomes de utilizador que está a utilizar para UserA e UserB. 

1. Instale a versão mais recente dos módulos PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) e [Az.](https://www.powershellgallery.com/packages/Az) Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão PowerShell.
3. No PowerShell, inicie sessão na subscrição do UserB como UserB, introduzindo o `Add-AzureAccount` comando. A conta com que inicia sessão deve ter as permissões necessárias para criar uma rede virtual de observação. Para obter uma lista de permissões, consulte [permissões de espreitar rede virtual](virtual-network-manage-peering.md#permissions).
4. Para criar uma rede virtual (clássica) com o PowerShell, tem de criar um novo, ou modificar um ficheiro de configuração de rede existente. Saiba como [exportar, atualizar e importar ficheiros de configuração da rede](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file). O ficheiro deve incluir o seguinte elemento **VirtualNetworkSite** para a rede virtual utilizada neste tutorial:

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

5. Inicie sessão na subscrição do UserB como UserB para utilizar comandos do Gestor de Recursos inserindo o `Connect-AzAccount` comando.
6. Atribua permissões do UtilizadorA à rede virtual B. Copie o seguinte script a um editor de texto no seu PC e `<SubscriptionB-id>` substitua-o pelo ID da subscrição B. Se não conhece o ID da assinatura, insira o `Get-AzSubscription` comando para o visualizar. O valor **para id** na saída devolvida é o seu ID de subscrição. O Azure criou a rede virtual (clássica) que criou no passo 4 num grupo de recursos chamado *Default-Networking*. Para executar o script, copie o script modificado, cole-o no PowerShell e, em seguida, prima `Enter` .

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Faça login no Azure como UserB e inicie sessão na subscrição do UserA como UserA, introduzindo o `Connect-AzAccount` comando. A conta com que inicia sessão deve ter as permissões necessárias para criar uma rede virtual de observação. Para obter uma lista de permissões, consulte [permissões de espreitar rede virtual](virtual-network-manage-peering.md#permissions).
8. Crie a rede virtual (Gestor de Recursos) copiando o seguinte script, colando-o para o PowerShell e, em seguida, `Enter` pressionando:

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

9. Atribua permissões userB à myVnetA. Copie o seguinte script para um editor de texto no seu PC e `<SubscriptionA-Id>` substitua-o pelo ID da assinatura A. Se não conhece o ID da assinatura, insira o `Get-AzSubscription` comando para o visualizar. O valor **para id** na saída devolvida é o seu ID de subscrição. Cole a versão modificada do script no PowerShell e, em seguida, pressione `Enter` para executá-la.

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Copie o seguinte script para um editor de texto no seu PC e `<SubscriptionB-id>` substitua-o pelo ID da subscrição B. Para espreitar o myVnetA para o myVNetB, copie o script modificado, cole-o no PowerShell e, em seguida, prima `Enter` .

    ```powershell
    Add-AzVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Veja o estado de observação do myVnetA copiando o seguinte script, colando-o em PowerShell e pressionando `Enter` .

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    O estado está **ligado.** Muda para **Connected** assim que configurar o espreitamento para o myVnetA a partir do myVnetB.

    Quaisquer recursos Azure que crie em qualquer rede virtual são agora capazes de comunicar uns com os outros através dos seus endereços IP. Se estiver a utilizar a resolução de nomeS Azure padrão para as redes virtuais, os recursos nas redes virtuais não são capazes de resolver nomes através das redes virtuais. Se quiser resolver nomes através de redes virtuais num espreitê-lo, tem de criar o seu próprio servidor DNS. Saiba como configurar a [resolução Name utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

12. **Opcional**: Embora a criação de máquinas virtuais não esteja abrangida por este tutorial, pode criar uma máquina virtual em cada rede virtual e ligar-se de uma máquina virtual à outra, para validar a conectividade.
13. **Opcional**: Para eliminar os recursos que cria neste tutorial, complete os passos em [Eliminar recursos](#delete-powershell) neste artigo.

## <a name="delete-resources"></a><a name="delete"></a>Eliminar recursos
Quando terminar este tutorial, talvez queira apagar os recursos que criou no tutorial, para não incorrer em taxas de utilização. A eliminação de um grupo de recursos também elimina todos os recursos que estão no grupo de recursos.

### <a name="azure-portal"></a><a name="delete-portal"></a>Portal Azure

1. Na caixa de pesquisa do portal, insira **o myResourceGroupA**. Nos resultados da pesquisa, clique **no myResourceGroupA**.
2. Na lâmina **myResourceGroupA,** clique no ícone **Eliminar.**
3. Para confirmar a eliminação, na caixa **TYPE THE RESOURCE GROUP NAME,** introduza o **myResourceGroupA** e, em seguida, clique em **Eliminar**.
4. Na caixa **de recursos de busca** no topo do portal, digite *myVnetB*. Clique **no myVnetB** quando aparecer nos resultados da pesquisa. Aparece uma lâmina para a rede virtual **myVnetB.**
5. Na lâmina **myVnetB,** clique em **Eliminar**.
6. Para confirmar a eliminação, clique em **Sim** na caixa **de rede virtual Delete.**

### <a name="azure-cli"></a><a name="delete-cli"></a>CLI do Azure

1. Faça login no Azure utilizando o CLI para eliminar a rede virtual (Gestor de Recursos) com o seguinte comando:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Inscreva-se no Azure utilizando o CLI clássico para eliminar a rede virtual (clássica) com os seguintes comandos:

   ```console
   azure config mode asm

   azure network vnet delete --vnet myVnetB --quiet
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. Na solicitação de comando PowerShell, insira o seguinte comando para eliminar a rede virtual (Gestor de Recursos):

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -Force
   ```

2. Para eliminar a rede virtual (clássica) com o PowerShell, tem de modificar um ficheiro de configuração de rede existente. Saiba como [exportar, atualizar e importar ficheiros de configuração da rede](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file). Remova o seguinte elemento VirtualNetworkSite para a rede virtual utilizada neste tutorial:

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
   > Importar um ficheiro de configuração de rede alterado pode causar alterações nas redes virtuais existentes (clássicas) na sua subscrição. Certifique-se de que remove apenas a rede virtual anterior e que não altera ou remove quaisquer outras redes virtuais existentes da sua subscrição. 

## <a name="next-steps"></a>Passos seguintes

- Familiarize-se completamente com [importantes restrições e comportamentos de observação de rede virtuais](virtual-network-manage-peering.md#requirements-and-constraints) antes de criar uma rede virtual que espreita para uso de produção.
- Saiba mais sobre todas as [definições de observação de rede virtual](virtual-network-manage-peering.md#create-a-peering).
- Aprenda a [criar um hub e falou topologia de rede](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) com olhando a rede virtual.