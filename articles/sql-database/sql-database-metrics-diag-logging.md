---
title: Log de métricas e diagnósticos
description: Saiba como habilitar o diagnóstico no banco de dados SQL do Azure para armazenar informações sobre a utilização de recursos e estatísticas de execução de consultas.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 11/15/2019
ms.openlocfilehash: 95953b4f052531c9804024410e225bb0b5c62aef
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539192"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Log de diagnóstico e métricas do banco de dados SQL do Azure

Neste tópico, você aprenderá a configurar o log de telemetria de diagnóstico para o banco de dados SQL do Azure por meio do portal do Azure, PowerShell, CLI do Azure, Azure Monitor API REST e Azure Resource Manager modelo. Esses diagnósticos podem ser usados para medir a utilização de recursos e estatísticas de execução de consulta.

Bancos de dados individuais, bancos de dados em pool em pools elásticos e bancos de dados de instância em uma instância gerenciada podem transmitir métricas e logs de diagnóstico para facilitar o monitoramento de desempenho. Você pode configurar um banco de dados para transmitir o uso de recursos, trabalhos e sessões e conectividade com um dos seguintes recursos do Azure:

- **Análise de SQL do Azure**: para obter o monitoramento inteligente de seus bancos de dados SQL do Azure que inclui relatórios de desempenho, alertas e recomendações de mitigação.
- **Hubs de eventos do Azure**: para integrar a telemetria do banco de dados SQL com suas soluções de monitoramento personalizadas ou pipelines ativos.
- **Armazenamento do Azure**: para arquivar grandes quantidades de telemetria por uma fração do preço.

    ![Arquitetura](./media/sql-database-metrics-diag-logging/architecture.png)

Para obter mais informações sobre as métricas e as categorias de log com suporte nos vários serviços do Azure, consulte:

- [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Visão geral dos logs de diagnóstico do Azure](../azure-monitor/platform/resource-logs-overview.md)

Este artigo fornece orientação para ajudá-lo a habilitar a telemetria de diagnóstico para bancos de dados SQL do Azure, pools elásticos e instâncias gerenciadas. Ele também pode ajudá-lo a entender como configurar o Análise de SQL do Azure como uma ferramenta de monitoramento para exibir a telemetria do diagnóstico de banco de dados.

## <a name="enable-logging-of-diagnostics-telemetry"></a>Habilitar o registro em log da telemetria de diagnóstico

Você pode habilitar e gerenciar as métricas e o log de telemetria de diagnóstico usando um dos seguintes métodos:

- Portal do Azure
- PowerShell
- CLI do Azure
- API REST do Azure Monitor
- Modelo Azure Resource Manager

Ao habilitar as métricas e o log de diagnóstico, você precisa especificar o destino de recurso do Azure para coletar a telemetria de diagnóstico. As opções disponíveis incluem:

- Análise de SQL do Azure
- Azure Event Hubs
- Storage do Azure

Você pode provisionar um novo recurso do Azure ou selecionar um recurso existente. Depois de escolher um recurso usando a opção **configurações de diagnóstico** , especifique quais dados coletar.

## <a name="supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases"></a>Log de diagnóstico com suporte para bancos de dados SQL do Azure e bancos de dados de instância

Habilite as métricas e o log de diagnóstico em bancos de dados SQL-eles não estão habilitados por padrão.

Você pode configurar bancos de dados SQL do Azure e bancos de dados de instância para coletar a seguinte telemetria de diagnóstico:

| Monitorando a telemetria para bancos de dados | Banco de dados individual e suporte a banco de dados em pool | Suporte a banco de dados de instância |
| :------------------- | ----- | ----- |
| [Métricas básicas](#basic-metrics): contém percentual de DTU/CPU, limite de DTU/CPU, porcentagem de leitura de dados físicos, porcentagem de gravação de log, êxito/falha/bloqueada por conexões de firewall, porcentagem de sessões, porcentagem de trabalhos, armazenamento, porcentagem de armazenamento e XTP porcentagem de armazenamento. | Sim | Não |
| [Instância e aplicativo avançado](#advanced-metrics): contém dados do sistema de arquivos tempdb e tamanho do arquivo de log e o arquivo de log de porcentagem do tempdb usado. | Sim | Não |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): contém informações sobre as estatísticas de tempo de execução de consulta, como uso da CPU e estatísticas de duração da consulta. | Sim | Sim |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): contém informações sobre as estatísticas de espera de consulta (as que suas consultas aguardaram) são CPU, log e bloqueio. | Sim | Sim |
| [Erros](#errors-dataset): contém informações sobre erros do SQL em um banco de dados. | Sim | Sim |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): contém informações sobre quanto tempo um banco de dados gastou aguardando em diferentes tipos de espera. | Sim | Não |
| [Tempos limite](#time-outs-dataset): contém informações sobre tempos limite em um banco de dados. | Sim | Não |
| [Blocos](#blockings-dataset): contém informações sobre o bloqueio de eventos em um banco de dados. | Sim | Não |
| [Deadlocks](#deadlocks-dataset): contém informações sobre eventos de deadlock em um banco de dados. | Sim | Não |
| [AutomaticTuning](#automatic-tuning-dataset): contém informações sobre as recomendações de ajuste automático para um banco de dados. | Sim | Não |
| [Sqlsights](#intelligent-insights-dataset): contém Intelligent insights no desempenho de um banco de dados. Para saber mais, consulte [Intelligent insights](sql-database-intelligent-insights.md). | Sim | Sim |

> [!IMPORTANT]
> Os pools elásticos e as instâncias gerenciadas têm sua própria telemetria de diagnóstico separada dos bancos de dados que eles contêm. É importante observar que a telemetria de diagnóstico é configurada separadamente para cada um desses recursos, conforme documentado abaixo.

> [!NOTE]
> Para habilitar o streaming de log de auditoria, consulte [Configurar a auditoria para seu banco de dados](sql-database-auditing.md#subheading-2)e [auditar logs em logs de Azure monitor e hubs de eventos do Azure](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

## <a name="azure-portal"></a>Portal do Azure

Você pode usar o menu **configurações de diagnóstico** para cada banco de dados único, em pool ou de instância em portal do Azure para configurar o streaming de telemetria de diagnóstico. Além disso, também é possível configurar a telemetria de diagnóstico separadamente para contêineres de banco de dados: pools elásticos e instâncias gerenciadas. Você pode definir os seguintes destinos para transmitir a telemetria de diagnóstico: armazenamento do Azure, hubs de eventos do Azure e logs de Azure Monitor.

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Configurar streaming de telemetria de diagnóstico para pools elásticos

   ![Ícone de pool elástico](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

Você pode configurar um recurso de pool elástico para coletar a seguinte telemetria de diagnóstico:

| Recurso | Monitorando telemetria |
| :------------------- | ------------------- |
| **Pool elástico** | As [métricas básicas](sql-database-metrics-diag-logging.md#basic-metrics) contêm percentual de EDTU/CPU, limite de EDTU/CPU, porcentagem de leitura de dados físicos, percentual de gravação de log, porcentagem de sessões, percentual de operadores, armazenamento, porcentagem de armazenamento, limite de armazenamento e porcentagem de armazenamento XTP. |

Para configurar o streaming de telemetria de diagnóstico para pools elásticos e bancos de dados em pools elásticos, você precisará configurar **as** seguintes opções de forma separada:

- Habilitar streaming de telemetria de diagnóstico para um pool elástico **e**
- Habilitar streaming de telemetria de diagnóstico para cada banco de dados no pool elástico

Isso ocorre porque o pool elástico é um contêiner de banco de dados com sua própria telemetria sendo separada de uma telemetria de banco de dados individual.

Para habilitar o streaming de telemetria de diagnóstico para um recurso de pool elástico, siga estas etapas:

1. Vá para o recurso de **pool elástico** em portal do Azure.
1. Selecione **configurações de diagnóstico**.
1. Selecione **Ativar diagnóstico** se não existir nenhuma configuração anterior ou selecione **Editar configuração** para editar uma configuração anterior.

   ![Habilitar o diagnóstico para pools elásticos](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. Insira um nome de configuração para sua própria referência.
1. Selecione um recurso de destino para os dados de diagnóstico de streaming: **arquivar na conta de armazenamento**, **transmitir para um hub de eventos**ou **Enviar para log Analytics**.
1. Para o log Analytics, selecione **Configurar** e crie um novo espaço de trabalho selecionando **+ criar novo espaço de trabalho**ou selecione um espaço de trabalho existente.
1. Marque a caixa de seleção para telemetria de diagnóstico de pool elástico: métricas **básicas** .
   ![configurar o diagnóstico para pools elásticos](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

1. Selecione **Guardar**.
1. Além disso, configure o streaming de telemetria de diagnóstico para cada banco de dados dentro do pool elástico que você deseja monitorar seguindo as etapas descritas na próxima seção.

> [!IMPORTANT]
> Além de configurar a telemetria de diagnóstico para um pool elástico, você também precisa configurar a telemetria de diagnóstico para cada banco de dados no pool elástico, conforme documentado abaixo.

### <a name="configure-streaming-of-diagnostics-telemetry-for-single-database-or-database-in-elastic-pool"></a>Configurar o streaming de telemetria de diagnóstico para um banco de dados individual ou banco de dados no pool elástico

   ![Ícone do banco de dados SQL](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

Para habilitar o streaming de telemetria de diagnóstico para bancos de dados individuais ou em pool, siga estas etapas:

1. Vá para o recurso de **banco de dados SQL** do Azure.
1. Selecione **configurações de diagnóstico**.
1. Selecione **Ativar diagnóstico** se não existir nenhuma configuração anterior ou selecione **Editar configuração** para editar uma configuração anterior.
   - Você pode criar até três conexões paralelas para a telemetria de diagnóstico de fluxo.
   - Selecione **Adicionar configuração de diagnóstico** para configurar o streaming paralelo de dados de diagnóstico para vários recursos.

   ![Habilitar o diagnóstico para bancos de dados únicos, em pool ou de instância](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)

1. Insira um nome de configuração para sua própria referência.
1. Selecione um recurso de destino para os dados de diagnóstico de streaming: **arquivar na conta de armazenamento**, **transmitir para um hub de eventos**ou **Enviar para log Analytics**.
1. Para obter a experiência de monitoramento padrão baseada em eventos, marque as seguintes caixas de seleção para a telemetria do log de diagnóstico de banco de dados: **Sqlsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** , **Erros**, **DatabaseWaitStatistics**, **tempos limite**, **blocos**e **deadlocks**.
1. Para uma experiência de monitoramento avançada, com base em um minuto, marque a caixa de seleção para métricas **básicas** .
   ![configurar o diagnóstico para bancos de dados únicos, em pool ou de instância](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
1. Selecione **Guardar**.
1. Repita essas etapas para cada banco de dados que você deseja monitorar.

> [!NOTE]
> Para habilitar o streaming de log de auditoria, consulte [Configurar a auditoria para seu banco de dados](sql-database-auditing.md#subheading-2)e [auditar logs em logs de Azure monitor e hubs de eventos do Azure](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

> [!TIP]
> Repita essas etapas para cada banco de dados SQL do Azure que você deseja monitorar.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instances"></a>Configurar streaming de telemetria de diagnóstico para instâncias gerenciadas

   ![Ícone de instância gerenciada](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

Você pode configurar um recurso de instância gerenciada para coletar a seguinte telemetria de diagnóstico:

| Recurso | Monitorando telemetria |
| :------------------- | ------------------- |
| **Instância gerenciada** | [ResourceUsageStats](#resource-usage-stats-for-managed-instance) contém contagem de vCores, percentual médio de CPU, solicitações de e/s, bytes lidos/gravados, espaço de armazenamento reservado e espaço de armazenamento usado. |

Para configurar o streaming de telemetria de diagnóstico para instâncias gerenciadas e bancos de dados de instância, você precisará configurar **as** seguintes opções de forma separada:

- Habilitar streaming de telemetria de diagnóstico para instância gerenciada **e**
- Habilitar streaming de telemetria de diagnóstico para cada banco de dados de instância

Isso ocorre porque a instância gerenciada é um contêiner de banco de dados com sua própria telemetria, separada de uma telemetria de banco de dados de instância individual.

Para habilitar o streaming de telemetria de diagnóstico para um recurso de instância gerenciada, siga estas etapas:

1. Vá para o recurso de **instância gerenciada** em portal do Azure.
1. Selecione **configurações de diagnóstico**.
1. Selecione **Ativar diagnóstico** se não existir nenhuma configuração anterior ou selecione **Editar configuração** para editar uma configuração anterior.

   ![Habilitar o diagnóstico para a instância gerenciada](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. Insira um nome de configuração para sua própria referência.
1. Selecione um recurso de destino para os dados de diagnóstico de streaming: **arquivar na conta de armazenamento**, **transmitir para um hub de eventos**ou **Enviar para log Analytics**.
1. Para o log Analytics, selecione **Configurar** e crie um novo espaço de trabalho selecionando **+ criar novo espaço de trabalho**ou use um espaço de trabalho existente.
1. Marque a caixa de seleção para telemetria de diagnóstico de instância: **ResourceUsageStats**.

   ![Configurar o diagnóstico para a instância gerenciada](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

1. Selecione **Guardar**.
1. Além disso, configure o streaming de telemetria de diagnóstico para cada banco de dados de instância dentro da instância gerenciada que você deseja monitorar seguindo as etapas descritas na próxima seção.

> [!IMPORTANT]
> Além de configurar a telemetria de diagnóstico para uma instância gerenciada, você também precisa configurar a telemetria de diagnóstico para cada banco de dados de instância, conforme documentado abaixo.

### <a name="configure-streaming-of-diagnostics-telemetry-for-instance-databases"></a>Configurar o streaming de telemetria de diagnóstico para bancos de dados de instância

   ![Banco de dados de instância no ícone de instância gerenciada](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Para habilitar o streaming de telemetria de diagnóstico para bancos de dados de instância, siga estas etapas:

1. Vá para o recurso de **banco de dados de instância** na instância gerenciada.
1. Selecione **configurações de diagnóstico**.
1. Selecione **Ativar diagnóstico** se não existir nenhuma configuração anterior ou selecione **Editar configuração** para editar uma configuração anterior.
   - Você pode criar até três (3) conexões paralelas para a telemetria de diagnóstico de fluxo.
   - Selecione **+ Adicionar configuração de diagnóstico** para configurar o streaming paralelo de dados de diagnóstico para vários recursos.

   ![Habilitar o diagnóstico para bancos de dados de instância](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

1. Insira um nome de configuração para sua própria referência.
1. Selecione um recurso de destino para os dados de diagnóstico de streaming: **arquivar na conta de armazenamento**, **transmitir para um hub de eventos**ou **Enviar para log Analytics**.
1. Marque as caixas de seleção para telemetria de diagnóstico de banco de dados: **Sqlsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** e **erros**.
   ![configurar o diagnóstico para bancos de dados de instância](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
1. Selecione **Guardar**.
1. Repita essas etapas para cada banco de dados de instância que você deseja monitorar.

> [!TIP]
> Repita essas etapas para cada banco de dados de instância que você deseja monitorar.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

Você pode habilitar o log de diagnóstico e métricas usando o PowerShell.

- Para ativar o armazenamento de registos de diagnóstico numa conta de armazenamento, utilize este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   A ID da conta de armazenamento é a ID de recurso para a conta de armazenamento de destino.

- Para ativar a transmissão em fluxo de registos de diagnóstico para um hub de eventos, use este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   O ID de regra de Azure Service Bus é uma cadeia de caracteres com este formato:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Para ativar o envio de registos de diagnóstico para uma área de trabalho do Log Analytics, use este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Pode obter o ID de recurso da sua área de trabalho do Log Analytics, utilizando o seguinte comando:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Pode combinar estes parâmetros para ativar várias opções de saída.

### <a name="to-configure-multiple-azure-resources"></a>Para configurar vários recursos do Azure

Para dar suporte a várias assinaturas, use o script do PowerShell de [habilitar o log de métricas de recursos do Azure usando o PowerShell](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/).

Forneça a ID de recurso do espaço de trabalho \<$WSID\> como um parâmetro ao executar o `Enable-AzureRMDiagnostics.ps1` de script para enviar dados de diagnóstico de vários recursos para o espaço de trabalho.

- Para obter a ID do espaço de trabalho \<$WSID\> do destino dos dados de diagnóstico, use o seguinte script:

    ```powershell
    $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

   Substitua \<subID\> pela ID da assinatura, \<RG_NAME\> com o nome do grupo de recursos e \<WS_NAME a\> pelo nome do espaço de trabalho.

### <a name="azure-cli"></a>CLI do Azure

Você pode habilitar o log de diagnóstico e métricas usando o CLI do Azure.

> [!NOTE]
> Há suporte para scripts para habilitar o log de diagnóstico para o CLI do Azure v 1.0. Observe que a CLI v 2.0 não tem suporte no momento.

- Para habilitar o armazenamento de logs de diagnóstico em uma conta de armazenamento, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   A ID da conta de armazenamento é a ID de recurso para a conta de armazenamento de destino.

- Para habilitar o streaming de logs de diagnóstico para um hub de eventos, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   A ID da regra do barramento de serviço é uma cadeia de caracteres com este formato:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Para habilitar o envio de logs de diagnóstico para um espaço de trabalho Log Analytics, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Pode combinar estes parâmetros para ativar várias opções de saída.

### <a name="rest-api"></a>API REST

Leia sobre como [alterar as configurações de diagnóstico usando a API REST do Azure monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

### <a name="resource-manager-template"></a>Modelo do Resource Manager

Leia sobre como [habilitar as configurações de diagnóstico na criação de recursos usando um modelo do Resource Manager](../azure-monitor/platform/diagnostic-settings-template.md).

## <a name="stream-into-azure-sql-analytics"></a>Transmitir para Análise de SQL do Azure

Análise de SQL do Azure é uma solução de nuvem que monitora o desempenho de bancos de dados SQL do Azure, pools elásticos e instâncias gerenciadas em escala e em várias assinaturas. Ele pode ajudá-lo a coletar e visualizar as métricas de desempenho do banco de dados SQL do Azure e tem inteligência interna para solução de problemas de desempenho.

![Descrição geral da análise SQL do Azure](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

As métricas e os logs de diagnóstico do banco de dados SQL podem ser transmitidos em Análise de SQL do Azure usando a opção **Enviar para log Analytics** interna na guia Configurações de diagnóstico no Portal. Você também pode habilitar o log Analytics usando uma configuração de diagnóstico por meio de cmdlets do PowerShell, o CLI do Azure ou a API REST do Azure Monitor.

### <a name="installation-overview"></a>Descrição geral da instalação

Você pode monitorar uma frota de banco de dados SQL com Análise de SQL do Azure. Execute as seguintes etapas:

1. Crie uma solução de Análise de SQL do Azure no Azure Marketplace.
2. Crie um espaço de trabalho de monitoramento na solução.
3. Configure bancos de dados para transmitir a telemetria de diagnósticos para o espaço de trabalho.

Se você estiver usando pools elásticos ou instâncias gerenciadas, também precisará configurar o streaming de telemetria de diagnóstico desses recursos.

### <a name="create-azure-sql-analytics-resource"></a>Criar Análise de SQL do Azure recurso

1. Pesquise Análise de SQL do Azure no Azure Marketplace e selecione-o.

   ![Pesquisar Análise de SQL do Azure no portal](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Selecione **criar** na tela de visão geral da solução.

3. Preencha o formulário de Análise de SQL do Azure com as informações adicionais necessárias: nome do espaço de trabalho, assinatura, grupo de recursos, local e tipo de preço.

   ![Configurar Análise de SQL do Azure no portal](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Selecione **OK** para confirmar e, em seguida, selecione **criar**.

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Configurar bancos de dados para registrar logs de diagnóstico e métricas

A maneira mais fácil de configurar o local em que os bancos de dados registram métricas é usando o portal do Azure. Conforme descrito anteriormente, vá para o recurso de banco de dados SQL no portal do Azure e selecione **configurações de diagnóstico**.

Se você estiver usando pools elásticos ou instâncias gerenciadas, também precisará definir configurações de diagnóstico nesses recursos para permitir que a telemetria de diagnóstico seja transmitida para o espaço de trabalho.

### <a name="use-the-sql-analytics-solution-for-monitoring-and-alerting"></a>Usar a solução de análise de SQL para monitoramento e alertas

Você pode usar a análise do SQL como um painel hierárquico para exibir os recursos do banco de dados SQL.

- Para saber como usar a solução de análise de SQL, consulte [monitorar o banco de dados SQL usando a solução de análise de SQL](../log-analytics/log-analytics-azure-sql.md).
- Para saber como configurar alertas para o banco de dados SQL e a instância gerenciada com base na análise de SQL, consulte [criando alertas para o banco de dados SQL e instância gerenciada](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts).

## <a name="stream-into-event-hubs"></a>Transmitir em fluxo para os Hubs de Eventos

Você pode transmitir os logs de diagnóstico e as métricas do banco de dados SQL para os hubs de eventos usando o **fluxo interno para uma** opção de Hub de eventos na portal do Azure. Você também pode habilitar a ID da regra do barramento de serviço usando uma configuração de diagnóstico por meio de cmdlets do PowerShell, o CLI do Azure ou a API REST Azure Monitor.

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>O que fazer com as métricas e os logs de diagnóstico nos hubs de eventos

Depois que os dados selecionados são transmitidos para os hubs de eventos, você está um pouco mais perto de habilitar cenários de monitoramento avançados. Os hubs de eventos atuam como a porta frontal de um pipeline de eventos. Depois que os dados são coletados em um hub de eventos, eles podem ser transformados e armazenados usando um provedor de análise em tempo real ou um adaptador de armazenamento. Hubs de eventos dissocia a produção de um fluxo de eventos do consumo desses eventos. Dessa forma, os consumidores de eventos podem acessar os eventos em sua própria agenda. Para obter mais informações sobre hubs de eventos, consulte:

- [O que são hubs de eventos do Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Introdução ao Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Você pode usar métricas transmitidas em hubs de eventos para:

- **Exibir a integridade do serviço transmitindo dados de Hot-Path para Power BI**

   Usando os hubs de eventos, Stream Analytics e Power BI, você pode facilmente transformar suas métricas e dados de diagnóstico em informações quase em tempo real nos serviços do Azure. Para obter uma visão geral de como configurar um hub de eventos, processar dados com Stream Analytics e usar Power BI como uma saída, consulte [Stream Analytics e Power bi](../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Transmitir logs para fluxos de telemetria e log de terceiros**

   Usando o streaming de hubs de eventos, você pode obter suas métricas e logs de diagnóstico em várias soluções de monitoramento e log Analytics de terceiros.

- **Criar uma plataforma de registro em log e telemetria personalizada**

   Você já tem uma plataforma de telemetria personalizada ou está considerando criar uma? A natureza de publicação-assinatura altamente escalonável dos hubs de eventos permite ingerir os logs de diagnóstico com flexibilidade. Consulte [o guia de Dan Rosanova sobre para usar os hubs de eventos em uma plataforma de telemetria de escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Transmitir para o armazenamento

Você pode armazenar as métricas do banco de dados SQL e os logs de diagnóstico no armazenamento do Azure usando a opção interno **arquivar em uma conta de armazenamento** no portal do Azure. Você também pode habilitar o armazenamento usando uma configuração de diagnóstico por meio de cmdlets do PowerShell, o CLI do Azure ou a API REST do Azure Monitor.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Esquema de métricas e logs de diagnóstico na conta de armazenamento

Depois de configurar a coleta de métricas e logs de diagnóstico, um contêiner de armazenamento é criado na conta de armazenamento que você selecionou quando as primeiras linhas de dados estão disponíveis. A estrutura dos BLOBs é:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ou, mais simplesmente:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por exemplo, um nome de BLOB para métricas básicas pode ser:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Um nome de BLOB para armazenar dados de um pool elástico é semelhante a:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Política de retenção de dados e preços

Se você selecionar hubs de eventos ou uma conta de armazenamento, poderá especificar uma política de retenção. Esta política exclui dados que são mais antigos do que um período de tempo selecionado. Se você especificar Log Analytics, a política de retenção dependerá do tipo de preço selecionado. Nesse caso, as unidades livres fornecidas de ingestão de dados podem permitir o monitoramento gratuito de vários bancos de dado a cada mês. Qualquer consumo de telemetria de diagnóstico que ultrapasse as unidades livres pode incorrer em custos. Lembre-se de que os bancos de dados ativos com cargas de trabalho mais pesadas ingerirão mais informações do que bancos de dados ociosos. Para obter mais informações, consulte [preços do log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Se você estiver usando Análise de SQL do Azure, poderá monitorar o consumo de ingestão de dados na solução selecionando **espaço de trabalho do OMS** no menu de navegação de análise de SQL do Azure e, em seguida, selecionando **uso** e **custos estimados**.

## <a name="metrics-and-logs-available"></a>Métricas e logs disponíveis

Monitoramento de telemetria disponível para o banco de dados SQL do Azure, pools elásticos e instância gerenciada está documentado abaixo. A telemetria de monitoramento coletada dentro do SQL Analytics pode ser usada para sua própria análise personalizada e desenvolvimento de aplicativos usando Azure Monitor linguagem de [consultas de log](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) .

## <a name="basic-metrics"></a>Métricas básicas

Consulte as tabelas a seguir para obter detalhes sobre as métricas básicas por recurso.

> [!NOTE]
> A opção de métricas básica era conhecida anteriormente como todas as métricas. A alteração feita era apenas para a nomenclatura e não havia nenhuma alteração nas métricas monitoradas. Essa alteração foi iniciada para permitir a introdução de categorias métricas adicionais no futuro.

### <a name="basic-metrics-for-elastic-pools"></a>Métricas básicas para pools elásticos

|**Recurso**|**Métricas**|
|---|---|
|Conjunto elástico|porcentagem de eDTU, eDTU usado, limite de eDTU, percentual de CPU, porcentagem de leitura de dados físicos, percentual de gravação de log, porcentagem de sessões, porcentagem de trabalhos, armazenamento, porcentagem de armazenamento, limite de armazenamento, porcentagem de armazenamento XTP |

### <a name="basic-metrics-for-azure-sql-databases"></a>Métricas básicas para bancos de dados SQL do Azure

|**Recurso**|**Métricas**|
|---|---|
|Base de dados SQL do Azure|Porcentagem de DTU, DTU usada, limite de DTU, porcentagem de CPU, porcentagem de leitura de dados físicos, porcentagem de gravação de log, êxito/falha/bloqueada por conexões de firewall, porcentagem de sessões, porcentagem de trabalhos, armazenamento, porcentagem de armazenamento, percentual de armazenamento XTP e deadlocks |

## <a name="advanced-metrics"></a>Métricas avançadas

Consulte a tabela a seguir para obter detalhes sobre as métricas avançadas.

|**Métricas**|**Nome de exibição da métrica**|**Descrição**|
|---|---|---|
|tempdb_data_size| Tamanho do arquivo de dados tempdb em kilobytes |Tamanho do arquivo de dados tempdb em kilobytes. Não aplicável a data warehouses. Essa métrica estará disponível para bancos de dados usando o modelo de compra vCore ou 100 DTU e superior para modelos de compra baseados em DTU. |
|tempdb_log_size| Tamanho do arquivo de log de tempdb em kilobytes |Tamanho do arquivo de log de tempdb em kilobytes. Não aplicável a data warehouses. Essa métrica estará disponível para bancos de dados usando o modelo de compra vCore ou 100 DTU e superior para modelos de compra baseados em DTU. |
|tempdb_log_used_percent| Log de porcentagem de tempdb usado |Log de porcentagem de tempdb usado. Não aplicável a data warehouses. Essa métrica estará disponível para bancos de dados usando o modelo de compra vCore ou 100 DTU e superior para modelos de compra baseados em DTU. |

## <a name="basic-logs"></a>Logs básicos

Os detalhes da telemetria disponível para todos os logs estão documentados nas tabelas a seguir. Consulte o [log de diagnóstico com suporte](#supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases) para entender quais logs têm suporte para um tipo de banco de dados específico – Azure SQL único, em pool ou em um banco de dados de instância.

### <a name="resource-usage-stats-for-managed-instance"></a>Estatísticas de uso de recursos para instância gerenciada

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de data/hora quando o log foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Always: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: ResourceUsageStats |
|Recurso|O nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: MANAGEDINSTANCES |
|SubscriptionId|GUID de assinatura do banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome da instância gerenciada |
|ResourceId|URI de recurso |
|SKU_s|SKU de produto de instância gerenciada |
|virtual_core_count_s|Número de vCores disponíveis |
|avg_cpu_percent_s|Percentual médio de CPU |
|reserved_storage_mb_s|Capacidade de armazenamento reservada na instância gerenciada |
|storage_space_used_mb_s|Armazenamento usado na instância gerenciada |
|io_requests_s|Contagem de IOPS |
|io_bytes_read_s|Bytes de IOPS lidos |
|io_bytes_written_s|Bytes de IOPS gravados |

### <a name="query-store-runtime-statistics"></a>Estatísticas de tempo de execução Repositório de Consultas

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora quando o log foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Always: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: QueryStoreRuntimeStatistics |
|OperationName|Nome da operação. Sempre: QueryStoreRuntimeStatisticsEvent |
|Recurso|O nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: servidores/bancos de dados |
|SubscriptionId|GUID de assinatura do banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor para o banco de dados |
|ElasticPoolName_s|Nome do pool elástico para o banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|query_hash_s|Hash de consulta |
|query_plan_hash_s|Hash do plano de consulta |
|statement_sql_handle_s|Identificador SQL de instrução |
|interval_start_time_d|Iniciar DateTimeOffset do intervalo em número de tiques de 1900-1-1 |
|interval_end_time_d|Terminar DateTimeOffset do intervalo em número de tiques de 1900-1-1 |
|logical_io_writes_d|Número total de gravações de e/s lógicas |
|max_logical_io_writes_d|Número máximo de gravações de e/s lógicas por execução |
|physical_io_reads_d|Número total de leituras de e/s físicas |
|max_physical_io_reads_d|Número máximo de leituras lógicas de e/s por execução |
|logical_io_reads_d|Número total de leituras lógicas de e/s |
|max_logical_io_reads_d|Número máximo de leituras lógicas de e/s por execução |
|execution_type_d|Tipo de execução |
|count_executions_d|Número de execuções da consulta |
|cpu_time_d|Tempo total de CPU consumido pela consulta em microssegundos |
|max_cpu_time_d|Tempo máximo de CPU do consumidor por uma única execução em microssegundos |
|dop_d|Soma dos graus de paralelismo |
|max_dop_d|Grau máximo de paralelismo usado para execução única |
|rowcount_d|Número total de linhas retornadas |
|max_rowcount_d|Número máximo de linhas retornadas na execução única |
|query_max_used_memory_d|Quantidade total de memória usada em KB |
|max_query_max_used_memory_d|Quantidade máxima de memória usada por uma única execução em KB |
|duration_d|Tempo total de execução em microssegundos |
|max_duration_d|Tempo máximo de execução de uma única execução |
|num_physical_io_reads_d|Número total de leituras físicas |
|max_num_physical_io_reads_d|Número máximo de leituras físicas por execução |
|log_bytes_used_d|Quantidade total de bytes de log usados |
|max_log_bytes_used_d|Quantidade máxima de bytes de log usados por execução |
|query_id_d|ID da consulta no Repositório de Consultas |
|plan_id_d|ID do plano em Repositório de Consultas |

Saiba mais sobre [repositório de consultas dados de estatísticas de tempo de execução](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Estatísticas de espera Repositório de Consultas

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora quando o log foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Always: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: QueryStoreWaitStatistics |
|OperationName|Nome da operação. Sempre: QueryStoreWaitStatisticsEvent |
|Recurso|O nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: servidores/bancos de dados |
|SubscriptionId|GUID de assinatura do banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor para o banco de dados |
|ElasticPoolName_s|Nome do pool elástico para o banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|wait_category_s|Categoria da espera |
|is_parameterizable_s|É a consulta pode conter parâmetro |
|statement_type_s|Tipo da instrução |
|statement_key_hash_s|Hash de chave de instrução |
|exec_type_d|Tipo de execução |
|total_query_wait_time_ms_d|Tempo de espera total da consulta na categoria de espera específica |
|max_query_wait_time_ms_d|Tempo de espera máximo da consulta em execução individual na categoria de espera específica |
|query_param_type_d|0 |
|query_hash_s|Hash de consulta no Repositório de Consultas |
|query_plan_hash_s|Hash do plano de consulta no Repositório de Consultas |
|statement_sql_handle_s|Identificador de instrução no Repositório de Consultas |
|interval_start_time_d|Iniciar DateTimeOffset do intervalo em número de tiques de 1900-1-1 |
|interval_end_time_d|Terminar DateTimeOffset do intervalo em número de tiques de 1900-1-1 |
|count_executions_d|Contagem de execuções da consulta |
|query_id_d|ID da consulta no Repositório de Consultas |
|plan_id_d|ID do plano em Repositório de Consultas |

Saiba mais sobre [repositório de consultas dados de estatísticas de espera](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Conjunto de um erro

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora quando o log foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Always: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: erros |
|OperationName|Nome da operação. Sempre: ErrorEvent |
|Recurso|O nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: servidores/bancos de dados |
|SubscriptionId|GUID de assinatura do banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor para o banco de dados |
|ElasticPoolName_s|Nome do pool elástico para o banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|Mensagem|Mensagem de erro em texto sem formatação |
|user_defined_b|É o bit de erro definido pelo usuário |
|error_number_d|Código de erro |
|Gravidade|Severidade do erro |
|state_d|Estado do erro |
|query_hash_s|Hash de consulta da consulta com falha, se disponível |
|query_plan_hash_s|Hash do plano de consulta da consulta com falha, se disponível |

Saiba mais sobre [SQL Server mensagens de erro](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors?view=sql-server-ver15).

### <a name="database-wait-statistics-dataset"></a>Conjunto de dados de estatísticas Wait

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora quando o log foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Always: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: DatabaseWaitStatistics |
|OperationName|Nome da operação. Sempre: DatabaseWaitStatisticsEvent |
|Recurso|O nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: servidores/bancos de dados |
|SubscriptionId|GUID de assinatura do banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor para o banco de dados |
|ElasticPoolName_s|Nome do pool elástico para o banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|wait_type_s|Nome do tipo de espera |
|start_utc_date_t [UTC]|Hora de início do período medido |
|end_utc_date_t [UTC]|Hora de término do período medido |
|delta_max_wait_time_ms_d|Tempo máximo aguardado por execução |
|delta_signal_wait_time_ms_d|Tempo de espera total de sinais |
|delta_wait_time_ms_d|Tempo de espera total no período |
|delta_waiting_tasks_count_d|Número de tarefas em espera |

Saiba mais sobre [Estatísticas de espera do banco de dados](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Conjunto de data de tempos limite

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora quando o log foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Always: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: tempos limite |
|OperationName|Nome da operação. Sempre: TimeoutEvent |
|Recurso|O nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: servidores/bancos de dados |
|SubscriptionId|GUID de assinatura do banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor para o banco de dados |
|ElasticPoolName_s|Nome do pool elástico para o banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|error_state_d|Código de estado de erro |
|query_hash_s|Hash de consulta, se disponível |
|query_plan_hash_s|Hash do plano de consulta, se disponível |

### <a name="blockings-dataset"></a>Bloco de conjunto de um

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora quando o log foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Always: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: blocos |
|OperationName|Nome da operação. Sempre: BlockEvent |
|Recurso|O nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: servidores/bancos de dados |
|SubscriptionId|GUID de assinatura do banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor para o banco de dados |
|ElasticPoolName_s|Nome do pool elástico para o banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|lock_mode_s|Modo de bloqueio usado pela consulta |
|resource_owner_type_s|Proprietário do bloqueio |
|blocked_process_filtered_s|XML de relatório de processo bloqueado |
|duration_d|Duração do bloqueio em microssegundos |

### <a name="deadlocks-dataset"></a>Conjunto de os deadlocks

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC] |Carimbo de data/hora quando o log foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Always: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: deadlocks |
|OperationName|Nome da operação. Sempre: DeadlockEvent |
|Recurso|O nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: servidores/bancos de dados |
|SubscriptionId|GUID de assinatura do banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor para o banco de dados |
|ElasticPoolName_s|Nome do pool elástico para o banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|deadlock_xml_s|XML do relatório de deadlock |

### <a name="automatic-tuning-dataset"></a>Conjunto de conjuntos de automaticamente

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora quando o log foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Always: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: AutomaticTuning |
|Recurso|O nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: servidores/bancos de dados |
|SubscriptionId|GUID de assinatura do banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor para o banco de dados |
|LogicalDatabaseName_s|Nome do banco de dados |
|ElasticPoolName_s|Nome do pool elástico para o banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|RecommendationHash_s|Hash exclusivo da recomendação de ajuste automático |
|OptionName_s|Operação de ajuste automático |
|Schema_s|Esquema de banco de dados |
|Table_s|Tabela afetada |
|IndexName_s|Nome do índice |
|IndexColumns_s|Nome da coluna |
|IncludedColumns_s|Colunas incluídas |
|EstimatedImpact_s|Impacto estimado do JSON de recomendação de ajuste automático |
|Event_s|Tipo de evento de ajuste automático |
|Timestamp_t|Último carimbo de data/hora atualizado |

### <a name="intelligent-insights-dataset"></a>Conjunto de Intelligent Insights

Saiba mais sobre o [formato de log de Intelligent insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Passos seguintes

Para saber como habilitar o registro em log e entender as métricas e as categorias de log com suporte nos vários serviços do Azure, consulte:

- [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Visão geral dos logs de diagnóstico do Azure](../azure-monitor/platform/resource-logs-overview.md)

Para saber mais sobre os hubs de eventos, leia:

- [O que são hubs de eventos do Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Introdução ao Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Para saber como configurar alertas com base na telemetria do log Analytics, consulte:

- [Criando alertas para o banco de dados SQL e a instância gerenciada](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)
