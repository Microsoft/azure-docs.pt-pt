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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74829116"
---
|**VpN<br><br>Gateway Geração** |**SKU**   | **S2S/VNet para VNet<br>Túneis** | **Ligações SSTP <br>P2S** | **Conexões<br> P2S IKEv2/OpenVPN** | **Referência de<br>Débito de Agregação** | **BGP** | **Zona redundante** |
|---            |---         | ---        | ---       | ---            | ---       | ---       | ---|
|**Geração 1**|**Básico**   | Um máximo de 10    | Um máximo de 128  | Não suportado  | 100 Mbps  | Não suportado| Não |
|**Geração 1**|**VpnGw1**  | Um máximo de 30*   | Um máximo de 128  | Um máximo de 250       | 650 Mbps  | Suportado | Não |
|**Geração 1**|**VpnGw2**  | Um máximo de 30*   | Um máximo de 128  | Um máximo de 500       | 1 Gbps    | Suportado | Não |
|**Geração 1**|**VpnGw3**  | Um máximo de 30*   | Um máximo de 128  | Um máximo de 1000      | 1,25 Gbps | Suportado | Não |
|**Geração 1**|**VpnGw1AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 250       | 650 Mbps  | Suportado | Sim |
|**Geração 1**|**VpnGw2AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 500       | 1 Gbps    | Suportado | Sim |
|**Geração 1**|**VpnGw3AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 1000      | 1,25 Gbps | Suportado | Sim |
|        |            |            |           |                |           |           |     |
|**Geração 2**|**VpnGw2**  | Um máximo de 30*   | Um máximo de 128  | Um máximo de 500       | 1,25 Gbps | Suportado | Não |
|**Geração 2**|**VpnGw3**  | Um máximo de 30*   | Um máximo de 128  | Um máximo de 1000      | 2.5 Gbps  | Suportado | Não |
|**Geração 2**|**VpnGw4**  | Um máximo de 30*   | Um máximo de 128  | Um máximo de 5000      | 5 Gbps    | Suportado | Não |
|**Geração 2**|**VpnGw5**  | Um máximo de 30*   | Um máximo de 128  | Um máximo de 10000      | 10 Gbps   | Suportado | Não |
|**Geração 2**|**VpnGw2AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 500       | 1,25 Gbps | Suportado | Sim |
|**Geração 2**|**VpnGw3AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 1000      | 2.5 Gbps  | Suportado | Sim |
|**Geração 2**|**VpnGw4AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 5000      | 5 Gbps    | Suportado | Sim |
|**Geração 2**|**VpnGw5AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 10000      | 10 Gbps   | Suportado | Sim |

(*) Utilize a [WAN Virtual](../articles/virtual-wan/virtual-wan-about.md) se precisar de mais de 30 túneis S2S VPN.

* A redimensionamento de VpnGw SKUs é permitida dentro da mesma geração, exceto a redimensionamento do SKU Básico. O Basic SKU é um SKU legado e tem limitações de recursos. Para passar do Basic para outro VpnGw SKU, deve eliminar o gateway Basic SKU VPN e criar uma nova porta de entrada com a combinação de tamanho Geração e SKU desejada.

* Esses limites de ligação são separados. Por exemplo, pode ter 128 ligações SSTP e também 250 ligações IKEv2 num SKU VpnGw1.

* As informações sobre os preços estão disponíveis na página [Preços](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Pode encontrar informações sobre SLA (contrato de nível de serviço) na página do [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* Num único túnel, pode ser alcançada uma potência máxima de 1 gbps. O Benchmark agregado de produção na tabela acima baseia-se em medições de múltiplos túneis agregados através de um único portal. A Referência de Débito Agregado para um Gateway de VPN é uma combinação de S2S + P2S. **Se tiver muitas ligações P2S, isso pode afetar negativamente uma ligação S2S devido às limitações de débito.** O Benchmark de Produção Agregado não é uma entrada garantida devido às condições de tráfego da Internet e aos seus comportamentos de aplicação.

Para ajudar os nossos clientes a compreender o desempenho relativo das SKUs usando diferentes algoritmos, usamos ferramentas de iPerf e CTSTraffic disponíveis ao público para medir os desempenhos. A tabela abaixo lista os resultados dos testes de desempenho para a Geração 1, VpnGw SKUs. Como pode ver, o melhor desempenho é obtido quando usamos algoritmo GCMAES256 tanto para encriptação iPsec como para integridade. Obtivemos um desempenho médio ao usar AES256 para encriptação IPsec e SHA256 para Integridade. Quando usamos O DES3 para encriptação IPsec e SHA256 para integridade, obtivemos o desempenho mais baixo.

|**Geração**|**SKU**   | **Algoritmos<br>usados** | **Entrada<br>observada** | **Pacotes por<br>segundo observados** |
|---           |---       | ---                 | ---            | ---                    |
|**Geração 1**|**VpnGw1**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58,000<br>50 000<br>50 000|
|**Geração 1**|**VpnGw2**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90.000<br>80.000<br>55,000|
|**Geração 1**|**VpnGw3**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105,000<br>90.000<br>60 000|
|**Geração 1**|**VpnGw1AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58,000<br>50 000<br>50 000|
|**Geração 1**|**VpnGw2AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90.000<br>80.000<br>55,000|
|**Geração 1**|**VpnGw3AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105,000<br>90.000<br>60 000|
