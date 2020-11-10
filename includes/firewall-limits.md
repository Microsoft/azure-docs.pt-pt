---
title: incluir ficheiro
description: incluir ficheiro
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 11/06/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: d479c3087b971aa17cf145e0111890da07381eab
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94386489"
---
| Recurso | Limite |
| --- | --- |
| Débito de dados |30 Gbps<sup>1</sup> |
|Regras|10,000. Todos os tipos de regras combinados.|
|Regras máximas de DNAT|298 para um único endereço IP público.<br>Quaisquer endereços IP públicos adicionais contribuem para as portas SNAT disponíveis, mas reduzem o número das regras de ADN disponíveis. Por exemplo, dois endereços IP públicos permitem 297 regras de ADN. Se o protocolo de uma regra for configurado tanto para a TCP como para a UDP, conta como duas regras.|
|Tamanho mínimo AzureFirewallSubnet |/26|
|Gama portuária em regras de rede e aplicação|1 - 65535|
|Endereços IP públicos|250 no máximo. Todos os endereços IP públicos podem ser usados nas regras do DNAT e todos contribuem para portas SNAT disponíveis.|
|Endereços IP em grupos IP|Máximo de 100 grupos IP por firewall.<br>Máximo de 5000 endereços IP individuais ou prefixos IP por cada Grupo IP.
|Tabela de rota|Por padrão, a AzureFirewallSubnet tem uma rota de 0.0.0.0/0 com o valor NextHopType definido para a **Internet**.<br><br>O Azure Firewall tem de ter conectividade Internet direta. Se o seu AzureFirewallSubnet aprender uma rota padrão para a sua rede no local via BGP, deve sobrepor-se a isso com um UDR de 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta na Internet. Por defeito, o Azure Firewall não suporta a escavação forçada para uma rede no local.<br><br>No entanto, se a sua configuração necessitar de um túnel forçado para uma rede no local, a Microsoft irá apoiá-la caso a caso. Contacte o Suporte para que possamos rever o seu caso. Se for aceite, permitiremos a sua subscrição e garantiremos que a conectividade de Internet de firewall necessária seja mantida.|
|FQDNs nas regras de rede|Para um bom desempenho, não exceda mais de 1000 FQDNs em todas as regras de rede por firewall.|

<sup>1</sup> Se precisar de aumentar estes limites, contacte o Suporte Azure.
