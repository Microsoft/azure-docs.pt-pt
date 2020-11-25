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
ms.openlocfilehash: 069d7af9cee0bee673c8f0b32659ce362fe4dd70
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026494"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>Para modificar o "gatewayIpAddress" do gateway de rede local

Se o dispositivo VPN que pretende ligar foi alterou o respetivo endereço IP público, tem de modificar o gateway de rede local para que essa alteração seja refletida. O endereço IP do gateway pode ser alterado sem remover uma ligação de gateway de VPN existente (caso tenha um). Para modificar o endereço IP do gateway, substitua os valores "Site2" e "TestRG1" pelos seus utilizando o comando [az network local-gateway update](/cli/azure/network/local-gateway).

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

Certifique-se de que o endereço IP está correto na saída:

```
"gatewayIpAddress": "23.99.222.170",
```