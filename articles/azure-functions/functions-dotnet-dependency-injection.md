---
title: Utilizar a injeção de dependências nas Funções do Azure do .NET
description: Saiba como usar a injeção de dependência para registar e utilizar serviços em funções .NET
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: 97e8a34f3b8639990f8de736a8f1f7429ebfd448
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83739146"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Utilizar a injeção de dependências nas Funções do Azure do .NET

A Azure Functions suporta o padrão de design de software de injeção de dependência (DI), que é uma técnica para alcançar a [Inversão de Controlo (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre as classes e as suas dependências.

- A injeção de dependência nas Funções Azure é construída com base nas características de injeção de dependência do núcleo .NET. Recomenda-se a familiaridade com a injeção de dependência do [núcleo .NET.](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) Existem diferenças na forma como sobrepor as dependências e como os valores de configuração são lidos com funções azure no plano de consumo.

- O suporte à injeção de dependência começa com as Funções Azure 2.x.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder utilizar a injeção de dependência, tem de instalar os seguintes pacotes NuGet:

- [Microsoft.Azure.Functions.Extensões](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) versão de pacote 1.0.28 ou mais tarde

## <a name="register-services"></a>Serviços de registo

Para registar serviços, crie um método para configurar e adicionar componentes a uma `IFunctionsHostBuilder` instância.  O hospedeiro funções Azure cria uma instância `IFunctionsHostBuilder` e passa-a diretamente para o seu método.

Para registar o método, adicione o atributo de `FunctionsStartup` montagem que especifica o nome tipo utilizado durante o arranque.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

### <a name="caveats"></a>Ressalvas

Uma série de etapas de registo são executadas antes e depois do tempo de execução processa a classe de startups. Por isso, tenha em mente os seguintes itens:

- *A classe de startups destina-se apenas à configuração e inscrição.* Evite utilizar serviços registados no arranque durante o processo de arranque. Por exemplo, não tente registar uma mensagem num madeireiro que está a ser registado durante o arranque. Este ponto do processo de registo é muito cedo para que os seus serviços estejam disponíveis para uso. Após a execução do método, o tempo de `Configure` funcionamento das Funções continua a registar dependências adicionais, o que pode afetar o funcionamento dos seus serviços.

- O recipiente de *injeção de dependência contém apenas tipos explicitamente registados*. Os únicos serviços disponíveis como tipos injetáveis são os que são configurados no `Configure` método. Como resultado, tipos específicos de funções como e não estão disponíveis durante a `BindingContext` `ExecutionContext` configuração ou como tipos injetáveis.

## <a name="use-injected-dependencies"></a>Use dependências injetadas

A injeção de construtor é usada para disponibilizar as suas dependências numa função. A utilização da injeção de construtor requer que não utilize classes estáticas.

A amostra seguinte demonstra como as `IMyService` dependências e `HttpClient` dependências são injetadas numa função desencadeada pelo HTTP. Este exemplo utiliza o pacote [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) necessário para registar um `HttpClient` no arranque.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, HttpClient httpClient)
        {
            _service = service;
            _client = httpClient;
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

## <a name="service-lifetimes"></a>Vida útil

As aplicações Azure Functions fornecem as mesmas vidas de serviço que [ASP.NET Injeção de Dependência.](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes) Para uma aplicação Functions, as diferentes vidas de serviço comportam-se da seguinte forma:

- **Transitória**: Os serviços transitórios são criados a cada pedido do serviço.
- **Âmbito:** A vida útil do serviço com âmbito de aplicação corresponde a uma vida útil de execução de função. Os serviços de âmbito são criados uma vez por execução. Posteriormente, os pedidos para esse serviço durante a execução reutilizam a instância de serviço existente.
- **Singleton**: O serviço singleton corresponde à vida útil do hospedeiro e é reutilizado através de execuções de funções nesse caso. Os serviços de vida singleton são recomendados para ligações e clientes, por exemplo `SqlConnection` ou `HttpClient` por exemplo.

Ver ou descarregar uma [amostra de diferentes vidas de serviço](https://aka.ms/functions/di-sample) no GitHub.

## <a name="logging-services"></a>Serviços de exploração madeireira

Se precisar do seu próprio fornecedor de registo, registe um tipo personalizado como `ILoggerProvider` instância. Os Insights de Aplicação são adicionados automaticamente pelas Funções Azure.

> [!WARNING]
> - Não adicione à recolha de serviços uma `AddApplicationInsightsTelemetry()` vez que regista serviços que entram em conflito com os serviços prestados pelo ambiente.
> - Não registe o seu próprio `TelemetryConfiguration` registo ou se estiver a utilizar a funcionalidade `TelemetryClient` Inejádininsights de Aplicação. Se precisar configurar a sua própria `TelemetryClient` instância, crie uma através da injetada `TelemetryConfiguration` como mostrado nas [Funções Monitor Azure](./functions-monitoring.md#version-2x-and-later-2).

### <a name="iloggert-and-iloggerfactory"></a>ILogger <T> e ILoggerFactory

O hospedeiro injetará `ILogger<T>` e `ILoggerFactory` serviços em construtores.  No entanto, por defeito, estes novos filtros de registo serão filtrados dos registos de função.  Terá de modificar o `host.json` ficheiro para optar por filtros e categorias adicionais.  A amostra seguinte demonstra a adição de um `ILogger<HttpTrigger>` com registos que serão expostos pelo hospedeiro.

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

E um `host.json` ficheiro que adiciona o filtro de registo.

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingExcludedTypes": "Request",
            "samplingSettings": {
                "isEnabled": true
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

## <a name="function-app-provided-services"></a>App de funções prestada serviços

O anfitrião da função regista muitos serviços. Os seguintes serviços são seguros de assumir como uma dependência na sua aplicação:

|Tipo de Serviço|Vida útil|Descrição|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Rio Singleton|Configuração do tempo de execução|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Rio Singleton|Responsável por fornecer a identificação da instância anfitriã|

Se houver outros serviços em que quer assumir uma dependência, [crie um problema e proponha-os no GitHub.](https://github.com/azure/azure-functions-host)

### <a name="overriding-host-services"></a>Serviços de acolhimento predominantes

Os serviços de sobreposição prestados pelo anfitrião não são atualmente suportados.  Se houver serviços que pretende anular, [crie um problema e proponha-os no GitHub.](https://github.com/azure/azure-functions-host)

## <a name="working-with-options-and-settings"></a>Trabalhar com opções e configurações

Os valores definidos nas definições da [aplicação](./functions-how-to-use-azure-function-app-settings.md#settings) estão disponíveis num `IConfiguration` caso, o que lhe permite ler os valores de definições de aplicações na classe de startups.

Pode extrair valores da `IConfiguration` instância para um tipo personalizado. Copiar os valores de definições da aplicação para um tipo personalizado facilita o teste dos seus serviços tornando estes valores injetáveis. As definições lidas na configuração devem ser simples pares de chaves/valor.

Considere a seguinte classe que inclui uma propriedade chamada consistente com uma definição de app:

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

Chamar `Bind` cópias valores que têm nomes de propriedade correspondentes da configuração para a instância personalizada. A instância de opções está agora disponível no recipiente IoC para injetar numa função.

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

Consulte o [padrão opções em ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) para obter mais detalhes sobre o trabalho com opções.

> [!WARNING]
> Evite tentar ler valores a partir de ficheiros como *local.settings.json* ou *appsettings.{ ambiente}.json* sobre o plano de consumo. Os valores lidos a partir destes ficheiros relacionados com ligações de gatilho não estão disponíveis à medida que a app se esescala porque a infraestrutura de hospedagem não tem acesso à informação de configuração.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte os seguintes recursos:

- [Como monitorizar a sua aplicação de funções](functions-monitoring.md)
- [Boas práticas para funções](functions-best-practices.md)
