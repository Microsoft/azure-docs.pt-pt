---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/17/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 49f48c2d0bf865cf8c8fde831e7a597f8701d213
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66150043"
---
Pode verificar se a ligação teve êxito com o cmdlet "Get-AzVirtualNetworkGatewayConnection", com ou sem "-Debug". 

1. Utilize o seguinte exemplo de cmdlet, configurando os valores para corresponder aos seus. Se lhe for pedido, selecione "A" para executar "Todos". No exemplo, '--name' refere-se ao nome da ligação que pretende testar.

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
   ```
2. Quando o cmdlet terminar, veja os valores. No exemplo abaixo, o estado da ligação é apresentado como "Ligado" e pode ver bytes de entrada e de saída.
   
   ```
   "connectionStatus": "Connected",
   "ingressBytesTransferred": 33509044,
   "egressBytesTransferred": 4142431
   ```