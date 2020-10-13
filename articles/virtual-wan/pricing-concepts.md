---
title: Sobre os preços de WAN virtual
titleSuffix: Azure Virtual WAN
description: Este artigo descreve questões comuns de preços do WAN virtual
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: reyandap
ms.custom: references_pricing
ms.openlocfilehash: b4025990a1a62351d3971d788558dea8ecb390ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327962"
---
# <a name="about-virtual-wan-pricing"></a>Sobre os preços de WAN virtual

A Azure Virtual WAN reúne vários serviços de rede e segurança num quadro unificado. Baseia-se num hub e na arquitetura de fala, onde os hubs são geridos pela Microsoft com vários serviços fornecidos dentro do hub, tais como VPN, ExpressRoute, User VPN (Ponto a local), Firewall, Encaminhamento, etc.

Cada serviço em VIRTUAL WAN tem o preço. Portanto, sugerir um único preço não é aplicável a VIRTUAL WAN. A [Calculadora de Preços Azure](https://azure.microsoft.com/pricing/calculator/) fornece um mecanismo para obter o custo, que se baseia nos serviços prestados num WAN virtual. Este artigo discute perguntas comumente sobre preços de WAN virtuais.

>[!NOTE]
>Para obter informações sobre preços correntes, consulte [os preços de ODDS Virtuais.](https://azure.microsoft.com/pricing/details/virtual-wan/)
>

## <a name="common-pricing-questions"></a><a name="questions"></a>Questões de preços comuns

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>O que é uma unidade de escala?

Uma unidade de **escala** fornece a unidade para a capacidade agregada de Site-to-site (S2S), Point-to-site (P2S) e ExpressRoute (ER) num hub virtual. Por exemplo:

* **1 A unidade de escala S2S VPN** implica uma capacidade total de 500 Mbps VPN gateway (são implantados dois casos para resiliência) num hub virtual que custa $0.361/hora.
* **Uma unidade de escala ER** implica um total de 2 Gbps ER gateway no hub virtual que custa $0,42/hora.
* **5 unidades de escala ER** implicariam um total de 10 Gbps ER gateway dentro de um hub virtual VNet com preços de $0,42*5/h. Er incrementa $0,25/hora da 6ª para a 10ª escala.

### <a name="what-is-a-connection-unit"></a><a name="connection-unit"></a>O que é uma unidade de ligação?

Uma unidade de **ligação** aplica-se a qualquer ponto final no local/ponto final não-Microsoft que se ligue aos gateways Azure. Para a VPN site-to-site, isto implica ramos. Para a VPN do utilizador (ponto a local), isto implica utilizadores remotos. Para o ExpressRoute, isto implica ligações de circuito ExpressRoute.<br>Por exemplo:

* Uma ligação de ramo que ligue à Azure VPN num hub virtual custa $0,05/hora. Portanto, 100 ligações de filial conectando-se a um hub virtual Azure custariam $0,05*100/h.

* Duas ligações do circuito ExpressRoute que se ligam a um hub virtual custariam $0,05*2/hora.

* Três ligações remotas do utilizador que se ligam ao gateway P2S do hub virtual Azure custariam $0,03*3/hora.

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>Como são calculadas as taxas de transferência de dados?

* Qualquer trânsito que entre em Azure não é cobrado. O tráfego que sai de Azure (via VPN, ExpressRoute ou ligações VPN do utilizador ponto-a-local) está sujeito às taxas padrão [de transferência de dados Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

* Para taxas de transferência de dados entre um hub VIRTUAL WAN, e um hub remoto virtual WAN ou VNet em uma região diferente do hub de origem, os encargos de transferência de dados aplicam-se para o tráfego deixando um hub. Exemplo: O tráfego que sai de um centro dos EUA oriental será cobrado $0,02/GB indo para um centro dos EUA. Não há nenhuma carga para o tráfego entrando no centro oeste dos EUA. As seguintes tabelas mostram as acusações.

As tabelas abaixo utilizam as seguintes abreviaturas: {NAM: América do Norte}, {EU: Europe}, {MEA: Middle East Africa}, {OC: Oceania (Austrália Central e Austrália Central 2)}, {LATAM: América Latina} 

**Preços intra-continente(*)**

| Intra-Continent| Preço ($/GB)|
|---|---|
| NAM para NAM|$0,02 |
| UE à UE |$0,02 |
| ASIA-ÁSIA (excluindo a China)|$0,10 |
| MEA a MEA|$0,16 |
| LATAM-LATAM |$0,16 |
| OC-OC|$0,12 |

**Preços intercontinentais(*)**

| Inter-Continental| Preço ($/GB)|
|---|---|
| Do NAM à UE ou à UE até ao NAM |$0,07 |
| Da LATAM a qualquer lugar |$0,17 |
| DE MEA a qualquer lugar |$0,17 |
| Da OCEANIA a qualquer lugar |$0,12 |
| Da ÁSIA (exceto CHINA) para qualquer lugar |$0,12 |

(*) Algumas taxas podem ser aplicadas a partir de 1 de agosto de 2020.

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>Qual é a diferença entre uma taxa standard de hub e uma taxa de processamento standard?

O WAN virtual vem em dois sabores:

* Um **WAN virtual básico,** onde os utilizadores podem implementar vários hubs e desfrutar da conectividade VPN Site-to-site. Um WAN virtual básico não tem capacidades avançadas tais como hubs totalmente malhados, conectividade ExpressRoute, conectividade VPN/Ponto-a-local VPN, conectividade transitiva VNet-to-VNet, conectividade de trânsito VPN e ExpressRoute, ou Firewall Azure, etc. Não existe taxa base ou taxa de processamento de dados para centros num WAN virtual básico.

* Um **WAN virtual standard** fornece capacidades avançadas, tais como hubs totalmente malhados, conectividade ExpressRoute, conectividade VPN/VPN ponto-a-local, conectividade transitiva VNet-to-VNet, conectividade de trânsito VPN e ExpressRoute, Azure Firewall, etc. Todo o encaminhamento de hub virtual é fornecido por um router que permite vários serviços em um hub virtual. Há uma taxa base para o hub, que tem um preço de $0,25/hora. Há também uma taxa para o processamento de dados no router de hub virtual para a conectividade de trânsito VNet-to-VNet. Veja a seguinte figura.

 Na figura **exemplo** abaixo, existem dois VNets, VNET 1 e VNET 2. Vamos supor que há 1 Dados gbps sendo enviados de um VM em VNET 1 para outro VM em VNET 2. Aplicam-se os seguintes encargos:

* Taxa base do Hub Virtual $0.25/hr

* Taxa de processamento de dados de hub virtual de $0,02/GB para 1 Gbps

**Exemplo**

   :::image type="content" source="./media/pricing-concepts/figure-1.png" alt-text="Exemplo":::

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)

* Para obter preços atuais, consulte [os preços de WAN virtuais.](https://azure.microsoft.com/pricing/details/virtual-wan/)
