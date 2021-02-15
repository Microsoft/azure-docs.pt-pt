---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 06df27b5eeb74f75bc49a848881b56aa4e81c57b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380504"
---
Se o dispositivo VPN que pretende ligar foi alterou o respetivo endereço IP público, tem de modificar o gateway de rede local para que essa alteração seja refletida. Ao modificar este valor, também pode modificar os prefixos de endereços em simultâneo. Certifique-se de que utiliza o nome existente do seu gateway de rede local, de modo a substituir as definições atuais. Se utilizar um nome diferente, irá criar um novo gateway de rede local, em vez de substituir o existente.

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name Site1 `
-Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName TestRG1
```
