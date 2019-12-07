---
title: Plataforma de dados Azure Monitor | Microsoft Docs
description: Os dados de monitoramento coletados pelo Azure Monitor são separados em métricas leves e capazes de dar suporte a cenários e logs quase em tempo real que são usados para análise avançada.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 4e9779f612bc4a2521459bf76a6e2b399fc89e07
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894135"
---
# <a name="azure-monitor-data-platform"></a>Plataforma de dados Azure Monitor

Habilitar a capacidade de observação nos ambientes de computação complexos de hoje que executam aplicativos distribuídos que dependem de serviços de nuvem e locais, requer a coleta de dados operacionais de cada camada e de todos os componentes do sistema distribuído. Você precisa ser capaz de executar informações aprofundadas sobre esses dados e consolidá-los em um único painel com perspectivas diferentes para dar suporte à infinidade de participantes em sua organização.

[Azure monitor](../overview.md) coleta e agrega dados de uma variedade de fontes em uma plataforma de dados comum, na qual ela pode ser usada para análise, visualização e alertas. Ele fornece uma experiência consistente com base em dados de várias fontes, o que fornece informações detalhadas em todos os seus recursos monitorados e até mesmo com dados de outros serviços que armazenam seus dados em Azure Monitor.


![Descrição geral do Azure Monitor](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Dados de observação em Azure Monitor
Métricas, logs e rastreamentos distribuídos são conhecidos como os três pilares da observação. Esses são os diferentes tipos de dados que uma ferramenta de monitoramento deve coletar e analisar para fornecer uma observação suficiente de um sistema monitorado. A observação pode ser obtida correlacionando os dados de vários pilares e agregando dados em todo o conjunto de recursos que estão sendo monitorados. Como Azure Monitor armazena dados de várias fontes juntas, os dados podem ser correlacionados e analisados usando um conjunto comum de ferramentas. Ele também correlaciona dados em várias assinaturas e locatários do Azure, além de hospedar dados para outros serviços.

Os recursos do Azure geram uma quantidade significativa de dados de monitoramento. O Azure Monitor consolida esses dados junto com os dados de monitoramento de outras fontes em uma plataforma de métricas ou logs. Cada um é otimizado para cenários de monitoramento específicos e cada um dá suporte a diferentes recursos no Azure Monitor. Recursos como análise de dados, visualizações ou alertas exigem que você compreenda as diferenças para que você possa implementar seu cenário necessário da maneira mais eficiente e econômica. As informações em Azure Monitor como [Application insights](../app/app-insights-overview.md) ou [Azure monitor para VMs](../insights/vminsights-overview.md) têm ferramentas de análise que permitem que você se concentre no cenário de monitoramento específico sem ter que entender as diferenças entre os dois tipos de dados. 


### <a name="metrics"></a>Métricas
[Métricas](data-platform-metrics.md) são valores numéricos que descrevem um aspecto de um sistema em um determinado momento. Eles são coletados em intervalos regulares e são identificados com um carimbo de data/hora, um nome, um valor e um ou mais rótulos de definição. As métricas podem ser agregadas usando uma variedade de algoritmos, em comparação com outras métricas, e analisadas quanto a tendências ao longo do tempo. 

As métricas em Azure Monitor são armazenadas em um banco de dados de série temporal que é otimizado para análise de dado com carimbo de data/hora. Isso torna as métricas especialmente adequadas para alertas e detecção rápida de problemas. Eles podem informar como o sistema está sendo executado, mas normalmente precisam ser combinados com logs para identificar a causa raiz dos problemas.

As métricas estão disponíveis para análise interativa no portal do Azure com [Metrics Explorer](../app/metrics-explorer.md). Eles podem ser adicionados a um [painel do Azure](../learn/tutorial-app-dashboards.md) para visualização em combinação com outros dados e usados para [alertas](alerts-metric.md)quase em tempo real.

Leia mais sobre Azure Monitor métricas, incluindo suas fontes de dados em [métricas no Azure monitor](data-platform-metrics.md).

### <a name="logs"></a>Registos
[Logs](data-platform-logs.md) são eventos que ocorreram no sistema. Eles podem conter diferentes tipos de dados e podem ser um texto de forma estruturado ou livre com um carimbo de data/hora. Eles podem ser criados esporadicamente, pois os eventos no ambiente geram entradas de log e um sistema com carga pesada geralmente gerará mais volume de log.

Os logs em Azure Monitor são armazenados em um espaço de trabalho Log Analytics baseado no [Azure data Explorer](/azure/data-explorer/) que fornece um mecanismo de análise poderoso e uma [linguagem de consulta avançada](/azure/kusto/query/). Os logs normalmente fornecem informações suficientes para fornecer o contexto completo do problema identificado e são valiosos para identificar o caso de problemas raiz.

> [!NOTE]
> É importante distinguir entre Azure Monitor logs e fontes de dados de log no Azure. Por exemplo, os eventos de nível de assinatura no Azure são gravados em um [log de atividades](activity-logs-overview.md) que você pode exibir no menu Azure monitor. A maioria dos recursos irá gravar informações operacionais em um [log de recursos](resource-logs-overview.md) que você pode encaminhar para locais diferentes. Os logs de Azure Monitor são uma plataforma de dados de log que coleta logs de atividade e logs de recursos junto com outros dados de monitoramento para fornecer análise profunda em todo o seu conjunto de recursos.


 Você pode trabalhar com [consultas de log](../log-query/log-query-overview.md) interativamente com [log Analytics](../log-query/portals.md) no portal do Azure ou adicionar os resultados a um [painel do Azure](../learn/tutorial-app-dashboards.md) para visualização em combinação com outros dados. Você também pode criar [alertas de log](alerts-log.md) , que dispararão um alerta com base nos resultados de uma consulta de agendamento.

Leia mais sobre logs de Azure Monitor, incluindo suas fontes de dados em [logs em Azure monitor](data-platform-logs.md).

### <a name="distributed-traces"></a>Rastreamentos distribuídos
Os rastreamentos são uma série de eventos relacionados que seguem uma solicitação de usuário por meio de um sistema distribuído. Eles podem ser usados para determinar o comportamento do código do aplicativo e o desempenho de transações diferentes. Enquanto os logs são frequentemente criados por componentes individuais de um sistema distribuído, um rastreamento mede a operação e o desempenho do seu aplicativo em todo o conjunto de componentes.

O rastreamento distribuído no Azure Monitor é habilitado com o [SDK do Application insights](../app/distributed-tracing.md)e os dados de rastreamento são armazenados com outros dados de log do aplicativo coletados pelo Application insights. Isso o disponibiliza para as mesmas ferramentas de análise que outros dados de log, incluindo consultas de log, painéis e alertas.

Leia mais sobre o rastreamento distribuído em [o que é o rastreamento distribuído?](../app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Comparar Azure Monitor métricas e logs

A tabela a seguir compara as métricas e os logs em Azure Monitor.

| Atributo  | Métricas | Registos |
|:---|:---|:---|
| Vantagens | Leve e capaz de cenários quase em tempo real, como alertas. Ideal para detecção rápida de problemas. | Analisado com a linguagem de consulta avançada. Ideal para análise profunda e identificação da causa raiz. |
| Dados | Somente valores numéricos | Texto ou dados numéricos |
| Estrutura | Conjunto padrão de propriedades, incluindo o tempo de exemplo, o recurso que está sendo monitorado, um valor numérico. Algumas métricas incluem várias dimensões para definição adicional. | Conjunto exclusivo de propriedades dependendo do tipo de log. |
| Coleção | Coletados em intervalos regulares. | Pode ser coletado esporadicamente, pois os eventos disparam um registro a ser criado. |
| Exibir no portal do Azure | Explorador de Métricas | Log Analytics |
| As fontes de dados incluem | Métricas de plataforma coletadas dos recursos do Azure.<br>Aplicativos monitorados pelo Application Insights.<br>Personalizado definido por aplicativo ou API. | Logs de aplicativos e recursos.<br>Soluções de monitoramento.<br>Agentes e extensões de VM.<br>Solicitações e exceções de aplicativos.<br>Central de segurança do Azure.<br>API do coletor de dados. |

## <a name="collect-monitoring-data"></a>Coletar dados de monitoramento
[Fontes diferentes de dados para Azure monitor](data-sources.md) serão gravadas em um espaço de trabalho log Analytics (logs) ou no banco de dados de métricas Azure monitor (métricas) ou ambos. Algumas fontes serão gravadas diretamente nesses armazenamentos de dados, enquanto outras podem gravar em outro local, como o armazenamento do Azure, e exigir alguma configuração para popular os logs ou as métricas. 

Consulte [métricas em Azure monitor](data-platform-metrics.md) e [logs em Azure monitor](data-platform-logs.md) para obter uma lista de diferentes fontes de dados que preenchem cada tipo.


## <a name="stream-data-to-external-systems"></a>Dados de Stream a sistemas externos
Além de usar as ferramentas no Azure para analisar dados de monitorização, pode ter um requisito reencaminhá-lo para uma ferramenta externa, como um produto de management (SIEM) de eventos e informações de segurança. Este encaminhamento é geralmente feito diretamente a partir de recursos monitorizados através de [os Hubs de eventos do Azure](/azure/event-hubs/). Algumas fontes podem ser configuradas para enviar dados diretamente a um hub de eventos, enquanto você pode usar outro processo, como um aplicativo lógico para recuperar os dados necessários. Consulte [transmitir dados de monitoramento do Azure para um hub de eventos para consumo por uma ferramenta externa](stream-monitoring-data-event-hubs.md) para obter detalhes.



## <a name="next-steps"></a>Passos seguintes

- Leia mais sobre [métricas em Azure monitor](data-platform-metrics.md).
- Leia mais sobre [logs em Azure monitor](data-platform-logs.md).
- Saiba mais sobre o [monitorização dos dados disponíveis](data-sources.md) diferentes recursos no Azure.
