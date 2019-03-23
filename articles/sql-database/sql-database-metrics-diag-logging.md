---
title: Registo de diagnóstico e de métricas de base de dados SQL do Azure | Documentos da Microsoft
description: Saiba como configurar a base de dados do Azure SQL para armazenar as estatísticas de execução de consulta e de utilização de recursos.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: da7dfdb1217e41b7dcb7c7fb6ade55c33488e54b
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372612"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Métricas de base de dados SQL do Azure e o registo de diagnósticos

Instância bases de dados numa instância gerida podem transmitir em fluxo registos de diagnóstico e métricas para monitorização de desempenho mais fácil, bases de dados agrupadas em conjuntos elásticos e bases de dados individuais. Pode configurar uma base de dados para transmitir a utilização de recursos, funções de trabalho e sessões e conectividade a um dos seguintes recursos do Azure:

- **Análise de SQL do Azure**: para obter monitorização inteligentes de seus bancos de dados SQL do Azure, que inclui relatórios de desempenho, alertas e recomendações de atenuação.
- **Os Hubs de eventos do Azure**: para integrar a telemetria de base de dados SQL com as soluções de monitorização personalizadas monitorizção ou em pipelines.
- **O armazenamento do Azure**: para arquivar grandes quantidades de telemetria para uma fração do preço.

    ![Arquitetura](./media/sql-database-metrics-diag-logging/architecture.png)

Para obter mais informações sobre as categorias de métricas e registo suportado por vários serviços do Azure, consulte:

- [Descrição geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Descrição geral dos registos de diagnóstico do Azure](../azure-monitor/platform/diagnostic-logs-overview.md)

Este artigo fornece orientações para o ajudar a ativar a telemetria de diagnóstico para bases de dados SQL do Azure, conjuntos elásticos e instâncias geridas. Ele também pode ajudar a compreender como configurar a análise de SQL do Azure como uma ferramenta de monitoramento para ver a telemetria de diagnóstico de base de dados.

## <a name="enable-logging-of-diagnostics-telemetry"></a>Ativar o registo de telemetria de diagnóstico

Pode ativar e gerir as métricas e registos de telemetria de diagnóstico utilizando um dos seguintes métodos:

- Portal do Azure
- PowerShell
- CLI do Azure
- API de REST do Azure Monitor
- Modelo Azure Resource Manager

Quando ativar métricas e registo de diagnósticos, tem de especificar o destino de recursos do Azure para recolher a telemetria de diagnóstico. Opções disponíveis incluem:

- Análise de SQL do Azure
- Azure Event Hubs
- Storage do Azure

Pode aprovisionar um novo recurso do Azure ou selecione um recurso existente. Depois de escolher um recurso utilizando o **das definições de diagnóstico** opção, especifique os dados a recolher.

## <a name="supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases"></a>Suportado registo de diagnósticos para bases de dados SQL do Azure e bases de dados de instância

Ativar a métricas e diagnósticos de registo em bases de dados SQL – não estão ativadas por predefinição.

Pode configurar bases de dados SQL do Azure e bases de dados de instância para recolher a telemetria de diagnóstico seguinte:

| Telemetria para bases de dados de monitorização | Base de dados e suporte de bases de dados agrupadas | Suporte de base de dados de instância |
| :------------------- | ----- | ----- |
| [Todas as métricas](#all-metrics): Contém a percentagem de DTU/CPU, limite de DTU/da CPU, físico dados lidos percentagem, de registo de escrita em percentagem, com êxito/com falhas/bloqueado por ligações de firewall, percentagem de sessões, percentagem de funções de trabalho, armazenamento, percentagem de armazenamento e percentagem de armazenamento do XTP. | Sim | Não |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): Contém informações sobre as estatísticas de tempo de execução de consulta como a utilização da CPU e estatísticas de duração de consulta. | Sim | Sim |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): Contém informações sobre as estatísticas de espera de consulta (o que as suas consultas aguardado), como CPU, o registo e o bloqueio. | Sim | Sim |
| [Erros](#errors-dataset): Contém informações sobre erros do SQL na base de dados. | Sim | Sim |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): Contém informações sobre quanto tempo a base de dados gasto aguardando tipos diferentes de espera. | Sim | Não |
| [Tempos limite](#time-outs-dataset): Contém informações sobre tempos limite na base de dados. | Sim | Não |
| [Blocos](#blockings-dataset): Contém informações sobre o bloqueio de eventos na base de dados. | Sim | Não |
| [SQLInsights](#intelligent-insights-dataset): Contém informações inteligentes sobre o desempenho. Para obter mais informações, consulte [informações inteligentes](sql-database-intelligent-insights.md). | Sim | Sim |

> [!IMPORTANT]
> Conjuntos elásticos e as instâncias geridas que a sua própria telemetria de diagnóstico separado dos bancos de dados que contêm. Isso é importante observar que a telemetria de diagnóstico está configurada em separado para cada um destes recursos, como documentado a seguir.

## <a name="azure-portal"></a>Portal do Azure

Pode usar **as definições de diagnóstico** menu para cada um único, agrupados, ou instância de base de dados no portal do Azure para configurar a transmissão em fluxo de telemetria de diagnóstico. Além disso, telemetria de diagnóstico pode também ser configurada separadamente para os contentores de base de dados: conjuntos elásticos e as instâncias geridas. Pode definir os seguintes destinos para transmitir a telemetria de diagnóstico: Registos do Azure, armazenamento, o Event Hubs do Azure e o Azure Monitor.

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Configurar a transmissão em fluxo de telemetria de diagnóstico para conjuntos elásticos

   ![Ícone de conjunto elástico](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

Pode configurar um recurso de conjunto elástico para recolher a telemetria de diagnóstico seguinte:

| Recurso | Monitorização de telemetria |
| :------------------- | ------------------- |
| **Conjunto elástico** | [Todas as métricas](sql-database-metrics-diag-logging.md#all-metrics) contém a percentagem de eDTU/CPU, limite de eDTU/da CPU, físico dados lidos percentagem, log escrever percentagem, percentagem de sessões, percentagem de funções de trabalho, armazenamento, percentagem de armazenamento, limite de armazenamento e a percentagem de armazenamento do XTP. |

Para ativar a transmissão em fluxo de telemetria de diagnóstico para um recurso de conjunto elástico, siga estes passos:

1. Vá para o recurso de conjunto elástico no portal do Azure.
1. Selecione **as definições de diagnóstico**.
1. Selecione **ativar os diagnósticos** se não existem definições anteriores existe ou selecione **Editar definição** para editar uma definição anterior.

   ![Ativar diagnósticos para conjuntos elásticos](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. Introduza um nome de definição para sua própria referência.
1. Selecione um recurso de destino para os dados de diagnóstico de transmissão em fluxo: **Arquivo para a conta de armazenamento**, **Stream para um hub de eventos**, ou **enviar para o Log Analytics**.
1. Para o log analytics, selecione **configurar** e criar uma nova área de trabalho ao selecionar **+ criar nova área de trabalho**, ou selecione uma área de trabalho existente.
1. Selecione a caixa de verificação de telemetria de diagnóstico do conjunto elástico: **AllMetrics**.
1. Selecione **Guardar**.

   ![Configurar os diagnósticos para conjuntos elásticos](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

> [!IMPORTANT]
> Além de configurar a telemetria de diagnóstico de um conjunto elástico, também tem de configurar a telemetria de diagnóstico para cada base de dados num conjunto elástico, como documentado a seguir. 

### <a name="configure-streaming-of-diagnostics-telemetry-for-single-database-or-database-in-elastic-pool"></a>Configurar a transmissão em fluxo de telemetria de diagnóstico para a base de dados ou base de dados num conjunto elástico

   ![Ícone de base de dados SQL](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

Para ativar de transmissão em fluxo de telemetria de diagnóstico para único, agrupados, ou bases de dados de instância, siga estes passos:

1. Aceda ao seu recurso de base de dados SQL do Azure.
1. Selecione **as definições de diagnóstico**.
1. Selecione **ativar os diagnósticos** se não existem definições anteriores existe ou selecione **Editar definição** para editar uma definição anterior.
   - Pode criar até três ligações paralelas a telemetria de diagnóstico do fluxo.
   - Selecione **+ Adicionar definição de diagnóstico** para configurar a transmissão em fluxo paralela de dados de diagnóstico para vários recursos.

   ![Ativar os diagnósticos para única, agrupada ou bases de dados de instância](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)
1. Introduza um nome de definição para sua própria referência.
1. Selecione um recurso de destino para os dados de diagnóstico de transmissão em fluxo: **Arquivo para a conta de armazenamento**, **Stream para um hub de eventos**, ou **enviar para o Log Analytics**.
1. Para a experiência de monitorização padrão, com base em eventos, selecione as caixas de verificação seguintes para a telemetria de registo de diagnóstico de base de dados: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **erros** , **DatabaseWaitStatistics**, **tempos limite**, **blocos**, e **Deadlocks**.
1. Para uma experiência de monitorização avançada, de minuto-baseado num, selecione a caixa de verificação **AllMetrics**.
1. Selecione **Guardar**.

   ![Configurar os diagnósticos para única, agrupada ou bases de dados de instância](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)

> [!NOTE]
> Não não possível ativar os registos de auditoria de segurança das definições de diagnóstico da base de dados. Para ativar a transmissão de registos de auditoria, consulte [configurar a auditoria da base de dados](sql-database-auditing.md#subheading-2), e [registos em registos de Monitor do Azure e Hubs de eventos de auditoria](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/13/sql-audit-logs-in-azure-log-analytics-and-azure-event-hubs/).
> [!TIP]
> Repita estes passos para cada base de dados de SQL do Azure que pretende monitorizar.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instances"></a>Configurar a transmissão em fluxo de telemetria de diagnóstico para instâncias geridas

   ![Ícone de instância gerida](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

Pode configurar um recurso de instância gerida para recolher a telemetria de diagnóstico seguinte:

| Recurso | Monitorização de telemetria |
| :------------------- | ------------------- |
| **Instância gerida** | ResourceUsageStats contém a contagem de vCores, percentagem de CPU média, pedidos de e/s, espaço de armazenamento reservada, lidos/escritos de bytes e espaço de armazenamento utilizado. |

Para ativar a transmissão em fluxo de telemetria de diagnóstico para um recurso de instância gerida, siga estes passos:

1. Vá para o recurso de instância gerida no portal do Azure.
1. Selecione **as definições de diagnóstico**.
1. Selecione **ativar os diagnósticos** se não existem definições anteriores existe ou selecione **Editar definição** para editar uma definição anterior.

   ![Ativar diagnósticos para a instância gerida](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. Introduza um nome de definição para sua própria referência.
1. Selecione um recurso de destino para os dados de diagnóstico de transmissão em fluxo: **Arquivo para a conta de armazenamento**, **Stream para um hub de eventos**, ou **enviar para o Log Analytics**.
1. Para o log analytics, selecione **configurar** e criar uma nova área de trabalho ao selecionar **+ criar nova área de trabalho**, ou utilizar uma área de trabalho existente.
1. Selecione a caixa de verificação de telemetria de diagnóstico por exemplo: **ResourceUsageStats**.
1. Selecione **Guardar**.

   ![Configurar diagnósticos para a instância gerida](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

> [!IMPORTANT]
> Além de configurar a telemetria de diagnóstico para uma instância gerida, terá também de configurar a telemetria de diagnóstico para cada instância da base de dados, como documentado a seguir. 

### <a name="configure-streaming-of-diagnostics-telemetry-for-instance-databases"></a>Configurar a transmissão de telemetria de diagnóstico, por exemplo, bancos de dados

   ![Base de dados de instância no ícone de instância gerida](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Para ativar a transmissão de telemetria de diagnóstico, por exemplo, bancos de dados, siga estes passos:

1. Vá para a base de dados de instância na instância gerida.
2. Selecione **as definições de diagnóstico**.
3. Selecione **ativar os diagnósticos** se não existem definições anteriores existe ou selecione **Editar definição** para editar uma definição anterior.
   - Pode criar até três (3) paralelas ligações a telemetria de diagnóstico do fluxo.
   - Selecione **+ Adicionar definição de diagnóstico** para configurar a transmissão em fluxo paralela de dados de diagnóstico para vários recursos.

   ![Ativar diagnósticos, por exemplo bases de dados](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

4. Introduza um nome de definição para sua própria referência.
5. Selecione um recurso de destino para os dados de diagnóstico de transmissão em fluxo: **Arquivo para a conta de armazenamento**, **Stream para um hub de eventos**, ou **enviar para o Log Analytics**.
6. Selecione as caixas de verificação para a telemetria de diagnóstico de base de dados: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** e **erros**.
7. Selecione **Guardar**.

   ![Configurar diagnósticos, por exemplo bases de dados](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)

> [!TIP]
> Repita estes passos para cada base de dados de instância que pretende monitorizar.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é suportado pelo SQL Database do Azure, mas todo o desenvolvimento futuro é para o módulo de Az.Sql. Para estes cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Pode ativar métricas e registo de diagnósticos com o PowerShell.

- Para ativar o armazenamento de registos de diagnóstico numa conta de armazenamento, utilize este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   O ID de conta de armazenamento é o ID de recurso para a conta de armazenamento de destino.

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

Para oferecer suporte a várias subscrições, utilize o script do PowerShell da [registo de métricas de recurso de ativar o Azure com o PowerShell](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/).

Forneça o ID de recurso da área de trabalho \<$WSID\> como um parâmetro ao executar o script `Enable-AzureRMDiagnostics.ps1` para enviar dados de diagnóstico de vários recursos para a área de trabalho.

- Para obter o ID de área de trabalho \<$WSID\> do destino para os seus dados de diagnóstico, utilize o seguinte script:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```
   Substitua \<subID\> com o ID de subscrição \<RG_NAME\> com o nome do grupo de recursos, e \<WS_NAME\> com o nome de área de trabalho.

### <a name="azure-cli"></a>CLI do Azure

Pode ativar métricas e registo de diagnósticos com a CLI do Azure.

> [!NOTE]
> Scripts para ativar o registo de diagnóstico são suportadas para v1.0 da CLI do Azure. Tenha em atenção que a CLI v2.0 é suportada neste momento.

- Para ativar o armazenamento de registos de diagnóstico numa conta de armazenamento, utilize este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   O ID de conta de armazenamento é o ID de recurso para a conta de armazenamento de destino.

- Para ativar a transmissão em fluxo de registos de diagnóstico para um hub de eventos, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   O ID da regra do Service Bus é uma cadeia de caracteres com este formato:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Para ativar o envio de registos de diagnóstico para uma área de trabalho do Log Analytics, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Pode combinar estes parâmetros para ativar várias opções de saída.

### <a name="rest-api"></a>API REST

Saiba mais sobre como [alterar as definições de diagnóstico, utilizando a API de REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

### <a name="resource-manager-template"></a>Modelo do Resource Manager

Saiba mais sobre como [ative as definições de diagnóstico durante a criação de recursos com um modelo do Resource Manager](../azure-monitor/platform/diagnostic-logs-stream-template.md).

## <a name="stream-into-azure-sql-analytics"></a>Stream em análise SQL do Azure

Análise de SQL do Azure é uma solução de cloud que monitoriza o desempenho de bases de dados SQL do Azure, conjuntos elásticos e as instâncias geridas em escala e em várias subscrições. Ele pode ajudá-lo a recolher e visualizar métricas de desempenho da base de dados do Azure SQL e possui inteligência incorporada para resolução de problemas de desempenho.

![Descrição geral da análise SQL do Azure](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

Registos de diagnóstico e métricas de base de dados SQL podem ser transmitidos em análise de SQL do Azure utilizando o incorporado **enviar para o Log Analytics** opção no separador de definições de diagnóstico no portal. Também pode ativar o log analytics utilizando uma definição de diagnóstico através de cmdlets do PowerShell, a CLI do Azure ou a API de REST do Azure Monitor.

### <a name="installation-overview"></a>Visão geral da instalação

Pode monitorar uma frota de base de dados SQL com a análise de SQL do Azure. Execute os seguintes passos:

1. Crie uma solução de análise de SQL do Azure no Azure Marketplace.
2. Crie uma área de trabalho de monitorização na solução.
3. Configure bases de dados de telemetria de diagnóstico de fluxo para a área de trabalho.

Se estiver a utilizar os conjuntos elásticos ou instâncias geridas, terá também de configurar a telemetria de diagnóstico de transmissão em fluxo a partir desses recursos.

### <a name="create-azure-sql-analytics-resource"></a>Criar recurso de análise de SQL do Azure

1. Procure a análise de SQL do Azure no Azure Marketplace e selecioná-lo.

   ![Procure a análise de SQL do Azure no portal](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Selecione **criar** no ecrã de descrição geral da solução.

3. Preencha o formulário de análise de SQL do Azure com as informações adicionais, que é necessários: nome de área de trabalho, subscrição, grupo de recursos, localização e escalão de preço.

   ![Configure a análise de SQL do Azure no portal](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Selecione **OK** para confirmar e, em seguida, selecione **criar**.

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Configurar bases de dados aos registos de diagnóstico e métricas de registo

A forma mais fácil de configurar em que a métrica de registo de bases de dados é através do portal do Azure. Conforme descrito anteriormente, aceda ao seu recurso de base de dados SQL no portal do Azure e selecione **as definições de diagnóstico**.

Se estiver a utilizar os conjuntos elásticos ou instâncias geridas, terá também de configurar as definições de diagnóstico nestes recursos para ativar a telemetria de diagnóstico para transmitir em fluxo para a área de trabalho.

### <a name="use-the-sql-analytics-solution"></a>Utilizar a solução de análise de SQL

Pode utilizar a análise de SQL como um dashboard hierárquico para ver os recursos de base de dados SQL. Para saber como utilizar a solução de análise de SQL, veja [monitorizar a base de dados de SQL com a solução de análise de SQL](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Transmitir em fluxo para os Hubs de Eventos

Pode transmitir em fluxo registos de diagnóstico e métricas de base de dados SQL para os Hubs de eventos utilizando o incorporado **Stream para um hub de eventos** opção no portal do Azure. Também pode ativar o ID da regra do Service Bus utilizando uma definição de diagnóstico através de cmdlets do PowerShell, a CLI do Azure ou a API de REST do Azure Monitor.

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>O que fazer com métricas e diagnósticos registos nos Hubs de eventos

Depois dos dados selecionados são transmitidos em Hubs de eventos, está um passo mais perto para ativar cenários de monitorização avançada. Os Event Hubs atuam como a porta de entrada para um pipeline de eventos. Depois de dados são recolhidos para um hub de eventos, podem ser transformado e armazenado através de um fornecedor de análise em tempo real ou um adaptador de armazenamento. Os Hubs de eventos desassocia a produção de um fluxo de eventos do consumo desses eventos. Dessa forma, os consumidores de eventos podem acessar os eventos no seu próprio agendamento. Para obter mais informações sobre os Hubs de eventos, consulte:

- [Quais são os Hubs de eventos do Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Introdução ao Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Pode utilizar métricas de transmissão em fluxo contínuo nos Hubs de eventos para:

- **Ver o estado de funcionamento do serviço através da transmissão em fluxo de dados de caminhos recorrentes para o Power BI**. Ao utilizar os Hubs de eventos, o Stream Analytics e o Power BI, pode facilmente transformar os dados de métricas e diagnósticos para perto de informações em tempo real nos seus serviços do Azure. Para uma descrição geral de como configurar um hub de eventos, o processamento de dados com o Stream Analytics e utilize o Power BI como resultado, consulte [Stream Analytics e o Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Stream registos para fluxos de telemetria e de registo de terceiros**. Ao utilizar os Hubs de eventos de transmissão em fluxo, pode obter os registos de métricas e diagnósticos em várias soluções de análise de monitorização e registo de terceiros.

- **Criar uma plataforma de Registro em log e telemetria personalizada**. Fazer já tiver uma plataforma de telemetria personalizados internos ou estão pensando em criar uma? Altamente dimensionável de publicação-subscrição natureza dos Hubs de eventos permite ingerir forma flexível os registos de diagnóstico. Ver [guia de Dan Rosanova para utilizar os Hubs de eventos numa plataforma de telemetria de escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Stream para o armazenamento

Pode armazenar métricas de base de dados SQL e os registos de diagnósticos no armazenamento do Azure utilizando o incorporado **arquivo para uma conta de armazenamento** opção no portal do Azure. Também pode ativar o armazenamento utilizando uma definição de diagnóstico através de cmdlets do PowerShell, a CLI do Azure ou a API de REST do Azure Monitor.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Esquema de métricas e diagnósticos registos na conta de armazenamento

Depois de configurar a coleção de registos de diagnóstico e métricas, é criado um contentor de armazenamento na conta de armazenamento que selecionou quando as primeiras linhas de dados estão disponíveis. A estrutura dos blobs é:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ou, mais simples:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por exemplo, pode ser um nome de blob para todas as métricas:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Um nome de blob para o armazenamento de dados a partir de um conjunto elástico é semelhante a:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Transferir os registos e métricas do armazenamento

Saiba como [transferir registos de diagnóstico e métricas do armazenamento](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).

## <a name="data-retention-policy-and-pricing"></a>Política de retenção de dados e preços

Se selecionar os Hubs de eventos ou uma conta de armazenamento, pode especificar uma política de retenção. Esta política elimina os dados que é mais antigos que um período de tempo selecionado. Se especificar o Log Analytics, a política de retenção depende do escalão de preço selecionado. Neste caso, as unidades gratuitas fornecidas de ingestão de dados, podem ativar gratuita monitorização de vários bancos de dados por mês. O consumo de telemetria de diagnóstico em excesso relativamente às unidades gratuitas pode implicar custos. Lembre-se de que as bases de dados ativas com cargas de trabalho mais pesadas ingerir mais dados do que as bases de dados inativos. Para obter mais informações, consulte [preços do Log analytics](https://azure.microsoft.com/pricing/details/monitor/).

Se estiver a utilizar a análise de SQL do Azure, pode monitorizar o consumo de ingestão de dados na solução selecionando **área de trabalho do OMS** no menu de navegação de análise de SQL do Azure e, em seguida, selecionar **utilização** e **os custos estimados**.

## <a name="metrics-and-logs-available"></a>Métricas e registos disponíveis

Telemetria de monitorização recolhida pode ser utilizada pelos seus próprios _análise personalizada_ e _desenvolvimento de aplicativos_ usando [idioma de análise de SQL](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries).

## <a name="all-metrics"></a>Todas as métricas

Consulte as tabelas seguintes para obter detalhes sobre todas as métricas por recurso.

### <a name="all-metrics-for-elastic-pools"></a>Todas as métricas para conjuntos elásticos

|**Recurso**|**Métricas**|
|---|---|
|Conjunto elástico|percentagem de eDTU, eDTU utilizado, o limite de eDTU, percentagem de CPU, percentagem de leitura de dados físicos, log escrever percentagem, percentagem de sessões, percentagem de funções de trabalho, armazenamento, percentagem de armazenamento, limite de armazenamento, percentagem de armazenamento do XTP |

### <a name="all-metrics-for-azure-sql-databases"></a>Todas as métricas para bases de dados do Azure SQL

|**Recurso**|**Métricas**|
|---|---|
|Base de dados SQL do Azure|Percentagem de DTU, DTU utilizado, o limite DTU, percentagem de CPU, percentagem de leitura de dados físicos, de registo de escrita em percentagem, com êxito/com falhas/bloqueado por ligações de firewall, percentagem de sessões, percentagem de funções de trabalho, armazenamento, percentagem de armazenamento, percentagem de armazenamento do XTP, e deadlocks |

## <a name="all-logs"></a>Todos os registos

Detalhes de telemetria disponível para todos os registos são divulgadas em tabelas abaixo. Veja [suportado registo de diagnósticos](#supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases) para compreender os registos que são suportados para um tipo de base de dados específica - SQL do Azure única, agrupada, ou instância de base de dados.

### <a name="resource-usage-stats-for-managed-instance"></a>Estatísticas de utilização de recursos para a instância gerida

|Propriedade|Descrição|
|---|---|
|TenantId|O ID de inquilino |
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de hora quando o registo foi registado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: ResourceUsageStats |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: MANAGEDINSTANCES |
|SubscriptionId|GUID da subscrição para a base de dados |
|ResourceGroup|Nome do grupo de recursos para a base de dados |
|LogicalServerName_s|Nome da instância gerida |
|ResourceId|URI do recurso |
|SKU_s|SKU do produto de instância gerida |
|virtual_core_count_s|Número de vCores disponíveis |
|avg_cpu_percent_s|Percentagem de CPU média |
|reserved_storage_mb_s|Capacidade de armazenamento reservada na instância gerida |
|storage_space_used_mb_s|Armazenamento utilizado na instância gerida |
|io_requests_s|Contagem IOPS |
|io_bytes_read_s|Bytes IOPS de leitura |
|io_bytes_written_s|Bytes IOPS escritos |

### <a name="query-store-runtime-statistics"></a>Estatísticas de tempo de execução de consulta Store

|Propriedade|Descrição|
|---|---|
|TenantId|O ID de inquilino |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de hora quando o registo foi registado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: QueryStoreRuntimeStatistics |
|OperationName|Nome da operação. Sempre: QueryStoreRuntimeStatisticsEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID da subscrição para a base de dados |
|ResourceGroup|Nome do grupo de recursos para a base de dados |
|LogicalServerName_s|Nome do servidor da base de dados |
|ElasticPoolName_s|Nome do conjunto elástico da base de dados, se aplicável |
|DatabaseName_s|Nome da base de dados |
|ResourceId|URI do recurso |
|query_hash_s|Hash de consulta |
|query_plan_hash_s|Hash de plano de consulta |
|statement_sql_handle_s|Identificador de sql de declaração |
|interval_start_time_d|Iniciar o datetimeoffset do intervalo de no número de tiques de 1900-1-1 |
|interval_end_time_d|Datetimeoffset de fim do intervalo no número de tiques de 1900-1-1 |
|logical_io_writes_d|Número total de escritas de e/s lógicas |
|max_logical_io_writes_d|Número máx. de e/s lógica escritas por execução |
|physical_io_reads_d|Número total de leituras de e/s físicas |
|max_physical_io_reads_d|Número máx. de e/s lógica leituras por execução |
|logical_io_reads_d|Número total de leituras lógicas de e/s |
|max_logical_io_reads_d|Número máx. de e/s lógica leituras por execução |
|execution_type_d|Tipo de execução |
|count_executions_d|Número de execuções da consulta |
|cpu_time_d|Tempo total de CPU consumido pela consulta em microssegundos |
|max_cpu_time_d|Consumidor de tempo máx. da CPU por uma única execução em microssegundos |
|dop_d|Soma de graus de paralelismo |
|max_dop_d|Grau máximo de paralelismo utilizado para execução única |
|rowcount_d|Número total de linhas devolvidas |
|max_rowcount_d|Número máx. de linhas retornadas na execução única |
|query_max_used_memory_d|Quantidade total de memória utilizada no artigo BDC |
|max_query_max_used_memory_d|Quantidade máxima de memória utilizada por uma única execução no artigo BDC |
|duration_d|Tempo de execução total em microssegundos |
|max_duration_d|Máx. tempo de execução de uma única execução |
|num_physical_io_reads_d|Número total de leituras físicos |
|max_num_physical_io_reads_d|Número máx. de leituras físicos por execução |
|log_bytes_used_d|Quantidade total de bytes de registo utilizados |
|max_log_bytes_used_d|Quantidade máxima de bytes de registo utilizados por execução |
|query_id_d|ID da consulta no Query Store |
|plan_id_d|ID do plano de consulta Store |

Saiba mais sobre [dados de estatísticas de tempo de execução de consulta Store](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Estatísticas de espera de Store de consulta

|Propriedade|Descrição|
|---|---|
|TenantId|O ID de inquilino |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de hora quando o registo foi registado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: QueryStoreWaitStatistics |
|OperationName|Nome da operação. Sempre: QueryStoreWaitStatisticsEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID da subscrição para a base de dados |
|ResourceGroup|Nome do grupo de recursos para a base de dados |
|LogicalServerName_s|Nome do servidor da base de dados |
|ElasticPoolName_s|Nome do conjunto elástico da base de dados, se aplicável |
|DatabaseName_s|Nome da base de dados |
|ResourceId|URI do recurso |
|wait_category_s|Categoria da espera |
|is_parameterizable_s|A consulta é parametrizável |
|statement_type_s|Tipo de instrução |
|statement_key_hash_s|Hash de chave de instrução |
|exec_type_d|Tipo de execução |
|total_query_wait_time_ms_d|Tempo de espera total da consulta na categoria específica de espera |
|max_query_wait_time_ms_d|Tempo de espera máximo da consulta em execução individual na categoria específica de espera |
|query_param_type_d|0 |
|query_hash_s|Hash de consulta no Query Store |
|query_plan_hash_s|Hash de plano de consulta no Query Store |
|statement_sql_handle_s|Identificador de instrução de consulta Store |
|interval_start_time_d|Iniciar o datetimeoffset do intervalo de no número de tiques de 1900-1-1 |
|interval_end_time_d|Datetimeoffset de fim do intervalo no número de tiques de 1900-1-1 |
|count_executions_d|Contagem de execuções da consulta |
|query_id_d|ID da consulta no Query Store |
|plan_id_d|ID do plano de consulta Store |

Saiba mais sobre [dados de estatísticas de espera de consulta Store](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Conjunto de dados de erros

|Propriedade|Descrição|
|---|---|
|TenantId|O ID de inquilino |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de hora quando o registo foi registado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT.SQ |
|Categoria|Nome da categoria. Sempre: Erros |
|OperationName|Nome da operação. Sempre: ErrorEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID da subscrição para a base de dados |
|ResourceGroup|Nome do grupo de recursos para a base de dados |
|LogicalServerName_s|Nome do servidor da base de dados |
|ElasticPoolName_s|Nome do conjunto elástico da base de dados, se aplicável |
|DatabaseName_s|Nome da base de dados |
|ResourceId|URI do recurso |
|Mensagem|Mensagem de erro em texto simples |
|user_defined_b|É o bit de definidas pelo utilizador do erro |
|error_number_d|Código de erro |
|Gravidade|Gravidade do erro |
|state_d|Estado do erro |
|query_hash_s|Hash de consulta da consulta com falhas, se disponível |
|query_plan_hash_s|Hash de plano de consulta da consulta com falhas, se disponível |

Saiba mais sobre [mensagens de erro do SQL Server](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Conjunto de dados de estatísticas de espera de base de dados

|Propriedade|Descrição|
|---|---|
|TenantId|O ID de inquilino |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de hora quando o registo foi registado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: DatabaseWaitStatistics |
|OperationName|Nome da operação. Sempre: DatabaseWaitStatisticsEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID da subscrição para a base de dados |
|ResourceGroup|Nome do grupo de recursos para a base de dados |
|LogicalServerName_s|Nome do servidor da base de dados |
|ElasticPoolName_s|Nome do conjunto elástico da base de dados, se aplicável |
|DatabaseName_s|Nome da base de dados |
|ResourceId|URI do recurso |
|wait_type_s|Nome do tipo de espera |
|start_utc_date_t [UTC]|Hora de início do período de medida |
|end_utc_date_t [UTC]|Hora de fim do período de medida |
|delta_max_wait_time_ms_d|Tempo por execução de espera máximo |
|delta_signal_wait_time_ms_d|Tempo de espera de sinais total |
|delta_wait_time_ms_d|Tempo de espera total no período |
|delta_waiting_tasks_count_d|Número de tarefas de espera |

Saiba mais sobre [estatísticas de espera de base de dados](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Conjunto de dados de tempos limite

|Propriedade|Descrição|
|---|---|
|TenantId|O ID de inquilino |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de hora quando o registo foi registado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: Tempos limite |
|OperationName|Nome da operação. Sempre: TimeoutEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID da subscrição para a base de dados |
|ResourceGroup|Nome do grupo de recursos para a base de dados |
|LogicalServerName_s|Nome do servidor da base de dados |
|ElasticPoolName_s|Nome do conjunto elástico da base de dados, se aplicável |
|DatabaseName_s|Nome da base de dados |
|ResourceId|URI do recurso |
|error_state_d|Código de estado de erro |
|query_hash_s|Consultar o hash, se disponível |
|query_plan_hash_s|Consultar o hash do plano, se disponível |

### <a name="blockings-dataset"></a>Blockings conjunto de dados

|Propriedade|Descrição|
|---|---|
|TenantId|O ID de inquilino |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de hora quando o registo foi registado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: blocos |
|OperationName|Nome da operação. Sempre: BlockEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID da subscrição para a base de dados |
|ResourceGroup|Nome do grupo de recursos para a base de dados |
|LogicalServerName_s|Nome do servidor da base de dados |
|ElasticPoolName_s|Nome do conjunto elástico da base de dados, se aplicável |
|DatabaseName_s|Nome da base de dados |
|ResourceId|URI do recurso |
|lock_mode_s|Modo de bloqueio usado pela consulta |
|resource_owner_type_s|Proprietário do bloqueio |
|blocked_process_filtered_s|Bloqueado o processo de relatório XML |
|duration_d|Duração do bloqueio em microssegundos |

### <a name="deadlocks-dataset"></a>Conjunto de dados de deadlocks

|Propriedade|Descrição|
|---|---|
|TenantId|O ID de inquilino |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC] |Carimbo de hora quando o registo foi registado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: Impasses |
|OperationName|Nome da operação. Sempre: DeadlockEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID da subscrição para a base de dados |
|ResourceGroup|Nome do grupo de recursos para a base de dados |
|LogicalServerName_s|Nome do servidor da base de dados |
|ElasticPoolName_s|Nome do conjunto elástico da base de dados, se aplicável |
|DatabaseName_s|Nome da base de dados |
|ResourceId|URI do recurso |
|deadlock_xml_s|Relatório de deadlock XML |

### <a name="automatic-tuning-dataset"></a>Conjunto de dados de otimização automática

|Propriedade|Descrição|
|---|---|
|TenantId|O ID de inquilino |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de hora quando o registo foi registado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: AutomaticTuning |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID da subscrição para a base de dados |
|ResourceGroup|Nome do grupo de recursos para a base de dados |
|LogicalServerName_s|Nome do servidor da base de dados |
|LogicalDatabaseName_s|Nome da base de dados |
|ElasticPoolName_s|Nome do conjunto elástico da base de dados, se aplicável |
|DatabaseName_s|Nome da base de dados |
|ResourceId|URI do recurso |
|RecommendationHash_s|Valor hash exclusivo de recomendação de otimização automática |
|OptionName_s|Operação de otimização automática |
|Schema_s|Esquema de base de dados |
|Table_s|Tabela afetada |
|IndexName_s|Nome do índice |
|IndexColumns_s|Nome da coluna |
|IncludedColumns_s|Colunas incluídas |
|EstimatedImpact_s|Estimado o impacto da recomendação de otimização automática JSON |
|Event_s|Tipo de evento de otimização automática |
|Timestamp_t|Última timestamp atualizado |

### <a name="intelligent-insights-dataset"></a>Conjunto de dados de informações inteligente

Saiba mais sobre o [formato de registo de informações inteligentes](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Passos Seguintes

Para saber como ativar o registo e compreender as métricas e registo categorias suportadas por vários serviços do Azure, veja:

- [Descrição geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Descrição geral dos registos de diagnóstico do Azure](../azure-monitor/platform/diagnostic-logs-overview.md)

Para saber mais sobre os Hubs de eventos, leia:

- [O que é o Event Hubs do Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Introdução ao Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Para saber mais sobre o armazenamento do Azure, veja [como transferir os registos de diagnóstico e métricas do armazenamento](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).
