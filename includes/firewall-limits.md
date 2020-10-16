---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 07/30/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 59699d493880034ad1d26a56c63a9ed8401ef371
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87507502"
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

<sup>1</sup> Se precisar de aumentar estes limites, contacte o Suporte Azure.
