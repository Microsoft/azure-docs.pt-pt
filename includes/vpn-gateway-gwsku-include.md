---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8087025810214f3edbb74e628698eb69558f3500
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010867"
---
Quando cria um gateway de rede virtual, tem de especificar o SKU de gateway que pretende utilizar. Selecione o SKU que atende as suas necessidades com base nos tipos de cargas de trabalho, taxas de transferência, funcionalidades e SLA. Para gateway de rede virtual SKUs em Zonas de Disponibilidade Azure, consulte [Azure Availability Zones gateway SKUs](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md).

###  <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>SKUs de Gateway por túnel, ligação e débito

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

> [!NOTE]
> VpnGw SKUs (VpnGw1, VpnGw1AZ, VpnGw2, VpnGw2AZ, VpnGw3, VpnGw3AZ, VpnGw4, VpnGw4AZ, VpnGw5 e VpnGw5AZ) são apoiados apenas para a implementação do modelo de gestor de recursos. As redes virtuais clássicas devem continuar a utilizar os antigos SKUs (legados).
>  * Para obter informações sobre o trabalho com o gateway legacy SKUs (Basic, Standard e HighPerformance), consulte [Trabalhar com SKUs de gateway VPN (SKUs legado)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
>  * Para expressRoute gateway SKUs, consulte [Gateways de rede virtual para ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).
>

###  <a name="gateway-skus-by-feature-set"></a><a name="feature"></a>GATEWAY SKUs por conjunto de funcionalidades

O novo gateway VPN SKUs dinamiza os conjuntos de funcionalidades oferecidos nos gateways:

| **SKU**| **Funcionalidades**|
| ---    | ---         |
|**Básico** (**)   | **VPN baseada em rotas:** 10 túneis para as ligações S2S; nenhuma autenticação RADIUS para P2S; nenhum IKEv2 para P2S<br>**VPN baseado em políticas:**(IKEv1): 1 Túnel S2S/ligação; sem P2S|
| **Todos os SKUs de Geração1 e Geração2, exceto Basic** | **VPN baseada em rotas:** até 30 túneis (*), P2S, BGP, política IPsec/IKE personalizada ativa e personalizada, coexistência ExpressRoute/VPN |
|        |             |

(*) Pode configurar "PolicyBasedTrafficSelectors" para ligar uma porta de entrada VPN baseada em rotas a vários dispositivos de firewall baseados em políticas no local. Consulte [Gateways de ligação VPN para vários dispositivos VPN com base na política local com o PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) para obter detalhes.

() \* \* O SKU Básico é considerado um legado SKU. O SKU Básico tem determinadas limitações de características. Não é possível redimensionar um portal que utilize um SKU Básico para um dos novos SKUs de gateway, em vez disso, deve mudar para um novo SKU, que envolve apagar e recriar o seu gateway VPN.

###  <a name="gateway-skus---production-vs-dev-test-workloads"></a><a name="workloads"></a>Gateway SKUs - Produção vs. Dev-Test Workloads

Devido a diferenças nos SLA e nos conjuntos de funcionalidades, é recomendável seguir os SKU para produção versus dev-test:

| **Carga de trabalho**                       | **SKUs**               |
| ---                                | ---                    |
| **Produção, cargas de trabalho críticas** | Todos os SKUs de Geração1 e Geração2, exceto Basic |
| **Dev-test ou prova de conceito**   | Básico (**)                 |
|                                    |                        |

() \* \* O SKU Básico é considerado um SKU legado e tem limitações de recursos. Verifique se a funcionalidade de que necessita é suportada antes de utilizar o SKU Básico.

Se estiver a utilizar os antigos SKUs (legado), as recomendações da SKU de produção são Standard e HighPerformance. Para obter informações e instruções para os antigos SKUs, consulte [gateway SKUs (legado)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
