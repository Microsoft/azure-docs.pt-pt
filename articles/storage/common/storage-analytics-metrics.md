---
title: Métricas de Análise de Armazenamento Azure (clássico)
description: Saiba como utilizar as métricas de Storage Analytics no Armazenamento Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 5613453667e3bb278f4da22ebed4502def70235b
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83675894"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Métricas de Análise de Armazenamento Azure (clássico)

O Azure Storage utiliza a solução Storage Analytics para armazenar métricas que incluem estatísticas de transações agregadas e dados de capacidade sobre pedidos a um serviço de armazenamento. As transações são reportadas ao nível da operação API e ao nível do serviço de armazenamento. A capacidade é reportada ao nível do serviço de armazenamento. Os dados das métricas podem ser utilizados para:
- Analise o uso do serviço de armazenamento.
- Diagnosticar problemas com pedidos feitos contra o serviço de armazenamento.
- Melhorar o desempenho das aplicações que utilizam um serviço.

 As métricas de Armazenamento Analytics são ativadas por padrão para novas contas de armazenamento. Pode configurar métricas no [portal Azure.](https://portal.azure.com/) Para mais informações, consulte Monitor uma conta de [armazenamento no portal Azure](/azure/storage/storage-monitor-storage-account). Também pode ativar o Storage Analytics programaticamente através da API REST ou da biblioteca do cliente. Utilize as operações de set Service Properties para ativar o Storage Analytics para cada serviço.  

> [!NOTE]
> As métricas de Armazenamento Analytics estão disponíveis para armazenamento Azure Blob, armazenamento de fila Azure, armazenamento de mesa Azure e Ficheiros Azure.
> As métricas de Armazenamento Analytics são agora métricas clássicas. Recomendamos que utilize métricas de [armazenamento no Monitor Azure](monitor-storage.md) em vez de métricas de Storage Analytics.

## <a name="transaction-metrics"></a>Métricas de transação  
 Um conjunto robusto de dados é registado em intervalos de hora ou minuto para cada serviço de armazenamento e solicitado operação API, que inclui entrada e saída, disponibilidade, erros e percentagens de pedidos categorizadas. Para obter uma lista completa dos detalhes da transação, consulte o esquema de tabela de [métricas de Armazenamento Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

 Os dados de transação são registados ao nível do serviço e ao nível de operação da API. Ao nível do serviço, as estatísticas que resumem todas as operações solicitadas da API são escritas a uma entidade de mesa a cada hora, mesmo que não tenham sido feitos pedidos ao serviço. Ao nível da operação DaPI, as estatísticas só são escritas a uma entidade se a operação for solicitada dentro dessa hora.  

 Por exemplo, se executar uma operação **GetBlob** no seu serviço de blob, o Storage Analytics Metrics regista o pedido e inclui-o nos dados agregados para o serviço blob e a operação **GetBlob.** Se nenhuma operação **GetBlob** for solicitada durante a hora, uma entidade não é escrita para *$MetricsTransactionsBlob* para essa operação.  

 As métricas de transação são registadas para pedidos de utilizador e pedidos feitos pela própria Storage Analytics. Por exemplo, são registados pedidos da Storage Analytics para escrever registos e entidades de mesa.

## <a name="capacity-metrics"></a>Métricas de capacidade  

> [!NOTE]
>  Atualmente, as métricas de capacidade estão disponíveis apenas para o serviço de blob.

 Os dados de capacidade são registados diariamente para o serviço de blob de uma conta de armazenamento, e duas entidades de tabela são escritas. Uma entidade fornece estatísticas para os dados dos utilizadores, e a outra fornece estatísticas sobre o `$logs` recipiente blob usado pela Storage Analytics. O quadro *$MetricsCapacityBlob* inclui as seguintes estatísticas:  

- **Capacidade**: A quantidade de armazenamento utilizada pelo serviço de blob da conta de armazenamento, em bytes.  
- **Contagem de contentores**: O número de recipientes blob no serviço de blob da conta de armazenamento.  
- **ObjectCount**: O número de blocos ou bolhas de página comprometidos e não comprometidos no serviço de blob da conta de armazenamento.  

  Para obter mais informações sobre métricas de capacidade, consulte O esquema de tabela de [métricas de Armazenamento Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Como as métricas são armazenadas  

 Todos os dados métricos de cada um dos serviços de armazenamento são armazenados em três tabelas reservadas para esse serviço. Uma tabela é para informações de transações, uma tabela é para informações de transações minúsculas, e outra tabela é para informações de capacidade. As informações de transação e de transação minuciosa consistem em dados de pedido e resposta. A informação sobre a capacidade consiste em dados de utilização de armazenamento. Métricas de hora, métricas minúsculas e capacidade para o serviço de blob de uma conta de armazenamento é acessado em tabelas que são nomeadas como descrito na tabela seguinte.  

|Nível de métricas|Nomes de mesa|Suportado para versões|  
|-------------------|-----------------|----------------------------|  
|Métricas horárias, localização primária|- $MetricsTransactionsBlob<br />- $MetricsTransactionsTable<br />- $MetricsTransactionsQueue|Versões antes de 15 de agosto de 2013, apenas. Embora estes nomes ainda sejam suportados, recomendamos que mude para a utilização das tabelas que se seguem.|  
|Métricas horárias, localização primária|- $MetricsHourPrimaryTransactionsBlob<br />$MetricsHourPrimaryTransactionsTable<br />- $MetricsHourPrimaryTransactionsQueue<br />- $MetricsHourPrimaryTransactionsFile|Todas as versões. O suporte para métricas de serviço de ficheiros só está disponível na versão 5 de abril de 2015, e mais tarde.|  
|Métricas minúsculas, localização primária|- $MetricsMinutePrimaryTransactionsBlob<br />- $MetricsMinutePrimaryTransactionsTable<br />- $MetricsMinutePrimaryTransactionsQueue<br />$MetricsMinutePrimaryTransactionsFile|Todas as versões. O suporte para métricas de serviço de ficheiros só está disponível na versão 5 de abril de 2015, e mais tarde.|  
|Métricas horárias, localização secundária|- $MetricsHourSecondaryTransactionsBlob<br />- $MetricsHourSecondaryTransactionsTable<br />$MetricsHourSecondaryTransactionsQueue|Todas as versões. A replicação georedundantde de acesso de leitura deve ser ativada.|  
|Métricas minúsculas, localização secundária|- $MetricsMinuteSecondaryTransactionsBlob<br />- $MetricsMinuteSecondaryTransactionsTable<br />- $MetricsMinuteSecondaryTransactionsQueue|Todas as versões. A replicação georedundantde de acesso de leitura deve ser ativada.|  
|Capacidade (apenas serviço de blob)|$MetricsCapacityBlob|Todas as versões.|  

 Estas tabelas são automaticamente criadas quando o Storage Analytics está ativado para um ponto final do serviço de armazenamento. São acedidos através do espaço de nome da conta de armazenamento, por exemplo, `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")` . As tabelas de métricas não aparecem numa operação de listagem e devem ser acedidas diretamente através do nome da tabela.

## <a name="enable-metrics-by-using-the-azure-portal"></a>Ativar métricas utilizando o portal Azure
Siga estes passos para permitir métricas no [portal Azure:](https://portal.azure.com)

1. Vá para a sua conta de armazenamento.
1. Selecione **definições de Diagnóstico (clássica)** no painel de menus.
1. Certifique-se de que o **Estado** está definido para **.**.
1. Selecione as métricas para os serviços que pretende monitorizar.
1. Especifique uma política de retenção para indicar quanto tempo reter métricas e registar dados.
1. Selecione **Guardar**.

O [portal Azure](https://portal.azure.com) não lhe permite configurar métricas minúsculas na sua conta de armazenamento. Deve ativar métricas minúsculas utilizando o PowerShell ou programáticamente.

## <a name="enable-storage-metrics-by-using-powershell"></a>Ativar métricas de armazenamento utilizando o PowerShell  
Pode utilizar o PowerShell na sua máquina local para configurar métricas de armazenamento na sua conta de armazenamento utilizando o cmdlet **Get-AzStorageServiceMetricsProperty** do Azure PowerShell para recuperar as definições atuais. Utilize o cmdlet **Set-AzStorageServiceMetricsProperty** para alterar as definições atuais.  

Os cmdlets que controlam as métricas de armazenamento utilizam os seguintes parâmetros:  

* **Tipo de serviço**: Os valores possíveis são **Blob,** **Queue,** **Table,** Table e **File**.
* **Tipo métrica:** Os valores possíveis são **hora** e **minuto**.  
* **MetricsLevel**: Os valores possíveis são:
   * **Nenhum:** desliga a monitorização.
   * **Serviço**: Recolhe métricas como entrada e saída, disponibilidade, latência e percentagens de sucesso, que são agregadas para os serviços de blob, fila, mesa e ficheiros.
   * **ServiçoAndApi**: Para além das métricas de serviço, recolhe o mesmo conjunto de métricas para cada operação de armazenamento na API do serviço de armazenamento Azure.

Por exemplo, os seguintes interruptores de comando em métricas minúsculas para o serviço de blob na sua conta de armazenamento com o período de retenção definido para cinco dias: 

> [!NOTE]
> Este comando pressupõe que assinou a sua assinatura Azure usando o `Connect-AzAccount` comando.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* Substitua o valor do `<resource-group-name>` espaço reservado pelo nome do seu grupo de recursos.      
* Substitua o valor do `<storage-account-name>` espaço reservado pelo nome da sua conta de armazenamento.



O seguinte comando recupera o nível de métricas atuais e os dias de retenção para o serviço de blob na sua conta de armazenamento predefinido:  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Para obter informações sobre como configurar os cmdlets Azure PowerShell para trabalhar com a sua subscrição Azure e como selecionar a conta de armazenamento predefinida para usar, consulte [Instalar e configurar o PowerShell Azure](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Ativar as métricas de armazenamento programáticamente  
Além de utilizar o portal Azure ou os cmdlets Azure PowerShell para controlar as métricas de armazenamento, também pode utilizar uma das APIs de Armazenamento Azure. Por exemplo, se utilizar uma linguagem .NET pode utilizar a biblioteca de clientes Azure Storage.  

As classes **CloudBlobClient,** **CloudQueueClient,** **CloudTableClient**e **CloudFileClient** têm métodos como **SetServiceProperties** e **SetServicePropertiesAsync** que tomam um objeto **ServiceProperties** como parâmetro. Pode utilizar o objeto **ServiceProperties** para configurar métricas de armazenamento. Por exemplo, o seguinte corte C# mostra como alterar o nível de métricas e os dias de retenção para as métricas de fila horária:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Para obter mais informações sobre a utilização de um idioma .NET para configurar métricas de armazenamento, consulte [as bibliotecas de clientes do Azure Storage para .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

Para obter informações gerais sobre a configuração das métricas de armazenamento utilizando a API REST, consulte [Aanálise de Armazenamento habilitação e configuração](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

##  <a name="view-storage-metrics"></a>Ver métricas de armazenamento  
Depois de configurar as métricas do Storage Analytics para monitorizar a sua conta de armazenamento, o Storage Analytics regista as métricas num conjunto de tabelas bem conhecidas na sua conta de armazenamento. Pode configurar gráficos para visualizar métricas horárias no [portal Azure:](https://portal.azure.com)

1. Vá à sua conta de armazenamento no [portal Azure.](https://portal.azure.com)
1. Selecione **Métricas (clássicas)** no painel de menus para o serviço cujas métricas pretende ver.
1. Selecione o gráfico que pretende configurar.
1. No painel do Gráfico de **Edição,** selecione a **gama de tempo,** o **tipo Gráfico,** e as métricas que pretende exibidas na tabela.

Na secção **de Monitorização (clássica)** do menu da sua conta de armazenamento no portal Azure, pode configurar [as regras](#metrics-alerts)de Alerta . Por exemplo, pode enviar alertas de e-mail para notificá-lo quando uma métrica específica atinge um determinado valor.

Se quiser descarregar as métricas para armazenamento a longo prazo ou analisá-las localmente, deve usar uma ferramenta ou escrever algum código para ler as tabelas. Tem de descarregar as métricas minúsculas para análise. As tabelas não aparecem se listar todas as tabelas na sua conta de armazenamento, mas pode acessá-las diretamente pelo nome. Muitas ferramentas de navegação de armazenamento estão cientes destas tabelas e permitem vê-las diretamente. Para obter uma lista de ferramentas disponíveis, consulte [as ferramentas do cliente do Azure Storage.](/azure/storage/storage-explorers)

||||  
|-|-|-|  
|**Métricas**|**Nomes de mesa**|**Notas**|  
|Métricas horárias|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|Em versões anteriores a 15 de agosto de 2013, estas tabelas eram conhecidas como:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> As métricas para o serviço de ficheiros estão disponíveis a partir da versão 5 de abril de 2015.|  
|Métricas minúsculas|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Só pode ser ativado utilizando o PowerShell ou programáticamente.<br /><br /> As métricas para o serviço de ficheiros estão disponíveis a partir da versão 5 de abril de 2015.|  
|Capacidade|$MetricsCapacityBlob|Só serviço blob.|  

Para obter todos os detalhes dos esquemas destas tabelas, consulte o esquema de tabela de [métricas de Armazenamento Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema). As seguintes linhas de amostra mostram apenas um subconjunto das colunas disponíveis, mas ilustram algumas características importantes da forma como as métricas de armazenamento salvam estas métricas:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**RowKey**|**Carimbo de data/hora**|**TotalRequests**|**TotalBillableRequests**|**TotalIngress**|**TotalEgress**|**Disponibilidade**|**AverageE2ELatency**|**AverageServerLatency**|**PercentSuccess**|  
|2014052T1100|utilizador; Todos|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|2014052T1100|utilizador; ConsultasEntidades|2014-05-22T11:01:16.7640250z|5|5|2694|45951|100|143.8|7.8|100|  
|2014052T1100|utilizador; ConsultaEntidade|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|2014052T1100|utilizador; UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

Neste exemplo de dados de métricas minúsculas, a chave de partição utiliza o tempo em resolução minúscula. A chave da linha identifica o tipo de informação que está armazenada na fila. A informação é composta pelo tipo de acesso e pelo tipo de pedido:  

-   O tipo de acesso é **utilizador** ou **sistema,** onde **o utilizador** se refere a todos os pedidos do utilizador para o serviço de armazenamento e **o sistema** refere-se a pedidos feitos pelo Storage Analytics.  
-   O tipo de pedido é **todo,** caso em que se trata de uma linha sumária, ou identifica a API específica, como **QueryEntity** ou **UpdateEntity**.  

Estes dados da amostra mostram todos os registos durante um único minuto (a partir das 11:00), pelo que o número de pedidos da **QueryEntities** mais o número de pedidos da **QueryEntity** mais o número de pedidos da **UpdateEntity** soma até sete. Este total é mostrado no **utilizador:Toda** a linha. Da mesma forma, pode obter a latência média de ponta a ponta 104.4286 no **utilizador:Todas as** linhas calculando ((143,8 * 5) + 3 + 9)/7.  

## <a name="metrics-alerts"></a>Alertas de métricas
Considere configurar alertas no [portal Azure](https://portal.azure.com) para que seja automaticamente notificado de mudanças importantes no comportamento dos seus serviços de armazenamento. Se utilizar uma ferramenta Do Storage Explorer para descarregar estes dados de métricas num formato delimitado, pode utilizar o Microsoft Excel para analisar os dados. Para obter uma lista das ferramentas disponíveis do Storage Explorer, consulte [as ferramentas do cliente do Azure Storage.](/azure/storage/storage-explorers) Pode configurar alertas no painel **Alerta (clássico),** que é acessível sob **monitorização (clássica)** no painel de menus da conta de armazenamento.

> [!IMPORTANT]
> Pode haver um atraso entre um evento de armazenamento e quando os dados correspondentes das métricas horárias ou minúsculas são registados. No caso de métricas minúsculas, vários minutos de dados podem ser escritos de uma só vez. Esta emissão pode levar a que as transações a partir de minutos anteriores sejam agregadas na transação durante o momento atual. Quando este problema acontece, o serviço de alerta pode não ter todos os dados de métricas disponíveis para o intervalo de alerta configurado, o que pode levar a alertas disparando inesperadamente.
>

## <a name="access-metrics-data-programmatically"></a>Dados de métricas de acesso programática  
A listagem seguinte mostra o código C# da amostra que acede às métricas minúsculas durante uma série de minutos e exibe os resultados numa janela da consola. A amostra de código utiliza a versão 4.x ou posterior da biblioteca do cliente Azure Storage, que inclui a classe **CloudAnalyticsClient** que simplifica o acesso às tabelas de métricas no armazenamento.  

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

## <a name="billing-on-storage-metrics"></a>Faturação em métricas de armazenamento
Escreva pedidos para criar entidades de tabela para métricas são cobrados às tarifas padrão aplicáveis a todas as operações de Armazenamento Azure.  

Ler e eliminar pedidos de dados métricos por um cliente também são faturados a taxas padrão. Se configurar uma política de retenção de dados, não é cobrado quando o Azure Storage elimina dados de métricas antigas. Se eliminar dados de análise, a sua conta é cobrada para as operações de eliminação.  

A capacidade utilizada pelas tabelas métricas também é faturada. Utilize as seguintes informações para estimar a quantidade de capacidade utilizada para armazenar dados métricos:  

-   Se cada hora um serviço utilizar todas as API em cada serviço, aproximadamente 148 KB de dados são armazenados a cada hora nas tabelas de transações de métricas se você ativar um nível de serviço e um resumo de nível de API.  
-   Se dentro de cada hora um serviço utilizar todas as API do serviço, aproximadamente 12 KB de dados são armazenados a cada hora nas tabelas de transações de métricas se você ativar apenas um resumo de nível de serviço.  
-   A tabela de capacidade para bolhas tem duas linhas adicionadas por dia, desde que tenha optado por registos. Este cenário implica que todos os dias o tamanho desta tabela aumenta até cerca de 300 bytes.

## <a name="next-steps"></a>Passos seguintes
* [Monitorizar uma conta de armazenamento](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Esquema de mesa de métricas de análise de armazenamento](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Armazenamento Analytics registrado operações e mensagens de estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Armazenamento Analytics logging](storage-analytics-logging.md)
