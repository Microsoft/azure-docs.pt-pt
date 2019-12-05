---
title: Criar e executar testes de disponibilidade personalizados usando Azure Functions
description: Este documento explicará como criar uma função do Azure com API trackavailability () que será executado periodicamente de acordo com a configuração fornecida na função TimerTrigger. Os resultados desse teste serão enviados para o recurso de Application Insights, no qual você poderá consultar e alertar sobre os dados de resultados de disponibilidade. Os testes personalizados permitirão que você escreva testes de disponibilidade mais complexos do que o possível usando a interface do usuário do portal, monitore um aplicativo dentro de sua VNET do Azure, altere o endereço do ponto de extremidade ou crie um teste de disponibilidade se ele não estiver disponível em sua região.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 11/22/2019
ms.openlocfilehash: c7a8ffb9873fd70353f38bb2b2bbfdb584992377
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815747"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Criar e executar testes de disponibilidade personalizados usando Azure Functions

Este artigo abordará como criar uma função do Azure com API trackavailability () que será executado periodicamente de acordo com a configuração fornecida na função TimerTrigger com sua própria lógica de negócios. Os resultados desse teste serão enviados para o recurso de Application Insights, no qual você poderá consultar e alertar sobre os dados de resultados de disponibilidade. Isso permite que você crie testes personalizados semelhantes ao que você pode fazer por meio do [monitoramento de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) no Portal. Os testes personalizados permitirão que você escreva testes de disponibilidade mais complexos do que o possível usando a interface do usuário do portal, monitore um aplicativo dentro de sua VNET do Azure, altere o endereço do ponto de extremidade ou crie um teste de disponibilidade mesmo que esse recurso não esteja disponível em sua região.

> [!NOTE]
> Este exemplo é projetado unicamente para mostrar a você a mecânica de como a chamada à API API trackavailability () funciona em uma função do Azure. Não como escrever o código de teste HTTP subjacente/lógica de negócios que seria necessário para transformá-lo em um teste de disponibilidade totalmente funcional. Por padrão, se você percorrer este exemplo, criará um teste de disponibilidade que sempre gerará uma falha.

## <a name="create-timer-triggered-function"></a>Criar função disparada pelo temporizador

- Se você tiver um recurso de Application Insights:
    - Por padrão Azure Functions cria um recurso de Application Insights, mas se você quiser usar um dos recursos já criados, será necessário especificar isso durante a criação.
    - Siga as instruções sobre como [criar um recurso de Azure Functions e uma função disparada por temporizador](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (parar antes de limpar) com as seguintes opções.
        -  Selecione a guia **monitoramento** perto da parte superior.

            ![ Criar um aplicativo Azure Functions com seu próprio recurso do App insights](media/availability-azure-functions/create-function-app.png)

        - Selecione a caixa suspensa Application Insights e digite ou selecione o nome do recurso.

            ![Selecionando recurso de Application Insights existente](media/availability-azure-functions/app-insights-resource.png)

        - Selecione **revisão + criar**
- Se você ainda não tiver um recurso Application Insights criado para a função disparada pelo temporizador:
    - Por padrão, quando você estiver criando seu aplicativo de Azure Functions, ele criará um recurso de Application Insights para você.
    - Siga as instruções sobre como [criar um recurso de Azure Functions e uma função disparada por temporizador](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (parar antes da limpeza).

## <a name="sample-code"></a>Código de exemplo

Copie o código abaixo no arquivo run. CSX (isso substituirá o código pré-existente). Para fazer isso, vá para o aplicativo Azure Functions e selecione a função de gatilho de temporizador à esquerda.

>[!div class="mx-imgBorder"]
>![execute. CSX da função do Azure no portal do Azure](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Para o endereço do ponto de extremidade que você usaria: `EndpointAddress= https://dc.services.visualstudio.com/v2/track`. A menos que o recurso esteja localizado em uma região como o Azure governamental ou o Azure China, nesse caso, consulte este artigo sobre como [substituir os pontos de extremidade padrão](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) e selecionar o apontador de canal de telemetria apropriado para sua região.

```C#
#load "runAvailabilityTest.csx"
 
using System;
using System.Diagnostics;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
 
// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
// DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string instrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");
 
//[CONFIGURATION_REQUIRED]
// If your resource is in a region like Azure Government or Azure China, change the endpoint address accordingly.
// Visit https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification for more details.
private const string EndpointAddress = "https://dc.services.visualstudio.com/v2/track";
 
private static readonly TelemetryConfiguration telemetryConfiguration = new TelemetryConfiguration(instrumentationKey, new InMemoryChannel { EndpointAddress = EndpointAddress });
private static readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
 
public async static Task Run(TimerInfo myTimer, ILogger log)
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
 
    log.LogInformation($"Executing availability test run for {testName} at: {DateTime.Now}");
    string operationId = Guid.NewGuid().ToString("N");
 
    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = testName,
        RunLocation = location,
        Success = false
    };
 
    var stopwatch = new Stopwatch();
    stopwatch.Start();
 
    try
    {
        await RunAvailbiltyTestAsync(log);
        availability.Success = true;
    }
    catch (Exception ex)
    {
        availability.Message = ex.Message;
 
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", testName);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        telemetryClient.TrackException(exceptionTelemetry);
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;
 
        telemetryClient.TrackAvailability(availability);
        // call flush to ensure telemetry is sent
        telemetryClient.Flush();
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
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.8.2" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>![à direita selecione, adicionar. Nomeie o arquivo function. proj](media/availability-azure-functions/addfile.png)

À direita em Exibir arquivos, selecione **Adicionar**. Chame o novo arquivo **runAvailabilityTest. CSX** com a configuração a seguir.

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>Verificar disponibilidade

Para verificar se tudo está funcionando, você pode examinar o grafo na guia disponibilidade do recurso de Application Insights.

> [!NOTE]
> Se você implementou sua própria lógica de negócios em runAvailabilityTest. CSX, verá resultados bem-sucedidos como nas capturas de tela abaixo, se você não tiver feito isso, você verá resultados com falha.

>[!div class="mx-imgBorder"]
>![guia disponibilidade com resultados bem-sucedidos](media/availability-azure-functions/availtab.png)

Ao configurar seu teste usando Azure Functions você observará que, ao contrário de usar **Adicionar teste** na guia disponibilidade, o nome do teste não será exibido e você não poderá interagir com ele. Os resultados são visualizados, mas você obtém uma exibição resumida em vez da mesma exibição detalhada que você obtém ao criar um teste de disponibilidade por meio do Portal.

Para ver os detalhes da transação de ponta a ponta, selecione **bem-sucedido** ou **falha** em analisar em e, em seguida, selecione um exemplo. Você também pode obter os detalhes da transação de ponta a ponta selecionando um ponto de dados no grafo.

>[!div class="mx-imgBorder"]
>![selecionar um exemplo de teste de disponibilidade](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![detalhes de transação de ponta a ponta](media/availability-azure-functions/end-to-end.png)

Se você executou tudo como está (sem adicionar lógica de negócios), verá que o teste falhou.

## <a name="query-in-logs-analytics"></a>Consulta em logs (análise)

Você pode usar logs (análise) para exibir os resultados de disponibilidade, as dependências e muito mais. Para saber mais sobre logs, visite [visão geral de consultas de log](../../azure-monitor/log-query/log-query-overview.md).

>[!div class="mx-imgBorder"]
>![resultados de disponibilidade](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![Dependências](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Passos seguintes

- [Mapeamento de Aplicações](../../azure-monitor/app/app-map.md)
- [Diagnóstico de transação](../../azure-monitor/app/transaction-diagnostics.md)
