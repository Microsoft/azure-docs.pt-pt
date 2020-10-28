---
title: Tutorial para usar bandeiras de recurso numa aplicação .NET Core ! Microsoft Docs
description: Neste tutorial, aprende-se a implementar bandeiras de funcionalidades em aplicações .NET Core.
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
ms.date: 09/17/2020
ms.author: lcozzens
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 6da2aa645549920cce2f5c0cfe8a32c98dc04708
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746139"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Tutorial: Use bandeiras de recurso numa aplicação core ASP.NET

As bibliotecas de Gestão de Recursos Centrais .NET fornecem suporte idiomático para a implementação de bandeiras de características numa aplicação .NET ou ASP.NET Core. Estas bibliotecas permitem-lhe adicionar declarativamente bandeiras de características ao seu código para que não tenha de escrever todas as `if` declarações manualmente.

As bibliotecas de Gestão de Recursos também gerem ciclos de vida de bandeira de recurso nos bastidores. Por exemplo, as bibliotecas renovam e cache estados de bandeira, ou garantem que um estado de bandeira seja imutável durante uma chamada de pedido. Além disso, a biblioteca core ASP.NET oferece integrações fora da caixa, incluindo ações de controlador MVC, vistas, rotas e middleware.

O [Add feature bandeiras para uma aplicação core ASP.NET Quickstart](./quickstart-feature-flag-aspnet-core.md) mostra várias formas de adicionar bandeiras de funcionalidades numa aplicação core ASP.NET. Este tutorial explica estes métodos com mais detalhes. Para obter uma referência completa, consulte a [documentação de gestão de recursos do núcleo ASP.NET.](/dotnet/api/microsoft.featuremanagement)

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Adicione bandeiras de funcionalidades em partes chave da sua aplicação para controlar a disponibilidade do recurso.
> * Integre-se com a Configuração da Aplicação quando estiver a usá-lo para gerir as bandeiras de funcionalidades.

## <a name="set-up-feature-management"></a>Configurar gestão de recursos

Adicione uma referência aos `Microsoft.FeatureManagement.AspNetCore` pacotes e `Microsoft.FeatureManagement` NuGet para utilizar o gestor de funcionalidades .NET Core.
    
O gestor de funcionalidades .NET Core `IFeatureManager` obtém bandeiras de funcionalidades do sistema de configuração nativa da estrutura. Como resultado, pode definir as bandeiras de funcionalidade da sua aplicação utilizando qualquer fonte de configuração que .NET Core suporte, incluindo aappsettings.jslocal *em* variáveis de ficheiros ou ambiente. `IFeatureManager` baseia-se na injeção de dependência do núcleo .NET. Pode registar os serviços de gestão de recursos utilizando convenções padrão:

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

Por predefinição, o gestor de recursos recupera bandeiras de características a partir `"FeatureManagement"` da secção dos dados de configuração .NET Core. O exemplo a seguir diz ao gestor de recursos para ler a partir de uma secção diferente `"MyFeatureFlags"` chamada:

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

Se utilizar filtros nas bandeiras de recurso, tem de incluir uma biblioteca adicional e registá-la. O exemplo a seguir mostra como utilizar um filtro de recurso incorporado chamado `PercentageFilter` :

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

Recomendamos que mantenha as bandeiras de características fora da aplicação e as gere separadamente. Ao fazê-lo, permite-lhe modificar estados de bandeira a qualquer momento e fazer com que essas alterações produzam efeitos imediatamente na aplicação. A Configuração de Aplicações oferece um local centralizado para organizar e controlar todas as suas bandeiras de funcionalidades através de um portal dedicado UI. A Configuração da Aplicação também entrega as bandeiras à sua aplicação diretamente através das suas bibliotecas de clientes .NET Core.

A forma mais fácil de ligar a aplicação Core ASP.NET à Configuração de Aplicações é através do fornecedor de `Microsoft.Azure.AppConfiguration.AspNetCore` configuração. Siga estes passos para utilizar este pacote NuGet.

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

2. Abra *Startup.cs* e atualize o `Configure` método para adicionar o middleware incorporado chamado `UseAzureAppConfiguration` . Este middleware permite que os valores da bandeira de funcionalidade sejam atualizados num intervalo recorrente enquanto a aplicação web core ASP.NET continua a receber pedidos.

   ```csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       app.UseAzureAppConfiguration();
       app.UseMvc();
   }
   ```

Espera-se que os valores da bandeira da característica mudem ao longo do tempo. Por predefinição, os valores da bandeira de recurso são em cache por um período de 30 segundos, pelo que uma operação de atualização desencadeada quando o middleware recebe um pedido não atualizaria o valor até que o valor em cache expirasse. O código que se segue mostra como alterar o tempo de validade da cache ou o intervalo de votação para 5 minutos na `options.UseFeatureFlags()` chamada.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.CacheExpirationTime = TimeSpan.FromMinutes(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Declaração de bandeira de característica

Cada bandeira de características tem duas partes: um nome e uma lista de um ou mais filtros que são usados para avaliar se o estado de uma característica está *ligado* (isto é, quando o seu valor `True` é). Um filtro define uma caixa de utilização para quando uma função deve ser ligada.

Quando uma bandeira de recurso tem vários filtros, a lista de filtros é percorrida até que um dos filtros determine que a função deve ser ativada. Nessa altura, a bandeira da funcionalidade está *acesa* , e os restantes resultados do filtro são ignorados. Se nenhum filtro indicar que a função deve ser ativada, a bandeira de características está *desligada* .

O gestor de funcionalidades suporta *appsettings.js* como fonte de configuração para bandeiras de funcionalidades. O exemplo a seguir mostra como configurar bandeiras de recurso num ficheiro JSON:

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

Por convenção, a `FeatureManagement` secção deste documento JSON é utilizada para configurações de bandeira de características. O exemplo anterior mostra três bandeiras de características com os seus filtros definidos na `EnabledFor` propriedade:

* `FeatureA`está *ligado.*
* `FeatureB`está *desligado.*
* `FeatureC` especifica um filtro nomeado `Percentage` com uma `Parameters` propriedade. `Percentage` é um filtro configurável. Neste exemplo, `Percentage` especifica uma probabilidade de 50% para a bandeira estar `FeatureC` *em* .

## <a name="feature-flag-references"></a>Referências de bandeira de característica

Para que possa facilmente referenciar as bandeiras de recurso em código, deve defini-las como `enum` variáveis:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Verificações de bandeira de recurso

O padrão básico de gestão de recursos é verificar primeiro se uma bandeira de recurso está definida para *.* Em caso afirmativo, o gestor de funcionalidades executa então as ações que a funcionalidade contém. Por exemplo:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Injeção de dependência

Em ASP.NET Core MVC, pode aceder ao gestor de recursos `IFeatureManager` através da injeção de dependência:

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

## <a name="controller-actions"></a>Ações de controlador

Nos controladores MVC, utiliza-se o `FeatureGate` atributo para controlar se uma classe de controlador inteiro ou uma ação específica está ativada. O seguinte controlador requer estar ligado antes de `HomeController` qualquer ação que a classe do controlador `FeatureA` contenha possa ser executada: *on*

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

A seguinte `Index` ação deve ser `FeatureA` *iniciada* antes de poder ser executada:

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Quando um controlador ou ação MVC é bloqueado porque a bandeira da função de controlo está *desligada,* uma interface registada `IDisabledFeaturesHandler` é chamada. A `IDisabledFeaturesHandler` interface predefinida devolve um código de estado 404 ao cliente sem organismo de resposta.

## <a name="mvc-views"></a>Vistas para o MVC

Abra *_ViewImports.cshtml* no diretório *views* e adicione o ajudante de etiquetas de recurso:

```html
@addTagHelper *, Microsoft.FeatureManagement.AspNetCore
```

Nas vistas de MVC, pode utilizar uma `<feature>` etiqueta para renderizar conteúdo com base no facto de uma bandeira de recurso estar ativada:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Para apresentar conteúdo alternativo quando os requisitos não estão satisfeitos, o `negate` atributo pode ser utilizado.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

A etiqueta de funcionalidade `<feature>` também pode ser usada para mostrar conteúdo se alguma ou todas as funcionalidades de uma lista estiverem ativadas.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>Filtros MVC

Pode configurar filtros MVC para que sejam ativados com base no estado de uma bandeira de recurso. O seguinte código adiciona um filtro MVC chamado `SomeMvcFilter` . Este filtro só é acionado dentro do gasoduto MVC se `FeatureA` estiver ativado. Esta capacidade está limitada a `IAsyncActionFilter` . 

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

Também pode usar bandeiras de recurso para adicionar balcões de aplicação e middleware de aplicação condicionalmente. O seguinte código insere um componente de middleware no pipeline de pedido apenas quando `FeatureA` estiver ativado:

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

Neste tutorial, aprendeu a implementar bandeiras de funcionalidades na sua aplicação Core ASP.NET utilizando as `Microsoft.FeatureManagement` bibliotecas. Para obter mais informações sobre o suporte à gestão de funcionalidades na configuração ASP.NET Core e App, consulte os seguintes recursos:

* [ASP.NET Código de amostra de bandeira de característica do núcleo](./quickstart-feature-flag-aspnet-core.md)
* [Documentação microsoft.featureManagement](/dotnet/api/microsoft.featuremanagement)
* [Gerir sinalizadores de funcionalidades](./manage-feature-flags.md)