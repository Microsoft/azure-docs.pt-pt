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
ms.openlocfilehash: e368b1590f263618969423d57cdf0531fc2bb54d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184098"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>Para modificar o "gatewayIpAddress" do gateway de rede local

Se o dispositivo VPN que pretende ligar foi alterou o respetivo endereço IP público, tem de modificar o gateway de rede local para que essa alteração seja refletida. O endereço IP do gateway pode ser alterado sem remover uma ligação de gateway de VPN existente (caso tenha um). Para modificar o endereço IP do gateway, substitua os valores "Site2" e "TestRG1" pelos seus utilizando o comando [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway).

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

Certifique-se de que o endereço IP está correto na saída:

```
"gatewayIpAddress": "23.99.222.170",
```
