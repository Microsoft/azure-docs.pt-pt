---
title: Solução Azure SQL Analytics no Monitor Azure [ Monitor Azul] Microsoft Docs
description: A solução Azure SQL Analytics ajuda-o a gerir as suas bases de dados Azure SQL
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 02/21/2020
ms.reviewer: carlrab
ms.openlocfilehash: 921a05c4dc6c1d5cfa663ac71b469573b8f1925b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275467"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitor Azure SQL Database utilizando O Analítico Azure SQL (Pré-visualização)

![Símbolo azure SQL Analytics](./media/azure-sql/azure-sql-symbol.png)

O Azure SQL Analytics é uma solução avançada de monitorização da nuvem para monitorizar o desempenho de todas as suas bases de dados Azure SQL em escala e em várias subscrições numa única vista. A Azure SQL Analytics recolhe e visualiza as principais métricas de desempenho com inteligência incorporada para resolução de problemas de desempenho.

Ao utilizar estas métricas recolhidas, pode criar regras e alertas de monitorização personalizados. O Azure SQL Analytics ajuda-o a identificar problemas em cada camada da sua pilha de aplicações. Utiliza métricas de Diagnóstico Azure juntamente com as vistas do Azure Monitor para apresentar dados sobre todas as suas bases de dados Azure SQL num único espaço de trabalho do Log Analytics. O Azure Monitor ajuda-o a recolher, correlacionar e visualizar dados estruturados e não estruturados.

Para uma visão geral prática sobre a utilização da solução Azure SQL Analytics e para cenários de utilização típicos, consulte o vídeo incorporado:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Origens ligadas

O Azure SQL Analytics é uma solução de monitorização apenas em nuvem que suporta o streaming de telemetria de diagnósticos para todas as suas bases de dados Azure SQL. Uma vez que o Azure SQL Analytics não utiliza agentes para se ligar ao Monitor Azure, não suporta a monitorização do SQL Server hospedado no local ou em máquinas virtuais.

| Origem Ligada | Suportado | Descrição |
| --- | --- | --- |
| [Definições de diagnósticos](../platform/diagnostic-settings.md) | **Sim** | Os dados métricos e de registo do Azure são enviados diretamente para o Azure Monitor Logs. |
| [Conta de armazenamento azure](../platform/collect-azure-metrics-logs.md) | Não | O Monitor Azure não lê os dados de uma conta de armazenamento. |
| [Agentes do Windows](../platform/agent-windows.md) | Não | Os agentes diretos do Windows não são utilizados pela Azure SQL Analytics. |
| [Agentes do Linux](../learn/quick-collect-linux-computer.md) | Não | Os agentes diretos do Linux não são usados pela Azure SQL Analytics. |
| [Grupo de gestão do System Center Operations Manager](../platform/om-agents.md) | Não | Uma ligação direta do agente do Gestor de Operações ao Monitor Azure não é utilizada pela Azure SQL Analytics. |

## <a name="azure-sql-analytics-options"></a>Opções Azure SQL Analytics

A tabela abaixo descreve opções suportadas para duas versões do painel de instrumentos Azure SQL Analytics, uma para bases de dados individuais e agréns e piscinas elásticas, e outra para instâncias geridas e bases de dados de instâncias.

| Opção Azure SQL Analytics | Descrição | Base de dados única e reunida e suporte de piscinas elásticas | Casos geridos e suporte à base de dados de instâncias |
| --- | ------- | ----- | ----- |
| Recurso por tipo | Perspetiva que conta todos os recursos monitorizados. | Sim | Sim |
| Informações | Fornece uma broca hierárquica em Insights Inteligentes para o desempenho. | Sim | Sim |
| Erros | Fornece uma perfuração hierárquica em erros SQL que ocorreram nas bases de dados. | Sim | Sim |
| Tempos limite | Fornece uma perfuração hierárquica em intervalos de SQL que ocorreram nas bases de dados. | Sim | Não |
| Bloqueios | Fornece uma perfuração hierárquica em bloqueios SQL que ocorreram nas bases de dados. | Sim | Não |
| A base de dados aguarda | Fornece uma perfuração hierárquica nas estatísticas de espera sql no nível da base de dados. Inclui resumos do tempo total de espera e do tempo de espera por tipo de espera. |Sim | Não |
| Duração da consulta | Fornece uma perfuração hierárquica nas estatísticas de execução de consultas, tais como duração da consulta, utilização de CPU, utilização de Dados IO, utilização de Log IO. | Sim | Sim |
| Esperas de consultas | Fornece uma perfuração hierárquica nas estatísticas de espera de consulta por categoria de espera. | Sim | Sim |

## <a name="configuration"></a>Configuração

Utilize o processo descrito nas [soluções Add Azure Monitor da Galeria Solutions](../../azure-monitor/insights/solutions.md) para adicionar o Azure SQL Analytics (Pré-visualização) ao seu espaço de trabalho Log Analytics.

### <a name="configure-azure-sql-databases-to-stream-diagnostics-telemetry"></a>Configure bases de dados SQL Azure para transmitir telemetria de diagnósticos

Depois de ter criado a solução Azure SQL Analytics no seu espaço de trabalho, precisa de **configurar cada** recurso que pretende monitorizar para transmitir a sua telemetria de diagnóstico sql analytics. Siga instruções detalhadas nesta página:

- Ative o Azure Diagnostics para a sua base de dados Azure SQL transmitir [telemetria de diagnóstico sQL Analytics](../../sql-database/sql-database-metrics-diag-logging.md).

A página acima também fornece instruções sobre o suporte para a monitorização de várias subscrições Azure a partir de um único espaço de trabalho Azure SQL Analytics como um único painel de vidro.

## <a name="using-azure-sql-analytics"></a>Usando a Azure SQL Analytics

Quando adiciona sql Analytics azure ao seu espaço de trabalho, o azulejo Azure SQL Analytics é adicionado ao seu espaço de trabalho, e aparece em visão geral. Selecione Ver ligação sumária para carregar o conteúdo do azulejo.

![Azulejo resumo Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-01.png)

Uma vez carregado, o azulejo mostra o número de bases de dados individuais e agréctadas, piscinas elásticas, instâncias geridas e bases de dados geridas de instâncias a partir das quais o Azure SQL Analytics está a receber telemetria de diagnóstico.

![Azulejo Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-02.png)

O Azure SQL Analytics fornece duas visões distintas -- uma para monitorizar bases de dados individuais e bases de dados e piscinas elásticas, e a outra vista para monitorizar casos geridos e bases de dados de instâncias.

Para ver o painel de monitorização do Azure SQL Analytics para bases de dados individuais e agrécteis e piscinas elásticas, clique na parte superior do azulejo. Para ver o painel de monitorização do Azure SQL Analytics para casos geridos e bases de dados de instâncias, clique na parte inferior do azulejo.

### <a name="viewing-azure-sql-analytics-data"></a>Visualização de dados do Azure SQL Analytics

O painel inclui a visão geral de todas as bases de dados que são monitorizadas através de diferentes perspetivas. Para que diferentes perspetivas funcionem, deve permitir que métricas ou registos adequados dos seus recursos SQL sejam transmitidos para o espaço de trabalho do Log Analytics.

Se algumas métricas ou registos não forem transmitidos para o Monitor Azure, os azulejos do Azure SQL Analytics não são preenchidos com informações de monitorização.

### <a name="single-and-pooled-databases-and-elastic-pools-view"></a>Bases de dados individuais e reunidas e vista de piscinas elásticas

Uma vez selecionado o azulejo Azure SQL Analytics para a base de dados, o painel de monitorização é mostrado.

![Visão geral da Análise Azure SQL](./media/azure-sql/azure-sql-sol-overview.png)

Selecionando qualquer um dos azulejos, abre um relatório de perfuração para a perspetiva específica. Uma vez selecionada a perspetiva, o relatório de perfuração é aberto.

![Tempos de análise Azure SQL](./media/azure-sql/azure-sql-sol-metrics.png)

Cada perspetiva, neste ponto de vista, fornece resumos nos níveis de subscrição, servidor, piscina elástica e bases de dados. Além disso, cada perspetiva mostra uma perspetiva específica do relatório sobre a direita. A seleção de subscrição, servidor, piscina ou base de dados da lista continua a perfuração.

### <a name="managed-instance-and-instances-databases-view"></a>Vista de dados de instâncias e instâncias geridas

Uma vez selecionado o azulejo Azure SQL Analytics para as bases de dados, o painel de monitorização é mostrado.

![Visão geral da Análise Azure SQL](./media/azure-sql/azure-sql-sol-overview-mi.png)

Selecionando qualquer um dos azulejos, abre um relatório de perfuração para a perspetiva específica. Uma vez selecionada a perspetiva, o relatório de perfuração é aberto.

Selecionando a visão de instância gerida, mostra detalhes sobre a utilização de instâncias geridas, bases de dados que contém e telemetria nas consultas executadas em toda a instância.

![Tempos de análise Azure SQL](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Relatório de Insights Inteligentes

A Base de Dados Azure SQL [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) informa-o sobre o que está a acontecer com o desempenho de todas as bases de dados Azure SQL. Todos os Insights Inteligentes recolhidos podem ser visualizados e acedidos através da perspetiva Insights.

![Insights de análise Azure SQL](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Piscinas elásticas e relatórios de base de dados

Tanto os pools elásticos como as bases de dados têm os seus próprios relatórios específicos que mostram todos os dados recolhidos para o recurso no tempo especificado.

![Base de Dados de Análise Azure SQL](./media/azure-sql/azure-sql-sol-database.png)

![Piscina elástica Azure SQL](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Relatórios de consulta

Através da duração da consulta e das perspetivas de consulta, pode correlacionar o desempenho de qualquer consulta através do relatório de consulta. Este relatório compara o desempenho da consulta em diferentes bases de dados e facilita a identificação de bases de dados que executam bem a consulta selecionada contra as que são lentas.

![Consultas de análise Azure SQL](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Permissões

Para utilizar o Azure SQL Analytics, os utilizadores precisam de uma autorização mínima do papel do Leitor no Azure. Esta função, no entanto, não permite que os utilizadores vejam o texto de consulta, ou realizem quaisquer ações de afinação automática. Funções mais permissivas em Azure que permitem a utilização do Azure SQL Analytics ao máximo são proprietário, colaborador, Colaborador sQL DB ou Colaborador do Servidor SQL. Também pode considerar a criação de um papel personalizado no portal com permissões específicas necessárias apenas para utilizar o Azure SQL Analytics, e sem acesso à gestão de outros recursos.

### <a name="creating-a-custom-role-in-portal"></a>Criar um papel personalizado no portal

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Reconhecendo que algumas organizações impõem controlos rigorosos de permissão em Azure, encontre o seguinte script PowerShell que permite a criação de uma função personalizada "SQL Analytics Monitoring Operator" no portal Azure com as permissões mínimas de leitura e escrita necessárias para utilize a Azure SQL Analytics na sua máxima extensão.

Substitua o "{SubscriptionId}" no script abaixo com o seu ID de subscrição Azure e execute o script registado como proprietário ou papel de contribuinte no Azure.

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

Assim que a nova função for criada, atribua esta função a cada utilizador que precisa de conceder permissões personalizadas para utilizar o Azure SQL Analytics.

## <a name="analyze-data-and-create-alerts"></a>Analisar dados e criar alertas

A análise de dados no Azure SQL Analytics baseia-se no [idioma Log Analytics](../log-query/get-started-queries.md) para a sua consulta e reportagem personalizadas. Encontre a descrição dos dados disponíveis recolhidos a partir de recursos de base de dados para consulta personalizada em [métricas e registos disponíveis](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

O alerta automatizado no Azure SQL Analytics baseia-se na escrita de uma consulta de Log Analytics que desencadeia um alerta sobre uma condição satisfeita. Encontre abaixo vários exemplos em consultas de Log Analytics sobre as quais o alerta pode ser configurado no Azure SQL Analytics.

### <a name="creating-alerts-for-azure-sql-database"></a>Criação de alertas para base de dados Azure SQL

Pode [facilmente criar alertas](../platform/alerts-metric.md) com os dados provenientes dos recursos da Base de Dados Azure SQL. Aqui estão algumas consultas de [registo](../log-query/log-query-overview.md) úteis que pode usar com um alerta de registo:

#### <a name="high-cpu-on-azure-sql-database"></a>Alto CPU na Base de Dados Azure SQL

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
> - Substitua o cpu_percent de valor Do Nome Métrico por dtu_consumption_percent para obter resultados dTU elevados.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Alta CPU em piscinas elásticas azure SQL Database

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
> - Substitua o cpu_percent de valor Do Nome Métrico por dtu_consumption_percent para obter resultados dTU elevados.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Armazenamento de base de dados Azure SQL em média acima de 95% nos últimos 1 hora

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
> - Esta consulta requer uma regra de alerta para disparar um alerta quando existem resultados (> 0 resultados) da consulta, denotando que a condição existe em algumas bases de dados. A produção é uma lista de recursos de base de dados que estão acima do storage_threshold dentro do time_range definido.
> - A produção é uma lista de recursos de base de dados que estão acima do storage_threshold dentro do time_range definido.

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
>
> - A pré-exigência da configuração deste alerta é que as bases de dados monitorizadas transmitem o log de diagnóstico sQLInsights para o Azure SQL Analytics.
> - Esta consulta requer uma regra de alerta a ser configurada com a mesma frequência que alert_run_interval, a fim de evitar resultados duplicados. A regra deve ser configurada para disparar fora do alerta quando existam resultados (> 0 resultados) da consulta.
> - Personalize o alert_run_interval para especificar o intervalo de tempo para verificar se a condição ocorreu em bases de dados configuradas para transmitir o registo SQLInsights para o Azure SQL Analytics.
> - Personalize a insights_string para capturar a saída do texto de análise de causas da raiz insights. Este é o mesmo texto exibido no UI do Azure SQL Analytics que pode utilizar a partir dos insights existentes. Em alternativa, pode utilizar a consulta abaixo para ver o texto de todos os Insights gerados na sua subscrição. Utilize a saída da consulta para colher as cordas distintas para configurar alertas em Insights.

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
> - Esta consulta requer uma regra de alerta a ser configurada para disparar um alerta quando existam resultados (> 0 resultados) da consulta, denotando que a condição existe na instância gerida. A produção é consumo percentual de armazenamento na instância gerida.

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
> - Esta consulta requer uma regra de alerta a ser configurada para disparar um alerta quando existam resultados (> 0 resultados) da consulta, denotando que a condição existe na instância gerida. A produção é um consumo médio de percentagem de utilização de CPU em período definido na instância gerida.

### <a name="pricing"></a>Preços

Embora o Azure SQL Analytics seja livre de usar, aplica-se o consumo de telemetria de diagnósticoacima das unidades gratuitas de ingestão de dados atribuídas todos os meses, ver [preços de Log Analytics.](https://azure.microsoft.com/pricing/details/monitor) As unidades gratuitas de ingestão de dados fornecidas permitem a monitorização gratuita de várias bases de dados todos os meses. Bases de dados mais ativas com cargas de trabalho mais pesadas ingebem mais dados versus bases de dados inativas. Pode monitorizar facilmente o seu consumo de ingestão de dados no Azure SQL Analytics selecionando o Workspace OMS no menu de navegação do Azure SQL Analytics e, em seguida, selecionando o Uso e Custos Estimados.

## <a name="next-steps"></a>Passos seguintes

- Utilize consultas de [registo](../log-query/log-query-overview.md) no Monitor Azure para visualizar dados detalhados do Azure SQL.
- [Crie os seus próprios dashboards](../learn/tutorial-logs-dashboards.md) mostrando dados Azure SQL.
- [Crie alertas](../platform/alerts-overview.md) quando ocorrerem eventos específicos do Azure SQL.
