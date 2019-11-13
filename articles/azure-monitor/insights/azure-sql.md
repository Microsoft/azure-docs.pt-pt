---
title: Análise de SQL do Azure solução no Azure Monitor | Microsoft Docs
description: Solução de análise de SQL do Azure ajuda-o a gerir as suas bases de dados SQL do Azure
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 11/12/2019
ms.reviewer: carlrab
ms.openlocfilehash: b092c547edf37b31e6099227d8cc0e69048bad7a
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961977"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitorizar a base de dados do SQL do Azure através da análise de SQL do Azure (pré-visualização)

![Símbolo de análise de SQL do Azure](./media/azure-sql/azure-sql-symbol.png)

O Análise de SQL do Azure é uma solução de monitoramento de nuvem avançada para monitorar o desempenho de bancos de dados SQL do Azure, pools elásticos e instâncias gerenciadas em escala e entre várias assinaturas por meio de um único painel de vidro. Recolhe e visualiza métricas de desempenho importantes da Base de Dados SQL do Azure com inteligência integrada para resolução de problemas de desempenho.

Ao utilizar as métricas que recolhe com a solução, pode criar regras personalizadas de monitorização e alertas. A solução ajuda-o a identificar problemas em cada camada de sua pilha de aplicativos. Ele usa as métricas de diagnóstico do Azure junto com Azure Monitor exibições para apresentar dados sobre todos os seus bancos de dado SQL do Azure, pools elásticos e bancos de dados em instâncias gerenciadas em um único espaço de trabalho de Log Analytics. Azure Monitor ajuda a coletar, correlacionar e Visualizar dados estruturados e não estruturados.

Para obter uma descrição geral prática sobre como utilizar a solução de análise de SQL do Azure e para cenários de uso típico, veja o vídeo incorporado:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Origens ligadas

Análise de SQL do Azure é uma cloud apenas a monitorização de solução suporte de transmissão em fluxo de telemetria de diagnóstico para bases de dados SQL do Azure: bases de dados de instância únicas, agrupados e geridos. Como a solução não usa agentes para se conectar ao Azure Monitor, a solução não oferece suporte ao monitoramento de SQL Server hospedados localmente ou em VMs, consulte a tabela de compatibilidade abaixo.

| Origem Ligada | Suportado | Descrição |
| --- | --- | --- |
| [Configurações de diagnóstico](../platform/diagnostic-settings.md) | **Sim** | Os dados de log e métrica do Azure são enviados para Azure Monitor logs diretamente pelo Azure. |
| [Conta de armazenamento do Azure](../platform/collect-azure-metrics-logs.md) | Não | Azure Monitor não lê os dados de uma conta de armazenamento. |
| [Agentes do Windows](../platform/agent-windows.md) | Não | Agentes diretos do Windows não são usados pela solução. |
| [Agentes do Linux](../learn/quick-collect-linux-computer.md) | Não | Agentes diretos do Linux não são usados pela solução. |
| [Grupo de gestão do System Center Operations Manager](../platform/om-agents.md) | Não | Uma conexão direta do agente de Operations Manager para Azure Monitor não é usada pela solução. |

## <a name="azure-sql-analytics-options"></a>Opções de Análise de SQL do Azure

A tabela abaixo descreve as opções com suporte para duas versões do painel Análise de SQL do Azure, uma para o banco de dados SQL do Azure e pools elásticos e a outra para Instância Gerenciada.

| Opção Análise de SQL do Azure | Descrição | Suporte de agrupamentos de base de dados SQL e elástico | Suporte a instância gerenciados |
| --- | ------- | ----- | ----- |
| Recurso por tipo | Ponto de vista que conte de todos os recursos monitorizados. | Sim | Sim |
| Informações | Fornece a desagregação hierárquica em informações inteligentes sobre o desempenho. | Sim | Sim |
| Erros | Fornece a desagregação hierárquica em erros de SQL que ocorreram nas bases de dados. | Sim | Sim |
| Tempos limite | Fornece a desagregação hierárquica em tempos limite SQL que ocorreram nas bases de dados. | Sim | Não |
| Bloqueios | Fornece a desagregação hierárquica no blockings SQL que ocorreram nas bases de dados. | Sim | Não |
| Esperas de base de dados | Fornece a desagregação hierárquica para estatísticas de espera SQL no nível da base de dados. Inclui resumos de tempo de espera total e o tempo de espera por tipo de espera. |Sim | Sim |
| Duração da consulta | Fornece a desagregação hierárquica para as estatísticas de execução de consulta, como a duração de consulta, a utilização da CPU, utilização e/s de dados, utilização e/s de registo. | Sim | Sim |
| Esperas de consulta | Fornece a desagregação hierárquica para as estatísticas de espera de consulta por categoria de espera. | Sim | Sim |

## <a name="configuration"></a>Configuração
Use o processo descrito em [Adicionar soluções de Azure monitor da Galeria de soluções](../../azure-monitor/insights/solutions.md) para adicionar a solução de análise de SQL do Azure (versão prévia) ao seu espaço de trabalho do log Analytics.

### <a name="configure-azure-sql-databases-elastic-pools-and-managed-instances-to-stream-diagnostics-telemetry"></a>Configurar bases de dados do Azure SQL, os conjuntos elásticos e instâncias geridas para a telemetria de diagnóstico do stream

Assim que tiver criado a solução de análise de SQL do Azure na sua área de trabalho, precisa **configure cada** recursos que pretende monitorizar a respetiva telemetria de diagnóstico para a solução do stream. Siga as instruções detalhadas nesta página:

- Ativar os diagnósticos do Azure para a base de dados SQL do Azure para [transmitir a telemetria de diagnóstico para análise de SQL do Azure](../../sql-database/sql-database-metrics-diag-logging.md).

A página acima fornece também instruções sobre como ativar o suporte para a monitorização de várias subscrições do Azure a partir de um único espaço de trabalho de análise de SQL do Azure como um único painel de vidro.

## <a name="using-the-solution"></a>Utilizar a solução

Quando adiciona a solução para a área de trabalho, o mosaico de análise de SQL do Azure é adicionado à sua área de trabalho e é apresentado na descrição geral. Selecione Exibir link de resumo para carregar o conteúdo do bloco.

![Bloco de resumo Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-tile-01.png)

Depois de carregado, o bloco mostra o número de bancos de dados SQL do Azure, pools elásticos, instâncias gerenciadas e bancos de dados em instâncias gerenciadas das quais a solução está recebendo a telemetria de diagnóstico.

![Mosaico de análise de SQL do Azure](./media/azure-sql/azure-sql-sol-tile-02.png)

A solução fornece duas vistas separadas – uma para monitorização de bases de dados do Azure SQL e conjuntos elásticos e a outra exibição para monitorizar a instância gerida e bases de dados em instâncias geridas.

Para ver o dashboard de monitorização de análise de SQL do Azure para conjuntos elásticos e bases de dados do Azure SQL, clique na parte superior do mosaico. Para ver o dashboard de monitorização para a instância gerida e bases de dados na instância gerida a análise de SQL do Azure, clique na parte inferior do mosaico.

### <a name="viewing-azure-sql-analytics-data"></a>Ver os dados de análise de SQL do Azure

O dashboard inclui a descrição geral de todas as bases de dados que são monitorizados através de diferentes perspectivas. Para que diferentes perspectivas funcionem, você deve habilitar as métricas ou os logs apropriados em seus recursos do SQL para serem transmitidos para Log Analytics espaço de trabalho.

Observe que, se algumas métricas ou logs não forem transmitidos para Azure Monitor, os blocos na solução não serão preenchidos com informações de monitoramento.

### <a name="azure-sql-database-and-elastic-pool-view"></a>Base de dados SQL do Azure e o modo de exibição do conjunto elástico

Quando o mosaico de análise de SQL do Azure para a base de dados é selecionado, é apresentado o dashboard de monitorização.

![Descrição geral da análise SQL do Azure](./media/azure-sql/azure-sql-sol-overview.png)

Selecionar qualquer um dos mosaicos, abre-se de um relatório de desagregação para o ponto de vista específico. Depois do ponto de vista é selecionado, é aberto o relatório de desagregação.

![Tempos limite de análise SQL do Azure](./media/azure-sql/azure-sql-sol-metrics.png)

Cada ponto de vista nesta vista fornece resumos na subscrição, o servidor, o conjunto elástico e o nível base de dados. Além disso, cada uma delas mostra um ponto de vista específicos para o relatório no lado direito. Selecionar a subscrição, servidor, agrupamento ou base de dados da lista continua a desagregação.

### <a name="managed-instance-and-databases-in-managed-instance-view"></a>A instância gerida e bases de dados na vista de instância gerida

Quando o mosaico de análise de SQL do Azure para as bases de dados é selecionado, é apresentado o dashboard de monitorização.

![Descrição geral da análise SQL do Azure](./media/azure-sql/azure-sql-sol-overview-mi.png)

Selecionar qualquer um dos mosaicos, abre-se de um relatório de desagregação para o ponto de vista específico. Depois do ponto de vista é selecionado, é aberto o relatório de desagregação.

Selecionar vista de instância gerida, mostra detalhes sobre a utilização de instância gerida, contém as bases de dados e telemetria sobre as consultas executadas em toda a instância.

![Tempos limite de análise SQL do Azure](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Relatório de informações inteligente

Base de dados SQL do Azure [informações inteligentes](../../sql-database/sql-database-intelligent-insights.md) permite-lhe determinar o que está acontecendo com desempenho de todas as bases de dados SQL do Azure. Todas as informações inteligentes recolhidos podem ser visualizadas e acessadas por meio da perspectiva de informações.

![Informações de análise SQL do Azure](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Conjunto elástico e os relatórios de dados

Conjuntos elásticos e bases de dados SQL tem seus próprios relatórios específicos que mostram todos os dados que são recolhidos para o recurso no tempo especificado.

![Base de dados de análise SQL do Azure](./media/azure-sql/azure-sql-sol-database.png)

![Conjunto elástico de SQL do Azure](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Relatórios de consulta

Através da duração de consulta e perspectivas de esperas de consulta, pode correlacionar o desempenho de qualquer consulta através do relatório de consulta. Este relatório compara o desempenho das consultas entre bases de dados diferentes e torna mais fácil identificar bases de dados que executam a consulta selecionada bem em comparação com aqueles que forem lentas.

![Consultas de análise SQL do Azure](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Permissões

Para utilizar a análise de SQL do Azure, os utilizadores têm de ser concedida uma permissão mínima da função do leitor no Azure. Esta função, no entanto, não permitir que os utilizadores ver o texto da consulta ou executar quaisquer ações de otimização de automática. As funções mais permissivas no Azure que permitem a utilizar a solução até ao limite máximo são proprietário, Contribuidor, contribuinte da BD SQL ou contribuinte do SQL Server. Também poderá querer considerar a criação de uma função personalizada no portal com permissões específicas necessárias apenas para utilizar a análise de SQL do Azure e sem acesso à gestão de outros recursos.

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

Análise de dados na análise de SQL do Azure baseia-se no [linguagem do Log Analytics](../log-query/get-started-queries.md) para seus relatórios e consultas personalizadas. Descrição de localizar os dados disponíveis recolhidos a partir de recursos de base de dados para consultas personalizadas no [métricas e registos disponíveis](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

Alertas automatizados na solução se baseia em escrever uma consulta do Log Analytics que aciona um alerta numa condição cumprida. Encontre abaixo vários exemplos em consultas do Log Analytics após os alertas podem ser configuradas na solução.

### <a name="creating-alerts-for-azure-sql-database"></a>Criar alertas para a base de dados do Azure SQL

Pode facilmente [criar alertas](../platform/alerts-metric.md) com os dados provenientes de recursos de base de dados do Azure SQL. Aqui estão algumas útil [registar as consultas](../log-query/log-query-overview.md) que pode utilizar com um alerta de registo:

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
> - O pré-requisito da configuração desse alerta é que os bancos de dados monitorados transmitem métricas básicas para a solução.
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
> - O pré-requisito da configuração desse alerta é que os bancos de dados monitorados transmitem métricas básicas para a solução.
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
> - O pré-requisito da configuração desse alerta é que os bancos de dados monitorados transmitem métricas básicas para a solução.
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
> - Pré-requisito de configurar este alerta é o log de diagnóstico que bases de dados monitorizadas stream SQLInsights à solução.
> - Esta consulta requer uma regra de alerta para ser configurado para ser executado com a mesma frequência de alert_run_interval para evitar resultados duplicados. A regra deve ser configurada para disparar o alerta quando existem resultados (> 0 resultados) da consulta.
> - Personalize o alert_run_interval para especificar o intervalo de tempo para verificar se a condição ocorreu em bases de dados configuradas para o registo de SQLInsights de fluxo para a solução.
> - Personalize o insights_string para capturar a saída do texto de análise de causa de raiz de informações. Este é o mesmo texto apresentado na IU da solução que pode utilizar o insights existente. Em alternativa, pode utilizar a consulta abaixo para ver o texto de todas as informações geradas na sua subscrição. Utilize a saída da consulta para coletar as cadeias de caracteres distintas para configurar alertas nas informações.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instance"></a>Criar alertas para a instância gerida

#### <a name="managed-instance-storage-is-above-90"></a>O armazenamento é superior a 90% de instância gerida

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
> - Pré-requisito de configurar este alerta é que a instância gerida monitorizado tem a transmissão em fluxo do registo de ResourceUsageStats ativado para a solução de.
> - Esta consulta requer uma regra de alerta para ser configurado para acionar um alerta quando existem resultados (> 0 resultados) da consulta, que indica que a condição existe na instância gerida. O resultado é o consumo de percentagem de armazenamento na instância gerida.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Consumo médio de CPU de instância gerido é superior a 95%, em que a última 1 hora

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
> - Pré-requisito de configurar este alerta é que a instância gerida monitorizado tem a transmissão em fluxo do registo de ResourceUsageStats ativado para a solução de.
> - Esta consulta requer uma regra de alerta para ser configurado para acionar um alerta quando existem resultados (> 0 resultados) da consulta, que indica que a condição existe na instância gerida. A saída é médio consumo de percentagem de utilização da CPU no período de tempo definido na instância gerida.

### <a name="pricing"></a>Preços

Embora a solução é gratuita, consumo de telemetria de diagnóstico acima as unidades gratuitas de ingestão de dados alocados a cada mês aplica-se, consulte [preços do Log Analytics](https://azure.microsoft.com/pricing/details/monitor). As unidades gratuitas de ingestão de dados fornecido ativar gratuita monitorização de vários bancos de dados por mês. Tenha em atenção que as bases de dados mais ativos com cargas de trabalho mais pesadas ingerir dados mais versus bases de dados inativos. Pode monitorizar facilmente o seu consumo de ingestão de dados na solução, selecione a área de trabalho do OMS no menu de navegação de análise de SQL do Azure e, em seguida, selecionar a utilização e custos estimados.

## <a name="next-steps"></a>Passos seguintes

- Use [consultas de log](../log-query/log-query-overview.md) em Azure monitor para exibir dados do Azure SQL detalhados.
- [Criar seus próprios dashboards](../learn/tutorial-logs-dashboards.md) a mostrar dados de SQL do Azure.
- [Criar alertas](../platform/alerts-overview.md) quando ocorrem eventos específicos do SQL do Azure.
