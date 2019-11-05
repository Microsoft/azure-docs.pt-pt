---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 17499fad64c8ae313f7a544015a04cc20e8bbabe
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495688"
---
|**Geração de<br>do gateway de<br>VPN** |**SKU**   | **S2S/VNet para VNet<br>Túneis** | **Ligações SSTP <br>P2S** | **P2S<br> conexões IKEv2/OpenVPN** | **Referência de<br>Débito de Agregação** | **BGP** | **Com redundância de zona** |
|---            |---         | ---        | ---       | ---            | ---       | ---       | ---|
|**Generation1**|**Básica**   | Um máximo de 10    | Um máximo de 128  | Não Suportado  | 100 Mbps  | Não Suportado| Não |
|**Generation1**|**VpnGw1**  | Um máximo de 30*   | Um máximo de 128  | Um máximo de 250       | 650 Mbps  | Suportado | Não |
|**Generation1**|**VpnGw2**  | Um máximo de 30*   | Um máximo de 128  | Um máximo de 500       | 1 Gbps    | Suportado | Não |
|**Generation1**|**VpnGw3**  | Um máximo de 30*   | Um máximo de 128  | Um máximo de 1000      | 1,25 Gbps | Suportado | Não |
|**Generation1**|**VpnGw1AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 250       | 650 Mbps  | Suportado | Sim |
|**Generation1**|**VpnGw2AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 500       | 1 Gbps    | Suportado | Sim |
|**Generation1**|**VpnGw3AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 1000      | 1,25 Gbps | Suportado | Sim |
|        |            |            |           |                |           |           |     |
|**Generation2**|**VpnGw2**  | Um máximo de 30*   | Um máximo de 128  | Um máximo de 500       | 1,25 Gbps | Suportado | Não |
|**Generation2**|**VpnGw3**  | Um máximo de 30*   | Um máximo de 128  | Um máximo de 1000      | 2,5 Gbps  | Suportado | Não |
|**Generation2**|**VpnGw4**  | Um máximo de 30*   | Um máximo de 128  | Um máximo de 1000      | 5 Gbps    | Suportado | Não |
|**Generation2**|**VpnGw5**  | Um máximo de 30*   | Um máximo de 128  | Um máximo de 1000      | 10 Gbps   | Suportado | Não |
|**Generation2**|**VpnGw2AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 500       | 1,25 Gbps | Suportado | Sim |
|**Generation2**|**VpnGw3AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 1000      | 2,5 Gbps  | Suportado | Sim |
|**Generation2**|**VpnGw4AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 1000      | 5 Gbps    | Suportado | Sim |
|**Generation2**|**VpnGw5AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 1000      | 10 Gbps   | Suportado | Sim |

(*) Utilize a [WAN Virtual](../articles/virtual-wan/virtual-wan-about.md) se precisar de mais de 30 túneis S2S VPN.

* O redimensionamento de SKUs VpnGw é permitido dentro da mesma geração, exceto o redimensionamento da SKU básica. O SKU básico é um SKU herdado e tem limitações de recursos. Para mover do básico para outro SKU do VpnGw, você deve excluir o gateway de VPN de SKU básico e criar um novo gateway com a combinação de tamanho de SKU e geração desejada.

* Esses limites de ligação são separados. Por exemplo, pode ter 128 ligações SSTP e também 250 ligações IKEv2 num SKU VpnGw1.

* As informações sobre os preços estão disponíveis na página [Preços](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Pode encontrar informações sobre SLA (contrato de nível de serviço) na página do [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* Em um único túnel, um máximo de 1 Gbps de taxa de transferência pode ser alcançado. O parâmetro de comparação de taxa de transferência agregado na tabela acima baseia-se em medidas de vários túneis agregados por meio de um único gateway. A Referência de Débito Agregado para um Gateway de VPN é uma combinação de S2S + P2S. **Se tiver muitas ligações P2S, isso pode afetar negativamente uma ligação S2S devido às limitações de débito.** O benchmark de taxa de transferência agregada não é uma taxa de transferência garantida devido às condições de tráfego da Internet e aos comportamentos do aplicativo.

* Para ajudar nossos clientes a entender o desempenho relativo de SKUs usando algoritmos diferentes, usamos as ferramentas iPerf e CTSTraffic publicamente disponíveis para medir o desempenho. A tabela a seguir lista os resultados de testes de desempenho para SKUs de geração 1, VpnGw. Como você pode ver, o melhor desempenho é obtido quando usamos o algoritmo GCMAES256 para a criptografia e a integridade do IPsec. Temos o desempenho médio ao usar AES256 para criptografia IPsec e SHA256 para integridade. Quando usamos DES3 para criptografia IPsec e SHA256 para integridade, temos o menor desempenho.

|**Geração**|**SKU**   | **Algoritmos<br>usados** | **Taxa de transferência<br>observada** | **<br>de pacotes por segundo observados** |
|---           |---       | ---                 | ---            | ---                    |
|**Generation1**|**VpnGw1**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58.000<br>50 000<br>50 000|
|**Generation1**|**VpnGw2**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90.000<br>80.000<br>55.000|
|**Generation1**|**VpnGw3**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105.000<br>90.000<br>60.000|
|**Generation1**|**VpnGw1AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58.000<br>50 000<br>50 000|
|**Generation1**|**VpnGw2AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90.000<br>80.000<br>55.000|
|**Generation1**|**VpnGw3AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105.000<br>90.000<br>60.000|
