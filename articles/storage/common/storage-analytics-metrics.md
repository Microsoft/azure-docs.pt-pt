---
title: Métricas de análise de armazenamento do Azure (clássico)
description: Saiba como utilizar as métricas do armazenamento do Azure.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: b35d3d22ce154420c9099143894688389e8af420
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2019
ms.locfileid: "58078045"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Métricas de análise de armazenamento do Azure (clássico)

Análise de armazenamento pode armazenar métricas que incluem dados de estatísticas e a capacidade de agregadas de transações sobre pedidos para um serviço de armazenamento. Transações são comunicadas ao nível da operação ambas API, bem como o nível de serviço de armazenamento e capacidade é comunicada o nível de serviço de armazenamento. Dados de métricas podem ser utilizados para analisar a utilização do serviço de armazenamento, diagnóstico de problemas com pedidos efetuados para o serviço de armazenamento e para melhorar o desempenho das aplicações que utilizam um serviço.  

 Métricas da análise de armazenamento estão ativadas por predefinição para novas contas de armazenamento. Pode configurar métricas no [portal do Azure](https://portal.azure.com/); para obter detalhes, veja [monitorizar uma conta de armazenamento no portal do Azure](/azure/storage/storage-monitor-storage-account). Também pode ativar a análise de armazenamento através de programação com a API REST ou a biblioteca de cliente. Utilize as operações de definir propriedades do serviço para ativar a análise de armazenamento para cada serviço.  

> [!NOTE]
> Métricas da análise de armazenamento estão disponíveis para os serviços de Blob, fila, tabela e ficheiro.
> Métricas da análise de armazenamento estão agora métricas clássicas. A Microsoft recomenda utilizar [métricas do armazenamento no Azure Monitor](/azure/storage/common/storage-metrics-in-azure-monitor.md) em vez de métricas da análise de armazenamento.

## <a name="transaction-metrics"></a>Métricas de transação  
 Um robusto conjunto de dados é registrado em intervalos por hora ou minutos para cada serviço de armazenamento e a operação de API, incluindo a entrada/saída, disponibilidade, erros, pedida e categorizadas percentagens de pedido. Pode ver uma lista completa dos detalhes da transação no [esquema de tabela de métricas de análise de armazenamento](/rest/api/storageservices/storage-analytics-metrics-table-schema.md) tópico.  

 Dados de transações são registrados em dois níveis – o nível de serviço e o nível de operação de API. No nível de serviço, estatísticas que resume todas pediu a operações de API são escritas para uma entidade de tabela por hora, mesmo que não existem pedidos foram feitos para o serviço. No nível de operação da API, as estatísticas só são escritas para uma entidade se a operação foi pedida nessa hora.  

 Por exemplo, se efetuar uma **GetBlob** operação no seu serviço de BLOBs, métricas da análise de armazenamento irá registrar a solicitação e incluí-lo nos dados agregados para o serviço Blob, bem como a **GetBlob** operação. No entanto, se nenhum **GetBlob** operação é solicitada durante a hora, uma entidade será não ser escrita para o *$MetricsTransactionsBlob* para essa operação.  

 Métricas de transação são registradas para pedidos de utilizadores e pedidos efetuados por análise de armazenamento em si. Por exemplo, são registados pedidos pela análise de armazenamento para escrever registos e as entidades da tabela.

## <a name="capacity-metrics"></a>Métricas de capacidade  

> [!NOTE]
>  Atualmente, as métricas de capacidade só estão disponíveis para o serviço de Blobs.

 Dados de capacidade é registados diariamente para serviço de uma conta de armazenamento de BLOBs e duas entidades de tabela são escritas. Uma entidade fornece estatísticas dos dados do usuário e o outro fornece estatísticas sobre o `$logs` utilizado pela análise de armazenamento de contentor de Blobs. O *$MetricsCapacityBlob* tabela inclui as estatísticas seguintes:  

- **Capacidade**: A quantidade de armazenamento utilizada pelo serviço de Blob da conta de armazenamento, em bytes.  
- **ContainerCount**: O número de contentores de BLOBs no serviço de Blob da conta de armazenamento.  
- **ObjectCount**: O número de consolidada e não consolidados bloco ou página blobs no serviço de Blob da conta de armazenamento.  

  Para obter mais informações sobre as métricas de capacidade, consulte [esquema de tabela de métricas de análise de armazenamento](/rest/api/storageservices/storage-analytics-metrics-table-schema.md).  

## <a name="how-metrics-are-stored"></a>Como as métricas são armazenadas  

 Todos os dados de métricas para cada um dos serviços de armazenamento são armazenados em três tabelas reservadas para esse serviço: uma tabela para obter informações de transação, uma tabela para informações sobre transações minuto e outra tabela para obter informações de capacidade. Informações de transação de transação e o minuto consistem em dados de solicitação e resposta e informações de capacidade consiste em armazenamento dados de utilização. Métrica de hora, a métrica de minuto e a capacidade para um armazenamento de Blob da conta é de serviço pode ser acedido em tabelas com o nome, tal como descrito na tabela seguinte.  

|Nível de métricas|Nomes de tabela|Suporte para versões|  
|-------------------|-----------------|----------------------------|  
|Métricas de hora a hora, localização primária|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|Versões anteriores a 2013-08-15 apenas. Embora esses nomes ainda são suportados, recomenda-se que mude para utilizar as tabelas listadas abaixo.|  
|Métricas de hora a hora, localização primária|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|Todas as versões. Suporte para as métricas de serviço de arquivo está disponível apenas na versão 04-05 de 2015 e posterior.|  
|Métrica de minuto, localização primária|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|Todas as versões. Suporte para as métricas de serviço de arquivo está disponível apenas na versão 04-05 de 2015 e posterior.|  
|Métricas de hora a hora, localização secundária|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|Todas as versões. Replicação georredundante de acesso de leitura tem de estar ativada.|  
|Métrica de minuto, localização secundária|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|Todas as versões. Replicação georredundante de acesso de leitura tem de estar ativada.|  
|Capacidade (serviço de BLOBs apenas)|$MetricsCapacityBlob|Todas as versões.|  

 Essas tabelas são criadas automaticamente quando a análise de armazenamento é ativada para um ponto de extremidade do serviço de armazenamento. Estes são acedidos através do espaço de nomes da conta de armazenamento, por exemplo: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. As tabelas de métricas não aparecem numa operação de listagem e têm de ser acedidas diretamente através do nome da tabela.  

## <a name="enable-metrics-using-the-azure-portal"></a>Ativar as métricas no portal do Azure
Siga estes passos para ativar as métricas no [portal do Azure](https://portal.azure.com):

1. Navegue até à sua conta de armazenamento.
1. Selecione **as definições de diagnóstico (clássico)** partir do **Menu** painel.
1. Certifique-se de que **Status** está definida como **no**.
1. Selecione as métricas para os serviços que pretende monitorizar.
1. Especifique uma política de retenção para indicar o período de tempo para manter as métricas e registos de dados.
1. Selecione **Guardar**.

O [portal do Azure](https://portal.azure.com) não atualmente permitem-lhe configurar métrica de minuto na sua conta de armazenamento; tem de ativar métrica de minuto com o PowerShell ou programaticamente.

> [!NOTE]
>  Tenha em atenção que o portal do Azure não atualmente permitem-lhe configurar métrica de minuto na sua conta de armazenamento. Tem de ativar métrica de minuto com o PowerShell ou programaticamente.  

## <a name="enable-storage-metrics-using-powershell"></a>Ativar as métricas de armazenamento com o PowerShell  
Pode utilizar o PowerShell no seu computador local para configurar as métricas de armazenamento na sua conta de armazenamento utilizando o cmdlet do Azure PowerShell **Get-AzureStorageServiceMetricsProperty** para obter as definições atuais e o cmdlet  **Conjunto AzureStorageServiceMetricsProperty** para alterar as definições atuais.  

Os cmdlets que controlam as métricas de armazenamento, utilize os seguintes parâmetros:  

* **ServiceType**, valor possível são **Blob**, **fila**, **tabela**, e **ficheiro**.
* **MetricsType**, os valores possíveis são **hora** e **minuto**.  
* **MetricsLevel**, os valores possíveis são:
* **Nenhum**: Desativa a monitorização.
* **Serviço**: Recolhe métricas, como entrada/saída, disponibilidade, latência e percentagens de êxito, o que são agregadas para o blob, fila, tabela e serviços de ficheiros.
* **ServiceAndApi**: Além das métricas de serviço, recolhe o mesmo conjunto de métricas para cada operação de armazenamento na API de serviços de armazenamento do Azure.

Por exemplo, o comando seguinte muda em métrica de minuto para o serviço blob na sua conta de armazenamento predefinida com a período definido como cinco dias de retenção:  

```  
Set-AzureStorageServiceMetricsProperty -MetricsType Minute   
-ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5  
```  

O comando seguinte obtém os atuais por hora métricas ao nível e retenção dias para o serviço blob na sua conta de armazenamento predefinida:  

```  
Get-AzureStorageServiceMetricsProperty -MetricsType Hour   
-ServiceType Blob  
```  

Para obter informações sobre como configurar os cmdlets do PowerShell do Azure para trabalhar com a sua subscrição do Azure e como selecionar a conta de armazenamento predefinida a utilizar, consulte: [Como instalar e configurar o Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Ativar as métricas de armazenamento por meio de programação  
Além de utilizar o portal do Azure ou cmdlets do Azure PowerShell para controlar as métricas de armazenamento, também pode utilizar uma das APIs de armazenamento do Azure. Por exemplo, se estiver a utilizar uma linguagem .NET pode utilizar a biblioteca de cliente de armazenamento.  

As classes **CloudBlobClient**, **CloudQueueClient**, **CloudTableClient**, e **CloudFileClient** todas têm métodos como  **SetServiceProperties** e **SetServicePropertiesAsync** que demoram um **ServiceProperties** objeto como um parâmetro. Pode utilizar o **ServiceProperties** objeto para configurar as métricas de armazenamento. Por exemplo, o seguinte C# fragmento mostra como alterar os dias de retenção e de nível de métricas para as métricas de fila por hora:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Para obter mais informações sobre como utilizar uma linguagem .NET para configurar as métricas de armazenamento, consulte [biblioteca de cliente de armazenamento para .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

Para obter informações gerais sobre como configurar as métricas de armazenamento com a API REST, consulte [ativar e configurar a análise de armazenamento](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics.md).  

##  <a name="viewing-storage-metrics"></a>Ver métricas de armazenamento  
Depois de configurar as métricas da análise de armazenamento para monitorizar a sua conta de armazenamento, análise de armazenamento regista as métricas num conjunto de tabelas bem conhecidas na sua conta de armazenamento. Pode configurar gráficos para ver as métricas de hora a hora no [portal do Azure](https://portal.azure.com):

1. Navegue até à sua conta de armazenamento na [portal do Azure](https://portal.azure.com).
1. Selecione **métrica (clássico)** no **Menu** painel para o serviço cujas métricas que deseja ver.
1. Clique no gráfico que pretende configurar.
1. Na **editar gráfico** painel, selecione a **intervalo de tempo**, **tipo de gráfico**e as métricas que pretende apresentar no gráfico.

Na **monitorização (clássico)** seção do painel de menu da conta de armazenamento no portal do Azure, pode configurar [regras de alerta](#metrics-alerts), como o envio de correio eletrónico alertas para notificá-lo quando uma métrica específica atinge um determinado valor.

Se quiser baixar as métricas de armazenamento de longa duração ou analisá-los localmente, tem de utilizar uma ferramenta ou escrever um código para ler as tabelas. Tem de transferir a métrica de minuto para análise. As tabelas não aparecem se listar todas as tabelas na sua conta de armazenamento, mas pode acessá-los diretamente por nome. Muitas ferramentas de navegação de armazenamento está atento a estas tabelas e ative-o a visualizá-los diretamente (consulte [ferramentas de cliente de armazenamento do Azure](/azure/storage/storage-explorers) para obter uma lista das ferramentas disponíveis).

||||  
|-|-|-|  
|**Métricas**|**Nomes de tabela**|**Notas**|  
|Métricas por hora|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|Nas versões anteriores ao 08-2013-15, essas tabelas eram conhecidas como:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> As métricas para o serviço de ficheiros estão disponíveis a partir de 2015-04-05 de versão.|  
|Métrica de minuto|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Só pode ser ativada com o PowerShell ou programaticamente.<br /><br /> As métricas para o serviço de ficheiros estão disponíveis a partir de 2015-04-05 de versão.|  
|Capacidade|$MetricsCapacityBlob|Serviço blob.|  

Pode encontrar detalhes completos dos esquemas para essas tabelas na [esquema de tabela de métricas de análise de armazenamento](/rest/api/storageservices/storage-analytics-metrics-table-schema.md). As linhas de exemplo abaixo mostram apenas um subconjunto de colunas disponíveis, mas ilustram alguns recursos importantes da forma como as métricas do armazenamento guarda estas métricas:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**RowKey**|**Timestamp**|**TotalRequests**|**TotalBillableRequests**|**TotalIngress**|**TotalEgress**|**Disponibilidade**|**Apresentam uma AverageE2ELatency**|**AverageServerLatency**|**PercentSuccess**|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|user;QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

Nestes dados de métrica de minuto de exemplo, a chave de partição utiliza o tempo de resolução de minuto. A chave de linha identifica o tipo de informações armazenadas na linha, e isso é composto por duas partes de informações, o tipo de acesso e o tipo de pedido:  

-   O tipo de acesso está **usuário** ou **system**, onde **utilizador** refere-se a todos os pedidos de utilizador para o serviço de armazenamento, e **sistema** refere-se a pedidos efetuados por análise de armazenamento.  

-   O tipo de pedido é **todos os** caso em que é uma linha de resumida ou identifica como a API específica **QueryEntity** ou **UpdateEntity**.  

Os dados de exemplo acima mostram todos os registos para um único minuto (começando em 11:00), então, o número de **QueryEntities** pedidos mais o número de **QueryEntity** pedidos mais o número de  **UpdateEntity** pedidos adicionar até sete, que é o total apresentado no **utilizador: todos os** linha. Da mesma forma, pode derivar a latência de ponto-a-ponto média 104.4286 sobre o **utilizador: All** linha, calculando ((143.8 * 5) + 3 + 9) / 7.  

## <a name="metrics-alerts"></a>Alertas de métricas
Deve considerar como configurar alertas no [portal do Azure](https://portal.azure.com) para que será automaticamente notificado sobre alterações importantes no comportamento dos seus serviços de armazenamento. Se usar uma ferramenta do Explorador de armazenamento para transferir estes dados de métricas num formato delimitado, pode utilizar o Microsoft Excel para analisar os dados. Ver [ferramentas de cliente de armazenamento do Azure](/azure/storage/storage-explorers) para obter uma lista de ferramentas do Explorador de armazenamento disponível. Pode configurar alertas no **alerta (clássico)** painel, acessível na **monitorização (clássico)** no painel de menu de conta de armazenamento.

> [!IMPORTANT]
> Poderá haver um atraso entre um evento de armazenamento e quando os dados de métricas de hora a hora ou minuto correspondente são registrados. No caso de métrica de minuto, vários minutos de dados podem ser escritos de uma só vez. Isso pode levar a transações de minutos anteriores a ser agregados numa transação para o minuto atual. Quando isto acontecer, o serviço de alertas pode não ter todos os dados de métricas disponíveis para o intervalo de alerta configurado, que pode levar a alertas de disparo inesperadamente.
>

## <a name="accessing-metrics-data-programmatically"></a>Aceder aos dados de métricas programaticamente  
A listagem a seguir mostra o exemplo c# código que acede a métrica de minuto para uma variedade de minutos e exibe os resultados numa janela de consola. O código de exemplo utiliza a versão da biblioteca de clientes de armazenamento do Azure 4.x ou posterior, que inclui a **CloudAnalyticsClient** classe que simplifica a aceder as tabelas de métricas no armazenamento.  

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

## <a name="billing-on-storage-metrics"></a>Faturação nas métricas de armazenamento  
Escreva pedidos para criar entidades de tabelas para métricas são cobrados às tarifas padrão aplicáveis a todas as operações de armazenamento do Azure.  

Pedidos de leitura e de eliminação de dados de métricas por um cliente também são a cobrar tarifas padrão. Se tiver configurado uma política de retenção de dados, não são cobradas ao armazenamento do Azure elimina dados antigos de métricas. No entanto, se eliminar dados de análise, é cobrada a conta para as operações de eliminação.  

A capacidade utilizada pelas tabelas de métricas também está sujeitos a faturação. Pode utilizar o seguinte para estimar a quantidade de capacidade utilizada para armazenar dados de métricas:  

-   Se a cada hora, um serviço utilizar cada API em cada serviço, em seguida, aproximadamente 148KB de dados é armazenado nas tabelas de transação de métricas a cada hora se tiver ativado os dois e API-nível de serviço resumo.  
-   Se dentro de cada hora, um serviço utiliza cada API no serviço, em seguida, aproximadamente 12KB de dados é armazenado nas tabelas de transação de métricas a cada hora, se tiver ativado o resumo de nível de serviço apenas.  
-   A tabela de capacidade para blobs tem duas linhas adicionadas por dia, desde que tiver optado por participar para os registos. Isso implica que todos os dias, aumenta o tamanho desta tabela por até aproximadamente 300 bytes.

## <a name="next-steps"></a>Passos Seguintes
* [Como monitorizar uma conta de armazenamento](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Esquema de tabela de métricas de análise de armazenamento](/rest/api/storageservices/storage-analytics-metrics-table-schema.md)   
* [Análise de armazenamento conectado operações e mensagens de estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages.md)   
* [Registo de análise de armazenamento](storage-analytics-logging.md)
