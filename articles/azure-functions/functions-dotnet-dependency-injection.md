---
title: Utilizar a injeção de dependências nas Funções do Azure do .NET
description: Saiba como utilizar a injeção de dependência para registar e utilizar serviços em funções .NET
author: ggailey777
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/15/2020
ms.author: glenga
ms.reviewer: jehollan
ms.openlocfilehash: f535a27e3afadaf8eefc41c5f1a8ab6c02d24c04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715938"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Utilizar a injeção de dependências nas Funções do Azure do .NET

A Azure Functions suporta o padrão de design de software de injeção de dependência (DI), que é uma técnica para alcançar a [Inversão do Controlo (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre as classes e as suas dependências.

- A injeção de dependência nas funções Azure é construída com base nas funcionalidades de injeção de dependência do núcleo .NET. Recomenda-se a familiaridade com [a injeção de dependência do núcleo .NET.](/aspnet/core/fundamentals/dependency-injection) Existem diferenças na forma como se sobrepõe às dependências e à forma como os valores de configuração são lidos com as Funções Azure no plano de Consumo.

- O suporte para a injeção de dependência começa com as Funções Azure 2.x.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder utilizar a injeção de dependência, tem de instalar os seguintes pacotes NuGet:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) versão 1.0.28 ou posterior

## <a name="register-services"></a>Serviços de registo

Para registar serviços, crie um método para configurar e adicionar componentes a uma `IFunctionsHostBuilder` instância.  O anfitrião Azure Functions cria uma instância `IFunctionsHostBuilder` de e passa-a diretamente para o seu método.

Para registar o método, adicione o `FunctionsStartup` atributo de montagem que especifica o tipo de nome utilizado durante o arranque.

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton<IMyService>((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

Este exemplo utiliza o pacote [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) necessário para registar um `HttpClient` no arranque.

### <a name="caveats"></a>Ressalvas

Uma série de etapas de registo executadas antes e depois do tempo de execução processa a classe de arranque. Portanto, tenha em mente os seguintes itens:

- *A classe de arranque destina-se apenas à configuração e inscrição.* Evite utilizar serviços registados no arranque durante o processo de arranque. Por exemplo, não tente registar uma mensagem num madeireiro que está a ser registado durante o arranque. Este ponto do processo de registo é muito cedo para que os seus serviços estejam disponíveis para uso. Após a execução do `Configure` método, o tempo de execução das Funções continua a registar dependências adicionais, o que pode afetar o funcionamento dos seus serviços.

- *O recipiente de injeção de dependência só contém tipos explicitamente registados*. Os únicos serviços disponíveis como tipos injetáveis são o que estão configurados no `Configure` método. Como resultado, tipos específicos de funções como `BindingContext` e `ExecutionContext` não estão disponíveis durante a configuração ou como tipos injetáveis.

## <a name="use-injected-dependencies"></a>Utilize dependências injetadas

A injeção de construtor é utilizada para disponibilizar as suas dependências numa função. A utilização de injeção de construtor requer que não utilize classes estáticas para serviços injetados ou para as suas classes de funções.

A amostra a seguir demonstra como as `IMyService` `HttpClient` dependências são injetadas numa função acionada por HTTP.

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;
using System.Threading.Tasks;

namespace MyNamespace
{
    public class MyHttpTrigger
    {
        private readonly HttpClient _client;
        private readonly IMyService _service;

        public MyHttpTrigger(HttpClient httpClient, IMyService service)
        {
            this._client = httpClient;
            this._service = service;
        }

        [FunctionName("MyHttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            var response = await _client.GetAsync("https://microsoft.com");
            var message = _service.GetMessage();

            return new OkObjectResult("Response from function with injected dependencies.");
        }
    }
}
```

Este exemplo utiliza o pacote [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) necessário para registar um `HttpClient` no arranque.

## <a name="service-lifetimes"></a>Vida útil

As aplicações Azure Functions fornecem as mesmas vidas de serviço [que ASP.NET Injeção de Dependência](/aspnet/core/fundamentals/dependency-injection#service-lifetimes). Para uma aplicação Funções, as diferentes vidas de serviço comportam-se da seguinte forma:

- **Transitório:** Os serviços transitórios são criados a cada pedido do serviço.
- **Âmbito :** A vida útil do serviço de âmbito corresponde a uma execução útil da função. Os serviços de âmbito são criados uma vez por execução. Os pedidos posteriores para esse serviço durante a execução reutilizam a instância de serviço existente.
- **Singleton**: A vida útil de singleton corresponde à vida útil do hospedeiro e é reutilizada através de execuções de funções nesse caso. Os serviços de vida da Singleton são recomendados para ligações e clientes, por exemplo `DocumentClient` ou `HttpClient` casos.

Veja ou descarregue uma [amostra de diferentes vidas de serviço](https://github.com/Azure/azure-functions-dotnet-extensions/tree/main/src/samples/DependencyInjection/Scopes) no GitHub.

## <a name="logging-services"></a>Serviço de registo

Se precisar do seu próprio fornecedor de registo, registe um tipo personalizado como uma instância de [`ILoggerProvider`](/dotnet/api/microsoft.extensions.logging.iloggerfactory) , que está disponível através do pacote [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) NuGet.

O Application Insights é adicionado automaticamente pelas Funções Azure.

> [!WARNING]
> - Não adicione `AddApplicationInsightsTelemetry()` à recolha de serviços uma vez que regista serviços que entram em conflito com os serviços prestados pelo meio ambiente.
> - Não registe o seu próprio `TelemetryConfiguration` ou se estiver a utilizar a funcionalidade De Insights de `TelemetryClient` Aplicação incorporada. Se precisar de configurar o seu próprio `TelemetryClient` exemplo, crie um através do injetado, `TelemetryConfiguration` como mostrado nas [Funções de Azure do Monitor](./functions-monitoring.md#version-2x-and-later-2).

### <a name="iloggert-and-iloggerfactory"></a>ILogger <T> e ILoggerFactory

O hospedeiro injeta `ILogger<T>` e `ILoggerFactory` serviços em construtores.  No entanto, por predefinição, estes novos filtros de registo são filtrados fora dos registos de funções.  É necessário modificar o `host.json` ficheiro para optar por filtros e categorias adicionais.

O exemplo a seguir demonstra como adicionar um `ILogger<HttpTrigger>` com troncos expostos ao hospedeiro.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

O ficheiro de exemplo a seguir `host.json` adiciona o filtro de registo.

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingSettings": {
                "isEnabled": true,
                "excludedTypes": "Request"
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

## <a name="function-app-provided-services"></a>App de função prestado serviços

O anfitrião da função regista muitos serviços. Os seguintes serviços são seguros para assumir como uma dependência na sua aplicação:

|Tipo de Serviço|Vida útil|Descrição|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Rio Singleton|Configuração do tempo de execução|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Rio Singleton|Responsável por fornecer a ID da instância de anfitrião|

Se houver outros serviços em que pretende assumir uma dependência, [crie um problema e proponha-os no GitHub.](https://github.com/azure/azure-functions-host)

### <a name="overriding-host-services"></a>Serviços de acolhimento dominantes

Os serviços de sobreposição prestados pelo anfitrião não são atualmente suportados.  Se houver serviços que pretende anular, [crie um problema e proponha-os no GitHub.](https://github.com/azure/azure-functions-host)

## <a name="working-with-options-and-settings"></a>Trabalhar com opções e configurações

Os [valores definidos](./functions-how-to-use-azure-function-app-settings.md#settings) nas definições de apps estão disponíveis num `IConfiguration` caso que lhe permite ler os valores de definições de aplicações na classe de arranque.

Pode extrair valores da `IConfiguration` instância para um tipo personalizado. Copiar os valores de definições da aplicação para um tipo personalizado torna fácil testar os seus serviços tornando estes valores injetáveis. As definições lidas na instância de configuração devem ser simples pares de chaves/valor.

Considere a seguinte classe que inclui uma propriedade nomeada consistente com uma configuração de aplicação:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

E um `local.settings.json` ficheiro que pode estruturar a definição personalizada da seguinte forma:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

A partir do interior do `Startup.Configure` método, pode extrair valores da instância para o `IConfiguration` seu tipo personalizado utilizando o seguinte código:

```csharp
builder.Services.AddOptions<MyOptions>()
    .Configure<IConfiguration>((settings, configuration) =>
    {
        configuration.GetSection("MyOptions").Bind(settings);
    });
```

Chamar `Bind` valores de cópias que têm nomes de propriedade correspondentes da configuração para a instância personalizada. A instância de opções está agora disponível no recipiente IoC para injetar numa função.

O objeto de opções é injetado na função como um exemplo da `IOptions` interface genérica. Utilize a `Value` propriedade para aceder aos valores encontrados na sua configuração.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

Consulte o [padrão de Opções em ASP.NET Core](/aspnet/core/fundamentals/configuration/options) para obter mais detalhes sobre o trabalho com opções.

## <a name="customizing-configuration-sources"></a>Personalizar fontes de configuração

> [!NOTE]
> A personalização da fonte de configuração está disponível a partir das versões anfitriões do Azure Functions 2.0.14192.0 e 3.0.14191.0.

Para especificar fontes de configuração adicionais, substitua o `ConfigureAppConfiguration` método na classe da sua aplicação de funções. `StartUp`

A amostra seguinte adiciona valores de configuração a partir de uma base e de um opcional ficheiro de configurações de aplicações específicas para o ambiente.

```csharp
using System.IO;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
        {
            FunctionsHostBuilderContext context = builder.GetContext();

            builder.ConfigurationBuilder
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, "appsettings.json"), optional: true, reloadOnChange: false)
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, $"appsettings.{context.EnvironmentName}.json"), optional: true, reloadOnChange: false)
                .AddEnvironmentVariables();
        }
    }
}
```

Adicione fornecedores de configuração à `ConfigurationBuilder` propriedade de `IFunctionsConfigurationBuilder` . Para obter mais informações sobre a utilização de fornecedores de configuração, consulte [Configuração no ASP.NET Core](/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.1#configuration-providers).

A `FunctionsHostBuilderContext` é obtida a partir de `IFunctionsConfigurationBuilder.GetContext()` . Utilize este contexto para recuperar o nome do ambiente atual e resolver a localização dos ficheiros de configuração na pasta da aplicação de funções.

Por predefinição, ficheiros de configuração como *appsettings.jsligados* não são automaticamente copiados para a pasta de saída da aplicação de função. Atualize o seu ficheiro *.csproj* para corresponder à seguinte amostra para garantir que os ficheiros são copiados.

```xml
<None Update="appsettings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>      
</None>
<None Update="appsettings.Development.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
</None>
```

> [!IMPORTANT]
> Para aplicações de funções em execução nos planos Consumption ou Premium, as modificações nos valores de configuração utilizados nos gatilhos podem causar erros de escala. Qualquer alteração a estas propriedades pela `FunctionsStartup` classe resulta num erro de arranque de aplicações de função.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os seguintes recursos:

- [Como monitorizar a sua aplicação de função](functions-monitoring.md)
- [Melhores práticas para funções](functions-best-practices.md)
