---
title: Como executar Durable Functions como trabalhos Web-Azure
description: Saiba como codificar e configurar o Durable Functions para executar em trabalhos Web usando o SDK de trabalhos Web.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 06f2019dbaff390e88c73d1aae7a635a34a64721
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614616"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Como executar Durable Functions como trabalhos Web

Por padrão, Durable Functions usa o tempo de execução Azure Functions para hospedar orquestrações. No entanto, pode haver alguns cenários em que você precisa de mais controle sobre o código que escuta eventos. Este artigo mostra como implementar sua orquestração usando o SDK de trabalhos Web. Para ver uma comparação mais detalhada entre funções e trabalhos Web, consulte [comparar funções e trabalhos](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)Web.

[Azure Functions](../functions-overview.md) e a extensão de [Durable Functions](durable-functions-overview.md) são criadas no [SDK de trabalhos](../../app-service/webjobs-sdk-how-to.md)Web. O host de trabalho no SDK de trabalhos Web é o tempo de execução no Azure Functions. Se você precisar controlar o comportamento de maneiras não possíveis no Azure Functions, você poderá desenvolver e executar Durable Functions usando o SDK de trabalhos Web por conta própria.

Na versão 3. x do SDK de trabalhos Web, o host é uma implementação de `IHost`e, na versão 2. x, você usa o objeto `JobHost`.

O exemplo de encadeamento de Durable Functions está disponível em uma versão do SDK do webjobs 2. x: Baixe ou clone o [repositório de durable Functions](https://github.com/azure/azure-functions-durable-extension/)e vá para os *exemplos\\pasta webjobssdk\\de encadeamento* .

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você esteja familiarizado com os conceitos básicos do SDK de trabalhos C# Web, desenvolvimento de biblioteca de classes para Azure Functions e Durable functions. Se você precisar de uma introdução a esses tópicos, consulte os seguintes recursos:

* [Introdução ao SDK de trabalhos Web](../../app-service/webjobs-sdk-get-started.md)
* [Criar a sua primeira função com o Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Durable Functions](durable-functions-sequence.md)

Para concluir as etapas neste artigo:

* [Instale o Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/) com a carga de trabalho de **desenvolvimento do Azure** .

  Se você já tiver o Visual Studio, mas não tiver essa carga de trabalho, adicione a carga de trabalho selecionando **ferramentas** > **obter ferramentas e recursos**.

  (Você pode usar [Visual Studio Code](https://code.visualstudio.com/) em vez disso, mas algumas das instruções são específicas para o Visual Studio.)

* Instale e execute o [emulador de armazenamento do Azure](../../storage/common/storage-use-emulator.md) versão 5,2 ou posterior. Uma alternativa é atualizar o arquivo *app. config* com uma cadeia de conexão de armazenamento do Azure.

## <a name="webjobs-sdk-versions"></a>Versões do SDK de trabalhos Web

Este artigo explica como desenvolver um projeto webjobs SDK 2. x (equivalente a Azure Functions versão 1. x). Para obter informações sobre a versão 3. x, consulte [SDK de trabalhos Web 3. x](#webjobs-sdk-3x) mais adiante neste artigo.

## <a name="create-a-console-app"></a>Criar uma aplicação de consola

Para executar Durable Functions como trabalhos Web, você deve primeiro criar um aplicativo de console. Um projeto do SDK de trabalhos Web é apenas um projeto de aplicativo de console com os pacotes NuGet apropriados instalados.

Na caixa de diálogo **novo projeto** do Visual Studio, selecione **área de trabalho clássica do Windows** > **aplicativo de console (.NET Framework)** . No arquivo de projeto, o `TargetFrameworkVersion` deve ser `v4.6.1`.

O Visual Studio também tem um modelo de projeto WebJob, que pode ser usado selecionando **nuvem** > **WebJob do Azure (.NET Framework)** . Esse modelo instala vários pacotes, alguns dos quais você talvez não precise.

## <a name="install-nuget-packages"></a>Instalar pacotes NuGet

Você precisa de pacotes NuGet para o SDK de trabalhos Web, associações de núcleo, a estrutura de log e a extensão de tarefa durável. Aqui estão os comandos do **console do Gerenciador de pacotes** para esses pacotes, com os números de versão mais recentes estáveis a partir da data em que este artigo foi escrito:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

Você também precisa de provedores de registro em log. Os comandos a seguir instalam o provedor do Aplicativo Azure insights e o `ConfigurationManager`. O `ConfigurationManager` permite que você obtenha a chave de instrumentação Application Insights das configurações do aplicativo.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

O comando a seguir instala o provedor de console do:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Código JobHost

Depois de criar o aplicativo de console e instalar os pacotes NuGet necessários, você estará pronto para usar Durable Functions. Você faz isso usando o código JobHost.

Para usar a extensão Durable Functions, chame `UseDurableTask` no objeto `JobHostConfiguration` em seu método `Main`:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Para obter uma lista de propriedades que podem ser definidas no objeto `DurableTaskExtension`, consulte [host. JSON](../functions-host-json.md#durabletask).

O método `Main` também é o local para configurar provedores de log. O exemplo a seguir configura o console e os provedores de Application Insights.

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

Durable Functions no contexto de trabalhos Web difere um pouco de Durable Functions no contexto de Azure Functions. É importante estar ciente das diferenças à medida que você escreve seu código.

O SDK de trabalhos Web não dá suporte aos seguintes recursos de Azure Functions:

* [Atributo FunctionName](#functionname-attribute)
* [Gatilho HTTP](#http-trigger)
* [API de gerenciamento de HTTP Durable Functions](#http-management-api)

### <a name="functionname-attribute"></a>Atributo FunctionName

Em um projeto do SDK de trabalhos Web, o nome do método de uma função é o nome da função. O atributo `FunctionName` é usado somente em Azure Functions.

### <a name="http-trigger"></a>Acionador HTTP

O SDK de trabalhos Web não tem um gatilho HTTP. O cliente de orquestração do projeto de exemplo usa um gatilho de temporizador:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>API de gerenciamento HTTP

Como não tem nenhum gatilho HTTP, o SDK de trabalhos Web não tem nenhuma [API de gerenciamento http](durable-functions-http-api.md).

Em um projeto do SDK de trabalhos Web, você pode chamar métodos no objeto de cliente Orchestration, em vez de enviar solicitações HTTP. Os métodos a seguir correspondem às três tarefas que você pode fazer com a API de gerenciamento HTTP:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

A função de cliente de orquestração no projeto de exemplo inicia a função de orquestrador e entra em um loop que chama `GetStatusAsync` a cada 2 segundos:

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

Você tem Durable Functions configurado para ser executado como um WebJob, e agora você tem uma compreensão de como isso será diferente de executar Durable Functions como Azure Functions autônomo. Neste ponto, a exibição do trabalho em um exemplo pode ser útil.

Esta seção fornece uma visão geral de como executar o [projeto de exemplo](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Para obter instruções detalhadas que explicam como executar um projeto do SDK de trabalhos Web localmente e implantá-lo em um WebJob do Azure, consulte Introdução ao [SDK do webjobs](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Executar localmente

1. Verifique se o emulador de armazenamento está em execução (consulte [pré-requisitos](#prerequisites)).

1. Se você quiser ver os logs no Application Insights ao executar o projeto localmente:

    a. Crie um recurso de Application Insights e use o tipo de aplicativo **geral** para ele.

    b. Salve a chave de instrumentação no arquivo *app. config* .

1. Execute o projeto.

### <a name="run-in-azure"></a>Executar no Azure

1. Crie um aplicativo Web e uma conta de armazenamento.

1. No aplicativo Web, salve a cadeia de conexão de armazenamento em uma configuração de aplicativo chamada `AzureWebJobsStorage`.

1. Crie um recurso de Application Insights e use o tipo de aplicativo **geral** para ele.

1. Salve a chave de instrumentação em uma configuração de aplicativo chamada `APPINSIGHTS_INSTRUMENTATIONKEY`.

1. Implante como um WebJob.

## <a name="webjobs-sdk-3x"></a>SDK de trabalhos Web 3. x

Este artigo explica como desenvolver um projeto do SDK de trabalhos Web 2. x. Se você estiver desenvolvendo um projeto do [SDK do webjobs 3. x](../../app-service/webjobs-sdk-get-started.md) , esta seção o ajudará a entender as diferenças.

A principal alteração introduzida é o uso do .NET Core em vez de .NET Framework. Para criar um projeto webjobs SDK 3. x, as instruções são as mesmas, com estas exceções:

1. Crie um aplicativo de console do .NET Core. Na caixa de diálogo **novo projeto** do Visual Studio, selecione **.NET Core** > **aplicativo de console (.NET Core)** . O arquivo de projeto especifica que `TargetFramework` é `netcoreapp2.x`.

1. Escolha a versão de lançamento SDK 3. x dos seguintes pacotes:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Defina a cadeia de conexão de armazenamento e a chave de instrumentação de Application Insights em um arquivo *appSettings. JSON* , usando a estrutura de configuração do .NET Core. Segue-se um exemplo:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Altere o código do método `Main` para fazer isso. Segue-se um exemplo:

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

Para saber mais sobre o SDK de trabalhos Web, consulte [como usar o SDK de trabalhos](../../app-service/webjobs-sdk-how-to.md)Web.
