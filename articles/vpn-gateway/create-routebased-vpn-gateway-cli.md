---
title: 'Criar um gateway de VPN do Azure baseado em rota: CLI'
description: Aprenda rapidamente a criar um gateway de VPN usando a CLI
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/04/2018
ms.author: cherylmc
ms.openlocfilehash: 1f0cc1d63f8560399d1d71c8d010c37bd2c5e387
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75778758"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>Criar um gateway de VPN baseado em rota usando a CLI

Este artigo ajuda você a criar rapidamente um gateway de VPN do Azure baseado em rota usando o CLI do Azure. Um gateway de VPN é usado ao criar uma conexão VPN com sua rede local. Você também pode usar um gateway de VPN para conectar o VNets.

As etapas neste artigo criarão uma VNet, uma sub-rede, uma sub-rede de gateway e um gateway de VPN baseado em rota (gateway de rede virtual). Um gateway de rede virtual pode levar de 45 minutos ou mais para ser criado. Depois que a criação do gateway for concluída, você poderá criar conexões. Essas etapas exigem uma assinatura do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Para localizar a versão instalada, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos usando o comando [AZ Group Create](/cli/azure/group) . Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. 


```azurecli-interactive 
az group create --name TestRG1 --location eastus
```

## <a name="vnet"></a>Criar uma rede virtual

Crie uma rede virtual usando o comando [AZ Network vnet Create](/cli/azure/network/vnet) . O exemplo a seguir cria uma rede virtual chamada **VNet1** na localização **lesteus** :

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

A sub-rede de gateway contém os endereços IP reservados que os serviços de gateway de rede virtual usam. Use os exemplos a seguir para adicionar uma sub-rede de gateway:

```azurepowershell-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="PublicIP"></a>Solicitar um endereço IP público

Um gateway de VPN deve ter um endereço IP público alocado dinamicamente. O endereço IP público será alocado para o gateway de VPN que você criar para sua rede virtual. Use o exemplo a seguir para solicitar um endereço IP público:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="CreateGateway"></a>Criar o gateway de VPN

Crie o gateway de VPN com o comando [az network vnet-gateway create](/cli/azure/group).

Se você executar esse comando usando o parâmetro `--no-wait`, não verá nenhum comentário ou saída. O parâmetro `--no-wait` permite que o gateway seja criado em segundo plano. Isso não significa que o gateway de VPN é criado imediatamente.

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

## <a name="viewgw"></a>Exibir o gateway de VPN

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

A resposta é semelhante a esta:

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

### <a name="view-the-public-ip-address"></a>Exibir o endereço IP público

Para exibir o endereço IP público atribuído ao seu gateway, use o exemplo a seguir:

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

O valor associado ao campo **ipAddress** é o endereço IP público do seu gateway de VPN.

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

Quando você não precisar mais dos recursos que criou, use [AZ Group Delete](/cli/azure/group) para excluir o grupo de recursos. Isto elimina o grupo de recursos e todos os recursos contidos no mesmo.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>Passos seguintes

Depois que o gateway terminar de criar, você poderá criar uma conexão entre sua rede virtual e outra VNet. Ou crie uma conexão entre sua rede virtual e um local.

> [!div class="nextstepaction"]
> [Criar uma conexão site a site](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Criar uma conexão ponto a site](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Criar uma conexão com outra VNet](vpn-gateway-vnet-vnet-rm-ps.md)
