---
title: Remover Insights de Aplicações em Estúdio Visual - Monitor Azure
description: Como remover a Aplicação Insights SDK para ASP.NET e ASP.NET Core em Estúdio Visual.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 1d70413fa6a47e2d41693db6eb705f31b2a2b1b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101704315"
---
# <a name="how-to-remove-application-insights-in-visual-studio"></a>Como remover insights de aplicações em Estúdio Visual

Este artigo irá mostrar-lhe como remover o ASP.NET e ASP.NET Core Application Insights SDK em Visual Studio.

Para remover o Application Insights, terá de remover as embalagens e referências do NuGet da API na sua aplicação. Pode desinstalar os pacotes NuGet utilizando a Consola de Gestão de Pacotes ou Gerir a Solução NuGet em Estúdio Visual. As seguintes secções mostrarão duas formas de remover os Pacotes NuGet e o que foi automaticamente adicionado no seu projeto. Certifique-se de que confirma os ficheiros adicionados e as áreas com o seu próprio código em que fez chamadas para a API são removidas.

## <a name="uninstall-using-the-package-management-console"></a>Desinstalar utilizando a consola de gestão de pacotes

# <a name="net"></a>[.NET](#tab/net)

1. Para abrir a Consola de Gestão de Pacotes, no menu superior selecione Ferramentas > NuGet Package Manager > consola de gestor de pacotes.
     
    ![No menu superior clique em Ferramentas > NuGet Package Manager > consola de gestor de pacotes](./media/remove-application-insights/package-manager.png)

    > [!NOTE]
    > Se a recolha de vestígios estiver ativa, é necessário desinstalar primeiro o Microsoft.ApplicationInsights.TraceListener. Introduza `Uninstall-package Microsoft.ApplicationInsights.TraceListener` então siga o passo abaixo para remover Microsoft.ApplicationInsights.Web.

1. Introduza o seguinte comando: `Uninstall-Package Microsoft.ApplicationInsights.Web -RemoveDependencies`

    Após a entrada no comando, o pacote Application Insights e todas as suas dependências serão desinstalados do projeto.
    
    ![Insira o comando na consola](./media/remove-application-insights/package-management-console.png)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. Para abrir a Consola de Gestão de Pacotes, no menu superior selecione Ferramentas > NuGet Package Manager > consola de gestor de pacotes.

    ![No menu superior clique em Ferramentas > NuGet Package Manager > consola de gestor de pacotes](./media/remove-application-insights/package-manager.png)

1. Introduza o seguinte comando: ` Uninstall-Package Microsoft.ApplicationInsights.AspNetCore -RemoveDependencies`

    Após a entrada no comando, o pacote Application Insights e todas as suas dependências serão desinstalados do projeto.

---

## <a name="uninstall-using-the-visual-studio-nuget-ui"></a>Desinstalar usando o Visual Studio NuGet UI

# <a name="net"></a>[.NET](#tab/net)

1. No *Solution Explorer* à direita, clique à direita na **Solução** e selecione **Gerir pacotes nuget para solução.**

    Em seguida, verá um ecrã que lhe permite editar todos os pacotes NuGet que fazem parte do projeto.
    
     ![Clique à direita Solução, no Explorador de Solução e, em seguida, selecione Gerir pacotes nuget para solução](./media/remove-application-insights/manage-nuget-framework.png)

    > [!NOTE]
    > Se a recolha de vestígios estiver ativa, é necessário desinstalar primeiro o Microsoft.ApplicationInsights.TraceListener sem remover as dependências selecionadas e, em seguida, seguir os passos abaixo para desinstalar microsoft.ApplicationInsights.Web com as dependências de remoção selecionadas.

1. Clique no pacote **Microsoft.ApplicationInsights.Web.** À direita, verifique a caixa de verificação ao lado do **Project** para selecionar todos os projetos.

1. Para remover todas as dependências ao desinstalar, selecione o botão de desaudamento **das opções** abaixo da secção onde selecionou o projeto.

    Em *Opções de Desinstalar*, selecione a caixa de verificação ao lado *das dependências de Remover*.

1. Selecione **Desinstalar**.
    
    ![A screenshot mostra a janela Microsoft.ApplicationInsights.Web com as dependências de Remover verificadas e desinstalar realçadas.](./media/remove-application-insights/uninstall-framework.png)

    Será apresentada uma caixa de diálogo que mostra todas as dependências a serem removidas da aplicação. Selecione **ok** para desinstalar.
    
    ![A screenshot mostra uma caixa de diálogo com as dependências a serem removidas.](./media/remove-application-insights/preview-uninstall-framework.png)
    
1.  Depois de tudo desinstalado, poderá ainda ver "ApplicationInsights.config" e "AiHandleErrorAttribute.cs" no *Solution Explorer*. Pode apagar os dois ficheiros manualmente.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. No *Solution Explorer* à direita, clique à direita na **Solução** e selecione **Gerir pacotes nuget para solução.**

    Em seguida, verá um ecrã que lhe permite editar todos os pacotes NuGet que fazem parte do projeto.

    ![Clique à direita Solução, no Explorador de Solução e, em seguida, selecione Gerir pacotes nuget para solução](./media/remove-application-insights/manage-nuget-core.png)

1. Clique no pacote "Microsoft.ApplicationInsights.AspNetCore". À direita, verifique a caixa de verificação ao lado do *Project* para selecionar todos os projetos e, em seguida, selecione **Desinstalar**.

    ![Verifique as dependências de remoção e, em seguida, desinstalar](./media/remove-application-insights/uninstall-core.png)

---

## <a name="what-is-created-when-you-add-application-insights"></a>O que é criado quando adiciona Insights de Aplicação

Quando adiciona Insights de Aplicação ao seu projeto, cria ficheiros e adiciona código a alguns dos seus ficheiros. Apenas desinstalar os Pacotes NuGet nem sempre descartará os ficheiros e códigos. Para remover totalmente os Insights de Aplicação, deverá verificar e eliminar manualmente o código ou ficheiros adicionados, juntamente com quaisquer chamadas API adicionadas no seu projeto.

# <a name="net"></a>[.NET](#tab/net)

Quando adiciona Telemetria Application Insights a um projeto de ASP.NET do Visual Studio, adiciona os seguintes ficheiros:

- ApplicationInsights.config
- AiHandleErrorAttribute.cs

São adicionados os seguintes códigos:

- [Nome do seu projeto].csproj

    ```C#
     <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4</ApplicationInsightsResourceId>
    ```

- Packages.config

    ```xml
    <packages>
    ...
    
      <package id="Microsoft.ApplicationInsights" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Agent.Intercept" version="2.4.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.DependencyCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.PerfCounterCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Web" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" version="2.12.0" targetFramework="net472" />
    
      <package id="Microsoft.AspNet.TelemetryCorrelation" version="1.0.7" targetFramework="net472" />
    
      <package id="System.Buffers" version="4.4.0" targetFramework="net472" />
      <package id="System.Diagnostics.DiagnosticSource" version="4.6.0" targetFramework="net472" />
      <package id="System.Memory" version="4.5.3" targetFramework="net472" />
      <package id="System.Numerics.Vectors" version="4.4.0" targetFramework="net472" />
      <package id="System.Runtime.CompilerServices.Unsafe" version="4.5.2" targetFramework="net472" />
    ...
    </packages>
    ```

- Layout.cshtml

    Se o seu projeto tiver um ficheiro Layout.cshtml, o código abaixo é adicionado.
    
    ```html
    <head>
    ...
        <script type = 'text/javascript' >
            var appInsights=window.appInsights||function(config)
            {
                function r(config){ t[config] = function(){ var i = arguments; t.queue.push(function(){ t[config].apply(t, i)})} }
                var t = { config:config},u=document,e=window,o='script',s=u.createElement(o),i,f;for(s.src=config.url||'//az416426.vo.msecnd.net/scripts/a/ai.0.js',u.getElementsByTagName(o)[0].parentNode.appendChild(s),t.cookie=u.cookie,t.queue=[],i=['Event','Exception','Metric','PageView','Trace','Ajax'];i.length;)r('track'+i.pop());return r('setAuthenticatedUserContext'),r('clearAuthenticatedUserContext'),config.disableExceptionTracking||(i='onerror',r('_'+i),f=e[i],e[i]=function(config, r, u, e, o) { var s = f && f(config, r, u, e, o); return s !== !0 && t['_' + i](config, r, u, e, o),s}),t
            }({
                instrumentationKey:'00000000-0000-0000-0000-000000000000'
            });
            
            window.appInsights=appInsights;
            appInsights.trackPageView();
        </script>
    ...
    </head>
    ```

- ConnectedService.jsem

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=613413"
      }
    }
    ```

- FilterConfig.cs

    ```csharp
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());// This line was added
            }
    ```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Quando adiciona telemetria application insights a um projeto de modelo de ASP.NET Core do Estúdio Visual, adiciona o seguinte código:

- [Nome do seu projeto].csproj

    ```csharp
      <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4core</ApplicationInsightsResourceId>
      </PropertyGroup>
    
      <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.12.0" />
      </ItemGroup>
    
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    ```

- Appsettings.js:

    ```json
    "ApplicationInsights": {
        "InstrumentationKey": "00000000-0000-0000-0000-000000000000"
    ```

- ConnectedService.jsem
    
    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```
- Startup.cs

    ```csharp
       public void ConfigureServices(IServiceCollection services)
            {
                services.AddRazorPages();
                services.AddApplicationInsightsTelemetry(); // This is added
            }
    ```

---

## <a name="next-steps"></a>Passos seguintes

- [Azure Monitor](../overview.md)
