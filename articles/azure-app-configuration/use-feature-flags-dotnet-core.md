---
title: Tutorial para usar bandeiras de recurso numa aplicação .NET Core | Microsoft Docs
description: Neste tutorial, aprende-se a implementar bandeiras de funcionalidades em aplicações .NET Core.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/17/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 4d54e1ff07b250b5595d2f8aee5f022bd2359721
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729512"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Tutorial: Use bandeiras de recurso numa aplicação core ASP.NET

As bibliotecas de Gestão de Recursos Centrais .NET fornecem suporte idiomático para a implementação de bandeiras de características numa aplicação .NET ou ASP.NET Core. Estas bibliotecas permitem-lhe adicionar declarativamente bandeiras de funcionalidades ao seu código para que não tenha de escrever manualmente código para ativar ou desativar funcionalidades com `if` declarações.

As bibliotecas de Gestão de Recursos também gerem ciclos de vida de bandeira de recurso nos bastidores. Por exemplo, as bibliotecas renovam e cache estados de bandeira, ou garantem que um estado de bandeira seja imutável durante uma chamada de pedido. Além disso, a biblioteca core ASP.NET oferece integrações fora da caixa, incluindo ações de controlador MVC, vistas, rotas e middleware.

A [funcionalidade Add bandeiras para uma aplicação core ASP.NET Quickstart](./quickstart-feature-flag-aspnet-core.md) mostra um exemplo simples de como usar bandeiras de funcionalidades numa aplicação core ASP.NET. Este tutorial mostra opções de configuração adicionais e capacidades das bibliotecas de Gestão de Recursos. Pode utilizar a aplicação de amostra criada no quickstart para experimentar o código de amostra mostrado neste tutorial. 

Para a documentação de referência da API de gestão de recursos do núcleo ASP.NET, consulte [o Microsoft.FeatureManagement Namespace](/dotnet/api/microsoft.featuremanagement).

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Adicione bandeiras de funcionalidades em partes chave da sua aplicação para controlar a disponibilidade do recurso.
> * Integre-se com a Configuração da Aplicação quando estiver a usá-lo para gerir as bandeiras de funcionalidades.

## <a name="set-up-feature-management"></a>Configurar gestão de recursos

Para aceder ao gestor de funcionalidades .NET Core, a sua aplicação deve ter referências ao `Microsoft.FeatureManagement.AspNetCore` pacote NuGet.

O gestor de funcionalidades .NET Core está configurado a partir do sistema de configuração nativa da estrutura. Como resultado, pode definir as definições de bandeira de funcionalidade da sua aplicação utilizando qualquer fonte de configuração que .NET Core suporte, incluindo aappsettings.jslocal *em* variáveis de ficheiros ou ambiente.

Por predefinição, o gestor de funcionalidades recupera a configuração da bandeira da `"FeatureManagement"` secção dos dados de configuração do Núcleo .NET. Para utilizar a localização de configuração predefinida, ligue para o método [AddFeatureManagement](/dotnet/api/microsoft.featuremanagement.servicecollectionextensions.addfeaturemanagement) do **IServiceCollection** passado para o método **ConfigureServices** da classe **Startup.**


```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement();
    }
}
```

Pode especificar que a configuração de gestão de funcionalidades deve ser recuperada a partir de uma secção de configuração diferente, chamando [Configuração.GetSection](/dotnet/api/microsoft.web.administration.configuration.getsection) e passando no nome da secção desejada. O exemplo a seguir diz ao gestor de recursos para ler a partir de uma secção diferente `"MyFeatureFlags"` chamada:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement(Configuration.GetSection("MyFeatureFlags"));
    }
}
```


Se utilizar filtros nas suas bandeiras de funcionalidade, deve incluir o espaço de [nomes Microsoft.FeatureManagement.FeatureFilters](/dotnet/api/microsoft.featuremanagement.featurefilters) e adicionar uma chamada aos [AddFeatureFilters](/dotnet/api/microsoft.featuremanagement.ifeaturemanagementbuilder.addfeaturefilter) especificando o nome do tipo do filtro que pretende utilizar como tipo genérico do método. Para obter mais informações sobre a utilização de filtros de funcionalidades para ativar e desativar dinamicamente a funcionalidade, consulte [Ativar o lançamento encenado de funcionalidades para audiências direcionadas](./howto-targetingfilter-aspnet-core.md).

O exemplo a seguir mostra como utilizar um filtro de recurso incorporado chamado `PercentageFilter` :



```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

Em vez de codificar duramente as suas bandeiras de recurso na sua aplicação, recomendamos que mantenha as bandeiras fora da aplicação e as gere separadamente. Ao fazê-lo, permite-lhe modificar estados de bandeira a qualquer momento e fazer com que essas alterações produzam efeitos imediatamente na aplicação. O serviço Azure App Configuration fornece um portal dedicado UI para gerir todas as suas bandeiras de funcionalidades. O serviço de Configuração de Aplicações Azure também fornece as bandeiras de funcionalidades à sua aplicação diretamente através das suas bibliotecas de clientes .NET Core.

A forma mais fácil de ligar a sua aplicação Core ASP.NET à Configuração de Aplicações é através do fornecedor de configuração incluído no `Microsoft.Azure.AppConfiguration.AspNetCore` pacote NuGet. Depois de incluir uma referência ao pacote, siga estes passos para utilizar este pacote NuGet.

1. Abrir o ficheiro *.cs Programa* e adicionar o seguinte código.
    > [!IMPORTANT]
    > `CreateHostBuilder` substitui `CreateWebHostBuilder` em .NET Core 3.x. Selecione a sintaxe correta com base no seu ambiente.

    ### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
            }).UseStartup<Startup>());
    ```
        
    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration(config =>
                    {
                        var settings = config.Build();
                        config.AddAzureAppConfiguration(options =>
                            options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                    }).UseStartup<Startup>();
    ```
    ---

2. *Open Startup.cs* e atualizar o `Configure` e método para adicionar o `ConfigureServices` middleware incorporado chamado `UseAzureAppConfiguration` . Este middleware permite que os valores da bandeira de funcionalidade sejam atualizados num intervalo recorrente enquanto a aplicação web core ASP.NET continua a receber pedidos.



    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseAzureAppConfiguration();
    }
    ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddAzureAppConfiguration();
   }
   ```
   
Num cenário típico, irá atualizar os valores da bandeira de funcionalidade periodicamente à medida que implementa e ativa e diferentes funcionalidades da sua aplicação. Por predefinição, os valores da bandeira de recurso são em cache por um período de 30 segundos, pelo que uma operação de atualização desencadeada quando o middleware recebe um pedido não atualizaria o valor até que o valor em cache expirasse. O código que se segue mostra como alterar o tempo de validade da cache ou o intervalo de votação para 5 minutos, definindo o [CacheExpirationInterval](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.featuremanagement.featureflagoptions.cacheexpirationinterval) na chamada para **UseFeatureFlags**.


    
```csharp
config.AddAzureAppConfiguration(options =>
    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags(featureFlagOptions => {
        featureFlagOptions.CacheExpirationInterval = TimeSpan.FromMinutes(5);
    }));
});
```


## <a name="feature-flag-declaration"></a>Declaração de bandeira de característica

Cada declaração de bandeira tem duas partes: um nome, e uma lista de um ou mais filtros que são usados para avaliar se o estado de uma característica está *ligado* (isto é, quando o seu valor `True` está). Um filtro define um critério para quando uma função deve ser ligada.

Quando uma bandeira de recurso tem vários filtros, a lista de filtros é percorrida até que um dos filtros determine que a função deve ser ativada. Nessa altura, a bandeira da funcionalidade está *acesa*, e os restantes resultados do filtro são ignorados. Se nenhum filtro indicar que a função deve ser ativada, a bandeira de características está *desligada*.

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
* `FeatureC` especifica um filtro nomeado `Percentage` com uma `Parameters` propriedade. `Percentage` é um filtro configurável. Neste exemplo, `Percentage` especifica uma probabilidade de 50% para a bandeira estar `FeatureC` *em*. Para obter um guia sobre como utilizar filtros de funcionalidades, consulte [filtros de funcionalidades de utilização para permitir as bandeiras de características condicionais](./howto-feature-filters-aspnet-core.md).




## <a name="use-dependency-injection-to-access-ifeaturemanager"></a>Use a injeção de dependência para aceder iFeatureManager 

Para algumas operações, como verificar manualmente os valores da bandeira de características, é necessário obter um exemplo de [IFeatureManager](/dotnet/api/microsoft.featuremanagement.ifeaturemanager?preserve-view=true&view=azure-dotnet-preview). Em ASP.NET Core MVC, pode aceder ao gestor de recursos `IFeatureManager` através de injeção de dependência. No exemplo seguinte, é adicionado um argumento do tipo `IFeatureManager` à assinatura do construtor para um controlador. O tempo de funcionamento resolve automaticamente a referência e fornece uma parte da interface ao chamar o construtor. Se estiver a utilizar um modelo de aplicação no qual o controlador já tem um ou mais argumentos de injeção de dependência no construtor, `ILogger` como, por exemplo, pode apenas adicionar `IFeatureManager` como argumento adicional:

### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)
    
```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```
    
### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

---

## <a name="feature-flag-references"></a>Referências de bandeira de característica

Defina as bandeiras de características como variáveis de cordas para as referenciar a partir do código:

```csharp
public static class MyFeatureFlags
{
    public const string FeatureA = "FeatureA";
    public const string FeatureB = "FeatureB";
    public const string FeatureC = "FeatureC";
}
```

## <a name="feature-flag-checks"></a>Verificações de bandeira de recurso

Um padrão comum de gestão de recursos é verificar se uma bandeira de recurso está definida para *e, em* caso afirmativo, executar uma secção de código. Por exemplo:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(MyFeatureFlags.FeatureA))
{
    // Run the following code
}
```

## <a name="controller-actions"></a>Ações de controlador

Com controladores MVC, pode utilizar o `FeatureGate` atributo para controlar se uma classe de controlador inteiro ou uma ação específica está ativada. O seguinte controlador requer estar ligado antes de `HomeController` qualquer ação que a classe do controlador `FeatureA` contenha possa ser executada: 

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

Quando um controlador ou ação MVC é bloqueado porque a bandeira de características de controlo está *desligada,* uma interface [IDisabledFeaturesHandler](/dotnet/api/microsoft.featuremanagement.mvc.idisabledfeatureshandler?preserve-view=true&view=azure-dotnet-preview) é chamada. A `IDisabledFeaturesHandler` interface predefinida devolve um código de estado 404 ao cliente sem organismo de resposta.

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

Pode configurar filtros MVC para que sejam ativados com base no estado de uma bandeira de recurso. Esta capacidade limita-se a filtros que implementam [o IAsyncActionFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncactionfilter). O seguinte código adiciona um filtro MVC chamado `ThirdPartyActionFilter` . Este filtro só é acionado dentro do gasoduto MVC se `FeatureA` estiver ativado.  

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<ThirdPartyActionFilter>(MyFeatureFlags.FeatureA);
    });
}
```

## <a name="middleware"></a>Middleware

Também pode usar bandeiras de recurso para adicionar balcões de aplicação e middleware de aplicação condicionalmente. O seguinte código insere um componente de middleware no pipeline de pedido apenas quando `FeatureA` estiver ativado:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(MyFeatureFlags.FeatureA);
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