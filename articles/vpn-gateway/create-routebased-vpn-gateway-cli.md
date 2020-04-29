---
title: 'Criar um Gateway Azure VPN baseado em rotas: CLI'
description: Aprenda rapidamente a criar um Gateway VPN usando o CLI
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/04/2018
ms.author: cherylmc
ms.openlocfilehash: 121790fce220874babedf67cd72471caa7e92ae6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241096"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>Criar um gateway VPN baseado em rota usando cli

Este artigo ajuda-o a criar rapidamente um gateway Azure VPN baseado em rotas utilizando o Azure CLI. Um gateway VPN é usado ao criar uma ligação VPN à sua rede no local. Também pode utilizar um gateway VPN para ligar VNets.

Os passos neste artigo criarão um VNet, uma subnet, uma subnet gateway e um gateway VPN baseado em rota (gateway de rede virtual). Um portal de rede virtual pode demorar 45 minutos ou mais para criar. Uma vez concluída a criação do portal, pode criar ligações. Estes passos requerem uma subscrição Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Para localizar a versão instalada, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um grupo de recursos utilizando o [grupo AZ criar](/cli/azure/group) comando. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. 


```azurecli-interactive
az group create --name TestRG1 --location eastus
```

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Criar uma rede virtual

Criar uma rede virtual utilizando a [rede az vnet criar](/cli/azure/network/vnet) comando. O exemplo seguinte cria uma rede virtual chamada **VNet1** na localização **EastUS:**

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

A sub-rede gateway contém os endereços IP reservados que os serviços de gateway da rede virtual utilizam. Utilize os seguintes exemplos para adicionar uma sub-rede de gateway:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="request-a-public-ip-address"></a><a name="PublicIP"></a>Solicite um endereço IP público

Um gateway VPN deve ter um endereço IP público dinamicamente atribuído. O endereço IP público será atribuído ao gateway VPN que cria para a sua rede virtual. Utilize o seguinte exemplo para solicitar um endereço IP público:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="create-the-vpn-gateway"></a><a name="CreateGateway"></a>Criar o gateway de VPN

Crie o gateway de VPN com o comando [az network vnet-gateway create](/cli/azure/group).

Se executar este comando `--no-wait` utilizando o parâmetro, não vê qualquer feedback ou saída. O `--no-wait` parâmetro permite que o portal seja criado em segundo plano. Não significa que o gateway VPN seja criado imediatamente.

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

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Ver o gateway VPN

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

Para ver o endereço IP público atribuído ao seu portal, utilize o seguinte exemplo:

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

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisa dos recursos que criou, utilize o [grupo Az](/cli/azure/group) eliminar para eliminar o grupo de recursos. Isto elimina o grupo de recursos e todos os recursos contidos no mesmo.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>Passos seguintes

Uma vez que o gateway termine de criar, pode criar uma ligação entre a sua rede virtual e outro VNet. Ou criar uma ligação entre a sua rede virtual e um local no local.

> [!div class="nextstepaction"]
> [Criar uma ligação site a site](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Criar uma ligação de ponto a site](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Criar uma ligação a outro VNet](vpn-gateway-vnet-vnet-rm-ps.md)
