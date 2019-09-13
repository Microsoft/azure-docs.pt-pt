---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/22/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 65c1011e6e005c190d1ae5d51fdd009f66a20956
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70919725"
---
|**SKU**   | **S2S/VNet para VNet<br>Túneis** | **Ligações SSTP <br>P2S** | **P2S<br> conexões IKEv2/OpenVPN** | **Referência de<br>Débito de Agregação** | **BGP** | **Com redundância de zona** |
|---       | ---        | ---       | ---            | ---       | --- | --- |
|**Básica** | Um máximo de 10    | Um máximo de 128  | Não Suportada  | 100 Mbps  | Não Suportada| Não |
|**VpnGw1**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 250       | 650 Mbps  | Suportadas | Não |
|**VpnGw2**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 500       | 1 Gbps    | Suportadas | Não |
|**VpnGw3**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 1000      | 1,25 Gbps | Suportadas | Não |
|**VpnGw1AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 250       | 650 Mbps  | Suportadas | Sim |
|**VpnGw2AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 500       | 1 Gbps    | Suportadas | Sim |
|**VpnGw3AZ**| Um máximo de 30*   | Um máximo de 128  | Um máximo de 1000      | 1,25 Gbps | Suportadas | Sim |

(*) Utilize a [WAN Virtual](../articles/virtual-wan/virtual-wan-about.md) se precisar de mais de 30 túneis S2S VPN.

* Esses limites de ligação são separados. Por exemplo, pode ter 128 ligações SSTP e também 250 ligações IKEv2 num SKU VpnGw1.

* As informações sobre os preços estão disponíveis na página [Preços](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Pode encontrar informações sobre SLA (contrato de nível de serviço) na página do [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* VpnGw1, VpnGw2, VpnGw3, VpnGw1AZ, VpnGw2AZ e VpnGw3AZ têm suporte para gateways de VPN usando apenas o modelo de implantação do Gerenciador de recursos.

* O SKU básico é considerado um SKU herdado. A SKU básica tem certas limitações de recursos. Você não pode redimensionar um gateway que usa uma SKU básica para uma das novas SKUs de gateway. em vez disso, você deve alterar para uma nova SKU, o que envolve excluir e recriar o gateway de VPN. Verifique se o recurso de que você precisa tem suporte antes de usar o SKU básico.

* A Referência de Débito de Agregação baseia-se em medidas de vários túneis agregados através de um único gateway. A Referência de Débito Agregado para um Gateway de VPN é uma combinação de S2S + P2S. **Se tiver muitas ligações P2S, isso pode afetar negativamente uma ligação S2S devido às limitações de débito.** O benchmark de taxa de transferência agregada não é uma taxa de transferência garantida devido às condições de tráfego da Internet e aos comportamentos do aplicativo.

* Para ajudar nossos clientes a entender o desempenho relativo das SKUs VpnGw, usamos as ferramentas iPerf e CTSTraffic disponíveis publicamente para medir o desempenho. A tabela abaixo lista os resultados de testes de desempenho usando algoritmos diferentes. Como você pode ver, o melhor desempenho é obtido quando usamos o algoritmo GCMAES256 para a criptografia e a integridade do IPsec. Temos o desempenho médio ao usar AES256 para criptografia IPsec e SHA256 para integridade. Quando usamos DES3 para criptografia IPsec e SHA256 para integridade, temos o menor desempenho.

|**SKU**   | **Algoritmos<br>usados** | **Taxa<br>de transferência observada** | **Pacotes observados<br>por segundo** |
|---       | ---                 | ---            | ---                    |
|**VpnGw1**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58.000<br>50,000<br>50,000|
|**VpnGw2**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55.000|
|**VpnGw3**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105.000<br>90,000<br>60,000|
|**VpnGw1AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58.000<br>50,000<br>50,000|
|**VpnGw2AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55.000|
|**VpnGw3AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105.000<br>90,000<br>60,000|
