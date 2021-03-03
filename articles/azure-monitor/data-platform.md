---
title: Plataforma de dados Azure Monitor | Microsoft Docs
description: Os dados de monitorização recolhidos pelo Azure Monitor são separados em métricas leves e capazes de suportar cenários e registos em tempo real que são utilizados para análise avançada.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 7356b9bb814f8bca5465fe74d48409b9dbca6d3b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731702"
---
# <a name="azure-monitor-data-platform"></a>Plataforma de dados Azure Monitor

Permitindo a observabilidade em todos os ambientes de computação complexos de hoje em dia, executando aplicações distribuídas que dependem tanto de serviços em nuvem como no local, requer a recolha de dados operacionais de cada camada e cada componente do sistema distribuído. Você precisa ser capaz de realizar insights profundos sobre estes dados e consolidá-lo em um único painel de vidro com diferentes perspetivas para apoiar a multiplicidade de stakeholders na sua organização.

[O Azure Monitor](overview.md) recolhe e agrega dados de várias fontes numa plataforma comum de dados onde pode ser usado para análise, visualização e alerta. Fornece uma experiência consistente em além de dados de múltiplas fontes, o que lhe dá informações profundas em todos os seus recursos monitorizados e até mesmo com dados de outros serviços que armazenam os seus dados no Azure Monitor.


![Descrição geral do Azure Monitor](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Dados de observabilidade no Monitor Azure
Métricas, troncos e vestígios distribuídos são geralmente referidos como os três pilares da observabilidade. Estes são os diferentes tipos de dados que uma ferramenta de monitorização deve recolher e analisar para fornecer uma visibilidade suficiente de um sistema monitorizado. A observabilidade pode ser alcançada correlacionando dados de múltiplos pilares e agregando dados em todo o conjunto de recursos que estão sendo monitorizados. Como o Azure Monitor armazena dados de várias fontes em conjunto, os dados podem ser correlacionados e analisados usando um conjunto comum de ferramentas. Também correlaciona dados em várias subscrições e inquilinos da Azure, além de hospedar dados para outros serviços.

Os recursos azure geram uma quantidade significativa de dados de monitorização. O Azure Monitor consolida estes dados juntamente com a monitorização de dados de outras fontes numa plataforma Métrica ou Logs. Cada um é otimizado para cenários de monitorização específicos, e cada um suporta diferentes funcionalidades no Azure Monitor. Funcionalidades como análise de dados, visualizações ou alerta requerem que compreenda as diferenças para que possa implementar o cenário necessário da forma mais eficiente e rentável. Insights no Azure Monitor, como [Insights de Aplicação](app/app-insights-overview.md) ou [insights VM,](vm/vminsights-overview.md) têm ferramentas de análise que permitem focar-se no cenário de monitorização particular sem ter de compreender as diferenças entre os dois tipos de dados. 


### <a name="metrics"></a>Métricas
[As métricas](essentials/data-platform-metrics.md) são valores numéricos que descrevem algum aspeto de um sistema num determinado momento do tempo. São recolhidas em intervalos regulares e são identificadas com um carimbo de data/hora, um nome, um valor e uma ou mais etiquetas. As métricas podem ser agregadas com vários algoritmos, em comparação com outras métricas, e analisadas relativamente a tendências ao longo do tempo. 

As métricas no Azure Monitor são armazenadas numa base de dados de séries temporais que é otimizada para analisar dados com carimbo temporal. Isto torna as métricas particularmente adequadas para alertar e detetar rapidamente problemas. Eles podem dizer-lhe como o seu sistema está a funcionar, mas normalmente precisam de ser combinados com registos para identificar a causa principal dos problemas.

As métricas estão disponíveis para análise interativa no portal Azure com [o Azure Metrics Explorer.](essentials/metrics-getting-started.md) Podem ser adicionados a um [dashboard Azure](app/tutorial-app-dashboards.md) para visualização em combinação com outros dados e utilizados para [alertar](alerts/alerts-metric.md)em tempo real.

Leia mais sobre as Métricas do Monitor Azure, incluindo as suas fontes de [dados em Métricas no Azure Monitor](essentials/data-platform-metrics.md).

### <a name="logs"></a>Registos
[Os registos](logs/data-platform-logs.md) são eventos que ocorreram dentro do sistema. Podem conter diferentes tipos de dados e podem ser estruturados ou em formato livre texto com uma datatamp. Podem ser criados esporadicamente como eventos nas entradas de registo geradas no ambiente e um sistema sobrecarregado poderá gerar mais volume do registo.

Os registos no Azure Monitor são armazenados num espaço de trabalho log analytics baseado no [Azure Data Explorer,](/azure/data-explorer/) que fornece um poderoso motor de análise e [uma linguagem de consulta rica.](/azure/kusto/query/) Os registos normalmente fornecem informações suficientes para fornecer o contexto completo da questão que está a ser identificada e são valiosos para identificar casos de raiz de problemas.

> [!NOTE]
> É importante distinguir entre registos do Monitor Azure e fontes de dados de registo em Azure. Por exemplo, os eventos de nível de subscrição em Azure são escritos para um registo de [atividade](essentials/platform-logs-overview.md) que você pode ver a partir do menu Azure Monitor. A maioria dos recursos escreverá informações operacionais para um [registo de recursos](essentials/platform-logs-overview.md) que pode encaminhar para diferentes locais. O Azure Monitor Logs é uma plataforma de dados de registo que recolhe registos de atividade e registos de recursos, juntamente com outros dados de monitorização para fornecer uma análise profunda em todo o seu conjunto de recursos.


 Pode trabalhar com [consultas de registo](logs/log-query-overview.md) interativamente com o Log [Analytics](logs/log-query-overview.md) no portal Azure ou adicionar os resultados a um [dashboard Azure](app/tutorial-app-dashboards.md) para visualização em combinação com outros dados. Também pode criar [alertas de registo](alerts/alerts-log.md) que irão desencadear um alerta com base nos resultados de uma consulta de agendamento.

Leia mais sobre os Registos do Monitor Azure, incluindo as suas fontes de [dados em Registos no Azure Monitor](logs/data-platform-logs.md).

### <a name="distributed-traces"></a>Vestígios distribuídos
Os vestígios são uma série de eventos relacionados que seguem um pedido do utilizador através de um sistema distribuído. Podem ser usados para determinar o comportamento do código de aplicação e a realização de diferentes transações. Embora os registos sejam muitas vezes criados por componentes individuais de um sistema distribuído, um traço mede o funcionamento e desempenho da sua aplicação em todo o conjunto de componentes.

O rastreio distribuído no Azure Monitor é ativado com o [Application Insights SDK](app/distributed-tracing.md), e os dados de rastreio são armazenados com outros dados de registo de aplicações recolhidos pela Application Insights. Isto coloca-o à disposição das mesmas ferramentas de análise que outros dados de registo, incluindo consultas de registo, dashboards e alertas.

Leia mais sobre rastreio distribuído no [What is Distributed Tracing?](app/distributed-tracing.md)


## <a name="compare-azure-monitor-metrics-and-logs"></a>Compare métricas e registos do monitor Azure

A tabela a seguir compara métricas e registos no Azure Monitor.

| Atributo  | Métricas | Registos |
|:---|:---|:---|
| Benefícios | Leve e capaz de cenários em tempo real, como alertar. Ideal para deteção rápida de problemas. | Analisado com uma linguagem de consulta rica. Ideal para análise profunda e identificação da causa raiz. |
| Dados | Valores numéricos apenas | Dados de texto ou numéricos |
| Estrutura | Conjunto padrão de propriedades, incluindo tempo de amostra, monitorização de recursos, um valor numérico. Algumas métricas incluem múltiplas dimensões para uma definição posterior. | Conjunto único de propriedades dependendo do tipo de registo. |
| Coleção | Recolhidos em intervalos regulares. | Pode ser recolhido esporadicamente à medida que os eventos desencadeiam um registo a ser criado. |
| Ver no portal do Azure | Explorador de Métricas | Log Analytics |
| As fontes de dados incluem | Métricas de plataforma recolhidas a partir de recursos Azure.<br>Aplicações monitorizadas por Application Insights.<br>Personalizado definido por aplicação ou API. | Registos de aplicações e recursos.<br>Soluções de monitorização.<br>Agentes e extensões de VM.<br>Pedidos de inscrição e exceções.<br>Centro de Segurança do Azure.<br>API de Colecionador de Dados. |

## <a name="collect-monitoring-data"></a>Recolher dados de monitorização
Diferentes [fontes de dados para o Azure Monitor](agents/data-sources.md) escreverão para um espaço de trabalho Log Analytics (Logs) ou para a base de dados de métricas do Monitor Azure (Métricas) ou ambos. Algumas fontes escreverão diretamente para estas lojas de dados, enquanto outras podem escrever para outro local, como o armazenamento de Azure e exigir alguma configuração para preencher registos ou métricas. 

Consulte [métricas no Monitor E](essentials/data-platform-metrics.md) [Registos Azure no Monitor Azure](logs/data-platform-logs.md) para obter uma lista de diferentes fontes de dados que povoam cada tipo.


## <a name="stream-data-to-external-systems"></a>Transmitir dados para sistemas externos
Além de utilizar as ferramentas em Azure para analisar os dados de monitorização, poderá ter a obrigação de encaminhá-los para uma ferramenta externa, como um produto de informação de segurança e gestão de eventos (SIEM). Este reencaminhamento é normalmente feito diretamente a partir de recursos monitorizados através de [Azure Event Hubs](../event-hubs/index.yml). Algumas fontes podem ser configuradas para enviar dados diretamente para um centro de eventos enquanto você pode usar outro processo, como uma App Lógica para recuperar os dados necessários. Consulte [os dados de monitorização do Stream Azure num centro de eventos para consumo através de uma ferramenta externa](essentials/stream-monitoring-data-event-hubs.md) para obter mais detalhes.



## <a name="next-steps"></a>Passos seguintes

- Leia mais sobre [Métricas no Monitor Azure.](essentials/data-platform-metrics.md)
- Leia mais sobre [Registos no Monitor Azure](logs/data-platform-logs.md).
- Conheça os [dados de monitorização disponíveis](agents/data-sources.md) para diferentes recursos em Azure.

