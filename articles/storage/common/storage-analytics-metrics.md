---
title: Métricas da análise de armazenamento do Azure (clássico)
description: Saiba como usar métricas no armazenamento do Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: ca831fe66a0ce6a2dbfafc54a761b86473067b10
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846897"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Métricas da análise de armazenamento do Azure (clássico)

Análise de Armazenamento pode armazenar métricas que incluem estatísticas de transação agregadas e dados de capacidade sobre solicitações para um serviço de armazenamento. As transações são relatadas no nível de operação da API, bem como no nível de serviço de armazenamento, e a capacidade é relatada no nível de serviço de armazenamento. Os dados de métricas podem ser usados para analisar o uso do serviço de armazenamento, diagnosticar problemas com solicitações feitas no serviço de armazenamento e melhorar o desempenho de aplicativos que usam um serviço.  

 As métricas de Análise de Armazenamento são habilitadas por padrão para novas contas de armazenamento. Você pode configurar as métricas no [portal do Azure](https://portal.azure.com/); para obter detalhes, consulte [monitorar uma conta de armazenamento no portal do Azure](/azure/storage/storage-monitor-storage-account). Você também pode habilitar Análise de Armazenamento programaticamente por meio da API REST ou da biblioteca de cliente. Use as operações definir propriedades de serviço para habilitar o Análise de Armazenamento para cada serviço.  

> [!NOTE]
> Análise de Armazenamento métricas estão disponíveis para os serviços BLOB, fila, tabela e arquivo.
> As métricas de Análise de Armazenamento agora são métricas clássicas. A Microsoft recomenda o uso de [métricas de armazenamento em Azure monitor](storage-metrics-in-azure-monitor.md) em vez de análise de armazenamento métricas.

## <a name="transaction-metrics"></a>Métricas de transação  
 Um conjunto robusto de dados é registrado em intervalos de hora ou minutos para cada serviço de armazenamento e operação de API solicitada, incluindo entrada/saída, disponibilidade, erros e percentuais de solicitação categorizados. Você pode ver uma lista completa dos detalhes da transação no tópico [análise de armazenamento tabela de métricas esquema](/rest/api/storageservices/storage-analytics-metrics-table-schema) .  

 Os dados de transação são registrados em dois níveis: o nível de serviço e o nível de operação da API. No nível de serviço, as estatísticas que resumem todas as operações de API solicitadas são gravadas em uma entidade de tabela a cada hora, mesmo que nenhuma solicitação tenha sido feita ao serviço. No nível de operação da API, as estatísticas serão gravadas somente em uma entidade se a operação tiver sido solicitada nessa hora.  

 Por exemplo, se você executar uma operação getBlob em seu serviço Blob, análise de armazenamento métricas registrará a solicitação e a incluirá nos dados agregados para o serviço BLOB, bem como a operação **getBlob** . No entanto, se nenhuma operação getBlob for solicitada durante a hora, uma entidade não será gravada no *$MetricsTransactionsBlob* para essa operação.  

 As métricas de transação são registradas para solicitações de usuário e solicitações feitas por Análise de Armazenamento si mesma. Por exemplo, as solicitações por Análise de Armazenamento para gravar logs e entidades de tabela são registradas.

## <a name="capacity-metrics"></a>Métricas de capacidade  

> [!NOTE]
>  Atualmente, as métricas de capacidade estão disponíveis apenas para o serviço BLOB.

 Os dados de capacidade são gravados diariamente para o serviço blob de uma conta de armazenamento e duas entidades de tabela são gravadas. Uma entidade fornece estatísticas para dados do usuário e a outra fornece estatísticas sobre o `$logs` contêiner de blob usado pelo análise de armazenamento. A tabela *$MetricsCapacityBlob* inclui as seguintes estatísticas:  

- **Capacidade**: A quantidade de armazenamento usada pelo serviço BLOB da conta de armazenamento, em bytes.  
- **ContainerCount**: O número de contêineres de blob no serviço BLOB da conta de armazenamento.  
- **ObjectCount**: O número de blobs de blocos ou páginas confirmados e não confirmados no serviço BLOB da conta de armazenamento.  

  Para obter mais informações sobre as métricas de capacidade, consulte [análise de armazenamento esquema de tabela](/rest/api/storageservices/storage-analytics-metrics-table-schema)de métricas.  

## <a name="how-metrics-are-stored"></a>Como as métricas são armazenadas  

 Todos os dados de métricas para cada um dos serviços de armazenamento são armazenados em três tabelas reservadas para esse serviço: uma tabela para informações de transação, uma tabela para informações de transação de minuto e outra tabela para informações de capacidade. As informações de transação de transação e de minuto consistem em dados de solicitação e resposta, e as informações de capacidade consistem em dados de uso de armazenamento. As métricas de hora, as métricas de minuto e a capacidade para o serviço blob de uma conta de armazenamento podem ser acessadas em tabelas nomeadas conforme descrito na tabela a seguir.  

|Nível de métricas|Nomes de tabela|Com suporte para versões|  
|-------------------|-----------------|----------------------------|  
|Métricas por hora, local principal|-$MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-$MetricsTransactionsQueue|Versões anteriores a 2013-08-15 apenas. Embora esses nomes ainda tenham suporte, é recomendável que você alterne para o usando as tabelas listadas abaixo.|  
|Métricas por hora, local principal|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|Todas as versões. O suporte para métricas de serviço de arquivo está disponível somente na versão 2015-04-05 e posterior.|  
|Métricas de minutos, localização primária|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|Todas as versões. O suporte para métricas de serviço de arquivo está disponível somente na versão 2015-04-05 e posterior.|  
|Métricas por hora, local secundário|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|Todas as versões. A replicação com redundância geográfica com acesso de leitura deve estar habilitada.|  
|Métricas de minuto, local secundário|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|Todas as versões. A replicação com redundância geográfica com acesso de leitura deve estar habilitada.|  
|Capacidade (somente serviço BLOB)|$MetricsCapacityBlob|Todas as versões.|  

 Essas tabelas são criadas automaticamente quando Análise de Armazenamento está habilitado para um ponto de extremidade de serviço de armazenamento. Eles são acessados por meio do namespace da conta de armazenamento, `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`por exemplo:. As tabelas de métricas não aparecem em uma operação de listagem e devem ser acessadas diretamente por meio do nome da tabela.  

## <a name="enable-metrics-using-the-azure-portal"></a>Habilitar métricas usando o portal do Azure
Siga estas etapas para habilitar as métricas no [portal do Azure](https://portal.azure.com):

1. Navegue até à sua conta de armazenamento.
1. Selecione **configurações de diagnóstico (clássico)** no painel de **menus** .
1. Verifique se o **status** está definido como **ativado**.
1. Selecione as métricas para os serviços que você deseja monitorar.
1. Especifique uma política de retenção para indicar por quanto tempo as métricas e os dados de log são retidos.
1. Selecione **Guardar**.

O [portal do Azure](https://portal.azure.com) atualmente não permite que você configure métricas de minutos em sua conta de armazenamento; Você deve habilitar as métricas de minuto usando o PowerShell ou programaticamente.

> [!NOTE]
>  Observe que o portal do Azure atualmente não permite que você configure as métricas de minuto em sua conta de armazenamento. Você deve habilitar as métricas de minuto usando o PowerShell ou programaticamente.

## <a name="enable-storage-metrics-using-powershell"></a>Habilitar métricas de armazenamento usando o PowerShell  
Você pode usar o PowerShell em seu computador local para configurar as métricas de armazenamento em sua conta de armazenamento usando o cmdlet Azure PowerShell **Get-AzureStorageServiceMetricsProperty** para recuperar as configurações atuais e o cmdlet  **Set-AzureStorageServiceMetricsProperty** para alterar as configurações atuais.  

Os cmdlets que controlam as métricas de armazenamento usam os seguintes parâmetros:  

* **ServiceType**, o valor possível são **blob**, **fila**, **tabela**e **arquivo**.
* **Valorespossíveis metricstype**, os valores possíveis são **Hour** e **minute**.  
* **MetricsLevel**, os valores possíveis são:
* **Nenhum**: Desativa o monitoramento.
* **Serviço**: Coleta métricas como entrada/saída, disponibilidade, latência e porcentagens de êxito, que são agregadas para os serviços BLOB, fila, tabela e arquivo.
* **ServiceAndApi**: Além das métricas de serviço, o coleta o mesmo conjunto de métricas para cada operação de armazenamento na API do serviço de armazenamento do Azure.

Por exemplo, o comando a seguir alterna as métricas de minuto para o serviço blob em sua conta de armazenamento com o período de retenção definido como cinco dias: 

> [!NOTE]
> Esse comando pressupõe que você tenha entrado em sua assinatura do Azure usando `Connect-AzAccount` o comando.

```  
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* Substitua o `<resource-group-name>` valor do espaço reservado pelo nome do seu grupo de recursos.

* Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da sua conta de armazenamento.



O comando a seguir recupera o nível de métrica por hora atual e os dias de retenção para o serviço blob em sua conta de armazenamento padrão:  

```  
Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Para obter informações sobre como configurar os cmdlets Azure PowerShell para trabalhar com sua assinatura do Azure e como selecionar a conta de armazenamento padrão a ser usada, consulte: [Como instalar e configurar Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Habilitar métricas de armazenamento programaticamente  
Além de usar os cmdlets portal do Azure ou Azure PowerShell para controlar as métricas de armazenamento, você também pode usar uma das APIs de armazenamento do Azure. Por exemplo, se você estiver usando uma linguagem .NET, poderá usar a biblioteca de cliente de armazenamento.  

As classes **CloudBlobClient**, **CloudQueueClient**, **CloudTableClient**e **CloudFileClient** têm métodos como setserviceproperties e **SetServicePropertiesAsync** que usam umObjeto serviceproperties como um parâmetro. Você pode usar o objeto serviceproperties para configurar as métricas de armazenamento. Por exemplo, o trecho C# a seguir mostra como alterar o nível de métricas e os dias de retenção para as métricas de fila por hora:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Para obter mais informações sobre como usar uma linguagem .NET para configurar as métricas de armazenamento, consulte [biblioteca de cliente de armazenamento para .net](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

Para obter informações gerais sobre como configurar as métricas de armazenamento usando a API REST, consulte [Habilitando e Configurando análise de armazenamento](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

##  <a name="viewing-storage-metrics"></a>Exibindo métricas de armazenamento  
Depois de configurar as métricas de Análise de Armazenamento para monitorar sua conta de armazenamento, Análise de Armazenamento registra as métricas em um conjunto de tabelas conhecidas em sua conta de armazenamento. Você pode configurar gráficos para exibir métricas por hora no [portal do Azure](https://portal.azure.com):

1. Navegue até sua conta de armazenamento no [portal do Azure](https://portal.azure.com).
1. Selecione **métricas (clássicas)** na folha do **menu** para o serviço cujas métricas você deseja exibir.
1. Clique no gráfico que você deseja configurar.
1. Na folha **Editar gráfico** , selecione o **intervalo de tempo**, o **tipo de gráfico**e as métricas que você deseja exibir no gráfico.

Na seção **monitoramento (clássico)** da folha do menu da sua conta de armazenamento no portal do Azure, você pode configurar [regras de alerta](#metrics-alerts), como enviar alertas de email para notificá-lo quando uma métrica específica atingir um determinado valor.

Se você quiser baixar as métricas para armazenamento de longo prazo ou analisá-las localmente, deverá usar uma ferramenta ou escrever algum código para ler as tabelas. Você deve baixar as métricas de minuto para análise. As tabelas não serão exibidas se você listar todas as tabelas em sua conta de armazenamento, mas puder acessá-las diretamente pelo nome. Muitas ferramentas de navegação de armazenamento estão cientes dessas tabelas e permitem exibi-las diretamente (consulte [ferramentas de cliente de armazenamento do Azure](/azure/storage/storage-explorers) para obter uma lista de ferramentas disponíveis).

||||  
|-|-|-|  
|**Métricas**|**Nomes de tabela**|**Notas**|  
|Métricas por hora|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|Nas versões anteriores à 2013-08-15, essas tabelas eram conhecidas como:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> As métricas para o serviço de arquivo estão disponíveis a partir da versão 2015-04-05.|  
|Métricas de minuto|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Só pode ser habilitado usando o PowerShell ou programaticamente.<br /><br /> As métricas para o serviço de arquivo estão disponíveis a partir da versão 2015-04-05.|  
|Capacidade|$MetricsCapacityBlob|Somente serviço BLOB.|  

Você pode encontrar detalhes completos dos esquemas para essas tabelas em [análise de armazenamento esquema de tabela](/rest/api/storageservices/storage-analytics-metrics-table-schema)de métricas. As linhas de exemplo a seguir mostram apenas um subconjunto das colunas disponíveis, mas ilustram alguns recursos importantes da maneira como as métricas de armazenamento salvam essas métricas:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**RowKey**|**Estampa**|**TotalRequests**|**TotalBillableRequests**|**TotalIngress**|**TotalEgress**|**Disponibilidade**|**AverageE2ELatency**|**AverageServerLatency**|**PercentSuccess**|  
|20140522T1100|usuário Os|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|usuário QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

Neste exemplo de dados de métricas de minuto, a chave de partição usa a resolução de tempo em minutos. A chave de linha identifica o tipo de informações que são armazenadas na linha e isso é composto por duas partes de informações, o tipo de acesso e o tipo de solicitação:  

-   O tipo de acesso é **usuário** ou **sistema**, em que o **usuário** se refere a todas as solicitações de usuário para o serviço de armazenamento e o **sistema** se refere a solicitações feitas por análise de armazenamento.  

-   O tipo de solicitação é **tudo** que, nesse caso, é uma linha de resumo ou identifica a API específica, como **QueryEntity** ou **UpdateEntity**.  

Os dados de exemplo acima mostram todos os registros de um único minuto (começando em 11:10:00), portanto, o número de solicitações de **QueryEntities** mais o número de solicitações de **QueryEntity** mais o número de solicitações de **UpdateEntity** somam-se a sete, que é o total mostrado na linha **usuário: todas** . Da mesma forma, você pode derivar a latência média de ponta a ponta 104,4286 na linha **usuário: todas** calculando ((143,8 * 5) + 3 + 9)/7.  

## <a name="metrics-alerts"></a>Alertas de métricas
Você deve considerar a configuração de alertas no [portal do Azure](https://portal.azure.com) para que você seja notificado automaticamente sobre alterações importantes no comportamento de seus serviços de armazenamento. Se você usar uma ferramenta do Gerenciador de armazenamento para baixar esses dados de métrica em um formato delimitado, poderá usar o Microsoft Excel para analisar os dados. Consulte [ferramentas de cliente de armazenamento do Azure](/azure/storage/storage-explorers) para obter uma lista de ferramentas disponíveis do Gerenciador de armazenamento. Você pode configurar alertas na folha **alerta (clássico)** , acessível em **monitoramento (clássico)** na folha do menu da conta de armazenamento.

> [!IMPORTANT]
> Pode haver um atraso entre um evento de armazenamento e quando os dados de métricas por hora ou minuto correspondentes são registrados. No caso de métricas de minutos, vários minutos de dados podem ser gravados ao mesmo tempo. Isso pode levar a transações de minutos anteriores que estão sendo agregadas à transação para o minuto atual. Quando isso acontece, o serviço de alerta pode não ter todos os dados de métricas disponíveis para o intervalo de alerta configurado, o que pode levar a acionamento inesperado de alertas.
>

## <a name="accessing-metrics-data-programmatically"></a>Acessando dados de métricas programaticamente  
A listagem a seguir mostra C# o código de exemplo que acessa as métricas de minuto para um intervalo de minutos e exibe os resultados em uma janela de console. O exemplo de código usa a biblioteca de cliente de armazenamento do Azure versão 4. x ou posterior, que inclui a classe **CloudAnalyticsClient** que simplifica o acesso às tabelas de métricas no armazenamento.  

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey  
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

     // Filter on "user" transactions after fetching the metrics from Table Storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage)  
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

## <a name="billing-on-storage-metrics"></a>Cobrança sobre métricas de armazenamento  
As solicitações de gravação para criar entidades de tabela para métricas são cobradas com as taxas padrão aplicáveis a todas as operações de armazenamento do Azure.  

As solicitações de leitura e exclusão de dados de métricas por um cliente também são faturáveis com tarifas padrão. Se você tiver configurado uma política de retenção de dados, não será cobrado quando o armazenamento do Azure excluir dados de métricas antigos. No entanto, se você excluir dados de análise, sua conta será cobrada pelas operações de exclusão.  

A capacidade usada pelas tabelas de métricas também é faturável. Você pode usar o seguinte para estimar a quantidade de capacidade usada para armazenar dados de métricas:  

-   Se a cada hora um serviço utilizar cada API em cada serviço, aproximadamente 148KB de dados serão armazenados a cada hora nas tabelas de transação de métricas se você tiver habilitado o resumo no nível de serviço e de API.  
-   Se, em cada hora, um serviço utilizar cada API no serviço, aproximadamente 12KB de dados serão armazenados a cada hora nas tabelas de transação de métricas se você tiver habilitado apenas o resumo de nível de serviço.  
-   A tabela de capacidade para BLOBs tem duas linhas adicionadas por dia, desde que você tenha optado por logs. Isso implica que, todos os dias, o tamanho dessa tabela aumenta em até cerca de 300 bytes.

## <a name="next-steps"></a>Passos Seguintes
* [Como monitorar uma conta de armazenamento](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Esquema de tabela de métricas Análise de Armazenamento](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Análise de Armazenamento mensagens de status e operações registradas](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Log de Análise de Armazenamento](storage-analytics-logging.md)
