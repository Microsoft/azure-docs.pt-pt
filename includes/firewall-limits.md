---
title: incluir ficheiro
description: incluir ficheiro
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 05/06/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 5a5f831e72da6bedaf12d3ed82e79f0250a09062
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82876048"
---
| Recurso | Limite |
| --- | --- |
| Débito de dados |30 Gbps<sup>1</sup> |
|Regras|10,000. Todos os tipos de regras combinados.|
|Regras máximas de ADN|298<br>Se o protocolo de uma regra estiver configurado tanto para tcp como uDP, conta como duas regras.|
|Tamanho mínimo da AzureFirewallSubnet |/26|
|Gama portuária nas regras de rede e aplicação|1 - 65535|
|Endereços IP públicos|250 no máximo para as portas de ADN e SNAT.|
|Endereços IP grupos IP|50 grupos IP ou menos: máximo 5000 endereços IP individuais cada um por instância de firewall.<br>51 - 100 Grupos IP: 500 endereços IP individuais cada um por instância de firewall.<br><br>Para mais informações consulte [grupos IP (pré-visualização) em Firewall Azure](../articles/firewall/ip-groups.md#ip-address-limits)
|Tabela de rota|Por padrão, o AzureFirewallSubnet tem uma rota de 0.0.0.0/0 com o valor NextHopType definido para a **Internet**.<br><br>O Azure Firewall deve ter conectividade direta com a Internet. Se o seu AzureFirewallSubnet aprender uma rota padrão para a sua rede no local via BGP, deve sobrepor-se a isso com um UDR 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta da Internet. Por padrão, o Azure Firewall não suporta túneis forçados para uma rede no local.<br><br>No entanto, se a sua configuração necessitar de túneis forçados para uma rede no local, a Microsoft irá apoiá-la caso a caso. Contacte o Suporte para que possamos rever o seu caso. Se for aceite, permitiremos a sua subscrição e garantiremos a manutenção da conectividade de Firewall Internet necessária.|

<sup>1</sup> Se precisar de aumentar estes limites, contacte o Suporte Azure.
