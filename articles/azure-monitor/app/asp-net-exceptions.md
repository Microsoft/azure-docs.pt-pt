---
title: Diagnosticar falhas e exceções nas aplicações web com o Azure Application Insights | Documentos da Microsoft
description: Capture exceções a partir de aplicações do ASP.NET, juntamente com a telemetria de pedido.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d1e98390-3ce4-4d04-9351-144314a42aa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: mbullwin
ms.openlocfilehash: cb32069de295b883cdc6d3a9fa495b1bea719c39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691861"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Diagnosticar exceções nas aplicações web com o Application Insights
Exceções na sua aplicação web em direto são enviadas pelos [Application Insights](../../azure-monitor/app/app-insights-overview.md). Pode correlacionar pedidos com falhas com exceções e outros eventos no cliente e servidor, para que pode rapidamente a diagnosticar as causas.

## <a name="set-up-exception-reporting"></a>Configurar relatórios de exceção
* Para ter exceções reportadas da sua aplicação de servidor:
  * Instale [SDK do Application Insights](../../azure-monitor/app/asp-net.md) em seu código de aplicação, ou
  * Servidores web do IIS: Execute [Application Insights agente](../../azure-monitor/app/monitor-performance-live-website-now.md); ou
  * Aplicações web do Azure: Adicionar o [extensão do Application Insights](../../azure-monitor/app/azure-web-apps.md)
  * Aplicações web de Java: Instalar o [agente Java](../../azure-monitor/app/java-agent.md)
* Instalar o [fragmento do JavaScript](../../azure-monitor/app/javascript.md) nas suas páginas da web para capturar exceções do browser.
* Em algumas estruturas de aplicativo ou com algumas definições, tem de efetuar alguns passos adicionais para capturar exceções mais:
  * [Formulários da Web](#web-forms)
  * [MVC](#mvc)
  * [1 de Web API. *](#web-api-1x)
  * [Web API 2.*](#web-api-2x)
  * [WCF](#wcf)

## <a name="diagnosing-exceptions-using-visual-studio"></a>Diagnosticar exceções com o Visual Studio
Abra a solução de aplicação no Visual Studio para ajudar na depuração.

Execute a aplicação, no seu servidor ou no computador de desenvolvimento com o F5.

Abra a janela de pesquisa do Application Insights no Visual Studio e defini-lo para apresentar eventos a partir da sua aplicação. Enquanto estiver a depurar, pode fazê-lo apenas ao clicar no botão do Application Insights.

![Com o botão direito no projeto e escolha Application Insights, aberto.](./media/asp-net-exceptions/34.png)

Tenha em atenção que pode filtrar o relatório para mostrar apenas exceções.

*Sem exceções que mostra? Ver [capturar exceções](#exceptions).*

Clique num relatório de exceção para mostrar o rastreamento de pilha.
Clique numa referência de linha no rastreio de pilha, para abrir o ficheiro de código relevante.

No código, tenha em atenção que o CodeLens mostra dados sobre as exceções:

![Notificação de CodeLens de exceções.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Diagnosticar falhas no portal do Azure
Application Insights é fornecido com uma experiência APM organizada para ajudar a diagnosticar falhas nas suas aplicações monitorizadas. Para começar, clique na opção de falhas no menu de recursos do Application Insights, localizado na secção investigar.
Deverá ver uma vista de ecrã inteiro, que mostra as tendências da taxa de falha para os seus pedidos, quantos deles estão a falhar e o número de utilizadores é afetado. No lado direito verá algumas das mais úteis distribuições específicas selecionado para operação, incluindo códigos de resposta de 3 principais tipos de exceção do 3 principais e 3 principais tipos de dependência a falhar a falhar.

![Falhas de triagem vista (Guia de operações)](./media/asp-net-exceptions/FailuresTriageView.png)

Num único clique, pode analisar exemplos representativos para cada um desses subconjuntos de operações. Em particular, para diagnosticar exceções, pode clicar na contagem de uma exceção em particular a serem apresentados com um painel de detalhes de exceções, como este:

![Painel de detalhes da exceção](./media/asp-net-exceptions/ExceptionDetailsBlade.png)

**Em alternativa,** em vez de olhar exceções de uma operação com falha específico, pode começar a partir da vista geral de exceções, alternando para o separador de exceções:

![Falhas de triagem vista (Guia de exceções)](./media/asp-net-exceptions/FailuresTriageView_Exceptions.png)

Aqui pode ver todas as exceções recolhidas para a sua aplicação monitorizada.

*Sem exceções que mostra? Ver [capturar exceções](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Dados de registo e rastreio personalizado
Para obter dados de diagnóstico específico para a sua aplicação, pode inserir código para enviar os seus dados de telemetria. Isso apresentado na pesquisa de diagnósticos juntamente com o pedido, a vista de página e a outros dados recolhidos automaticamente.

Tem várias opções:

* [Trackevent ()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) é normalmente utilizada para monitorização de padrões de utilização, mas os dados que envia também é apresentado em Personalizar eventos na pesquisa de diagnósticos. Eventos são nomeados e pode erguer propriedades de cadeia de caracteres e métricas numérico no qual pode [filtrar suas pesquisas de diagnóstico](../../azure-monitor/app/diagnostic-search.md).
* [Tracktrace ()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) permite-lhe enviar mais dados, tais como informações de publicação.
* [Trackexception ()](#exceptions) envia rastreios de pilha. [Obter mais informações sobre exceções](#exceptions).
* Se já utiliza uma arquitetura de registo, como Log4Net ou NLog, pode [capturar esses registos](asp-net-trace-logs.md) e vê-los na pesquisa de diagnósticos juntamente com os dados de pedido e de exceção.

Para ver estes eventos, abra [pesquisa](../../azure-monitor/app/diagnostic-search.md), abra o filtro e, em seguida, escolha o evento personalizado, rastreio ou exceção.

![Explorar](./media/asp-net-exceptions/viewCustomEvents.png)

> [!NOTE]
> Se a sua aplicação gerar uma grande quantidade de telemetria, o módulo de amostragem adaptável irá reduzir automaticamente o volume que é enviado para o portal, enviando apenas uma fração representativa dos eventos. Eventos que fazem parte da mesma operação serão selecionados ou desmarcados como um grupo, para que pode navegar entre os eventos relacionados. [Saiba mais sobre amostragem.](../../azure-monitor/app/sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Como ver dados de POSTAGEM de pedido
Detalhes do pedido não incluem os dados enviados para a aplicação numa chamada POST. Para ter estes dados reportados:

* [Instalar o SDK](../../azure-monitor/app/asp-net.md) no seu projeto de aplicativo.
* Insira o código na sua aplicação chamar [Microsoft.ApplicationInsights.TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace). Envie os dados POST no parâmetro de mensagem. Existe um limite de tamanho permitido, portanto, deve tentar enviar apenas os dados essenciais.
* Quando estiver a investigar uma solicitação com falha, localize os rastreios associados.

![Explorar](./media/asp-net-exceptions/060-req-related.png)

## <a name="exceptions"></a> Capturar exceções e dados de diagnóstico relacionados
Em primeiro lugar, não verá no portal de todas as exceções que provocam falhas na sua aplicação. Verá as exceções de browser (se estiver a utilizar o [JavaScript SDK](../../azure-monitor/app/javascript.md) nas suas páginas web). Mas a maioria das exceções de servidor são detetadas pelo IIS e precisa escrever um pouco de código para vê-los.

Pode:

* **Registar as exceções explicitamente** inserindo código em processadores de exceções para comunicar as exceções.
* **Capturar exceções automaticamente** ao configurar a sua estrutura do ASP.NET. As adições necessárias são diferentes para diferentes tipos de estrutura.

## <a name="reporting-exceptions-explicitly"></a>Relatórios de exceções explicitamente
A forma mais simples é inserir uma chamada para trackexception () num manipulador de exceção.

```javascript
    try
    { ...
    }
    catch (ex)
    {
      appInsights.trackException(ex, "handler loc",
        {Game: currentGame.Name,
         State: currentGame.State.ToString()});
    }
```

```csharp
    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }
```

```VB
    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)

      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try
```

Os parâmetros de propriedades e medidas são opcionais, mas são úteis para [filtragem e adicionar](../../azure-monitor/app/diagnostic-search.md) informações extras. Por exemplo, se tiver uma aplicação que pode executar vários jogos, foi possível encontrar todos os relatórios de exceção relacionados com um jogo específico. Pode adicionar tantos itens que forem necessárias para cada dicionário.

## <a name="browser-exceptions"></a>Exceções de browser
A maioria das exceções do browser são relatadas.

Se sua página da web inclui os arquivos de script de redes de entrega de conteúdos ou outros domínios, certifique-se de sua marca de script tem o atributo ```crossorigin="anonymous"```, e que o servidor envia [cabeçalhos CORS](https://enable-cors.org/). Isso permitirá que obter um rastreio de pilha e os detalhes de exceções de JavaScript não processadas destes recursos.

## <a name="web-forms"></a>Formulários da Web
Para formulários da web, será possível recolher as exceções quando não há nenhum redirecionamentos configurados com CustomErrors o módulo de HTTP.

Mas se tiver redirecionamentos de Active Directory, adicione as seguintes linhas para a função de Application_Error em Global.asax.cs. (Adicionar um arquivo global asax se ainda não tiver uma.)

```csharp
    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }
```

## <a name="mvc"></a>MVC
A partir do Application Insights Web SDK versão 2.6 (beta3 e posterior), o Application Insights recolhe sem tratamento exceções geradas em métodos de 5 + controladores MVC automaticamente. Se anteriormente tiver adicionado um manipulador personalizado para controlar essas exceções (conforme descrito nos seguintes exemplos), pode remover-a para impedir o controle duplo de exceções.

Há um número de casos que não é possível processar os filtros de exceção. Por exemplo:

* Exceções geradas por construtores controller.
* Exceções geradas por manipuladores de mensagens.
* Exceções geradas durante o roteamento.
* Exceções geradas durante a serialização de conteúdo de resposta.
* Exceção acionada durante a inicialização do aplicativo.
* Exceção lançada em tarefas em segundo plano.

Todas as exceções *manipulado* pelo aplicativo ainda precisam ser rastreadas manualmente.
Exceções sem tratamento provenientes de controladores normalmente resultam em 500 resposta de "Erro de servidor interno". Se esse tipo de resposta manualmente é construído como resultado da exceção processada (ou nenhuma exceção nada) é controlado em telemetria de pedido correspondente com `ResultCode` 500, no entanto o Application Insights SDK não consegue registar a exceção correspondente.

### <a name="prior-versions-support"></a>Suporte de versões anteriores
Se utilizar MVC 4 (e anterior) do Application Insights Web SDK 2.5 (e anterior), consulte os seguintes exemplos de acompanhar as exceções.

Se o [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) é de configuração `Off`, em seguida, as exceções estarão disponíveis para o [módulo HTTP](https://msdn.microsoft.com/library/ms178468.aspx) para recolher. No entanto, se for `RemoteOnly` (predefinição), ou `On`, em seguida, a exceção será limpa e não está disponível para o Application Insights recolher automaticamente. Pode corrigir isso substituindo o [classe System.Web.Mvc.HandleErrorAttribute](https://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx)e aplicar a classe substituída, conforme mostrado para as diferentes versões MVC abaixo ([origem da GitHub](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

```csharp
    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                }
            }
            base.OnException(filterContext);
        }
      }
    }
```

#### <a name="mvc-2"></a>MVC 2
Substitua o atributo HandleError seu novo atributo em seus controladores.

```csharp
    namespace MVC2App.Controllers
    {
        [AiHandleError]
        public class HomeController : Controller
        {
    ...
```

[Exemplo](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Registar `AiHandleErrorAttribute` como um filtro global em Global.asax.cs:

```csharp
    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...
```

[Exemplo](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5
Register AiHandleErrorAttribute como um filtro global em FilterConfig.cs:

```csharp
    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }
```

[Exemplo](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>API Web
A partir do Application Insights Web SDK versão 2.6 (beta3 e posterior), Application Insights recolhe exceções não tratadas nos métodos de controlador automaticamente emitidas para WebAPI 2 +. Se anteriormente tiver adicionado um manipulador personalizado para controlar essas exceções (conforme descrito nos seguintes exemplos), pode remover-a para impedir o controle duplo de exceções.

Há um número de casos que não é possível processar os filtros de exceção. Por exemplo:

* Exceções geradas por construtores controller.
* Exceções geradas por manipuladores de mensagens.
* Exceções geradas durante o roteamento.
* Exceções geradas durante a serialização de conteúdo de resposta.
* Exceção acionada durante a inicialização do aplicativo.
* Exceção lançada em tarefas em segundo plano.

Todas as exceções *manipulado* pelo aplicativo ainda precisam ser rastreadas manualmente.
Exceções sem tratamento provenientes de controladores normalmente resultam em 500 resposta de "Erro de servidor interno". Se esse tipo de resposta manualmente é construído como resultado da exceção processada (ou nenhuma exceção nada) é controlado numa telemetria de pedido correspondente com `ResultCode` 500, no entanto o Application Insights SDK não consegue registar a exceção correspondente.

### <a name="prior-versions-support"></a>Suporte de versões anteriores
Se usar WebAPI 1 (e anterior) do Application Insights Web SDK 2.5 (e anterior), consulte os seguintes exemplos de acompanhar as exceções.

#### <a name="web-api-1x"></a>Web API 1.x
Substitua System.Web.Http.Filters.ExceptionFilterAttribute:

```csharp
    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);
            }
            base.OnException(actionExecutedContext);
        }
      }
    }
```

Pode adicionar esse atributo substituído para controladores específicos, ou adicione-o para a configuração de filtro global na classe WebApiConfig:

```csharp
    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }
```

[Exemplo](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

#### <a name="web-api-2x"></a>Web API 2.x
Adicione uma implementação do IExceptionLogger:

```csharp
    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }
```

Adicione-a para os serviços no WebApiConfig:

```csharp
    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger());
        }
      }
     }
```

[Exemplo](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Como alternativas, pode:

1. Substitua o ExceptionHandler apenas uma implementação personalizada de IExceptionHandler. Apenas é chamado quando a estrutura é ainda pode escolher qual mensagem de resposta para enviar (não quando a ligação foi abortada por exemplo)
2. Filtros de exceção (conforme descrito na seção sobre controladores da Web API 1.x acima) - não é chamados em todos os casos.

## <a name="wcf"></a>WCF
Adicione uma classe que expande o atributo e implementa o IErrorHandler e o IServiceBehavior.

```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error,
            System.ServiceModel.Channels.MessageVersion version,
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

Add the attribute to the service implementations:

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1
        {
         ...
```

[Exemplo](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Contadores de desempenho de exceção
Se tiver [instalar o agente do Application Insights](../../azure-monitor/app/monitor-performance-live-website-now.md) no seu servidor, pode obter um gráfico de taxa de exceções, medido pela .NET. Isto inclui processadas e não processadas exceções de .NET.

Abrir um painel do Explorador de métricas, adicione um novo gráfico e selecione **taxa de exceções**, listado sob os contadores de desempenho.

O .NET framework calcula a taxa de contar o número de exceções num intervalo e divisão pelo comprimento do intervalo.

Isto é diferente da contagem de "Exceções" calculada ao portal do Application Insights contagem TrackException relatórios. Os intervalos de amostragem são diferentes e o SDK não envia relatórios TrackException para todos os processado e exceções não processadas.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passos Seguintes
* [Monitorizar o REST, SQL e outras chamadas para dependências](../../azure-monitor/app/asp-net-dependencies.md)
* [Monitorizar os tempos de carregamento de página, exceções do browser e chamadas AJAX](../../azure-monitor/app/javascript.md)
* [Contadores do Monitor de desempenho](../../azure-monitor/app/performance-counters.md)
