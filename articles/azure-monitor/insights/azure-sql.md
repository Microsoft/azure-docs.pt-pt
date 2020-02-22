---
title: Solução Azure SQL Analytics no Monitor Azure [ Monitor Azul] Microsoft Docs
description: Solução de análise de SQL do Azure ajuda-o a gerir as suas bases de dados SQL do Azure
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 02/21/2020
ms.reviewer: carlrab
ms.openlocfilehash: 8b1eeb94c035df7e5e5e1bb4668b7086eab49b45
ms.sourcegitcommit: 78f367310e243380b591ff10f2500feca93f5d0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77544289"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitorizar a base de dados do SQL do Azure através da análise de SQL do Azure (pré-visualização)

![Símbolo de análise de SQL do Azure](./media/azure-sql/azure-sql-symbol.png)

O Azure SQL Analytics é uma solução avançada de monitorização da nuvem para monitorizar o desempenho de todas as suas bases de dados Azure SQL em escala e em várias subscrições numa única vista. A Azure SQL Analytics recolhe e visualiza as principais métricas de desempenho com inteligência incorporada para resolução de problemas de desempenho.

Ao utilizar estas métricas recolhidas, pode criar regras e alertas de monitorização personalizados. O Azure SQL Analytics ajuda-o a identificar problemas em cada camada da sua pilha de aplicações. Utiliza métricas de Diagnóstico Azure juntamente com as vistas do Azure Monitor para apresentar dados sobre todas as suas bases de dados Azure SQL num único espaço de trabalho do Log Analytics. O Azure Monitor ajuda-o a recolher, correlacionar e visualizar dados estruturados e não estruturados.

Para obter uma descrição geral prática sobre como utilizar a solução de análise de SQL do Azure e para cenários de uso típico, veja o vídeo incorporado:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Origens ligadas

O Azure SQL Analytics é uma solução de monitorização apenas em nuvem que suporta o streaming de telemetria de diagnósticos para todas as suas bases de dados Azure SQL. Uma vez que o Azure SQL Analytics não utiliza agentes para se ligar ao Monitor Azure, não suporta a monitorização do SQL Server hospedado no local ou em máquinas virtuais.

| Origem Ligada | Suportado | Descrição |
| --- | --- | --- |
| [Definições de diagnóstico](../platform/diagnostic-settings.md) | **Sim** | Os dados métricos e de registo do Azure são enviados diretamente para o Azure Monitor Logs. |
| [Conta de armazenamento do Azure](../platform/collect-azure-metrics-logs.md) | Não | O Monitor Azure não lê os dados de uma conta de armazenamento. |
| [Agentes do Windows](../platform/agent-windows.md) | Não | Os agentes diretos do Windows não são utilizados pela Azure SQL Analytics. |
| [Agentes do Linux](../learn/quick-collect-linux-computer.md) | Não | Os agentes diretos do Linux não são usados pela Azure SQL Analytics. |
| [Grupo de gestão de gestão de gestão de gestão de operações do Centro de Sistema](../platform/om-agents.md) | Não | Uma ligação direta do agente do Gestor de Operações ao Monitor Azure não é utilizada pela Azure SQL Analytics. |

## <a name="azure-sql-analytics-options"></a>Opções Azure SQL Analytics

A tabela abaixo descreve opções suportadas para duas versões do painel de instrumentos Azure SQL Analytics, uma para bases de dados individuais e agréns e piscinas elásticas, e outra para instâncias geridas e bases de dados de instâncias.

| Opção Azure SQL Analytics | Descrição | Base de dados única e reunida e suporte de piscinas elásticas | Casos geridos e suporte à base de dados de instâncias |
| --- | ------- | ----- | ----- |
| Recurso por tipo | Ponto de vista que conte de todos os recursos monitorizados. | Sim | Sim |
| Informações | Fornece a desagregação hierárquica em informações inteligentes sobre o desempenho. | Sim | Sim |
| Erros | Fornece a desagregação hierárquica em erros de SQL que ocorreram nas bases de dados. | Sim | Sim |
| Tempos limite | Fornece a desagregação hierárquica em tempos limite SQL que ocorreram nas bases de dados. | Sim | Não |
| Bloqueios | Fornece a desagregação hierárquica no blockings SQL que ocorreram nas bases de dados. | Sim | Não |
| Esperas de base de dados | Fornece a desagregação hierárquica para estatísticas de espera SQL no nível da base de dados. Inclui resumos de tempo de espera total e o tempo de espera por tipo de espera. |Sim | Não |
| Duração da consulta | Fornece a desagregação hierárquica para as estatísticas de execução de consulta, como a duração de consulta, a utilização da CPU, utilização e/s de dados, utilização e/s de registo. | Sim | Sim |
| Esperas de consulta | Fornece a desagregação hierárquica para as estatísticas de espera de consulta por categoria de espera. | Sim | Sim |

## <a name="configuration"></a>Configuração

Utilize o processo descrito nas [soluções Add Azure Monitor da Galeria Solutions](../../azure-monitor/insights/solutions.md) para adicionar o Azure SQL Analytics (Pré-visualização) ao seu espaço de trabalho Log Analytics.

### <a name="configure-azure-sql-databases-to-stream-diagnostics-telemetry"></a>Configure bases de dados SQL Azure para transmitir telemetria de diagnósticos

Depois de ter criado a solução Azure SQL Analytics no seu espaço de trabalho, precisa de **configurar cada** recurso que pretende monitorizar para transmitir a sua telemetria de diagnóstico sql analytics. Siga as instruções detalhadas nesta página:

- Ative o Azure Diagnostics para a sua base de dados Azure SQL transmitir [telemetria de diagnóstico sQL Analytics](../../sql-database/sql-database-metrics-diag-logging.md).

A página acima fornece também instruções sobre como ativar o suporte para a monitorização de várias subscrições do Azure a partir de um único espaço de trabalho de análise de SQL do Azure como um único painel de vidro.

## <a name="using-azure-sql-analytics"></a>Usando a Azure SQL Analytics

Quando adiciona sql Analytics azure ao seu espaço de trabalho, o azulejo Azure SQL Analytics é adicionado ao seu espaço de trabalho, e aparece em visão geral. Selecione Ver ligação sumária para carregar o conteúdo do azulejo.

![Azulejo resumo Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-01.png)

Uma vez carregado, o azulejo mostra o número de bases de dados individuais e agréctadas, piscinas elásticas, instâncias geridas e bases de dados geridas de instâncias a partir das quais o Azure SQL Analytics está a receber telemetria de diagnóstico.

![Mosaico de análise de SQL do Azure](./media/azure-sql/azure-sql-sol-tile-02.png)

O Azure SQL Analytics fornece duas visões distintas -- uma para monitorizar bases de dados individuais e bases de dados e piscinas elásticas, e a outra vista para monitorizar casos geridos e bases de dados de instâncias.

Para ver o painel de monitorização do Azure SQL Analytics para bases de dados individuais e agrécteis e piscinas elásticas, clique na parte superior do azulejo. Para ver o painel de monitorização do Azure SQL Analytics para casos geridos e bases de dados de instâncias, clique na parte inferior do azulejo.

### <a name="viewing-azure-sql-analytics-data"></a>Ver os dados de análise de SQL do Azure

O dashboard inclui a descrição geral de todas as bases de dados que são monitorizados através de diferentes perspectivas. Para que diferentes perspetivas funcionem, deve permitir que métricas ou registos adequados dos seus recursos SQL sejam transmitidos para o espaço de trabalho do Log Analytics.

Se algumas métricas ou registos não forem transmitidos para o Monitor Azure, os azulejos do Azure SQL Analytics não são preenchidos com informações de monitorização.

### <a name="single-and-pooled-databases-and-elastic-pools-view"></a>Bases de dados individuais e reunidas e vista de piscinas elásticas

Quando o mosaico de análise de SQL do Azure para a base de dados é selecionado, é apresentado o dashboard de monitorização.

![Descrição geral da análise SQL do Azure](./media/azure-sql/azure-sql-sol-overview.png)

Selecionar qualquer um dos mosaicos, abre-se de um relatório de desagregação para o ponto de vista específico. Depois do ponto de vista é selecionado, é aberto o relatório de desagregação.

![Tempos limite de análise SQL do Azure](./media/azure-sql/azure-sql-sol-metrics.png)

Cada perspetiva, neste ponto de vista, fornece resumos nos níveis de subscrição, servidor, piscina elástica e bases de dados. Além disso, cada uma delas mostra um ponto de vista específicos para o relatório no lado direito. Selecionar a subscrição, servidor, agrupamento ou base de dados da lista continua a desagregação.

### <a name="managed-instance-and-instances-databases-view"></a>Vista de dados de instâncias e instâncias geridas

Quando o mosaico de análise de SQL do Azure para as bases de dados é selecionado, é apresentado o dashboard de monitorização.

![Descrição geral da análise SQL do Azure](./media/azure-sql/azure-sql-sol-overview-mi.png)

Selecionar qualquer um dos mosaicos, abre-se de um relatório de desagregação para o ponto de vista específico. Depois do ponto de vista é selecionado, é aberto o relatório de desagregação.

Selecionando a visão de instância gerida, mostra detalhes sobre a utilização de instâncias geridas, bases de dados que contém e telemetria nas consultas executadas em toda a instância.

![Tempos limite de análise SQL do Azure](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Relatório de informações inteligente

A Base de Dados Azure SQL [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) informa-o sobre o que está a acontecer com o desempenho de todas as bases de dados Azure SQL. Todas as informações inteligentes recolhidos podem ser visualizadas e acessadas por meio da perspectiva de informações.

![Informações de análise SQL do Azure](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Piscinas elásticas e relatórios de base de dados

Tanto os pools elásticos como as bases de dados têm os seus próprios relatórios específicos que mostram todos os dados recolhidos para o recurso no tempo especificado.

![Base de dados de análise SQL do Azure](./media/azure-sql/azure-sql-sol-database.png)

![Conjunto elástico de SQL do Azure](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Relatórios de consulta

Através da duração da consulta e das perspetivas de consulta, pode correlacionar o desempenho de qualquer consulta através do relatório de consulta. Este relatório compara o desempenho das consultas entre bases de dados diferentes e torna mais fácil identificar bases de dados que executam a consulta selecionada bem em comparação com aqueles que forem lentas.

![Consultas de análise SQL do Azure](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Permissões

Para utilizar a análise de SQL do Azure, os utilizadores têm de ser concedida uma permissão mínima da função do leitor no Azure. Esta função, no entanto, não permitir que os utilizadores ver o texto da consulta ou executar quaisquer ações de otimização de automática. Funções mais permissivas em Azure que permitem a utilização do Azure SQL Analytics ao máximo são proprietário, colaborador, Colaborador sQL DB ou Colaborador do Servidor SQL. Também poderá querer considerar a criação de uma função personalizada no portal com permissões específicas necessárias apenas para utilizar a análise de SQL do Azure e sem acesso à gestão de outros recursos.

### <a name="creating-a-custom-role-in-portal"></a>Criar uma função personalizada no portal

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

RECONHECENDO que algumas organizações impõem controlos de permissão strict no Azure, localize o seguinte script do PowerShell, permitindo a criação de uma função personalizada "SQL Analytics operador de monitorização" no portal do Azure com o mínimo de leitura e escrita as permissões necessárias para Utilize a análise de SQL do Azure para sua extensão mais completa.

Substitua o "{SubscriptionId}" no script com o seu ID de subscrição do Azure, abaixo e execute o script de logon como uma função de proprietário ou Contribuidor no Azure.

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

Depois de criar a nova função, atribua esta função para cada utilizador que tem de conceder permissões personalizadas para utilizar a análise de SQL do Azure.

## <a name="analyze-data-and-create-alerts"></a>Analisar dados e criar alertas

A análise de dados no Azure SQL Analytics baseia-se no [idioma Log Analytics](../log-query/get-started-queries.md) para a sua consulta e reportagem personalizadas. Encontre a descrição dos dados disponíveis recolhidos a partir de recursos de base de dados para consulta personalizada em [métricas e registos disponíveis](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

O alerta automatizado no Azure SQL Analytics baseia-se na escrita de uma consulta de Log Analytics que desencadeia um alerta sobre uma condição satisfeita. Encontre abaixo vários exemplos em consultas de Log Analytics sobre as quais o alerta pode ser configurado no Azure SQL Analytics.

### <a name="creating-alerts-for-azure-sql-database"></a>Criar alertas para a base de dados do Azure SQL

Pode [facilmente criar alertas](../platform/alerts-metric.md) com os dados provenientes dos recursos da Base de Dados Azure SQL. Aqui estão algumas consultas de [registo](../log-query/log-query-overview.md) úteis que pode usar com um alerta de registo:

#### <a name="high-cpu-on-azure-sql-database"></a>Elevada da CPU na base de dados SQL do Azure

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
> - A pré-exigência da configuração deste alerta é que as bases de dados monitorizadas transmitem métricas básicas para o Azure SQL Analytics.
> - Substitua o cpu_percent de valor MetricName dtu_consumption_percent para obter resultados DTU elevados em vez disso.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Elevada da CPU em conjuntos elásticos da base de dados do Azure SQL

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
> - A pré-exigência da configuração deste alerta é que as bases de dados monitorizadas transmitem métricas básicas para o Azure SQL Analytics.
> - Substitua o cpu_percent de valor MetricName dtu_consumption_percent para obter resultados DTU elevados em vez disso.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Armazenamento de base de dados SQL do Azure em média superior a 95% na última 1 hora

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
> - A pré-exigência da configuração deste alerta é que as bases de dados monitorizadas transmitem métricas básicas para o Azure SQL Analytics.
> - Esta consulta requer uma regra de alerta para ser configurado para acionar um alerta quando existem resultados (> 0 resultados) da consulta, que indica se a condição existe no algumas bases de dados. O resultado é uma lista de recursos de base de dados que estão acima storage_threshold dentro time_range definido.
> - O resultado é uma lista de recursos de base de dados que estão acima storage_threshold dentro time_range definido.

#### <a name="alert-on-intelligent-insights"></a>Alerta sobre informações inteligentes

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
> - A pré-exigência da configuração deste alerta é que as bases de dados monitorizadas transmitem o log de diagnóstico sQLInsights para o Azure SQL Analytics.
> - Esta consulta requer uma regra de alerta para ser configurado para ser executado com a mesma frequência de alert_run_interval para evitar resultados duplicados. A regra deve ser configurada para disparar o alerta quando existem resultados (> 0 resultados) da consulta.
> - Personalize o alert_run_interval para especificar o intervalo de tempo para verificar se a condição ocorreu em bases de dados configuradas para transmitir o registo SQLInsights para o Azure SQL Analytics.
> - Personalize o insights_string para capturar a saída do texto de análise de causa de raiz de informações. Este é o mesmo texto exibido no UI do Azure SQL Analytics que pode utilizar a partir dos insights existentes. Em alternativa, pode utilizar a consulta abaixo para ver o texto de todas as informações geradas na sua subscrição. Utilize a saída da consulta para coletar as cadeias de caracteres distintas para configurar alertas nas informações.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instances"></a>Criação de alertas para casos geridos

#### <a name="managed-instance-storage-is-above-90"></a>O armazenamento de instâncias geridas é superior a 90%

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
> - A pré-exigência da configuração deste alerta é que a instância gerida tem o fluxo de registo DeRecursosUsageStats habilitado para o Azure SQL Analytics.
> - Esta consulta requer uma regra de alerta a ser configurada para disparar um alerta quando existem resultados (> 0 resultados) da consulta, denotando que a condição existe na instância gerida. A produção é consumo percentual de armazenamento na instância gerida.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Consumo médio de CPU por exemplo gerido é superior a 95% nos últimos 1 hora

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
> - A pré-exigência da configuração deste alerta é que a instância gerida monitorizada tem o fluxo de registo DeUtilização de Recursos Ativado para o Azure SQL Analytics.
> - Esta consulta requer uma regra de alerta a ser configurada para disparar um alerta quando existem resultados (> 0 resultados) da consulta, denotando que a condição existe na instância gerida. A produção é um consumo médio de percentagem de utilização de CPU em período definido na instância gerida.

### <a name="pricing"></a>Preços

Embora o Azure SQL Analytics seja livre de usar, aplica-se o consumo de telemetria de diagnósticoacima das unidades gratuitas de ingestão de dados atribuídas todos os meses, ver [preços de Log Analytics.](https://azure.microsoft.com/pricing/details/monitor) As unidades gratuitas de ingestão de dados fornecido ativar gratuita monitorização de vários bancos de dados por mês. Bases de dados mais ativas com cargas de trabalho mais pesadas ingebem mais dados versus bases de dados inativas. Pode monitorizar facilmente o seu consumo de ingestão de dados no Azure SQL Analytics selecionando o Workspace OMS no menu de navegação do Azure SQL Analytics e, em seguida, selecionando o Uso e Custos Estimados.

## <a name="next-steps"></a>Passos seguintes

- Utilize consultas de [registo](../log-query/log-query-overview.md) no Monitor Azure para visualizar dados detalhados do Azure SQL.
- [Crie os seus próprios dashboards](../learn/tutorial-logs-dashboards.md) mostrando dados Azure SQL.
- [Crie alertas](../platform/alerts-overview.md) quando ocorrerem eventos específicos do Azure SQL.
