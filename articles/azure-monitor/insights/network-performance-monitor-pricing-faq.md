---
title: FAQ sobre preços para o Monitor de desempenho de rede do Azure | Documentos da Microsoft
description: Perguntas mais frequentes sobre - Monitor de desempenho de rede do Azure
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: cherylmc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: agummadi
ms.openlocfilehash: 68e84d8ac82d5cdd52bf92df858730315514c17c
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612858"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Alterações de preços para o Monitor de desempenho de rede do Azure

Temos atendendo a solicitações seus comentários e recentemente introduzidos um [os novos preços experiência](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) para vários serviços de monitorização no Azure. Este artigo captura as alterações de preços relacionadas com o Azure [Monitor de desempenho de rede](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) num formato de pergunta e resposta de fácil leitura.

Monitor de desempenho de rede é composta por três componentes:
* [Monitor de desempenho](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Monitor do ponto final de serviço](https://docs.microsoft.com/azure/networking/network-monitoring-overview)
* [Monitor do ExpressRoute](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

As secções seguintes explicam as alterações de preços para os componentes NPM.

## <a name="performance-monitor"></a>Monitorização de Desempenho

**Como foi a utilização do Monitor de desempenho cobrado no modelo antigo?**

A faturação para o NPM foi com base na utilização e consumo dos dois componentes:
* **Nós**: Todas as transações sintéticas originam e os nós de terminar. Nós também são conhecidos como agentes ou agentes de gestão da Microsoft.
* **Dados**: Os resultados dos vários testes de rede são armazenados na área de trabalho do Log Analytics.

Sob o modelo antigo, a fatura foi calculada com base no número de nós e o volume de dados gerados. 

**Como é cobrada a utilização do Monitor de desempenho sob o modelo de novo?**

A funcionalidade de Monitor de desempenho no NPM agora é cobrada com base numa combinação de: 

* Ligações de sub-rede monitorizadas
* Volume de dados

**O que é uma ligação de sub-rede?**

Monitor de desempenho monitoriza a ligação entre dois ou mais localizações na rede. A ligação entre um grupo de nós ou agentes numa sub-rede e um grupo de nós numa sub-rede de outra, é chamada de uma ligação de sub-rede.

**Tenho duas sub-redes (A e B), e tenho vários agentes em cada sub-rede. Monitor de desempenho monitora a conectividade entre todos os agentes na sub-rede A e todos os agentes na sub-rede B. Vou ser cobrado com base no número de ligações de sub-rede entre?**

Não. Para efeitos de faturação, todas as ligações de sub-rede A sub-rede B são agrupadas numa ligação de sub-rede. A faturação para uma única ligação. Monitor de desempenho continua a monitorizar a conectividade entre vários agentes em cada sub-rede.

**Quais são os custos para a monitorização de uma ligação de sub-rede?**

O custo de monitorização de uma ligação de sub-rede única durante todo o mês, consulte a [malha de Ping](https://azure.microsoft.com/pricing/details/network-watcher/) secção.

**Quais são os encargos de dados que gera o Monitor de desempenho?**

O custo de ingestão (carregamento de dados para a área de trabalho do Log Analytics no Azure Monitor, processamento e a indexação) está disponível na [página de preços](https://azure.microsoft.com/pricing/details/log-analytics/) para o Log Analytics, na secção de ingestão de dados. O custo da retenção de dados (isto é, dados mantidos de acordo com a opção do cliente, além do primeiro mês) também está disponível na [página de preços](https://azure.microsoft.com/pricing/details/log-analytics/), na secção de retenção de dados.


## <a name="expressroute-monitor"></a>Monitor do ExpressRoute

**Quais são os custos de utilização do Monitor do ExpressRoute?**

Custos de Monitor do ExpressRoute são faturados com base no volume de dados gerados durante a monitorização. Para obter mais informações, consulte "Quais são os encargos de dados que gera o Monitor de desempenho?"

**Eu uso o Monitor do ExpressRoute para monitorizar vários circuitos do ExpressRoute. Sou cobrado com base no número de circuitos a ser monitorizado?**

Não são cobradas com base no número de circuitos ou o tipo de peering (por exemplo, peering privado, peering da Microsoft). É cobrado com base no volume de dados, conforme explicado anteriormente.

**O que é o volume de dados gerados quando um único circuito monitoriza o ExpressRoute?**

O volume de dados gerados por mês, quando o ExpressRoute monitoriza uma ligação de peering privada, é o seguinte:

|Percentil      |Dados/mês (MB)|
| :---:          |           ---:|
|50<sup>th</sup> |            192|
|60<sup>th</sup> |            256|
|70<sup>th</sup> |            360|
|80<sup>th</sup> |            498|
|90<sup>th</sup> |            870|
|95<sup>th</sup> |           1560|


De acordo com esta tabela, os clientes no percentil 50th pagam 192 MB de dados. No USD $2.30/GB, o primeiro mês, o custo incorrido para a monitorização um circuito está USD $0.43 (= 192 * 2.30 / 1024).

**Quais são algumas razões para variações do volume de dados?**

O volume de dados gerados de monitorização depende de vários fatores, tais como:
* Número de agentes. Aumenta a precisão do isolamento de falhas com um aumento no número de agentes.
* Número de saltos na rede.
* Número de caminhos entre a origem e destino.

Os clientes nos percentis superior (na tabela anterior), normalmente, a monitorizar os circuitos de vários pontos vantajosos na sua rede no local. Vários agentes também são colocados-se na rede, mais distantes o router de limite de fornecedor de serviço. Os agentes, muitas vezes, são colocados em vários sites de utilizador, ramificações e racks nos centros de dados.

## <a name="service-endpoint-monitor"></a>Monitor do ponto final de serviço

**Quais são os custos de utilização do Monitor do ponto final de serviço?**

As cobranças da utilização do Monitor de ponto final de serviço são calculadas com base em:
* Número de ligações
* Volume de dados

**O que é uma ligação?**

Uma ligação é um teste de acessibilidade para um ponto de extremidade (serviço de rede ou URL) de um único agente durante todo o mês. Por exemplo, a monitorização de uma ligação para bing.com por três agentes constitui três ligações.

**Quais são os custos para o Monitor de ponto final de serviço?**

Consulte a [monitorização de ligação](https://azure.microsoft.com/pricing/details/network-watcher/) secção para o custo da monitorização de um ponto de extremidade durante todo o mês. O custo para dados está disponível na [página de preços](https://azure.microsoft.com/pricing/details/log-analytics/) para o Log Analytics, na secção de ingestão de dados.

## <a name="references"></a>Referências

[Log Analytics FAQ sobre preços](https://azure.microsoft.com/pricing/details/log-analytics/): A secção de FAQ tem informações sobre o escalão gratuito, por nó de preços e outros detalhes de preços.

