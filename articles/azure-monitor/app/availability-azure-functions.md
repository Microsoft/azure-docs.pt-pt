---
title: Criar e executar testes de disponibilidade personalizados usando funções Azure
description: Este doc cobrirá como criar uma Função Azure com TrackAvailability() que será executada periodicamente de acordo com a configuração dada na função TimerTrigger. Os resultados deste teste serão enviados para o seu recurso Application Insights, onde poderá consultar e alertar sobre os dados dos resultados da disponibilidade. Os testes personalizados permitir-lhe-ão escrever testes de disponibilidade mais complexos do que é possível utilizando o portal UI, monitorizar uma aplicação dentro do seu Azure VNET, alterar o endereço de ponto final ou criar um teste de disponibilidade se não estiver disponível na sua região.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/04/2020
ms.openlocfilehash: bf756255d5eb0ffb3e57f13f4248fb8e38f26129
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91773183"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Criar e executar testes de disponibilidade personalizados usando funções Azure

Este artigo cobrirá como criar uma Função Azure com TrackAvailability() que será executada periodicamente de acordo com a configuração dada na função TimerTrigger com a sua própria lógica de negócio. Os resultados deste teste serão enviados para o seu recurso Application Insights, onde poderá consultar e alertar sobre os dados dos resultados da disponibilidade. Isto permite-lhe criar testes personalizados semelhantes ao que pode fazer através [do Availability Monitoring](./monitor-web-app-availability.md) no portal. Os testes personalizados permitir-lhe-ão escrever testes de disponibilidade mais complexos do que é possível utilizando o portal UI, monitorizar uma aplicação dentro do seu Azure VNET, alterar o endereço de ponto final ou criar um teste de disponibilidade mesmo que esta funcionalidade não esteja disponível na sua região.

> [!NOTE]
> Este exemplo é projetado exclusivamente para mostrar-lhe a mecânica de como a chamada API trackAvailability funciona dentro de uma Função Azure. Não como escrever a lógica de código/negócio http subjacente que seria necessária para transformá-lo num teste de disponibilidade totalmente funcional. Por padrão, se passar por este exemplo, estará a criar um teste de disponibilidade que irá sempre gerar uma falha.

## <a name="create-timer-triggered-function"></a>Criar função de gatilho do temporizador

- Se tiver um recurso de insights de aplicação:
    - Por predefinição, as Funções Azure criam um recurso Application Insights, mas se pretender utilizar um dos recursos já criados, terá de especificar isso durante a criação.
    - Siga as instruções sobre como [criar um recurso Azure Functions e função de temporizador](../../azure-functions/functions-create-scheduled-function.md) (parar antes de limpar) com as seguintes escolhas.
        -  Selecione o **separador Monitorar** perto da parte superior.

            ![ Crie uma app Azure Functions com o seu próprio recurso App Insights](media/availability-azure-functions/create-function-app.png)

        - Selecione a caixa de entrega de Insights de Aplicação e escreva ou selecione o nome do seu recurso.

            ![Selecionando o recurso de Insights de Aplicação existente](media/availability-azure-functions/app-insights-resource.png)

        - Selecione **Rever + criar**
- Se ainda não tiver um Recurso de Insights de Aplicação criado para a função de desacionado do temporizador:
    - Por predefinição, quando estiver a criar a sua aplicação Azure Functions, criará um recurso 'Insights de Aplicação' para si.
    - Siga as instruções sobre como [criar um recurso Azure Functions e função de gatilho do Temporizador](../../azure-functions/functions-create-scheduled-function.md) (parar antes da limpeza).

## <a name="sample-code"></a>Código de exemplo

Copie o código abaixo no ficheiro run.csx (isto substituirá o código pré-existente). Para isso, entre na sua aplicação Azure Functions e selecione a função de gatilho do temporizador à esquerda.

>[!div class="mx-imgBorder"]
>![Run.csx da função Azure no portal Azure](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Para o Endpoint Address utilizaria: `EndpointAddress= https://dc.services.visualstudio.com/v2/track` . A menos que o seu recurso esteja localizado numa região como o Governo Azure ou a Azure China, nesse caso, consulte este artigo sobre [a sobreposição dos pontos finais predefinidos](./custom-endpoints.md#regions-that-require-endpoint-modification) e selecione o ponto final apropriado do Canal de Telemetria para a sua região.

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

No lado direito dos ficheiros de visualização, **selecione Adicionar**. Ligue para a nova função de **ficheiro.proj** com a seguinte configuração.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights" Version="2.15.0" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>![Na seleção certa, adicione. Nomeie a função de ficheiro.proj](media/availability-azure-functions/addfile.png)

No lado direito dos ficheiros de visualização, **selecione Adicionar**. Ligue para o novo ficheiro **runAvailabilityTest.csx** com a seguinte configuração.

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>Ver disponibilidade

Para se certificar de que está tudo a funcionar, pode olhar para o gráfico no separador Disponibilidade do seu recurso Application Insights.

> [!NOTE]
> Se implementou a sua própria lógica de negócio no runAvailabilityTest.csx, então verá resultados bem-sucedidos como nas imagens abaixo, se não o fez, verá resultados falhados. Os testes criados com `TrackAvailability()` serão **apresentados** com CUSTOM ao lado do nome de teste.

>[!div class="mx-imgBorder"]
>![Separador de disponibilidade com resultados bem sucedidos](media/availability-azure-functions/availability-custom.png)

Para ver os detalhes da transação de ponta a ponta, selecione **Successful** ou **Failed** under drill e, em seguida, selecione uma amostra. Também pode chegar aos detalhes da transação de ponta a ponta selecionando um ponto de dados no gráfico.

>[!div class="mx-imgBorder"]
>![Selecione um teste de disponibilidade de amostra](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![Detalhes de transações de ponta a ponta](media/availability-azure-functions/end-to-end.png)

Se executou tudo como está (sem adicionar lógica de negócio), verá que o teste falhou.

## <a name="query-in-logs-analytics"></a>Consulta em Registos (Analytics)

Pode utilizar Logs (analytics) para visualizar os resultados da disponibilidade, dependências e muito mais. Para saber mais sobre Os Registos, visite [a visão geral da consulta de registo.](../log-query/log-query-overview.md)

>[!div class="mx-imgBorder"]
>![Resultados da disponibilidade](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![A screenshot mostra o novo separador de consulta com dependências limitadas a 50.](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Passos seguintes

- [Mapeamento de Aplicações](./app-map.md)
- [Diagnóstico da transação](./transaction-diagnostics.md)

