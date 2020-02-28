---
title: Preços FAQ para Monitor de Desempenho da Rede Azure  Microsoft Docs
description: Perguntas frequentes - Monitor de Desempenho da Rede Azure
ms.subservice: logs
ms.topic: conceptual
author: agummadi
ms.author: agummadi
ms.date: 04/02/2018
ms.openlocfilehash: c5a80c7ff6d08ce601a6c2bbb0db0ef866d3b425
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77654397"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Alterações de preços para o Monitor de Desempenho da Rede Azure

Ouvimos o seu feedback e introduzimos recentemente uma [nova experiência](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) de preços para vários serviços de monitorização em todo o Azure. Este artigo captura as alterações de preços relacionadas com o Azure [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) num formato de perguntas e respostas de fácil leitura.

O Monitor de Desempenho da Rede é composto por três componentes:
* [Monitor de Desempenho](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Monitor de fim de serviço](https://docs.microsoft.com/azure/networking/network-monitoring-overview)
* [ExpressRoute Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

As seguintes secções explicam as alterações de preços dos componentes NPM.

## <a name="performance-monitor"></a>Monitorização de Desempenho

**Como foi cobrado o uso do Performance Monitor no modelo antigo?**

A faturação para OPm Foi baseada na utilização e consumo de dois componentes:
* **Nós**: Todas as transações sintéticas têm origem e terminam nos nós. Os nódosos também são referidos como agentes ou agentes de gestão da Microsoft.
* **Dados**: Os resultados dos vários testes de rede são armazenados no espaço de trabalho do Log Analytics.

Sob o modelo antigo, a conta foi calculada com base no número de nós e no volume de dados gerados. 

**Como é que o uso do Monitor de Desempenho é carregado ao abrigo do novo modelo?**

A funcionalidade Performance Monitor em NPM é agora faturada com base numa combinação de: 

* Ligações de sub-rede monitorizadas
* Volume de dados

**O que é uma ligação de sub-rede?**

O Monitor de Desempenho monitoriza a conectividade entre dois ou mais locais da rede. A ligação entre um grupo de nós ou agentes numa subnet, e um grupo de nós noutra subnet, é chamada de ligação de sub-rede.

**Tenho duas subredes (A e B), e tenho vários agentes em cada subnet. O Monitor de Desempenho monitoriza a conectividade de todos os agentes na sub-rede A a todos os agentes da sub-rede B. Serei acusado com base no número de ligações inter-sub-rede?**

Não. Para efeitos de faturação, todas as ligações da sub-rede A à sub-rede B são agrunadas numa única ligação de sub-rede. É cobrado por uma única ligação. O Monitor de Desempenho continua a monitorizar a conectividade entre vários agentes em cada subnet.

**Quais são os custos para monitorizar uma ligação de sub-rede?**

Para o custo de monitorização de uma única ligação de sub-rede durante todo o mês, consulte a secção [Ping Mesh.](https://azure.microsoft.com/pricing/details/network-watcher/)

**Quais são os encargos para os dados que o Performance Monitor gera?**

O custo de ingestão (upload de dados para log Analytics workspace in Azure Monitor, processamento e indexação) está disponível na página de [preços](https://azure.microsoft.com/pricing/details/log-analytics/) do Log Analytics, na secção de Ingestion de Dados. O custo para a retenção de dados (isto é, dados retidos por opção do cliente, para além do primeiro mês) também está disponível na página de [preços,](https://azure.microsoft.com/pricing/details/log-analytics/)na secção de Retenção de Dados.


## <a name="expressroute-monitor"></a>ExpressRoute Monitor

**Quais são os encargos para a utilização do Monitor ExpressRoute?**

Os encargos para o ExpressRoute Monitor são faturados com base no volume de dados gerados durante a monitorização. Para obter mais informações, consulte "Quais são os encargos para os dados que o Performance Monitor gera?"

**Uso o Monitor ExpressRoute para monitorizar vários circuitos ExpressRoute. Estou carregado com base no número de circuitos a serem monitorizados?**

Não é cobrado com base no número de circuitos ou no tipo de observação (por exemplo, peering privado, microsoft peering). É cobrado com base no volume de dados, como explicado anteriormente.

**Qual é o volume de dados gerado quando o ExpressRoute monitoriza um único circuito?**

O volume de dados gerados por mês, quando o ExpressRoute monitoriza uma ligação de pares privados, é o seguinte:

|Percentil      |Dados/mês (MB)|
| :---:          |           ---:|
|50<sup>º</sup> |            192|
|60<sup>º</sup> |            256|
|70<sup>º</sup> |            360|
|80<sup>º</sup> |            498|
|90<sup>º</sup> |            870|
|95<sup></sup> |           1560|


De acordo com esta tabela, os clientes do percentil 50 pagam por 192 MB de dados. A USD $2,30/GB no primeiro mês, o custo incorrido para a monitorização de um circuito é USD $0,43 (= 192 * 2,30 / 1024).

**Quais são algumas razões para variações no volume de dados?**

O volume de dados de monitorização gerados depende de vários fatores, tais como:
* Número de agentes. A precisão do isolamento de falhas aumenta com o aumento do número de agentes.
* Número de lúpulos na rede.
* Número de caminhos entre a fonte e o destino.

Os clientes dos percentilmais elevados (na tabela anterior) geralmente monitorizam os seus circuitos a partir de vários pontos de vista na sua rede no local. Vários agentes também são colocados mais fundo na rede, mais longe do router de borda do prestador de serviços. Os agentes são frequentemente colocados em vários sites de utilizadores, agências e racks em datacenters.

## <a name="service-endpoint-monitor"></a>Monitor de fim de serviço

**Quais são os encargos para a utilização do Monitor de Ponto final do Serviço?**

Os encargos para a utilização do Monitor de Fim de Serviço são calculados com base em:
* Número de ligações
* Volume de dados

**O que é uma ligação?**

Uma ligação é um teste de alcance a um ponto final (URL ou serviço de rede) de um único agente durante todo o mês. Por exemplo, monitorizar uma ligação com bing.com de três agentes constitui três ligações.

**Quais são os custos para o Monitor de Ponto final do Serviço?**

Consulte a secção de Monitorização de [Ligação](https://azure.microsoft.com/pricing/details/network-watcher/) para o custo de monitorização de um ponto final durante todo o mês. A cobrança de dados está disponível na [página de preços](https://azure.microsoft.com/pricing/details/log-analytics/) do Log Analytics, na secção de Ingestion de Dados.

## <a name="references"></a>Referências

[Log Analytics Pricing FAQ](https://azure.microsoft.com/pricing/details/log-analytics/): A secção DE PERGUNTAS FREQUENTES tem informações sobre o nível livre, por preços de nó e outros detalhes de preços.

