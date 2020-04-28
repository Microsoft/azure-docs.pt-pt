---
title: Como executar Funções Duráveis como WebJobs - Azure
description: Aprenda a codificar e configurar Funções Duráveis para executar em WebJobs utilizando o WebJobs SDK.
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: d8dd0c86fbc520d0bd3ef6034891bd9871774b4a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74232730"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Como executar funções duráveis como WebJobs

Por predefinição, as Funções Duráveis utilizam o tempo de funcionamento das Funções Azure para acolher orquestrações. No entanto, pode haver certos cenários em que você precisa de mais controlo sobre o código que ouve para os eventos. Este artigo mostra-lhe como implementar a sua orquestração usando o WebJobs SDK. Para ver uma comparação mais detalhada entre Funções e WebJobs, consulte [Compare Functions e WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[As funções azure](../functions-overview.md) e a extensão [funções duráveis](durable-functions-overview.md) são construídas no [WebJobs SDK](../../app-service/webjobs-sdk-how-to.md). O anfitrião do trabalho no WebJobs SDK é o tempo de funcionamento em Funções Azure. Se precisar de controlar o comportamento de formas não possíveis nas Funções Azure, pode desenvolver e executar Funções Duráveis utilizando o WebJobs SDK.

Na versão 3.x do WebJobs SDK, o `IHost`anfitrião é uma implementação `JobHost` de , e na versão 2.x utiliza o objeto.

A amostra de funções duráveis em cadeia está disponível numa versão WebJobs SDK 2.x: descarregue ou clone o [repositório de Funções Duráveis,](https://github.com/azure/azure-functions-durable-extension/)e vá para as *amostras\\webjobssdk\\chaining* pasta.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que está familiarizado com os fundamentos do WebJobs SDK, c# desenvolvimento da biblioteca da classe C# para funções azure, e Funções Duráveis. Se precisar de uma introdução a estes tópicos, consulte os seguintes recursos:

* [Começar com o WebJobs SDK](../../app-service/webjobs-sdk-get-started.md)
* [Criar a sua primeira função com o Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Funções Duráveis](durable-functions-sequence.md)

Para completar os passos deste artigo:

* [Instale o Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/) com a carga de trabalho de **desenvolvimento do Azure.**

  Se já tem o Estúdio Visual, mas não tem essa carga de trabalho, adicione a carga de trabalho selecionando **Ferramentas** > **Get Tools e Funcionalidades**.

  (Pode utilizar o [Código do Estúdio Visual](https://code.visualstudio.com/) em vez disso, mas algumas das instruções são específicas do Visual Studio.)

* Instale e execute a versão 5.2 ou posterior do [Emulador de Armazenamento Azure.](../../storage/common/storage-use-emulator.md) Uma alternativa é atualizar o ficheiro *App.config* com uma cadeia de ligação de armazenamento Azure.

## <a name="webjobs-sdk-versions"></a>Versões WebJobs SDK

Este artigo explica como desenvolver um projeto WebJobs SDK 2.x (equivalente à versão 1.x das Funções Azure). Para obter informações sobre a versão 3.x, consulte [WebJobs SDK 3.x](#webjobs-sdk-3x) mais tarde neste artigo.

## <a name="create-a-console-app"></a>Criar uma aplicação de consola

Para executar Funções Duráveis como WebJobs, primeiro tem de criar uma aplicação de consola. Um projeto WebJobs SDK é apenas um projeto de aplicação de consola com os pacotes NuGet apropriados instalados.

Na caixa de diálogo do Estúdio Visual **New Project,** selecione **Windows Classic Desktop** > **Console App (.NET Framework)**. No ficheiro do `TargetFrameworkVersion` projeto, `v4.6.1`deve ser.

O Visual Studio também tem um modelo de projeto WebJob, que pode utilizar selecionando **Cloud** > **Azure WebJob (.NET Framework)**. Este modelo instala muitos pacotes, alguns dos quais você pode não precisar.

## <a name="install-nuget-packages"></a>Instalar pacotes NuGet

Você precisa de pacotes NuGet para o WebJobs SDK, ligações nucleares, a estrutura de registo e a extensão de Tarefa Durável. Aqui estão os comandos de consola do Gestor de **Pacotes** para esses pacotes, com os últimos números de versão estável a partir da data em que este artigo foi escrito:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

Também precisa de fornecedores de exploração madeireira. Os seguintes comandos instalam o `ConfigurationManager`fornecedor de Insights de Aplicação Azure e o . O `ConfigurationManager` permite-lhe obter a chave de instrumentação Application Insights a partir de definições de aplicações.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

O seguinte comando instala o fornecedor de consolas:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Código JobHost

Tendo criado a aplicação de consola e instalado os pacotes NuGet de que necessita, está pronto para utilizar Funções Duráveis. Fá-lo utilizando o código JobHost.

Para utilizar a extensão `UseDurableTask` das `JobHostConfiguration` Funções `Main` Duráveis, ligue para o objeto no seu método:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Para obter uma lista de propriedades `DurableTaskExtension` que pode definir no objeto, consulte [host.json](../functions-host-json.md#durabletask).

O `Main` método é também o local para criar fornecedores de exploração madeireira. O exemplo seguinte configura os fornecedores de consola e de Aplicação Insights.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Funções

Funções Duráveis no contexto do WebJobs difere um pouco das Funções Duráveis no contexto das Funções Azure. É importante estar atento às diferenças enquanto escreve o seu código.

O WebJobs SDK não suporta as seguintes funcionalidades de Funções Azure:

* [Atributo nome de função](#functionname-attribute)
* [Acionador HTTP](#http-trigger)
* [Funções Duráveis HTTP gestão API](#http-management-api)

### <a name="functionname-attribute"></a>Atributo nome de função

Num projeto WebJobs SDK, o nome de método de uma função é o nome da função. O `FunctionName` atributo é usado apenas em Funções Azure.

### <a name="http-trigger"></a>Acionador HTTP

O WebJobs SDK não tem um gatilho HTTP. O cliente de orquestração do projeto de amostra utiliza um gatilho temporizador:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>Http gestão API

Como não tem gatilho HTTP, o WebJobs SDK não tem [API de gestão HTTP](durable-functions-http-api.md).

Num projeto WebJobs SDK, pode ligar para métodos sobre o objeto do cliente da orquestração, em vez de enviar pedidos HTTP. Os seguintes métodos correspondem às três tarefas que pode fazer com a API de gestão http:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

A função do cliente de orquestração no projeto de amostra inicia `GetStatusAsync` a função de orquestrador, e depois entra num loop que liga a cada 2 segundos:

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>Executar o exemplo

Tem funções duráveis configuradas para funcionar como um WebJob, e agora tem uma compreensão de como isso vai diferir de executar Funções Duráveis como Funções Azure autónomas. Neste momento, vê-lo funcionar numa amostra pode ser útil.

Esta secção fornece uma visão geral de como executar o [projeto da amostra.](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining) Para instruções detalhadas que expliquem como executar um projeto WebJobs SDK localmente e implementá-lo para um WebJob Azure, consulte [Get started with the WebJobs SDK](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Executar localmente

1. Certifique-se de que o emulador de armazenamento está em funcionamento (ver [Pré-requisitos).](#prerequisites)

1. Se quiser ver registos em Insights de Aplicação quando executa o projeto localmente:

    a. Crie um recurso Deinsights de Aplicação e utilize o tipo de aplicação **geral** para o mesmo.

    b. Guarde a chave de instrumentação no ficheiro *App.config.*

1. Executar o projeto.

### <a name="run-in-azure"></a>Correr em Azure

1. Crie uma aplicação web e uma conta de armazenamento.

1. Na aplicação web, guarde a cadeia `AzureWebJobsStorage`de ligação de armazenamento numa definição de aplicação chamada .

1. Crie um recurso Deinsights de Aplicação e utilize o tipo de aplicação **geral** para o mesmo.

1. Guarde a chave de instrumentação numa definição de aplicação chamada `APPINSIGHTS_INSTRUMENTATIONKEY`.

1. Implemente como um WebJob.

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

Este artigo explica como desenvolver um projeto WebJobs SDK 2.x. Se estiver a desenvolver um projeto [WebJobs SDK 3.x,](../../app-service/webjobs-sdk-get-started.md) esta secção ajuda-o a compreender as diferenças.

A principal alteração introduzida é a utilização de .NET Core em vez de .NET Framework. Para criar um projeto WebJobs SDK 3.x, as instruções são as mesmas, com estas exceções:

1. Crie uma aplicação de consola .NET Core. Na caixa de diálogo Do **Novo Projeto** do Estúdio Visual, selecione **.NET Core** > **Console App (.NET Core)**. O ficheiro do `TargetFramework` projeto `netcoreapp2.x`especifica que é .

1. Escolha a versão de lançamento WebJobs SDK 3.x dos seguintes pacotes:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Delineie a cadeia de ligação de armazenamento e a chave de instrumentação Application Insights num ficheiro *appsettings.json,* utilizando a estrutura de configuração .NET Core. Segue-se um exemplo:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Mude `Main` o código de método para fazer isto. Segue-se um exemplo:

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o WebJobs SDK, consulte [como utilizar o WebJobs SDK](../../app-service/webjobs-sdk-how-to.md).
