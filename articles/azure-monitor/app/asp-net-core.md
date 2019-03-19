---
title: Azure Application Insights para ASP.NET Core | Documentos da Microsoft
description: Monitorizar aplicações web de ASP.NET Core para disponibilidade, desempenho e utilização.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: mbullwin
ms.openlocfilehash: 7f3b8101b633c977201b6c413ad12e4bbe55e9a7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011791"
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights para Núcleo do ASP.NET

O Azure Application Insights fornece monitorização detalhada do seu aplicativo da web até o nível de código. Pode monitorizar facilmente a sua aplicação web de disponibilidade, desempenho e utilização. Também pode identificar e diagnosticar erros rapidamente na sua aplicação sem ter de esperar que um utilizador os comunique.

Este artigo explica os passos de criação de um exemplo de ASP.NET Core [páginas Razor](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) application no Visual Studio. Ele também mostra como iniciar a monitorização utilizando o Application Insights.

## <a name="prerequisites"></a>Pré-requisitos

- .NET core 2.0.0 SDK ou posterior
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) versão 15.7.3 ou posterior, com a carga de trabalho de desenvolvimento de ASP.NET e web

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Criar um projeto do ASP.NET Core no Visual Studio

1. Com o botão direito **Visual Studio 2017**e, em seguida, selecione **executar como administrador**.
2. Selecione **arquivo** > **nova** > **projeto** (Ctrl + Shift + N).

   ![Novo menu de projeto de captura de ecrã do Visual Studio](./media/asp-net-core/001-new-project.png)

3. Expanda **Visual C#**. Selecione **.NET Core** > **aplicação Web ASP.NET Core**. Introduza um nome de projeto e um nome de solução e, em seguida, selecione **criar novo repositório de Git**.

   ![Assistente de novo projeto de captura de ecrã do Visual Studio](./media/asp-net-core/002-asp-net-core-web-application.png)

4. Selecione **.NET Core** > **ASP.NET Core 2.0** **aplicação Web** > **OK**.

    ![Seleção de modelo de projeto nova captura de ecrã do Visual Studio](./media/asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Pesquisa do Application Insights

No Visual Studio 2015 Update 2 ou posterior com um projeto do ASP.NET Core 2 + com base, pode aproveitar [pesquisa do Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio), mesmo antes de adicionar explicitamente o Application Insights ao seu projeto.

Para testar esta funcionalidade:

1. Execute a aplicação. Para executar a aplicação, selecione o **IIS Express** ícone (![ícone de captura de ecrã do Visual Studio IIS Express](./media/asp-net-core/004-iis-express.png)).

2. Selecione **View** > **outros Windows** > **pesquisa do Application Insights**.

   ![Seleção de ferramentas de diagnóstico de captura de ecrã do Visual Studio](./media/asp-net-core/005-view-other-windows-search.png)

3. Atualmente, a telemetria da sessão de depuração está disponível apenas para análise de local. Para ativar totalmente o Application Insights, selecione **preparação de telemetria** no canto superior direito ou concluir os passos apresentados na secção seguinte.

   ![Pesquisa de captura de ecrã do Visual Studio Application Insights](./media/asp-net-core/006-search.png)

> [!NOTE]
> Para saber mais sobre como as luzes do Visual Studio desses recursos, como [pesquisa do Application Insights](../../azure-monitor/app/visual-studio.md) e [CodeLens](../../azure-monitor/app/visual-studio-codelens.md) localmente antes de adicionar o Application Insights ao seu projeto ASP.NET Core, veja [ Continuação de pesquisa do Application Insights](#application-insights-search-continued).

## <a name="add-application-insights-telemetry"></a>Adicionar Telemetria do Application Insights

1. Selecione **Project** > **adicionar telemetria do Application Insights**. (Ou, pode clicar **serviços ligados**e, em seguida, selecione **Adicionar serviço ligado**.)

    ![Menu de seleção de projeto novo captura de ecrã do Visual Studio](./media/asp-net-core/007-project-add-telemetry.png)

2. Selecione **começar**. (Dependendo da versão do Visual Studio, o texto pode variar um pouco. Algumas versões anteriores de ter uma **começar gratuitamente** botão em vez disso.)

    ![Captura de ecrã do Application Insights botão Iniciar](./media/asp-net-core/008-get-started.png)

3. Selecione a sua subscrição, em seguida, selecione **Resource** > **registar**.

## <a name="changes-made-to-your-project"></a>Alterações feitas ao seu projeto

O Application Insights é uma sobrecarga baixa. Para rever as modificações feitas em seu projeto ao adicionar telemetria do Application Insights:

Selecione **View** > **Team Explorer** (Ctrl +\, Ctrl + M) > **projeto** > **alterações**

- Alterações de total de quatro aparecem:

  ![Captura de ecrã de ficheiros foi alterada ao adicionar o Application Insights](./media/asp-net-core/009-changes.png)

- Um novo ficheiro é criado:

  - _ConnectedService.json_

    ```json
    {
     "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
     "Version": "8.12.10405.1",
     "GettingStartedDocument": {
       "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
     }
    }
    ```

- Três arquivos são modificados (comentários adicionais adicionados para realçar as alterações):

  - _appsettings.json_:

    ```json
    {
      "Logging": {
        "IncludeScopes": false,
        "LogLevel": {
          "Default": "Warning"
        }
      },
    // Changes to file post adding Application Insights Telemetry:
      "ApplicationInsights": {
        "InstrumentationKey": "10101010-1010-1010-1010-101010101010"
      }
    }
    //
    ```

  - _ContosoDotNetCore.csproj_:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
      <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
    <!--Changes to file post adding Application Insights Telemetry:-->
        <ApplicationInsightsResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsResourceId>
        <ApplicationInsightsAnnotationResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsAnnotationResourceId>
    <!---->
      </PropertyGroup>
      <ItemGroup>
    <!--Changes to file post adding Application Insights Telemetry:-->
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.1.1" />
    <!---->
        <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.8" />
      </ItemGroup>
      <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
      </ItemGroup>
    <!--Changes to file post adding Application Insights Telemetry:-->
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    <!---->
    </Project>
    ```

  -  _Program.cs_:

      ```csharp
      using System;
      using System.Collections.Generic;
      using System.IO;
      using System.Linq;
      using System.Threading.Tasks;
      using Microsoft.AspNetCore;
      using Microsoft.AspNetCore.Hosting;
      using Microsoft.Extensions.Configuration;
      using Microsoft.Extensions.Logging;

      namespace DotNetCore
      {
          public class Program
          {
              public static void Main(string[] args)
              {
                  BuildWebHost(args).Run();
              }

              public static IWebHost BuildWebHost(string[] args) =>
                  WebHost.CreateDefaultBuilder(args)
      // Change to file post adding Application Insights Telemetry:
                      .UseApplicationInsights()
      //
                      .UseStartup<Startup>()
                      .Build();
          }
      }
      ```

## <a name="send-ilogger-logs-to-application-insights"></a>Enviar registos de ILogger para o Application Insights

O Application Insights suporta captura os registos enviados por ILogger. Configurar o Check-out de Registro em log os exemplos de código [aqui](https://docs.microsoft.com/azure/azure-monitor/app/ilogger).

## <a name="synthetic-transactions-with-powershell"></a>Transações sintéticas com o PowerShell

Para automatizar os pedidos à sua aplicação utilizando transações sintéticas:

1. Para executar a aplicação, selecione o ![Ícone de captura de ecrã do Visual Studio IIS Express](./media/asp-net-core/004-iis-express.png) ícone.

2. Copie o URL da barra de endereço do browser. O URL está no formato `http://localhost:<port number>`.

   ![Captura de ecrã da URL na barra de endereço do browser](./media/asp-net-core/0013-copy-url.png)

3. Execute o seguinte loop de PowerShell para criar 100 transações sintéticas, utilizando a aplicação de teste. Modificar o número de porta depois `localhost:` para corresponder ao URl que copiou no passo anterior. Por exemplo:

   ```PowerShell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-the-application-insights-portal"></a>Abra o portal do Application Insights

Depois de executar os comandos do PowerShell na secção anterior, abra o Application Insights para ver as transações de e para confirmar que os dados estão a ser recolhidos. 

No menu do Visual Studio, selecione **Project** > **Application Insights** > **abrir Portal do Application Insights**.

   ![Captura de ecrã de descrição geral do Application Insights](./media/asp-net-core/010-portal.png)

> [!NOTE]
> A captura de ecrã do exemplo anterior, **Live Stream**, **tempo de carregamento da vista de página**, e **pedidos falhados** não são recolhidos. A secção seguinte descreve os passos para adicionar cada um deles. Se já está a recolher **Live Stream** e **tempo de carregamento da vista de página**, conclua os passos apenas para **pedidos falhados**.

## <a name="collect-failed-requests-live-stream-and-page-view-load-time"></a>Coletar pedidos falhados, o Stream em direto e o tempo de carregamento da vista de página

### <a name="failed-requests"></a>Pedidos com Falhas

Tecnicamente, pedidos falhados estão a ser recolhidos, mas não existem pedidos com falhas tem ocorrido ainda. Para acelerar o processo, pode adicionar uma exceção personalizada ao projeto existente para simular uma exceção do mundo real. Se a aplicação ainda está em execução no Visual Studio, antes de continuar, selecione **parar depuração** (Shift + F5).

1. Na **Explorador de soluções**, expanda **páginas** > **carregarei**e, em seguida, abra *About.cshtml.cs*.

   ![Captura de ecrã do Explorador de soluções do Visual Studio](./media/asp-net-core/011-about.png)

2. Adicionar uma exceção em ``Message=``e, em seguida, guarde a alteração para o ficheiro.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc.RazorPages;

    namespace DotNetCore.Pages
    {
        public class AboutModel : PageModel
        {
            public string Message { get; set; }

            public void OnGet()
            {
                Message = "Your application description page.";
                throw new Exception("Test Exception");
            }
        }
    }
    ```

### <a name="live-stream"></a>Live Stream

Para acessar a funcionalidade do Stream em direto do Application Insights com o ASP.NET Core, atualize os pacotes de NuGet Microsoft.ApplicationInsights.AspNetCore 2.2.0.

No Visual Studio, selecione **Project** > **gerir pacotes NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > (versão) **2.2.0** > **update**.

  ![Captura de ecrã do Gestor de pacotes do NuGet](./media/asp-net-core/012-nuget-update.png)

São apresentados vários pedidos de confirmação. Leia e aceite se concordar com as alterações.

### <a name="page-view-load-time"></a>Tempo de carregamento da vista de página

1. No Visual Studio, aceda a **Explorador de soluções** > **páginas**. Tem de modificar dois ficheiros: *Layout. cshtml* e *ViewImports.cshtml*.

2. Na *ViewImports.cshtml*, adicione este código:

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. Na *layout. cshtml*, adicione o seguinte código antes do ``</head>`` etiqueta e antes de quaisquer outros scripts:

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-and-live-stream"></a>Teste de pedidos falhados, tempo de carregamento da vista de página e Live Stream

Para testar e confirmar que tudo o que está a funcionar:

1. Execute a aplicação. Para executar a aplicação, selecione o ![Ícone de captura de ecrã do Visual Studio IIS Express](./media/asp-net-core/004-iis-express.png) ícone.

2. Vá para o **sobre** página para acionar a exceção de teste. (Se estiver no modo de depuração no Visual Studio, selecione **continuar** para a exceção a aparecer no Application Insights.)

3. Volte a executar o script de transação simulado do PowerShell que foi utilizado anteriormente. (Poderá ter de ajustar o número de porta no script.)

4. Se o **descrição geral** página no Insights de aplicativos ainda não está aberta, no menu do Visual Studio, selecione **projeto** > **Application Insights**  >  **Abrir o Portal do Application Insights**. 

   > [!TIP]
   > Se não vir o seu novo tráfego, verifique o valor de **intervalo de tempo**e, em seguida, selecione **atualizar**.

   ![Captura de ecrã da janela de descrição geral](./media/asp-net-core/0019-overview-updated.png)

5. Selecione **Live Stream**.

   ![Captura de ecrã do Stream de métricas em direto](./media/asp-net-core/0020-live-metrics-stream.png)

   (Se o script do PowerShell ainda está em execução, deverá ver métricas em direto. Se o seu script de PowerShell parou de executar, execute novamente o script com open Live Stream.)

## <a name="application-insights-sdk-comparison"></a>Comparação do Application Insights SDK

O grupo de produtos do Application Insights tem trabalhado arduamente para alcançar a paridade de funcionalidades entre o [completa do SDK do .NET Framework](https://github.com/Microsoft/ApplicationInsights-dotnet) e o SDK .NET Core. O 2.2.0 da versão dos [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) para o Application Insights fecha-se em grande parte a diferença de funcionalidades.

A tabela seguinte descreve mais das diferenças e compensações entre [.NET e .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server):

   | Comparação SDK | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Métricas em direto**      | **+** |**-** | **+** |
   | **Canal de telemetria do servidor** | **+** |**-** | **+**|
   |**Amostragem adaptável**| **+** | **-** | **+**|
   | **Chamadas de dependência SQL**     | **+** |**-** | **+**|
   | **Contadores de desempenho*** | **+** | **-**| **-**|

Contadores de desempenho neste contexto, consulte a [contadores de desempenho do lado do servidor](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) como processador, memória e utilização do disco.

## <a name="open-source-sdk"></a>SDK de código-fonte aberto
[Ler e contribuir para o código](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="application-insights-search-continued"></a>Continuação de pesquisa do Application Insights

Nesta secção pode ajudar a compreender melhor como funciona a pesquisa do Application Insights no Visual Studio para um projeto do ASP.NET Core 2. Ele funciona dessa maneira, mesmo quando ainda não instalou explicitamente os pacotes de NuGet do Application Insights ainda. Também pode ser útil examinar a saída de depuração.

Se pesquisar a saída para a palavra _insight_, destacam-se os resultados semelhantes ao seguinte:

```DebugOutput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

Na saída, o CoreCLR carrega dois assemblies: 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

O _não configurado_ referência em cada instância de telemetria do Application Insights indica que esta aplicação não está associada a uma ikey. Os dados que são gerados enquanto a aplicação está em execução não são enviados para o Azure. Os dados estão disponíveis apenas para análise e pesquisa local.

A funcionalidade é possível em parte porque o pacote NuGet _Microsoft.AspNetCore.All_ demora [ _Microsoft.ASPNetCoreApplicationInsights.HostingStartup_ ](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1) como uma dependência.

![Gráfico de dependência de captura de ecrã do NuGet para Microsoft.AspNETCore.all](./media/asp-net-core/013-dependency.png)

Fora do Visual Studio, se estava editando um projeto do ASP.NET Core no VSCode ou noutro editor, esses assemblies não automaticamente carregar durante a depuração se explicitamente ainda não adicionou o Application Insights ao seu projeto.

No entanto, no Visual Studio, esta iluminação cópia de segurança de funcionalidades do Application Insights locais de assemblies externos é realizada usando a [IHostingStartup Interface](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1). A interface adiciona dinamicamente o Application Insights durante a depuração.

Saiba mais sobre o aprimoramento de uma aplicação a partir de um [assembly externo no ASP.NET Core com IHostingStartup](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1). 

### <a name="disable-application-insights-in-visual-studio-net-core-projects"></a>Desativar o Application Insights em projetos do Visual Studio .NET Core

Embora a luz-up automática do Application Insights pesquisar funcionalidade pode ser útil, ver a telemetria de depuração gerado quando não estavam esperando por que ele pode ser confuso.

Se desativar apenas a geração de telemetria é suficiente, pode adicionar este bloco de código para o **configure** método de sua _Startup.cs_ ficheiro:

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

Ainda assim será carregada CoreCLR _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ e _Microsoft.ApplicationInsights.AspNetCore.dll_, mas os ficheiros não fazem nada.

Se pretender desativar completamente o Application Insights no seu projeto do Visual Studio .NET Core, o método preferencial é selecionar **ferramentas** > **opções**  >   **Soluções e projetos** > **projetos da Web**. Selecione o **desativar local Application Insights para projetos da web ASP.NET Core** caixa de verificação. Esta funcionalidade foi adicionada no Visual Studio 15,6.

![Ecrã de captura de ecrã das opções janela Web projetos do Visual Studio](./media/asp-net-core/014-disable.png)

Se estiver a executar uma versão anterior do Visual Studio e de que pretende remover completamente todos os assemblies que foram carregados por meio *IHostingStartup*, tem duas opções:

* Adicione `.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")` para _Program.cs_:

  ```csharp
  using System;
  using System.Collections.Generic;
  using System.IO;
  using System.Linq;
  using System.Threading.Tasks;
  using Microsoft.AspNetCore;
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.Configuration;
  using Microsoft.Extensions.Logging;

  namespace DotNetCore
  {
      public class Program
      {
          public static void Main(string[] args)
          {
              BuildWebHost(args).Run();
          }

          public static IWebHost BuildWebHost(string[] args) =>
              WebHost.CreateDefaultBuilder(args)
                  .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
                  .UseStartup<Startup>()
                  .Build();
      }
  }
  ```

* Adicione ``"ASPNETCORE_preventHostingStartup": "True"`` para _launchSettings.json_ variáveis de ambiente.

O problema com o uso de um desses métodos é que não a tiver apenas o Application Insights. Os métodos também desativar qualquer coisa no Visual Studio que estava a utilizar o *IHostingStartup* funcionalidade animados.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Passos Seguintes
* [Explore os fluxos de utilizador](../../azure-monitor/app/usage-flows.md) para compreender a forma como os utilizadores navegam pela aplicação.
* [Configurar a recolha de instantâneo](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications) para ver o estado do código-fonte e variáveis no momento é emitida uma exceção.
* [Utilizar a API](../../azure-monitor/app/api-custom-events-metrics.md) para enviar os seus próprios eventos e métricas para uma visão mais detalhada do desempenho e a utilização da sua aplicação.
* Uso [testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) para verificar a sua aplicação a partir do constantemente em todo o mundo.
