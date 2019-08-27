---
title: Estrutura de logs de Azure Monitor | Microsoft Docs
description: Você precisa de uma consulta de log para recuperar dados de log de Azure Monitor.  Este artigo descreve como novas consultas de log são usadas em Azure Monitor e fornece conceitos que você precisa entender antes de criar uma.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: bwren
ms.openlocfilehash: b1d22729724c2d1a8e3705e017762dcef588245e
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034877"
---
# <a name="structure-of-azure-monitor-logs"></a>Estrutura de logs de Azure Monitor
A capacidade de obter informações rapidamente sobre seus dados usando uma consulta de [log](log-query-overview.md) é um recurso poderoso de Azure monitor. Para criar consultas eficientes e úteis, você deve entender alguns conceitos básicos, como onde estão localizados os dados desejados e como eles são estruturados. Este artigo fornece os conceitos básicos de que você precisa para começar.

## <a name="overview"></a>Descrição geral
Os dados em logs de Azure Monitor são armazenados em um espaço de trabalho Log Analytics ou em um aplicativo Application Insights. Ambos são fornecidos pelo [Azure data Explorer](/azure/data-explorer/) significando que eles aproveitam seu poderoso mecanismo de dados e linguagem de consulta.

Os dados em espaços de trabalho e aplicativos são organizados em tabelas, cada um dos quais armazena diferentes tipos de dados e tem seu próprio conjunto exclusivo de propriedades. A maioria das [fontes de dados](../platform/data-sources.md) será gravada em suas próprias tabelas em um espaço de trabalho log Analytics, enquanto Application insights será gravado em um conjunto predefinido de tabelas em um aplicativo Application insights. As consultas de log são muito flexíveis, permitindo que você combine facilmente os dados de várias tabelas e até mesmo use uma consulta entre recursos para combinar dados de tabelas em vários espaços de trabalho ou para escrever consultas que combinam dados de espaço de trabalho e de aplicativo.

A imagem a seguir mostra exemplos de fontes de dados que gravam em tabelas diferentes que são usadas em consultas de exemplo.

![Tabelas](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Área de trabalho do Log Analytics
Todos os dados coletados pelos logs de Azure Monitor, exceto pelo Application Insights, são armazenados em um [espaço de trabalho log Analytics](../platform/manage-access.md). Você pode criar um ou mais espaços de trabalho dependendo de seus requisitos específicos. [Fontes de dados](../platform/data-sources.md) como logs de atividades e logs de diagnóstico de recursos do Azure, agentes em máquinas virtuais e dados de insights e soluções de monitoramento gravarão dados em um ou mais espaços de trabalho que você configurar como parte de sua integração. Outros serviços, como a [central de segurança do Azure](/azure/security-center/) e o [Azure Sentinel](/azure/sentinel/) , também usam um espaço de trabalho log Analytics para armazenar seus dados para que possam ser analisados usando consultas de log junto com dados de monitoramento de outras fontes.

Tipos diferentes de dados são armazenados em tabelas diferentes no espaço de trabalho e cada tabela tem um conjunto exclusivo de propriedades. Um conjunto padrão de tabelas é adicionado a um espaço de trabalho quando ele é criado, e novas tabelas são adicionadas para diferentes fontes de dados, soluções e serviços à medida que são integradas. Você também pode criar tabelas personalizadas usando a [API do coletor de dados](../platform/data-collector-api.md).

Você pode procurar as tabelas em um espaço de trabalho e seu esquema na guia **esquema** em log Analytics para o espaço de trabalho.

![Esquema de espaço de trabalho](media/scope/workspace-schema.png)

Use a consulta a seguir para listar as tabelas no espaço de trabalho e o número de registros coletados em cada um no dia anterior. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Consulte a documentação para cada fonte de dados para obter detalhes das tabelas que eles criam. Os exemplos incluem artigos para [fontes de dados do Agent](../platform/agent-data-sources.md), logs de [diagnóstico](../platform/diagnostic-logs-schema.md)e soluções de [monitoramento](../insights/solutions-inventory.md).

### <a name="workspace-permissions"></a>Permissões do espaço de trabalho
Consulte [criando uma implantação de logs de Azure monitor](../platform/design-logs-deployment.md) para entender a estratégia de controle de acesso e as recomendações para fornecer acesso aos dados em um espaço de trabalho. Além de conceder acesso ao próprio espaço de trabalho, você pode limitar o acesso a tabelas individuais usando o [RBAC de nível de tabela](../platform/manage-access.md#table-level-rbac).

## <a name="application-insights-application"></a>Application Insights aplicativo
Quando você cria um aplicativo no Application Insights, um aplicativo correspondente é criado automaticamente em logs de Azure Monitor. Nenhuma configuração é necessária para coletar dados e o aplicativo irá gravar dados de monitoramento automaticamente, como exibições de página, solicitações e exceções.

Ao contrário de um espaço de trabalho Log Analytics, um aplicativo Application Insights tem um conjunto fixo de tabelas. Você não pode configurar outras fontes de dados para gravar no aplicativo, portanto, nenhuma tabela adicional pode ser criada. 

| Tabela | Descrição | 
|:---|:---|
| availabilityResults | Dados de resumo dos testes de disponibilidade. |
| browserTimings      | Dados sobre o desempenho do cliente, como o tempo necessário para processar os dados de entrada. |
| customEvents        | Eventos personalizados criados pelo seu aplicativo. |
| customMetrics       | Métricas personalizadas criadas pelo seu aplicativo. |
| dependências        | Chama do aplicativo para componentes externos. |
| exceções          | Exceções geradas pelo tempo de execução do aplicativo. |
| pageViews           | Dados sobre cada exibição do site com informações do navegador. |
| performanceCounters | Medições de desempenho dos recursos de computação que dão suporte ao aplicativo. |
| pedidos            | Detalhes de cada solicitação de aplicativo.  |
| rastreamentos              | Resultados do rastreamento distribuído. |

Você pode exibir o esquema para cada tabela na guia **esquema** em log Analytics para o aplicativo.

![Esquema do aplicativo](media/scope/application-schema.png)

## <a name="standard-properties"></a>Propriedades padrão
Embora cada tabela em logs de Azure Monitor tenha seu próprio esquema, há propriedades padrão compartilhadas por todas as tabelas. Consulte [propriedades padrão em logs de Azure monitor](../platform/log-standard-properties.md) para obter detalhes de cada um.

| Área de trabalho do Log Analytics | Application Insights aplicativo | Descrição |
|:---|:---|:---|
| TimeGenerated | timestamp  | Data e hora em que o registro foi criado. |
| Type          | itemType   | Nome da tabela da qual o registro foi recuperado. |
| _ResourceId   |            | Identificador exclusivo do recurso ao qual o registro está associado. |
| _IsBillable   |            | Especifica se os dados ingeridos são faturáveis. |
| _BilledSize   |            | Especifica o tamanho em bytes de dados que serão cobrados. |

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre como usar [log Analytics para criar e editar pesquisas de log](../log-query/portals.md).
- Confira um [tutorial sobre como escrever consultas](../log-query/get-started-queries.md) usando a nova linguagem de consulta.
