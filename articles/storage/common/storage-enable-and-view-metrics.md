---
title: Ativar as métricas de armazenamento no portal do Azure | Documentos da Microsoft
description: Como ativar as métricas de armazenamento para os serviços de Blob, fila, tabela e ficheiro
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 02/14/2017
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 770a494e93a51fc018b2bfe803ac15ba543f35d4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58011343"
---
# <a name="enabling-azure-storage-metrics-and-viewing-metrics-data"></a>Ativar as métricas de armazenamento do Azure e visualizar dados de métricas
[!INCLUDE [storage-selector-portal-enable-and-view-metrics](../../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Descrição geral
Métricas de armazenamento é ativada por predefinição, quando cria uma nova conta de armazenamento. Pode configurar a monitorização através do [portal do Azure](https://portal.azure.com) ou o Windows PowerShell, ou por meio de programação através de uma das bibliotecas de cliente do armazenamento.

Pode configurar um período de retenção para os dados das métricas: este período determina quanto o armazenamento de serviço mantém as métricas e os encargos de para o espaço necessário para armazená-las. Normalmente, deve usar um período de retenção mais curto para a métrica de minuto de métricas de hora a hora devido a espaço extra significativa, necessário para a métrica de minuto. Escolha um período de retenção, de modo que tem tempo suficiente para analisar os dados e transferir quaisquer métricas que deseja manter para análise offline ou fins de relatórios. Lembre-se de que também são cobradas por baixar os dados das métricas da sua conta de armazenamento.

## <a name="how-to-enable-metrics-using-the-azure-portal"></a>Como ativar as métricas no portal do Azure
Siga estes passos para ativar as métricas no [portal do Azure](https://portal.azure.com):

1. Navegue até à sua conta de armazenamento.
1. Selecione **diagnóstico** partir do **Menu** painel.
1. Certifique-se de que **Status** está definida como **no**.
1. Selecione as métricas para os serviços que pretende monitorizar.
1. Especifique uma política de retenção para indicar o período de tempo para manter as métricas e registos de dados.
1. Selecione **Guardar**.

O [portal do Azure](https://portal.azure.com) não atualmente permitem-lhe configurar métrica de minuto na sua conta de armazenamento; tem de ativar métrica de minuto com o PowerShell ou programaticamente.

## <a name="how-to-enable-metrics-using-powershell"></a>Como ativar métricas com o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pode utilizar o PowerShell no seu computador local para configurar as métricas de armazenamento na sua conta de armazenamento com o cmdlet do Azure PowerShell Get-AzStorageServiceMetricsProperty para recuperar as configurações atuais e o cmdlet Set-AzStorageServiceMetricsProperty Para alterar as definições atuais.

Os cmdlets que controlam as métricas de armazenamento, utilize os seguintes parâmetros:

* MetricsType: os valores possíveis são hora e minuto.
* ServiceType: os valores possíveis são BLOBs, filas e tabelas.
* MetricsLevel: os valores possíveis são None, serviço e ServiceAndApi.

Por exemplo, o comando seguinte muda em métrica de minuto para o serviço Blob na sua conta de armazenamento predefinida com a período definido como cinco dias de retenção:

```powershell
Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`
```

O comando seguinte obtém os atuais por hora métricas ao nível e retenção dias para o serviço blob na sua conta de armazenamento predefinida:

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob
```

Para obter informações sobre como configurar os cmdlets do PowerShell do Azure para trabalhar com a sua subscrição do Azure e como selecionar a conta de armazenamento predefinida a utilizar, consulte: [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="how-to-enable-storage-metrics-programmatically"></a>Como ativar as métricas de armazenamento por meio de programação
O seguinte trecho do c# mostra como ativar o registo para o serviço de Blobs com a biblioteca de cliente de armazenamento para .NET e de métricas:

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

// Create service client for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Enable Storage Analytics logging and set retention policy to 10 days.
ServiceProperties properties = new ServiceProperties();
properties.Logging.LoggingOperations = LoggingOperations.All;
properties.Logging.RetentionDays = 10;
properties.Logging.Version = "1.0";

// Configure service properties for metrics. Both metrics and logging must be set at the same time.
properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.HourMetrics.RetentionDays = 10;
properties.HourMetrics.Version = "1.0";

properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.MinuteMetrics.RetentionDays = 10;
properties.MinuteMetrics.Version = "1.0";

// Set the default service version to be used for anonymous requests.
properties.DefaultServiceVersion = "2015-04-05";

// Set the service properties.
blobClient.SetServiceProperties(properties);
```

## <a name="viewing-storage-metrics"></a>Ver métricas de armazenamento
Depois de configurar as métricas da análise de armazenamento para monitorizar a sua conta de armazenamento, análise de armazenamento regista as métricas num conjunto de tabelas bem conhecidas na sua conta de armazenamento. Pode configurar gráficos para ver as métricas de hora a hora no [portal do Azure](https://portal.azure.com):

1. Navegue até à sua conta de armazenamento na [portal do Azure](https://portal.azure.com).
1. Selecione **métricas** no **Menu** painel para o serviço cujas métricas que deseja ver.
1. Selecione **editar** no gráfico de que pretende configurar.
1. Na **editar gráfico** painel, selecione a **intervalo de tempo**, **tipo de gráfico**e as métricas que pretende apresentar no gráfico.
1. Selecione **OK**

Se quiser baixar as métricas de armazenamento de longa duração ou analisá-los localmente, terá de:

* Utilize uma ferramenta que reconheça a destas tabelas e permite-lhe ver e transferi-los.
* Escreva uma aplicação personalizada ou um script para ler e armazenar as tabelas.

Muitas ferramentas de terceiros de navegação de armazenamento estão atento a essas tabelas e ative-o a visualizá-los diretamente.
Ver [ferramentas de cliente de armazenamento do Azure](storage-explorers.md) para obter uma lista das ferramentas disponíveis.

> [!NOTE]
> A partir da versão 0.8.0 do [Explorador de armazenamento do Microsoft Azure](https://storageexplorer.com/), pode ver e transferir as tabelas de métricas da análise.
>
>

Para acessar programaticamente as tabelas de análise, tenha em atenção que as tabelas de análise não aparecem se listar todas as tabelas na sua conta de armazenamento. Pode acessá-los diretamente por nome ou utilize o [CloudAnalyticsClient API](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.analytics.cloudanalyticsclient.aspx) na biblioteca de cliente .NET para consultar os nomes de tabela.

### <a name="hourly-metrics"></a>Métricas por hora
* $MetricsHourPrimaryTransactionsBlob
* $MetricsHourPrimaryTransactionsTable
* $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>Métrica de minuto
* $MetricsMinutePrimaryTransactionsBlob
* $MetricsMinutePrimaryTransactionsTable
* $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>Capacidade
* $MetricsCapacityBlob

Pode encontrar detalhes completos dos esquemas para essas tabelas na [esquema de tabela de métricas de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343264.aspx). As linhas de exemplo abaixo mostram apenas um subconjunto de colunas disponíveis, mas ilustram alguns recursos importantes da forma como as métricas do armazenamento guarda estas métricas:

| PartitionKey | RowKey | Carimbo de data/hora | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Disponibilidade | Apresentam uma AverageE2ELatency | AverageServerLatency | PercentSuccess |
| --- |:---:| ---:| --- | --- | --- | --- | --- | --- | --- | --- |
| 20140522T1100 |user;All |2014-05-22T11:01:16.7650250Z |7 |7 |4003 |46801 |100 |104.4286 |6.857143 |100 |
| 20140522T1100 |user;QueryEntities |2014-05-22T11:01:16.7640250Z |5 |5 |2694 |45951 |100 |143.8 |7.8 |100 |
| 20140522T1100 |user;QueryEntity |2014-05-22T11:01:16.7650250Z |1 |1 |538 |633 |100 |3 |3 |100 |
| 20140522T1100 |user;UpdateEntity |2014-05-22T11:01:16.7650250Z |1 |1 |771 |217 |100 |9 |6 |100 |

Nestes dados de métrica de minuto de exemplo, a chave de partição utiliza o tempo de resolução de minuto. A chave de linha identifica o tipo de informações armazenadas na linha. A chave de linha é composta por duas partes de informações, o tipo de acesso e o tipo de pedido:

* O tipo de acesso é o utilizador ou o sistema, em que o utilizador refere-se a todos os pedidos de utilizador para o serviço de armazenamento e o sistema refere-se a pedidos efetuados pela análise de armazenamento.
* O tipo de pedido é tudo caso em é uma linha de resumida, ou ele identifica a API específica, como QueryEntity ou UpdateEntity.

Os dados de exemplo acima mostram todos os registos durante um minuto único (começando em 11:00), para que o número de pedidos de QueryEntities mais o número de QueryEntity pedidos mais o número de UpdateEntity solicita adicionar até sete, que é o total mostrado na linha de utilizador: All. Da mesma forma, pode derivar a latência de ponto-a-ponto média 104.4286 na linha de utilizador: All, calculando ((143.8 * 5) + 3 + 9) / 7.

Tenha em atenção que **definições de métrica de hora a hora de BLOBs** são aplicadas para ambos **métricas de capacidade do Blob** ($MetricsCapacityBlob) e **métricas de transação por hora Blob** ($ MetricsHourPrimaryTransactionsBlob). Ambos estão ativadas ou desativadas em conjunto e utilizam a mesma política de retenção.

## <a name="metrics-alerts"></a>Alertas de métricas
Deve considerar como configurar alertas no [portal do Azure](https://portal.azure.com) para que as métricas de armazenamento pode automaticamente notificá-lo das importantes alterações no comportamento dos seus serviços de armazenamento. Se usar uma ferramenta do Explorador de armazenamento para transferir estes dados de métricas num formato delimitado, pode utilizar o Microsoft Excel para analisar os dados. Ver [ferramentas de cliente de armazenamento do Azure](storage-explorers.md) para obter uma lista de ferramentas do Explorador de armazenamento disponível. Pode configurar alertas no **regras de alerta** painel, acessível na **monitorização** no painel de menu de conta de armazenamento.

> [!IMPORTANT]
> Poderá haver um atraso entre um evento de armazenamento e quando os dados de métricas de hora a hora ou minuto correspondente são registrados. Ao iniciar a métrica de minuto, vários minutos de dados podem ser escritos de uma só vez. Transações de minutos anteriores, em seguida, podem ser agregadas para a transação para o minuto atual. Quando isto acontecer, o serviço de alertas pode não ter todos os dados de métricas disponíveis para o intervalo de alerta configurado, que pode levar a alertas de disparo inesperadamente.
>

## <a name="accessing-metrics-data-programmatically"></a>Aceder aos dados de métricas programaticamente
A listagem a seguir mostra o exemplo c# código que acede a métrica de minuto para uma variedade de minutos e exibe os resultados numa janela de consola. Ele usa a biblioteca de armazenamento do Azure versão 4, que inclui a classe CloudAnalyticsClient que simplifica a aceder as tabelas de métricas no armazenamento.

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
        // (StartsWith is not supported using LINQ with Azure table storage)
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

## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>O que os custos pode implicar quando ativar as métricas de armazenamento?
Escreva pedidos para criar entidades de tabelas para métricas são cobrados às tarifas padrão aplicáveis a todas as operações de armazenamento do Azure.

Pedidos de leitura e delete por um cliente para dados de métricas também são a cobrar tarifas padrão. Se tiver configurado uma política de retenção de dados, não são cobradas ao armazenamento do Azure elimina dados antigos de métricas. No entanto, se eliminar dados de análise, é cobrada a conta para as operações de eliminação.

A capacidade utilizada pelas tabelas de métricas também está sujeitos a faturação: pode utilizar o seguinte para estimar a quantidade de capacidade utilizada para armazenar dados de métricas:

* Se a cada hora, um serviço utilizar cada API em cada serviço, em seguida, aproximadamente 148KB de dados é armazenado nas tabelas de transação de métricas a cada hora se tiver ativado o serviço e nível de API resumo.
* Se a cada hora, um serviço utilizar cada API em cada serviço, em seguida, aproximadamente 12KB de dados é armazenado nas tabelas de transação de métricas a cada hora se tiver ativado apenas de nível de serviço resumo.
* A tabela de capacidade para blobs tem duas linhas adicionadas por dia (desde o usuário optou por para os registos): isso implica que todos os dias aumenta o tamanho desta tabela por até aproximadamente 300 bytes.

## <a name="next-steps"></a>Passos Seguintes
[Permitindo o armazenamento de registo e aceder aos dados de registo](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data)
