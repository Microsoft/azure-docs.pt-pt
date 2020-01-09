---
title: Criar um emparelhamento de rede virtual do Azure-Resource Manager-assinaturas diferentes
titlesuffix: Azure Virtual Network
description: Saiba como criar um emparelhamento de rede virtual entre redes virtuais criadas por meio do Resource Manager que existem em diferentes assinaturas do Azure.
services: virtual-network
documentationcenter: ''
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2019
ms.author: anavin
ms.openlocfilehash: dd1d930fa09e3e53a4ac67e513ba1bff77ee1376
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75373367"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Criar um emparelhamento de rede virtual – Gerenciador de recursos, assinaturas diferentes

Neste tutorial, você aprenderá a criar um emparelhamento de rede virtual entre redes virtuais criadas por meio do Resource Manager. As redes virtuais existem em assinaturas diferentes. O emparelhamento de duas redes virtuais permite que os recursos em diferentes redes virtuais se comuniquem entre si com a mesma largura de banda e latência como se os recursos estivessem na mesma rede virtual. Saiba mais sobre o [emparelhamento de rede virtual](virtual-network-peering-overview.md).

As etapas para criar um emparelhamento de rede virtual são diferentes, dependendo se as redes virtuais estão na mesma ou em diferentes assinaturas e em qual [modelo de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) as redes virtuais são criadas. Saiba como criar um emparelhamento de rede virtual em outros cenários selecionando o cenário da tabela a seguir:

|Modelo de implementação do Azure  | Subscrição do Azure  |
|--------- |---------|
|[O Gerenciador de recursos](tutorial-connect-virtual-networks-portal.md) |Idêntica|
|[Um Gerenciador de recursos, um clássico](create-peering-different-deployment-models.md) |Idêntica|
|[Um Gerenciador de recursos, um clássico](create-peering-different-deployment-models-subscriptions.md) |Outros|

Um emparelhamento de rede virtual não pode ser criado entre duas redes virtuais implantadas por meio do modelo de implantação clássico. Se você precisar conectar redes virtuais que foram criadas por meio do modelo de implantação clássico, você pode usar um [Gateway de VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure para conectar as redes virtuais.

Este tutorial emparelha redes virtuais na mesma região. Você também pode emparelhar redes virtuais em diferentes [regiões com suporte](virtual-network-manage-peering.md#cross-region). É recomendável que você se familiarize com os [requisitos e as restrições de emparelhamento](virtual-network-manage-peering.md#requirements-and-constraints) antes de emparelhar redes virtuais.

Você pode usar o [portal do Azure](#portal), a CLI ( [interface de linha de comando](#cli) ) do Azure, o Azure [PowerShell](#powershell)ou um [modelo de Azure Resource Manager](#template) para criar um emparelhamento de rede virtual. Selecione qualquer um dos links de ferramentas anteriores para ir diretamente para as etapas para criar um emparelhamento de rede virtual usando sua ferramenta de escolha.

Se as redes virtuais estiverem em assinaturas diferentes e as assinaturas estiverem associadas a locatários Azure Active Directory diferentes, conclua as seguintes etapas antes de continuar:
1. Adicione o usuário de cada locatário Active Directory como um [usuário convidado](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) no locatário de Azure Active Directory oposto.
1. Cada usuário deve aceitar o convite de usuário convidado do locatário de Azure Active Directory oposto.

## <a name="portal"></a>Criar emparelhamento-portal do Azure

As etapas a seguir usam contas diferentes para cada assinatura. Se você estiver usando uma conta que tenha permissões para ambas as assinaturas, poderá usar a mesma conta para todas as etapas, ignorar as etapas para fazer logoff do portal e ignorar as etapas para atribuir outras permissões de usuário para as redes virtuais.

1. Faça logon no [portal do Azure](https://portal.azure.com) como *UserA*. A conta com a qual você faz logon deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Para obter uma lista de permissões, consulte [permissões de emparelhamento de rede virtual](virtual-network-manage-peering.md#permissions).
2. Selecione **+ criar um recurso**, selecione **rede**e, em seguida, selecione **rede virtual**.
3. Selecione ou insira os seguintes valores de exemplo para as seguintes configurações e, em seguida, selecione **criar**:
    - **Name**: *myVnetA*
    - **Espaço de endereço**: *10.0.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - **Intervalo de endereços da sub-rede**: *10.0.0.0/24*
    - **Assinatura**: selecione A assinatura A.
    - **Grupo de recursos**: selecione **criar novo** e insira *myResourceGroupA*
    - **Local**: *leste dos EUA*
4. Na caixa **Pesquisar recursos** na parte superior do portal, digite *myVnetA*. Selecione **myVnetA** quando ele aparecer nos resultados da pesquisa. 
5. Selecione **controle de acesso (iam)** na lista vertical de opções no lado esquerdo.
6. Em **myVnetA-controle de acesso (iam)** , selecione **+ Adicionar atribuição de função**.
7. Selecione **colaborador de rede** na caixa **função** .
8. Na caixa **selecionar** , selecione *UserB*ou digite o endereço de email de UserB para procurá-lo.
9. Selecione **Guardar**.
10. Em **myVnetA-controle de acesso (iam)** , selecione **Propriedades** na lista vertical de opções no lado esquerdo. Copie a **ID do recurso**, que é usada em uma etapa posterior. A ID do recurso é semelhante ao exemplo a seguir: `/subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA`.
11. Faça logoff do portal como UserA e, em seguida, faça logon como UserB.
12. Conclua as etapas 2-3, inserindo ou selecionando os seguintes valores na etapa 3:

    - **Name**: *myVnetB*
    - **Espaço de endereço**: *10.1.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - **Intervalo de endereços da sub-rede**: *10.1.0.0/24*
    - **Assinatura**: selecione a assinatura B.
    - **Grupo de recursos**: selecione **criar novo** e insira *myResourceGroupB*
    - **Local**: *leste dos EUA*

13. Na caixa **Pesquisar recursos** na parte superior do portal, digite *myVnetB*. Selecione **myVnetB** quando ele aparecer nos resultados da pesquisa.
14. Em **myVnetB**, selecione **Propriedades** na lista vertical de opções no lado esquerdo. Copie a **ID do recurso**, que é usada em uma etapa posterior. A ID do recurso é semelhante ao exemplo a seguir: `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`.
15. Selecione **controle de acesso (iam)** em **myVnetB**e conclua as etapas 5-10 para MyVnetB, inserindo **UserA** na etapa 8.
16. Faça logoff do portal como UserB e faça logon como UserA.
17. Na caixa **Pesquisar recursos** na parte superior do portal, digite *myVnetA*. Selecione **myVnetA** quando ele aparecer nos resultados da pesquisa.
18. Selecione **myVnetA**.
19. Em **configurações**, selecione **emparelhamentos**.
20. Em **myVnetA-emparelhamentos**, selecione **+ Adicionar**
21. Em **Adicionar emparelhamento**, insira ou selecione as seguintes opções e, em seguida, selecione **OK**:
     - **Name**: *myVnetAToMyVnetB*
     - **Modelo de implantação de rede virtual**: selecione **Gerenciador de recursos**.
     - **Sei minha ID de recurso**: Marque esta caixa.
     - **ID do recurso**: Insira a ID do recurso da etapa 14.
     - **Permitir acesso à rede virtual:** Verifique se **habilitado** está selecionado.
    Nenhuma outra configuração é usada neste tutorial. Para saber mais sobre todas as configurações de emparelhamento, leia [gerenciar emparelhamentos de rede virtual](virtual-network-manage-peering.md#create-a-peering).
22. O emparelhamento que você criou aparece uma breve espera depois de selecionar **OK** na etapa anterior. **Iniciado** é listado na coluna **status de emparelhamento** para o emparelhamento **myVnetAToMyVnetB** que você criou. Você myVnetAu a myVnetB, mas agora você deve emparelhar myVnetB para myVnetA. O emparelhamento deve ser criado em ambas as direções para permitir que os recursos nas redes virtuais se comuniquem entre si.
23. Faça logoff do portal como UserA e faça logon como UserB.
24. Conclua as etapas 17-21 novamente para myVnetB. Na etapa 21, nomeie o emparelhamento *myVnetBToMyVnetA*, selecione *MyVnetA* para **rede virtual**e insira a ID da etapa 10 na caixa ID do **recurso** .
25. Alguns segundos depois de selecionar **OK** para criar o emparelhamento para myVnetB, o emparelhamento de **myVnetBToMyVnetA** que você acabou de criar é listado com **conectado** na coluna **status de emparelhamento** .
26. Faça logoff do portal como UserB e faça logon como UserA.
27. Conclua as etapas 17-19 novamente. O **status de emparelhamento** para o emparelhamento **myVnetAToVNetB** agora também está **conectado**. O emparelhamento é estabelecido com êxito depois que você vê **conectado** na coluna **status de emparelhamento** para ambas as redes virtuais no emparelhamento. Todos os recursos do Azure criados em qualquer rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nome do Azure padrão para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você quiser resolver nomes entre redes virtuais em um emparelhamento, você deve criar seu próprio servidor DNS. Saiba como configurar a [resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você pode criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual para a outra, para validar a conectividade.
29. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas na seção [excluir recursos](#delete-portal) deste artigo.

## <a name="cli"></a>Criar emparelhamento-CLI do Azure

Este tutorial usa contas diferentes para cada assinatura. Se você estiver usando uma conta que tenha permissões para ambas as assinaturas, poderá usar a mesma conta para todas as etapas, ignorar as etapas para fazer logoff do Azure e remover as linhas de script que criam atribuições de função de usuário. Substitua UserA@azure.com e UserB@azure.com em todos os scripts a seguir pelos nomes de acessados que você está usando para UserA e UserB. 

Os seguintes scripts:

- Requer o CLI do Azure versão 2.0.4 ou posterior. Para localizar a versão, execute `az --version`. Se você precisar atualizar, consulte [instalar CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Funciona em um shell bash. Para obter as opções de execução de scripts da CLI do Azure num cliente Windows, veja [Instalar a CLI do Azure no Windows](/cli/azure/install-azure-cli-windows).

Em vez de instalar a CLI e suas dependências, você pode usar o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Selecione o botão **experimentar** no script a seguir, que invoca um Cloud Shell no qual você pode fazer logon em sua conta do Azure com.

1. Abra uma sessão da CLI e faça logon no Azure como UserA usando o comando `azure login`. A conta com a qual você faz logon deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Para obter uma lista de permissões, consulte [permissões de emparelhamento de rede virtual](virtual-network-manage-peering.md#permissions).
2. Copie o script a seguir em um editor de texto em seu computador, substitua `<SubscriptionA-Id>` pela ID da assinaturaA e copie o script modificado, Cole-o na sessão da CLI e pressione `Enter`. Se você não souber sua ID de assinatura, insira o comando `az account show`. O valor de **ID** na saída é sua ID de assinatura.

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

3. Faça logoff do Azure como UserA usando o comando `az logout` e, em seguida, faça logon no Azure como UserB. A conta com a qual você faz logon deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Para obter uma lista de permissões, consulte [permissões de emparelhamento de rede virtual](virtual-network-manage-peering.md#permissions).
4. Criar myVnetB. Copie o conteúdo do script na etapa 2 para um editor de texto em seu computador. Substitua `<SubscriptionA-Id>` pela ID de SubscriptionB. Altere 10.0.0.0/16 para 10.1.0.0/16, altere tudo como para B e todos os BS para a. Copie o script modificado, Cole-o em sua sessão da CLI e pressione `Enter`.
5. Faça logoff do Azure como UserB e faça logon no Azure como UserA.
6. Crie um emparelhamento de rede virtual de myVnetA para myVnetB. Copie o conteúdo do script a seguir em um editor de texto em seu computador. Substitua `<SubscriptionB-Id>` pela ID de SubscriptionB. Para executar o script, copie o script modificado, Cole-o na sessão da CLI e pressione Enter.

    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)

        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet-id /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. Exiba o estado de emparelhamento de myVnetA.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    O estado é **iniciado**. Ele muda para **conectado** depois que você cria o emparelhamento para MyVnetA de myVnetB.

8. Faça logoff do UserA do Azure e faça logon no Azure como UserB.
9. Crie o emparelhamento de myVnetB para myVnetA. Copie o conteúdo do script na etapa 6 para um editor de texto em seu computador. Substitua `<SubscriptionB-Id>` pela ID da assinaturaA e altere tudo como para B e todas as BS para a. Depois de fazer as alterações, copie o script modificado, Cole-o na sessão da CLI e pressione `Enter`.
10. Exiba o estado de emparelhamento de myVnetB. Copie o conteúdo do script na etapa 7 para um editor de texto em seu computador. Altere a para B para o grupo de recursos e os nomes de rede virtual, copie o script, Cole o script modificado em na sessão da CLI e, em seguida, pressione `Enter`. O estado de emparelhamento é **conectado**. O estado de emparelhamento de myVnetA muda para **conectado** depois que você criou o emparelhamento de MyVnetB para myVnetA. Você pode fazer logon UserA novamente no Azure e concluir a etapa 7 novamente para verificar o estado de emparelhamento de myVnetA. 

    > [!NOTE]
    > O emparelhamento não é estabelecido até que o estado de emparelhamento seja **conectado** para ambas as redes virtuais.

11. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você pode criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual para a outra, para validar a conectividade.
12. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas em [excluir recursos](#delete-cli) neste artigo.

Todos os recursos do Azure criados em qualquer rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nome do Azure padrão para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você quiser resolver nomes entre redes virtuais em um emparelhamento, você deve criar seu próprio servidor DNS. Saiba como configurar a [resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="powershell"></a>Criar emparelhamento-PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este tutorial usa contas diferentes para cada assinatura. Se você estiver usando uma conta que tenha permissões para ambas as assinaturas, poderá usar a mesma conta para todas as etapas, ignorar as etapas para fazer logoff do Azure e remover as linhas de script que criam atribuições de função de usuário. Substitua UserA@azure.com e UserB@azure.com em todos os scripts a seguir pelos nomes de acessados que você está usando para UserA e UserB.

1. Confirme se você tem Azure PowerShell versão 1.0.0 ou superior. Você pode fazer isso executando o `Get-Module -Name Az` é recomendável instalar a versão mais recente do [módulo AZ](/powershell/azure/install-az-ps)do PowerShell. Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json). 
2. Inicie uma sessão do PowerShell.
3. No PowerShell, faça logon no Azure como UserA digitando o comando `Connect-AzAccount`. A conta com a qual você faz logon deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Para obter uma lista de permissões, consulte [permissões de emparelhamento de rede virtual](virtual-network-manage-peering.md#permissions).
4. Crie um grupo de recursos e uma rede virtual A. Copie o script a seguir em um editor de texto em seu computador. Substitua `<SubscriptionA-Id>` pela ID da assinaturaA. Se você não souber sua ID de assinatura, insira o comando `Get-AzSubscription` para exibi-la. O valor de **ID** na saída retornada é sua ID de assinatura. Para executar o script, copie o script modificado, Cole-o no PowerShell e, em seguida, pressione `Enter`.

    ```powershell
    # Create a resource group.
    New-AzResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. Faça logoff do UserA do Azure e faça logon no UserB. A conta com a qual você faz logon deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Para obter uma lista de permissões, consulte [permissões de emparelhamento de rede virtual](virtual-network-manage-peering.md#permissions).
6. Copie o conteúdo do script na etapa 4 para um editor de texto em seu computador. Substitua `<SubscriptionA-Id>` pela ID da assinatura B. Altere 10.0.0.0/16 para 10.1.0.0/16. Altere tudo como para B e todo o BS para a. Para executar o script, copie o script modificado, Cole-o no PowerShell e, em seguida, pressione `Enter`.
7. Faça logoff do UserB do Azure e faça logon no UserA.
8. Crie o emparelhamento de myVnetA para myVnetB. Copie o script a seguir em um editor de texto em seu computador. Substitua `<SubscriptionB-Id>` pela ID da assinatura B. Para executar o script, copie o script modificado, Cole-o no PowerShell e, em seguida, pressione `Enter`.

   ```powershell
   # Peer myVnetA to myVnetB.
   $vNetA=Get-AzVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
   Add-AzVirtualNetworkPeering `
     -Name 'myVnetAToMyVnetB' `
     -VirtualNetwork $vNetA `
     -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
   ```

9. Exiba o estado de emparelhamento de myVnetA.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    O estado é **iniciado**. Ele muda para **conectado** depois que você configura o emparelhamento para MyVnetA de myVnetB.

10. Faça logoff do UserA do Azure e faça logon no UserB.
11. Crie o emparelhamento de myVnetB para myVnetA. Copie o conteúdo do script na etapa 8 para um editor de texto em seu computador. Substitua `<SubscriptionB-Id>` pela ID da assinatura A e altere tudo como para B e todas as BS para a. Para executar o script, copie o script modificado, Cole-o no PowerShell e, em seguida, pressione `Enter`.
12. Exiba o estado de emparelhamento de myVnetB. Copie o conteúdo do script na etapa 9 para um editor de texto em seu computador. Altere a para B para o grupo de recursos e os nomes de rede virtual. Para executar o script, Cole o script modificado no PowerShell e, em seguida, pressione `Enter`. O estado é **conectado**. O estado de emparelhamento de **myVnetA** muda para **conectado** depois que você criou o emparelhamento de **myVnetB** para **myVnetA**. Você pode registrar UserA novamente no Azure e concluir a etapa 9 novamente para verificar o estado de emparelhamento de myVnetA.

    > [!NOTE]
    > O emparelhamento não é estabelecido até que o estado de emparelhamento seja **conectado** para ambas as redes virtuais.

    Todos os recursos do Azure criados em qualquer rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nome do Azure padrão para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você quiser resolver nomes entre redes virtuais em um emparelhamento, você deve criar seu próprio servidor DNS. Saiba como configurar a [resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você pode criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual para a outra, para validar a conectividade.
14. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas em [excluir recursos](#delete-powershell) neste artigo.

## <a name="template"></a>Criar emparelhamento-modelo do Resource Manager

1. Para criar uma rede virtual e atribuir as [permissões](virtual-network-manage-peering.md#permissions)apropriadas, conclua as etapas nas seções [portal](#portal), [CLI do Azure](#cli)ou [PowerShell](#powershell) deste artigo.
2. Salve o texto a seguir em um arquivo no computador local. Substitua `<subscription ID>` pela ID da assinatura do UserA. Você pode salvar o arquivo como vnetpeeringA. JSON, por exemplo.

   ```json
   {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
    "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
   }
   ```

3. Faça logon no Azure como UserA e implante o modelo usando o [portal](../azure-resource-manager/templates/deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), o [PowerShell](../azure-resource-manager/templates/deploy-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template)ou o [CLI do Azure](../azure-resource-manager/templates/deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Especifique o nome do arquivo que você salvou o texto JSON de exemplo na etapa 2 para.
4. Copie o JSON de exemplo da etapa 2 para um arquivo no computador e faça alterações nas linhas que começam com:
   - **nome**: Altere *myVnetA/myVnetAToMyVnetB* para *myVnetB/myVnetBToMyVnetA*.
   - **ID**: substitua `<subscription ID>` pela ID da assinatura do UserB e altere *myVnetB* para *myVnetA*.
5. Conclua a etapa 3 novamente, conectado ao Azure como UserB.
6. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você pode criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual para a outra, para validar a conectividade.
7. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas na seção [excluir recursos](#delete) deste artigo, usando o portal do Azure, o PowerShell ou o CLI do Azure.

## <a name="delete"></a>Excluir recursos
Ao concluir este tutorial, talvez você queira excluir os recursos criados no tutorial, para que não incorra em encargos de uso. A exclusão de um grupo de recursos também exclui todos os recursos que estão no grupo de recursos.

### <a name="delete-portal"></a>Portal do Azure

1. Faça logon no portal do Azure como UserA.
2. Na caixa de pesquisa do portal, digite **myResourceGroupA**. Nos resultados da pesquisa, selecione **myResourceGroupA**.
3. Selecione **Eliminar**.
4. Para confirmar a exclusão, na caixa **digite o nome do grupo de recursos** , digite **myResourceGroupA**e, em seguida, selecione **excluir**.
5. Faça logoff do portal como UserA e faça logon como UserB.
6. Conclua as etapas 2-4 para myResourceGroupB.

### <a name="delete-cli"></a>CLI do Azure

1. Faça logon no Azure como UserA e execute o seguinte comando:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Faça logoff do Azure como UserA e faça logon como UserB.
3. Execute o seguinte comando:

   ```azurecli-interactive
   az group delete --name myResourceGroupB --yes
   ```

### <a name="delete-powershell"></a>PowerShell

1. Faça logon no Azure como UserA e execute o seguinte comando:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -force
   ```

2. Faça logoff do Azure como UserA e faça logon como UserB.
3. Execute o seguinte comando:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupB -force
   ```

## <a name="next-steps"></a>Passos seguintes

- Familiarize-se totalmente com [restrições e comportamentos importantes de emparelhamento de rede virtual](virtual-network-manage-peering.md#requirements-and-constraints) antes de criar um emparelhamento de rede virtual para uso em produção.
- Saiba mais sobre todas [as configurações de emparelhamento de rede virtual](virtual-network-manage-peering.md#create-a-peering).
- Saiba como [criar uma topologia de rede de Hub e spoke com o](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) emparelhamento de rede virtual.
