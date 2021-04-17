---
title: 'Criar uma gateway Azure VPN baseada em rotas: CLI'
description: Crie rapidamente um gateway Azure VPN baseado em rotas utilizando o Azure CLI, para uma ligação VPN a uma rede no local ou para ligar redes virtuais.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 87e3490711990944e017d2d463090f3c8697956c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484233"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>Criar um gateway VPN baseado em rotas usando CLI

Este artigo ajuda-o a criar rapidamente um gateway Azure VPN baseado em rotas utilizando o Azure CLI. Um gateway VPN é usado ao criar uma ligação VPN à sua rede no local. Também pode utilizar uma porta de entrada VPN para ligar VNets.

Os passos deste artigo criarão um VNet, uma sub-rede, uma sub-rede de gateway, e um gateway VPN baseado em rotas (gateway de rede virtual). Um gateway de rede virtual pode demorar 45 minutos ou mais a criar. Uma vez concluída a criação do gateway, pode então criar ligações. Estes passos requerem uma assinatura Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.4 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um grupo de recursos utilizando o [grupo az criar](/cli/azure/group) comando. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. 


```azurecli-interactive
az group create --name TestRG1 --location eastus
```

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Criar uma rede virtual

Criar uma rede virtual utilizando o [vnet de rede az criar](/cli/azure/network/vnet) comando. O exemplo a seguir cria uma rede virtual chamada **VNet1** na localização **EastUS:**

```azurecli-interactive
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="add-a-gateway-subnet"></a><a name="gwsubnet"></a>Adicionar uma sub-rede do gateway

A sub-rede gateway contém os endereços IP reservados que os serviços de gateway de rede virtuais utilizam. Utilize os seguintes exemplos para adicionar uma sub-rede de gateway:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="request-a-public-ip-address"></a><a name="PublicIP"></a>Solicitar um endereço IP público

Um gateway VPN deve ter um endereço IP público atribuído dinamicamente. O endereço IP público será atribuído ao gateway VPN que cria para a sua rede virtual. Use o seguinte exemplo para solicitar um endereço IP público:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="create-the-vpn-gateway"></a><a name="CreateGateway"></a>Criar o gateway de VPN

Crie o gateway de VPN com o comando [az network vnet-gateway create](/cli/azure/network/vnet-gateway).

Se executar este comando utilizando o `--no-wait` parâmetro, não vê qualquer feedback ou saída. O `--no-wait` parâmetro permite que o portal seja criado em segundo plano. Não significa que o portal VPN seja criado imediatamente.

```azurecli-interactive
az network vnet-gateway create \
  -n VNet1GW \
  -l eastus \
  --public-ip-address VNet1GWIP \
  -g TestRG1 \
  --vnet VNet1 \
  --gateway-type Vpn \
  --sku VpnGw1 \
  --vpn-type RouteBased \
  --no-wait
```

Um gateway de VPN pode demorar 45 minutos ou mais a ser criado.

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Ver o portal VPN

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

A resposta é semelhante a esta:

```output
{
  "activeActive": false,
  "bgpSettings": null,
  "enableBgp": false,
  "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
  "gatewayDefaultSite": null,
  "gatewayType": "Vpn",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
  "ipConfigurations": [
    {
      "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
      "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
      "name": "vnetGatewayConfig0",
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Updating",
      "publicIpAddress": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
        "resourceGroup": "TestRG1"
      },
      "resourceGroup": "TestRG1",
      "subnet": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet",
        "resourceGroup": "TestRG1"
      }
    }
  ],
  "location": "eastus",
  "name": "VNet1GW",
  "provisioningState": "Updating",
  "resourceGroup": "TestRG1",
  "resourceGuid": "69c269e3-622c-4123-9231",
  "sku": {
    "capacity": 2,
    "name": "VpnGw1",
    "tier": "VpnGw1"
  },
  "tags": null,
  "type": "Microsoft.Network/virtualNetworkGateways",
  "vpnClientConfiguration": null,
  "vpnType": "RouteBased"
}
```

### <a name="view-the-public-ip-address"></a>Ver o endereço IP público

Para visualizar o endereço IP público atribuído ao seu gateway, utilize o seguinte exemplo:

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

O valor associado ao campo **ipAddress** é o endereço IP público do seu gateway VPN.

Resposta de exemplo:

```output
{
  "dnsSettings": null,
  "etag": "W/\"a12d4d03-b27a-46cc-b222-8d9364b8166a\"",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "13.90.195.184",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar dos recursos criados, utilize [o grupo AZ para eliminar](/cli/azure/group) o grupo de recursos. Isto elimina o grupo de recursos e todos os recursos contidos no mesmo.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>Passos seguintes

Uma vez que o gateway tenha terminado de criar, pode criar uma ligação entre a sua rede virtual e outro VNet. Ou criar uma ligação entre a sua rede virtual e um local no local.

> [!div class="nextstepaction"]
> [Criar uma ligação site a site](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Criar uma ligação de ponto a site](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Criar uma ligação a outro VNet](vpn-gateway-vnet-vnet-rm-ps.md)
