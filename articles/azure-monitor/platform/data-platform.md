---
title: Plataforma de dados Azure Monitor  Microsoft Docs
description: Os dados de monitorização recolhidos pelo Azure Monitor são separados em métricas leves e capazes de suportar cenários e registos em tempo real que são utilizados para análises avançadas.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: a1b5859341237c1b177ee8deaf636a67f4824948
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666552"
---
# <a name="azure-monitor-data-platform"></a>Plataforma de dados Azure Monitor

Permitir a observabilidade em todos os complexos ambientes informáticos de hoje em dia que executam aplicações distribuídas que dependem tanto de serviços em nuvem como no local, requer a recolha de dados operacionais de todas as camadas e todos os componentes do sistema distribuído. Você precisa ser capaz de realizar insights profundos sobre estes dados e consolidá-lo em um único painel de vidro com diferentes perspetivas para apoiar a multiplicidade de stakeholders na sua organização.

[O Azure Monitor](../overview.md) recolhe e agrega dados de uma variedade de fontes para uma plataforma de dados comum onde pode ser usado para análise, visualização e alerta. Proporciona uma experiência consistente em além de dados de várias fontes, o que lhe dá informações profundas sobre todos os seus recursos monitorizados e até mesmo com dados de outros serviços que armazenam os seus dados no Monitor Azure.


![Descrição geral do Azure Monitor](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Dados de observabilidade no Monitor Azure
Métricas, troncos e vestígios distribuídos são comumente referidos como os três pilares da observabilidade. Estes são os diferentes tipos de dados que uma ferramenta de monitorização deve recolher e analisar para proporcionar uma observabilidade suficiente de um sistema monitorizado. A observabilidade pode ser alcançada correlando dados de vários pilares e agregando dados em todo o conjunto de recursos que estão a ser monitorizados. Uma vez que o Azure Monitor armazena dados de várias fontes em conjunto, os dados podem ser correlacionados e analisados utilizando um conjunto comum de ferramentas. Também correlaciona dados em várias subscrições e inquilinos do Azure, além de hospedar dados para outros serviços.

Os recursos do Azure geram uma quantidade significativa de dados de monitorização. O Azure Monitor consolida estes dados juntamente com a monitorização de dados de outras fontes numa plataforma de Métricas ou Registos. Cada um é otimizado para cenários de monitorização particulares, e cada um suporta diferentes funcionalidades no Monitor Azure. Funcionalidades como análise de dados, visualizações ou alertas exigem que compreenda as diferenças para que possa implementar o seu cenário exigido da forma mais eficiente e rentável. Insights no Monitor Azure, tais como [Application Insights](../app/app-insights-overview.md) ou [Azure Monitor para VMs,](../insights/vminsights-overview.md) têm ferramentas de análise que permitem focar-se no cenário de monitorização específico sem ter de compreender as diferenças entre os dois tipos de dados. 


### <a name="metrics"></a>Métricas
[As métricas](data-platform-metrics.md) são valores numéricos que descrevem algum aspeto de um sistema num determinado momento do tempo. São recolhidos regularmente e identificados com uma marca de tempo, um nome, um valor e um ou mais rótulos definidores. As métricas podem ser agregadas usando uma variedade de algoritmos, em comparação com outras métricas, e analisadas para tendências ao longo do tempo. 

As métricas do Monitor Azure são armazenadas numa base de dados da série temporal que é otimizada para analisar dados carimbados no tempo. Isto torna as métricas particularmente adequadas para alertar e detetar rapidamente os problemas. Eles podem dizer-lhe como o seu sistema está a funcionar, mas normalmente precisa ser combinado com registos para identificar a causa principal dos problemas.

As métricas estão disponíveis para análise interativa no portal Azure com [o Azure Metrics Explorer.](../platform/metrics-getting-started.md) Podem ser adicionados a um [painel azure](../learn/tutorial-app-dashboards.md) para visualização em combinação com outros dados e utilizados para [alerta](alerts-metric.md)em tempo quase real .

Leia mais sobre as Métricas do Monitor Azure, incluindo as suas fontes de dados em [Métricas no Monitor Azure](data-platform-metrics.md).

### <a name="logs"></a>Registos
[Os registos](data-platform-logs.md) são eventos que ocorreram dentro do sistema. Podem conter diferentes tipos de dados e podem ser estruturados ou texto de formulário livre com uma marca de tempo. Podem ser criados esporadicamente à medida que os eventos no ambiente geram entradas de registo, e um sistema sob carga pesada normalmente gera mais volume de registo.

Os registos no Monitor Azure são armazenados num espaço de trabalho do Log Analytics baseado no [Azure Data Explorer,](/azure/data-explorer/) que fornece um poderoso motor de análise e [linguagem de consulta rica.](/azure/kusto/query/) Os registos normalmente fornecem informações suficientes para fornecer o contexto completo do problema que está a ser identificado e são valiosos para identificar caso raiz de problemas.

> [!NOTE]
> É importante distinguir entre registos do Monitor Azure e fontes de dados de registo em Azure. Por exemplo, os eventos de nível de subscrição em Azure são escritos para um registo de [atividade](platform-logs-overview.md) que você pode ver a partir do menu Do Monitor Azure. A maioria dos recursos irá escrever informações operacionais para um registo de [recursos](platform-logs-overview.md) que você pode encaminhar para diferentes locais. O Azure Monitor Logs é uma plataforma de dados de registo que recolhe registos de atividade sonantes e registos de recursos, juntamente com outros dados de monitorização, para fornecer uma análise profunda em todo o conjunto de recursos.


 Pode trabalhar com consultas de [log](../log-query/log-query-overview.md) interactivamente com [log Analytics](../log-query/portals.md) no portal Azure ou adicionar os resultados a um dashboard [Azure](../learn/tutorial-app-dashboards.md) para visualização em combinação com outros dados. Também pode criar [alertas](alerts-log.md) de registo que desencadearão um alerta com base nos resultados de uma consulta de horário.

Leia mais sobre os Registos do Monitor Azure, incluindo as suas fontes de dados em [Registos no Monitor Azure](data-platform-logs.md).

### <a name="distributed-traces"></a>Vestígios distribuídos
Os vestígios são uma série de eventos relacionados que seguem um pedido do utilizador através de um sistema distribuído. Podem ser usados para determinar o comportamento do código de aplicação e o desempenho de diferentes transações. Embora os registos sejam muitas vezes criados por componentes individuais de um sistema distribuído, um vestígio mede o funcionamento e o desempenho da sua aplicação em todo o conjunto de componentes.

O rastreio distribuído no Monitor Azure está ativado com o SDK de Insights de [Aplicação,](../app/distributed-tracing.md)e os dados de rastreio são armazenados com outros dados de registo de aplicações recolhidos pela Application Insights. Isto disponibiliza-o para as mesmas ferramentas de análise que outros dados de registo, incluindo consultas de registo, dashboards e alertas.

Ler mais sobre rastreiodistribuído no [What is Distributed Tracing?](../app/distributed-tracing.md)


## <a name="compare-azure-monitor-metrics-and-logs"></a>Compare métricas e registos do Monitor Azure

A tabela seguinte compara métricas e registos no Monitor Azure.

| Atributo  | Métricas | Registos |
|:---|:---|:---|
| Benefícios | Leve e capaz de cenários quase em tempo real, como alertar. Ideal para deteção rápida de problemas. | Analisado com linguagem de consulta rica. Ideal para análise profunda e identificação de causa raiz. |
| Dados | Apenas valores numéricos | Texto ou dados numéricos |
| Estrutura | Conjunto padrão de propriedades, incluindo tempo de amostra, recurso a ser monitorizado, um valor numérico. Algumas métricas incluem múltiplas dimensões para mais definição. | Conjunto único de propriedades dependendo do tipo de log. |
| Coleção | Recolhidos em intervalos regulares. | Pode ser recolhido esporadicamente à medida que os eventos desencadeiam um registo a ser criado. |
| Vista no portal Azure | Explorador de Métricas | Log Analytics |
| Fontes de dados incluem | Métricas da plataforma recolhidas a partir de recursos Azure.<br>Aplicações monitorizadas por Insights de Aplicação.<br>Personalizado definido por aplicação ou API. | Registos de aplicação e recursos.<br>Soluções de monitorização.<br>Agentes e extensões VM.<br>Pedidos de candidatura e exceções.<br>Centro de Segurança Azure.<br>Data Collector API. |

## <a name="collect-monitoring-data"></a>Recolher dados de monitorização
Diferentes [fontes de dados para o Monitor Azure](data-sources.md) escreverão para um espaço de trabalho de Log Analytics (Registos) ou para a base de dados de métricas do Monitor Azure (Métricas) ou ambas. Algumas fontes escreverão diretamente para estas lojas de dados, enquanto outras podem escrever para outro local, como o armazenamento do Azure e exigir alguma configuração para povoar registos ou métricas. 

Consulte [métricas no Monitor Azure](data-platform-metrics.md) e [em Registos no Monitor Azure](data-platform-logs.md) para obter uma listagem de diferentes fontes de dados que povoam cada tipo.


## <a name="stream-data-to-external-systems"></a>Dados de Stream a sistemas externos
Além de usar as ferramentas no Azure para analisar dados de monitorização, pode ter um requisito reencaminhá-lo para uma ferramenta externa, como um produto de management (SIEM) de eventos e informações de segurança. Este encaminhamento é normalmente feito diretamente a partir de recursos monitorizados através de Hubs de [Eventos Azure](/azure/event-hubs/). Algumas fontes podem ser configuradas para enviar dados diretamente para um centro de eventos, enquanto você pode usar outro processo, como uma App Lógica para recuperar os dados necessários. Consulte os dados de [monitorização do Stream Azure num centro](stream-monitoring-data-event-hubs.md) de eventos para consumo por uma ferramenta externa para obter mais detalhes.



## <a name="next-steps"></a>Passos seguintes

- Leia mais sobre [Métricas no Monitor Azure.](data-platform-metrics.md)
- Leia mais sobre [logs no Monitor Azure](data-platform-logs.md).
- Conheça os [dados de monitorização disponíveis](data-sources.md) para diferentes recursos no Azure.
