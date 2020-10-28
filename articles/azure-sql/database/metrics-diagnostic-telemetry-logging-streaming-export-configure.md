---
title: Configure a exportação de métricas e registos de recursos
description: Saiba como configurar a exportação de métricas e registos de recursos, incluindo análise inteligente de diagnóstico da Base de Dados Azure SQL e da Azure SQL Managed Instance para o destino da sua escolha para armazenar informações sobre a utilização de recursos e estatísticas de execução de consultas.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: seoapril2019
ms.devlang: sqldbrb=2
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: jrasnik, sstein
ms.date: 04/06/2020
ms.openlocfilehash: b1e1de694b6333a350d034b08225aeea117ae703
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790479"
---
# <a name="configure-streaming-export-of-azure-sql-database-and-sql-managed-instance-diagnostic-telemetry"></a>Configure exportação de streaming de Azure SQL Database e SQL Managed Instance telemetria de diagnóstico
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Neste artigo, você vai aprender sobre as métricas de desempenho e registos de recursos para Azure SQL Database que você pode exportar para um de vários destinos para análise. Você aprenderá a configurar a exportação de streaming desta telemetria de diagnóstico através do portal Azure, PowerShell, Azure CLI, o REST API e os modelos Azure Resource Manager.

Você também vai aprender sobre os destinos para os quais você pode transmitir esta telemetria de diagnóstico e como escolher entre estas escolhas. As suas opções de destino incluem:

- [Log Analytics e SQL Analytics](#stream-into-sql-analytics)
- [Hubs de Eventos](#stream-into-event-hubs)
- [Armazenamento do Azure](#stream-into-azure-storage)

## <a name="diagnostic-telemetry-for-export"></a>Telemetria de diagnóstico para exportação

O mais importante entre a telemetria de diagnóstico que pode exportar é o registo Intelligent Insights (SQLInsights). [A Intelligent Insights](intelligent-insights-overview.md) usa inteligência incorporada para monitorizar continuamente o uso da base de dados através da inteligência artificial e detetar eventos disruptivos que causam um fraco desempenho. Uma vez detetado, é realizada uma análise detalhada que gera um registo de Insights Inteligentes com uma avaliação inteligente do problema. Esta avaliação consiste numa análise de causa-raiz da questão do desempenho da base de dados e, sempre que possível, de recomendações para melhorias de desempenho. É necessário configurar a exportação de streaming deste registo para ver o seu conteúdo.

Além de transmitir a exportação do registo De Insights Inteligentes, também pode exportar uma variedade de métricas de desempenho e registos de bases de dados adicionais. A tabela seguinte descreve as métricas de desempenho e registos de recursos que pode configurar para o streaming de exportação para um dos vários destinos. Esta telemetria de diagnóstico pode ser configurada para bases de dados individuais, piscinas elásticas e bases de dados agrárias, e casos geridos e bases de dados de casos.

| Telemetria de diagnóstico para bases de dados | Suporte à base de dados Azure SQL | Suporte de Instância Gerida do SQL do Azure |
| :------------------- | ----- | ----- |
| [Métricas básicas](#basic-metrics): Contém percentagem de DTU/CPU, limite de DTU/CPU, percentagem de leitura de dados físicos, percentagem de registo, Sucesso/Falhado/Bloqueado por ligações de firewall, percentagem de sessões, percentagem de trabalhadores, armazenamento, percentagem de armazenamento e percentagem de armazenamento de XTP. | Sim | Não |
| [Instância e App Advanced](#advanced-metrics): Contém dados de base de dados do sistema temporário e registo do tamanho do ficheiro e ficheiro de registo temporário por cento utilizado. | Sim | Não |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): Contém informações sobre as estatísticas de tempo de consulta, tais como estatísticas de utilização de CPU e duração de consulta. | Sim | Sim |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): Contém informações sobre as estatísticas de espera de consulta (em que as suas consultas aguardavam) tais como CPU, LOG e LOCKING. | Sim | Sim |
| [Erros](#errors-dataset): Contém informações sobre erros SQL numa base de dados. | Sim | Sim |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): Contém informações sobre quanto tempo uma base de dados passou à espera de diferentes tipos de espera. | Sim | Não |
| [Intervalos de](#time-outs-dataset)tempo : Contém informações sobre intervalos de tempo numa base de dados. | Sim | Não |
| [Blocos](#blockings-dataset): Contém informações sobre o bloqueio de eventos numa base de dados. | Sim | Não |
| [Impasses](#deadlocks-dataset): Contém informações sobre eventos de impasse numa base de dados. | Sim | Não |
| [Sintonização Automática](#automatic-tuning-dataset): Contém informações sobre recomendações de sintonização automática para uma base de dados. | Sim | Não |
| [SQLInsights](#intelligent-insights-dataset): Contém insights inteligentes sobre o desempenho de uma base de dados. Para saber mais, consulte [Insights Inteligentes.](intelligent-insights-overview.md) | Sim | Sim |

> [!NOTE]
> As definições de diagnóstico não podem ser configuradas para as **bases de dados** do sistema, tais são bases de dados master, msdb, modelo, recursos e bases de dados temporárias.

## <a name="streaming-export-destinations"></a>Destinos de exportação de streaming

Esta telemetria de diagnóstico pode ser transmitida para um dos seguintes recursos Azure para análise.

- **[Log Analytics espaço de trabalho:](#stream-into-sql-analytics)**

  Os dados transmitidos para um [espaço de trabalho Log Analytics](../../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace) podem ser consumidos pela [SQL Analytics](../../azure-monitor/insights/azure-sql.md). O SQL Analytics é uma solução de monitorização apenas em nuvem que fornece uma monitorização inteligente das suas bases de dados que inclui relatórios de desempenho, alertas e recomendações de mitigação. Os dados transmitidos para um espaço de trabalho do Log Analytics podem ser analisados com outros dados de monitorização recolhidos e também permite-lhe alavancar outras funcionalidades do Azure Monitor, tais como alertas e visualizações
- **[Hubs de Eventos Azure:](#stream-into-event-hubs)**

  Os dados transmitidos para um [Azure Event Hub](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs)fornecem a seguinte funcionalidade:

  - **Fluxo de registos para sistemas de registo e telemetria** de terceiros : Transmita todas as suas métricas e registos de recursos para um único centro de eventos para obter dados de registo de tubos para uma ferramenta siem de terceiros ou ferramenta de análise de registo.
  - **Construa uma plataforma de telemetria e registo personalizado** : A natureza altamente escalável de publicação-subscrição dos centros de eventos permite-lhe ingerir métricas e registos de recursos de forma flexível numa plataforma de telemetria personalizada. Consulte [Designing e Dimensionamento de uma Plataforma global de Telemetria à Escala Global nos Hubs de Eventos Azure](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) para mais detalhes.
  - **Ver saúde do serviço através do streaming de dados para Power BI** : Use Event Hubs, Stream Analytics e Power BI para transformar os seus dados de diagnóstico em insights quase em tempo real nos seus serviços Azure. Consulte [Stream Analytics e Power BI: Um dashboard de análise em tempo real para transmitir dados](../../stream-analytics/stream-analytics-power-bi-dashboard.md) para obter detalhes sobre esta solução.
- **[Armazenamento Azure:](#stream-into-azure-storage)**

  Os dados transmitidos para [o Azure Storage](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage) permitem-lhe arquivar grandes quantidades de telemetria de diagnóstico por uma fração do custo das duas opções de streaming anteriores.

Esta telemetria de diagnóstico transmitida para um destes destinos pode ser usada para medir as estatísticas de utilização de recursos e de execução de consultas para facilitar a monitorização do desempenho.

![O diagrama mostra muitas bases de dados e bases de dados SQL em instâncias geridas que enviam telemetria para Azure Diagnostics, que reencaminha informações para Azure SQL Analytics, Event Hub e armazenamento.](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/architecture.png)

## <a name="enable-and-configure-the-streaming-export-of-diagnostic-telemetry"></a>Permitir e configurar a exportação em streaming de telemetria de diagnóstico

Pode ativar e gerir as métricas e os registos de telemetria de diagnóstico com um dos seguintes métodos:

- Portal do Azure
- PowerShell
- CLI do Azure
- API REST do Azure Monitor
- Modelo Azure Resource Manager

> [!NOTE]
> Para permitir o streaming de registos de auditoria de telemetria de segurança, consulte [configurar a auditoria para a sua base de dados](./auditing-overview.md#setup-auditing) e [auditar registos nos registos do Azure Monitor e nos Hubs de Eventos Azure](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

## <a name="configure-the-streaming-export-of-diagnostic-telemetry"></a>Configure a exportação em streaming de telemetria de diagnóstico

Pode utilizar o menu **de definições de Diagnóstico** no portal Azure para ativar e configurar o streaming de telemetria de diagnóstico. Além disso, pode utilizar o PowerShell, o Azure CLI, o [REST API](/rest/api/monitor/diagnosticsettings)e [os modelos Resource Manager](../../azure-monitor/samples/resource-manager-diagnostic-settings.md) para configurar o streaming de telemetria de diagnóstico. Pode definir os seguintes destinos para transmitir a telemetria de diagnóstico: Azure Storage, Azure Event Hubs e registos Azure Monitor.

> [!IMPORTANT]
> A exportação em streaming de telemetria de diagnóstico não é ativada por defeito.

Selecione um dos seguintes separadores para orientação passo a passo para configurar a exportação de telemetria de diagnóstico no portal Azure e para scripts para realizar o mesmo com o PowerShell e o Azure CLI.

# <a name="azure-portal"></a>[Portal do Azure](#tab/azure-portal)

### <a name="elastic-pools-in-azure-sql-database"></a>Piscinas elásticas em Azure SQL Database

Pode configurar um recurso elástico de piscina para recolher a seguinte telemetria de diagnóstico:

| Recurso | Telemetria de monitorização |
| :------------------- | ------------------- |
| **Conjunto elástico** | [As métricas básicas](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics) contêm percentagem de eDTU/CPU, limite de eDTU/CPU, percentagem de leitura de dados físicos, percentagem de registo, percentagem de sessões, percentagem de trabalhadores, armazenamento, percentagem de armazenamento e percentagem de armazenamento de XTP. |

Para configurar o streaming de telemetria de diagnóstico para piscinas elásticas e bases de dados em conjunto, é necessário configurar separadamente cada uma separadamente:

- Permitir o streaming de telemetria de diagnóstico para uma piscina elástica
- Ativar o streaming de telemetria de diagnóstico para cada base de dados em piscina elástica

O recipiente de piscina elástica tem a sua própria telemetria separada da telemetria de cada base de dados.

Para permitir o streaming de telemetria de diagnóstico para um recurso elástico de piscina, siga estes passos:

1. Aceda ao recurso **elástico** da piscina no portal Azure.
2. Selecione **definições de Diagnóstico** .
3. Selecione **Ligue os diagnósticos** se não existirem definições anteriores ou selecione **a definição de Edição** para editar uma definição anterior.

   ![Permitir diagnósticos para piscinas elásticas](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-enable.png)

4. Introduza um nome de definição para a sua própria referência.
5. Selecione um recurso de destino para os dados de diagnóstico de streaming: **Arquivar para conta de armazenamento,** transmitir para um centro de **eventos,** ou **enviar para registar analytics** .
6. Para análise de registos, selecione **Configure** e crie um novo espaço de trabalho selecionando **+Create New Workspace,** ou selecione um espaço de trabalho existente.
7. Selecione a caixa de verificação para telemetria de diagnóstico de piscina elástica: **métricas básicas.**
   ![Configurar diagnósticos para piscinas elásticas](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-selection.png)

8. Selecione **Guardar** .
9. Além disso, configurar o streaming de telemetria de diagnóstico para cada base de dados dentro da piscina elástica que pretende monitorizar seguindo os passos descritos na secção seguinte.

> [!IMPORTANT]
> Além de configurar a telemetria de diagnóstico para uma piscina elástica, também é necessário configurar a telemetria de diagnóstico para cada base de dados na piscina elástica.

### <a name="databases-in-azure-sql-database"></a>Bases de dados em Azure SQL Database

Pode configurar um recurso de base de dados para recolher a seguinte telemetria de diagnóstico:

| Recurso | Telemetria de monitorização |
| :------------------- | ------------------- |
| **Base de dados única ou agrizada** | [As métricas básicas](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics) contêm percentagem de DTU, DTU usado, limite de DTU, percentagem de CPU, percentagem de leitura de dados físicos, percentagem de registo, Sucesso/Falhado/Bloqueado por ligações de firewall, percentagem de sessões, percentagem de trabalhadores, armazenamento, percentagem de armazenamento, percentagem de armazenamento de XTP e impasses. |

Para permitir o streaming de telemetria de diagnóstico para uma base de dados única ou agampenada, siga estes passos:

1. Aceda ao recurso de base de dados Azure **SQL.**
2. Selecione **definições de Diagnóstico** .
3. Selecione **Ligue os diagnósticos** se não existirem definições anteriores ou selecione **a definição de Edição** para editar uma definição anterior. Pode criar até três ligações paralelas para transmitir telemetria de diagnóstico.
4. **Selecione Adicionar a definição** de diagnóstico para configurar a transmissão paralela de dados de diagnóstico a múltiplos recursos.

   ![Permitir diagnósticos para bases de dados individuais e agrizadas](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-enable.png)

5. Introduza um nome de definição para a sua própria referência.
6. Selecione um recurso de destino para os dados de diagnóstico de streaming: **Arquivar para conta de armazenamento,** transmitir para um centro de **eventos,** ou **enviar para registar analytics** .
7. Para a experiência de monitorização padrão baseada em eventos, selecione as seguintes caixas de verificação para telemetria de diagnóstico de base de **dados: SQLInsights,** **AutomaticTuning,** **QueryStoreRuntimeStatistics** , **QueryStoreWaitStatistics,** **Errors,** **DatabaseWaitStatistics,** **Timeouts,** **Blocks** e **Deadlocks** .
8. Para uma experiência de monitorização avançada e baseada em um minuto, selecione a caixa de verificação para métricas **básicas.**

   ![Configurar diagnósticos para Azure SQL Database](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-selection.png)
9. Selecione **Guardar** .
10. Repita estes passos para cada base de dados que pretende monitorizar.

> [!TIP]
> Repita estes passos para cada base de dados única e agrizada que pretende monitorizar.

### <a name="instances-in-azure-sql-managed-instance"></a>Instâncias em Azure SQL Caso Gerido

Pode configurar um recurso de instância gerido para recolher a seguinte telemetria de diagnóstico:

| Recurso | Telemetria de monitorização |
| :------------------- | ------------------- |
| **Instância gerida** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) contém contagem de vCores, percentagem média de CPU, pedidos de IO, bytes lidos/escritos, espaço de armazenamento reservado e espaço de armazenamento usado. |

Para configurar o streaming de telemetria de diagnóstico para bases de dados de casos geridos e casos, terá de configurar separadamente cada uma delas:

- Permitir o streaming de telemetria de diagnóstico para instância gerida
- Permitir o streaming de telemetria de diagnóstico para cada base de dados de instâncias

O contentor de instância gerida tem a sua própria telemetria separada da telemetria de cada instância.

Para permitir o streaming de telemetria de diagnóstico para um recurso de exemplo gerido, siga estes passos:

1. Aceda ao recurso **de exemplo gerido** no portal Azure.
2. Selecione **definições de Diagnóstico** .
3. Selecione **Ligue os diagnósticos** se não existirem definições anteriores ou selecione **a definição de Edição** para editar uma definição anterior.

   ![Permitir diagnósticos para instâncias geridas](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-enable.png)

4. Introduza um nome de definição para a sua própria referência.
5. Selecione um recurso de destino para os dados de diagnóstico de streaming: **Arquivar para conta de armazenamento,** transmitir para um centro de **eventos,** ou **enviar para registar analytics** .
6. Para análise de registos, selecione **Configure** e crie um novo espaço de trabalho selecionando **+Create New Workspace,** ou use um espaço de trabalho existente.
7. Selecione a caixa de verificação, por exemplo, telemetria de diagnóstico: **ResourceUsageStats** .

   ![Configurar diagnósticos para instância gerida](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-selection.png)

8. Selecione **Guardar** .
9. Além disso, configurar o streaming de telemetria de diagnóstico para cada base de dados de instâncias dentro da instância gerida que pretende monitorizar seguindo os passos descritos na secção seguinte.

> [!IMPORTANT]
> Além de configurar a telemetria de diagnóstico para um caso gerido, também é necessário configurar a telemetria de diagnóstico para cada base de dados de cada instância.

### <a name="databases-in-azure-sql-managed-instance"></a>Bases de dados em Azure SQL Gestão De Instância

Pode configurar um recurso de base de dados de casos para recolher a seguinte telemetria de diagnóstico:

| Recurso | Telemetria de monitorização |
| :------------------- | ------------------- |
| **Base de dados de casos** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) contém contagem de vCores, percentagem média de CPU, pedidos de IO, bytes lidos/escritos, espaço de armazenamento reservado e espaço de armazenamento usado. |

Para permitir o streaming de telemetria de diagnóstico para uma base de dados de casos, siga estes passos:

1. Aceda a **recursos de base de dados** de caso dentro de instâncias geridas.
2. Selecione **definições de Diagnóstico** .
3. Selecione **Ligue os diagnósticos** se não existirem definições anteriores ou selecione **a definição de Edição** para editar uma definição anterior.
   - Pode criar até três (3) ligações paralelas para transmitir telemetria de diagnóstico.
   - **Selecione +Adicione** a definição de diagnóstico para configurar o streaming paralelo de dados de diagnóstico para vários recursos.

   ![Permitir diagnósticos por exemplo bases de dados](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-enable.png)

4. Introduza um nome de definição para a sua própria referência.
5. Selecione um recurso de destino para os dados de diagnóstico de streaming: **Arquivar para conta de armazenamento,** transmitir para um centro de **eventos,** ou **enviar para registar analytics** .
6. Selecione as caixas de verificação para telemetria de diagnóstico de base de **dados: SQLInsights** , **QueryStoreRuntimeStatistics** , **QueryStoreWaitStatistics** e **Errors** .
   ![Configurar diagnósticos para por exemplo bases de dados](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-selection.png)
7. Selecione **Guardar** .
8. Repita estes passos para cada base de dados de cada instância que pretende monitorizar.

> [!TIP]
> Repita estes passos para cada base de dados de cada instância que pretende monitorizar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Pode ativar métricas e diagnósticos de registo utilizando o PowerShell.

- Para permitir o armazenamento de métricas e registos de recursos numa conta de armazenamento, utilize este comando:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
  ```

  O ID da conta de armazenamento é o ID de recursos para a conta de armazenamento de destino.

- Para permitir o streaming de métricas e registos de recursos para um centro de eventos, utilize este comando:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
  ```

  O ID da regra do Azure Service Bus é uma cadeia de caracteres com este formato:

  ```powershell
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Para permitir o envio de métricas e registos de recursos para um espaço de trabalho Log Analytics, utilize este comando:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
  ```

- Para obter o ID do recurso da área de trabalho do Log Analytics, utilize o seguinte comando:

  ```powershell
  (Get-AzOperationalInsightsWorkspace).ResourceId
  ```

Pode combinar estes parâmetros para ativar várias opções de saída.

**Para configurar múltiplos recursos Azure**

Para suportar várias subscrições, utilize o script PowerShell do [Enable Azure resource metrics logging usando PowerShell](/archive/blogs/msoms/enable-azure-resource-metrics-logging-using-powershell).

Forneça o ID do recurso de espaço de trabalho \<$WSID\> como parâmetro ao executar o script para enviar `Enable-AzureRMDiagnostics.ps1` dados de diagnóstico de múltiplos recursos para o espaço de trabalho.

- Para obter o ID do espaço de trabalho \<$WSID\> do destino para os seus dados de diagnóstico, utilize o seguinte script:

  ```powershell
  $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
  .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
  ```

  \<subID\>Substitua-o pelo ID de subscrição, \<RG_NAME\> pelo nome do grupo de recursos e pelo nome do espaço de \<WS_NAME\> trabalho.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Pode ativar métricas e diagnósticos de registo utilizando o CLI Azure.

> [!IMPORTANT]
> Os scripts para permitir a sessão de diagnóstico são suportados para Azure CLI v1.0. Azure CLI v2.0 não está suportado neste momento.

- Para permitir o armazenamento de métricas e registos de recursos numa conta de armazenamento, utilize este comando:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
  ```

  O ID da conta de armazenamento é o ID de recursos para a conta de armazenamento de destino.

- Para permitir o streaming de métricas e registos de recursos para um centro de eventos, utilize este comando:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
  ```

  A regra do autocarro de serviço iD é uma cadeia com este formato:

  ```azurecli-interactive
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Para permitir o envio de métricas e registos de recursos para um espaço de trabalho Log Analytics, utilize este comando:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
  ```

Pode combinar estes parâmetros para ativar várias opções de saída.

---

## <a name="stream-into-sql-analytics"></a>Transmita para o SQL Analytics

Azure SQL Database e Azure SQL Managed Instance métricas e registos de recursos que são transmitidos para um espaço de trabalho Log Analytics podem ser consumidos pela Azure SQL Analytics. O Azure SQL Analytics é uma solução em nuvem que monitoriza o desempenho de bases de dados individuais, piscinas elásticas e bases de dados agalhadas, e gestão de casos e bases de dados de casos em escala e em várias subscrições. Pode ajudá-lo a recolher e visualizar métricas de desempenho, e tem inteligência incorporada para resolução de problemas de desempenho.

![Visão geral da Azure SQL Analytics](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/azure-sql-sol-overview.png)

### <a name="installation-overview"></a>Descrição geral da instalação

Pode monitorizar uma coleção de bases de dados e coleções de bases de dados com a Azure SQL Analytics, realizando os seguintes passos:

1. Crie uma solução Azure SQL Analytics a partir do Azure Marketplace.
2. Crie um espaço de trabalho Log Analytics na solução.
3. Configure bases de dados para transmitir telemetria de diagnóstico para o espaço de trabalho.

Pode configurar a exportação em streaming desta telemetria de diagnóstico utilizando a opção **de Envio para Log Analytics** incorporada no separador de definições de diagnóstico no portal Azure. Também pode ativar o streaming num espaço de trabalho do Log Analytics utilizando definições de diagnóstico através de [cmdlets PowerShell](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-powershell#configure-the-streaming-export-of-diagnostic-telemetry), O [CLI Azure,](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-cli#configure-the-streaming-export-of-diagnostic-telemetry)o [Azure Monitor REST API](/rest/api/monitor/diagnosticsettings), ou [modelos de Gestor de Recursos](../../azure-monitor/samples/resource-manager-diagnostic-settings.md).

### <a name="create-an-azure-sql-analytics-resource"></a>Criar um recurso Azure SQL Analytics

1. Procure a Azure SQL Analytics no Azure Marketplace e selecione-o.

   ![Pesquisa de Azure SQL Analytics no portal](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-in-marketplace.png)

2. **Selecione Criar** no ecrã geral da solução.

3. Preencha o formulário Azure SQL Analytics com as informações adicionais necessárias: nome do espaço de trabalho, subscrição, grupo de recursos, localização e nível de preços.

   ![Configure Azure SQL Analytics no portal](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-configuration-blade.png)

4. Selecione **OK** para confirmar e, em seguida, selecione **Criar** .

### <a name="configure-the-resource-to-record-metrics-and-resource-logs"></a>Configure o recurso para registar métricas e registos de recursos

É necessário configurar separadamente o streaming de telemetria de diagnóstico para bases de dados individuais e agimidas, piscinas elásticas, instâncias geridas e bases de dados de casos. A forma mais fácil de configurar onde um recurso regista métricas é usando o portal Azure. Para obter etapas detalhadas, consulte [configurar a exportação em streaming de telemetria de diagnóstico](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal#configure-the-streaming-export-of-diagnostic-telemetry).

### <a name="use-azure-sql-analytics-for-monitoring-and-alerting"></a>Utilize a Azure SQL Analytics para monitorização e alerta

Pode utilizar o SQL Analytics como um dashboard hierárquico para visualizar os seus recursos de base de dados.

- Para aprender a utilizar o Azure SQL Analytics, consulte [o Monitor utilizando o SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Para aprender a configurar alertas para o SQL Analytics, consulte [criar alertas para base de dados, piscinas elásticas e instâncias geridas.](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)

## <a name="stream-into-event-hubs"></a>Transmitir em fluxo para os Hubs de Eventos

Pode transmitir métricas de dados de Azure SQL e Azure SQL Gestd Instance e registos de recursos em Centros de Eventos, utilizando o Stream incorporado para uma opção de centro de **eventos** no portal Azure. Também pode ativar o ID da regra do autocarro de serviço utilizando definições de diagnóstico através de cmdlets PowerShell, do Azure CLI ou da API do Monitor Azure. Certifique-se de que o centro de eventos está na mesma região que a sua base de dados e servidor.

### <a name="what-to-do-with-metrics-and-resource-logs-in-event-hubs"></a>O que fazer com métricas e registos de recursos em Centros de Eventos

Depois de os dados selecionados terem sido transmitidos para Os Centros de Eventos, estás um passo mais perto de ativar cenários de monitorização avançados. O Event Hubs funciona como a porta da frente para um oleoduto de eventos. Após a recolha de dados num centro de eventos, pode ser transformado e armazenado utilizando um fornecedor de análise em tempo real ou um adaptador de armazenamento. O Event Hubs dissocia a produção de uma série de eventos do consumo desses eventos. Desta forma, os consumidores podem aceder aos eventos na sua própria agenda. Para obter mais informações sobre os Centros de Eventos, consulte:

- [O que são hubs de eventos Azure?](../../event-hubs/event-hubs-about.md)
- [Introdução ao Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

Pode utilizar métricas em streaming em Centros de Eventos para:

- **Ver saúde do serviço através do streaming de dados de caminho quente para Power BI**

  Ao utilizar os Centros de Eventos, Stream Analytics e Power BI, pode facilmente transformar as suas métricas e dados de diagnóstico em insights quase em tempo real nos seus serviços Azure. Para uma visão geral de como configurar um centro de eventos, processar dados com Stream Analytics e usar o Power BI como saída, consulte [Stream Analytics e Power BI](../../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Registos de fluxo para correntes de registo e telemetria de terceiros**

  Ao utilizar o streaming de Event Hubs, pode obter as suas métricas e registos de recursos em várias soluções de monitorização e análise de registo de terceiros.

- **Crie uma plataforma de registo e telemetria personalizada**

  Já tem uma plataforma de telemetria personalizada ou está a considerar construir uma? A natureza altamente escalável de subscrição de eventos hubs permite-lhe ingerir métricas e registos de recursos de forma flexível. Consulte o [guia de Dan Rosanova para usar os Centros de Eventos numa plataforma de telemetria à escala global.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="stream-into-azure-storage"></a>Fluir para o Armazenamento Azure

Pode armazenar métricas e registos de recursos no Azure Storage utilizando o Arquivo incorporado para uma opção de **conta de armazenamento** no portal Azure. Também pode ativar o Armazenamento utilizando definições de diagnóstico através de cmdlets PowerShell, do CLI Azure ou da API do Monitor Azure.

### <a name="schema-of-metrics-and-resource-logs-in-the-storage-account"></a>Esquema de métricas e registos de recursos na conta de armazenamento

Depois de configurar métricas e recolha de registos de recursos, é criado um recipiente de armazenamento na conta de armazenamento que selecionou quando as primeiras linhas de dados estão disponíveis. A estrutura das bolhas é:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ou, mais simplesmente:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por exemplo, um nome blob para métricas básicas pode ser:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Um nome blob para armazenar dados de uma piscina elástica parece:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Política de conservação de dados e preços

Se selecionar Centros de Eventos ou uma conta de Armazenamento, pode especificar uma política de retenção. Esta política elimina dados mais antigos do que um período de tempo selecionado. Se especificar o Log Analytics, a política de retenção depende do nível de preços selecionado. Neste caso, as unidades gratuitas de ingestão de dados disponibilizadas podem permitir um acompanhamento gratuito de várias bases de dados todos os meses. Qualquer consumo de telemetria de diagnóstico superior às unidades livres pode incorrer em custos.

> [!IMPORTANT]
> Bases de dados ativas com cargas de trabalho mais pesadas ingerem mais dados do que bases de dados ociosas. Para obter mais informações, consulte [os preços de Analítica de Registo](https://azure.microsoft.com/pricing/details/monitor/).

Se estiver a utilizar o Azure SQL Analytics, pode monitorizar o consumo de ingestão de dados selecionando o espaço de **trabalho OMS** no menu de navegação do Azure SQL Analytics e, em seguida, selecionando **Utilização** e **Custos Estimados** .

## <a name="metrics-and-logs-available"></a>Métricas e registos disponíveis

A telemetria de monitorização disponível para bases de dados individuais, bases de dados agrárias, piscinas elásticas, instâncias geridas e bases de dados de casos está documentada nesta secção do artigo. A telemetria de monitorização recolhida dentro do SQL Analytics pode ser usada para a sua própria análise personalizada e desenvolvimento de aplicações usando [o idioma de consultas de log do Azure Monitor.](../../azure-monitor/log-query/get-started-queries.md)

### <a name="basic-metrics"></a>Métricas básicas

Consulte as seguintes tabelas para obter detalhes sobre métricas básicas por recurso.

> [!NOTE]
> A opção métrica básica era anteriormente conhecida como Todas as métricas. A alteração feita foi apenas para o nome e não houve alteração das métricas monitorizadas. Esta alteração foi iniciada para permitir a introdução de categorias métricas adicionais no futuro.

#### <a name="basic-metrics-for-elastic-pools"></a>Métricas básicas para piscinas elásticas

|**Recurso**|**Métricas**|
|---|---|
|Conjunto elástico|percentagem de eDTU, eDTU usado, limite de eDTU, percentagem de CPU, percentagem de leitura de dados físicos, percentagem de registo, percentagem de sessões, percentagem de trabalhadores, armazenamento, percentagem de armazenamento, limite de armazenamento, percentagem de armazenamento de XTP |

#### <a name="basic-metrics-for-single-and-pooled-databases"></a>Métricas básicas para bases de dados individuais e agrizadas

|**Recurso**|**Métricas**|
|---|---|
|Base de dados única e agrizada|Percentagem de DTU, DTU usado, limite de DTU, percentagem de CPU, percentagem de leitura de dados físicos, percentagem de registo, Sucesso/Falhado/Bloqueado por ligações de firewall, percentagem de sessões, percentagem de trabalhadores, armazenamento, percentagem de armazenamento, percentagem de armazenamento de XTP e impasses |

### <a name="advanced-metrics"></a>Métricas avançadas

Consulte a tabela seguinte para obter detalhes sobre métricas avançadas.

|**Métrica**|**Nome de exibição métrica**|**Descrição**|
|---|---|---|
|sqlserver_process_core_percent<sup>1</sup>|Núcleo de processo SQL por cento|Percentagem de utilização do CPU para o processo SQL, medido pelo sistema operativo.|
|sqlserver_process_memory_percent<sup>1</sup> |SQL processa a memória por cento|Percentagem de utilização da memória para o processo SQL, medido pelo sistema operativo.|
|tempdb_data_size<sup>2</sup>| Kilobytes do tamanho do ficheiro de dados temporários |Kilobytes tamanho de ficheiro de dados temporários.|
|tempdb_log_size<sup>2</sup>| Kilobytes do tamanho do ficheiro de registo temporário |Kilobytes tamanho do ficheiro de registo temporário.|
|tempdb_log_used_percent<sup>2</sup>| Registo de percentagem de temperatura usado |Tempdb% % registo usado.|

<sup>1</sup> Esta métrica está disponível para bases de dados utilizando o modelo de compra vCore com 2 vCores e superior, ou 200 DTU e superior para modelos de compra baseados em DTU.

<sup>2</sup> Esta métrica está disponível para bases de dados utilizando o modelo de compra vCore com 2 vCores e superior, ou 200 DTU e superior para modelos de compra baseados em DTU. Esta métrica não está atualmente disponível para bases de dados de hiperescala ou armazéns de dados.

### <a name="basic-logs"></a>Troncos básicos

Os detalhes da telemetria disponível para todos os registos estão documentados nas seguintes tabelas. Para obter mais informações, consulte [a telemetria de diagnóstico suportada.](#diagnostic-telemetry-for-export)

#### <a name="resource-usage-stats-for-managed-instances"></a>Estatísticas de utilização de recursos para casos geridos

|Propriedade|Descrição|
|---|---|
|TenantId|Sua iD do seu inquilino |
|SourceSystem|Sempre: Azure|
|TempoGerado [UTC]|Carimbo de tempo quando o registo foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT. SQL |
|Categoria|Nome da categoria. Sempre: ResourceUsageStats |
|Recurso|O nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: MANAGEDINSTANCES |
|SubscriptionId|Guia de subscrição para a base de dados |
|ResourceGroup|Nome do grupo de recursos para a base de dados |
|LogicalServerName_s|Nome da instância gerida |
|ResourceId|URI de recursos |
|SKU_s|Sql Managed Instance produto SKU |
|virtual_core_count_s|Número de vCores disponíveis |
|avg_cpu_percent_s|Percentagem média de CPU |
|reserved_storage_mb_s|Capacidade de armazenamento reservada no caso gerido |
|storage_space_used_mb_s|Armazenamento usado no caso gerido |
|io_requests_s|Contagem de IOPS |
|io_bytes_read_s|IOPS bytes ler |
|io_bytes_written_s|Bytes IOPS escritos |

#### <a name="query-store-runtime-statistics"></a>Estatísticas de tempo de execução da loja de consultas

|Propriedade|Descrição|
|---|---|
|TenantId|Sua iD do seu inquilino |
|SourceSystem|Sempre: Azure |
|TempoGerado [UTC]|Carimbo de tempo quando o registo foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT. SQL |
|Categoria|Nome da categoria. Sempre: QueryStoreRuntimeStatistics |
|OperationName|O nome da operação. Sempre: QueryStoreRuntimeStatisticsEvent |
|Recurso|O nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BASES DE DADOS |
|SubscriptionId|Guia de subscrição para a base de dados |
|ResourceGroup|Nome do grupo de recursos para a base de dados |
|LogicalServerName_s|Nome do servidor para a base de dados |
|ElasticPoolName_s|Nome da piscina elástica para a base de dados, se houver |
|DatabaseName_s|Nome da base de dados |
|ResourceId|URI de recursos |
|query_hash_s|Haxixe de consulta |
|query_plan_hash_s|Haxixe do plano de consulta |
|statement_sql_handle_s|Pega sql declaração |
|interval_start_time_d|Início do intervalo de datas em número de tiques de 1900-1-1 |
|interval_end_time_d|Fim do intervalo do intervalo em número de tiques de 1900-1-1 |
|logical_io_writes_d|Número total de IO lógico escreve |
|max_logical_io_writes_d|Número máximo de IO lógico escreve por execução |
|physical_io_reads_d|Número total de leituras físicas de IO |
|max_physical_io_reads_d|Número máximo de leituras lógicas de IO por execução |
|logical_io_reads_d|Número total de leituras lógicas de IO |
|max_logical_io_reads_d|Número máximo de leituras lógicas de IO por execução |
|execution_type_d|Tipo de execução |
|count_executions_d|Número de execuções da consulta |
|cpu_time_d|Tempo total de CPU consumido pela consulta em microsegundos |
|max_cpu_time_d|Consumidor de tempo de CPU max por uma única execução em microsegundos |
|dop_d|Soma de graus de paralelismo |
|max_dop_d|Grau máximo de paralelismo utilizado para a execução única |
|rowcount_d|Número total de linhas devolvidas |
|max_rowcount_d|Número máximo de linhas devolvidas em execução única |
|query_max_used_memory_d|Quantidade total de memória utilizada no KB |
|max_query_max_used_memory_d|Quantidade máxima de memória usada por uma única execução em KB |
|duration_d|Tempo total de execução em microsegundos |
|max_duration_d|Tempo máximo de execução de uma única execução |
|num_physical_io_reads_d|Número total de leituras físicas |
|max_num_physical_io_reads_d|Número máximo de leituras físicas por execução |
|log_bytes_used_d|Quantidade total de bytes de log utilizados |
|max_log_bytes_used_d|Quantidade máxima de bytes de log utilizados por execução |
|query_id_d|ID da consulta na Loja de Consultas |
|plan_id_d|ID do plano na Loja de Consultas |

Saiba mais sobre [os dados das estatísticas de tempo de execução da Query Store.](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql)

#### <a name="query-store-wait-statistics"></a>Estatísticas de espera da Loja de Consultas

|Propriedade|Descrição|
|---|---|
|TenantId|Sua iD do seu inquilino |
|SourceSystem|Sempre: Azure |
|TempoGerado [UTC]|Carimbo de tempo quando o registo foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT. SQL |
|Categoria|Nome da categoria. Sempre: QueryStoreWaitStatistics |
|OperationName|O nome da operação. Sempre: QueryStoreWaitStatisticsEvent |
|Recurso|O nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BASES DE DADOS |
|SubscriptionId|Guia de subscrição para a base de dados |
|ResourceGroup|Nome do grupo de recursos para a base de dados |
|LogicalServerName_s|Nome do servidor para a base de dados |
|ElasticPoolName_s|Nome da piscina elástica para a base de dados, se houver |
|DatabaseName_s|Nome da base de dados |
|ResourceId|URI de recursos |
|wait_category_s|Categoria da espera |
|is_parameterizable_s|É a consulta parameterizável |
|statement_type_s|Tipo de declaração |
|statement_key_hash_s|Haxixe-chave de declaração |
|exec_type_d|Tipo de execução |
|total_query_wait_time_ms_d|Tempo total de espera da consulta na categoria de espera específica |
|max_query_wait_time_ms_d|Tempo de espera máximo da consulta na execução individual na categoria de espera específica |
|query_param_type_d|0 |
|query_hash_s|Haxixe de consulta na Loja de Consultas |
|query_plan_hash_s|Plano de consulta haxixe na Loja de Consultas |
|statement_sql_handle_s|Pega de declaração na Loja de Consultas |
|interval_start_time_d|Início do intervalo de datas em número de tiques de 1900-1-1 |
|interval_end_time_d|Fim do intervalo do intervalo em número de tiques de 1900-1-1 |
|count_executions_d|Contagem das execuções da consulta |
|query_id_d|ID da consulta na Loja de Consultas |
|plan_id_d|ID do plano na Loja de Consultas |

Saiba mais sobre [os dados das estatísticas de espera da Loja de Consultas.](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql)

#### <a name="errors-dataset"></a>Conjunto de dados de erros

|Propriedade|Descrição|
|---|---|
|TenantId|Sua iD do seu inquilino |
|SourceSystem|Sempre: Azure |
|TempoGerado [UTC]|Carimbo de tempo quando o registo foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT. SQL |
|Categoria|Nome da categoria. Sempre: Erros |
|OperationName|O nome da operação. Sempre: ErrorEvent |
|Recurso|O nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BASES DE DADOS |
|SubscriptionId|Guia de subscrição para a base de dados |
|ResourceGroup|Nome do grupo de recursos para a base de dados |
|LogicalServerName_s|Nome do servidor para a base de dados |
|ElasticPoolName_s|Nome da piscina elástica para a base de dados, se houver |
|DatabaseName_s|Nome da base de dados |
|ResourceId|URI de recursos |
|Mensagem|Mensagem de erro em texto simples |
|user_defined_b|É a bit definida pelo utilizador de erro |
|error_number_d|Código de erro |
|Gravidade|Gravidade do erro |
|state_d|Estado do erro |
|query_hash_s|Haxixe de consulta da consulta falhada, se disponível |
|query_plan_hash_s|Plano de consulta da consulta falhada, se disponível |

Saiba mais sobre [as mensagens de erro SQL](/sql/relational-databases/errors-events/database-engine-events-and-errors).

#### <a name="database-wait-statistics-dataset"></a>Conjunto de dados de estatísticas de espera de base de dados

|Propriedade|Descrição|
|---|---|
|TenantId|Sua iD do seu inquilino |
|SourceSystem|Sempre: Azure |
|TempoGerado [UTC]|Carimbo de tempo quando o registo foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT. SQL |
|Categoria|Nome da categoria. Sempre: DatabaseWaitStatistics |
|OperationName|O nome da operação. Sempre: DatabaseWaitStatisticsEvent |
|Recurso|O nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BASES DE DADOS |
|SubscriptionId|Guia de subscrição para a base de dados |
|ResourceGroup|Nome do grupo de recursos para a base de dados |
|LogicalServerName_s|Nome do servidor para a base de dados |
|ElasticPoolName_s|Nome da piscina elástica para a base de dados, se houver |
|DatabaseName_s|Nome da base de dados |
|ResourceId|URI de recursos |
|wait_type_s|Nome do tipo de espera |
|start_utc_date_t [UTC]|Tempo de início do período medido |
|end_utc_date_t [UTC]|Tempo de fim do período medido |
|delta_max_wait_time_ms_d|Max esperou tempo por execução |
|delta_signal_wait_time_ms_d|Total de sinais tempo de espera |
|delta_wait_time_ms_d|Tempo total de espera no período |
|delta_waiting_tasks_count_d|Número de tarefas de espera |

Saiba mais sobre [estatísticas de espera na base de dados.](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)

#### <a name="time-outs-dataset"></a>Conjunto de dados de intervalos de tempo

|Propriedade|Descrição|
|---|---|
|TenantId|Sua iD do seu inquilino |
|SourceSystem|Sempre: Azure |
|TempoGerado [UTC]|Carimbo de tempo quando o registo foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT. SQL |
|Categoria|Nome da categoria. Sempre: Intervalos |
|OperationName|O nome da operação. Sempre: TimeoutEvent |
|Recurso|O nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BASES DE DADOS |
|SubscriptionId|Guia de subscrição para a base de dados |
|ResourceGroup|Nome do grupo de recursos para a base de dados |
|LogicalServerName_s|Nome do servidor para a base de dados |
|ElasticPoolName_s|Nome da piscina elástica para a base de dados, se houver |
|DatabaseName_s|Nome da base de dados |
|ResourceId|URI de recursos |
|error_state_d|Código de estado de erro |
|query_hash_s|Haxixe de consulta, se disponível |
|query_plan_hash_s|Haxixe plano de consulta, se disponível |

#### <a name="blockings-dataset"></a>Conjunto de dados de bloqueio

|Propriedade|Descrição|
|---|---|
|TenantId|Sua iD do seu inquilino |
|SourceSystem|Sempre: Azure |
|TempoGerado [UTC]|Carimbo de tempo quando o registo foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT. SQL |
|Categoria|Nome da categoria. Sempre: Blocos |
|OperationName|O nome da operação. Sempre: BlockEvent |
|Recurso|O nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BASES DE DADOS |
|SubscriptionId|Guia de subscrição para a base de dados |
|ResourceGroup|Nome do grupo de recursos para a base de dados |
|LogicalServerName_s|Nome do servidor para a base de dados |
|ElasticPoolName_s|Nome da piscina elástica para a base de dados, se houver |
|DatabaseName_s|Nome da base de dados |
|ResourceId|URI de recursos |
|lock_mode_s|Modo de bloqueio utilizado pela consulta |
|resource_owner_type_s|Dono da fechadura |
|blocked_process_filtered_s|Relatório de processo bloqueado XML |
|duration_d|Duração do bloqueio em microsegundos |

#### <a name="deadlocks-dataset"></a>Conjunto de dados de impasses

|Propriedade|Descrição|
|---|---|
|TenantId|Sua iD do seu inquilino |
|SourceSystem|Sempre: Azure |
|TempoGerado [UTC] |Carimbo de tempo quando o registo foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT. SQL |
|Categoria|Nome da categoria. Sempre: Impasses |
|OperationName|O nome da operação. Sempre: DeadlockEvent |
|Recurso|O nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BASES DE DADOS |
|SubscriptionId|Guia de subscrição para a base de dados |
|ResourceGroup|Nome do grupo de recursos para a base de dados |
|LogicalServerName_s|Nome do servidor para a base de dados |
|ElasticPoolName_s|Nome da piscina elástica para a base de dados, se houver |
|DatabaseName_s|Nome da base de dados |
|ResourceId|URI de recursos |
|deadlock_xml_s|Relatório de impasse XML |

#### <a name="automatic-tuning-dataset"></a>Conjunto automático de dados de afinação

|Propriedade|Descrição|
|---|---|
|TenantId|Sua iD do seu inquilino |
|SourceSystem|Sempre: Azure |
|TempoGerado [UTC]|Carimbo de tempo quando o registo foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT. SQL |
|Categoria|Nome da categoria. Sempre: Sintonização Automática |
|Recurso|O nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BASES DE DADOS |
|SubscriptionId|Guia de subscrição para a base de dados |
|ResourceGroup|Nome do grupo de recursos para a base de dados |
|LogicalServerName_s|Nome do servidor para a base de dados |
|LogicalDatabaseName_s|Nome da base de dados |
|ElasticPoolName_s|Nome da piscina elástica para a base de dados, se houver |
|DatabaseName_s|Nome da base de dados |
|ResourceId|URI de recursos |
|RecommendationHash_s|Haxixe único de recomendação de afinação automática |
|OptionName_s|Operação de afinação automática |
|Schema_s|Esquema de base de dados |
|Table_s|Tabela afetada |
|IndexName_s|Nome do índice |
|IndexColumns_s|Nome da coluna |
|IncludedColumns_s|Colunas incluídas |
|EstimatedImpact_s|Impacto estimado da recomendação de afinação automática JSON |
|Event_s|Tipo de evento de afinação automática |
|Timestamp_t|Última hora atualizada |

#### <a name="intelligent-insights-dataset"></a>Conjunto de dados de insights inteligentes

Saiba mais sobre o [formato de registo de Insights Inteligentes.](intelligent-insights-use-diagnostics-log.md)

## <a name="next-steps"></a>Passos seguintes

Para aprender como permitir a exploração madeireira e compreender as métricas e categorias de registo suportadas pelos vários serviços Azure, consulte:

- [Visão geral das métricas no Microsoft Azure](../../azure-monitor/platform/data-platform.md)
- [Descrição geral dos registos de plataforma Azure](../../azure-monitor/platform/platform-logs-overview.md)

Para saber mais sobre os Centros de Eventos, leia:

- [O que são os Hubs de Eventos do Azure?](../../event-hubs/event-hubs-about.md)
- [Introdução ao Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

Para aprender a configurar alertas baseados na telemetria a partir de análise de registos consulte:

- [Criação de alertas para a Base de Dados Azure SQL e para a Azure SQL Gestão de Instâncias](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)