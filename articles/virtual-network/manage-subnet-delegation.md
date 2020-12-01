---
title: Adicione ou remova uma delegação de sub-redes numa rede virtual Azure
titlesuffix: Azure Virtual Network
description: Saiba como adicionar ou remover uma sub-rede delegada para um serviço em Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: kumud
ms.openlocfilehash: 4e4f002d038820edf128e3fefb229a0918a8ac55
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433511"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Adicionar ou remover uma delegação de sub-redes

A delegação da Subnet dá permissões explícitas ao serviço para criar recursos específicos de serviço na sub-rede utilizando um identificador único ao implementar o serviço. Este artigo descreve como adicionar ou remover uma sub-rede delegada para um serviço Azure.

## <a name="portal"></a>Portal

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta secção, cria-se uma rede virtual e a sub-rede que mais tarde irá delegar num serviço Azure.

1. No lado superior esquerdo do ecrã, **selecione Criar uma** rede virtual  >  **de rede de rede de** recursos  >  **Virtual network**.
1. Na **Criar rede virtual,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira *a MyVirtualNetwork*. |
    | Espaço de endereços | Insira *10.0.0.0/16*. |
    | Subscrição | Selecione a sua subscrição.|
    | Grupo de recursos | **Selecione Criar novo,** insira *o myResourceGroup* e, em seguida, selecione **OK**. |
    | Localização | **Selecione Eastus**.|
    | Sub-rede - Nome | Insira *o mySubnet.* |
    | Sub-rede - Intervalo de endereços | Insira *10.0.0.0/24*. |
    |||
1. Deixe o resto como padrão e, em seguida, **selecione Criar**.

### <a name="permissions"></a>Permissões

Se não criou a sub-rede que gostaria de delegar num serviço Azure, precisa da seguinte permissão: `Microsoft.Network/virtualNetworks/subnets/write` .

A função [de contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) de rede incorporada também contém as permissões necessárias.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delege uma sub-rede para um serviço Azure

Nesta secção, delega a sub-rede que criou na secção anterior a um serviço Azure.

1. Na barra de pesquisa do portal, *insira a myVirtualNetwork*. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-a.
2. Nos resultados da pesquisa, selecione *myVirtualNetwork*.
3. Selecione **sub-redes**, em **DEFINIÇÕES** e, em seguida, selecione **mySubnet**.
4. Na página *mySubnet,* para a lista de delegações da **Sub-rede,** selecione dos serviços listados na **sub-rede Delegado para um serviço** (por exemplo, **Microsoft.DBforPostgreSQL/serversv2).**  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Remover a delegação de sub-redes de um serviço Azure

1. Na barra de pesquisa do portal, *insira a myVirtualNetwork*. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-a.
2. Nos resultados da pesquisa, selecione *myVirtualNetwork*.
3. Selecione **sub-redes**, em **DEFINIÇÕES** e, em seguida, selecione **mySubnet**.
4. Na página *mySubnet,* para a lista de delegações da **Sub-rede,** selecione **Nenhum** dos serviços listados na **sub-rede delegado para um serviço**. 

## <a name="azure-cli"></a>CLI do Azure

Prepare o seu ambiente para o Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este artigo requer a versão 2.0.28 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo a seguir cria um grupo de recursos chamado **myResourceGroup** na localização **este:**

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie uma rede virtual com o nome **myVnet**, com uma sub-rede de nome **mySubnet** em **myResourceGroup**, através de [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.0.0.0/24
```
### <a name="permissions"></a>Permissões

Se não criou a sub-rede que gostaria de delegar num serviço Azure, precisa da seguinte permissão: `Microsoft.Network/virtualNetworks/subnets/write` .

A função [de contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) de rede incorporada também contém as permissões necessárias.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delege uma sub-rede para um serviço Azure

Nesta secção, delega a sub-rede que criou na secção anterior a um serviço Azure. 

Utilize [a atualização da sub-rede de rede Az](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) para atualizar a sub-rede chamada **mySubnet** com uma delegação para um serviço Azure.  Neste **exemplo, o Microsoft.DBforPostgreSQL/serversv2** é utilizado para a delegação de exemplo:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

Para verificar se a delegação foi aplicada, utilize [a rede az vnet show](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show). Verifique se o serviço é delegado na sub-rede sob o nome do serviço de **propriedade:**

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```

```json
[
  {
    "actions": [
      "Microsoft.Network/virtualNetworks/subnets/join/action"
    ],
    "etag": "W/\"8a8bf16a-38cf-409f-9434-fe3b5ab9ae54\"",
    "id": "/subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/0",
    "name": "0",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "serviceName": "Microsoft.DBforPostgreSQL/serversv2",
    "type": "Microsoft.Network/virtualNetworks/subnets/delegations"
  }
]
```

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Remover a delegação de sub-redes de um serviço Azure

Utilize [a atualização da sub-rede de vnet](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) da rede Az para remover a delegação da sub-rede denominada **mySubnet**:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
Para verificar se a delegação foi removida, utilize [a rede az vnet show](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show). Verifique se o serviço é removido da sub-rede sob o serviço de **propriedadeName**:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
A saída do comando é um suporte nulo:
```json
[]
```

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="connect-to-azure"></a>Ligar ao Azure

```azurepowershell-interactive
  Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
Criar um grupo de recursos com [o New-AzResourceGroup](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>Criar a rede virtual

Crie uma rede virtual chamada **myVnet** com uma sub-rede chamada **mySubnet** usando [New-AzVirtualNetSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) no **myResourceGroup** usando [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). O espaço de endereço IP para a rede virtual é **10.0.0.0/16**. A sub-rede dentro da rede virtual é **de 10.0.0.0/24**.  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>Permissões

Se não criou a sub-rede que gostaria de delegar num serviço Azure, precisa da seguinte permissão: `Microsoft.Network/virtualNetworks/subnets/write` .

A função [de contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) de rede incorporada também contém as permissões necessárias.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delege uma sub-rede para um serviço Azure

Nesta secção, delega a sub-rede que criou na secção anterior a um serviço Azure. 

Utilize [o Add-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/add-azdelegation?view=latest) para atualizar a sub-rede chamada **mySubnet** com uma delegação chamada **myDelegation** a um serviço Azure.  Neste **exemplo, o Microsoft.DBforPostgreSQL/serversv2** é utilizado para a delegação de exemplo:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Utilize [a Get-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) para verificar a delegação:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  ProvisioningState : Succeeded
  ServiceName       : Microsoft.DBforPostgreSQL/serversv2
  Actions           : {Microsoft.Network/virtualNetworks/subnets/join/action}
  Name              : myDelegation
  Etag              : W/"9cba4b0e-2ceb-444b-b553-454f8da07d8a"
  Id                : /subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/myDelegation

```
### <a name="remove-subnet-delegation-from-an-azure-service"></a>Remover a delegação de sub-redes de um serviço Azure

Utilize [remove-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/remove-azdelegation?view=latest) para remover a delegação da sub-rede denominada **mySubnet**:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Utilize [a Get-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) para verificar se a delegação foi removida:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>Passos seguintes
- Saiba como [gerir as sub-redes em Azure.](virtual-network-manage-subnet.md)
