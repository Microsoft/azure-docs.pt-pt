---
title: Métricas Advisor C# quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 31cdb1529ce06906aef2f546ab1c173361e7d779
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444278"
---
[Documentação de referência](/dotnet/api/overview/azure/ai.metricsadvisor-readme-pre)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Azure.AI.MetricsAdvisor)  |  [Amostras](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples/README.md)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Assim que tiver a sua subscrição Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" Crie um recurso Metrics Advisor crie um recurso "  target="_blank"> Metrics Advisor no portal </a> Azure para implementar a sua instância Metrics Advisor.  
* A sua própria base de dados SQL com dados de séries de tempo.
   
> [!TIP]
> * Pode encontrar amostras do Consultor .NET Metrics no [GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples/README.md).
> * Pode levar 10 a 30 minutos para o seu recurso Metrics Advisor implementar uma instância de serviço para que possa utilizar. Clique **em Ir para o recurso** assim que implementar com sucesso. Após a implementação, pode começar a utilizar a sua instância Metrics Advisor com o portal web e a API REST. 
> * Pode encontrar o URL para a API REST no portal Azure, na secção **Visão Geral** do seu recurso. Terá o seguinte aspeto:
>    * `https://<instance-name>.cognitiveservices.azure.com/`
   
## <a name="setting-up"></a>Configuração

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente 

Depois de criar um novo projeto, instale a biblioteca do cliente clicando corretamente na solução de projeto no **Solution Explorer** e selecione **Gerir pacotes NuGet**. No gestor de pacotes que abre **selecione Navegar,** verificar **Incluir pré-relançar,** e procurar `Azure.AI.MetricsAdvisor` . Selecione a versão `1.0.0-beta.2` e, em seguida, **instale**. 

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome `metrics-advisor-quickstart` . Este comando cria um projeto simples "Hello World" C# com um único ficheiro de origem: *program.cs*. 

```console
dotnet new console -n metrics-advisor-quickstart
```

Mude o seu diretório para a pasta de aplicações recém-criada. Pode construir a aplicação com:

```console
dotnet build
```

A saída de construção não deve conter avisos ou erros. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente 

Se estiver a utilizar um IDE diferente do Visual Studio, pode instalar a biblioteca de clientes Metrics Advisor para .NET com o seguinte comando:

```console
dotnet add package Azure.AI.MetricsAdvisor --version 1.0.0-beta.2
```

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.MetricsAdvisor_1.0.0-beta.1/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples)que contém os exemplos de código neste arranque rápido.

A partir do diretório do projeto, abra o ficheiro *program.cs* e adicione as `using` seguintes diretivas:

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Azure.AI.MetricsAdvisor.Administration;
using Azure.AI.MetricsAdvisor;
using Azure.AI.MetricsAdvisor.Models;
```

No método da `Main()` aplicação, adicione chamadas para os métodos utilizados neste arranque rápido. Vais criar isto mais tarde.

```csharp
static void Main(string[] args){
    // You will create the below methods later in the quickstart
    exampleTask1();
}
```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes lidam com algumas das principais características do Metrics Advisor C# SDK.

|Nome|Descrição|
|---|---|
| [MetricsAdvisorClient](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorClient.cs) | **Usado para:** <br> - Listagem de incidentes <br> - Listagem da causa principal dos incidentes <br> - Recuperação dos dados originais das séries de tempo e dos dados das séries de tempo enriquecidos pelo serviço. <br> - Alertas de listagem <br> - Adicionar feedback para sintonizar o seu modelo |
| [MétricasAdvisorDissinsecimento](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorAdministrationClient.cs)| **Permite-lhe:** <br> - Gerir feeds de dados <br> - Configurações de deteção de anomalias configurantes configurações de configuração de anomalias <br> - Configurações de alerta de anomalias configurações de alerta de anomalias configurações <br> - Gerir ganchos  |
| [DataFeed](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/Models/DataFeed/DataFeed.cs)| **O que o Metrics Advisor ingere a partir da sua fonte de dados. A `DataFeed` contém linhas de:** <br> - Prazos <br> - Dimensões zero ou mais <br> - Uma ou mais medidas  |
| [DataFeedMetric](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/Models/DataFeed/DataFeedMetric.cs)| A `DataFeedMetric` é uma medida quantificável que é usada para monitorizar e avaliar o estado de um processo de negócio específico. Pode ser uma combinação de múltiplos valores de séries de tempo divididos em dimensões. Por exemplo, uma métrica de saúde web pode conter dimensões para a contagem de utilizadores e para o mercado en-us. |

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes Metrics Advisor para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Adicionar um feed de dados](#add-a-data-feed)
* [Verifique o estado da ingestão](#check-the-ingestion-status)
* [Deteção de anomalias de configuração](#configure-anomaly-detection)
* [Criar um gancho](#create-a-hook)
* [Criar uma configuração de alerta](#create-an-alert-configuration)
* [Consultar o alerta](#query-the-alert)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Na classe da `Program` aplicação, crie variáveis para as chaves e ponto final do seu recurso.

> [!IMPORTANT]
> Aceda ao portal do Azure. Se o recurso Metrics Advisor que criou na secção **Pré-Requisitos** implementado com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes**. Pode encontrar as chaves de subscrição e o ponto final na página **Chave e Ponta final** do recurso, na **Gestão de Recursos.** <br><br>Para recuperar a sua chave API tem de ir a [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) . Selecione o apropriado: **Diretório,** **Subscrições** e **Espaço de Trabalho** para o seu recurso e escolha Começar a **trabalhar**. Poderá então recuperar as suas chaves API de [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) .   
>
> Lembre-se de remover a chave do seu código quando terminar, e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Consulte o artigo [de segurança](../../../cognitive-services-security.md) dos Serviços Cognitivos para obter mais informações.

Assim que tiver as teclas de subscrição e API, crie um MetricsAdvisorKeyCredential. Com o ponto final e a credencial chave, pode criar [`MetricsAdvisorClient`](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorClient.cs) um:

```csharp
string endpoint = "<endpoint>";
string subscriptionKey = "<subscriptionKey>";
string apiKey = "<apiKey>";
var credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
var client = new MetricsAdvisorClient(new Uri(endpoint), credential);
```

Também pode criar um [`MetricsAdvisorAdministrationClient`](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorAdministrationClient.cs) para realizar operações administrativas:

```csharp
string endpoint = "<endpoint>";
string subscriptionKey = "<subscriptionKey>";
string apiKey = "<apiKey>";
var credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
var adminClient = new MetricsAdvisorAdministrationClient(new Uri(endpoint), credential);
```

## <a name="add-a-data-feed"></a>Adicionar um feed de dados

O Metrics Advisor suporta vários tipos de fontes de dados. Nesta amostra vamos ilustrar como criar um `DataFeed` que extrai dados de um servidor SQL. `connection_String`Substitua-o pela sua própria cadeia de ligação ao servidor SQL e substitua-os `query` por uma consulta que retorne os seus dados a um único tempo. Também terá de ajustar os `DataFeedMetric` valores e `DataFeedDimension` valores com base nos seus dados personalizados.


```csharp
string sqlServerConnectionString = "<connection_String>";
string sqlServerQuery = "<query>";

var dataFeedName = "Sample data feed";
var dataFeedSource = new SqlServerDataFeedSource(sqlServerConnectionString, sqlServerQuery);
var dataFeedGranularity = new DataFeedGranularity(DataFeedGranularityType.Daily);

var dataFeedMetrics = new List<DataFeedMetric>()
{
    new DataFeedMetric("cost"),
    new DataFeedMetric("revenue")
};
var dataFeedDimensions = new List<DataFeedDimension>()
{
    new DataFeedDimension("category"),
    new DataFeedDimension("city")
};
var dataFeedSchema = new DataFeedSchema(dataFeedMetrics)
{
    DimensionColumns = dataFeedDimensions
};

var ingestionStartTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var dataFeedIngestionSettings = new DataFeedIngestionSettings(ingestionStartTime);

var dataFeed = new DataFeed(dataFeedName, dataFeedSource, dataFeedGranularity, dataFeedSchema, dataFeedIngestionSettings);

Response<string> response = await adminClient.CreateDataFeedAsync(dataFeed);

string dataFeedId = response.Value;

Console.WriteLine($"Data feed ID: {dataFeedId}");

// Note that only the ID of the data feed is known at this point. You can perform another
// service call to GetDataFeedAsync or GetDataFeed to get more information, such as status,
// created time, the list of administrators, or the metric IDs.

Response<DataFeed> response = await adminClient.GetDataFeedAsync(dataFeedId);

DataFeed dataFeed = response.Value;

Console.WriteLine($"Data feed status: {dataFeed.Status.Value}");
Console.WriteLine($"Data feed created time: {dataFeed.CreatedTime.Value}");

Console.WriteLine($"Data feed administrators:");
foreach (string admin in dataFeed.Administrators)
{
    Console.WriteLine($" - {admin}");
}

Console.WriteLine($"Metric IDs:");
foreach (DataFeedMetric metric in dataFeed.Schema.MetricColumns)
{
    Console.WriteLine($" - {metric.MetricName}: {metric.MetricId}");
}
```

## <a name="check-the-ingestion-status"></a>Verifique o estado da ingestão

Verifique o estado de ingestão de um criado anteriormente `DataFeed`

```csharp
string dataFeedId = "<dataFeedId>";

var startTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var endTime = DateTimeOffset.Parse("2020-09-09T00:00:00Z");
var options = new GetDataFeedIngestionStatusesOptions(startTime, endTime)
{
    TopCount = 5
};

Console.WriteLine("Ingestion statuses:");
Console.WriteLine();

int statusCount = 0;

await foreach (DataFeedIngestionStatus ingestionStatus in adminClient.GetDataFeedIngestionStatusesAsync(dataFeedId, options))
{
    Console.WriteLine($"Timestamp: {ingestionStatus.Timestamp}");
    Console.WriteLine($"Status: {ingestionStatus.Status}");
    Console.WriteLine($"Service message: {ingestionStatus.Message}");
    Console.WriteLine();

    // Print at most 5 statuses.
    if (++statusCount >= 5)
    {
        break;
    }
}
```

## <a name="configure-anomaly-detection"></a>Deteção de anomalias de configuração 

Crie uma configuração de deteção de anomalias para dizer ao serviço quais os pontos de dados que devem ser considerados anomalias.

```csharp
string metricId = "<metricId>";
string configurationName = "Sample anomaly detection configuration";

var hardThresholdSuppressCondition = new SuppressCondition(1, 100);
var hardThresholdCondition = new HardThresholdCondition(AnomalyDetectorDirection.Down, hardThresholdSuppressCondition)
{
    LowerBound = 5.0
};

var smartDetectionSuppressCondition = new SuppressCondition(4, 50);
var smartDetectionCondition = new SmartDetectionCondition(10.0, AnomalyDetectorDirection.Up, smartDetectionSuppressCondition);

var detectionCondition = new MetricWholeSeriesDetectionCondition()
{
    HardThresholdCondition = hardThresholdCondition,
    SmartDetectionCondition = smartDetectionCondition,
    CrossConditionsOperator = DetectionConditionsOperator.Or
};

var detectionConfiguration = new AnomalyDetectionConfiguration(metricId, configurationName, detectionCondition);

Response<string> response = await adminClient.CreateDetectionConfigurationAsync(detectionConfiguration);

string detectionConfigurationId = response.Value;

Console.WriteLine($"Anomaly detection configuration ID: {detectionConfigurationId}");
```

### <a name="create-a-hook"></a>Criar um gancho

O Metrics Advisor suporta as `EmailNotificationHook` classes e as classes como meio de subscrever `WebNotificationHook` notificações de alertas. Neste exemplo vamos ilustrar como criar um `EmailNotificationHook` . É necessário passar o gancho para uma configuração de alerta de anomalia para começar a receber notificações. Consulte a amostra [para criar uma configuração de alerta de anomalia](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor#create-an-anomaly-alert-configuration) para obter mais informações.

```csharp
string hookName = "Sample hook";
var emailsToAlert = new List<string>()
{
    "email1@sample.com",
    "email2@sample.com"
};

var emailHook = new EmailNotificationHook(hookName, emailsToAlert);

Response<string> response = await adminClient.CreateHookAsync(emailHook);

string hookId = response.Value;

Console.WriteLine($"Hook ID: {hookId}");
```

##  <a name="create-an-alert-configuration"></a>Criar uma configuração de alerta

Crie um `AnomalyAlertConfiguration` para dizer ao serviço quais anomalias devem desencadear alertas.

```csharp
string hookId = "<hookId>";
string anomalyDetectionConfigurationId = "<anomalyDetectionConfigurationId>";

string configurationName = "Sample anomaly alert configuration";
var idsOfHooksToAlert = new List<string>() { hookId };

var scope = MetricAnomalyAlertScope.GetScopeForWholeSeries();
var metricAlertConfigurations = new List<MetricAnomalyAlertConfiguration>()
{
    new MetricAnomalyAlertConfiguration(anomalyDetectionConfigurationId, scope)
};

AnomalyAlertConfiguration alertConfiguration = new AnomalyAlertConfiguration(configurationName, idsOfHooksToAlert, metricAlertConfigurations);

Response<string> response = await adminClient.CreateAlertConfigurationAsync(alertConfiguration);

string alertConfigurationId = response.Value;

Console.WriteLine($"Alert configuration ID: {alertConfigurationId}");
```

### <a name="query-the-alert"></a>Consultar o alerta

Veja os alertas criados por uma determinada configuração de alerta de anomalia.

```csharp
string anomalyAlertConfigurationId = "<anomalyAlertConfigurationId>";

var startTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var endTime = DateTimeOffset.UtcNow;
var options = new GetAlertsOptions(startTime, endTime, AlertQueryTimeMode.AnomalyTime)
{
    TopCount = 5
};

int alertCount = 0;

await foreach (AnomalyAlert alert in client.GetAlertsAsync(anomalyAlertConfigurationId, options))
{
    Console.WriteLine($"Alert created at: {alert.CreatedTime}");
    Console.WriteLine($"Alert at timestamp: {alert.Timestamp}");
    Console.WriteLine($"Id: {alert.Id}");
    Console.WriteLine();

    // Print at most 5 alerts.
    if (++alertCount >= 5)
    {
        break;
    }
}
```

Assim que souberes da identificação de um alerta, enumera as [anomalias](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/README.md#data-point-anomaly) que desencadearam este alerta.

```csharp
string alertConfigurationId = "<alertConfigurationId>";
string alertId = "<alertId>";

var options = new GetAnomaliesForAlertOptions() { TopCount = 3 };

int anomalyCount = 0;

await foreach (DataPointAnomaly anomaly in client.GetAnomaliesAsync(alertConfigurationId, alertId, options))
{
    Console.WriteLine($"Anomaly detection configuration ID: {anomaly.AnomalyDetectionConfigurationId}");
    Console.WriteLine($"Metric ID: {anomaly.MetricId}");
    Console.WriteLine($"Anomaly at timestamp: {anomaly.Timestamp}");
    Console.WriteLine($"Anomaly detected at: {anomaly.CreatedTime}");
    Console.WriteLine($"Status: {anomaly.Status}");
    Console.WriteLine($"Severity: {anomaly.Severity}");
    Console.WriteLine("Series key:");

    foreach (KeyValuePair<string, string> keyValuePair in anomaly.SeriesKey.AsDictionary())
    {
        Console.WriteLine($"  Dimension '{keyValuePair.Key}': {keyValuePair.Value}");
    }

    Console.WriteLine();

    // Print at most 3 anomalies.
    if (++anomalyCount >= 3)
    {
        break;
    }
}
```


### <a name="run-the-application"></a>Executar a aplicação

Executar o pedido do seu diretório de candidaturas com o `dotnet run` comando.

```dotnet
dotnet run
```