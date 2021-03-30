---
title: 'Ver rotas eficazes de um hub virtual: Azure Virtual WAN | Microsoft Docs'
description: Como ver rotas eficazes para um hub virtual em Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ad487aa7492abcf02988c78ccfa2ba7cd4798249
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90983659"
---
# <a name="view-virtual-hub-effective-routes"></a>Ver rotas efetivas do hub virtual

Pode ver todas as rotas do seu hub VIRTUAL WAN no portal Azure. Este artigo acompanha-o através dos passos para ver rotas eficazes. Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="select-connections-or-route-tables"></a><a name="routing"></a>Selecione ligações ou tabelas de rotas

1. Navegue para o seu centro virtual e, em seguida, **selecione Encaminhamento**. Na página 'Encaminhamento', selecione **Rotas Eficazes**.
1. A partir do dropdown, pode selecionar **a Tabela de Rotas**. Se não vir uma opção 'Route Table', isto significa que não tem uma tabela de rotas personalizada ou padrão configurada neste hub virtual.

## <a name="view-output"></a><a name="output"></a>Ver saída

A saída da página mostra os seguintes campos:

* **Prefixo**: Prefixo de endereço conhecido da entidade atual (aprendido com o router de hub virtual)
* **Próximo tipo de lúpulo**: Pode ser Conexão de Rede Virtual, VPN_S2S_Gateway, ExpressRouteGateway, Remote Hub ou Azure Firewall.
* **Próximo salto**: Esta é a ligação ao ID de recurso do próximo salto, ou simplesmente mostra on-link para implicar o hub atual.
* **Origem**: Identificação de recursos da fonte de encaminhamento.
* **Percurso AS**: Percurso do Atributo BGP AS (sistema autónomo) lista todos os números de AS que precisam de ser percorridos para chegar ao local onde o prefixo a que o caminho está ligado, é anunciado.

### <a name="example"></a><a name="example"></a>Exemplo

Os valores na tabela de exemplos a seguir implicam que a ligação virtual do hub ou a tabela de rotas aprenderam a rota de 10.2.0.0/24 (prefixo de ramo). Aprendeu a rota devido ao **VPN Next hop tipo** VPN_S2S_Gateway com **Next hop** VPN Gateway ID de recurso. **Route Origin** aponta para o ID de recurso da porta/tabela de gateway/route origins. **O Caminho as** indica o Caminho AS para o ramo.

Utilize a barra de deslocamento na parte inferior da tabela para ver o "Caminho AS".

| **Prefixo** |  **Tipo de salto seguinte** | **Próximo salto** |  **Origem da Rota** |**AS Path** |
| ---        | ---                | ---          | ---               | ---         |
| 10.2.0.0/24| VPN_S2S_Gateway |/subscrições/ `<sub id>` /resourceGroups/ `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw|/subscrições/ `<sub id>` /resourceGroups/ `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw| 20 000|

**Considerações:**

* Se vir 0.0.0.0/0 na saída **Get Effective Routes,** isso implica que o percurso existe numa das tabelas de rotas. No entanto, se esta rota foi criada para a internet, uma bandeira adicional **"enableInternetSecurity":** é necessária verdadeira na ligação. A rota efetiva no VM NIC não mostrará a rota se a bandeira "enableInternetSecurity" na ligação for "falsa".

* O campo **Rota Padrão propagação** é visto no portal Azure Virtual WAN quando edita uma ligação de rede virtual, uma ligação VPN ou uma ligação ExpressRoute. Este campo indica a bandeira **activaçãoInternetSecurity,** que é sempre por padrão "falsa" para ligações ExpressRoute e VPN, mas "verdadeira" para ligações de rede virtuais.

* Ao visualizar rotas eficazes num VM NIC, se vir o próximo salto como 'Virtual Network Gateway', isso implica o router de hub Virtual quando o VM está numa fala ligada a um hub VIRTUAL WAN.

* Ver As rotas eficazes para uma tabela de rotas de hub virtual só são povoadas se o hub virtual tiver pelo menos um tipo de ligação (VPN/ER/VNET) ligado a ele.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a WAN Virtual, veja a [Descrição Geral da WAN Virtual](virtual-wan-about.md).
* Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).
