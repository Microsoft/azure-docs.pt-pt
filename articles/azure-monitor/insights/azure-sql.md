---
title: Análise de SQL do Azure solução no Azure Monitor | Microsoft Docs
description: Análise de SQL do Azure solução ajuda você a gerenciar seus bancos de dados SQL do Azure
services: log-analytics
ms.service: log-analytics
ms.custom: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: carlrab
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: f27ea984e2c11be03d69295d206c1b99791a3f72
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693285"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitorar o banco de dados SQL do Azure usando Análise de SQL do Azure (versão prévia)

![Símbolo de Análise de SQL do Azure](./media/azure-sql/azure-sql-symbol.png)

O Análise de SQL do Azure é uma solução de monitoramento de nuvem avançada para monitorar o desempenho de bancos de dados SQL do Azure, pools elásticos e instâncias gerenciadas em escala e entre várias assinaturas por meio de um único painel de vidro. Recolhe e visualiza métricas de desempenho importantes da Base de Dados SQL do Azure com inteligência integrada para resolução de problemas de desempenho.

Usando as métricas que você coleta com a solução, você pode criar alertas e regras de monitoramento personalizados. A solução ajuda a identificar problemas em cada camada de sua pilha de aplicativos. Ele usa as métricas de diagnóstico do Azure junto com Azure Monitor exibições para apresentar dados sobre todos os seus bancos de dado SQL do Azure, pools elásticos e bancos de dados em instâncias gerenciadas em um único espaço de trabalho de Log Analytics. Azure Monitor ajuda a coletar, correlacionar e Visualizar dados estruturados e não estruturados.

Para obter uma visão geral prática sobre como usar a solução Análise de SQL do Azure e para cenários de uso típicos, consulte o vídeo incorporado:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Origens ligadas

Análise de SQL do Azure é uma solução de monitoramento somente de nuvem que dá suporte a streaming de telemetria de diagnóstico para bancos de dados SQL do Azure: bancos de dados únicos, em pool e Instância Gerenciada. Como a solução não usa agentes para se conectar ao Azure Monitor, a solução não oferece suporte ao monitoramento de SQL Server hospedados localmente ou em VMs, consulte a tabela de compatibilidade abaixo.

| Origem Ligada | Suportadas | Descrição |
| --- | --- | --- |
| [Configurações de diagnóstico](../platform/diagnostic-settings.md) | **Sim** | Os dados de log e métrica do Azure são enviados para Azure Monitor logs diretamente pelo Azure. |
| [Conta de armazenamento do Azure](../platform/collect-azure-metrics-logs.md) | Não | Azure Monitor não lê os dados de uma conta de armazenamento. |
| [Agentes do Windows](../platform/agent-windows.md) | Não | Agentes diretos do Windows não são usados pela solução. |
| [Agentes do Linux](../learn/quick-collect-linux-computer.md) | Não | Agentes diretos do Linux não são usados pela solução. |
| [System Center Operations Manager grupo de gerenciamento](../platform/om-agents.md) | Não | Uma conexão direta do agente de Operations Manager para Azure Monitor não é usada pela solução. |

## <a name="configuration"></a>Configuração
Use o processo descrito em [Adicionar soluções de Azure monitor da Galeria de soluções](../../azure-monitor/insights/solutions.md) para adicionar a solução de análise de SQL do Azure (versão prévia) ao seu espaço de trabalho do log Analytics.

### <a name="configure-azure-sql-databases-elastic-pools-and-managed-instances-to-stream-diagnostics-telemetry"></a>Configurar bancos de dados SQL do Azure, pools elásticos e instâncias gerenciadas para a telemetria de diagnóstico de fluxo

Depois de criar Análise de SQL do Azure solução em seu espaço de trabalho, você precisa **configurar cada** recurso que deseja monitorar para transmitir sua telemetria de diagnóstico para a solução. Siga as instruções detalhadas nesta página:

- Habilite Diagnóstico do Azure para o banco de dados SQL do Azure para [transmitir a telemetria de diagnóstico para análise de SQL do Azure](../../sql-database/sql-database-metrics-diag-logging.md).

A página acima também fornece instruções sobre como habilitar o suporte para monitorar várias assinaturas do Azure de um único espaço de trabalho de Análise de SQL do Azure como um único painel de vidro.

## <a name="using-the-solution"></a>Utilizar a solução

Quando você adiciona a solução ao seu espaço de trabalho, o bloco Análise de SQL do Azure é adicionado ao seu espaço de trabalho e aparece em visão geral. Selecione Exibir link de resumo para carregar o conteúdo do bloco.

![Bloco de resumo Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-tile-01.png)

Depois de carregado, o bloco mostra o número de bancos de dados SQL do Azure, pools elásticos, instâncias gerenciadas e bancos de dados em instâncias gerenciadas das quais a solução está recebendo a telemetria de diagnóstico.

![Bloco Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-tile-02.png)

A solução fornece duas exibições separadas, uma para monitorar bancos de dados SQL do Azure e pools elásticos, e outra exibição para monitorar Instância Gerenciada e bancos de dados em instâncias gerenciadas.

Para exibir Análise de SQL do Azure painel de monitoramento para bancos de dados SQL do Azure e pools elásticos, clique na parte superior do bloco. Para exibir Análise de SQL do Azure painel de monitoramento para Instância Gerenciada e bancos de dados no Instância Gerenciada, clique na parte inferior do bloco.

### <a name="viewing-azure-sql-analytics-data"></a>Exibindo dados de Análise de SQL do Azure

O painel inclui a visão geral de todos os bancos de dados monitorados por diferentes perspectivas. Para que diferentes perspectivas funcionem, você deve habilitar as métricas ou os logs apropriados em seus recursos do SQL para serem transmitidos para Log Analytics espaço de trabalho.

Observe que, se algumas métricas ou logs não forem transmitidos para Azure Monitor, os blocos na solução não serão preenchidos com informações de monitoramento.

### <a name="azure-sql-database-and-elastic-pool-view"></a>Banco de dados SQL do Azure e exibição de pool elástico

Depois que o bloco Análise de SQL do Azure do banco de dados for selecionado, o painel Monitoramento será mostrado.

![Visão geral de Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-overview.png)

A seleção de qualquer um dos blocos abre um relatório de busca detalhada na perspectiva específica. Depois que a perspectiva for selecionada, o relatório de busca detalhada será aberto.

![Tempos limite de Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-metrics.png)

Cada perspectiva nessa exibição fornece resumos sobre assinatura, servidor, pool elástico e nível de banco de dados. Além disso, cada perspectiva mostra uma perspectiva específica para o relatório à direita. A seleção da assinatura, do servidor, do pool ou do banco de dados da lista continua a busca detalhada.

### <a name="managed-instance-and-databases-in-managed-instance-view"></a>Instância Gerenciada e bancos de dados na exibição Instância Gerenciada

Depois que o bloco de Análise de SQL do Azure para os bancos de dados for selecionado, o painel de monitoramento será mostrado.

![Visão geral de Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-overview-mi.png)

A seleção de qualquer um dos blocos abre um relatório de busca detalhada na perspectiva específica. Depois que a perspectiva for selecionada, o relatório de busca detalhada será aberto.

Selecionar Instância Gerenciada exibição, mostra detalhes sobre a utilização de Instância Gerenciada, os bancos de dados que ele contém e a telemetria nas consultas executadas na instância.

![Tempos limite de Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="perspectives"></a>Perspetivas

A tabela abaixo descreve as perspectivas com suporte para duas versões do painel, uma para o banco de dados SQL do Azure e pools elásticos e a outra para Instância Gerenciada.

| Perspectiva | Descrição | Suporte ao banco de dados SQL e a pools elásticos | Suporte a Instância Gerenciada |
| --- | ------- | ----- | ----- |
| Recurso por tipo | Perspectiva que conta todos os recursos monitorados. | Sim | Sim |
| Insights | Fornece detalhamento hierárquico em Intelligent Insights no desempenho. | Sim | Sim |
| Erros | Fornece uma análise hierárquica dos erros do SQL que ocorreram nos bancos de dados. | Sim | Sim |
| Tempos limite | Fornece uma análise hierárquica detalhada dos tempos limite do SQL que ocorreram nos bancos de dados. | Sim | Não |
| Bloqueios | Fornece detalhamento hierárquico em blocos SQL que ocorreram nos bancos de dados. | Sim | Não |
| Esperas de banco de dados | Fornece detalhamento hierárquico em estatísticas de espera do SQL no nível do banco de dados. Inclui resumos do tempo de espera total e o tempo de espera por tipo de espera. |Sim | Sim |
| Duração da consulta | Fornece detalhamento hierárquico nas estatísticas de execução da consulta, como duração da consulta, uso da CPU, uso de e/s de dados, uso de e/s de log. | Sim | Sim |
| Esperas de consulta | Fornece uma busca detalhada hierárquica nas estatísticas de espera da consulta por categoria de espera. | Sim | Sim |

### <a name="intelligent-insights-report"></a>Relatório de Intelligent Insights

O banco de dados SQL do Azure [Intelligent insights](../../sql-database/sql-database-intelligent-insights.md) permite que você saiba o que está acontecendo com o desempenho de todos os bancos de dados SQL do Azure. Todas as Intelligent Insights coletadas podem ser visualizadas e acessadas por meio da perspectiva de informações.

![Informações do Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Relatórios de pool elástico e banco de dados

Os pools elásticos e os bancos de dados SQL têm seus próprios relatórios específicos que mostram todos os dados coletados para o recurso no tempo especificado.

![Banco de dados Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-database.png)

![Pool elástico do SQL do Azure](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Relatórios de consulta

Por meio das perspectivas de duração da consulta e esperas de consulta, você pode correlacionar o desempenho de qualquer consulta por meio do relatório de consulta. Este relatório compara o desempenho da consulta entre diferentes bancos de dados e torna mais fácil identificar os bancos de dados que executam a consulta selecionada bem em comparação com os que estão lentos.

![Consultas de Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Permissões

Para usar Análise de SQL do Azure, os usuários precisam receber uma permissão mínima da função leitor no Azure. No entanto, essa função não permite que os usuários vejam o texto da consulta ou executem quaisquer ações de ajuste automático. Mais funções permissivas no Azure que permitem usar a solução para a extensão mais completa são proprietário, colaborador, colaborador do banco de informações do SQL ou colaborador do SQL Server. Você também pode querer considerar a criação de uma função personalizada no portal com permissões específicas necessárias apenas para usar Análise de SQL do Azure e sem acesso ao gerenciamento de outros recursos.

### <a name="creating-a-custom-role-in-portal"></a>Criando uma função personalizada no portal

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Reconhecendo que algumas organizações impõem controles de permissão estritos no Azure, encontre o seguinte script do PowerShell habilitando a criação de uma função personalizada "operador de monitoramento do SQL Analytics" em portal do Azure com as permissões mínimas de leitura e gravação necessárias para Use Análise de SQL do Azure para sua extensão mais completa.

Substitua "{SubscriptionId}" no script abaixo pela sua ID de assinatura do Azure e execute o script conectado como uma função de proprietário ou colaborador no Azure.

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

Depois que a nova função for criada, atribua essa função a cada usuário de que você precisa para conceder permissões personalizadas para usar Análise de SQL do Azure.

## <a name="analyze-data-and-create-alerts"></a>Analisar dados e criar alertas

A análise de dados no Análise de SQL do Azure é baseada na [linguagem log Analytics](../log-query/get-started-queries.md) para sua consulta e relatórios personalizados. Localize a descrição dos dados disponíveis coletados do recurso de banco de dado para consulta personalizada em [métricas e logs disponíveis](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

Os alertas automatizados na solução baseiam-se na gravação de uma consulta Log Analytics que dispara um alerta sobre uma condição atendida. Encontre abaixo vários exemplos de Log Analytics consultas sobre as quais os alertas podem ser configurados na solução.

### <a name="creating-alerts-for-azure-sql-database"></a>Criando alertas para o banco de dados SQL do Azure

Você pode [criar facilmente alertas](../platform/alerts-metric.md) com os dados provenientes dos recursos do banco de dados SQL do Azure. Aqui estão algumas [consultas de log](../log-query/log-query-overview.md) úteis que você pode usar com um alerta de log:

#### <a name="high-cpu-on-azure-sql-database"></a>Alta CPU no banco de dados SQL do Azure

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - O pré-requisito da configuração desse alerta é que os bancos de dados monitorados transmitem métricas básicas para a solução.
> - Substitua o valor Metricname cpu_percent por dtu_consumption_percent para obter resultados de DTU altos em vez disso.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Alta CPU em pools elásticos do banco de dados SQL do Azure

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - O pré-requisito da configuração desse alerta é que os bancos de dados monitorados transmitem métricas básicas para a solução.
> - Substitua o valor Metricname cpu_percent por dtu_consumption_percent para obter resultados de DTU altos em vez disso.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Armazenamento do banco de dados SQL do Azure em média acima de 95% na última hora

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
> - O pré-requisito da configuração desse alerta é que os bancos de dados monitorados transmitem métricas básicas para a solução.
> - Essa consulta requer que uma regra de alerta seja configurada para disparar um alerta quando houver resultados (> 0 resultados) da consulta, indicando que a condição existe em alguns bancos de dados. A saída é uma lista de recursos de banco de dados que estão acima do storage_threshold dentro do time_range definido.
> - A saída é uma lista de recursos de banco de dados que estão acima do storage_threshold dentro do time_range definido.

#### <a name="alert-on-intelligent-insights"></a>Alerta sobre insights inteligentes

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
> - O pré-requisito da configuração desse alerta é que os bancos de dados monitorados transmitem o log de diagnóstico do sqlsights para a solução.
> - Essa consulta requer que uma regra de alerta seja configurada para ser executada com a mesma frequência que alert_run_interval para evitar resultados duplicados. A regra deve ser configurada para disparar o alerta quando existem resultados (> 0 resultados) da consulta.
> - Personalize o alert_run_interval para especificar o intervalo de tempo para verificar se a condição ocorreu nos bancos de dados configurados para transmitir o log sqlsights para a solução.
> - Personalize o insights_string para capturar a saída do texto de análise da causa raiz do insights. Esse é o mesmo texto exibido na interface do usuário da solução que você pode usar a partir de informações existentes. Como alternativa, você pode usar a consulta abaixo para ver o texto de todas as informações geradas em sua assinatura. Use a saída da consulta para coletar as cadeias de caracteres distintas para configurar alertas em informações.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instance"></a>Criando alertas para Instância Gerenciada

#### <a name="managed-instance-storage-is-above-90"></a>Instância Gerenciada armazenamento está acima de 90%

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
> - O pré-requisito da configuração desse alerta é que o Instância Gerenciada monitorado tem o streaming do log ResourceUsageStats habilitado para a solução.
> - Essa consulta requer que uma regra de alerta seja configurada para disparar um alerta quando houver resultados (> 0 resultados) da consulta, indicando que a condição existe no Instância Gerenciada. A saída é o consumo percentual de armazenamento no Instância Gerenciada.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Instância Gerenciada consumo médio de CPU está acima de 95% na última hora

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
> - O pré-requisito da configuração desse alerta é que o Instância Gerenciada monitorado tem o streaming do log ResourceUsageStats habilitado para a solução.
> - Essa consulta requer que uma regra de alerta seja configurada para disparar um alerta quando houver resultados (> 0 resultados) da consulta, indicando que a condição existe no Instância Gerenciada. A saída é o consumo percentual médio de utilização da CPU no período definido na Instância Gerenciada.

### <a name="pricing"></a>Preços

Embora a solução esteja livre para uso, o consumo de telemetria de diagnóstico acima das unidades livres de ingestão de dados alocada a cada mês se aplica, consulte [preços de log Analytics](https://azure.microsoft.com/pricing/details/monitor). As unidades livres de ingestão de dados fornecida permitem o monitoramento gratuito de vários bancos de dado a cada mês. Observe que mais bancos de dados ativos com cargas de trabalho mais pesadas ingerirão mais informações em comparação com bancos de dados ociosos. Você pode monitorar facilmente o consumo de ingestão de dados na solução selecionando espaço de trabalho do OMS no menu de navegação de Análise de SQL do Azure e, em seguida, selecionando uso e custos estimados.

## <a name="next-steps"></a>Passos seguintes

- Use [consultas de log](../log-query/log-query-overview.md) em Azure monitor para exibir dados do Azure SQL detalhados.
- [Crie seus próprios painéis](../learn/tutorial-logs-dashboards.md) mostrando dados SQL do Azure.
- [Criar alertas](../platform/alerts-overview.md) quando ocorrerem eventos específicos do Azure SQL.
