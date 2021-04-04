---
title: Como executar Funções Duradouras como WebJobs - Azure
description: Aprenda a codificar e configurar funções duráveis para executar em WebJobs utilizando o WebJobs SDK.
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 826e475eb71563b52d687903aeac4ec936e267f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009505"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Como executar Funções Duradouras como WebJobs

Por predefinição, as Funções Duradouras utilizam o tempo de execução das Funções Azure para acolher orquestrações. No entanto, pode haver certos cenários em que é necessário mais controlo sobre o código que ouve os acontecimentos. Este artigo mostra-lhe como implementar a sua orquestração usando o WebJobs SDK. Para ver uma comparação mais detalhada entre Funções e WebJobs, consulte [Compare Functions e WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[As funções Azure](../functions-overview.md) e a extensão [funções duráveis](durable-functions-overview.md) são construídas no [WebJobs SDK](../../app-service/webjobs-sdk-how-to.md). O anfitrião de trabalho no WebJobs SDK é o tempo de execução em Azure Functions. Se precisar de controlar o comportamento de formas não possíveis nas Funções Azure, pode desenvolver e executar Funções Duradouras utilizando o próprio WebJobs SDK.

Na versão 3.x do WebJobs SDK, o anfitrião é uma implementação de `IHost` , e na versão 2.x você usa o `JobHost` objeto.

A amostra de Funções Duradouras em cadeia está disponível numa versão WebJobs SDK 2.x: descarregue ou clone o [repositório funções duráveis](https://github.com/azure/azure-functions-durable-extension/), e o ramo *v1* de checkout e vá para a pasta *de \\ \\ acorrentamento webjobssdk amostras.*

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que está familiarizado com o básico do desenvolvimento da biblioteca de classe CJobs SDK, C# para Funções Azure e Funções Duradouras. Se precisar de uma introdução a estes tópicos, consulte os seguintes recursos:

* [Começa com o WebJobs SDK](../../app-service/webjobs-sdk-get-started.md)
* [Criar a sua primeira função com o Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Funções duradouras](durable-functions-sequence.md)

Para completar os passos deste artigo:

* [Instale o Visual Studio 2019](/visualstudio/install/) com a carga de trabalho **de desenvolvimento do Azure.**

  Se já tem o Visual Studio, mas não tem essa carga de trabalho, adicione a carga de trabalho selecionando **Ferramentas**  >  **Obter Ferramentas e Funcionalidades**.

  (Em vez disso, pode utilizar [o Código do Estúdio Visual,](https://code.visualstudio.com/) mas algumas das instruções são específicas do Visual Studio.)

* Instale e execute a versão 5.2 ou posterior do [Emulador de Armazenamento Azure.](../../storage/common/storage-use-emulator.md) Uma alternativa é atualizar o ficheiro *App.config* com uma cadeia de ligação Azure Storage.

## <a name="webjobs-sdk-versions"></a>Versões WebJobs SDK

Este artigo explica como desenvolver um projeto WebJobs SDK 2.x (equivalente à versão 1.x do Azure Functions). Para obter informações sobre a versão 3.x, consulte [WebJobs SDK 3.x](#webjobs-sdk-3x) mais tarde neste artigo.

## <a name="create-a-console-app"></a>Criar uma aplicação de consola

Para executar Funções Duradouras como WebJobs, tem primeiro de criar uma aplicação para consolas. Um projeto WebJobs SDK é apenas um projeto de aplicação de consola com os pacotes NuGet apropriados instalados.

Na caixa de diálogo Visual Studio **New Project,** selecione **Windows Classic Desktop** Console App  >  **(.NET Framework)**. No ficheiro do projeto, o `TargetFrameworkVersion` deve ser `v4.6.1` .

O Visual Studio também tem um modelo de projeto WebJob, que pode utilizar selecionando **Cloud**  >  **Azure WebJob (.NET Framework)**. Este modelo instala muitos pacotes, alguns dos quais pode não precisar.

## <a name="install-nuget-packages"></a>Instalar pacotes NuGet

Você precisa de pacotes NuGet para o WebJobs SDK, ligações fundamentais, a estrutura de registo e a extensão de Tarefa Durável. Aqui estão os comandos **da Consola Package Manager** para esses pacotes, com os números de versão mais recentes estáveis a partir da data em que este artigo foi escrito:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

Também precisa de fornecedores de registo. Os seguintes comandos instalam o fornecedor Azure Application Insights e o `ConfigurationManager` . `ConfigurationManager`Permite obter a chave de instrumentação Application Insights a partir das definições de aplicações.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

O seguinte comando instala o fornecedor de consolas:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Código JobHost

Depois de ter criado a aplicação de consola e instalado os pacotes NuGet de que necessita, está pronto para utilizar Funções Duradouras. Fá-lo usando o código JobHost.

Para utilizar a extensão funções duradouras, ligue `UseDurableTask` para o objeto no seu `JobHostConfiguration` `Main` método:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Para obter uma lista de propriedades que pode definir no `DurableTaskExtension` objeto, consulte [host.js.](../functions-host-json.md#durabletask)

O `Main` método é também o local para a criação de fornecedores de exploração madeireira. O exemplo a seguir configura os fornecedores de consolas e de insights de aplicação.

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

Funções duradouras no contexto do WebJobs difere um pouco das Funções Duráveis no contexto das Funções Azure. É importante estar atento às diferenças à medida que escreve o seu código.

O WebJobs SDK não suporta as seguintes funcionalidades Azure Functions:

* [Atributo de nome de função](#functionname-attribute)
* [Acionador HTTP](#http-trigger)
* [Funções Duradouras HTTP Gestão API](#http-management-api)

### <a name="functionname-attribute"></a>Atributo de nome de função

Num projeto WebJobs SDK, o nome do método de uma função é o nome da função. O `FunctionName` atributo é utilizado apenas em Funções Azure.

### <a name="http-trigger"></a>Acionador HTTP

O WebJobs SDK não tem um gatilho HTTP. O cliente de orquestração do projeto de amostra usa um gatilho temporizador:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>API de gestão de GESTÃO HTTP

Como não tem gatilho HTTP, o WebJobs SDK não tem [API de gestão HTTP.](durable-functions-http-api.md)

Num projeto WebJobs SDK, pode ligar para métodos no objeto do cliente de orquestração, em vez de enviar pedidos HTTP. Os seguintes métodos correspondem às três tarefas que pode fazer com a API de gestão HTTP:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

A função do cliente de orquestração no projeto de amostra inicia a função de orquestrador, e depois entra num ciclo que liga `GetStatusAsync` a cada 2 segundos:

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

Você tem Funções Duradouras configurada para funcionar como um WebJob, e agora você tem uma compreensão de como isso vai diferir de executar Funções Duráveis como Funções Azure autónomas. Neste momento, vê-lo funcionar numa amostra pode ser útil.

Esta secção fornece uma visão geral de como executar o projeto de [amostragem.](https://github.com/Azure/azure-functions-durable-extension/tree/v1/samples/webjobssdk/chaining) Para instruções detalhadas que expliquem como executar um projeto WebJobs SDK localmente e implementá-lo num Azure WebJob, consulte [Começar com o WebJobs SDK](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Executar localmente

1. Certifique-se de que o emulador de armazenamento está em funcionamento (ver [Pré-requisitos).](#prerequisites)

1. Se quiser ver registos em Application Insights quando executar o projeto localmente:

    a. Crie um recurso Application Insights e use o tipo de aplicação **Geral** para o mesmo.

    b. Guarde a chave de instrumentação no ficheiro *App.config.*

1. Executar o projeto.

### <a name="run-in-azure"></a>Correr em Azure

1. Crie uma aplicação web e uma conta de armazenamento.

1. Na aplicação web, guarde a cadeia de conexão de armazenamento numa definição de aplicação chamada `AzureWebJobsStorage` .

1. Crie um recurso Application Insights e use o tipo de aplicação **Geral** para o mesmo.

1. Guarde a chave de instrumentação numa definição de aplicação chamada `APPINSIGHTS_INSTRUMENTATIONKEY` .

1. Implemente como WebJob.

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

Este artigo explica como desenvolver um projeto WebJobs SDK 2.x. Se está a desenvolver um projeto [WebJobs SDK 3.x,](../../app-service/webjobs-sdk-get-started.md) esta secção ajuda-o a compreender as diferenças.

A principal alteração introduzida é a utilização de .NET Core em vez de .NET Framework. Para criar um projeto WebJobs SDK 3.x, as instruções são as mesmas, com estas exceções:

1. Crie uma aplicação de consola .NET Core. Na caixa de diálogo Visual Studio **New Project,** selecione **.NET Core**  >  **Console App (.NET Core)**. O ficheiro do projeto especifica que `TargetFramework` é `netcoreapp2.x` .

1. Escolha a versão de lançamento WebJobs SDK 3.x dos seguintes pacotes:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Desave a cadeia de ligação de armazenamento e a tecla de instrumentação Application Insights numa *appsettings.jsem* ficheiro, utilizando a estrutura de configuração .NET Core. Eis um exemplo:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Altere o `Main` código-método para o fazer. Eis um exemplo:

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

Para saber mais sobre o WebJobs SDK, consulte [Como utilizar o WebJobs SDK](../../app-service/webjobs-sdk-how-to.md).
