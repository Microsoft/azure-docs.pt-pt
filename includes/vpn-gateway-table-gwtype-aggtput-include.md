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
ms.openlocfilehash: 1643b20c6c157c43e93967cef364e703dbf4478e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010828"
---
|**Geração VPN <br> Gateway <br>** |**SKU**   | **S2S/VNet para VNet<br>Túneis** | **Ligações SSTP <br>P2S** | **Ligações P2S <br> IKEv2/OpenVPN** | **Referência de<br>Débito de Agregação** | **BGP** | **Zona redundante** |
|---            |---         | ---        | ---       | ---            | ---       | ---       | ---|
|**Geração1**|**Básica**   | Um máximo de 10    | Um máximo de 128  | Não suportado  | 100 Mbps  | Não suportado| No |
|**Geração1**|**VpnGw1**  | Um máximo de 30*   | Um máximo de 128  | Um máximo de 250       | 650 Mbps  | Suportado | No |
|**Geração1**|**VpnGw2**  | Um máximo de 30*   | Um máximo de 128  | Um máximo de 500       | 1 Gbps    | Suportado | No |
|**Geração1**|**VpnGw3**  | Um máximo de 30*   | Um máximo de 128  | Um máximo de 1000      | 1,25 Gbps | Suportado | No |
|**Geração1**|**VpnGw1AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 250       | 650 Mbps  | Suportado | Yes |
|**Geração1**|**VPNGw2AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 500       | 1 Gbps    | Suportado | Yes |
|**Geração1**|**VpnGw3AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 1000      | 1,25 Gbps | Suportado | Yes |
|        |            |            |           |                |           |           |     |
|**Geração2**|**VpnGw2**  | Um máximo de 30*   | Um máximo de 128  | Um máximo de 500       | 1,25 Gbps | Suportado | No |
|**Geração2**|**VpnGw3**  | Um máximo de 30*   | Um máximo de 128  | Um máximo de 1000      | 2,5 Gbps  | Suportado | No |
|**Geração2**|**VpnGw4**  | Um máximo de 30*   | Um máximo de 128  | Um máximo de 5000      | 5 Gbps    | Suportado | No |
|**Geração2**|**VpnGw5**  | Um máximo de 30*   | Um máximo de 128  | Um máximo de 10000      | 10 Gbps   | Suportado | No |
|**Geração2**|**VPNGw2AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 500       | 1,25 Gbps | Suportado | Yes |
|**Geração2**|**VpnGw3AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 1000      | 2,5 Gbps  | Suportado | Yes |
|**Geração2**|**VpnGw4AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 5000      | 5 Gbps    | Suportado | Yes |
|**Geração2**|**VpnGw5AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 10000      | 10 Gbps   | Suportado | Yes |

(*) Utilize a [WAN Virtual](../articles/virtual-wan/virtual-wan-about.md) se precisar de mais de 30 túneis S2S VPN.

* A redimensionamento de SKUs VPNGw é permitida dentro da mesma geração, exceto a redimensionamento do SKU Básico. O SKU Básico é um SKU legado e tem limitações de recursos. Para passar de Basic para outro SKU VPNGw, você deve eliminar o gateway Basic SKU VPN e criar um novo gateway com a combinação de tamanhos de Geração e SKU desejado.

* Esses limites de ligação são separados. Por exemplo, pode ter 128 ligações SSTP e também 250 ligações IKEv2 num SKU VpnGw1.

* As informações sobre os preços estão disponíveis na página [Preços](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Pode encontrar informações sobre SLA (contrato de nível de serviço) na página do [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* Num único túnel pode ser alcançado um máximo de 1 Gbps. O Índice de Produção Agregada na tabela acima baseia-se em medições de múltiplos túneis agregados através de um único gateway. A Referência de Débito Agregado para um Gateway de VPN é uma combinação de S2S + P2S. **Se tiver muitas ligações P2S, isso pode afetar negativamente uma ligação S2S devido às limitações de débito.** O Índice de Produção Agregada não é um rendimento garantido devido às condições de tráfego da Internet e aos seus comportamentos de aplicação.

Para ajudar os nossos clientes a compreender o desempenho relativo dos SKUs utilizando diferentes algoritmos, utilizamos ferramentas iPerf e CTSTraffic publicamente disponíveis para medir desempenhos. A tabela abaixo lista os resultados dos testes de desempenho para a Geração 1, SKUs VPNGw. Como pode ver, o melhor desempenho é obtido quando usamos algoritmo GCMAES256 para encriptação e integridade do IPsec. Obtivemos um desempenho médio ao usar o AES256 para encriptação IPsec e SHA256 para Integridade. Quando usamos DES3 para encriptação IPsec e SHA256 para Integridade, obtivemos o menor desempenho.

|**Geração**|**SKU**   | **Algoritmos <br> utilizados** | **Produção <br> observada** | **Pacotes por segundo <br> observados** |
|---           |---       | ---                 | ---            | ---                    |
|**Geração1**|**VpnGw1**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58,000<br>50 000<br>50 000|
|**Geração1**|**VpnGw2**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90.000<br>80.000<br>55,000|
|**Geração1**|**VpnGw3**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105,000<br>90.000<br>60 000|
|**Geração1**|**VpnGw1AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58,000<br>50 000<br>50 000|
|**Geração1**|**VPNGw2AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90.000<br>80.000<br>55,000|
|**Geração1**|**VpnGw3AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105,000<br>90.000<br>60 000|
