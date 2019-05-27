---
title: Tutorial para utilizar os sinalizadores de recurso num aplicativo do .NET Core | Documentos da Microsoft
description: Neste tutorial, irá aprender a implementar os sinalizadores de recurso nas aplicações de .NET Core
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
ms.openlocfilehash: f712cc34a3d41ea9472bf9428606cb378eef8c18
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244264"
---
# <a name="tutorial-use-feature-flags-in-a-net-core-app"></a>Tutorial: Utilizar sinalizadores de funcionalidades numa aplicação .NET Core

As bibliotecas de gestão de recursos do .NET Core fornecem suporte idiomático para a implementação de sinalizadores de recurso num aplicativo .NET ou ASP.NET Core. Eles permitem-lhe adicionar sinalizadores de recurso ao seu código mais de forma declarativa, para que não precisa escrever todos os `if` instruções para os mesmos manualmente. Gerir a funcionalidade sinalizador ciclos de vida (por exemplo, a atualização e a cache de sinalizador Estados, garantir um Estado de sinalizador sejam imutáveis durante uma chamada de pedido) por trás da cena. Além disso, a biblioteca do ASP.NET Core oferece integrações de out-of-the-box, incluindo ações do controlador MVC, vistas, rotas e middleware.

O [adicionar sinalizadores de recurso para uma aplicação ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) guia de introdução mostra várias formas para adicionar os sinalizadores de recurso num aplicativo do ASP.NET Core. Este tutorial explica isso em mais detalhes. Consulte a [documentação de gestão de recursos do ASP.NET Core](https://go.microsoft.com/fwlink/?linkid=2091410) para uma referência completa.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicione os sinalizadores de recurso em partes-chave da sua aplicação para controlar a disponibilidade de funcionalidades.
> * Integre com a configuração de aplicação quando utilizá-lo para gerir sinalizadores de recurso.

## <a name="setup"></a>Configuração

O Gestor de recursos do .NET Core `IFeatureManager` obtém os sinalizadores de recurso do sistema de configuração nativo da estrutura. Como resultado, pode definir sinalizadores de recurso da sua aplicação utilizam qualquer origem de configuração que suporte o .NET Core, incluindo o local *appSettings* variáveis de ambiente ou ficheiro. Gestor de recursos depende de injeção de dependência do .NET Core. Pode registrar os serviços de gestão de recursos com as convenções padrão.

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

O Gestor de recursos obtém os sinalizadores de recurso da secção "FeatureManagement" os dados de configuração do .NET Core por predefinição. O exemplo a seguir informa-o para ler a partir de uma secção diferente chamada "MyFeatureFlags" em vez disso.

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

Se utilizar o filtro na sua sinalizadores de recurso, terá de incluir uma biblioteca adicional e registá-lo. O exemplo seguinte mostra como utilizar um filtro de recurso interno denominado **PercentageFilter "** .

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

Opere com eficiência, deve manter os sinalizadores de recurso fora do aplicativo e geri-los separadamente. Se o fizer, permite-lhe alterar o sinalizador Estados em qualquer altura e ter essas alterações entrarem em vigor no aplicativo imediatamente. Configuração de aplicações fornece um local centralizado para organizar e controlar todos os seus recursos sinaliza através de um portal dedicado da interface do Usuário e oferece os sinalizadores para a sua aplicação diretamente através do seu cliente de .NET Core de bibliotecas. A maneira mais fácil para ligar a sua aplicação ASP.NET Core para configuração de aplicações é através do fornecedor de configuração `Microsoft.Extensions.Configuration.AzureAppConfiguration`. Pode utilizar este pacote de NuGet no seu código, adicionando o seguinte para o *Program.cs* ficheiro:

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

Valores de sinalizador de funcionalidade devem mudar ao longo do tempo. Por predefinição, o Gestor de recursos irá atualizar os valores de sinalizador de funcionalidade a cada 30 segundos. Pode utilizar um intervalo de consulta diferentes no `options.UseFeatureFlags()` chamada acima.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.PollInterval = TimeSpan.FromSeconds(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Declaração de sinalizador de funcionalidade

Sinalizador de cada funcionalidade tem duas partes: um nome e uma lista de um ou mais filtros que são utilizados para avaliar se o estado de uma funcionalidade está *nos* (ou seja, quando seu valor é `True`). Um filtro define um caso de utilização para o qual um recurso deve estar ativado. Se um sinalizador de funcionalidade tiver vários filtros, a lista de filtro é percorrida por ordem até que um dos filtros determina que o recurso deve estar ativado. Neste momento, o sinalizador de funcionalidade é considerado *no* e qualquer restantes filtrar os resultados são ignorados. Se nenhum filtro indica que o recurso deve estar ativado, o sinalizador de funcionalidade está *desativar*.

Manager suporta a funcionalidade *appSettings* como uma origem de configuração para os sinalizadores de recurso. O exemplo seguinte mostra como configurar a sinalizadores de recurso num ficheiro json.

```JSON
"FeatureManagement": {
    "FeatureX": true, // Feature flag set to on
    "FeatureY": false, // Feature flag set to off
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

Por convenção, o `FeatureManagement` seção deste documento json é usada para as definições do sinalizador de funcionalidade. O exemplo acima mostra três sinalizadores de recurso com os filtros definidos na *EnabledFor* propriedade:

* **FeatureA** é *no*.
* **FeatureB** é *desativar*.
* **FeatureC** Especifica um filtro com o nome *percentagem* com um *parâmetros* propriedade. *Percentagem* é um exemplo de um filtro configurável e especifica uma probabilidade de 50% para o **FeatureC** sinalizador para ser *no*.

## <a name="referencing"></a>Referência

Embora não obrigatório, sinalizadores de recurso devem ser definidos como `enum` variáveis para que possa ser referenciados facilmente no código.

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-check"></a>Verificação do sinalizador de funcionalidade

O padrão básico de gerenciamento de recursos é primeiro verificar se um sinalizador de funcionalidade é definido como *no* e, em seguida, executar as ações de circunscritas, se for esse o caso.

```csharp
IFeatureManager featureManager;
...
if (featureManager.IsEnabled(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Injeção de dependência

No ASP.NET Core MVC, o Gestor de recursos `IFeatureManager` podem ser acedidos através de injeção de dependência.

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

## <a name="controller-action"></a>Ação de controlador

Controladores do MVC, de um `Feature` atributo pode ser utilizado para controlar se uma classe de controlador inteiro ou uma ação específica está ativada. O seguinte procedimento `HomeController` controlador requer *FeatureA* ser *no* antes de qualquer ação contém pode ser executada.

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

O seguinte procedimento `Index` ação acima requer *FeatureA* ser *no* antes de ser executado.

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Quando um controlador MVC ou ação está bloqueada, porque o sinalizador de funcionalidade de controle está *off*, registrado `IDisabledFeatureHandler` é chamado. A predefinição `IDisabledFeatureHandler` devolve um código de 404 estado ao cliente com nenhum corpo de resposta.

## <a name="view"></a>Visualizar

Em modos de exibição do MVC, um `<feature>` etiqueta pode ser usada para processar conteúdo com base em se um sinalizador de funcionalidade está ativado ou não.

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

## <a name="mvc-filter"></a>Filtro do MVC

Filtros MVC podem ser configurados de forma que eles são ativados com base no estado de um sinalizador de funcionalidade. O seguinte adiciona um filtro MVC com o nome `SomeMvcFilter`. Este filtro é disparado no MVC pipeline apenas se for *FeatureA* está ativada.

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

## <a name="route"></a>Encaminhar

Rotas podem ser expostas dinamicamente com base no sinalizadores de recurso. O seguinte adiciona uma rota, que define `Beta` como o controlador padrão, apenas quando *FeatureA* está ativada.

```csharp
app.UseMvc(routes => {
    routes.MapRouteForFeature(nameof(MyFeatureFlags.FeatureA), "betaDefault", "{controller=Beta}/{action=Index}/{id?}");
});
```

## <a name="middleware"></a>Middleware

Sinalizadores de recurso podem ser utilizados para adicionar os ramos de aplicativos e middleware condicionalmente. As inserções seguintes, um componente de middleware no pedido de pipeline apenas quando *FeatureA* está ativada.

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Isto baseia-se desativar a capacidade mais genérica de todo o aplicativo com base num sinalizador de funcionalidade para filiais.

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como implementar sinalizadores de recurso na sua aplicação ASP.NET Core, utilizando o `Microsoft.FeatureManagement` bibliotecas. Consulte os seguintes recursos para obter mais informações sobre o suporte de gestão de recursos em ASP.NET Core e configuração de aplicações.

* [Código de exemplo do sinalizador de funcionalidade do ASP.NET Core](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Documentação de Microsoft.FeatureManagement](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Gerir sinalizadores de funcionalidades](./manage-feature-flags.md)
