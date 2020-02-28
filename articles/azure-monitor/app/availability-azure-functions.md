---
title: Criar e executar testes de disponibilidade personalizados utilizando funções azure
description: Este doc cobrirá como criar uma Função Azure com TrackAvailability() que será executada periodicamente de acordo com a configuração dada na função TimerTrigger. Os resultados deste teste serão enviados para o seu recurso Application Insights, onde poderá consultar e alertar sobre os dados dos resultados da disponibilidade. Testes personalizados permitirão escrever testes de disponibilidade mais complexos do que é possível usando o portal UI, monitorizar uma aplicação dentro do seu Azure VNET, alterar o endereço de ponto final ou criar um teste de disponibilidade se não estiver disponível na sua região.
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 11/22/2019
ms.openlocfilehash: 476d66c51c10a5fcfb3cb0319c47b3338d28812c
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665804"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Criar e executar testes de disponibilidade personalizados utilizando funções azure

Este artigo cobrirá como criar uma Função Azure com TrackAvailability() que será executada periodicamente de acordo com a configuração dada na função TimerTrigger com a sua própria lógica de negócio. Os resultados deste teste serão enviados para o seu recurso Application Insights, onde poderá consultar e alertar sobre os dados dos resultados da disponibilidade. Isto permite criar testes personalizados semelhantes ao que pode fazer através da [Monitorização](../../azure-monitor/app/monitor-web-app-availability.md) de Disponibilidade no portal. Os testes personalizados permitir-lhe-ão escrever testes de disponibilidade mais complexos do que é possível usando o portal UI, monitorizar uma aplicação dentro do seu Azure VNET, alterar o endereço de ponto final ou criar um teste de disponibilidade mesmo que esta funcionalidade não esteja disponível na sua região.

> [!NOTE]
> Este exemplo foi concebido exclusivamente para lhe mostrar a mecânica de como a chamada trackAvailability() API funciona dentro de uma Função Azure. Não como escrever o código/lógica de negócio seletiva http subjacente que seria necessário para transformar isto num teste de disponibilidade totalmente funcional. Por defeito, se passar por este exemplo, estará a criar um teste de disponibilidade que irá sempre gerar uma falha.

## <a name="create-timer-triggered-function"></a>Criar função ativada pelo temporizador

- Se tiver um Recurso de Insights de Aplicação:
    - Por padrão, as Funções Azure criam um recurso Application Insights, mas se quiser utilizar um dos seus recursos já criados, terá de especificar isso durante a criação.
    - Siga as instruções sobre como criar um recurso de [Funções Azure e função acionada pelo temporizador](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (parar antes de limpar) com as seguintes escolhas.
        -  Selecione o separador **Monitoramento** perto da parte superior.

            ![ Crie uma aplicação De Funções Azure com o seu próprio recurso App Insights](media/availability-azure-functions/create-function-app.png)

        - Selecione a caixa de dropdown de Insights de aplicação e escreva ou selecione o nome do seu recurso.

            ![Seleção do recurso Insights de Aplicação existente](media/availability-azure-functions/app-insights-resource.png)

        - Selecione **Review + criar**
- Se ainda não tiver um Recurso de Insights de Aplicação criado para a sua função acionada pelo temporizador:
    - Por padrão, quando estiver a criar a sua aplicação Funções Azure, criará um recurso Desinformação de Aplicação para si.
    - Siga as instruções sobre como criar um recurso de [funções Azure e função acionada pelo temporizador](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (parar antes de limpar).

## <a name="sample-code"></a>Código de exemplo

Copie o código abaixo no ficheiro run.csx (isto substituirá o código pré-existente). Para isso, entre na aplicação Funções Azure e selecione a função de gatilho do temporizador à esquerda.

>[!div class="mx-imgBorder"]
>![função Azure run.csx no portal Azure](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Para o Endereço Final, utilizaria: `EndpointAddress= https://dc.services.visualstudio.com/v2/track`. A menos que o seu recurso esteja localizado numa região como o Governo Azure ou a Azure China, caso em que consulte este artigo sobre [a sobreposição dos pontos finais padrão](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) e selecione o ponto final apropriado do Canal da Telemetria para a sua região.

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

No lado direito sob os ficheiros de visualização, selecione **Adicionar**. Ligue para a nova **função de ficheiro.proj** com a seguinte configuração.

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
>![Na selecione à direita, adicione. Nomeie a função de ficheiro.proj](media/availability-azure-functions/addfile.png)

No lado direito sob os ficheiros de visualização, selecione **Adicionar**. Ligue para o novo executante de **ficheiroSAvailabilityTest.csx** com a seguinte configuração.

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>Verifique a disponibilidade

Para se certificar de que está tudo a funcionar, pode olhar para o gráfico no separador De Disponibilidade do seu recurso Application Insights.

> [!NOTE]
> Se implementou a sua própria lógica de negócio no runAvailabilityTest.csx, então verá resultados bem sucedidos como nas imagens abaixo, se não o fez, então verá resultados falhados.

>[!div class="mx-imgBorder"]
>![separador disponibilidade com resultados bem-sucedidos](media/availability-azure-functions/availtab.png)

Quando configurar o seu teste utilizando funções Azure, notará que, ao contrário de utilizar o **teste Add** no separador Disponibilidade, o nome do seu teste não aparecerá e não poderá interagir com ele. Os resultados são visualizados mas obtém-se uma visão sumária em vez da mesma vista detalhada que obtém quando cria um teste de disponibilidade através do portal.

Para ver os detalhes da transação de ponta a ponta, selecione **Successful** ou **Failed** under drill e, em seguida, selecione uma amostra. Também pode chegar aos detalhes da transação de ponta a ponta selecionando um ponto de dados no gráfico.

>[!div class="mx-imgBorder"]
>![Selecione um teste de disponibilidade de amostra](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![detalhes da transação final](media/availability-azure-functions/end-to-end.png)

Se correu tudo como está (sem adicionar lógica de negócio), então verá que o teste falhou.

## <a name="query-in-logs-analytics"></a>Consulta em Registos (Analytics)

Pode utilizar Registos (análise) para ver os resultados de disponibilidade, dependências e muito mais. Para saber mais sobre Os Registos, visite a visão geral da [consulta de registo](../../azure-monitor/log-query/log-query-overview.md).

>[!div class="mx-imgBorder"]
>![Resultados da Disponibilidade](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![Dependências](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Passos seguintes

- [Mapeamento de Aplicações](../../azure-monitor/app/app-map.md)
- [Diagnósticos de transações](../../azure-monitor/app/transaction-diagnostics.md)
