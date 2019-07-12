---
title: Tutorial para utilizar os sinalizadores de recurso num aplicativo do .NET Core | Documentos da Microsoft
description: Neste tutorial, saiba como implementar os sinalizadores de recurso nas aplicações de .NET Core.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 99559c0c77c3e4b29badec1c0be2d741df1f0621
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798385"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Tutorial: Utilizar os sinalizadores de recurso numa aplicação ASP.NET Core

As bibliotecas de gestão de recursos do .NET Core fornecem suporte idiomático para a implementação de sinalizadores de recurso num aplicativo .NET ou ASP.NET Core. Essas bibliotecas permitem-lhe adicionar declarativamente os sinalizadores de recurso para o seu código para que não precisa escrever todos os `if` instruções para os mesmos manualmente.

As bibliotecas de gestão de recursos também gerenciam os ciclos de vida de sinalizador de funcionalidade em segundo plano. Por exemplo, as bibliotecas de atualizam e armazenar em cache os Estados de sinalizador ou garantem um Estado de sinalizador sejam imutáveis durante uma chamada de pedido. Além disso, a biblioteca do ASP.NET Core oferece integrações de out-of-the-box, incluindo ações do controlador MVC, vistas, rotas e middleware.

O [adicionar sinalizadores de recurso para uma aplicação ASP.NET Core início rápido](./quickstart-feature-flag-aspnet-core.md) mostra várias formas de adicionar sinalizadores de recurso num aplicativo do ASP.NET Core. Este tutorial explica esses métodos mais detalhadamente. Para obter uma referência completa, consulte a [documentação de gestão de recursos do ASP.NET Core](https://go.microsoft.com/fwlink/?linkid=2091410).

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Adicione os sinalizadores de recurso em partes-chave da sua aplicação para controlar a disponibilidade de funcionalidades.
> * Integre com a configuração de aplicações quando o estiver a utilizar para gerir sinalizadores de recurso.

## <a name="set-up-feature-management"></a>Configurar a gestão de recursos

O Gestor de recursos do .NET Core `IFeatureManager` obtém os sinalizadores de recurso do sistema de configuração nativo da estrutura. Como resultado, pode definir sinalizadores de recurso da sua aplicação ao utilizar qualquer origem de configuração que suporte o .NET Core, incluindo o local *appSettings* variáveis de ambiente ou ficheiro. `IFeatureManager` depende de injeção de dependência do .NET Core. Pode registrar os serviços de gestão de recursos, utilizando as convenções padrão:

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

Por predefinição, o Gestor de recursos obtém os sinalizadores de recurso do `"FeatureManagement"` seção dos dados de configuração .NET Core. O exemplo a seguir informa o Gestor de recursos para ler a partir de uma secção diferente chamada `"MyFeatureFlags"` em vez disso:

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

Se utilizar filtros em sua sinalizadores de recurso, terá de incluir uma biblioteca adicional e registá-lo. O exemplo seguinte mostra como utilizar um filtro de recurso interno chamado `PercentageFilter`:

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

Recomendamos que mantenha os sinalizadores de recurso fora da aplicação e geri-los separadamente. Se o fizer, permite-lhe alterar o sinalizador Estados em qualquer altura e ter essas alterações entrem em vigor no aplicativo imediatamente. Configuração de aplicações fornece um local centralizado para organizar e controlar todos os seus sinalizadores de recurso através de um portal dedicado da interface do Usuário. Configuração de aplicações também fornece os sinalizadores para a sua aplicação diretamente através do seu cliente de .NET Core bibliotecas.

A maneira mais fácil para ligar a sua aplicação ASP.NET Core para configuração de aplicações é através do fornecedor de configuração `Microsoft.Azure.AppConfiguration.AspNetCore`. Siga estes passos para utilizar este pacote do NuGet.

1. Open *Program.cs* de ficheiros e adicione o seguinte código.

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

2. Open *Startup.cs* e atualizar o `Configure` método para adicionar um middleware para permitir que os valores de sinalizador de funcionalidade sejam atualizados em intervalos periódicos enquanto o ASP.NET Core web aplicação continua a receber pedidos.

   ```csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       app.UseAzureAppConfiguration();
       app.UseMvc();
   }
   ```

Valores de sinalizador de funcionalidade devem mudar ao longo do tempo. Por predefinição, os valores de sinalizador de funcionalidade são colocadas em cache durante um período de 30 segundos, para que uma operação de atualização acionada quando o middleware recebe um pedido não atualizará o valor até que o valor em cache expire. O código seguinte mostra como alterar a hora de expiração da cache ou o intervalo de consulta para 5 minutos no `options.UseFeatureFlags()` chamar.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.CacheExpirationTime = TimeSpan.FromMinutes(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Declaração de sinalizador de funcionalidade

Sinalizador de cada funcionalidade tem duas partes: um nome e uma lista de um ou mais filtros que são utilizados para avaliar se o estado de uma funcionalidade está *nos* (ou seja, quando seu valor é `True`). Um filtro define um caso de utilização para quando um recurso deve estar ativado.

Quando um sinalizador de funcionalidade tem vários filtros, a lista de filtro é percorrida por ordem até que um dos filtros determina que a funcionalidade deve ser ativada. Nesse ponto, o sinalizador de funcionalidade está *no*, e qualquer restantes filtrar os resultados são ignorados. Se nenhum filtro indica que o recurso deve estar ativado, o sinalizador de funcionalidade está *desativar*.

Manager suporta a funcionalidade *appSettings* como uma origem de configuração para os sinalizadores de recurso. O exemplo seguinte mostra como configurar a sinalizadores de recurso num ficheiro JSON:

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

Por convenção, o `FeatureManagement` seção deste documento JSON é usada para as definições do sinalizador de funcionalidade. O exemplo anterior mostra três sinalizadores de recurso com os filtros definidos no `EnabledFor` propriedade:

* `FeatureA` é *no*.
* `FeatureB` é *desativar*.
* `FeatureC` Especifica um filtro com o nome `Percentage` com um `Parameters` propriedade. `Percentage` é um filtro configurável. Neste exemplo, `Percentage` Especifica uma probabilidade de 50 por cento para o `FeatureC` sinalizador para ser *no*.

## <a name="feature-flag-references"></a>Referências do sinalizador de funcionalidade

Para que pode facilmente fazer referência a sinalizadores de recurso no código, deve defini-las assim `enum` variáveis:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Verificações de sinalizador de funcionalidade

O padrão básico de gerenciamento de recursos é primeiro verificar se um sinalizador de funcionalidade é definido como *no*. Se, por isso, o Gestor de recursos, em seguida, executa as ações que contém a funcionalidade. Por exemplo:

```csharp
IFeatureManager featureManager;
...
if (featureManager.IsEnabled(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Injeção de dependência

No MVC do ASP.NET Core, pode acessar o Gestor de recursos `IFeatureManager` por meio de injeção de dependência:

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

Controladores do MVC, vai utilizar o `FeatureGate` de atributo para controlar se uma classe de controlador inteiro ou uma ação específica está ativada. O seguinte procedimento `HomeController` controlador requer `FeatureA` ser *no* antes de qualquer ação que contém a classe de controlador pode ser executada:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

O seguinte procedimento `Index` ação requer `FeatureA` ser *no* antes de ser executado:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Quando um controlador MVC ou ação está bloqueada, porque o sinalizador de funcionalidade de controle está *off*, registrado `IDisabledFeaturesHandler` denomina-se a interface. A predefinição `IDisabledFeaturesHandler` interface devolve um código de 404 estado ao cliente com nenhum corpo de resposta.

## <a name="mvc-views"></a>Exibições do MVC

Em modos de exibição do MVC, pode usar um `<feature>` marca para processar conteúdo com base em se um sinalizador de funcionalidade está ativado:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Para exibir conteúdo alternativo quando não são cumpridos os requisitos do `negate` atributo pode ser utilizado.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

A funcionalidade `<feature>` marca também pode ser utilizada para mostrar o conteúdo se quaisquer ou todas as funcionalidades numa lista estão ativadas.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>Filtros MVC

Pode configurar filtros MVC para que eles estão ativados com base no estado de um sinalizador de funcionalidade. O código a seguir adiciona um filtro MVC com o nome `SomeMvcFilter`. Este filtro é disparado no MVC pipeline apenas se `FeatureA` está ativada.

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

## <a name="routes"></a>Rotas

Pode usar os sinalizadores de recurso para expor dinamicamente as rotas. O código a seguir adiciona uma rota, que define `Beta` como o controlador padrão apenas quando `FeatureA` está ativado:

```csharp
app.UseMvc(routes => {
    routes.MapRouteForFeature(nameof(MyFeatureFlags.FeatureA), "betaDefault", "{controller=Beta}/{action=Index}/{id?}");
});
```

## <a name="middleware"></a>Middleware

Também pode utilizar os sinalizadores de recurso para adicionar condicionalmente ramos de aplicativos e middleware. As inserções de código seguinte, um componente de middleware no pedido de pipeline apenas quando `FeatureA` está ativado:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Esse código cria a desativar a capacidade de mais genérica de ramo de todo o aplicativo com base num sinalizador de funcionalidade:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como implementar os sinalizadores de recurso na sua aplicação ASP.NET Core, utilizando o `Microsoft.FeatureManagement` bibliotecas. Para obter mais informações sobre o suporte de gestão de recursos no ASP.NET Core e configuração de aplicações, consulte os seguintes recursos:

* [Código de exemplo do sinalizador de funcionalidade do ASP.NET Core](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Documentação de Microsoft.FeatureManagement](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Gerir sinalizadores de funcionalidades](./manage-feature-flags.md)
