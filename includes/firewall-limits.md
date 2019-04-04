---
title: incluir ficheiro
description: incluir ficheiro
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 3/25/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 0467359cd9d6a067e519a62532f00459bc5f68cb
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891054"
---
| Recurso | Limite predefinido |
| --- | --- |
| Taxa de transferência de dados |30 Gbps<sup>1</sup> |
|Regras|10 000, todos regra combinado de tipos.|
|Tamanho mínimo de AzureFirewallSubnet |/26|
|Intervalo de portas nas regras de rede e da aplicação|0-64,000. Trabalho está em curso para reduzir esta limitação.|
|Tabela de rotas|Por predefinição, o AzureFirewallSubnet tem uma rota de 0.0.0.0/0 com o valor de NextHopType definido como **Internet**.<br><br>Se ativar o protocolo de túnel forçado para o local através do ExpressRoute ou o Gateway de VPN, terá de configurar uma rota definida pelo utilizador de 0.0.0.0/0 (UDR) com o conjunto de valor NextHopType como Internet e associá-lo a seu AzureFirewallSubnet de explicitamente. Esta ação substitui um gateway predefinido potenciais anúncio do BGP voltar à sua rede no local. Se sua organização precisar de túnel forçado para o Firewall do Azure direcionar o tráfego de gateway padrão voltar por meio de sua rede no local, contacte o suporte. Podemos lista de permissões é mantida a sua subscrição para garantir a conectividade à Internet de firewall necessárias.|

<sup>1</sup>se precisar de aumentar estes limites, contacte o suporte do Azure.
