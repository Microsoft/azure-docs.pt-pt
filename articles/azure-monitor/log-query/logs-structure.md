---
title: Estrutura dos registos do Azure Monitor | Documentos da Microsoft
description: Precisa de uma consulta de registo para recuperar dados de registo do Azure Monitor.  Este artigo descreve como os novos registos, as consultas são utilizadas no Azure Monitor e fornece os conceitos que tem de compreender antes de criar um.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/16/2019
ms.author: bwren
ms.openlocfilehash: e243ebbc31f9e941678ac76a83738276995b5ba1
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297331"
---
# <a name="structure-of-azure-monitor-logs"></a>Estrutura dos registos do Azure Monitor
A capacidade de obter rapidamente informações sobre os seus dados com um [consulta de registo](log-query-overview.md) é um recurso poderoso do Azure Monitor. Para criar consultas eficientes e úteis, deve compreender alguns conceitos básicos, tais como onde os dados que pretende estão localizados e como eles são estruturados. Este artigo fornece os conceitos básicos que precisa para começar a utilizar.

## <a name="overview"></a>Descrição geral
Dados nos Logs de Monitor do Azure são armazenados numa área de trabalho do Log Analytics ou um aplicativo do Application Insights. Ambos têm a tecnologia [Explorador de dados do Azure](/azure/data-explorer/) que significa que tiram partido de sua linguagem de consulta e de motor de dados poderosas.

Dados em áreas de trabalho e aplicativos são organizados em tabelas, sendo que cada um armazena os diferentes tipos de dados e tem seu próprio conjunto exclusivo de propriedades. A maioria dos [origens de dados](../platform/data-sources.md) irá escrever suas próprias tabelas numa área de trabalho do Log Analytics, enquanto o Application Insights irá escrever num conjunto predefinido de tabelas num aplicativo do Application Insights. Consultas de registo são muito flexíveis, permitindo-lhe combinar facilmente dados de várias tabelas e até mesmo usar uma consulta entre recursos para combinar dados de tabelas em várias áreas de trabalho ou para escrever consultas que combinam dados de área de trabalho e a aplicação.

A imagem seguinte mostra exemplos de origens de dados que gravam em tabelas diferentes que são utilizadas em consultas de exemplo.

![Tabelas](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Área de trabalho do log Analytics
Todos os dados recolhidos por registos de Monitor do Azure, exceto para o Application Insights são armazenados num [área de trabalho do Log Analytics](../platform/manage-access.md). Pode criar um ou mais áreas de trabalho consoante os seus requisitos específicos. [Origens de dados](../platform/data-sources.md) , como registos de diagnóstico e registos de atividade dos recursos do Azure, agentes nas máquinas virtuais e dados de informações e soluções de monitorização irão escrever dados num ou mais áreas de trabalho que configura como parte de sua integração. Outros serviços, como [Centro de segurança do Azure](/azure/security-center/) e [Azure sentinela](/azure/sentinel/) também utilizar uma área de trabalho do Log Analytics para armazenar seus dados, para que podem ser analisados através de consultas de registo, juntamente com a monitorização dos dados dos outros origens.

Diferentes tipos de dados são armazenados em tabelas diferentes na área de trabalho, e cada tabela tem um conjunto exclusivo de propriedades. Um conjunto padrão de tabelas são adicionadas a uma área de trabalho quando é criado e novas tabelas são adicionadas para origens de dados diferentes, soluções e serviços, pois eles são carregadas. Também pode criar tabelas personalizadas com o [API do Recoletor de dados](../platform/data-collector-api.md).

Pode procurar as tabelas de uma área de trabalho e o respetiva esquema no **esquema** separador no Log Analytics para a área de trabalho.

![Esquema de área de trabalho](media/scope/workspace-schema.png)

Utilize a seguinte consulta lista as tabelas na área de trabalho e o número de registos recolhidos para cada ao longo do dia anterior. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Consulte a documentação para cada origem de dados para obter detalhes sobre as tabelas criadas por elas. Os exemplos incluem artigos para [origens de dados do agente](../platform/agent-data-sources.md), [registos de diagnóstico](../platform/diagnostic-logs-schema.md), e [soluções de monitorização](../insights/solutions-inventory.md).

### <a name="workspace-permissions"></a>Permissões de área de trabalho
Ver [permissões de área de trabalho e o escopo](../platform/manage-access.md#workspace-permissions-and-scope) para obter detalhes sobre como fornecer acesso aos dados numa área de trabalho. Para além de conceder acesso à área de trabalho em si, pode limitar o acesso às tabelas individuais usando [RBAC de nível de tabela](../platform/manage-access.md#table-level-rbac).

## <a name="application-insights-application"></a>Aplicação do Application Insights
Quando cria uma aplicação no Application Insights, uma aplicação correspondente é criada automaticamente nos Logs de Monitor do Azure. É necessária nenhuma configuração para recolher dados e a aplicação será automaticamente escrever dados, como vistas de página, pedidos e exceções de monitorização.

Ao contrário de uma área de trabalho do Log Analytics, um aplicativo do Application Insights tem um conjunto fixo de tabelas. Não é possível configurar outras origens de dados para escrever o aplicativo para que não tabelas adicionais podem ser criadas. 

| Tabela | Descrição | 
|:---|:---|
| availabilityResults | Dados de resumo dos testes de disponibilidade. |
| browserTimings      | Dados sobre o desempenho do cliente, como o tempo despendido para processar os dados de entrada. |
| customEvents        | Eventos personalizados criados pelo seu aplicativo. |
| customMetrics       | Métricas personalizadas criadas pelo seu aplicativo. |
| Dependências        | Chamadas a partir da aplicação para componentes externos. |
| Exceções          | Exceções geradas pelo tempo de execução do aplicativo. |
| pageViews           | Vista de dados sobre cada Web site com informações de browser. |
| performanceCounters | Medidas de desempenho dos recursos de computação que suporta o aplicativo. |
| pedidos            | Detalhes de cada pedido de aplicação.  |
| rastreios              | Resultados de rastreio distribuído. |

Pode ver o esquema para cada tabela na **esquema** separador no Log Analytics para a aplicação.

![Esquema de aplicações](media/scope/application-schema.png)

## <a name="standard-properties"></a>Propriedades padrão
Embora cada tabela nos registos do Azure Monitor tem seu próprio esquema, existem propriedades padrão partilhadas por todas as tabelas. Ver [propriedades padrão nos registos do Azure Monitor](../platform/log-standard-properties.md) para obter detalhes de cada.

| Área de trabalho do log Analytics | Aplicação do Application Insights | Descrição |
|:---|:---|:---|
| TimeGenerated | timestamp  | Data e hora de que criação do registo. |
| Tipo          | itemType   | Nome da tabela que o registo foi obtido do. |
| _ResourceId   |            | Identificador exclusivo para o recurso o registo está associado. |
| _IsBillable   |            | Especifica se os dados ingeridos são faturáveis. |
| _BilledSize   |            | Especifica o tamanho em bytes de dados que vão ser cobrados. |

## <a name="next-steps"></a>Passos Seguintes
- Aprenda a usar [pesquisas de registos do Log Analytics para criar e editar](../log-query/portals.md).
- Veja uma [tutorial sobre como escrever consultas](../log-query/get-started-queries.md) usando a nova linguagem de consulta.
