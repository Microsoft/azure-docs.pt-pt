---
title: Solução Azure SQL Analytics em Azure Monitor | Microsoft Docs
description: A solução Azure SQL Analytics ajuda-o a gerir as suas bases de dados Azure SQL
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 09/19/2020
ms.reviewer: carlrab
ms.openlocfilehash: 54ef88e65925ba9c7e9fe2e44ef0c76fbc9ceb04
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717490"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitor Azure SQL Database usando Azure SQL Analytics (Pré-visualização)

![Símbolo Azure SQL Analytics](./media/azure-sql/azure-sql-symbol.png)

O Azure SQL Analytics é uma solução avançada de monitorização da nuvem para monitorizar o desempenho de todas as suas bases de dados Azure SQL em escala e em várias subscrições numa única vista. O Azure SQL Analytics recolhe e visualiza as principais métricas de desempenho com inteligência incorporada para resolução de problemas de desempenho.

Ao utilizar estas métricas recolhidas, pode criar regras e alertas de monitorização personalizados. O Azure SQL Analytics ajuda-o a identificar problemas em cada camada da sua pilha de aplicações. Utiliza métricas de Diagnóstico Azure juntamente com as vistas do Azure Monitor para apresentar dados sobre todas as suas bases de dados Azure SQL num único espaço de trabalho log analytics. O Azure Monitor ajuda-o a recolher, correlacionar e visualizar dados estruturados e não estruturados.

Para obter uma visão geral prática sobre a utilização da solução Azure SQL Analytics e para cenários típicos de utilização, consulte o vídeo incorporado:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Origens ligadas

O Azure SQL Analytics é uma solução de monitorização de nuvem que suporta o streaming de telemetria de diagnóstico para todas as suas bases de dados Azure SQL. Como o Azure SQL Analytics não utiliza agentes para ligar ao Azure Monitor, não suporta a monitorização do SQL Server hospedado no local ou em máquinas virtuais.

| Origem Ligada | Suportado | Descrição |
| --- | --- | --- |
| [Definições de diagnóstico](../essentials/diagnostic-settings.md) | **Sim** | As métricas Azure e os dados de registo são enviados diretamente para os Registos do Monitor Azure pela Azure. |
| [Conta de armazenamento Azure](../essentials/resource-logs.md#send-to-log-analytics-workspace) | Não | O Azure Monitor não lê os dados de uma conta de armazenamento. |
| [Agentes do Windows](../agents/agent-windows.md) | Não | Os agentes diretos do Windows não são utilizados pela Azure SQL Analytics. |
| [Agentes do Linux](../vm/quick-collect-linux-computer.md) | Não | Os agentes Linux diretos não são usados pela Azure SQL Analytics. |
| [Grupo de gestão do System Center Operations Manager](../agents/om-agents.md) | Não | Uma ligação direta do agente gestor de operações ao Azure Monitor não é utilizada pela Azure SQL Analytics. |

## <a name="azure-sql-analytics-options"></a>Opções Azure SQL Analytics

A tabela abaixo descreve opções suportadas para duas versões do painel Azure SQL Analytics, uma para Azure SQL Database, e outra para bases de dados de Azure SQL Managed Instance.

| Opção Azure SQL Analytics | Descrição | Suporte à base de dados SQL | Suporte ao SQL Managed Instance |
| --- | ------- | ----- | ----- |
| Recurso por tipo | Perspetiva que conta todos os recursos monitorizados. | Sim | Sim |
| Informações | Fornece perfuração hierárquica em Insights Inteligentes para o desempenho. | Sim | Sim |
| Erros | Fornece perfuração hierárquica em erros SQL que aconteceram nas bases de dados. | Sim | Sim |
| Tempos limite | Fornece perfuração hierárquica em intervalos de TEMPO SQL que aconteceram nas bases de dados. | Sim | Não |
| Bloqueios | Fornece perfuração hierárquica em bloqueios SQL que aconteceram nas bases de dados. | Sim | Não |
| A base de dados espera | Fornece perfuração hierárquica em estatísticas de espera SQL no nível da base de dados. Inclui resumos do tempo total de espera e do tempo de espera por tipo de espera. |Sim | Não |
| Duração da consulta | Fornece perfuração hierárquica nas estatísticas de execução de consultas tais como duração da consulta, utilização de CPU, utilização de IO de dados, utilização do Log IO. | Sim | Sim |
| Esperas de consultas | Fornece perfuração hierárquica nas estatísticas de espera de consulta por categoria de espera. | Sim | Sim |

## <a name="configuration"></a>Configuração

Utilize o processo descrito nas [soluções Add Azure Monitor da Galeria de Soluções](./solutions.md) para adicionar Azure SQL Analytics (Preview) ao seu espaço de trabalho Log Analytics.

### <a name="configure-azure-sql-database-to-stream-diagnostics-telemetry"></a>Configure Base de Dados SQL para transmitir telemetria de diagnóstico

Depois de ter criado a solução Azure SQL Analytics no seu espaço de trabalho, precisa de **configurar cada** recurso que pretende monitorizar para transmitir a telemetria de diagnóstico para a Azure SQL Analytics. Siga instruções detalhadas nesta página:

- Ativar o Azure Diagnostics para a sua base de dados para [transmitir telemetria de diagnóstico para Azure SQL Analytics](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

A página acima também fornece instruções sobre como permitir o suporte para monitorizar várias subscrições de Azure a partir de um único espaço de trabalho Azure SQL Analytics como um único painel de vidro.

## <a name="using-azure-sql-analytics"></a>Usando Azure SQL Analytics

Quando adiciona a Azure SQL Analytics ao seu espaço de trabalho, o azulejo Azure SQL Analytics é adicionado ao seu espaço de trabalho, e aparece em visão geral. Selecione Ver Ligação Sumária para carregar o conteúdo do azulejo.

![Azulejo sumário Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-01.png)

Uma vez carregado, o azulejo mostra o número de bases de dados e piscinas elásticas na Base de Dados SQL e casos e dados de dados de casos em SQL Managed Instance a partir do qual a Azure SQL Analytics está a receber telemetria de diagnóstico.

![Azulejo Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-02.png)

O Azure SQL Analytics oferece duas vistas separadas - uma para monitorização da Base de Dados SQL, e a outra vista para monitorizar a SqL Managed Instance.

Para ver o painel de monitorização Azure SQL Analytics para a Base de Dados SQL, clique na parte superior do azulejo. Para ver o painel de monitorização Azure SQL Analytics para a SQL Managed Instance, clique na parte inferior do azulejo.

### <a name="viewing-azure-sql-analytics-data"></a>Visualização de dados do Azure SQL Analytics

O painel inclui a visão geral de todas as bases de dados que são monitorizadas através de diferentes perspetivas. Para que diferentes perspetivas funcionem, deve permitir que métricas ou registos adequados nos seus recursos SQL sejam transmitidos para o espaço de trabalho log Analytics.

Se algumas métricas ou troncos não forem transmitidos para o Azure Monitor, os azulejos em Azure SQL Analytics não são povoados com informações de monitorização.

### <a name="sql-database-view"></a>Vista da base de dados SQL

Uma vez selecionado o azulejo Azure SQL Analytics para a base de dados, é indicado o painel de monitorização.

![Screenshot que mostra o painel de monitorização.](./media/azure-sql/azure-sql-sol-overview.png)

Selecionando qualquer um dos azulejos, abre um relatório de perfuração na perspetiva específica. Uma vez selecionada a perspetiva, o relatório de perfuração é aberto.

![Screenshot que mostra o relatório de perfuração na perspetiva específica.](./media/azure-sql/azure-sql-sol-metrics.png)

Cada perspetiva nesta vista fornece resumos nos níveis de subscrição, servidor, piscina elástica e base de dados. Além disso, cada perspetiva mostra uma perspetiva específica do relatório à direita. A seleção de subscrição, servidor, pool ou base de dados da lista continua a perfuração.

### <a name="sql-managed-instance-view"></a>Vista de instância gerida sql

Uma vez selecionado o azulejo Azure SQL Analytics para as bases de dados, é indicado o painel de monitorização.

![Visão geral da Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview-mi.png)

Selecionando qualquer um dos azulejos, abre um relatório de perfuração na perspetiva específica. Uma vez selecionada a perspetiva, o relatório de perfuração é aberto.

Selecionando a visão SQL Managed Instance, mostra detalhes sobre a utilização de casos, bases de dados de casos e telemetria nas consultas executadas em toda a instância gerida.

![Intervalos de análise Azure SQL](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Relatório de Insights Inteligentes

A azure SQL Database [Intelligent Insights](../../azure-sql/database/intelligent-insights-overview.md) permite-lhe saber o que está a acontecer com o desempenho de todas as bases de dados Azure SQL. Todos os Insights Inteligentes recolhidos podem ser visualizados e acedidos através da perspetiva Insights.

![Azure SQL Analytics Insights](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Piscinas elásticas e relatórios de base de dados

Tanto as piscinas elásticas como as bases de dados têm os seus próprios relatórios específicos que mostram todos os dados recolhidos para o recurso no tempo especificado.

![Base de Dados Azure SQL Analytics](./media/azure-sql/azure-sql-sol-database.png)

![Piscina elástica Azure SQL](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Relatórios de consulta

Através da duração da consulta e da consulta espera perspetivas, pode correlacionar o desempenho de qualquer consulta através do relatório de consulta. Este relatório compara o desempenho da consulta em diferentes bases de dados e facilita a identificação de bases de dados que realizam bem a consulta selecionada em comparação com as que são lentas.

![Consultas Azure SQL Analytics](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Permissões

Para utilizar o Azure SQL Analytics, os utilizadores precisam de ter uma permissão mínima para o papel de Leitor em Azure. Esta função, no entanto, não permite que os utilizadores vejam o texto da consulta, nem realizem quaisquer ações de afinação automática. As funções mais permissivas no Azure que permitem utilizar o Azure SQL Analytics na medida do possível são o Proprietário, o Colaborador, o Contribuinte DB SQL ou o SqL Server Contributor. Também pode considerar a criação de um papel personalizado no portal com permissões específicas necessárias apenas para usar o Azure SQL Analytics, e sem acesso à gestão de outros recursos.

### <a name="creating-a-custom-role-in-portal"></a>Criar um papel personalizado no portal

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Reconhecendo que algumas organizações impõem rigorosos controlos de permissão em Azure, encontre o seguinte script PowerShell permitindo a criação de uma função personalizada "SQL Analytics Monitoring Operator" no portal Azure com as permissões mínimas de leitura e escrita necessárias para usar o Azure SQL Analytics na sua totalidade.

Substitua o "{SubscriptionId}" no script abaixo pelo ID de subscrição Azure e execute o script iniciado como titular ou contribuidor no Azure.

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

Uma vez criada a nova função, atribua esta função a cada utilizador que necessita de conceder permissões personalizadas para utilizar o Azure SQL Analytics.

## <a name="analyze-data-and-create-alerts"></a>Analisar dados e criar alertas

A análise de dados no Azure SQL Analytics baseia-se no [idioma Log Analytics](../logs/get-started-queries.md) para a sua consulta e reporte personalizados. Encontre a descrição dos dados disponíveis recolhidos a partir do recurso de base de dados para consulta personalizada em [métricas e registos disponíveis.](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#metrics-and-logs-available)

O alerta automatizado no Azure SQL Analytics baseia-se na escrita de uma consulta log analytics que desencadeia um alerta sobre uma condição cumprida. Encontre abaixo vários exemplos em consultas de Log Analytics sobre as quais o alerta pode ser configurado no Azure SQL Analytics.

### <a name="creating-alerts-for-azure-sql-database"></a>Criação de alertas para a Base de Dados Azure SQL

Pode [facilmente criar alertas](../alerts/alerts-metric.md) com os dados provenientes dos recursos da Base de Dados Azure SQL. Aqui estão algumas consultas de [registo](../logs/log-query-overview.md) úteis que você pode usar com um alerta de registo:

#### <a name="high-cpu"></a>CPU elevada

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - Pré-requisito para configurar este alerta é que as bases de dados monitorizadas transmitem métricas básicas para Azure SQL Analytics.
> - Substitua o valor MetricName cpu_percent por dtu_consumption_percent para obter resultados de DTU elevados.

#### <a name="high-cpu-on-elastic-pools"></a>CPU alto em piscinas elásticas

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - Pré-requisito para configurar este alerta é que as bases de dados monitorizadas transmitem métricas básicas para Azure SQL Analytics.
> - Substitua o valor MetricName cpu_percent por dtu_consumption_percent para obter resultados de DTU elevados.

#### <a name="storage-in-average-above-95-in-the-last-1-hr"></a>Armazenamento em média acima de 95% nos últimos 1 hora

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
>
> - Pré-requisito para configurar este alerta é que as bases de dados monitorizadas transmitem métricas básicas para Azure SQL Analytics.
> - Esta consulta requer que seja criada uma regra de alerta para disparar um alerta quando houver resultados (> 0 resultados) da consulta, denotando que a condição existe em algumas bases de dados. A saída é uma lista de recursos de base de dados que estão acima do storage_threshold dentro do time_range definido.
> - A saída é uma lista de recursos de base de dados que estão acima do storage_threshold dentro do time_range definido.

#### <a name="alert-on-intelligent-insights"></a>Alerta sobre insights inteligentes

> [!IMPORTANT]
> Caso uma base de dados esteja a ter um bom desempenho, e que não tenha sido gerada nenhuma Informação Inteligente, esta consulta falhará com uma mensagem de erro: Não conseguiu resolver a expressão escalara denominada "rootCauseAnalysis_s". Este comportamento é esperado para todos os casos em que não existam insights inteligentes para a base de dados.

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
>
> - Pré-requisito para configurar este alerta é que as bases de dados monitorizadas transmitem o registo de diagnósticos SQLInsights para Azure SQL Analytics.
> - Esta consulta requer que seja configurada uma regra de alerta para funcionar com a mesma frequência que alert_run_interval para evitar resultados duplicados. A regra deve ser configurada para disparar o alerta quando houver resultados (> 0 resultados) da consulta.
> - Personalize o alert_run_interval para especificar o intervalo de tempo para verificar se a condição ocorreu em bases de dados configuradas para transmitir o registo SQLInsights para a Azure SQL Analytics.
> - Personalize o insights_string para capturar a saída do texto de análise de causa de raiz insights. Este é o mesmo texto exibido no UI da Azure SQL Analytics que pode utilizar a partir dos insights existentes. Em alternativa, pode utilizar a consulta abaixo para ver o texto de todos os Insights gerados na sua subscrição. Utilize a saída da consulta para colher as cordas distintas para configurar alertas em Insights.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-sql-managed-instance"></a>Criação de alertas para exemplos geridos do SQL

#### <a name="storage-is-above-90"></a>O armazenamento é superior a 90%

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
>
> - O pré-requisito da configuração deste alerta é que a instância gerida monitorizada tem o streaming de registo de ResourceUsageStats ativado para Azure SQL Analytics.
> - Esta consulta requer que seja criada uma regra de alerta para disparar um alerta quando houver resultados (> 0 resultados) da consulta, denotando que a condição existe na instância gerida. A produção é o consumo percentual de armazenamento no caso gerido.

#### <a name="cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Consumo médio da CPU é superior a 95% nos últimos 1 hora

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
>
> - O pré-requisito da configuração deste alerta é que a instância gerida monitorizada tem o streaming de registo de ResourceUsageStats ativado para Azure SQL Analytics.
> - Esta consulta requer que seja criada uma regra de alerta para disparar um alerta quando houver resultados (> 0 resultados) da consulta, denotando que a condição existe na instância gerida. A produção é uma percentagem média de utilização do consumo de CPU em período definido no caso gerido.

### <a name="pricing"></a>Preços

Enquanto a Azure SQL Analytics é livre de utilizar, o consumo de telemetria de diagnóstico acima das unidades livres de ingestão de dados atribuídas todos os meses aplica-se, consulte [os preços do Log Analytics](https://azure.microsoft.com/pricing/details/monitor). As unidades gratuitas de ingestão de dados disponibilizadas permitem um acompanhamento gratuito de várias bases de dados todos os meses. Bases de dados mais ativas com cargas de trabalho mais pesadas ingerem mais dados versus bases de dados ociosas. Pode monitorizar facilmente o seu consumo de ingestão de dados no Azure SQL Analytics selecionando o espaço de trabalho OMS no menu de navegação do Azure SQL Analytics e, em seguida, selecionando Utilização e Custos Estimados.

## <a name="next-steps"></a>Passos seguintes

- Utilize [consultas de registo](../logs/log-query-overview.md) no Azure Monitor para ver dados detalhados do Azure SQL.
- [Crie os seus próprios dashboards](../visualize/tutorial-logs-dashboards.md) mostrando dados do Azure SQL.
- [Crie alertas](../alerts/alerts-overview.md) quando ocorrerem eventos específicos do Azure SQL.

