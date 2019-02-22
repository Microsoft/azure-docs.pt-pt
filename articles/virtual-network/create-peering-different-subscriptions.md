---
title: Criar uma Azure virtual network peering - Resource Manager - subscrições diferentes
titlesuffix: Azure Virtual Network
description: Saiba como criar uma rede virtual peering entre redes virtuais criadas através do Resource Manager que existam em diferentes subscrições do Azure.
services: virtual-network
documentationcenter: ''
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: jdial;anavin
ms.openlocfilehash: 5ce816c9e8bec716de840cc5f6fdd546b6abd298
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649786"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Criar um peering de rede virtual - Gestor de recursos, subscrições diferentes

Neste tutorial, vai aprender a criar uma rede virtual peering entre redes virtuais criadas através do Resource Manager. Existem as redes virtuais em diferentes subscrições. Peering dois recursos de permite de redes virtuais em redes virtuais diferentes para comunicar entre si com o mesmo da largura de banda e latência, como se os recursos estivessem na mesma rede virtual. Saiba mais sobre [peering de rede Virtual](virtual-network-peering-overview.md).

Os passos para criar um peering de rede virtual são diferentes, dependendo se as redes virtuais estão no mesmo, ou em diferentes, subscrições e qual [modelo de implementação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) as redes virtuais forem criadas com. Saiba como criar uma rede virtual peering em outros cenários ao selecionar o cenário da tabela seguinte:

|Modelo de implementação do Azure  | Subscrição do Azure  |
|--------- |---------|
|[Ambas com Resource Manager](tutorial-connect-virtual-networks-portal.md) |Mesmo|
|[Uma com Resource Manager, outra com clássica](create-peering-different-deployment-models.md) |Mesmo|
|[Uma com Resource Manager, outra com clássica](create-peering-different-deployment-models-subscriptions.md) |Diferente|

Não é possível criar um peering de rede virtual entre duas redes virtuais implementadas através do modelo de implementação clássica. Se precisar de ligar redes virtuais que foram ambas criadas por meio do modelo de implementação clássica, pode utilizar do Azure [Gateway de VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para ligar as redes virtuais.

Este tutorial elementos redes virtuais na mesma região. Pode também configurar o peering entre redes virtuais em diferentes [regiões suportadas](virtual-network-manage-peering.md#cross-region). É recomendável que se familiarizar com o [peering requisitos e restrições](virtual-network-manage-peering.md#requirements-and-constraints) antes peering de redes virtuais.

Pode utilizar o [portal do Azure](#portal), o Azure [interface de linha de comandos](#cli) (CLI), Azure [PowerShell](#powershell), ou um [modelo Azure Resource Manager](#template)para criar um peering de rede virtual. Selecione qualquer uma das ligações de ferramenta anterior para ir diretamente para os passos para criar um peering de rede virtual com a sua ferramenta preferencial.

## <a name="portal"></a>Criar peering - portal do Azure

Se as redes virtuais que pretende configurar o peering estão em subscrições que estão associadas aos diferentes inquilinos do Azure Active Directory, siga os passos na secção CLI e o PowerShell deste artigo. Portal não tem suporte para configurar o peering entre redes virtuais que pertencem a subscrições de diferentes inquilinos de diretório Active Directory.

Os seguintes passos utilizam contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todos os passos, ignore os passos para o registo de fora do portal do e ignore os passos para atribuir permissões de outro utilizador para as redes virtuais.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como *UserA*. A conta, que inicie sessão com tem de ter as permissões necessárias para criar um peering de rede virtual. Para obter uma lista de permissões, consulte [permissões de peering de rede Virtual](virtual-network-manage-peering.md#permissions).
2. Selecione **+ criar um recurso**, selecione **redes**e, em seguida, selecione **rede Virtual**.
3. Selecione ou introduza os seguintes valores de exemplo para as seguintes definições, em seguida, selecione **criar**:
    - **Name**: *myVnetA*
    - **Espaço de endereços**: *10.0.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - **Intervalo de endereços da sub-rede**: *10.0.0.0/24*
    - **Subscrição**: Selecione a subscrição A.
    - **Grupo de recursos**: Selecione **criar novo** e introduza *myResourceGroupA*
    - **Localização**: *E.U.A. leste*
4. Na **recursos de pesquisa** caixa na parte superior do portal, tipo *myVnetA*. Selecione **myVnetA** quando for apresentada nos resultados da pesquisa. 
5. Selecione **controlo de acesso (IAM)** na vertical lista de opções no lado esquerdo.
6. Sob **myVnetA - controlo de acesso (IAM)**, selecione **+ adicionar atribuição de função**.
7. Selecione **contribuinte de rede** no **função** caixa.
8. Na **selecionar** caixa, selecione *UserB*, ou escreva o endereço de e-mail do User-b para pesquisá-la.
9. Selecione **Guardar**.
10. Sob **myVnetA - controlo de acesso (IAM)**, selecione **propriedades** na vertical lista de opções no lado esquerdo. Copiar o **ID de recurso**, que é utilizado num passo posterior. O ID de recurso é semelhante ao seguinte exemplo: /subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA.
11. Terminar sessão no portal como UserA, em seguida, inicie sessão como utilizador b.
12. Conclua os passos 2 a 3, introduzir ou selecionar os seguintes valores no passo 3:

    - **Name**: *myVnetB*
    - **Espaço de endereços**: *10.1.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - **Intervalo de endereços da sub-rede**: *10.1.0.0/24*
    - **Subscrição**: Selecione a subscrição B.
    - **Grupo de recursos**: Selecione **criar novo** e introduza *myResourceGroupB*
    - **Localização**: *E.U.A. leste*

13. Na **recursos de pesquisa** caixa na parte superior do portal, tipo *myVnetB*. Selecione **myVnetB** quando for apresentada nos resultados da pesquisa.
14. Sob **myVnetB**, selecione **propriedades** na vertical lista de opções no lado esquerdo. Copiar o **ID de recurso**, que é utilizado num passo posterior. O ID de recurso é semelhante ao seguinte exemplo: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB.
15. Selecione **controlo de acesso (IAM)** sob **myVnetB**e, em seguida, conclua os passos 5 a 10 para myVnetB, introduzir **UserA** no passo 8.
16. Terminar sessão no portal como UserB e iniciar sessão como utilizador.
17. Na **recursos de pesquisa** caixa na parte superior do portal, tipo *myVnetA*. Selecione **myVnetA** quando for apresentada nos resultados da pesquisa.
18. Selecione **myVnetA**.
19. Sob **configurações**, selecione **Peerings**.
20. Sob **myVnetA - Peerings**, selecione **+ adicionar**
21. Sob **adicionar peering**, introduza ou selecione as seguintes opções, em seguida, selecione **OK**:
     - **Name**: *myVnetAToMyVnetB*
     - **Modelo de implementação de rede virtual**:  Selecione o **Resource Manager**.
     - **Eu sei o ID de recurso**: Esta caixa de verificação.
     - **ID de recurso**: Introduza o ID de recurso do passo 14.
     - **Permitir o acesso de rede virtual:** Certifique-se de que **ativado** está selecionada.
    Não existem outras definições são utilizadas neste tutorial. Para saber mais sobre todas as definições de peering, leia [gerir peerings de rede virtual](virtual-network-manage-peering.md#create-a-peering).
22. O peering que criou é apresentada uma breve espera depois de selecionar **OK** no passo anterior. **Iniciada** é listado na **estado de PEERING** coluna para o **myVnetAToMyVnetB** peering é criado. Já em modo de peering myVnetA para myVnetB, mas agora deve fazer um ponto myVnetB para myVnetA. O peering tem de ser criado em ambas as direções para permitir recursos em redes virtuais para comunicar entre si.
23. Terminar sessão no portal como UserA e iniciar sessão como utilizador b.
24. Conclua passos 17 21 novamente para myVnetB. No passo 21, nomeie o peering *myVnetBToMyVnetA*, selecione *myVnetA* para **rede Virtual**e introduza o ID do passo 10 a **ID de recurso**caixa.
25. Alguns segundos depois de selecionar **OK** para criar o peering para myVnetB, o **myVnetBToMyVnetA** peering acabou de criar é listada com **ligado** no **Estado de PEERING** coluna.
26. Terminar sessão no portal como UserB e iniciar sessão como utilizador.
27. Conclua os passos 17-19 novamente. O **estado de PEERING** para o **myVnetAToVNetB** peering é agora também **ligado**. O peering é estabelecido com êxito depois de confirmar **ligado** no **estado de PEERING** coluna para ambas as redes virtuais no peering. Todos os recursos do Azure que cria em qualquer rede virtual agora são capazes de comunicar entre si através de seus endereços IP. Se estiver a utilizar a resolução de nomes do Azure do padrão para as redes virtuais, os recursos as redes virtuais não são capazes de resolver nomes entre as redes virtuais. Se pretender resolver nomes entre redes virtuais num modo de peering, tem de criar seu próprio servidor DNS. Saiba como configurar [resolução de nomes através de seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Opcional**: Embora a criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conectividade.
29. **Opcional**: Para eliminar os recursos que criar neste tutorial, conclua os passos a [eliminar recursos](#delete-portal) seção deste artigo.

## <a name="cli"></a>Criar peering - CLI do Azure

Este tutorial utiliza contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todos os passos, ignore os passos para o registo do Azure e remover as linhas do script que criar atribuições de funções de utilizador. Substitua UserA@azure.com e UserB@azure.com em todos os scripts seguintes com os nomes de utilizador estiver a utilizar para UserA e UserB. Se as redes virtuais estão em subscrições diferentes, e subscrições estejam associadas aos diferentes inquilinos do Azure Active Directory, conclua os seguintes passos antes de continuar:
 - Adicionar o utilizador a partir de cada inquilino do Active Directory como um [utilizador convidado](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) no inquilino do Azure Active Directory oposto.
 - Cada utilizador tem de aceitar o convite de utilizador convidado do inquilino do Azure Active Directory oposto.

Os seguintes scripts:

- Necessita da versão 2.0.4 da CLI do Azure ou posterior. Para localizar a versão, execute `az --version`. Se precisar de atualizar, veja [instalar a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Funciona numa Bash shell. Para obter as opções de execução de scripts da CLI do Azure num cliente Windows, veja [Instalar a CLI do Azure no Windows](/cli/azure/install-azure-cli-windows).

Em vez de instalar a CLI e as respetivas dependências, pode utilizar o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Selecione o **experimente** botão no script que se segue, que invoca um Shell de Cloud que pode iniciar sessão sua conta do Azure com.

1. Abra uma sessão CLI e inicie sessão no Azure como UserA usando o `azure login` comando. A conta, que inicie sessão com tem de ter as permissões necessárias para criar um peering de rede virtual. Para obter uma lista de permissões, consulte [permissões de peering de rede Virtual](virtual-network-manage-peering.md#permissions).
2. Copie o seguinte script para um editor de texto no seu PC, substitua `<SubscriptionA-Id>` com o ID de SubscriptionA, em seguida, copie o script modificado, cole-o na sua sessão da CLI e prima `Enter`. Se não souber o Id de subscrição, introduza o comando de 'az account show'. O valor para **id** no resultado é o seu ID de subscrição.

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

3. Termine a sessão do Azure como UserA usando o `az logout` de comando, em seguida, inicie sessão no Azure como o utilizador b. A conta, que inicie sessão com tem de ter as permissões necessárias para criar um peering de rede virtual. Para obter uma lista de permissões, consulte [permissões de peering de rede Virtual](virtual-network-manage-peering.md#permissions).
4. Crie myVnetB. Copie o conteúdo de script no passo 2 para um editor de texto no seu PC. Substitua `<SubscriptionA-Id>` com o ID de SubscriptionB. Alteração 10.0.0.0/16 10.1.0.0/16, alteração tudo em relação às B e todos os Bs para cópia de r. o script modificado, cole-o na sua sessão da CLI, e prima `Enter`.
5. Termine a sessão do Azure como UserB e inicie sessão no Azure como o utilizador.
6. Crie uma rede virtual peering de myVnetA a myVnetB. Copie o seguinte conteúdo de script para um editor de texto no seu PC. Substitua `<SubscriptionB-Id>` com o ID de SubscriptionB. Para executar o script, copie o script modificado, em seguida, cole-o para a sua sessão CLI e prima Enter.

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

7. Ver o estado do peering de myVnetA.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    O estado é **iniciado**. Ele é alterado para **ligado** depois de criar o peering para myVnetA myVnetB.

8. Termine do utilizador do Azure e iniciar sessão no Azure como o utilizador b.
9. Crie o peering de myVnetB a myVnetA. Copie o conteúdo de script no passo 6 para um editor de texto no seu PC. Substitua `<SubscriptionB-Id>` com o ID para SubscriptionA e alteração tudo em relação às B e todos os Bs para r. Depois de fazer as alterações, copie o script modificado, cole-a na sua sessão da CLI e prima `Enter`.
10. Ver o estado do peering de myVnetB. Copie o conteúdo de script no passo 7 para um editor de texto no seu PC. A alteração para o B para o grupo de recursos e os nomes de rede virtual, copie o script, cole o script modificado à sua sessão CLI e, em seguida, prima `Enter`. O estado do peering é **ligado**. O estado do peering de myVnetA é alterado para **ligado** depois de criar o peering de myVnetB a myVnetA. O utilizador pode iniciar sessão novamente para o Azure e completa passo 7 novamente para verificar o estado do peering de myVnetA. 

    > [!NOTE]
    > O peering é estabelecido não até que seja o estado do peering **ligado** para ambas as redes virtuais.

11. **Opcional**: Embora a criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conectividade.
12. **Opcional**: Para eliminar os recursos que criar neste tutorial, conclua os passos em [eliminar recursos](#delete-cli) neste artigo.

Todos os recursos do Azure que cria em qualquer rede virtual agora são capazes de comunicar entre si através de seus endereços IP. Se estiver a utilizar a resolução de nomes do Azure do padrão para as redes virtuais, os recursos as redes virtuais não são capazes de resolver nomes entre as redes virtuais. Se pretender resolver nomes entre redes virtuais num modo de peering, tem de criar seu próprio servidor DNS. Saiba como configurar [resolução de nomes através de seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="powershell"></a>Criar peering - PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este tutorial utiliza contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todos os passos, ignore os passos para o registo do Azure e remover as linhas do script que criar atribuições de funções de utilizador. Substitua UserA@azure.com e UserB@azure.com em todos os scripts seguintes com os nomes de utilizador estiver a utilizar para UserA e UserB.
Se as redes virtuais estão em subscrições diferentes, e subscrições estejam associadas aos diferentes inquilinos do Azure Active Directory, conclua os seguintes passos antes de continuar:
 - Adicionar o utilizador a partir de cada inquilino do Active Directory como um [utilizador convidado](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) no inquilino do Azure Active Directory oposto.
 - Cada utilizador tem de aceitar o convite de utilizador convidado do inquilino do Active Directory oposto.

1. Confirme se tem o Azure PowerShell versão 1.0.0 ou superior. Pode fazê-lo ao executar o `Get-Module -Name Az` Recomendamos que instale a versão mais recente do PowerShell [módulo Az](/powershell/azure/install-az-ps). Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json). 
2. Inicie uma sessão do PowerShell.
3. No PowerShell, inicie sessão no Azure como o utilizador ao introduzir o `Connect-AzAccount` comando. A conta, que inicie sessão com tem de ter as permissões necessárias para criar um peering de rede virtual. Para obter uma lista de permissões, consulte [permissões de peering de rede Virtual](virtual-network-manage-peering.md#permissions).
4. Crie um grupo de recursos e a rede virtual de cópia de r. o seguinte script para um editor de texto no seu PC. Substitua `<SubscriptionA-Id>` com o ID de SubscriptionA. Se não souber o Id de subscrição, introduza o `Get-AzSubscription` comando para exibi-la. O valor para **Id** no resultado retornado é o ID da subscrição. Para executar o script, copie o script modificado, cole-a no PowerShell e, em seguida, prima `Enter`.

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

5. Termine o utilizador do Azure e iniciar sessão UserB. A conta, que inicie sessão com tem de ter as permissões necessárias para criar um peering de rede virtual. Para obter uma lista de permissões, consulte [permissões de peering de rede Virtual](virtual-network-manage-peering.md#permissions).
6. Copie o conteúdo de script no passo 4 para um editor de texto no seu PC. Substitua `<SubscriptionA-Id>` com o ID da subscrição 10.0.0.0/16 alteração B. para 10.1.0.0/16. Alterar tudo em relação às B e todos os Bs para r. Para executar o script, copie o script modificado, cole no PowerShell e, em seguida, prima `Enter`.
7. Termine UserB do Azure e iniciar sessão de utilizador.
8. Crie o peering de myVnetA a myVnetB. Copie o seguinte script para um editor de texto no seu PC. Substitua `<SubscriptionB-Id>` com o ID da subscrição B. Para executar o script, copie o script modificado, cole no PowerShell e, em seguida, prima `Enter`.

   ```powershell
   # Peer myVnetA to myVnetB.
   $vNetA=Get-AzVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
   Add-AzVirtualNetworkPeering `
     -Name 'myVnetAToMyVnetB' `
     -VirtualNetwork $vNetA `
     -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
   ```

9. Ver o estado do peering de myVnetA.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    O estado é **iniciado**. Ele é alterado para **ligado** depois de configurar o peering para myVnetA myVnetB.

10. Termine o utilizador do Azure e iniciar sessão UserB.
11. Crie o peering de myVnetB a myVnetA. Copie o conteúdo de script no passo 8 para um editor de texto no seu PC. Substitua `<SubscriptionB-Id>` com o ID de subscrição A e alteração tudo em relação às B e todos os Bs para r. Para executar o script, copie o script modificado, cole-a no PowerShell e, em seguida, prima `Enter`.
12. Ver o estado do peering de myVnetB. Copie o conteúdo de script no passo 9 para um editor de texto no seu PC. Alterar A para B para o grupo de recursos e os nomes de rede virtual. Para executar o script, cole o script modificado no PowerShell e, em seguida, prima `Enter`. O estado é **ligado**. O estado do peering de **myVnetA** é alterado para **ligado** depois de criar o peering a partir **myVnetB** para **myVnetA**. O utilizador pode iniciar sessão novamente para o Azure e completa passo 9 novamente para verificar o estado do peering de myVnetA.

    > [!NOTE]
    > O peering é estabelecido não até que seja o estado do peering **ligado** para ambas as redes virtuais.

    Todos os recursos do Azure que cria em qualquer rede virtual agora são capazes de comunicar entre si através de seus endereços IP. Se estiver a utilizar a resolução de nomes do Azure do padrão para as redes virtuais, os recursos as redes virtuais não são capazes de resolver nomes entre as redes virtuais. Se pretender resolver nomes entre redes virtuais num modo de peering, tem de criar seu próprio servidor DNS. Saiba como configurar [resolução de nomes através de seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Opcional**: Embora a criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conectividade.
14. **Opcional**: Para eliminar os recursos que criar neste tutorial, conclua os passos em [eliminar recursos](#delete-powershell) neste artigo.

## <a name="template"></a>Criar peering - modelo do Resource Manager

Se as redes virtuais estão em subscrições diferentes, e subscrições estejam associadas aos diferentes inquilinos do Azure Active Directory, conclua os seguintes passos antes de continuar:
 - Adicionar o utilizador a partir de cada inquilino do Active Directory como um [utilizador convidado](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) no inquilino do Azure Active Directory oposto.
 - Cada utilizador tem de aceitar o convite de utilizador convidado do inquilino do Active Directory oposto.

1. Para criar uma rede virtual e atribuir o adequado [permissões](virtual-network-manage-peering.md#permissions), conclua os passos a [Portal](#portal), [da CLI do Azure](#cli), ou [PowerShell](#powershell)seções deste artigo.
2. Guarde o texto que se segue para um ficheiro no seu computador local. Substitua `<subscription ID>` com ID da subscrição. do User- Pode salvar o arquivo como vnetpeeringA.json, por exemplo.

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

3. Inicie sessão no Azure como UserA e implementar o modelo através da [portal](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-templates-stored-locally), ou o [da CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Especifique o nome de ficheiro que guardou o texto json de exemplo no passo 2 para.
4. Copie o json de exemplo do passo 2 para um ficheiro no seu computador e fazer alterações para as linhas que começam com:
   - **name**: Alteração *myVnetA/myVnetAToMyVnetB* ao *myVnetB/myVnetBToMyVnetA*.
   - **id**: Substitua `<subscription ID>` com o ID de subscrição e a alteração do User-b *myVnetB* para *myVnetA*.
5. Passo concluído 3 novamente, sessão iniciada no Azure como o utilizador b.
6. **Opcional**: Embora a criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conectividade.
7. **Opcional**: Para eliminar os recursos que criar neste tutorial, conclua os passos a [eliminar recursos](#delete) seção deste artigo, com o portal do Azure, PowerShell ou a CLI do Azure.

## <a name="delete"></a>Eliminar recursos
Quando tiver concluído este tutorial, pode querer eliminar os recursos que criou no tutorial, pelo que não a incorrer em custos de utilização. Eliminar um grupo de recursos também elimina a todos os recursos que estão no grupo de recursos.

### <a name="delete-portal"></a>Portal do Azure

1. Inicie sessão no portal do Azure como o utilizador.
2. Na caixa de pesquisa de portal, introduza **myResourceGroupA**. Nos resultados da pesquisa, selecione **myResourceGroupA**.
3. Selecione **Eliminar**.
4. Para confirmar a eliminação, no **tipo o nome de grupo de recursos** , introduza **myResourceGroupA**e, em seguida, selecione **eliminar**.
5. Terminar sessão no portal como UserA e iniciar sessão como utilizador b.
6. Conclua os passos 2 a 4 para myResourceGroupB.

### <a name="delete-cli"></a>CLI do Azure

1. Inicie sessão no Azure como UserA e execute o seguinte comando:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Termine a sessão do Azure como UserA e iniciar sessão como utilizador b.
3. Execute o seguinte comando:

   ```azurecli-interactive
   az group delete --name myResourceGroupB --yes
   ```

### <a name="delete-powershell"></a>PowerShell

1. Inicie sessão no Azure como UserA e execute o seguinte comando:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -force
   ```

2. Termine a sessão do Azure como UserA e iniciar sessão como utilizador b.
3. Execute o seguinte comando:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupB -force
   ```

## <a name="next-steps"></a>Passos Seguintes

- Familiarize-se totalmente com importantes [restrições de peering de rede virtual e comportamentos](virtual-network-manage-peering.md#requirements-and-constraints) antes de criar uma rede virtual peering para produção utilizar.
- Saiba tudo sobre [as definições de peering de rede virtual](virtual-network-manage-peering.md#create-a-peering).
- Saiba como [criar um hub- and -spoke topologia de rede](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) com peering de rede virtual.