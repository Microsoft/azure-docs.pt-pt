---
title: Monitorização de ficheiros Azure | Microsoft Docs
description: Saiba como monitorizar o desempenho e disponibilidade dos Ficheiros Azure. Monitorize os dados do Azure Files, aprenda sobre a configuração e analise dados métricos e de registo.
author: normesta
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 3/02/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 620ee3bc5978da4b274aed9a412679ae0835f0b9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759832"
---
# <a name="monitoring-azure-files"></a>Monitorar ficheiros Azure

Quando você tem aplicações críticas e processos de negócio que dependem de recursos Azure, você quer monitorizar esses recursos para sua disponibilidade, desempenho e funcionamento. Este artigo descreve os dados de monitorização gerados pelos Ficheiros Azure e como pode utilizar as funcionalidades do Azure Monitor para analisar alertas sobre estes dados.

## <a name="monitor-overview"></a>Visão geral do monitor

A página **'Visão Geral'** no portal Azure para cada recurso de Ficheiros Azure inclui uma breve visão da utilização do recurso, como pedidos e faturação horária. Esta informação é útil, mas apenas uma pequena quantidade dos dados de monitorização está disponível. Alguns destes dados são recolhidos automaticamente e estão disponíveis para análise assim que criar o recurso. Pode ativar tipos adicionais de recolha de dados com alguma configuração.

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
O Azure Files cria dados de monitorização utilizando [o Azure Monitor,](../../azure-monitor/overview.md)que é um serviço de monitorização de pilhas completa em Azure. O Azure Monitor fornece um conjunto completo de funcionalidades para monitorizar os seus recursos e recursos Azure em outras nuvens e no local. 

Comece com o artigo [Monitorar os recursos do Azure com o Azure Monitor,](../../azure-monitor/essentials/monitor-azure-resource.md)que descreve o seguinte:

- O que é o Azure Monitor?
- Custos associados à monitorização
- Dados de monitorização recolhidos em Azure
- Recolha de dados configurado
- Ferramentas padrão em Azure para analisar e alertar sobre os dados de monitorização

As secções seguintes baseiam-se neste artigo descrevendo os dados específicos recolhidos a partir de Ficheiros Azure. Exemplos mostram como configurar a recolha de dados e analisar estes dados com ferramentas Azure.

## <a name="monitoring-data"></a>Monitorizar dados

A Azure Files recolhe os mesmos tipos de dados de monitorização que outros recursos Azure, que são descritos na [monitorização de dados a partir de recursos Azure](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Consulte [a referência de dados de monitorização do Ficheiro Azure](storage-files-monitoring-reference.md) para obter informações detalhadas sobre as métricas de métricas e registos criadas pelos Ficheiros Azure.

Métricas e registos no Azure Monitor suportam apenas contas de armazenamento do Gestor de Recursos Azure. O Azure Monitor não suporta contas clássicas de armazenamento. Se quiser utilizar métricas ou registos numa conta de armazenamento clássica, tem de migrar para uma conta de armazenamento do Azure Resource Manager. Consulte [a Migração para o Gestor de Recursos Azure](../../virtual-machines/migration-classic-resource-manager-overview.md).

## <a name="collection-and-routing"></a>Recolha e encaminhamento

As métricas da plataforma e o registo de Atividade são recolhidos automaticamente, mas podem ser encaminhados para outros locais utilizando uma definição de diagnóstico. 

Para recolher registos de recursos, tem de criar uma definição de diagnóstico. Quando criar a definição, escolha o **ficheiro** como o tipo de armazenamento para o qual pretende ativar os registos. Em seguida, especifique uma das seguintes categorias de operações para as quais pretende recolher registos. 

| Categoria | Descrição |
|:---|:---|
| ArmazenamentoRead | Leia as operações em objetos. |
| ArmazenamentoPorite | Escreva operações em objetos. |
| StorageDelete | Eliminar operações em objetos. |

Para obter a lista de operações SMB e REST que estão registadas, consulte [as operações de armazenamento registadas e as mensagens de estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e os [Ficheiros Azure que monitorizam a referência de dados](storage-files-monitoring-reference.md).

## <a name="creating-a-diagnostic-setting"></a>Criação de uma definição de diagnóstico

Pode criar uma definição de diagnóstico utilizando o portal Azure, o PowerShell, o Azure CLI ou um modelo de Gestor de Recursos Azure.

> [!NOTE]
> Os registos de armazenamento Azure no Azure Monitor estão em pré-visualização pública e estão disponíveis para testes de pré-visualização em todas as regiões de nuvem pública. Esta pré-visualização permite registos para bolhas (que inclui Azure Data Lake Storage Gen2), ficheiros, filas e tabelas. Esta funcionalidade encontra-se disponível para todas as contas de armazenamento que são criadas com o modelo de implementação do Azure Resource Manager. Consulte [a visão geral da conta de armazenamento](../common/storage-account-overview.md).

Para obter orientações gerais, consulte [Criar definição de diagnóstico para recolher registos e métricas da plataforma em Azure](../../azure-monitor/essentials/diagnostic-settings.md).

### <a name="azure-portal"></a>[Portal do Azure](#tab/azure-portal)

1. Inicie sessão no portal do Azure.

2. Navegue até à sua conta de armazenamento.

3. Na secção **de Monitorização,** clique nas **definições de Diagnóstico (pré-visualização)**.

   > [!div class="mx-imgBorder"]
   > ![portal - Registos de diagnóstico](media/storage-files-monitoring/diagnostic-logs-settings-pane.png)   

4. Escolha o **ficheiro** como o tipo de armazenamento para o qual pretende ativar os registos.

5. Clique **na definição de diagnóstico de adicionar**.

   > [!div class="mx-imgBorder"]
   > ![portal - Registos de recursos - adicione a definição de diagnóstico](media/storage-files-monitoring/diagnostic-logs-settings-pane-2.png)

   Aparece a página **de definições de Diagnóstico.**

   > [!div class="mx-imgBorder"]
   > ![Página de registos de recursos](media/storage-files-monitoring/diagnostic-logs-page.png)

6. No campo **Nome** da página, insira um nome para esta definição de registo de recursos. Em seguida, selecione quais as operações que pretende registadas (ler, escrever e apagar operações) e para onde pretende que os registos sejam enviados.

#### <a name="archive-logs-to-a-storage-account"></a>Registos de arquivo para uma conta de armazenamento

Se optar por arquivar os seus registos numa conta de armazenamento, pagará o volume de registos que são enviados para a conta de armazenamento. Para obter preços **específicos,** consulte a secção de Registos de Plataforma da página de preços do [Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

1. Selecione o Arquivo para uma caixa de verificação **de conta de armazenamento** e, em seguida, clique no botão **Configure.**

   > [!div class="mx-imgBorder"]   
   > ![Armazenamento de arquivo de página de definições de diagnóstico](media/storage-files-monitoring/diagnostic-logs-settings-pane-archive-storage.png)

2. Na lista de down-down da **conta De armazenamento,** selecione a conta de armazenamento para a que pretende arquivar os seus registos, clique no botão **OK** e, em seguida, clique no botão **Guardar.**

   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > Antes de escolher uma conta de armazenamento como destino de exportação, consulte [os registos de recursos do Archive Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) para entender os pré-requisitos na conta de armazenamento.

#### <a name="stream-logs-to-azure-event-hubs"></a>Registos de streaming para Azure Event Hubs

Se optar por transmitir os seus registos para um centro de eventos, pagará o volume de registos que são enviados para o centro de eventos. Para obter preços **específicos,** consulte a secção de Registos de Plataforma da página de preços do [Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

1. Selecione o Stream para uma caixa de verificação **do centro de eventos** e, em seguida, clique no botão **Configure.**

2. No painel **de centros de eventos Selecione um painel de centros** de eventos, escolha o espaço de nome, nome e nome da política do centro de eventos para o qual pretende transmitir os seus registos. 

   > [!div class="mx-imgBorder"]
   > ![Centro de evento de página de definições de diagnóstico](media/storage-files-monitoring/diagnostic-logs-settings-pane-event-hub.png)

3. Clique no botão **OK** e, em seguida, clique no botão **Guardar.**

#### <a name="send-logs-to-azure-log-analytics"></a>Enviar registos para Azure Log Analytics

1. Selecione a caixa de verificação **Enviar para registar analytics,** selecione um espaço de trabalho de análise de registo e, em seguida, clique no botão Guardar e, em seguida, clique no botão **Guardar.**

   > [!div class="mx-imgBorder"]   
   > ![Análise de registo de página de definições de diagnóstico](media/storage-files-monitoring/diagnostic-logs-settings-pane-log-analytics.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Abra uma janela de comando Windows PowerShell e inscreva-se na sua subscrição Azure utilizando o `Connect-AzAccount` comando. Em seguida, siga as instruções no ecrã.

   ```powershell
   Connect-AzAccount
   ```

2. Desaça a sua subscrição ativa para a subscrição da conta de armazenamento para a que pretende ativar o registo.

   ```powershell
   Set-AzContext -SubscriptionId <subscription-id>
   ```

#### <a name="archive-logs-to-a-storage-account"></a>Registos de arquivo para uma conta de armazenamento

Se optar por arquivar os seus registos numa conta de armazenamento, pagará o volume de registos que são enviados para a conta de armazenamento. Para obter preços **específicos,** consulte a secção de Registos de Plataforma da página de preços do [Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Ativar os registos utilizando o [cmdlet de definição de](/powershell/module/az.monitor/set-azdiagnosticsetting) diagnósticose de definição de energia, juntamente com o `StorageAccountId` parâmetro.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -StorageAccountId <storage-account-resource-id> -Enabled $true -Category <operations-to-log> 
```

Substitua o `<storage-service-resource--id>` espaço reservado neste corte pelo ID de recursos do serviço Azure File. Pode encontrar o ID de recursos no portal Azure abrindo a página **Propriedades** da sua conta de armazenamento.

Pode `StorageRead` `StorageWrite` utilizar, e `StorageDelete` pelo valor do parâmetro **Categoria.**

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Eis um exemplo:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Para obter uma descrição de cada parâmetro, consulte os [registos de recursos archive Azure através do Azure PowerShell](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Registos de fluxo para um centro de eventos

Se optar por transmitir os seus registos para um centro de eventos, pagará o volume de registos que são enviados para o centro de eventos. Para obter preços **específicos,** consulte a secção de Registos de Plataforma da página de preços do [Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Ativar os registos utilizando o [cmdlet de definição de](/powershell/module/az.monitor/set-azdiagnosticsetting) diagnósticos elétricos com o `EventHubAuthorizationRuleId` parâmetro.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Eis um exemplo:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

Para obter uma descrição de cada parâmetro, consulte os dados de [fluxo para centros de evento através de cmdlets PowerShell](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Enviar registos para o Log Analytics

Ativar os registos utilizando o [cmdlet de definição de](/powershell/module/az.monitor/set-azdiagnosticsetting) diagnósticos elétricos com o `WorkspaceId` parâmetro.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Eis um exemplo:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

Para obter mais informações, consulte [os Registos de Recursos stream Azure para registar o espaço de trabalho no Azure Monitor](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Primeiro, abra o [Azure Cloud Shell](../../cloud-shell/overview.md), ou se [instalou](/cli/azure/install-azure-cli) o Azure CLI localmente, abra uma aplicação de consola de comando como o Windows PowerShell.

2. Se a sua identidade estiver associada a mais de uma subscrição, então desa estale a sua subscrição ativa para a subscrição da conta de armazenamento para a qual pretende ativar os registos.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Substitua o `<subscription-id>` valor do espaço reservado pelo ID da sua subscrição.

#### <a name="archive-logs-to-a-storage-account"></a>Registos de arquivo para uma conta de armazenamento

Se optar por arquivar os seus registos numa conta de armazenamento, pagará o volume de registos que são enviados para a conta de armazenamento. Para obter preços **específicos,** consulte a secção de Registos de Plataforma da página de preços do [Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Ativar os registos utilizando as [definições de diagnóstico do monitor az criar](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) comando.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true}]'
```

Substitua o `<storage-service-resource--id>` espaço reservado neste corte pelo serviço de armazenamento ID Blob de recurso. Pode encontrar o ID de recursos no portal Azure abrindo a página **Propriedades** da sua conta de armazenamento.

Pode `StorageRead` utilizar, `StorageWrite` e pelo valor do parâmetro da `StorageDelete` **categoria.**

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Eis um exemplo:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true}]'`

Para obter uma descrição de cada parâmetro, consulte os [registos de Recursos de Arquivo através do CLI Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Registos de fluxo para um centro de eventos

Se optar por transmitir os seus registos para um centro de eventos, pagará o volume de registos que são enviados para o centro de eventos. Para obter preços **específicos,** consulte a secção de Registos de Plataforma da página de preços do [Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Ativar os registos utilizando as [definições de diagnóstico do monitor az criar](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) comando.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Eis um exemplo:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

Para obter uma descrição de cada parâmetro, consulte os dados do [Stream para Os Centros de Eventos via Azure CLI](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Enviar registos para o Log Analytics

Ativar os registos utilizando as [definições de diagnóstico do monitor az criar](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) comando.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Eis um exemplo:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 Para obter mais informações, consulte [os Registos de Recursos stream Azure para registar o espaço de trabalho no Azure Monitor](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

### <a name="template"></a>[Modelo](#tab/template)

Para ver um modelo do Gestor de Recursos Azure que cria uma definição de diagnóstico, consulte [a definição de Diagnóstico para armazenamento Azure](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage).

---

## <a name="analyzing-metrics"></a>Análise de métricas

Pode analisar métricas para armazenamento Azure com métricas de outros serviços Azure utilizando o Metrics Explorer. Open Metrics Explorer escolhendo **métricas** do menu **Azure Monitor.** Para obter detalhes sobre a utilização desta ferramenta, consulte [Começar com o Azure Metrics Explorer](../../azure-monitor/essentials/metrics-getting-started.md). 

Para métricas que suportam dimensões, pode filtrar a métrica com o valor de dimensão pretendido.  Para obter uma lista completa das dimensões que o Azure Storage suporta, consulte [as dimensões das Métricas.](storage-files-monitoring-reference.md#metrics-dimensions) As métricas para ficheiros Azure estão nestes espaços de nome: 

- Microsoft.Storage/storageAcontas
- Microsoft.Storage/storageAcontas/fileServices

Para obter uma lista de todas as métricas de suporte do Azure Monitor, que inclui ficheiros Azure, consulte [as métricas suportadas pelo Monitor Azure](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices).

### <a name="accessing-metrics"></a>Métricas de acesso

> [!TIP]
> Para ver exemplos de Azure CLI ou .NET, escolha os separadores correspondentes listados aqui.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Listar a definição métrica

Pode listar a definição métrica da sua conta de armazenamento ou do serviço Azure Files. Utilize o [cmdlet Get-AzMetricDefinition.](/powershell/module/az.monitor/get-azmetricdefinition)

Neste exemplo, substitua o `<resource-ID>` espaço reservado pelo ID de recursos de toda a conta de armazenamento ou pelo ID de recursos do serviço Azure Files.  Pode encontrar estes IDs de recursos nas páginas **Propriedades** da sua conta de armazenamento no portal Azure.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Valores métricos de leitura

Pode ler os valores métricos ao nível da conta da sua conta de armazenamento ou o serviço Azure Files. Utilize o [cmdlet Get-AzMetric.](/powershell/module/Az.Monitor/Get-AzMetric)

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Listar a definição métrica de nível de conta

Pode listar a definição métrica da sua conta de armazenamento ou do serviço Azure Files. Utilize o comando [de definições de listas de métricas az monitor.](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions)
 
Neste exemplo, substitua o `<resource-ID>` espaço reservado pelo ID de recursos de toda a conta de armazenamento ou pelo ID de recursos do serviço Azure Files. Pode encontrar estes IDs de recursos nas páginas **Propriedades** da sua conta de armazenamento no portal Azure.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Leia os valores métricos ao nível da conta

Pode ler os valores métricos da sua conta de armazenamento ou do serviço Azure Files. Utilize o comando [da lista de métricas do monitor az.](/cli/azure/monitor/metrics#az_monitor_metrics_list)

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/azure-portal)

O Azure Monitor fornece o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) para ler definições e valores métricos. O [código de amostra](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) mostra como utilizar o SDK com diferentes parâmetros. Você precisa usar `0.18.0-preview` ou uma versão posterior para métricas de armazenamento.
 
Nestes exemplos, substitua o `<resource-ID>` espaço reservado pelo ID de recursos de toda a conta de armazenamento ou pelo serviço Azure Files. Pode encontrar estes IDs de recursos nas páginas **Propriedades** da sua conta de armazenamento no portal Azure.

Substitua a `<subscription-ID>` variável pelo ID da sua subscrição. Para obter orientações sobre como obter valores `<tenant-ID>` `<application-ID>` para, `<AccessKey>` e, ver [Usar o portal para criar uma aplicação AD AZure e um principal de serviço que possa aceder a recursos.](../../active-directory/develop/howto-create-service-principal-portal.md) 

#### <a name="list-the-account-level-metric-definition"></a>Listar a definição métrica de nível de conta

O exemplo a seguir mostra como listar uma definição métrica ao nível da conta:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

#### <a name="reading-account-level-metric-values"></a>Leitura de valores métricos ao nível da conta

O exemplo a seguir mostra como ler `UsedCapacity` dados ao nível da conta:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            // Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

#### <a name="reading-multidimensional-metric-values"></a>Leitura de valores métricos multidimensionais

Para métricas multidimensionais, é necessário definir filtros de metadados se quiser ler dados métricos sobre valores de dimensão específicas.

O exemplo a seguir mostra como ler dados métricos sobre a multidimensional de suporte métrico:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for Azure Files
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

# <a name="template"></a>[Modelo](#tab/template)

N/D.

---

## <a name="analyzing-logs"></a>Análise de registos

Pode aceder a registos de recursos como uma bolha numa conta de armazenamento, como dados de eventos, ou através de consultas de Log Analytic.

Para obter a lista de operações SMB e REST que estão registadas, consulte [as operações de armazenamento registadas e as mensagens de estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e os [Ficheiros Azure que monitorizam a referência de dados](storage-files-monitoring-reference.md).

> [!NOTE]
> Os registos de armazenamento Azure no Azure Monitor estão em pré-visualização pública e estão disponíveis para testes de pré-visualização em todas as regiões de nuvem pública. Esta pré-visualização permite registos para blobs (que inclui Azure Data Lake Storage Gen2), ficheiros, filas, tabelas, contas de armazenamento premium em v1 de uso geral e contas de armazenamento v2 de uso geral. As contas clássicas de armazenamento não são suportadas.

As entradas de registo só são criadas se houver pedidos feitos contra o ponto final de serviço. Por exemplo, se uma conta de armazenamento tiver atividade no seu ponto final de ficheiro, mas não nos seus pontos finais de tabela ou fila, apenas são criados registos relativos ao serviço Azure File. Os registos de armazenamento Azure contêm informações detalhadas sobre pedidos bem sucedidos e falhados para um serviço de armazenamento. Estas informações podem ser utilizadas para monitorizar os pedidos individuais e diagnosticar problemas num serviço de armazenamento. Os pedidos são registados numa base de melhor esforço.

### <a name="log-authenticated-requests"></a>Registar pedidos autenticados

 São registados os seguintes tipos de pedidos autenticados:

- Pedidos com êxito
- Pedidos falhados, incluindo tempo limite, limitação, rede, autorização e outros erros
- Pedidos que utilizem Kerberos, NTLM ou assinatura de acesso partilhado (SAS), incluindo pedidos falhados e bem sucedidos
- Pedidos de dados de análise (dados clássicos de registo no recipiente **$logs** e dados métricos clássicos nas tabelas **$metric)**

Os pedidos feitos pelo próprio serviço Azure Files, como criação de registos ou eliminação, não são registados. Para obter uma lista completa dos pedidos de SMB e REST que estão registados, consulte [as operações registadas de Armazenamento e mensagens de estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e os [Ficheiros Azure que monitorizam a referência de dados](storage-files-monitoring-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Aceder a registos numa conta de armazenamento

Os registos aparecem como bolhas armazenadas num contentor na conta de armazenamento do alvo. Os dados são recolhidos e armazenados dentro de uma única bolha como uma carga de carga JSON delimitada pela linha. O nome da bolha segue esta convenção de nomeação:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/fileServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Eis um exemplo:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Aceder a troncos num centro de eventos

Os registos enviados para um centro de eventos não são armazenados como um ficheiro, mas pode verificar se o centro de eventos recebeu a informação de registo. No portal Azure, vá ao seu centro de eventos e verifique se a contagem **de mensagens recebidas** é superior a zero. 

![Registos de auditoria](media/storage-files-monitoring/event-hub-log.png)

Pode aceder e ler dados de registo que são enviados para o seu centro de eventos utilizando informações de segurança e ferramentas de gestão e monitorização de eventos. Para mais informações, veja [o que posso fazer com os dados de monitorização que estão a ser enviados para o meu centro de eventos?](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Aceder a registos num espaço de trabalho do Log Analytics

Pode aceder aos registos enviados para um espaço de trabalho do Log Analytics utilizando consultas de registo do Azure Monitor. Os dados são armazenados na tabela **StorageFileLogs.** 

Para mais informações, consulte [o tutorial do Log Analytics.](../../azure-monitor/logs/log-analytics-tutorial.md)

#### <a name="sample-kusto-queries"></a>Experimente consultas kusto

Aqui ficam algumas consultas que pode introduzir na barra **de pesquisa log** para ajudá-lo a monitorizar os seus Ficheiros Azure. Estas consultas funcionam com a [nova linguagem.](../../azure-monitor/logs/log-query-overview.md)

> [!IMPORTANT]
> Quando seleciona **Logs** do menu do grupo de recursos de conta de armazenamento, o Log Analytics é aberto com o âmbito de consulta definido para o grupo de recursos atual. Isto significa que as consultas de registo só incluirão dados desse grupo de recursos. Se pretender executar uma consulta que inclua dados de outros recursos ou dados de outros serviços Azure, selecione **Logs** do menu **Azure Monitor.** Consulte [o âmbito de consulta de registo e o intervalo de tempo no Azure Monitor Log Analytics](../../azure-monitor/logs/scope.md) para obter mais detalhes.

Utilize estas consultas para ajudá-lo a monitorizar as suas partilhas de ficheiros Azure:

- Ver erros de SMB na última semana

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) and StatusCode contains "-"
| sort by StatusCode
```
- Criar um gráfico de tortas de operações SMB na última semana

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

- Ver erros do REST na última semana

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) and StatusText !contains "Success"
| sort by StatusText asc
```

- Crie um gráfico de tortas de operações REST na última semana

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

Para ver a lista de nomes e descrições de colunas para Ficheiros Azure, consulte [StorageFileLogs](/azure/azure-monitor/reference/tables/storagefilelogs).

Para obter mais informações sobre como escrever consultas, consulte [o tutorial do Log Analytics.](../../azure-monitor/logs/log-analytics-tutorial.md)

## <a name="alerts"></a>Alertas

Os alertas do Azure Monitor notificam-no proativamente quando forem encontradas condições importantes nos seus dados de monitorização. Permitem identificar e resolver problemas no seu sistema antes que os seus clientes os percebam. Pode definir alertas em [métricas,](../../azure-monitor/alerts/alerts-metric-overview.md) [registos](../../azure-monitor/alerts/alerts-unified-log.md)e no registo de [atividades](../../azure-monitor/alerts/activity-log-alerts.md). 

A tabela a seguir enumera alguns cenários de exemplo para monitorizar e a métrica adequada a utilizar para o alerta:

| Scenario | Métrica para usar para alerta |
|-|-|
| A partilha de ficheiros está acelerada. | Métrica: Transações<br>Nome de dimensão: Tipo de resposta <br>Nome de dimensão: FileShare (apenas partilha de ficheiros premium) |
| O tamanho da partilha de ficheiros é de 80% da capacidade. | Métrica: Capacidade de Arquivo<br>Nome de dimensão: FileShare (apenas partilha de ficheiros premium) |
| A saída de partilha de ficheiros ultrapassou os 500 GiB num dia. | Métrica: Egress<br>Nome de dimensão: FileShare (apenas partilha de ficheiros premium) |

### <a name="how-to-create-alerts-for-azure-files"></a>Como criar alertas para ficheiros Azure

1. Aceda à sua **conta de armazenamento** no portal **Azure.** 

2. Clique **em Alertas** e, em seguida, clique **em + Nova regra de alerta**.

3. Clique **em Editar o recurso,** selecione o **tipo de recurso De ficheiro** e, em seguida, clique em **Fazer**. 

4. Clique **em Adicionar a condição** e forneça as seguintes informações para o alerta: 

    - **Métrica**
    - **Nome de dimensão**
    - **Lógica de alerta**

5. Clique **em Adicionar grupos de ação** e adicione um grupo de ação (e-mail, SMS, etc.) ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação.

6. Preencha os **detalhes do Alerta** como o nome da regra de **alerta,** **descrição** e **severidade**.

7. Clique **em Criar regra de alerta** para criar o alerta.

> [!NOTE]  
> Se criar um alerta e for muito barulhento, ajuste o valor do limiar e alerte a lógica.

### <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Como criar um alerta se uma partilha de ficheiros for acelerada

1. Aceda à sua **conta de armazenamento** no portal **Azure.**
2. Na secção **de Monitorização,** clique em **Alertas** e, em seguida, clique **em + Nova regra de alerta**.
3. Clique **em Editar o recurso,** selecione o **tipo de recurso De ficheiro** para a conta de armazenamento e, em seguida, clique em **Fazer**. Por exemplo, se o nome da conta de armazenamento `contoso` for, selecione o `contoso/file` recurso.
4. Clique **em Adicionar condição** para adicionar uma condição.
5. Verá uma lista de sinais suportados para a conta de armazenamento, selecione a métrica **de Transações.**
6. Na lâmina lógica de **sinal configurar,** clique no **nome Dimension** drop-down e selecione o tipo **de resposta**.
7. Clique nos **valores** de Dimension drop-down e selecione os tipos de resposta adequados para a sua partilha de ficheiros.

    Para ações de ficheiros padrão, selecione os seguintes tipos de resposta:

    - SucessoComPartiIopsThrottling
    - SucessoWithThrottling
    - ClientShareIopsThrottlingError

    Para ações de ficheiros premium, selecione os seguintes tipos de resposta:

    - SucessoComParteEgressThrottling
    - SucessoWithShareIngressThrottling
    - SucessoComPartiIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

   > [!NOTE]
   > Se os tipos de resposta não estiverem listados na redução dos valores de **dimensão,** isto significa que o recurso não foi estrangulado. Para adicionar os valores de dimensão, junto à lista de valores de **Dimensão,** selecione **Adicionar valor personalizado,** introduzir o tipo de respone (por exemplo, **SuccessWithThrottling),** selecionar **OK**, e, em seguida, repetir estes passos para adicionar todos os tipos de resposta aplicáveis para a sua partilha de ficheiros.

8. Para **ações de ficheiros premium,** clique no **nome Dimension** drop-down e selecione File **Share**. Para **ações de ficheiros padrão**, salte para **o passo #10**. .

   > [!NOTE]
   > Se a partilha de ficheiros for uma partilha de ficheiros padrão, a dimensão de Partilha de **Ficheiros** não listará a(s) partilha de ficheiros porque as métricas por partilha não estão disponíveis para ações de ficheiros padrão. Os alertas de estrangulamento para as ações de ficheiros padrão serão desencadeados se alguma parte do ficheiro dentro da conta de armazenamento for acelerada e o alerta não identificar qual a partilha de ficheiros que foi acelerada. Uma vez que as métricas por ação não estão disponíveis para ações de ficheiros padrão, a recomendação é ter uma ação de ficheiro por conta de armazenamento.

9. Clique nos **valores** de Dimension drop-down e selecione as ações de ficheiros em que pretende alertar.
10. Defina os **parâmetros** de alerta (valor limiar, operador, granularidade de agregação e frequência de avaliação) e clique em **Fazer**.

    > [!TIP]
    > Se estiver a utilizar um limiar estático, o gráfico métrico pode ajudar a determinar um valor limiar razoável se a parte do ficheiro estiver atualmente a ser estrangulada. Se estiver a utilizar um limiar dinâmico, o gráfico métrico apresentará os limiares calculados com base em dados recentes.

11. Clique **em Adicionar grupos de ação** para adicionar um grupo de **ação** (e-mail, SMS, etc.) ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação.
12. Preencha os **detalhes do Alerta** como o nome da regra de **alerta,** **descrição** e **severidade**.
13. Clique **em Criar regra de alerta** para criar o alerta.

### <a name="how-to-create-an-alert-if-the-azure-file-share-size-is-80-of-capacity"></a>Como criar um alerta se o tamanho da partilha do ficheiro Azure for 80% da capacidade

1. Aceda à sua **conta de armazenamento** no portal **Azure.**
2. Na secção **'Monitorização',** clique em **Alertas** e clique em **+ Nova regra de alerta**.
3. Clique **em Editar o recurso,** selecione o **tipo de recurso De ficheiro** para a conta de armazenamento e, em seguida, clique em **Fazer**. Por exemplo, se o nome da conta de armazenamento `contoso` for, selecione o `contoso/file` recurso.
4. Clique **em Adicionar condição** para adicionar uma condição.
5. Verá uma lista de sinais suportados para a conta de armazenamento, selecione a métrica capacidade de **ficheiro.**
6. Para **ações de ficheiros premium,** clique no **nome Dimension** drop-down e selecione File **Share**. Para **ações de ficheiros padrão**, salte para **o passo #8**. .

   > [!NOTE]
   > Se a partilha de ficheiros for uma partilha de ficheiros padrão, a dimensão de Partilha de **Ficheiros** não listará a(s) partilha de ficheiros porque as métricas por partilha não estão disponíveis para ações de ficheiros padrão. Os alertas para as ações de ficheiros padrão baseiam-se em todas as ações de ficheiros na conta de armazenamento. Uma vez que as métricas por ação não estão disponíveis para ações de ficheiros padrão, a recomendação é ter uma ação de ficheiro por conta de armazenamento.

7. Clique nos **valores** de Dimension drop-down e selecione as ações de ficheiros em que pretende alertar.
8. Introduza o **valor Threshold** em bytes. Por exemplo, se o tamanho da partilha de ficheiros for de 100 TiB e pretender receber um alerta quando o tamanho da ação do ficheiro for de 80% da capacidade, o valor-limiar nos bytes é de 87960930222080.
9. Defina o resto dos **parâmetros** de alerta (granularidade agregação e frequência de avaliação) e clique em **Fazer**.
10. Clique **em Adicionar grupos de ação** para adicionar um grupo de **ação** (e-mail, SMS, etc.) ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação.
11. Preencha os **detalhes do Alerta** como o nome da regra de **alerta,** **descrição** e **severidade**.
12. Clique **em Criar regra de alerta** para criar o alerta.

### <a name="how-to-create-an-alert-if-the-azure-file-share-egress-has-exceeded-500-gib-in-a-day"></a>Como criar um alerta se a saída de partilha de ficheiros Azure excedeu 500 GiB num dia

1. Aceda à sua **conta de armazenamento** no portal **Azure.**
2. Na secção 'Monitorização', clique em **Alertas** e clique em **+ Nova regra de alerta**.
3. Clique **em Editar o recurso,** selecione o **tipo de recurso De ficheiro** para a conta de armazenamento e, em seguida, clique em **Fazer**. Por exemplo, se o nome da conta de armazenamento for contoso, selecione o recurso contoso/ficheiro.
4. Clique **em Adicionar condição** para adicionar uma condição.
5. Verá uma lista de sinais suportados para a conta de armazenamento, selecione a métrica **Egress.**
6. Para **ações de ficheiros premium,** clique no **nome Dimension** drop-down e selecione File **Share**. Para **ações de ficheiros padrão**, salte para **o passo #8**. .

   > [!NOTE]
   > Se a partilha de ficheiros for uma partilha de ficheiros padrão, a dimensão de Partilha de **Ficheiros** não listará a(s) partilha de ficheiros porque as métricas por partilha não estão disponíveis para ações de ficheiros padrão. Os alertas para as ações de ficheiros padrão baseiam-se em todas as ações de ficheiros na conta de armazenamento. Uma vez que as métricas por ação não estão disponíveis para ações de ficheiros padrão, a recomendação é ter uma ação de ficheiro por conta de armazenamento.

7. Clique nos **valores** de Dimension drop-down e selecione as ações de ficheiros em que pretende alertar.
8. Introduza **536870912000** bytes para o valor limiar. 
9. Clique na **granularidade** da agregação e selecione **24 horas**.
10. Selecione a **Frequência da avaliação** e **clique em Fazer**.
11. Clique **em Adicionar grupos de ação** para adicionar um grupo de **ação** (e-mail, SMS, etc.) ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação.
12. Preencha os **detalhes do Alerta** como o nome da regra de **alerta,** **descrição** e **severidade**.
13. Clique **em Criar regra de alerta** para criar o alerta.

## <a name="next-steps"></a>Passos seguintes

- [Azure Files monitorizando a referência de dados](storage-files-monitoring-reference.md)
- [Monitorar recursos do Azure com o Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md)
- [Migração de métricas de armazenamento Azure](../common/storage-metrics-migration.md)
- [Planear uma implementação de Ficheiros do Azure](./storage-files-planning.md)
- [Como implementar os Ficheiros do Azure](./storage-how-to-create-file-share.md)
- [Resolução de problemas dos Ficheiros do Azure no Windows](./storage-troubleshoot-windows-file-connection-problems.md)
- [Resolução de problemas dos Ficheiros do Azure no Linux](./storage-troubleshoot-linux-file-connection-problems.md)
