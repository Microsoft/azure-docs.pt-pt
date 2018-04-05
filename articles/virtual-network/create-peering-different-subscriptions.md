---
title: Criar uma Azure virtual network peering - Resource Manager - diferentes subscrições | Microsoft Docs
description: Saiba como criar uma rede virtual peering entre redes virtuais criadas através do Gestor de recursos que existam em diferentes subscrições do Azure.
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
ms.date: 09/25/2017
ms.author: jdial;anavin
ms.openlocfilehash: 92cbcad42508f2ae6113d13449aba7eed5acd251
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Criar um peering de rede virtual - Resource Manager, subscrições diferentes 

Neste tutorial, pode aprende a criar uma peering entre redes virtuais criadas através do Gestor de recursos de rede virtual. Existem redes virtuais em diferentes subscrições. Peering dois recursos de permite redes virtuais em redes virtuais diferentes para comunicar entre si com o mesmo largura de banda e latência dado a entender, os recursos foram na mesma rede virtual. Saiba mais sobre [peering de rede Virtual](virtual-network-peering-overview.md). 

Os passos para criar um peering de rede virtual são diferentes, dependendo se as redes virtuais estão no mesmo, ou outro, subscrições e que [modelo de implementação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) as redes virtuais são criadas através do. Saiba como criar uma rede virtual peering noutros cenários, clicando no cenário da tabela seguinte:

|Modelo de implementação do Azure  | Subscrição do Azure  |
|--------- |---------|
|[O Gestor de recursos](tutorial-connect-virtual-networks-portal.md) |mesmo|
|[Um Gestor de recursos, um clássico](create-peering-different-deployment-models.md) |mesmo|
|[Um Gestor de recursos, um clássico](create-peering-different-deployment-models-subscriptions.md) |Diferente|

Não é possível criar uma rede virtual peering entre duas redes virtuais implementadas através do modelo de implementação clássica. Se pretender ligar redes virtuais que foram criados através do modelo de implementação clássica, pode utilizar um Azure [Gateway de VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para ligar as redes virtuais. 

Este tutorial elementos redes virtuais na mesma região. Também pode elemento redes virtuais em diferentes [regiões suportadas](virtual-network-manage-peering.md#cross-region). 

Pode utilizar o [portal do Azure](#portal), o Azure [interface de linha de comandos](#cli) (CLI), Azure [PowerShell](#powershell), ou um [modelo Azure Resource Manager](#template)para criar um peering de rede virtual. Clique em qualquer uma das ligações de ferramenta anterior para ir diretamente para os passos para criar um peering de rede virtual com a ferramenta de escolha.

## <a name="portal"></a>Criar peering - portal do Azure

Este tutorial utiliza contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todos os passos, ignore os passos para registo fora do portal e ignore os passos para atribuir permissões de outro utilizador para as redes virtuais.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como UserA. A conta que iniciar sessão com tem de ter as permissões necessárias para criar um peering de rede virtual. Para obter uma lista de permissões, consulte [peering permissões de rede Virtual](virtual-network-manage-peering.md#permissions).
2. Clique em **+ novo**, clique em **redes**, em seguida, clique em **rede Virtual**.
3. No **criar rede virtual** painel, introduza, ou selecione os valores para as seguintes definições, em seguida, clique em **criar**:
    - **Name**: *myVnetA*
    - **Espaço de endereços**: *10.0.0.0/16*
    - **Nome da sub-rede**: *predefinido*
    - **Intervalo de endereços da sub-rede**: *10.0.0.0/24*
    - **Subscrição**: selecione a subscrição A.
    - **Grupo de recursos**: selecione **criar nova** e introduza *myResourceGroupA*
    - **Localização**: *EUA leste*
4. No **procurar recursos** caixa na parte superior do portal, tipo *myVnetA*. Clique em **myVnetA** quando for apresentada nos resultados da pesquisa. É apresentado um painel para o **myVnetA** rede virtual.
5. No **myVnetA** painel apresentado, clique em **(IAM) do controlo de acesso** na vertical lista das opções no lado esquerdo do painel.
6. No **myVnetA - controlo de acesso (IAM)** painel apresentado, clique em **+ adicionar**.
7. No **adicionar permissões** painel que aparece, selecione **contribuinte de rede** no **função** caixa.
8. No **selecione** caixa, selecione um UserB ou escreva o endereço de correio eletrónico do User-b para procurá-lo. A lista de utilizadores apresentados é do mesmo inquilino do Azure Active Directory como a rede virtual que está a configurar o peering para.
9. Clique em **Guardar**.
10. No **myVnetA - controlo de acesso (IAM)** painel, clique em **propriedades** na vertical lista das opções no lado esquerdo do painel. Copiar o **ID de recurso**, que é utilizado num passo posterior. O ID de recurso é semelhante ao seguinte exemplo: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA.
11. Termine sessão no portal como UserA, em seguida, inicie sessão como utilizador b.
12. Conclua os passos 2-3, introduzir ou selecionar os seguintes valores no passo 3:

    - **Name**: *myVnetB*
    - **Espaço de endereços**: *10.1.0.0/16*
    - **Nome da sub-rede**: *predefinido*
    - **Intervalo de endereços da sub-rede**: *10.1.0.0/24*
    - **Subscrição**: selecione a subscrição B.
    - **Grupo de recursos**: selecione **criar nova** e introduza *myResourceGroupB*
    - **Localização**: *EUA leste*

13. No **procurar recursos** caixa na parte superior do portal, tipo *myVnetB*. Clique em **myVnetB** quando for apresentada nos resultados da pesquisa. É apresentado um painel para o **myVnetB** rede virtual.
14. No **myVnetB** painel apresentado, clique em **propriedades** na vertical lista das opções no lado esquerdo do painel. Copiar o **ID de recurso**, que é utilizado num passo posterior. O ID de recurso é semelhante ao seguinte exemplo: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB.
15. Clique em **(IAM) do controlo de acesso** no **myVnetB** painel e, em seguida, concluir os passos 5 a 10 para myVnetB, introduzir **UserA** no passo 8.
16. Termine sessão no portal como UserB e inicie sessão como utilizador.
17. No **procurar recursos** caixa na parte superior do portal, tipo *myVnetA*. Clique em **myVnetA** quando for apresentada nos resultados da pesquisa. É apresentado um painel para o **myVnet** rede virtual.
18. Clique em **myVnetA**.
19. No **myVnetA** painel apresentado, clique em **Peerings** na vertical lista das opções no lado esquerdo do painel.
20. No **myVnetA - Peerings** painel que antes eram, clique em **+ adicionar**
21. No **adicionar peering** painel apresentado, introduza, ou selecione as seguintes opções, em seguida, clique em **OK**:
     - **Name**: *myVnetAToMyVnetB*
     - **Modelo de implementação de rede virtual**: selecione **Resource Manager**.
     - **Sei o ID de recurso**: esta caixa de verificação.
     - **ID de recurso**: introduza o ID de recurso do passo 14.
     - **Permitir o acesso de rede virtual:** Certifique-se de que **ativado** está selecionada.
    Não existem outras definições são utilizadas neste tutorial. Para saber mais sobre todas as definições de peering, leia [gerir peerings de rede virtual](virtual-network-manage-peering.md#create-a-peering).
22. Depois de clicar em **OK** no passo anterior, o **adicionar peering** painel fecha e ver o **myVnetA - Peerings** painel novamente. Após alguns segundos, o peering que criou é apresentado no painel. **Iniciou** está listado no **PEERING estado** coluna para o **myVnetAToMyVnetB** peering é criado. Tiver executado o peering myVnetA para myVnetB, mas agora tem elemento myVnetB para myVnetA. O peering têm de ser criado em ambas as direções para ativar a recursos nas redes virtuais para comunicar entre si.
23. Termine sessão no portal como UserA e inicie sessão como utilizador b.
24. Execute os passos 17 21 novamente para myVnetB. No passo 21, nome do peering *myVnetBToMyVnetA*, selecione *myVnetA* para **rede Virtual**e introduza o ID do passo 10 a **ID de recurso**caixa.
25. Alguns segundos depois de clicar em **OK** para criar peering para myVnetB, o **myVnetBToMyVnetA** peering que acabou de criar está listado com **ligado** no  **Estado do PEERING** coluna.
26. Termine sessão no portal como UserB e inicie sessão como utilizador.
27. Conclua os passos 17-19 novamente. O **PEERING estado** para o **myVnetAToVNetB** peering é agora também **ligado**. O peering é estabelecido com êxito depois de confirmar **ligado** no **PEERING estado** coluna para ambas as redes virtuais no peering de. Quaisquer recursos do Azure, que criar a rede virtual estão agora capazes de comunicar entre si através dos respetivos endereços IP. Se estiver a utilizar a resolução do nome do Azure de predefinido para as redes virtuais, os recursos nas redes virtuais não são possível resolver nomes através de redes virtuais. Se pretender resolver nomes através de redes virtuais num peering, terá de criar o seu próprio servidor DNS. Saiba como configurar [resolução de nomes utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Opcional**: Apesar de criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conetividade.
29. **Opcional**: para eliminar os recursos que criou neste tutorial, concluir os passos a [eliminar recursos](#delete-portal) secção deste artigo.

## <a name="cli"></a>Criar peering - CLI do Azure

Este tutorial utiliza contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todos os passos, ignore os passos para registo no Azure e remover as linhas de script que criar atribuições de funções de utilizador. Substitua UserA@azure.com e UserB@azure.com em todas os scripts seguintes com os nomes de utilizador que está a utilizar para UserA e UserB.

O script seguinte:

- Requer a CLI do Azure versão 2.0.4 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de atualizar, veja [instalar o Azure CLI 2.0](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Funciona numa shell de deteção. Para obter as opções de execução de scripts da CLI do Azure num cliente Windows, veja [Executar a CLI do Azure no Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Em vez de instalar a CLI e as respetivas dependências, pode utilizar a Shell de nuvem do Azure. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Clique em de **experimente** botão no script que se segue, que invoca uma Shell de nuvem que pode iniciar sessão na sua conta do Azure com. 

1. Abra uma sessão CLI e inicie sessão no Azure como UserA utilizando o `azure login` comando. A conta que iniciar sessão com tem de ter as permissões necessárias para criar um peering de rede virtual. Para obter uma lista de permissões, consulte [peering permissões de rede Virtual](virtual-network-manage-peering.md#permissions).
2. Copie o seguinte script para um editor de texto no seu PC, substitua `<SubscriptionA-Id>` com o ID de SubscriptionA, em seguida, copie o script modificado, cole-o na sua sessão do CLI e prima `Enter`. Se não souber o Id de subscrição, introduza o comando 'Mostrar de conta az'. O valor para **id** na saída é o ID de subscrição

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
    
3. Termine sessão no Azure como UserA utilizando o `az logout` comando, em seguida, inicie sessão no Azure como UserB. A conta que iniciar sessão com tem de ter as permissões necessárias para criar um peering de rede virtual. Para obter uma lista de permissões, consulte [peering permissões de rede Virtual](virtual-network-manage-peering.md#permissions).
4. Crie myVnetB. Copie o conteúdo de script no passo 2 para um editor de texto no seu PC. Substitua `<SubscriptionA-Id>` com o ID de SubscriptionB. Alteração 10.0.0.0/16 10.1.0.0/16, alteração relativamente a B e todos os Bs recomeço cópia script modificado, cole-a à sua sessão do CLI e prima `Enter`. 
5. Termine sessão no Azure como UserB e inicie sessão no Azure como o utilizador.
6. Crie uma peering do myVnetA para myVnetB de rede virtual. Copie o seguinte conteúdo de script para um editor de texto no seu PC. Substitua `<SubscriptionB-Id>` com o ID de SubscriptionB. Para executar o script, copie o script modificado, cole-o sua sessão do CLI e prima Enter.
 
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

    O estado é **iniciado**. Alterar para **ligado** depois de criar o peering para myVnetA myVnetB.

8. Terminar UserA a partir do Azure e inicie sessão no Azure como UserB.
9. Crie o peering de myVnetB para myVnetA. Copie o conteúdo de script no passo 6 para um editor de texto no seu PC. Substitua `<SubscriptionB-Id>` com o ID de SubscriptionA e altere todas as relativamente B e todos os Bs para A. Assim que tiver tomado as alterações, copie o script modificado, cole-o a sessão CLI e prima `Enter`.
10. Ver o estado do peering de myVnetB. Copie o conteúdo de script no passo 7 para um editor de texto no seu PC. A alteração para o B para o grupo de recursos e nomes de rede virtual, copie o script, cole o script modificado à sua sessão CLI e, em seguida, prima `Enter`. O estado do peering é **ligado**. O estado de myVnetA peering mude para **ligado** depois de criar o peering de myVnetB para myVnetA. O utilizador pode iniciar sessão novamente para o Azure e concluída passo 7 novamente para verificar o estado do peering de myVnetA. 

    > [!NOTE]
    > O peering é estabelecido não até que o estado do peering é **ligado** para ambas as redes virtuais.

11. **Opcional**: Apesar de criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conetividade.
12. **Opcional**: para eliminar os recursos que criou neste tutorial, concluir os passos [eliminar recursos](#delete-cli) neste artigo.

Quaisquer recursos do Azure, que criar a rede virtual estão agora capazes de comunicar entre si através dos respetivos endereços IP. Se estiver a utilizar a resolução do nome do Azure de predefinido para as redes virtuais, os recursos nas redes virtuais não são possível resolver nomes através de redes virtuais. Se pretender resolver nomes através de redes virtuais num peering, terá de criar o seu próprio servidor DNS. Saiba como configurar [resolução de nomes utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
 
## <a name="powershell"></a>Criar peering - PowerShell

Este tutorial utiliza contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todos os passos, ignore os passos para registo no Azure e remover as linhas de script que criar atribuições de funções de utilizador. Substitua UserA@azure.com e UserB@azure.com em todas os scripts seguintes com os nomes de utilizador que está a utilizar para UserA e UserB.

1. Instale a versão mais recente do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do PowerShell. Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão do PowerShell.
3. No PowerShell, inicie sessão no Azure como UserA, introduzindo o `login-azurermaccount` comando. A conta que iniciar sessão com tem de ter as permissões necessárias para criar um peering de rede virtual. Para obter uma lista de permissões, consulte [peering permissões de rede Virtual](virtual-network-manage-peering.md#permissions).
4. Crie um grupo de recursos e a rede virtual recomeço cópia o seguinte script para um editor de texto no seu PC. Substitua `<SubscriptionA-Id>` com o ID de SubscriptionA. Se não souber o Id de subscrição, introduza o `Get-AzureRmSubscription` comando para vê-la. O valor para **Id** na saída devolvida é o ID de subscrição. Ao executar o script, copie o script modificado, cole-a para o PowerShell e, em seguida, prima `Enter`.

    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. Terminar UserA a partir do Azure e UserB de início de sessão. A conta que iniciar sessão com tem de ter as permissões necessárias para criar um peering de rede virtual. Para obter uma lista de permissões, consulte [peering permissões de rede Virtual](virtual-network-manage-peering.md#permissions).
6. Copie o conteúdo de script no passo 4 para um editor de texto no seu PC. Substitua `<SubscriptionA-Id>` com o ID da subscrição 10.0.0.0/16 B. Altere para 10.1.0.0/16. Altere todos os relativamente B e todos os Bs para A. Ao executar o script, copie o script modificado, cole no PowerShell e, em seguida, prima `Enter`.
7. Terminar UserB a partir do Azure e o utilizador de início de sessão.
8. Crie o peering de myVnetA para myVnetB. Copie o seguinte script para um editor de texto no seu PC. Substitua `<SubscriptionB-Id>` com o ID de subscrição B. Ao executar o script, copie o script modificado, cole para o PowerShell e, em seguida, prima `Enter`.
 
    ```powershell
    # Peer myVnetA to myVnetB.
    $vNetA=Get-AzureRmVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vNetA `
      -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
    ```

9. Ver o estado do peering de myVnetA.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    O estado é **iniciado**. Alterar para **ligado** depois de configurar o peering para myVnetA myVnetB.

10. Terminar UserA a partir do Azure e UserB de início de sessão.
11. Crie o peering de myVnetB para myVnetA. Copie o conteúdo de script no passo 8 para um editor de texto no seu PC. Substitua `<SubscriptionB-Id>` com o ID de subscrição uma e altere todas as relativamente B e todos os Bs para A. Ao executar o script, copie o script modificado, cole-a para o PowerShell e, em seguida, prima `Enter`.
12. Ver o estado do peering de myVnetB. Copie o conteúdo de script no passo 9 para um editor de texto no seu PC. A alteração para o B para o grupo de recursos e nomes de rede virtual. Ao executar o script, cole o script de modificação do PowerShell e, em seguida, prima `Enter`. O estado é **ligado**. O estado do peering de **myVnetA** alterações **ligado** depois de criar o peering de **myVnetB** para **myVnetA**. O utilizador pode iniciar sessão novamente para o Azure e concluída passo 9 novamente para verificar o estado do peering de myVnetA. 

    > [!NOTE]
    > O peering é estabelecido não até que o estado do peering é **ligado** para ambas as redes virtuais.

    Quaisquer recursos do Azure, que criar a rede virtual estão agora capazes de comunicar entre si através dos respetivos endereços IP. Se estiver a utilizar a resolução do nome do Azure de predefinido para as redes virtuais, os recursos nas redes virtuais não são possível resolver nomes através de redes virtuais. Se pretender resolver nomes através de redes virtuais num peering, terá de criar o seu próprio servidor DNS. Saiba como configurar [resolução de nomes utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Opcional**: Apesar de criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conetividade.
14. **Opcional**: para eliminar os recursos que criou neste tutorial, concluir os passos [eliminar recursos](#delete-powershell) neste artigo.

## <a name="template"></a>Criar peering - modelo do Resource Manager

1. Para criar uma rede virtual e atribua o adequado [permissões](virtual-network-manage-peering.md#permissions), concluir os passos a [Portal](#portal), [CLI do Azure](#cli), ou [PowerShell](#powershell)secções deste artigo.
2. Guarde o texto que se segue para um ficheiro no seu computador local. Substitua `<subscription ID>` do User-ID de subscrição Pode guardar o ficheiro como vnetpeeringA.json, por exemplo.

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

3. Inicie sessão no Azure como UserA e implementar a modelo utilizando o [portal](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-a-template-from-your-local-machine), ou o [CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Especifique o nome de ficheiro que guardou o texto de json de exemplo no passo 2.
4. Copie o json de exemplo do passo 2 para um ficheiro no seu computador e efetuar alterações às linhas que começam por:
    - **nome**: alteração *myVnetA/myVnetAToMyVnetB* para *myVnetB/myVnetBToMyVnetA*.
    - **ID**: substituir `<subscription ID>` com o ID de subscrição e a alteração do User-b *myVnetB* para *myVnetA*.
5. Passo completo 3 novamente, registado no Azure como UserB.
6. **Opcional**: Apesar de criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conetividade.
7. **Opcional**: para eliminar os recursos que criou neste tutorial, concluir os passos a [eliminar recursos](#delete) secção deste artigo, utilizando o portal do Azure, PowerShell ou a CLI do Azure.

## <a name="delete"></a>Eliminar recursos
Quando tiver terminado neste tutorial, pode querer eliminar os recursos que criou no tutorial, pelo que não implicar custos de utilização. Também eliminar um grupo de recursos elimina todos os recursos que estão no grupo de recursos.

### <a name="delete-portal"></a>Portal do Azure

1. Inicie sessão no portal do Azure como o utilizador.
2. Na caixa de pesquisa de portal, introduza **myResourceGroupA**. Nos resultados da pesquisa, clique em **myResourceGroupA**.
3. No **myResourceGroupA** painel, clique em de **eliminar** ícone.
4. Para confirmar a eliminação no **tipo o nome de grupo de recursos** box, introduza **myResourceGroupA**e, em seguida, clique em **eliminar**.
5. Termine sessão no portal como UserA e inicie sessão como utilizador b.
6. Execute os passos 2 a 4 para myResourceGroupB.

### <a name="delete-cli"></a>CLI do Azure

1. Inicie sessão no Azure como UserA e execute o seguinte comando:

    ```azurecli-interactive
    az group delete --name myResourceGroupA --yes
    ```
2. Termine sessão no Azure como UserA e inicie sessão como utilizador b.
3. Execute o seguinte comando:

    ```azurecli-interactive
    az group delete --name myResourceGroupB --yes
    ```

### <a name="delete-powershell"></a>PowerShell

1. Inicie sessão no Azure como UserA e execute o seguinte comando:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupA -force
    ```

2. Termine sessão no Azure como UserA e inicie sessão como utilizador b.
3. Execute o seguinte comando:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupB -force
    ```

## <a name="next-steps"></a>Passos seguintes

- Exaustivamente familiarizar-se com importante [restrições de peering de rede virtual e comportamentos](virtual-network-manage-peering.md#requirements-and-constraints) antes de criar uma rede virtual para a produção de peering utilize.
- Saiba mais sobre todos os [definições de rede virtual peering](virtual-network-manage-peering.md#create-a-peering).
- Saiba como [criar um hub- and -spoke topologia de rede](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) com peering de rede virtual.
