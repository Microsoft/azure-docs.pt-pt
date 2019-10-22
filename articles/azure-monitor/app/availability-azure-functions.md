---
title: Criar e executar testes de disponibilidade personalizados usando Azure Functions
description: Este documento explicará como criar uma função do Azure com API trackavailability () que será executado periodicamente de acordo com a configuração fornecida na função TimerTrigger. Os resultados desse teste serão enviados para o recurso de Application Insights, no qual você poderá consultar e alertar sobre os dados de resultados de disponibilidade. Os testes personalizados permitirão que você escreva testes de disponibilidade mais complexos do que o possível usando a interface do usuário do portal, monitore um aplicativo dentro de sua VNET do Azure, altere o endereço do ponto de extremidade ou crie um teste de disponibilidade se ele não estiver disponível em sua região.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 10/11/2019
ms.openlocfilehash: a2b29f2c24a3244cdc800d882e7c2ed8f943eabe
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677592"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Criar e executar testes de disponibilidade personalizados usando Azure Functions

Este artigo explicará como criar uma função do Azure com API trackavailability () que será executado periodicamente de acordo com a configuração fornecida na função TimerTrigger. Os resultados desse teste serão enviados para o recurso de Application Insights, no qual você poderá consultar e alertar sobre os dados de resultados de disponibilidade. Isso permite que você crie testes personalizados semelhantes ao que você pode fazer por meio do [monitoramento de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) no Portal. Os testes personalizados permitirão que você escreva testes de disponibilidade mais complexos do que o possível usando a interface do usuário do portal, monitore um aplicativo dentro de sua VNET do Azure, altere o endereço do ponto de extremidade ou crie um teste de disponibilidade mesmo que esse recurso não esteja disponível em sua região.


## <a name="create-timer-triggered-function"></a>Criar função disparada pelo temporizador

- Se você tiver um recurso de Application Insights:
    - Por padrão Azure Functions cria um recurso de Application Insights, mas se você quiser usar um dos recursos já criados, será necessário especificar isso durante a criação.
    - Siga as instruções sobre como [criar um recurso de Azure Functions e uma função disparada por temporizador](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (parar antes de limpar) com as seguintes opções.
        -  Clique na seção Application Insights antes de selecionar **criar**.

            ![ Criar um aplicativo Azure Functions com seu próprio recurso do App insights](media/availability-azure-functions/create-function-app.png)

        - Clique em **selecionar recurso existente** e digite o nome do recurso. Selecione **aplicar**

            ![Selecionando recurso de Application Insights existente](media/availability-azure-functions/app-insights-resource.png)

        - Selecione **Criar**
- Se você ainda não tiver um recurso Application Insights criado para a função disparada pelo temporizador:
    - Por padrão, quando você estiver criando seu aplicativo de Azure Functions, ele criará um recurso de Application Insights para você.
    - Siga as instruções sobre como [criar um recurso de Azure Functions e uma função disparada por temporizador](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (parar antes da limpeza).

## <a name="sample-code"></a>Código de exemplo

Copie o código abaixo no arquivo run. CSX (isso substituirá o código pré-existente). Para fazer isso, vá para o aplicativo Azure Functions e selecione a função de gatilho de temporizador à esquerda.

![Run. CSX da função do Azure em portal do Azure](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Para o endereço do ponto de extremidade que você usaria: `EndpointAddress= https://dc.services.visualstudio.com/v2/track`. A menos que o recurso esteja localizado em uma região como o Azure governamental ou o Azure China, nesse caso, consulte este artigo sobre como [substituir os pontos de extremidade padrão](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) e selecionar o apontador de canal de telemetria apropriado para sua região.

```C#
using System;
using System.Diagnostics;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

// [CONFIGURATION_REQUIRED] configure test timeout accordingly for which your request should run
private static readonly HttpClient HttpClient = new HttpClient { Timeout = TimeSpan.FromSeconds(30) };

// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
//DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string InstrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");

// [CONFIGURATION_REQUIRED] Configure EndpointAddress
private static readonly TelemetryConfiguration TelemetryConfiguration = new TelemetryConfiguration(InstrumentationKey, new ServerTelemetryChannel() { EndpointAddress = "<EndpointAddress>" });
private static readonly TelemetryClient TelemetryClient = new TelemetryClient(TelemetryConfiguration);

[FunctionName("Function")]
public static async void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Entering Run at: {DateTime.Now}");

    if (myTimer.IsPastDue)
    {
        log.LogWarning($"[Warning]: Timer is running late! Last ran at: {myTimer.ScheduleStatus.Last}");
    }

    // [CONFIGURATION_REQUIRED] provide {testName} accordingly for your test function
    string testName = "AvailabilityTestFunction";

    // REGION_NAME is a default environment variable that comes with App Service
    string location = Environment.GetEnvironmentVariable("REGION_NAME");

    // [CONFIGURATION_REQUIRED] configure {uri} and {contentMatch} accordingly for your web app. {uri} is the website that you are testing the availability of, make sure to include http:// ot https:// in your url. If {contentMatch} is present on the page, the test will succeed, otherwise it will fail.  
    await AvailabilityTestRun(
        name: testName,
        location: location,
        uri: "<http://example.com>",
        contentMatch: "<Enter a short string of text that is present  in the body of the page your are testing>",
        log: log
    );
}

private static async Task AvailabilityTestRun(string name, string location, string uri, string contentMatch, ILogger log)
{
    log.LogInformation($"Executing availability test run for {name} at: {DateTime.Now}");

    string operationId = Guid.NewGuid().ToString("N");

    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = name,
        RunLocation = location,
        Success = false
    };

    var stopwatch = new Stopwatch();
    stopwatch.Start();
    bool isMonitoringFailure = false;

    try
    {
        using (var httpResponse = await HttpClient.GetAsync(uri))
        {
            // add test results to availability telemetry property
            availability.Properties.Add("HttpResponseStatusCode", Convert.ToInt32(httpResponse.StatusCode).ToString());

            // check if response content contains specific text
            string content = httpResponse.Content != null ? await httpResponse.Content.ReadAsStringAsync() : "";
            if (httpResponse.IsSuccessStatusCode && content.Contains(contentMatch))
            {
                availability.Success = true;
                availability.Message = $"Test succeeded with response: {httpResponse.StatusCode}";
                log.LogTrace($"[Verbose]: {availability.Message}");
            }
            else if (!httpResponse.IsSuccessStatusCode)
            {
                availability.Message = $"Test failed with response: {httpResponse.StatusCode}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
            else
            {
                availability.Message = $"Test content does not contain: {contentMatch}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
        }
    }
    catch (TaskCanceledException e)
    {
        availability.Message = $"Test timed out: {e.Message}";
        log.LogWarning($"[Warning]: {availability.Message}");
    }
    catch (Exception ex)
    {
        // track exception when unable to determine the state of web app
        isMonitoringFailure = true;
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", name);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        exceptionTelemetry.Properties.Add("TestUri", uri);
        TelemetryClient.TrackException(exceptionTelemetry);
        log.LogError($"[Error]: {ex.Message}");

        // optional - throw to fail the function
        throw;
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;

        // do not make an assumption as to the state of the web app when monitoring failures occur
        if (!isMonitoringFailure)
        {
            TelemetryClient.TrackAvailability(availability);
            log.LogInformation($"Availability telemetry for {name} is sent.");
        }

        // call flush to ensure telemetries are sent
        TelemetryClient.Flush();
    }
}
```

À direita em Exibir arquivos, selecione **Adicionar**. Chame o novo arquivo **Function. proj** com a configuração a seguir.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
</Project>

```

![À direita, selecione Adicionar. Nomeie o arquivo function. proj](media/availability-azure-functions/addfile.png)

## <a name="check-availability"></a>Verificar disponibilidade

Para verificar se tudo está funcionando, você pode examinar o grafo na guia disponibilidade do recurso de Application Insights.

![Guia disponibilidade com resultados bem-sucedidos](media/availability-azure-functions/availtab.png)

Ao configurar seu teste usando Azure Functions você observará que, ao contrário de usar **Adicionar teste** na guia disponibilidade, o nome do teste não será exibido e você não poderá interagir com ele. Os resultados são visualizados, mas você obtém uma exibição resumida em vez da mesma exibição detalhada que você obtém ao criar um teste de disponibilidade por meio do Portal.

Para ver os detalhes da transação de ponta a ponta, selecione **bem-sucedido** ou **falha** em analisar em e, em seguida, selecione um exemplo. Você também pode obter os detalhes da transação de ponta a ponta selecionando um ponto de dados no grafo.

![Selecione um teste de disponibilidade de exemplo](media/availability-azure-functions/sample.png)

![Detalhes da transação de ponta a ponta](media/availability-azure-functions/end-to-end.png)

## <a name="query-in-logs-analytics"></a>Consulta em logs (análise)

Você pode usar logs (análise) para exibir os resultados de disponibilidade, as dependências e muito mais. Para saber mais sobre logs, visite [visão geral de consultas de log](../../azure-monitor/log-query/log-query-overview.md).

![Resultados da disponibilidade](media/availability-azure-functions/availabilityresults.png)

![Dependências](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Passos seguintes

- [Mapeamento de Aplicações](../../azure-monitor/app/app-map.md)
- [Diagnóstico de transação](../../azure-monitor/app/transaction-diagnostics.md)
