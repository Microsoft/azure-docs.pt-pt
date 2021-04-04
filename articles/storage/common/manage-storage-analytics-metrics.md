---
title: Ativar e gerir as métricas Azure Storage Analytics (clássicas) | Microsoft Docs
description: Saiba como ativar, editar e ver as métricas Azure Storage Analytics.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 784929e50d25a07ae92cf388be5ac14f6fa820a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99221644"
---
# <a name="enable-and-manage-azure-storage-analytics-metrics-classic"></a>Ativar e gerir as métricas Azure Storage Analytics (clássico)

[A Azure Storage Analytics](storage-analytics.md) fornece métricas para todos os serviços de armazenamento para bolhas, filas e mesas. Pode utilizar o [portal Azure](https://portal.azure.com) para configurar quais as métricas que são gravadas para a sua conta e configurar gráficos que fornecem representações visuais dos dados das suas métricas. Este artigo mostra-lhe como ativar e gerir métricas. Para aprender a ativar registos, consulte [Ativar e gerir registos Azure Storage Analytics (clássico)](manage-storage-analytics-logs.md).

Recomendamos que reveja [o Monitor Azure para armazenamento](../../azure-monitor/insights/storage-insights-overview.md) (pré-visualização). É uma funcionalidade do Azure Monitor que oferece uma monitorização abrangente das suas contas de Armazenamento Azure, proporcionando uma visão unificada do desempenho, capacidade e disponibilidade dos seus serviços de armazenamento Azure. Não requer que você ative ou configuure nada, e você pode imediatamente ver estas métricas a partir dos gráficos interativos pré-definidos e outras visualizações incluídas.

> [!NOTE]
> Existem custos associados à análise de dados de monitorização no portal Azure. Para obter mais informações, veja [Análise de Armazenamento](storage-analytics.md).
>
> As contas de armazenamento de blocos de desempenho premium não suportam métricas de Storage Analytics. Se pretender visualizar métricas com contas de armazenamento de blocos de desempenho premium, considere a utilização de [Métricas de Armazenamento Azure no Azure Monitor](../blobs/monitor-blob-storage.md).
>
> Para obter um guia aprofundado sobre a utilização de Storage Analytics e outras ferramentas para identificar, diagnosticar e resolver problemas relacionados com o Armazenamento Azure, consulte [Monitor, diagnóstico e resolução de problemas do Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).
>

<a id="Enable-metrics"></a>

## <a name="enable-metrics"></a>Ativar métricas

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal Azure](https://portal.azure.com), selecione **Contas de Armazenamento,** em seguida, o nome da conta de armazenamento para abrir o dashboard da conta.

2. Selecione **as definições de diagnóstico (clássica)** na secção **de Monitorização (clássica)** da lâmina do menu.
   
   ![Screenshot que realça a opção de definições de Diagnóstico (clássico) na secção Monitor (Classic).](./media/manage-storage-analytics-metrics/storage-enable-metrics-00.png)

3. Selecione o **tipo** de dados de métricas para cada **serviço** que deseja monitorizar e a **política de retenção** para os dados. Também pode desativar a monitorização definindo **o Estado** para **desligar**.

   > [!div class="mx-imgBorder"]
   > ![Configurar o registo no portal Azure.](./media/manage-storage-analytics-logs/enable-diagnostics.png) 

   Para definir a política de retenção de dados, mova o slider **de Retenção (dias)** ou introduza o número de dias de dados para reter, de 1 a 365. O incumprimento das novas contas de armazenamento é de sete dias. Se não quiser definir uma política de retenção, insira zero. Se não houver uma política de retenção, cabe-lhe a si eliminar os dados de monitorização.

   > [!WARNING]
   > As metics são armazenadas como dados na sua conta. Os dados métricos podem acumular-se na sua conta ao longo do tempo, o que pode aumentar o custo de armazenamento. Se precisar de dados métricos por apenas um pequeno período de tempo, pode reduzir os seus custos modificando a política de retenção de dados. Os dados de métricas obsoletos (dados mais antigos do que a sua política de retenção) são eliminados pelo sistema. Recomendamos a definição de uma política de retenção com base no tempo que pretende reter os dados das métricas da sua conta. Consulte [a Faturação nas métricas de armazenamento](storage-analytics-metrics.md#billing-on-storage-metrics) para obter mais informações.
   >

4. Quando terminar a configuração de monitorização, **selecione Guardar**.

Um conjunto predefinido de métricas é apresentado em gráficos na lâmina **de visão geral,** bem como na lâmina **métrica (clássica).** Uma vez ativadas as métricas para um serviço, pode levar até uma hora para os dados aparecerem nas suas tabelas. Pode selecionar **Editar** em qualquer gráfico métrico para configurar quais as métricas que são apresentadas na tabela.

Pode desativar a recolha e o registo de métricas definindo **status** to **Off**.

> [!NOTE]
> O Azure Storage utiliza [o armazenamento de mesa](storage-introduction.md#table-storage) para armazenar as métricas da sua conta de armazenamento e armazena as métricas nas tabelas na sua conta. Para mais informações, consulte: [Como as métricas são armazenadas.](storage-analytics-metrics.md#how-metrics-are-stored)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Abra uma janela de comando Windows PowerShell.

2. Inicie sessão na sua subscrição do Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

   ```powershell
   Connect-AzAccount
   ```

3. Se a sua identidade estiver associada a mais de uma subscrição, então desa estale a sua subscrição ativa.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Substitua o `<subscription-id>` valor do espaço reservado pelo ID da sua subscrição.

5. Obtenha o contexto da conta de armazenamento que define a conta de armazenamento que pretende utilizar.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Substitua o `<resource-group-name>` valor do espaço reservado pelo nome do seu grupo de recursos.

   * Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da sua conta de armazenamento. 

6. Pode utilizar o PowerShell na sua máquina local para configurar métricas de armazenamento na sua conta de armazenamento. Utilize o conjunto de cmdlet **AzStorageServiceMetricss para** alterar as definições atuais. 

   O comando que se segue liga as métricas minúsculas para o serviço blob na sua conta de armazenamento com o período de retenção definido para cinco dias.

   ```powershell
   Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $ctx
   ```   

   Este cmdlet utiliza os seguintes parâmetros:  

   - **ServiceType**: Os valores possíveis são **Blob,** **Queue,** **Table** e **File**.
   - **MétricasType**: Os valores possíveis são **hora** e **minuto**.  
   - **MetricsLevel**: Os valores possíveis são:
      - **Nenhum:** Desliga a monitorização.
      - **Serviço**: Recolhe métricas como entradas e saídas, disponibilidade, latência e percentagens de sucesso, que são agregadas para os serviços de blob, fila, mesa e arquivo.
      - **ServiçoAndApi**: Para além das métricas de serviço, recolhe o mesmo conjunto de métricas para cada operação de armazenamento na API do serviço de armazenamento Azure.

   O seguinte comando recupera o nível de métricas e os dias de retenção atuais para o serviço blob na sua conta de armazenamento predefinido:  

   ```powershell
   Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
   ```  

   Para obter informações sobre como configurar os cmdlets Azure PowerShell para trabalhar com a sua subscrição Azure e como selecionar a conta de armazenamento predefinida para utilizar, consulte [instalar e configurar a Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

Para obter mais informações sobre a utilização de uma língua .NET para configurar métricas de armazenamento, consulte [as bibliotecas de clientes do Azure Storage para .NET](/dotnet/api/overview/azure/storage).  

Para obter informações gerais sobre a configuração das métricas de armazenamento utilizando a API REST, consulte [ativar e configurar o Storage Analytics](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics). 

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Para obter mais informações sobre a utilização de uma língua .NET para configurar métricas de armazenamento, consulte [as bibliotecas de clientes do Azure Storage para .NET](/dotnet/api/overview/azure/storage).  

Para obter informações gerais sobre a configuração das métricas de armazenamento utilizando a API REST, consulte [ativar e configurar o Storage Analytics](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics). 

---

<a id="view-metrics"></a>

## <a name="view-metrics-in-a-chart"></a>Ver métricas num gráfico

Depois de configurar métricas de Storage Analytics para monitorizar a sua conta de armazenamento, o Storage Analytics regista as métricas num conjunto de tabelas bem conhecidas na sua conta de armazenamento. Pode configurar gráficos para visualizar métricas de hora em hora no [portal Azure](https://portal.azure.com).

Utilize o seguinte procedimento para escolher quais as métricas de armazenamento a visualizar num gráfico de métricas.

1. Comece por apresentar um gráfico métrico de armazenamento no portal Azure. Pode encontrar gráficos na lâmina da **conta de armazenamento** e na lâmina **métrica (clássica).**

   Neste exemplo, utiliza o seguinte gráfico que aparece na lâmina da **conta de armazenamento:**

   ![Seleção de gráficos no portal Azure](./media/manage-storage-analytics-metrics/stg-customize-chart-00.png)

2. Clique em qualquer lugar dentro da tabela para editar o gráfico.

3. Em seguida, selecione o intervalo de **tempo** das métricas a visualizar na tabela, e o **serviço** (blob, fila, mesa, arquivo) cujas métricas pretende apresentar. Aqui, as métricas da semana passada são selecionadas para exibir para o serviço blob:

   ![Intervalo de tempo e seleção de serviço na lâmina editar gráfico](./media/manage-storage-analytics-metrics/storage-edit-metric-time-range.png)

4. Selecione as **métricas** individuais que gostaria de exibir na tabela e, em seguida, clique em **OK**.

   ![Seleção métrica individual na lâmina do gráfico de edição](./media/manage-storage-analytics-metrics/storage-edit-metric-selections.png)

As definições do seu gráfico não afetam a recolha, agregação ou armazenamento de dados de monitorização na conta de armazenamento.

#### <a name="metrics-availability-in-charts"></a>Disponibilidade de métricas em gráficos

A lista de métricas disponíveis muda com base no serviço que escolheu no drop-down, e no tipo de unidade do gráfico que está a editar. Por exemplo, só pode selecionar métricas percentuais como *PercentNetworkError* e *PercentThrottlingError* apenas se estiver a editar um gráfico que apresente unidades em percentagem:

![Solicite gráfico de percentagem de erro no portal Azure](./media/manage-storage-analytics-metrics/stg-customize-chart-04.png)

#### <a name="metrics-resolution"></a>Resolução de métricas

As métricas **selecionadas** no Diagnóstico determinam a resolução das métricas disponíveis para a sua conta:

* A monitorização **agregada** fornece métricas tais como ingress/egress, disponibilidade, latência e percentagens de sucesso. Estas métricas são agregadas a partir dos serviços de bolha, mesa, arquivo e fila.
* **A API** fornece uma resolução mais fina, com métricas disponíveis para operações individuais de armazenamento, para além dos agregados ao nível do serviço.

## <a name="download-metrics-to-archive-or-analyze-locally"></a>Descarregue métricas para arquivar ou analisar localmente

Se pretender descarregar as métricas para armazenamento a longo prazo ou analisá-las localmente, deve utilizar uma ferramenta ou escrever algum código para ler as tabelas. As tabelas não aparecem se listar todas as tabelas da sua conta de armazenamento, mas pode acessá-las diretamente pelo nome. Muitas ferramentas de navegação de armazenamento estão cientes destas tabelas e permitem vê-las diretamente. Para obter uma lista de ferramentas disponíveis, consulte as [ferramentas do cliente do Azure Storage](./storage-explorers.md).

|Métricas|Nomes de tabelas|Notas| 
|-|-|-|  
|Métricas horárias|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|Em versões anteriores a 15 de agosto de 2013, estas tabelas eram conhecidas como:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> As métricas do serviço de ficheiros estão disponíveis a partir da versão 5 de abril de 2015.|  
|Métricas minúsculas|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Só pode ser ativado utilizando o PowerShell ou programático.<br /><br /> As métricas do serviço de ficheiros estão disponíveis a partir da versão 5 de abril de 2015.|  
|Capacidade|$MetricsCapacityBlob|Serviço blob apenas.|  

Para obter todos os detalhes dos esquemas para estas tabelas, consulte o esquema de [tabela de métricas storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema). As seguintes linhas de amostra mostram apenas um subconjunto das colunas disponíveis, mas ilustram algumas características importantes da forma como as métricas de armazenamento salvam estas métricas:  

|PartitionKey|RowKey|Timestamp|TotalRequests|TotalBillableRequests|TotalIngress|TotalEgress|Disponibilidade|AverageE2ELatency|AverageServerLatency|PercentSuccess| 
|-|-|-|-|-|-|-|-|-|-|-|  
|20140522T1100|utilizador; Todos os|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|utilizador; Consultas|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|utilizador; Entidade de Consultaria|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|utilizador; Entidade de Atualização|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

Neste exemplo de dados de métricas minúsculas, a tecla de partição utiliza o tempo em resolução minúscula. A chave da linha identifica o tipo de informação que é armazenada na fila. A informação é composta pelo tipo de acesso e pelo tipo de pedido:  

-   O tipo de acesso é **utilizador** ou **sistema**, onde **o utilizador** se refere a todos os pedidos do utilizador ao serviço de armazenamento e **sistema** refere-se a pedidos feitos pela Storage Analytics.  
-   O tipo de pedido é **ou todos**, nesse caso é uma linha sumária, ou identifica a API específica, como **a QueryEntity** ou **a UpdateEntity.**  

Estes dados da amostra mostram todos os registos por um único minuto (a partir das 11:00 AM), pelo que o número de pedidos de **Consultas** mais o número de pedidos da **QueryEntity** mais o número de pedidos da Entidade de **Atualização** soma sete. Este total é mostrado no **utilizador:Todas as** linhas. Da mesma forma, pode obter a latência média de ponta a ponta 104.4286 no **utilizador:Todas as** linhas calculando (143,8 * 5) + 3 + 9)/7.  

## <a name="view-metrics-data-programmatically"></a>Ver dados de métricas programáticamente

A listagem a seguir mostra o código da amostra C# que acede às métricas minúsculas durante uma série de minutos e exibe os resultados numa janela da consola. A amostra de código utiliza a versão 4.x ou posterior da biblioteca do cliente do Azure Storage, que inclui a classe **CloudAnalyticsClient** que simplifica o acesso às tabelas de métricas armazenadas. 

> [!NOTE]
> A classe **CloudAnalyticsClient** não está incluída na biblioteca de clientes de armazenamento Azure Blob v12 para .NET. No **dia 31 de agosto de 2023** serão retiradas as métricas de Storage Analytics, também referidas como *métricas clássicas.* Para obter mais informações, veja o [anúncio oficial](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Se utilizar métricas clássicas, recomendamos que faça a transição para métricas no Azure Monitor antes dessa data. 

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey.  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Azure Table storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage.)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

<a id="add-metrics-to-dashboard"></a>

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Adicione gráficos de métricas ao painel de instrumentos do portal

Pode adicionar gráficos de métricas de Armazenamento Azure para qualquer uma das suas contas de armazenamento no seu painel de instrumentos do portal.

1. Selecione clique **no painel de instrumentos Editar** enquanto vê o seu dashboard no [portal Azure](https://portal.azure.com).
1. Na Galeria de **Azulejos,** selecione **Localizar azulejos por**  >  **tipo.**
1. Selecione   >  **contas de armazenamento de tipo**.
1. Em **Recursos,** selecione a conta de armazenamento cujas métricas pretende adicionar ao painel de instrumentos.
1. Selecione **Monitorização de Categorias.**  >  
1. Arraste e deixe cair o azulejo do gráfico no seu painel de instrumentos para a métrica que gostaria de exibir. Repita todas as métricas que quiser visualizar no painel de instrumentos. Na imagem seguinte, o gráfico "Blobs - Total de pedidos" é destacado como um exemplo, mas todos os gráficos estão disponíveis para colocação no seu painel de instrumentos.

   ![Galeria de azulejos no portal Azure](./media/manage-storage-analytics-metrics/storage-customize-dashboard.png)
1. Selecione **Fazer a personalização** perto do topo do painel de instrumentos quando terminar de adicionar gráficos.

Uma vez adicionados gráficos ao seu painel de instrumentos, pode personalizá-los ainda mais como descrito nas tabelas de métricas de Personalização.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre Storage Analytics, consulte [Storage Analytics](storage-analytics.md) para Storage Analytics.
* [Configurar registos de Armazenamento Analytics](manage-storage-analytics-logs.md).
* Saiba mais sobre o esquema das métricas. Consulte [o esquema de tabela de métricas de Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema).