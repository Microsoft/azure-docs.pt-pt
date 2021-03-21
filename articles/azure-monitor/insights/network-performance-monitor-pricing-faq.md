---
title: Preços FAQ para monitor de desempenho da rede Azure | Microsoft Docs
description: Perguntas frequentes - Azure Network Performance Monitor
ms.topic: conceptual
author: agummadi
ms.author: agummadi
ms.date: 04/02/2018
ms.openlocfilehash: 56163d3267a69099cbf6a18266168e9198ea04c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101717439"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Alterações de preços para O Monitor de Desempenho da Rede Azure

> [!IMPORTANT]
> A partir de 1 de julho de 2021, não poderá adicionar novos testes num espaço de trabalho existente ou permitir um novo espaço de trabalho no Network Performance Monitor. Pode continuar a utilizar os testes criados antes de 1 de julho de 2021. Para minimizar a perturbação do serviço nas suas cargas de trabalho atuais, [migrar os seus testes do Monitor de Desempenho da Rede para o novo Monitor de Ligação](../../network-watcher/migrate-to-connection-monitor-from-network-performance-monitor.md) no Azure Network Watcher antes de 29 de fevereiro de 2024.

Ouvimos o seu feedback e introduzimos recentemente uma [nova experiência de preços](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) para vários serviços de monitorização em todo o Azure. Este artigo captura as alterações de preços relacionadas com o Azure [Network Performance Monitor](../../networking/network-monitoring-overview.md) (NPM) num formato de perguntas e respostas de fácil leitura.

O Monitor de Desempenho da Rede é composto por três componentes:
* [Monitorização de Desempenho](../../networking/network-monitoring-overview.md#performance-monitor)
* [Monitor de ponto de terminação de serviço](../../networking/network-monitoring-overview.md)
* [ExpressRoute Monitor](../../networking/network-monitoring-overview.md#expressroute-monitor)

As seguintes secções explicam as alterações de preços dos componentes NPM.

## <a name="performance-monitor"></a>Monitorização de Desempenho

**Como foi o uso do Monitor de Desempenho faturado no modelo antigo?**

A faturação para nPM baseou-se na utilização e consumo de dois componentes:
* **Nós**: Todas as transações sintéticas são originárias e terminadas nos nós. Os nós também são referidos como agentes ou agentes de gestão da Microsoft.
* **Dados**: Os resultados dos vários testes de rede são armazenados no espaço de trabalho Log Analytics.

Segundo o modelo antigo, a conta foi calculada com base no número de nós e no volume de dados gerados. 

**Como é cobrada a utilização do Monitor de Desempenho ao abrigo do novo modelo?**

A funcionalidade de Monitor de Desempenho em NPM é agora faturada com base numa combinação de: 

* Ligações de sub-redes monitorizadas
* Volume de dados

**O que é uma ligação de sub-rede?**

O Monitor de Desempenho monitoriza a conectividade entre dois ou mais locais da rede. A ligação entre um grupo de nós ou agentes numa sub-rede, e um grupo de nós noutra sub-rede, é chamada de ligação sub-rede.

**Tenho duas sub-redes (A e B), e tenho vários agentes em cada sub-rede. O Monitor de Desempenho monitoriza a conectividade de todos os agentes na sub-rede A a todos os agentes da sub-rede B. Serei cobrado com base no número de ligações inter-sub-redes?**

N.º Para efeitos de faturação, todas as ligações da sub-rede A à sub-rede B são agrupadas numa ligação de sub-rede. Estás cobrado por uma única ligação. O Monitor de Desempenho continua a monitorizar a conectividade entre vários agentes em cada sub-rede.

**Quais são os custos para a monitorização de uma ligação de sub-redes?**

Para o custo de monitorização de uma única ligação de sub-redes durante todo o mês, consulte a secção [de Malha ping.](https://azure.microsoft.com/pricing/details/network-watcher/)

**Quais são os encargos para os dados que o Monitor de Desempenho gera?**

A taxa de ingestão (upload de dados para log analytics espaço de trabalho em Azure Monitor, processamento e indexação) está disponível na [página de preços](https://azure.microsoft.com/pricing/details/log-analytics/) para Log Analytics, na secção Data Ingestion. A taxa de retenção de dados (ou seja, dados retidos por opção do cliente, para além do primeiro mês) também está disponível na [página de preços,](https://azure.microsoft.com/pricing/details/log-analytics/)na secção De Retenção de Dados.


## <a name="expressroute-monitor"></a>ExpressRoute Monitor

**Quais são os encargos para o uso do ExpressRoute Monitor?**

Os encargos para o ExpressRoute Monitor são faturados com base no volume de dados gerados durante a monitorização. Para mais informações, consulte "Quais são os encargos para os dados que o Monitor de Desempenho gera?"

**Uso o Monitor ExpressRoute para monitorizar vários circuitos ExpressRoute. Sou cobrado com base no número de circuitos que estão a ser monitorizados?**

Não é cobrado com base no número de circuitos ou no tipo de observação (por exemplo, espreitar privado, espreitar microsoft). É cobrado com base no volume de dados, como explicado anteriormente.

**Qual é o volume de dados gerado quando o ExpressRoute monitoriza um único circuito?**

O volume de dados gerados por mês, quando o ExpressRoute monitoriza uma ligação de observação privada, é o seguinte:

|Percentil      |Dados/mês (MB)|
| :---:          |           ---:|
|50<sup>th</sup> |            192|
|60<sup></sup> |            256|
|70<sup></sup> |            360|
|80<sup></sup> |            498|
|90<sup></sup> |            870|
|95<sup></sup> |           1560|


De acordo com esta tabela, os clientes do percentil 50 pagam 192 MB de dados. A USD $2,30/GB para o primeiro mês, o custo incorrido para a monitorização de um circuito é DE USD $0,43 (= 192 * 2,30 / 1024).

**Quais são algumas razões para variações no volume de dados?**

O volume de dados de monitorização gerados depende de vários fatores, tais como:
* Número de agentes. A precisão do isolamento de falhas aumenta com o aumento do número de agentes.
* Número de saltos na rede.
* Número de caminhos entre a fonte e o destino.

Os clientes com percentó de percentóneas mais elevados (na tabela anterior) monitorizam normalmente os seus circuitos a partir de vários pontos de vista na sua rede de acesso. Vários agentes também são colocados mais fundo na rede, mais longe do router de borda do prestador de serviços. Os agentes são frequentemente colocados em vários sites de utilizadores, ramos e racks em datacenters.

## <a name="service-endpoint-monitor"></a>Monitor de ponto de terminação de serviço

**Quais são os encargos para a utilização do Service Endpoint Monitor?**

Os encargos de utilização do Service Endpoint Monitor são calculados com base em:
* Número de ligações
* Volume de dados

**O que é uma ligação?**

Uma ligação é um teste de alcance a um ponto final (URL ou serviço de rede) de um único agente durante todo o mês. Por exemplo, monitorizar uma ligação a bing.com de três agentes constitui três ligações.

**Quais são os custos para o Service Endpoint Monitor?**

Consulte a secção [de Monitorização de Ligação](https://azure.microsoft.com/pricing/details/network-watcher/) para saber o custo de monitorização de um ponto final durante todo o mês. A cobrança de dados está disponível na [página de preços](https://azure.microsoft.com/pricing/details/log-analytics/) do Log Analytics, na secção De Ingestão de Dados.

## <a name="references"></a>Referências

[Log Analytics Preços FAQ](https://azure.microsoft.com/pricing/details/log-analytics/): A secção faQ tem informações sobre nível livre, por preço de nó e outros detalhes de preços.