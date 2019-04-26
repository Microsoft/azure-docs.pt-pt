---
title: 'Crie um gateway de VPN do Azure baseado na rota: CLI | Microsoft Docs'
description: Aprenda rapidamente a criar um Gateway de VPN com a CLI
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/04/2018
ms.author: cherylmc
ms.openlocfilehash: f5f62a6bfa1baa205e0496dd901f1f1eef660079
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60391247"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>Criar um gateway VPN baseado na rota com a CLI

Este artigo ajuda-o a criar rapidamente um gateway de VPN do Azure baseado na rota com a CLI do Azure. Um gateway de VPN é utilizado quando criar uma ligação VPN à sua rede no local. Também pode utilizar um gateway de VPN para ligar VNets.

Os passos neste artigo irão criar uma VNet, uma sub-rede, uma sub-rede de gateway e um gateway de VPN baseado na rota (gateway de rede virtual). Um gateway de rede virtual pode demorar 45 minutos ou mais para criar. Depois de concluída a criação de gateway, em seguida, pode criar ligações. Estes passos requerem uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Para localizar a versão instalada, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um grupo de recursos utilizando o [criar grupo az](/cli/azure/group) comando. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. 


```azurecli-interactive 
az group create --name TestRG1 --location eastus
```

## <a name="vnet"></a>Criar uma rede virtual

Criar uma rede virtual com o [vnet de rede de az criar](/cli/azure/network/vnet) comando. O exemplo seguinte cria uma rede virtual denominada **VNet1** no **EastUS** localização:

```azurecli-interactive 
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="gwsubnet"></a>Adicionar uma sub-rede de gateway

A sub-rede do gateway contém os endereços IP reservados que utilizam os serviços do gateway de rede virtual. Utilize os exemplos seguintes para adicionar uma sub-rede de gateway:

```azurepowershell-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="PublicIP"></a>Peça um endereço IP público

Um gateway de VPN tem de ter um endereço IP público alocado dinamicamente. O endereço IP público será atribuído ao gateway de VPN que criou para a rede virtual. Utilize o seguinte exemplo para pedir um endereço IP público:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="CreateGateway"></a>Criar o gateway VPN

Crie o gateway de VPN com o comando [az network vnet-gateway create](/cli/azure/group).

Se executar este comando utilizando o `--no-wait` parâmetro, não verá nenhum feedback nem resultados. O `--no-wait` parâmetro permite que o gateway ser criado em segundo plano. Não significa que o gateway de VPN é criado imediatamente.

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

## <a name="viewgw"></a>Ver o gateway de VPN

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

A resposta é semelhante a este:

```
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

Para ver o endereço IP público atribuído ao seu gateway, utilize o seguinte exemplo:

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

O valor associado a **ipAddress** campo é o endereço IP público do seu gateway VPN.

Resposta de exemplo:

```
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
## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos que criou, utilize [eliminação do grupo de az](/cli/azure/group) para eliminar o grupo de recursos. Isto elimina o grupo de recursos e todos os recursos contidos no mesmo.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>Passos Seguintes

Depois do gateway tenha terminado de criar, pode criar uma ligação entre a rede virtual e a outra VNet. Em alternativa, criar uma ligação entre a rede virtual e uma localização no local.

> [!div class="nextstepaction"]
> [Criar uma ligação site a site](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Criar uma ligação ponto a site](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Criar uma ligação a outra VNet](vpn-gateway-vnet-vnet-rm-ps.md)
