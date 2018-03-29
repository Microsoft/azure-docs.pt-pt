---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e021a1b109f735dee16d75c05c26ab35e599a3d9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2018
---
### <a name="to-view-local-network-gateways"></a>Para ver os gateways de rede local

Para ver uma lista de gateways da rede local, utilize o comando [az network local-gateway list](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_list).

```azurecli
az network local-gateway list --resource-group TestRG1
```

[!INCLUDE [modify-prefix](vpn-gateway-modify-ip-prefix-cli-include.md)]

[!INCLUDE [modify-gateway-IP](vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

### <a name="to-verify-the-shared-key-values"></a>Para verificar os valores de chave partilhada

Verifique se o valor da chave partilhada é igual ao valor que utilizou na configuração do dispositivo VPN. Se não for, execute a ligação novamente com o valor do dispositivo ou atualize o dispositivo com o valor que é devolvido. Os valores têm de corresponder. Para ver a chave partilhada, utilize [az network vpn-connection-list](https://docs.microsoft.com/cli/azure/network/vpn-connection#az_network_vpn_connection_list).

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 --resource-group TestRG1
```
### <a name="to-view-the-vpn-gateway-public-ip-address"></a>Para ver o endereço IP Público do gateway de VPN

Para encontrar o endereço IP público do gateway da rede virtual, utilize o comando [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_list). Para maior facilidade de leitura, a saída para este exemplo é formatada para apresentar a lista de IPs públicos no formato de tabela.

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```
