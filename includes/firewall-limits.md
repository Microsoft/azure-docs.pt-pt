---
title: incluir ficheiro
description: incluir ficheiro
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 5/3/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 8709d4d903bd31ff94d04ec61e226857e4190407
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238751"
---
| Resource | Limite predefinido |
| --- | --- |
| Taxa de transferência de dados |30 Gbps<sup>1</sup> |
|Regras|10 000, todos regra combinado de tipos.|
|Tamanho mínimo de AzureFirewallSubnet |/26|
|Intervalo de portas nas regras de rede e da aplicação|0-64,000. Trabalho está em curso para reduzir esta limitação.|
|Tabela de rotas|Por predefinição, o AzureFirewallSubnet tem uma rota de 0.0.0.0/0 com o valor de NextHopType definido como **Internet**.<br><br>Firewall do Azure tem de ter conectividade à Internet direta. Se sua AzureFirewallSubnet aprende uma rota predefinida para a sua rede no local através do BGP, tem de substituir isso com um UDR 0.0.0.0/0 com o **NextHopType** valor definido como **Internet** manter direto Desde Conectividade Internet. Por predefinição, o Firewall do Azure não suporta o túnel forçado a uma rede no local.<br><br>No entanto, se a configuração requer o túnel forçado a uma rede no local, a Microsoft suporta-lo um caso a caso. Contacte o suporte para que possamos examinar seu caso. Se aceites, vamos lista branca de sua subscrição e certifique-se de que é mantida a conectividade de Internet de firewall necessárias.|

<sup>1</sup>se precisar de aumentar estes limites, contacte o suporte do Azure.
