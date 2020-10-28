---
title: Métricas Azure Storage Analytics (clássico)
description: Aprenda a usar as métricas de Storage Analytics no Azure Storage. Conheça as métricas de transação e capacidade, como as métricas são armazenadas, permitindo métricas, e muito mais.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: f1ab2be598a24a2448fed44742733633a8e0fc8f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92787606"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Métricas Azure Storage Analytics (clássico)

O Azure Storage utiliza a solução Storage Analytics para armazenar métricas que incluem estatísticas de transações agregadas e dados de capacidade sobre pedidos a um serviço de armazenamento. As transações são reportadas ao nível de operação da API e ao nível do serviço de armazenamento. A capacidade é reportada ao nível do serviço de armazenamento. Os dados métricos podem ser utilizados para:
- Analise o uso do serviço de armazenamento.
- Diagnosticar problemas com pedidos feitos contra o serviço de armazenamento.
- Melhorar o desempenho das aplicações que utilizam um serviço.

 Armazenamento As métricas de Analytics são ativadas por padrão para novas contas de armazenamento. Pode configurar métricas no [portal Azure](https://portal.azure.com/). Para mais informações, consulte [uma conta de armazenamento no portal Azure.](./storage-monitor-storage-account.md) Também pode ativar o Storage Analytics programáticamente através da API REST ou da biblioteca do cliente. Utilize as operações De Conjunto De Propriedades de Serviço para ativar o Storage Analytics para cada serviço.  

> [!NOTE]
> As métricas de Storage Analytics estão disponíveis para armazenamento Azure Blob, armazenamento da fila Azure, armazenamento de mesa Azure e Ficheiros Azure.
> Armazenamento As métricas de Analytics são agora métricas clássicas. Recomendamos que utilize [métricas de armazenamento no Azure Monitor](../blobs/monitor-blob-storage.md) em vez de métricas de Storage Analytics.

## <a name="transaction-metrics"></a>Métricas de transação  
 Um conjunto robusto de dados é registado em intervalos de hora ou minuto para cada serviço de armazenamento e operação API solicitada, que inclui entradas e saídas, disponibilidade, erros e percentagens de pedido categorizadas. Para obter uma lista completa dos detalhes da transação, consulte o [esquema da tabela de métricas storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

 Os dados de transação são registados ao nível do serviço e do nível de operação da API. Ao nível do serviço, as estatísticas que resumem todas as operações solicitadas da API são escritas a uma entidade de mesa a cada hora, mesmo que não tenha sido feito nenhum pedido ao serviço. Ao nível da operação da API, as estatísticas só são escritas a uma entidade se a operação tiver sido solicitada dentro dessa hora.  

 Por exemplo, se efetuar uma operação **GetBlob** no seu serviço blob, a Storage Analytics Metrics regista o pedido e inclui-o nos dados agregados para o serviço blob e a operação **GetBlob.** Se não for solicitada nenhuma operação **GetBlob** durante a hora, uma entidade não é escrita para *$MetricsTransactionsBlob* para essa operação.  

 As métricas de transação são registadas para pedidos de utilizador e pedidos feitos pela própria Storage Analytics. Por exemplo, são registados pedidos por Storage Analytics para escrever registos e entidades de tabelas.

## <a name="capacity-metrics"></a>Métricas de capacidade  

> [!NOTE]
>  Atualmente, as métricas de capacidade estão disponíveis apenas para o serviço blob.

 Os dados de capacidade são registados diariamente para o serviço de blob de uma conta de armazenamento, e duas entidades de tabela são escritas. Uma entidade fornece estatísticas para os dados dos utilizadores, e a outra fornece estatísticas sobre o `$logs` recipiente blob utilizado pela Storage Analytics. A *tabela $MetricsCapacityBlob* inclui as seguintes estatísticas:  

- **Capacidade** : A quantidade de armazenamento utilizada pelo serviço blob da conta de armazenamento, em bytes.  
- **Contentor:** O número de recipientes de bolhas no serviço de bolhas da conta de armazenamento.  
- **ObjectCount** : O número de blocos ou bolhas de página comprometidos e não comprometidos no serviço blob da conta de armazenamento.  

  Para obter mais informações sobre as métricas de capacidade, consulte [o esquema da tabela de métricas storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Como as métricas são armazenadas  

 Todos os dados métricos de cada um dos serviços de armazenamento são armazenados em três tabelas reservadas para esse serviço. Uma tabela é para informações de transação, uma tabela é para informações de transação minúsculas, e outra tabela é para informações de capacidade. A informação de transação e de transação minúscula consiste em dados de pedido e resposta. A informação sobre a capacidade consiste em dados de utilização de armazenamento. As métricas de hora, as métricas minúsculas e a capacidade do serviço blob de uma conta de armazenamento são acedidas em tabelas que são indicadas como descritas na tabela seguinte.  

|Nível de métricas|Nomes de tabelas|Suportado para versões|  
|-------------------|-----------------|----------------------------|  
|Métricas horárias, localização primária|- $MetricsTransactionsBlob<br />- $MetricsTransactionsTable<br />- $MetricsTransactionsQueue|Versões anteriores a 15 de agosto de 2013, apenas. Embora estes nomes ainda estejam suportados, recomendamos que mude a utilizar as tabelas que se seguem.|  
|Métricas horárias, localização primária|- $MetricsHourPrimaryTransactionsBlob<br />- $MetricsHourPrimaryTransactionsTable<br />- $MetricsHourPrimaryTransactionsQueue<br />- $MetricsHourPrimaryTransactionsFile|Todas as versões. O suporte para métricas de serviço de ficheiros só está disponível na versão 5 de abril de 2015 e mais tarde.|  
|Métricas minúsculas, localização primária|- $MetricsMinutePrimaryTransactionsBlob<br />- $MetricsMinutePrimaryTransactionsTable<br />- $MetricsMinutePrimaryTransactionsQueue<br />- $MetricsMinutePrimaryTransactionsFile|Todas as versões. O suporte para métricas de serviço de ficheiros só está disponível na versão 5 de abril de 2015 e mais tarde.|  
|Métricas horárias, localização secundária|- $MetricsHourSecondaryTransactionsBlob<br />- $MetricsHourSecondaryTransactionsTable<br />- $MetricsHourSecondaryTransactionsQueue|Todas as versões. A replicação geo-redundante de acesso à leitura deve ser ativada.|  
|Métricas minúsculas, localização secundária|- $MetricsMinuteSecondaryTransactionsBlob<br />- $MetricsMinuteSecondaryTransactionsTable<br />- $MetricsMinuteSecondaryTransactionsQueue|Todas as versões. A replicação geo-redundante de acesso à leitura deve ser ativada.|  
|Capacidade (apenas serviço blob)|$MetricsCapacityBlob|Todas as versões.|  

 Estas tabelas são criadas automaticamente quando o Storage Analytics está ativado para um ponto final de serviço de armazenamento. São acedidos através do espaço de nome da conta de armazenamento, por `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")` exemplo, . As tabelas de métricas não aparecem numa operação de listagem e devem ser acedidas diretamente através do nome da tabela.

## <a name="enable-metrics-by-using-the-azure-portal"></a>Ativar métricas utilizando o portal Azure
Siga estes passos para permitir métricas no [portal Azure:](https://portal.azure.com)

1. Vá para a sua conta de armazenamento.
1. Selecione **definições de Diagnóstico (clássico)** no painel de menus.
1. Certifique-se de que **o estado** está definido para **On** .
1. Selecione as métricas para os serviços que pretende monitorizar.
1. Especifique uma política de retenção para indicar quanto tempo para reter métricas e registar dados.
1. Selecione **Guardar** .

O [portal Azure](https://portal.azure.com) não lhe permite configurar métricas minúsculas na sua conta de armazenamento. Deve ativar as métricas minúsculas utilizando o PowerShell ou programáticamente.

## <a name="enable-storage-metrics-by-using-powershell"></a>Ativar métricas de armazenamento utilizando o PowerShell  
Pode utilizar o PowerShell na sua máquina local para configurar métricas de armazenamento na sua conta de armazenamento utilizando o cmdlet **Get-AzStorageServiceMetricsProperty** para recuperar as definições atuais. Utilize o **cmdlet Set-AzStorageServiceMetricsProperty** para alterar as definições atuais.  

Os cmdlets que controlam as métricas de armazenamento utilizam os seguintes parâmetros:  

* **ServiceType** : Os valores possíveis são **Blob,** **Queue,** **Table** e **File** .
* **MétricasType** : Os valores possíveis são **hora** e **minuto** .  
* **MetricsLevel** : Os valores possíveis são:
   * **Nenhum:** Desliga a monitorização.
   * **Serviço** : Recolhe métricas como entradas e saídas, disponibilidade, latência e percentagens de sucesso, que são agregadas para os serviços de blob, fila, mesa e arquivo.
   * **ServiçoAndApi** : Para além das métricas de serviço, recolhe o mesmo conjunto de métricas para cada operação de armazenamento na API do serviço de armazenamento Azure.

Por exemplo, o seguinte comando liga as métricas minúsculas para o serviço blob na sua conta de armazenamento com o período de retenção definido para cinco dias: 

> [!NOTE]
> Este comando pressupõe que assinou a sua subscrição Azure usando o `Connect-AzAccount` comando.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* Substitua o `<resource-group-name>` valor do espaço reservado pelo nome do seu grupo de recursos.      
* Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da sua conta de armazenamento.



O seguinte comando recupera o nível de métricas e os dias de retenção atuais para o serviço blob na sua conta de armazenamento predefinido:  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Para obter informações sobre como configurar os cmdlets Azure PowerShell para trabalhar com a sua subscrição Azure e como selecionar a conta de armazenamento predefinida para utilizar, consulte [instalar e configurar a Azure PowerShell](/powershell/azure/).  

## <a name="enable-storage-metrics-programmatically"></a>Ativar métricas de armazenamento programáticamente  
Além de utilizar o portal Azure ou os cmdlets Azure PowerShell para controlar as métricas de armazenamento, também pode utilizar uma das APIs de Armazenamento Azure. Por exemplo, se utilizar um idioma .NET pode utilizar a biblioteca do cliente do Azure Storage.  

As classes **CloudBlobClient** , **CloudQueueClient,** **CloudTableClient** e **CloudFileClient** têm métodos como **SetServiceProperties** e **SetServicePropertiesAsync** que tomam um objeto **serviceProperties** como parâmetro. Pode utilizar o objeto **ServiceProperties** para configurar métricas de armazenamento. Por exemplo, o seguinte corte C# mostra como alterar o nível de métricas e os dias de retenção para as métricas de fila horária:  

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

##  <a name="view-storage-metrics"></a>Ver métricas de armazenamento  
Depois de configurar métricas de Storage Analytics para monitorizar a sua conta de armazenamento, o Storage Analytics regista as métricas num conjunto de tabelas bem conhecidas na sua conta de armazenamento. Pode configurar gráficos para visualizar métricas de hora em hora no [portal Azure:](https://portal.azure.com)

1. Aceda à sua conta de armazenamento no [portal Azure.](https://portal.azure.com)
1. Selecione **Métricas (clássicas)** no painel de menus para o serviço cujas métricas pretende ver.
1. Selecione o gráfico que pretende configurar.
1. No painel **de gráficos de edição,** selecione a **gama tempo,** o **tipo gráfico** e as métricas que deseja exibidas na tabela.

Na secção **de Monitorização (clássica)** do painel de menus da sua conta de armazenamento no portal Azure, pode configurar [as regras de Alerta](#metrics-alerts). Por exemplo, pode enviar alertas de e-mail para notificá-lo quando uma métrica específica atinge um determinado valor.

Se pretender descarregar as métricas para armazenamento a longo prazo ou analisá-las localmente, deve utilizar uma ferramenta ou escrever algum código para ler as tabelas. Tem de fazer o download das métricas minúsculas para análise. As tabelas não aparecem se listar todas as tabelas da sua conta de armazenamento, mas pode acessá-las diretamente pelo nome. Muitas ferramentas de navegação de armazenamento estão cientes destas tabelas e permitem vê-las diretamente. Para obter uma lista de ferramentas disponíveis, consulte as [ferramentas do cliente do Azure Storage](./storage-explorers.md).

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

-   O tipo de acesso é **utilizador** ou **sistema** , onde **o utilizador** se refere a todos os pedidos do utilizador ao serviço de armazenamento e **sistema** refere-se a pedidos feitos pela Storage Analytics.  
-   O tipo de pedido é **ou todos** , nesse caso é uma linha sumária, ou identifica a API específica, como **a QueryEntity** ou **a UpdateEntity.**  

Estes dados da amostra mostram todos os registos por um único minuto (a partir das 11:00 AM), pelo que o número de pedidos de **Consultas** mais o número de pedidos da **QueryEntity** mais o número de pedidos da Entidade de **Atualização** soma sete. Este total é mostrado no **utilizador:Todas as** linhas. Da mesma forma, pode obter a latência média de ponta a ponta 104.4286 no **utilizador:Todas as** linhas calculando (143,8 * 5) + 3 + 9)/7.  

## <a name="metrics-alerts"></a>Alertas de métricas
Considere configurar alertas no [portal Azure para](https://portal.azure.com) que seja automaticamente notificado de mudanças importantes no comportamento dos seus serviços de armazenamento. Se utilizar uma ferramenta Do Explorador de Armazenamento para descarregar estes dados métricos num formato delimitado, pode utilizar o Microsoft Excel para analisar os dados. Para obter uma lista das ferramentas disponíveis do Storage Explorer, consulte as [ferramentas do cliente do Azure Storage](./storage-explorers.md). Pode configurar alertas no painel **de alerta (clássico),** que é acessível em **Monitorização (clássica)** no painel do menu da conta de armazenamento.

> [!IMPORTANT]
> Pode haver um atraso entre um evento de armazenamento e quando os dados de métricas de hora ou minuto correspondentes são registados. No caso de métricas minúsculas, vários minutos de dados podem ser escritos de uma só vez. Esta emissão pode levar a que as transações de minutos anteriores sejam agregadas na transação para o minuto em curso. Quando este problema acontece, o serviço de alerta pode não ter todos os dados métricos disponíveis para o intervalo de alerta configurado, o que pode levar a alertas disparando inesperadamente.
>

## <a name="access-metrics-data-programmatically"></a>Aceder a dados de métricas programáticamente  
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

## <a name="billing-on-storage-metrics"></a>Faturação em métricas de armazenamento
Os pedidos de escrita para a criação de entidades de tabela para métricas são cobrados às taxas padrão aplicáveis a todas as operações de Armazenamento Azure.  

Ler e apagar pedidos de dados de métricas por um cliente também são faturais a taxas padrão. Se configurar uma política de retenção de dados, não é cobrado quando o Azure Storage elimina dados de métricas antigas. Se eliminar dados de análise, a sua conta é cobrada para as operações de eliminação.  

A capacidade utilizada pelas tabelas de métricas também é faturada. Utilizar as seguintes informações para estimar a quantidade de capacidade utilizada para armazenar dados de métricas:  

-   Se cada hora um serviço utilizar cada API em cada serviço, aproximadamente 148 KB de dados é armazenado a cada hora nas tabelas de transações de métricas se você ativar um resumo de nível de serviço e nível API.  
-   Se dentro de cada hora um serviço utilizar cada API no serviço, aproximadamente 12 KB de dados é armazenado a cada hora nas tabelas de transações de métricas se ativar apenas um resumo ao nível do serviço.  
-   A tabela de capacidade para bolhas tem duas linhas adicionadas por dia, desde que tenha optado por registos. Este cenário implica que todos os dias o tamanho desta tabela aumenta em cerca de 300 bytes.

## <a name="next-steps"></a>Passos seguintes
* [Monitorizar uma conta de armazenamento](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Armazenamento Analytics métricas esquema de tabela](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Armazenamento Analytics registou operações e mensagens de estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Registo de analítica de armazenamento](storage-analytics-logging.md)