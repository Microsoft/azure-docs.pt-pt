---
title: Perguntas frequentes sobre preços do Azure Monitor de Desempenho de Rede | Microsoft Docs
description: Perguntas frequentes-Monitor de Desempenho de Rede do Azure
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: agummadi
ms.author: agummadi
ms.date: 04/02/2018
ms.openlocfilehash: 3d69637c2851764363209ed8dfbe8c24c636ffba
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72898795"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Alterações de preços para o Azure Monitor de Desempenho de Rede

Nós ouvimos seus comentários e recentemente introduzimos uma [nova experiência de preços](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) para vários serviços de monitoramento no Azure. Este artigo captura as alterações de preço relacionadas ao [Monitor de desempenho de rede](https://docs.microsoft.com/azure/networking/network-monitoring-overview) do Azure (NPM) em um formato de pergunta e resposta fácil de ler.

Monitor de Desempenho de Rede consiste em três componentes:
* [Monitor de desempenho](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Monitor de ponto de extremidade de serviço](https://docs.microsoft.com/azure/networking/network-monitoring-overview)
* [Monitor do ExpressRoute](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

As seções a seguir explicam as alterações de preços para os componentes do NPM.

## <a name="performance-monitor"></a>Monitorização de Desempenho

**Como o uso do monitor de desempenho foi cobrado no modelo antigo?**

A cobrança para NPM foi baseada no uso e consumo de dois componentes:
* **Nós**: todas as transações sintéticas se originam e terminam nos nós. Os nós também são chamados de agentes ou agentes de gerenciamento da Microsoft.
* **Dados**: os resultados dos vários testes de rede são armazenados no espaço de trabalho log Analytics.

No modelo antigo, a conta foi calculada com base no número de nós e no volume de dados gerados. 

**Como o uso do monitor de desempenho é cobrado no novo modelo?**

O recurso de monitor de desempenho no NPM agora é cobrado com base em uma combinação de: 

* Links de sub-rede monitorados
* Volume de dados

**O que é um link de sub-rede?**

O monitor de desempenho monitora a conectividade entre dois ou mais locais na rede. A conexão entre um grupo de nós ou agentes em uma sub-rede e um grupo de nós em outra sub-rede é chamada de link de sub-rede.

**Tenho duas sub-redes (A e B) e tenho vários agentes em cada sub-rede. O monitor de desempenho monitora a conectividade de todos os agentes na sub-rede A para todos os agentes na sub-rede B. Serei cobrado com base no número de conexões entre sub-redes?**

Não. Para fins de cobrança, todas as conexões da sub-rede A à sub-rede B são agrupadas em um link de sub-rede. Você será cobrado por uma única conexão. O monitor de desempenho continua a monitorar a conectividade entre vários agentes em cada sub-rede.

**Quais são os custos para monitorar um link de sub-rede?**

Para o custo de monitorar um único link de sub-rede para o mês inteiro, consulte a seção [malha de ping](https://azure.microsoft.com/pricing/details/network-watcher/) .

**Quais são os encargos dos dados que o monitor de desempenho gera?**

A cobrança por ingestão (carregamento de dados para Log Analytics espaço de trabalho em Azure Monitor, processamento e indexação) está disponível na [página de preços](https://azure.microsoft.com/pricing/details/log-analytics/) para log Analytics, na seção ingestão de dados. A cobrança pela retenção de dados (ou seja, os dados retidos na opção do cliente, além do primeiro mês) também está disponível na [página de preços](https://azure.microsoft.com/pricing/details/log-analytics/), na seção retenção de dados.


## <a name="expressroute-monitor"></a>Monitor do ExpressRoute

**Quais são os encargos de uso do monitor do ExpressRoute?**

Os encargos do monitor do ExpressRoute são cobrados com base no volume de dados gerados durante o monitoramento. Para obter mais informações, consulte "quais são os encargos dos dados que o monitor de desempenho gera?"

**Uso o monitor do ExpressRoute para monitorar vários circuitos do ExpressRoute. Sou cobrado com base no número de circuitos que estão sendo monitorados?**

Você não é cobrado com base no número de circuitos ou no tipo de emparelhamento (por exemplo, emparelhamento privado, emparelhamento da Microsoft). Você é cobrado com base no volume de dados, conforme explicado anteriormente.

**Qual é o volume de dados gerados quando o ExpressRoute monitora um único circuito?**

O volume de dados gerados por mês, quando o ExpressRoute monitora uma conexão de emparelhamento privado, é o seguinte:

|Percentil      |Dados/mês (MB)|
| :---:          |           ---:|
|50<sup></sup> |            192|
|60<sup></sup> |            256|
|70<sup></sup> |            360|
|80<sup></sup> |            498|
|90<sup></sup> |            870|
|95<sup></sup> |           1560|


De acordo com essa tabela, os clientes no 50 º percentil pagam por 192 MB de dados. A USD $2,30/GB do primeiro mês, o custo incorrido para monitorar um circuito é de BRL $0.43 (= 192 * 2,30/1024).

**Quais são alguns motivos para variações no volume de dados?**

O volume de dados de monitoramento gerado depende de vários fatores, como:
* Número de agentes. A precisão do isolamento de falha aumenta com um aumento no número de agentes.
* Número de saltos na rede.
* Número de caminhos entre a origem e o destino.

Os clientes nos mais altos percentuais (na tabela anterior) geralmente monitoram seus circuitos de vários pontos privilegiando em sua rede local. Vários agentes também são colocados mais profundamente na rede, mais distante do roteador de borda do provedor de serviços. Os agentes geralmente são colocados em vários sites de usuário, branches e racks em datacenters.

## <a name="service-endpoint-monitor"></a>Monitor de ponto de extremidade de serviço

**Quais são os encargos para o uso do monitor de ponto de extremidade de serviço?**

Os encargos pelo uso do monitor de ponto de extremidade de serviço são calculados com base em:
* Número de conexões
* Volume de dados

**O que é uma conexão?**

Uma conexão é um teste de acessibilidade para um ponto de extremidade (URL ou serviço de rede) de um único agente para o mês inteiro. Por exemplo, o monitoramento de uma conexão com o bing.com de três agentes constitui três conexões.

**Quais são os custos para o monitor de ponto de extremidade de serviço?**

Consulte a seção [monitoramento de conexão](https://azure.microsoft.com/pricing/details/network-watcher/) para obter o custo de monitoramento de um ponto de extremidade para o mês inteiro. A cobrança de dados está disponível na [página de preços](https://azure.microsoft.com/pricing/details/log-analytics/) para log Analytics, na seção ingestão de dados.

## <a name="references"></a>Referências

[Log Analytics perguntas frequentes sobre preços](https://azure.microsoft.com/pricing/details/log-analytics/): a seção de perguntas frequentes tem informações sobre a camada gratuita, preços por nó e outros detalhes de preços.

