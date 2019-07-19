---
title: incluir ficheiro
description: incluir ficheiro
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 7/16/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: a42284765a46f4a000dc5b7fcf2867ef17d69570
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229223"
---
| Resource | Limite predefinido |
| --- | --- |
| Taxa de transferência de dados |30 Gbps<sup>1</sup> |
|Regras|10.000, todos os tipos de regra combinados.|
|Tamanho mínimo de AzureFirewallSubnet |/26|
|Intervalo de portas em regras de aplicativos e de rede|0-64000. O trabalho está em andamento para relaxar essa limitação.|
|Endereços IP públicos|máximo de 100|
|Tabela de rotas|Por padrão, AzureFirewallSubnet tem uma rota 0.0.0.0/0 com o valor NextHopType definido como **Internet**.<br><br>O Firewall do Azure deve ter conectividade direta com a Internet. Se o seu AzureFirewallSubnet aprende uma rota padrão para sua rede local via BGP, você deve substituí-lo por um UDR 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta com a Internet. Por padrão, o Firewall do Azure não dá suporte a túnel forçado para uma rede local.<br><br>No entanto, se sua configuração exigir túnel forçado para uma rede local, a Microsoft dará suporte a isso caso a caso. Contate o suporte para que possamos examinar seu caso. Se aceito, permitiremos sua assinatura e garantiremos que a conectividade de Internet do firewall necessária seja mantida.|

<sup>1</sup> Se você precisar aumentar esses limites, entre em contato com o suporte do Azure.
