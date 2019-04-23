---
title: Plataforma de dados do Azure Monitor | Documentos da Microsoft
description: A monitorização dos dados recolhidos pelo Azure Monitor é separada nas métricas que são leves e com capacidade de oferecer suporte a cenários em tempo real em tempo quase e registos que são utilizados para análise avançada.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 8883c1e7f2874e1e2e61b8eca122f2ec294c7849
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790931"
---
# <a name="azure-monitor-data-platform"></a>Plataforma de dados do Azure Monitor

Ativar observability nos ambientes de computação complexos atuais em execução de aplicações distribuídas que dependem de nuvem e serviços no local, requer o conjunto de dados operacionais a partir de cada camada e cada componente do sistema distribuído. Tem de ser capaz de executar informações aprofundadas sobre estes dados e consolidá-la num único painel de vidro com diferentes perspectivas para suportar as inúmeras partes interessadas na sua organização.

[O Azure Monitor](../overview.md) recolhe e agrega dados de uma variedade de origens numa plataforma de dados comuns em que podem ser utilizados para análise, visualização e alerta. Ele fornece uma experiência consistente com a base de dados de várias origens, o que dá-lhe informações aprofundadas através de todos os seus recursos monitorizados e até mesmo com dados de outros serviços que armazenam os dados no Azure Monitor.


![Descrição geral do Azure Monitor](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Dados de observability no Azure Monitor
As métricas, registos e rastreios distribuídos são normalmente chamados de como os três pilares da observability. Estes são os diferentes tipos de dados que uma ferramenta de monitorização tem de recolher e analisar para fornecer observability suficiente de um sistema monitorizado. Observability pode ser alcançado ao correlacionar dados de vários pilares e agregação de dados em todo o conjunto de recursos a ser monitorizado. Porque o Monitor do Azure armazena dados de várias origens em conjunto, os dados podem ser correlacionados e analisados através de um conjunto comum de ferramentas. Ele também correlaciona dados em várias subscrições do Azure e inquilinos, além de hospedar os dados para outros serviços.

Recursos do Azure geram uma quantidade significativa de dados de monitorização. O Azure Monitor consolida estes dados, juntamente com dados de outras origens de monitorização numa plataforma de métricas ou registos. Cada um está otimizado para cenários de monitorização específicos, e cada um oferece suporte a diferentes recursos no Azure Monitor. Recursos como análise de dados, visualizações ou alertas exigir-o a compreender as diferenças, para que pode implementar o seu cenário necessário de forma mais eficiente e económica. Insights no Azure Monitor, como [Application Insights](../app/app-insights-overview.md) ou [Azure Monitor para VMs](../insights/vminsights-overview.md) ter as ferramentas de análise que permitem-lhe concentrar-se no cenário de monitorização específico, sem ter de compreender o diferenças entre os dois tipos de dados. 


### <a name="metrics"></a>Métricas
[Métricas](data-platform-metrics.md) são valores numéricos que descrevem algum aspeto de um sistema num momento específico no tempo. Eles são recolhidos em intervalos regulares e são identificados com um carimbo, um nome, um valor e um ou mais etiquetas de definição. As métricas podem ser agregadas usando uma variedade de algoritmos, em comparação com outras métricas e analisados para as tendências ao longo do tempo. 

Métricas no Azure Monitor são armazenadas num banco de dados de séries de tempo que está otimizado para analisar os dados de horários. Isso torna as métricas mais rápido e particularmente adequado para alertas detecção de problemas. Poderão indicar-lhe o desempenho do seu sistema, mas normalmente, tem de ser combinados com os registos para identificar a causa raiz dos problemas.

As métricas estão disponíveis para análise interativa no portal do Azure com [Explorador de métricas](../app/metrics-explorer.md). Eles podem ser adicionados a uma [dashboard do Azure](../learn/tutorial-app-dashboards.md) para visualização em combinação com outros dados e utilizado para quase em tempo real [alertas](alerts-metric.md).

Saiba mais sobre as métricas de Monitor do Azure incluindo suas origens de dados no [métricas no Azure Monitor](data-platform-metrics.md).

### <a name="logs"></a>Registos
[Registos](data-platform-logs.md) são eventos que ocorreram no sistema. Eles podem conter diferentes tipos de dados e podem ser estruturados ou texto de formulário com um carimbo livre. Eles podem ser criados esporadicamente como eventos no ambiente de geram entradas de registo e um sistema com muita carga, normalmente, irá gerar mais volume de registo.

Os registos no Azure Monitor são armazenados numa área de trabalho do Log Analytics que se baseia [Explorador de dados do Azure](/azure/data-explorer/) que fornece um mecanismo de análise avançada e [linguagem de consulta avançada](/azure/kusto/query/). Registos normalmente fornecem informações suficientes para fornecer contexto completado do problema que está a ser identificado e são valiosos para identificar o caso de raiz dos problemas.

> [!NOTE]
> É importante distinguir entre os registos do Azure Monitor e origens de dados de registo no Azure. Por exemplo, os eventos de nível de subscrição no Azure são escritos para um [registo de atividades](activity-logs-overview.md) que pode ver no menu do Azure Monitor. A maioria dos recursos irão escrever as informações operacionais para um [registo de diagnóstico](diagnostic-logs-overview.md) que podem encaminhar para localizações diferentes. Registos de Monitor do Azure é uma plataforma de dados de registo que recolhe registos de atividades e os registos de diagnóstico juntamente com outros dados de monitorização para fornecer uma análise detalhada em sua todo o conjunto de recursos.


 Pode trabalhar com [consultas de registo](../log-query/log-query-overview.md) interativamente com [do Log Analytics](../log-query/portals.md) no portal do Azure ou adicionar os resultados para um [dashboard do Azure](../learn/tutorial-app-dashboards.md) para visualização em combinação com outros dados. Também pode criar [alertas de registo](alerts-log.md) que irá acionar um alerta com base nos resultados de uma consulta de agenda.

Saiba mais sobre os registos de Monitor do Azure incluindo suas origens de dados no [inicia sessão no Azure Monitor](data-platform-logs.md).

### <a name="distributed-traces"></a>Rastreios distribuídos
Rastreios são a série de eventos relacionados que se seguem um pedido de utilizador através de um sistema distribuído. Eles podem ser usados para determinar o comportamento do código de aplicativo e o desempenho de transações diferentes. Embora os registos, muitas vezes, serão criados por componentes individuais de um sistema distribuído, um rastreio mede a operação e o desempenho da sua aplicação em todo o conjunto de componentes.

O rastreio distribuído no Azure Monitor está ativado com o [SDK do Application Insights](../app/distributed-tracing.md), e os dados de rastreio são armazenados com outros dados de registo de aplicação recolhidos pelo Application Insights. Isto torna disponível para as mesmas ferramentas de análise que outros dados de registo, incluindo consultas de registo, dashboards e alertas.

Leia mais sobre distribuídas de rastreamento em [o que é o rastreio distribuído?](../app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Comparar o Azure Monitor métricas e registos

A tabela seguinte compara as métricas e registos no Azure Monitor.

| Atributo  | Métricas | Registos |
|:---|:---|:---|
| Benefícios | Simples e capaz de cenários de quase em tempo real, como alertas. Ideal para deteção rápida dos problemas. | Analisado com avançada linguagem de consulta. Ideal para uma análise detalhada e a identificar a causa raiz. |
| Dados | Apenas a valores numéricos | Dados de texto ou numérico |
| estrutura | Conjunto padrão de propriedades, incluindo o tempo de exemplo, o recurso a ser monitorizado, um valor numérico. Algumas métricas incluem várias dimensões para a definição ainda mais. | Conjunto exclusivo de propriedades, dependendo do tipo de registo. |
| Coleção | Coletados em intervalos regulares. | Pode ser recolhidos esporadicamente como eventos de acionam um registo a ser criada. |
| Ver no portal do Azure | Explorador de Métricas | Log Analytics |
| Origens de dados incluem | Métricas de plataforma recolhidos a partir de recursos do Azure.<br>Aplicações monitorizadas pelo Application Insights.<br>Personalizado definido pela aplicação ou API. | Aplicação e os registos de diagnóstico.<br>Soluções de monitorização.<br>Os agentes e as extensões de VM.<br>Pedidos de aplicação e exceções.<br>Centro de segurança do Azure.<br>API do Recoletor de dados. |

## <a name="collect-monitoring-data"></a>Recolher dados de monitorização
Diferentes [origens de dados para o Azure Monitor](data-sources.md) a gravação será feita uma área de trabalho do Log Analytics (Logs) ou a base de dados de métricas do Azure Monitor (métricas) ou ambos. Algumas origens de escrever diretamente para esses arquivos de dados, enquanto outros podem escrever para outra localização, como o armazenamento do Azure e precisar de alguma configuração para preencher os registos ou métricas. 

Ver [métricas no Azure Monitor](data-platform-metrics.md) e [inicia sessão no Azure Monitor](data-platform-logs.md) para obter uma lista de origens de dados que preencher cada tipo.


## <a name="stream-data-to-external-systems"></a>Dados de Stream a sistemas externos
Além de usar as ferramentas no Azure para analisar dados de monitorização, pode ter um requisito reencaminhá-lo para uma ferramenta externa, como um produto de management (SIEM) de eventos e informações de segurança. Este encaminhamento é geralmente feito diretamente a partir de recursos monitorizados através de [os Hubs de eventos do Azure](/azure/event-hubs/). Algumas origens podem ser configuradas para enviar dados diretamente para um hub de eventos, embora seja possível usar um processo diferente como uma aplicação lógica para recuperar os dados necessários. Ver [Azure Stream a monitorização dos dados para um hub de eventos para consumo por uma ferramenta externa](stream-monitoring-data-event-hubs.md) para obter detalhes.



## <a name="next-steps"></a>Passos Seguintes

- Leia mais sobre [métricas no Azure Monitor](data-platform-metrics.md).
- Leia mais sobre [inicia sessão no Azure Monitor](data-platform-logs.md).
- Saiba mais sobre o [monitorização dos dados disponíveis](data-sources.md) diferentes recursos no Azure.
