---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 61082333afc88abef0a8d8a57d1f1b1d893b6148
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184117"
---
### <a name="to-view-local-network-gateways"></a>Para ver os gateways de rede local

Para ver uma lista de gateways da rede local, utilize o comando [az network local-gateway list](https://docs.microsoft.com/cli/azure/network/local-gateway).

```azurecli
az network local-gateway list --resource-group TestRG1
```

[!INCLUDE [modify-prefix](vpn-gateway-modify-ip-prefix-cli-include.md)]

[!INCLUDE [modify-gateway-IP](vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

### <a name="to-verify-the-shared-key-values"></a>Para verificar os valores de chave partilhada

Verifique se o valor da chave partilhada é igual ao valor que utilizou na configuração do dispositivo VPN. Se não for, execute a ligação novamente com o valor do dispositivo ou atualize o dispositivo com o valor que é devolvido. Os valores têm de corresponder. Para ver a chave partilhada, utilize [az network vpn-connection-list](https://docs.microsoft.com/cli/azure/network/vpn-connection).

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 --resource-group TestRG1
```
### <a name="to-view-the-vpn-gateway-public-ip-address"></a>Para ver o endereço IP Público do gateway de VPN

  Para encontrar o endereço IP público do gateway da rede virtual, utilize o comando [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip). Para maior facilidade de leitura, a saída para este exemplo é formatada para apresentar a lista de IPs públicos no formato de tabela.

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```
