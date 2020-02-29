---
title: Utilize a injeção de dependência em funções .NET Azure
description: Saiba como usar a injeção de dependência para registar e utilizar serviços em funções .NET
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: 1aff2815144f776b351e92d8945b267d1451f9f6
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77915712"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Utilize a injeção de dependência em funções .NET Azure

A Azure Functions suporta o padrão de design de software de injeção de dependência (DI), que é uma técnica para alcançar a [Inversão de Controlo (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre as classes e as suas dependências.

- A injeção de dependência nas Funções Azure é construída com base nas características de injeção de dependência do núcleo .NET. Recomenda-se a familiaridade com a injeção de dependência do [núcleo .NET.](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) Existem, no entanto, diferenças na forma como se sobrepõem as dependências e como os valores de configuração são lidos com as Funções Azure no plano de Consumo.

- O suporte à injeção de dependência começa com as Funções Azure 2.x.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder utilizar a injeção de dependência, tem de instalar os seguintes pacotes NuGet:

- [Microsoft.Azure.Functions.Extensões](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions versão](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) de pacote 1.0.28 ou mais tarde

## <a name="register-services"></a>Serviços de registo

Para registar serviços, crie um método para configurar e adicionar componentes a uma instância `IFunctionsHostBuilder`.  O hospedeiro funções Azure cria uma instância de `IFunctionsHostBuilder` e passa-a diretamente para o seu método.

Para registar o método, adicione o atributo de montagem `FunctionsStartup` que especifica o nome tipo utilizado durante o arranque.

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

- *A classe de startups destina-se apenas à configuração e inscrição.* Evite utilizar serviços registados no arranque durante o processo de arranque. Por exemplo, não tente registar uma mensagem num madeireiro que está a ser registado durante o arranque. Este ponto do processo de registo é muito cedo para que os seus serviços estejam disponíveis para uso. Após a execução do método `Configure`, o tempo de funcionamento das Funções continua a registar dependências adicionais, o que pode afetar o funcionamento dos seus serviços.

- O recipiente de *injeção de dependência contém apenas tipos explicitamente registados*. Os únicos serviços disponíveis como tipos injetáveis são os que são configurados no método `Configure`. Como resultado, tipos específicos de funções como `BindingContext` e `ExecutionContext` não estão disponíveis durante a configuração ou como tipos injetáveis.

## <a name="use-injected-dependencies"></a>Use dependências injetadas

A injeção de construtor é usada para disponibilizar as suas dependências numa função. A utilização da injeção de construtor requer que não utilize classes estáticas.

A amostra que se segue demonstra como as dependências `IMyService` e `HttpClient` são injetadas numa função desencadeada pelo HTTP. Este exemplo utiliza o pacote [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) necessário para registar um `HttpClient` no arranque.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();
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
- **Singleton**: O serviço singleton corresponde à vida útil do hospedeiro e é reutilizado através de execuções de funções nesse caso. Os serviços de vida singleton são recomendados para ligações e clientes, por exemplo `SqlConnection` ou `HttpClient` casos.

Ver ou descarregar uma [amostra de diferentes vidas de serviço](https://aka.ms/functions/di-sample) no GitHub.

## <a name="logging-services"></a>Serviços de exploração madeireira

Se precisar do seu próprio fornecedor de registo, registe um tipo personalizado como `ILoggerProvider` instância. Os Insights de Aplicação são adicionados automaticamente pelas Funções Azure.

> [!WARNING]
> - Não adicione `AddApplicationInsightsTelemetry()` à recolha de serviços, uma vez que regista serviços que entram em conflito com os serviços prestados pelo ambiente.
> - Não registe o seu próprio `TelemetryConfiguration` ou `TelemetryClient` se estiver a utilizar a funcionalidade Inejáding Application Insights. Se precisar configurar o seu próprio `TelemetryClient` instância, crie um através do `TelemetryConfiguration` injetado, como mostrado nas [Funções Monitor Azure](./functions-monitoring.md#version-2x-and-later-2).

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

Os valores definidos nas [definições](./functions-how-to-use-azure-function-app-settings.md#settings) de apps estão disponíveis numa `IConfiguration` caso, o que lhe permite ler os valores de definições de aplicações na classe de startups.

Pode extrair valores do `IConfiguration` instância para um tipo personalizado. Copiar os valores de definições da aplicação para um tipo personalizado facilita o teste dos seus serviços tornando estes valores injetáveis. As definições lidas na configuração devem ser simples pares de chaves/valor.

Considere a seguinte classe que inclui uma propriedade chamada consistente com uma definição de app:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

E um ficheiro `local.settings.json` que pode estruturar a definição personalizada da seguinte forma:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

A partir do método `Startup.Configure`, pode extrair valores da `IConfiguration` instância para o seu tipo personalizado utilizando o seguinte código:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

Chamar `Bind` valores de cópias que têm nomes de propriedade correspondentes da configuração para a instância personalizada. A instância de opções está agora disponível no recipiente IoC para injetar numa função.

O objeto de opções é injetado na função como um exemplo da interface genérica `IOptions`. Utilize a propriedade `Value` para aceder aos valores encontrados na sua configuração.

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
