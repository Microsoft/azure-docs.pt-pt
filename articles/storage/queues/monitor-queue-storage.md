---
title: Monitorização do armazenamento da fila Azure
description: Saiba como monitorizar o desempenho e disponibilidade do Azure Queue Storage. Monitorize os dados de Armazenamento da Fila Azure, aprenda sobre a configuração e analise dados métricos e de registo.
author: normesta
services: storage
ms.author: normesta
ms.reviewer: fryu
ms.date: 10/26/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: queues
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 18991f83bfb365d1ced141fa44267502671854b8
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588299"
---
# <a name="monitoring-azure-queue-storage"></a>Monitorização do armazenamento da fila Azure

Quando você tem aplicações críticas e processos de negócio que dependem de recursos Azure, você quer monitorizar esses recursos para sua disponibilidade, desempenho e funcionamento. Este artigo descreve os dados de monitorização gerados pelo Azure Queue Storage e como pode utilizar as funcionalidades do Azure Monitor para analisar alertas sobre estes dados.

> [!NOTE]
> Os registos de armazenamento Azure no Azure Monitor estão em pré-visualização pública e estão disponíveis para testes de pré-visualização em todas as regiões de nuvem pública. Esta pré-visualização permite registos para bolhas (que inclui Azure Data Lake Storage Gen2), ficheiros, filas e tabelas. Esta funcionalidade encontra-se disponível para todas as contas de armazenamento que são criadas com o modelo de implementação do Azure Resource Manager. Consulte [a visão geral da conta de armazenamento](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Visão geral do monitor

A página **geral** no portal Azure para cada recurso de Armazenamento de Fila inclui uma breve visão da utilização do recurso, tais como pedidos e faturação horária. Esta informação é útil, mas apenas uma pequena quantidade dos dados de monitorização está disponível. Alguns destes dados são recolhidos automaticamente e estão disponíveis para análise assim que criar o recurso. Pode ativar tipos adicionais de recolha de dados com alguma configuração.

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?

O Azure Queue Storage cria dados de monitorização utilizando [o Azure Monitor,](../../azure-monitor/overview.md)que é um serviço de monitorização de pilhas completa em Azure. O Azure Monitor fornece um conjunto completo de funcionalidades para monitorizar os seus recursos Azure, bem como recursos em outras nuvens e no local.

Comece com [os recursos do Monitor Azure com o Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md) que descreve o seguinte:

- O que é o Azure Monitor?
- Custos associados à monitorização
- Dados de monitorização recolhidos em Azure
- Recolha de dados configurado
- Ferramentas padrão em Azure para analisar e alertar sobre os dados de monitorização

As seguintes secções baseiam-se neste artigo descrevendo os dados específicos recolhidos a partir do Azure Storage. Exemplos mostram como configurar a recolha de dados e analisar estes dados com ferramentas Azure.

## <a name="monitoring-data"></a>Monitorizar dados

O Azure Queue Storage recolhe os mesmos tipos de dados de monitorização que outros recursos Azure, que são descritos na [monitorização de dados a partir de recursos Azure](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data).

Consulte [a referência de dados de monitorização do armazenamento da fila Azure](monitor-queue-storage-reference.md) para obter informações detalhadas sobre as métricas de métricas e registos criadas pelo Azure Queue Storage.

Métricas e registos no Azure Monitor suportam apenas contas de armazenamento do Gestor de Recursos Azure. O Azure Monitor não suporta contas clássicas de armazenamento. Se quiser utilizar métricas ou registos numa conta de armazenamento clássica, tem de migrar para uma conta de armazenamento do Azure Resource Manager. Consulte [a Migração para o Gestor de Recursos Azure](../../virtual-machines/migration-classic-resource-manager-overview.md).

Pode continuar a usar métricas e registos clássicos, se quiser. Na verdade, métricas e troncos clássicos estão disponíveis em paralelo com métricas e troncos no Azure Monitor. O suporte permanece em vigor até que o Azure Storage termine o serviço em métricas e registos antigos.

## <a name="collection-and-routing"></a>Recolha e encaminhamento

As métricas da plataforma e o registo de atividade são recolhidos automaticamente, mas podem ser encaminhados para outros locais utilizando uma definição de diagnóstico.

Para recolher registos de recursos, tem de criar uma definição de diagnóstico. Quando criar a definição, escolha **a fila** como o tipo de armazenamento para o qual pretende ativar os registos. Em seguida, especifique uma das seguintes categorias de operações para as quais pretende recolher registos.

| Categoria | Descrição |
|:---|:---|
| **ArmazenamentoRead** | Leia as operações em objetos. |
| **ArmazenamentoPorite** | Escreva operações em objetos. |
| **StorageDelete** | Eliminar operações em objetos. |

## <a name="creating-a-diagnostic-setting"></a>Criação de uma definição de diagnóstico

Pode criar uma definição de diagnóstico utilizando o portal Azure, o PowerShell, o Azure CLI ou um modelo de Gestor de Recursos Azure.

Para obter orientações gerais, consulte [Criar definição de diagnóstico para recolher registos e métricas da plataforma em Azure](../../azure-monitor/platform/diagnostic-settings.md).

> [!NOTE]
> Os registos de armazenamento Azure no Azure Monitor estão em pré-visualização pública e estão disponíveis para testes de pré-visualização em todas as regiões de nuvem pública. Esta pré-visualização permite registos para bolhas (que inclui Azure Data Lake Storage Gen2), ficheiros, filas e tabelas. Esta funcionalidade encontra-se disponível para todas as contas de armazenamento que são criadas com o modelo de implementação do Azure Resource Manager. Consulte [a visão geral da conta de armazenamento](../common/storage-account-overview.md).

### <a name="azure-portal"></a>[Portal do Azure](#tab/azure-portal)

1. Inicie sessão no portal do Azure.

2. Navegue até à sua conta de armazenamento.

3. Na secção **de Monitorização,** clique nas **definições de Diagnóstico (pré-visualização)**.

   > [!div class="mx-imgBorder"]
   > ![portal - Registos de diagnóstico](media/monitor-queue-storage/diagnostic-logs-settings-pane.png)

4. Escolha **a fila** como o tipo de armazenamento para o qual pretende ativar os registos.

5. Clique **na definição de diagnóstico de adicionar**.

   > [!div class="mx-imgBorder"]
   > ![portal - Registos de recursos - adicione a definição de diagnóstico](media/monitor-queue-storage/diagnostic-logs-settings-pane-2.png)

   Aparece a página **de definições de Diagnóstico.**

   > [!div class="mx-imgBorder"]
   > ![Página de registos de recursos](media/monitor-queue-storage/diagnostic-logs-page.png)

6. No campo **Nome** da página, insira um nome para esta definição de registo de recursos. Em seguida, selecione quais as operações que pretende registadas (ler, escrever e apagar operações) e para onde pretende que os registos sejam enviados.

#### <a name="archive-logs-to-a-storage-account"></a>Registos de arquivo para uma conta de armazenamento

Se optar por arquivar os seus registos numa conta de armazenamento, pagará o volume de registos que são enviados para a conta de armazenamento. Para obter preços **específicos,** consulte a secção de Registos de Plataforma da página de preços do [Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

1. Selecione o Arquivo para uma caixa de verificação **de conta de armazenamento** e, em seguida, selecione o botão **Configure.**

   > [!div class="mx-imgBorder"]
   > ![Armazenamento de arquivo de página de definições de diagnóstico](media/monitor-queue-storage/diagnostic-logs-settings-pane-archive-storage.png)

2. Na lista de down-down da **conta De armazenamento,** selecione a conta de armazenamento para a que pretende arquivar os seus registos, clique no botão **OK** e, em seguida, selecione o botão **Guardar.**

   > [!NOTE]
   > Antes de escolher uma conta de armazenamento como destino de exportação, consulte [os registos de recursos do Archive Azure](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage) para entender os pré-requisitos na conta de armazenamento.

#### <a name="stream-logs-to-azure-event-hubs"></a>Registos de streaming para Azure Event Hubs

Se optar por transmitir os seus registos para um centro de eventos, pagará o volume de registos que são enviados para o centro de eventos. Para obter preços **específicos,** consulte a secção de Registos de Plataforma da página de preços do [Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

1. Selecione o Stream para uma caixa de verificação **do centro de eventos** e, em seguida, selecione o botão **Configure.**

2. No painel **de centros de eventos Selecione um painel de centros** de eventos, escolha o espaço de nome, nome e nome da política do centro de eventos para o qual pretende transmitir os seus registos.

   > [!div class="mx-imgBorder"]
   > ![Centro de evento de página de definições de diagnóstico](media/monitor-queue-storage/diagnostic-logs-settings-pane-event-hub.png)

3. Clique no botão **OK** e, em seguida, selecione o botão **Guardar.**

#### <a name="send-logs-to-azure-log-analytics"></a>Enviar registos para Azure Log Analytics

1. Selecione a caixa de verificação **Enviar para Registar Analytics,** selecione um espaço de trabalho Log Analytics e, em seguida, selecione o botão **Guardar.**

   > [!div class="mx-imgBorder"]
   > ![Análise de registo de página de definições de diagnóstico](media/monitor-queue-storage/diagnostic-logs-settings-pane-log-analytics.png)

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
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -StorageAccountId <storage-account-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Substitua o `<storage-service-resource--id>` espaço reservado neste corte com o ID de recurso da fila. Pode encontrar o ID de recursos no portal Azure abrindo a página **Propriedades** da sua conta de armazenamento.

Pode `StorageRead` `StorageWrite` utilizar, e `StorageDelete` pelo valor do parâmetro **Categoria.**

Eis um exemplo:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Para obter uma descrição de cada parâmetro, consulte [os registos de recursos do Archive Azure através da Azure PowerShell](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Registos de fluxo para um centro de eventos

Se optar por transmitir os seus registos para um centro de eventos, pagará o volume de registos que são enviados para o centro de eventos. Para obter preços **específicos,** consulte a secção de Registos de Plataforma da página de preços do [Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Ativar os registos utilizando o [cmdlet de definição de](/powershell/module/az.monitor/set-azdiagnosticsetting) diagnósticos elétricos com o `EventHubAuthorizationRuleId` parâmetro.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Eis um exemplo:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

Para obter uma descrição de cada parâmetro, consulte [os dados do Stream para Os Centros de Eventos através de cmdlets PowerShell](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Enviar registos para o Log Analytics

Ativar os registos utilizando o [cmdlet de definição de](/powershell/module/az.monitor/set-azdiagnosticsetting) diagnósticos elétricos com o `WorkspaceId` parâmetro.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Eis um exemplo:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

Para obter mais informações, consulte [os registos de recursos do Stream Azure para registar o espaço de trabalho no Azure Monitor](../../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Primeiro, abra o [Azure Cloud Shell](../../cloud-shell/overview.md), ou se [instalou o Azure CLI](/cli/azure/install-azure-cli) localmente, abra uma aplicação de consola de comando como PowerShell.

2. Se a sua identidade estiver associada a mais de uma subscrição, então desa estale a sua subscrição ativa para a subscrição da conta de armazenamento para a qual pretende ativar os registos.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Substitua o `<subscription-id>` valor do espaço reservado pelo ID da sua subscrição.

#### <a name="archive-logs-to-a-storage-account"></a>Registos de arquivo para uma conta de armazenamento

Se optar por arquivar os seus registos numa conta de armazenamento, pagará o volume de registos que são enviados para a conta de armazenamento. Para obter preços **específicos,** consulte a secção de Registos de Plataforma da página de preços do [Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Ativar os registos utilizando o [`az monitor diagnostic-settings create`](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) comando.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Substitua o `<storage-service-resource--id>` espaço reservado neste corte com o ID de recurso da fila. Pode encontrar o ID de recursos no portal Azure abrindo a página **Propriedades** da sua conta de armazenamento.

Pode `StorageRead` `StorageWrite` utilizar, e `StorageDelete` pelo valor do `category` parâmetro.

Eis um exemplo:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/queueServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true, "retentionPolicy": {"days": 90, "enabled": true}}]'`

Para obter uma descrição de cada parâmetro, consulte [os registos de recursos do Arquivo através do CLI Azure](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Registos de fluxo para um centro de eventos

Se optar por transmitir os seus registos para um centro de eventos, pagará o volume de registos que são enviados para o centro de eventos. Para obter preços **específicos,** consulte a secção de Registos de Plataforma da página de preços do [Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Ativar os registos utilizando o [`az monitor diagnostic-settings create`](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) comando.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Eis um exemplo:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/queueServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

Para obter uma descrição de cada parâmetro, consulte [os dados do Stream para Os Centros de Eventos via Azure CLI](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Enviar registos para o Log Analytics

Ativar os registos utilizando o [`az monitor diagnostic-settings create`](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) comando.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Eis um exemplo:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/queueServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 Para obter mais informações, consulte [os registos de recursos do Stream Azure para registar o espaço de trabalho no Azure Monitor](../../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).

# <a name="template"></a>[Modelo](#tab/template)

Para ver um modelo do Gestor de Recursos Azure que cria uma definição de diagnóstico, consulte [a definição de Diagnóstico para armazenamento Azure](../../azure-monitor/samples/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage).

---

## <a name="analyzing-metrics"></a>Análise de métricas

Pode analisar métricas para armazenamento Azure com métricas de outros serviços Azure usando O Azure Metrics Explorer. Open Metrics Explorer escolhendo **métricas** do menu **Azure Monitor.** Para obter detalhes sobre a utilização desta ferramenta, consulte [Começar com o Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md).

Este exemplo mostra como ver **transações** ao nível da conta.

![Screenshot de acesso às métricas no portal Azure](./media/monitor-queue-storage/access-metrics-portal.png)

Para métricas que suportam dimensões, pode filtrar a métrica com o valor de dimensão pretendido. Este exemplo mostra como visualizar **as Transações** ao nível da conta numa operação específica, selecionando valores para a dimensão **nome da API.**

![Screenshot de acesso a métricas com dimensão no portal Azure](./media/monitor-queue-storage/access-metrics-portal-with-dimension.png)

Para obter uma lista completa das dimensões que o Azure Storage suporta, consulte [as dimensões das Métricas.](monitor-queue-storage-reference.md#metrics-dimensions)

As métricas para o armazenamento da fila Azure estão nestes espaços de nome:

- Microsoft.Storage/storageAcontas
- Microsoft.Storage/storageAcontas/filas Serviços

Para obter uma lista de todas as métricas de suporte do Azure Monitor, que inclui o armazenamento da fila Azure, consulte [as métricas suportadas pelo Monitor Azure.](../../azure-monitor/platform/metrics-supported.md)

### <a name="accessing-metrics"></a>Métricas de acesso

> [!TIP]
> Para ver exemplos de Azure CLI ou .NET, escolha os separadores correspondentes listados aqui.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Listar a definição métrica

Pode listar a definição métrica da sua conta de armazenamento ou do serviço de armazenamento de filas. Utilize o [cmdlet Get-AzMetricDefinition.](/powershell/module/az.monitor/get-azmetricdefinition)

Neste exemplo, substitua o `<resource-ID>` espaço reservado pelo ID de recursos de toda a conta de armazenamento ou pelo ID de recursos da fila. Pode encontrar estes IDs de recursos nas páginas **Propriedades** da sua conta de armazenamento no portal Azure.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Valores métricos de leitura

Pode ler os valores métricos ao nível da conta da sua conta de armazenamento ou o serviço de Armazenamento de Fila. Utilize o [cmdlet Get-AzMetric.](/powershell/module/az.monitor/get-azmetric)

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Listar a definição métrica de nível de conta

Pode listar a definição métrica da sua conta de armazenamento ou do serviço de armazenamento de filas. Usa o [`az monitor metrics list-definitions`](/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions) comando.

Neste exemplo, substitua o `<resource-ID>` espaço reservado pelo ID de recursos de toda a conta de armazenamento ou pelo ID de recursos da fila. Pode encontrar estes IDs de recursos nas páginas **Propriedades** da sua conta de armazenamento no portal Azure.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Leia os valores métricos ao nível da conta

Pode ler os valores métricos da sua conta de armazenamento ou do serviço de Armazenamento de Fila. Usa o [`az monitor metrics list`](/cli/azure/monitor/metrics#az-monitor-metrics-list) comando.

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/azure-portal)

O Azure Monitor fornece o [.NET SDK](https://www.nuget.org/packages/microsoft.azure.management.monitor/) para ler definições e valores métricos. O [código de amostra](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) mostra como utilizar o SDK com diferentes parâmetros. Você precisa usar `0.18.0-preview` ou uma versão posterior para métricas de armazenamento.

Nestes exemplos, substitua o `<resource-ID>` espaço reservado pelo ID de recursos de toda a conta de armazenamento ou da fila. Pode encontrar estes IDs de recursos nas páginas **Propriedades** da sua conta de armazenamento no portal Azure.

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
        // Resource ID for queue storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default";
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

### <a name="template"></a>[Modelo](#tab/template)

N/D.

---

## <a name="analyzing-logs"></a>Análise de registos

Pode aceder a registos de recursos como uma fila numa conta de armazenamento, como dados de eventos, ou através de consultas de Log Analytics.

Para obter uma referência detalhada dos campos que aparecem nestes registos, consulte [a referência de dados de monitorização do armazenamento da fila Azure](monitor-queue-storage-reference.md).

> [!NOTE]
> Os registos de armazenamento Azure no Azure Monitor estão em pré-visualização pública e estão disponíveis para testes de pré-visualização em todas as regiões de nuvem pública. Esta pré-visualização permite registos para blobs (que inclui Azure Data Lake Storage Gen2), ficheiros, filas, tabelas, contas de armazenamento premium em v1 de uso geral e contas de armazenamento v2 de uso geral. As contas clássicas de armazenamento não são suportadas.

As entradas de registo só são criadas se houver pedidos feitos contra o ponto final de serviço. Por exemplo, se uma conta de armazenamento tiver atividade no seu ponto final de fila, mas não na sua mesa ou pontos finais blob, apenas são criados registos relativos ao Armazenamento de Fila. Os registos de armazenamento Azure contêm informações detalhadas sobre pedidos bem sucedidos e falhados para um serviço de armazenamento. Estas informações podem ser utilizadas para monitorizar os pedidos individuais e diagnosticar problemas num serviço de armazenamento. Os pedidos são registados numa base de melhor esforço.

### <a name="log-authenticated-requests"></a>Registar pedidos autenticados

São registados os seguintes tipos de pedidos autenticados:

- Pedidos com êxito
- Pedidos falhados, incluindo tempo limite, limitação, rede, autorização e outros erros
- Pedidos que utilizem uma assinatura de acesso partilhado (SAS) ou OAuth, incluindo pedidos falhados e bem sucedidos
- Pedidos de dados de análise (dados clássicos **de** registo no $logs dados métricos do recipiente e da classe nas tabelas **$metric)**

Os pedidos feitos pelo próprio serviço de Armazenamento de Fila, como criação de registos ou eliminação, não são registados. Para obter uma lista completa dos dados registados, consulte [as operações registadas do Armazenamento e as mensagens de estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e o formato de registo de [armazenamento.](monitor-queue-storage-reference.md)

### <a name="log-anonymous-requests"></a>Registar pedidos anónimos

Os seguintes tipos de pedidos anónimos são registados:

- Pedidos com êxito
- Erros de servidor
- Erros de tempo para o cliente e para o servidor
- Pedidos `GET` falhados com o código de erro 304 ( `Not Modified` )

Todos os outros pedidos anónimos falhados não estão registados. Para obter uma lista completa dos dados registados, consulte [as operações registadas do Armazenamento e as mensagens de estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e o formato de registo de [armazenamento.](monitor-queue-storage-reference.md)

### <a name="accessing-logs-in-a-storage-account"></a>Aceder a registos numa conta de armazenamento

Os registos aparecem como bolhas armazenadas num contentor na conta de armazenamento do alvo. Os dados são recolhidos e armazenados dentro de uma única bolha como uma carga de carga JSON delimitada pela linha. O nome da bolha segue esta convenção de nomeação:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/queueServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Eis um exemplo:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Aceder a troncos num centro de eventos

Os registos enviados para um centro de eventos não são armazenados como um ficheiro, mas pode verificar se o centro de eventos recebeu a informação de registo. No portal Azure, vá ao seu centro de eventos e verifique se a `incoming requests` contagem é maior do que zero.

![Registos de auditoria](media/monitor-queue-storage/event-hub-log.png)

Pode aceder e ler dados de registo que são enviados para o seu centro de eventos utilizando informações de segurança e ferramentas de gestão e monitorização de eventos. Para mais informações, veja [o que posso fazer com os dados de monitorização que estão a ser enviados para o meu centro de eventos?](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Aceder a registos num espaço de trabalho do Log Analytics

Pode aceder aos registos enviados para um espaço de trabalho do Log Analytics utilizando consultas de registo do Azure Monitor.

Para obter mais informações, consulte [Começar com o Log Analytics no Azure Monitor](../../azure-monitor/log-query/log-analytics-tutorial.md).

Os dados são armazenados na `StorageQueueLogs` tabela.

#### <a name="sample-kusto-queries"></a>Experimente consultas kusto

Aqui ficam algumas consultas que pode introduzir na barra **de pesquisa log** para ajudá-lo a monitorizar as suas filas. Estas consultas funcionam com a [nova linguagem.](../../azure-monitor/log-query/log-query-overview.md)

> [!IMPORTANT]
> Quando seleciona **Logs** do menu do grupo de recursos de conta de armazenamento, o Log Analytics é aberto com o âmbito de consulta definido para o grupo de recursos atual. Isto significa que as consultas de registo só incluirão dados desse grupo de recursos. Se pretender executar uma consulta que inclua dados de outros recursos ou dados de outros serviços Azure, selecione **Logs** do menu **Azure Monitor.** Consulte [o âmbito de consulta de registo e o intervalo de tempo no Azure Monitor Log Analytics](../../azure-monitor/log-query/scope.md) para obter mais detalhes.

Utilize estas consultas para ajudá-lo a monitorizar as suas contas de Armazenamento Azure:

- Para enumerar os 10 erros mais comuns nos últimos três dias.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```

- Para listar as 10 operações que causaram mais erros nos últimos três dias.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```

- Para listar as 10 melhores operações com a maior latência de ponta a ponta nos últimos três dias.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```

- Para listar todas as operações que causaram erros de estrangulamento do lado do servidor nos últimos três dias.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```

- Para listar todos os pedidos com acesso anónimo nos últimos três dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```

- Para criar um gráfico de operações usado nos últimos três dias.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc
    | render piechart
    ```

## <a name="faq"></a>FAQ

**O Azure Storage suporta métricas para discos geridos ou discos não geridos?**

N.º As instâncias de cálculo suportam as métricas nos discos. Para obter mais informações, consulte [as métricas de disco para discos geridos e não geridos.](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)

## <a name="next-steps"></a>Passos seguintes

- Para obter uma referência dos registos e métricas criados pelo Azure Queue Storage, consulte [a referência de dados de monitorização do armazenamento da fila Azure](monitor-queue-storage-reference.md).
- Para obter informações sobre a monitorização dos recursos do Azure, consulte [os recursos do Monitor Azure com o Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md).
- Para obter mais informações sobre a migração de métricas, consulte [a migração das métricas de Armazenamento Azure.](../common/storage-metrics-migration.md)
