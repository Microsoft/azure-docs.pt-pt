---
title: incluir ficheiro
description: incluir ficheiro
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 04/03/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 4972a80ec7c481ac7bc8860c005c8576ce37a090
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758603"
---
| Recurso | Limite |
| --- | --- |
| Débito de dados |30 Gbps<sup>1</sup> |
|Regras|10,000. Todos os tipos de regras combinados.|
|Regras máximas de ADN|298<br>Se o protocolo de uma regra estiver configurado tanto para tcp como uDP, conta como duas regras.|
|Tamanho mínimo da AzureFirewallSubnet |/26|
|Gama portuária nas regras de rede e aplicação|0-64,000. Estão em curso trabalhos para relaxar esta limitação.|
|Endereços IP públicos|100 máximo (atualmente, as portas SNAT são adicionadas apenas para os primeiros cinco endereços IP públicos.)|
|Endereços IP grupos IP|50 grupos IP ou menos: máximo 5000 endereços IP individuais cada um por instância de firewall.<br>51 - 100 Grupos IP: 500 endereços IP individuais cada um por instância de firewall.<br><br>Para mais informações consulte [grupos IP (pré-visualização) em Firewall Azure](../articles/firewall/ip-groups.md#ip-address-limits)
|Tabela de rota|Por padrão, o AzureFirewallSubnet tem uma rota de 0.0.0.0/0 com o valor NextHopType definido para a **Internet**.<br><br>O Azure Firewall deve ter conectividade direta com a Internet. Se o seu AzureFirewallSubnet aprender uma rota padrão para a sua rede no local via BGP, deve sobrepor-se a isso com um UDR 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta da Internet. Por padrão, o Azure Firewall não suporta túneis forçados para uma rede no local.<br><br>No entanto, se a sua configuração necessitar de túneis forçados para uma rede no local, a Microsoft irá apoiá-la caso a caso. Contacte o Suporte para que possamos rever o seu caso. Se for aceite, permitiremos a sua subscrição e garantiremos a manutenção da conectividade de Firewall Internet necessária.|

<sup>1</sup> Se precisar de aumentar estes limites, contacte o Suporte Azure.
