---
title: Tutorial para usar bandeiras de recurso numa aplicação .NET Core [ Microsoft Docs
description: Neste tutorial, aprende-se a implementar bandeiras de recurso em aplicações .NET Core.
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
ms.openlocfilehash: 3182961f928a9befc5a55fb6d58e22c74ba81089
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79473443"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Tutorial: Use bandeiras de recurso numa aplicação ASP.NET Core

As bibliotecas .NET Core Feature Management fornecem suporte idiomático para a implementação de bandeiras de recurso numa aplicação .NET ou ASP.NET Core. Estas bibliotecas permitem-lhe adicionar declarativamente bandeiras de recurso ao seu `if` código para que não tenha de escrever todas as declarações para elas manualmente.

As bibliotecas de Gestão de Recursos também gerem ciclos de vida de bandeira nos bastidores. Por exemplo, as bibliotecas refrescam e cache bandeira, ou garantem que um estado de bandeira seja imutável durante uma chamada de pedido. Além disso, a biblioteca ASP.NET Core oferece integrações fora da caixa, incluindo ações de controlador MVC, vistas, rotas e middleware.

As bandeiras de [recurso Add para uma aplicação ASP.NET Core O Quickstart](./quickstart-feature-flag-aspnet-core.md) mostra várias formas de adicionar bandeiras de recurso numa aplicação ASP.NET Core. Este tutorial explica estes métodos com mais detalhes. Para obter uma referência completa, consulte a documentação de gestão de [recursos ASP.NET Core.](https://go.microsoft.com/fwlink/?linkid=2091410)

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Adicione bandeiras de características em partes chave da sua aplicação para controlar a disponibilidade da funcionalidade.
> * Integre com a Configuração de Aplicações quando estiver a usá-la para gerir as bandeiras de recurso.

## <a name="set-up-feature-management"></a>Configurar a gestão de recursos

Adicione uma referência `Microsoft.FeatureManagement` ao pacote NuGet para utilizar o gestor de funcionalidades .NET Core.
    
O gestor `IFeatureManager` de funcionalidades .NET Core recebe bandeiras do sistema de configuração nativa do quadro. Como resultado, pode definir as bandeiras de funcionalidade da sua aplicação utilizando qualquer fonte de configuração que o Núcleo .NET suporte, incluindo as *aplicações locais.json* ficheiro ou variáveis ambientais. `IFeatureManager`depende da injeção de dependência do núcleo .NET. Pode registar os serviços de gestão de funcionalidades utilizando convenções padrão:

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

Por predefinição, o gestor de `"FeatureManagement"` funcionalidades recupera as bandeiras da secção dos dados de configuração .NET Core. O exemplo que se segue diz ao `"MyFeatureFlags"` gestor de funcionalidades para ler a partir de uma secção diferente chamada em vez disso:

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

Se utilizar filtros nas suas bandeiras de características, terá de incluir uma biblioteca adicional e registá-la. O exemplo que se segue mostra como `PercentageFilter`utilizar um filtro de características incorporado chamado:

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

Recomendamos que mantenha as bandeiras fora da aplicação e as gere separadamente. Ao fazê-lo, permite-lhe modificar os estados de bandeira a qualquer momento e fazer com que essas alterações entrem imediatamente em vigor na aplicação. A Configuração da Aplicação fornece um local centralizado para organizar e controlar todas as suas bandeiras através de um portal dedicado UI. A Configuração da Aplicação também entrega as bandeiras à sua aplicação diretamente através das suas bibliotecas de clientes .NET Core.

A forma mais fácil de ligar a sua aplicação ASP.NET Core à Configuração de Aplicações é através do fornecedor `Microsoft.Azure.AppConfiguration.AspNetCore`de configuração. Siga estes passos para utilizar este pacote NuGet.

1. Abra *Program.cs* ficheiro e adicione o seguinte código.

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

2. Abra *Startup.cs* e `Configure` atualize o método para adicionar um middleware para permitir que os valores da bandeira da funcionalidade sejam atualizados num intervalo recorrente enquanto a aplicação web ASP.NET Core continua a receber pedidos.

   ```csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       app.UseAzureAppConfiguration();
       app.UseMvc();
   }
   ```

Espera-se que os valores da bandeira de características mudem ao longo do tempo. Por predefinição, os valores da bandeira da funcionalidade são cacheed por um período de 30 segundos, pelo que uma operação de atualização desencadeada quando o middleware recebe um pedido não atualizaria o valor até que o valor em cache expirasse. O código seguinte mostra como alterar o tempo de validade `options.UseFeatureFlags()` da cache ou intervalo de votação para 5 minutos na chamada.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.CacheExpirationTime = TimeSpan.FromMinutes(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Declaração de bandeira de recurso

Cada bandeira de recurso tem duas partes: um nome e uma lista de um ou *on* mais filtros que são `True`usados para avaliar se o estado de uma característica está ligado (isto é, quando o seu valor está). Um filtro define um caso de utilização para quando uma função deve ser ligada.

Quando uma bandeira de características tem vários filtros, a lista de filtros é atravessada para poder até que um dos filtros determine que a funcionalidade deve ser ativada. Nessa altura, a bandeira da característica está *asereda,* e os resultados restantes do filtro são ignorados. Se nenhum filtro indicar que a função deve ser ativada, a bandeira de características está *desligada*.

O gestor de funcionalidades suporta *as definições de apps.json* como fonte de configuração para as bandeiras de recurso. O exemplo que se segue mostra como configurar bandeiras de recurso num ficheiro JSON:

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

Por convenção, a `FeatureManagement` secção deste documento JSON é utilizada para configurações de bandeiras de características. O exemplo anterior mostra três bandeiras de `EnabledFor` características com os seus filtros definidos na propriedade:

* `FeatureA`está *ligado.*
* `FeatureB`está *desligado.*
* `FeatureC`especifica um filtro `Percentage` nomeado `Parameters` com uma propriedade. `Percentage`é um filtro configurável. Neste exemplo, `Percentage` especifica uma probabilidade de 50% para a `FeatureC` bandeira estar em *.*

## <a name="feature-flag-references"></a>Referências de bandeira de recurso

Para que possa facilmente referir as bandeiras de `enum` recurso em código, deve defini-las como variáveis:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Verificações de bandeiras de características

O padrão básico de gestão de recursos é verificar primeiro se uma bandeira de característicaestá definida *.* Em caso afirmativo, o gestor de funcionalidades executa as ações que a funcionalidade contém. Por exemplo:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Injeção de dependência

Em ASP.NET Core MVC, pode `IFeatureManager` aceder ao gestor de funcionalidades através de injeção de dependência:

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

Nos controladores MVC, `FeatureGate` utiliza-se o atributo para controlar se uma classe controladora inteira ou uma ação específica está ativada. O `HomeController` controlador seguinte `FeatureA` requer estar *ligado* antes que qualquer ação que a classe controladorcon possa ser executada:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

As `Index` seguintes `FeatureA` ações têm de ser *tomadas* antes de poder ser executada:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Quando um controlador ou ação mvc é bloqueado porque a `IDisabledFeaturesHandler` bandeira da característica de controlo está *desligada,* uma interface registada é chamada. A `IDisabledFeaturesHandler` interface predefinida devolve um código de estado 404 ao cliente sem corpo de resposta.

## <a name="mvc-views"></a>Vistas do MVC

Nas vistas do MVC, `<feature>` pode utilizar uma etiqueta para renderizar conteúdo com base na ativação de uma bandeira de características:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Para apresentar conteúdo alternativo quando os `negate` requisitos não são cumpridos, o atributo pode ser utilizado.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

A `<feature>` etiqueta de recurso também pode ser usada para mostrar conteúdo se alguma ou qualquer funcionalidade de uma lista estiver ativada.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>Filtros MVC

Pode configurar filtros MVC para que sejam ativados com base no estado de uma bandeira de recurso. O seguinte código adiciona um `SomeMvcFilter`filtro MVC chamado . Este filtro só é acionado dentro `FeatureA` do gasoduto MVC se estiver ativado. Esta capacidade limita-se a `IAsyncActionFilter`. 

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

Também pode utilizar bandeiras de recurso para adicionar condicionalmente ramos de aplicação e middleware. O seguinte código insere um componente de `FeatureA` middleware no gasoduto de pedido apenas quando estiver ativado:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Este código baseia-se na capacidade mais genérica de ramificar toda a aplicação com base numa bandeira de recurso:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a implementar bandeiras de recurso `Microsoft.FeatureManagement` na sua aplicação ASP.NET Core utilizando as bibliotecas. Para obter mais informações sobre o suporte à gestão de funcionalidades na configuração ASP.NET Core e App, consulte os seguintes recursos:

* [código de amostra de amostra de característica sinuosa ASP.NET Núcleo](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Microsoft.FuncionalidadeSGe documentação](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Gerir sinalizadores de funcionalidades](./manage-feature-flags.md)
