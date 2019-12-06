---
title: incluir ficheiro
description: incluir ficheiro
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 12/06/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: a7b6867033e750f476b3d995926f0b670965a5d7
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875468"
---
| Recurso | Limite predefinido |
| --- | --- |
| Taxa de transferência de dados |30 Gbps<sup>1</sup> |
|Regras|10.000. Todos os tipos de regra combinados.|
|Regras de DNAT por endereço IP público|299|
|Tamanho mínimo de AzureFirewallSubnet |/26|
|Intervalo de portas em regras de aplicativos e de rede|0-64000. O trabalho está em andamento para relaxar essa limitação.|
|Endereços IP públicos|100 máximo (atualmente, as portas SNAT são adicionadas somente aos cinco primeiros endereços IP públicos).|
|Tabela de rota|Por padrão, AzureFirewallSubnet tem uma rota 0.0.0.0/0 com o valor NextHopType definido como **Internet**.<br><br>O Firewall do Azure deve ter conectividade direta com a Internet. Se o seu AzureFirewallSubnet aprende uma rota padrão para sua rede local via BGP, você deve substituí-lo por um UDR 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta com a Internet. Por padrão, o Firewall do Azure não dá suporte a túnel forçado para uma rede local.<br><br>No entanto, se sua configuração exigir túnel forçado para uma rede local, a Microsoft dará suporte a isso caso a caso. Contate o suporte para que possamos examinar seu caso. Se aceito, permitiremos sua assinatura e garantiremos que a conectividade de Internet do firewall necessária seja mantida.|

<sup>1</sup> Se você precisar aumentar esses limites, entre em contato com o suporte do Azure.
