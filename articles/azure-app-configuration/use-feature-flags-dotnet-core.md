---
title: Tutorial para usar sinalizadores de recurso em um aplicativo .NET Core | Microsoft Docs
description: Neste tutorial, você aprenderá a implementar sinalizadores de recurso em aplicativos .NET Core.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: b04fe3b6451fd7250bc3b05970d49fdb8e7003bd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899305"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Tutorial: usar sinalizadores de recurso em um aplicativo ASP.NET Core

As bibliotecas de gerenciamento de recursos do .NET core fornecem suporte idiomática para a implementação de sinalizadores de recursos em um aplicativo .NET ou ASP.NET Core. Essas bibliotecas permitem que você adicione sinalizadores de recurso declarativamente ao seu código para que você não precise escrever todas as instruções de `if` para elas manualmente.

As bibliotecas de gerenciamento de recursos também gerenciam os ciclos de vida do sinalizador de recursos nos bastidores. Por exemplo, as bibliotecas atualizam e armazenam em cache os Estados de sinalizador ou garantem que um estado de sinalizador seja imutável durante uma chamada de solicitação. Além disso, a biblioteca de ASP.NET Core oferece integrações prontas para uso, incluindo ações do controlador MVC, exibições, rotas e middleware.

O guia de [início rápido adicionar sinalizadores de recurso a um aplicativo ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) mostra várias maneiras de adicionar sinalizadores de recurso em um aplicativo ASP.NET Core. Este tutorial explica esses métodos mais detalhadamente. Para obter uma referência completa, consulte a [documentação de gerenciamento de recursos do ASP.NET Core](https://go.microsoft.com/fwlink/?linkid=2091410).

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Adicione sinalizadores de recurso em partes principais de seu aplicativo para controlar a disponibilidade do recurso.
> * Integre com a configuração de aplicativo quando estiver usando-a para gerenciar sinalizadores de recurso.

## <a name="set-up-feature-management"></a>Configurar o gerenciamento de recursos

O Gerenciador de recursos do .NET Core `IFeatureManager` Obtém os sinalizadores de recurso do sistema de configuração nativa da estrutura. Como resultado, você pode definir os sinalizadores de recurso do aplicativo usando qualquer fonte de configuração com suporte no .NET Core, incluindo as variáveis de ambiente ou arquivo *appSettings. JSON* local. `IFeatureManager` se baseia na injeção de dependência do .NET Core. Você pode registrar os serviços de gerenciamento de recursos usando convenções padrão:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
}
```

Por padrão, o Gerenciador de recursos recupera sinalizadores de recursos da seção `"FeatureManagement"` dos dados de configuração do .NET Core. O exemplo a seguir informa o Gerenciador de recursos para ler de uma seção diferente chamada `"MyFeatureFlags"` em vez disso:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement(options =>
        {
                options.UseConfiguration(Configuration.GetSection("MyFeatureFlags"));
        });
    }
}
```

Se você usar filtros em seus sinalizadores de recurso, você precisará incluir uma biblioteca adicional e registrá-la. O exemplo a seguir mostra como usar um filtro de recurso interno chamado `PercentageFilter`:

```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

Recomendamos que você mantenha os sinalizadores de recurso fora do aplicativo e gerencie-os separadamente. Isso permite que você modifique os Estados de sinalizador a qualquer momento e faça com que essas alterações entrem em vigor no aplicativo imediatamente. A configuração de aplicativo fornece um local centralizado para organizar e controlar todos os sinalizadores de recursos por meio de uma interface do usuário do portal dedicada. A configuração do aplicativo também fornece os sinalizadores para seu aplicativo diretamente por meio de suas bibliotecas de cliente do .NET Core.

A maneira mais fácil de conectar seu aplicativo ASP.NET Core à configuração de aplicativo é por meio do provedor de configuração `Microsoft.Azure.AppConfiguration.AspNetCore`. Siga estas etapas para usar este pacote NuGet.

1. Abra o arquivo *Program.cs* e adicione o código a seguir.

   ```csharp
   using Microsoft.Extensions.Configuration.AzureAppConfiguration;

   public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
       WebHost.CreateDefaultBuilder(args)
              .ConfigureAppConfiguration((hostingContext, config) => {
                  var settings = config.Build();
                  config.AddAzureAppConfiguration(options => {
                      options.Connect(settings["ConnectionStrings:AppConfig"])
                             .UseFeatureFlags();
                   });
              })
              .UseStartup<Startup>();
   ```

2. Abra *Startup.cs* e atualize o método `Configure` para adicionar um middleware para permitir que os valores de sinalizador de recurso sejam atualizados em um intervalo recorrente enquanto o aplicativo Web ASP.NET Core continua a receber solicitações.

   ```csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       app.UseAzureAppConfiguration();
       app.UseMvc();
   }
   ```

Espera-se que os valores de sinalizador de recurso sejam alterados ao longo do tempo. Por padrão, os valores de sinalizador de recurso são armazenados em cache por um período de 30 segundos, portanto, uma operação de atualização disparada quando o middleware recebe uma solicitação não atualizaria o valor até que o valor em cache expire. O código a seguir mostra como alterar o tempo de expiração do cache ou o intervalo de sondagem para 5 minutos na chamada `options.UseFeatureFlags()`.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.CacheExpirationTime = TimeSpan.FromMinutes(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Declaração de sinalizador de recurso

Cada sinalizador de recurso tem duas partes: um nome e uma lista de um ou mais filtros que são usados para avaliar se o estado de um recurso está *ativado* (ou seja, quando seu valor é `True`). Um filtro define um caso de uso para quando um recurso deve ser ativado.

Quando um sinalizador de recurso tem vários filtros, a lista de filtros é percorrida na ordem até que um dos filtros determine que o recurso deve ser habilitado. Nesse ponto, o sinalizador do recurso está *ativado*e os resultados restantes do filtro são ignorados. Se nenhum filtro indicar que o recurso deve ser habilitado, o sinalizador de recurso será *desativado*.

O Gerenciador de recursos dá suporte a *appSettings. JSON* como uma fonte de configuração para sinalizadores de recurso. O exemplo a seguir mostra como configurar sinalizadores de recurso em um arquivo JSON:

```JSON
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

Por convenção, a seção `FeatureManagement` deste documento JSON é usada para configurações de sinalizador de recurso. O exemplo anterior mostra três sinalizadores de recurso com seus filtros definidos na propriedade `EnabledFor`:

* o `FeatureA` está *ativado*.
* `FeatureB` está *desativado*.
* `FeatureC` especifica um filtro chamado `Percentage` com uma propriedade `Parameters`. `Percentage` é um filtro configurável. Neste exemplo, `Percentage` especifica uma probabilidade de 50% para que o sinalizador de `FeatureC` seja *ativado*.

## <a name="feature-flag-references"></a>Referências de sinalizador de recurso

Para que você possa referenciar facilmente os sinalizadores de recurso no código, você deve defini-los como variáveis `enum`:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Verificações de sinalizador de recurso

O padrão básico do gerenciamento de recursos é verificar primeiro se um sinalizador de recurso está definido como *on*. Nesse caso, o Gerenciador de recursos executa as ações que o recurso contém. Por exemplo:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Injeção de dependência

No ASP.NET Core MVC, você pode acessar o `IFeatureManager` do Gerenciador de recursos por meio da injeção de dependência:

```csharp
public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Ações do controlador

Em controladores MVC, você usa o atributo `FeatureGate` para controlar se uma classe de controlador inteira ou uma ação específica está habilitada. O controlador de `HomeController` a seguir requer que `FeatureA` seja *ativado* antes que qualquer ação que a classe de controlador contenha possa ser executada:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

A ação de `Index` a seguir requer que `FeatureA` esteja *ativada* para que possa ser executada:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Quando um controlador MVC ou uma ação é bloqueada porque o sinalizador de recurso de controle está *desativado*, uma interface `IDisabledFeaturesHandler` registrada é chamada. A interface de `IDisabledFeaturesHandler` padrão retorna um código de status 404 para o cliente sem corpo de resposta.

## <a name="mvc-views"></a>Exibições do MVC

Nas exibições do MVC, você pode usar uma marca de `<feature>` para renderizar o conteúdo com base no fato de um sinalizador de recurso estar habilitado:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Para exibir o conteúdo alternativo quando os requisitos não forem atendidos, o atributo `negate` poderá ser usado.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

O recurso `<feature>` marca também pode ser usado para mostrar o conteúdo se algum ou todos os recursos em uma lista estiverem habilitados.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>Filtros MVC

Você pode configurar filtros MVC para que eles sejam ativados com base no estado de um sinalizador de recurso. O código a seguir adiciona um filtro MVC chamado `SomeMvcFilter`. Esse filtro será disparado no pipeline MVC somente se `FeatureA` estiver habilitado. Essa funcionalidade é limitada a `IAsyncActionFilter`. 

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<SomeMvcFilter>(nameof(MyFeatureFlags.FeatureA));
    });
}
```

## <a name="middleware"></a>Middleware

Você também pode usar os sinalizadores de recurso para adicionar condicionalmente branches de aplicativo e middleware. O código a seguir insere um componente de middleware no pipeline de solicitação somente quando `FeatureA` está habilitado:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Esse código compila o recurso mais genérico para ramificar todo o aplicativo com base em um sinalizador de recurso:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a implementar sinalizadores de recurso em seu aplicativo ASP.NET Core usando as bibliotecas de `Microsoft.FeatureManagement`. Para obter mais informações sobre o suporte ao gerenciamento de recursos no ASP.NET Core e na configuração do aplicativo, consulte os seguintes recursos:

* [Código de exemplo do sinalizador de recurso ASP.NET Core](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Documentação do Microsoft. FeatureManagement](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Gerir sinalizadores de funcionalidades](./manage-feature-flags.md)
