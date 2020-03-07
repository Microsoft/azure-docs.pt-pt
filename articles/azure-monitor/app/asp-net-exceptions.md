---
title: Diagnosticar falhas e exceções com insights de aplicação azure
description: Capture exceções de apps ASP.NET juntamente com telemetria de pedidos.
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 24b7acfa6610c2040daf0f7d8d25f25391140303
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375060"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Diagnosticar exceções nas suas aplicações web com Insights de Aplicação
As exceções na sua aplicação ao vivo são relatadas pela [Application Insights](../../azure-monitor/app/app-insights-overview.md). Pode correlacionar pedidos falhados com exceções e outros eventos tanto no cliente como no servidor, para que possa diagnosticar rapidamente as causas.

## <a name="set-up-exception-reporting"></a>Configurar relatórios de exceção
* Para ter exceções reportadas a partir da sua aplicação de servidor:
  * Aplicações web Azure: Adicione a extensão de insights de [aplicação](../../azure-monitor/app/azure-web-apps.md)
  * Aplicativos de escala virtual Azure VM e Azure IIS: Adicione a extensão de monitorização de [aplicações](../../azure-monitor/app/azure-vm-vmss-apps.md)
  * Instale [insights de aplicação SDK](../../azure-monitor/app/asp-net.md) no seu código de aplicações, ou
  * Servidores web IIS: Executar [Agente de Insights de Aplicação;](../../azure-monitor/app/monitor-performance-live-website-now.md) ou
  * Aplicações web Java: Instale o [agente Java](../../azure-monitor/app/java-agent.md)
* Instale o [corte JavaScript](../../azure-monitor/app/javascript.md) nas suas páginas web para capturar exceções ao navegador.
* Em alguns quadros de aplicação ou com algumas configurações, é necessário tomar algumas medidas adicionais para apanhar mais exceções:
  * [Formulários web](#web-forms)
  * [MVC](#mvc)
  * [Web API 1.*](#web-api-1x)
  * [Web API 2.*](#web-api-2x)
  * [WCF](#wcf)

## <a name="diagnosing-exceptions-using-visual-studio"></a>Diagnóstico de exceções usando o Estúdio Visual
Abra a solução de aplicações no Visual Studio para ajudar na depuração.

Execute a aplicação, quer no seu servidor quer na sua máquina de desenvolvimento utilizando O F5.

Abra a janela de pesquisa de Insights de Aplicação no Estúdio Visual e desempente-a para exibir eventos a partir da sua aplicação. Enquanto está a depurar, pode fazê-lo apenas clicando no botão Insights de Aplicação.

![Clique no projeto e escolha Insights de Aplicação, Abra.](./media/asp-net-exceptions/34.png)

Note que pode filtrar o relatório para mostrar apenas exceções.

*Não há exceções? Ver [exceções de captura.](#exceptions)*

Clique num relatório de exceção para mostrar o seu traço de pilha.
Clique numa referência de linha no traço da pilha, para abrir o ficheiro de código relevante.

No código, note que codeLens mostra dados sobre as exceções:

![Notificação codeLens de exceções.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Diagnosticar falhas utilizando o portal Azure
Application Insights vem com uma experiência APM com curadoria para ajudá-lo a diagnosticar falhas nas suas aplicações monitorizadas. Para começar, clique na opção Falhas no menu de recursos De Insights de Aplicação localizado na secção Investigar.
Deve ver uma visão de ecrã completo que lhe mostre as tendências da taxa de falha para os seus pedidos, quantos deles estão a falhar e quantos utilizadores são impactados. À direita, você verá algumas das distribuições mais úteis específicas para a operação falhada selecionada, incluindo os três principais códigos de resposta, os três principais tipos de exceção, e os três principais tipos de dependência falhado.

![Visão de triagem de falhas (separador de operações)](./media/asp-net-exceptions/failures0719.png)

Num único clique, pode então rever amostras representativas para cada um destes subconjuntos de operações. Em particular, para diagnosticar exceções, pode clicar na contagem de uma determinada exceção a apresentar com o separador de detalhes de transação de ponta a ponta, como este:

![Separador de detalhes de transação de ponta a ponta](./media/asp-net-exceptions/end-to-end.png)

**Em alternativa,** em vez de olhar para exceções de uma operação de falha específica, pode começar a partir da visão geral das exceções, mudando para o separador Exceções no topo. Aqui pode ver todas as exceções recolhidas para a sua aplicação monitorizada.

*Não há exceções? Ver [exceções de captura.](#exceptions)*


## <a name="custom-tracing-and-log-data"></a>Dados de rastreio e registo personalizados
Para obter dados de diagnóstico específicos da sua aplicação, pode inserir código para enviar os seus próprios dados de telemetria. Isto apresentado em pesquisa de diagnóstico ao lado do pedido, vista da página e outros dados recolhidos automaticamente.

Tem várias opções:

* [TrackEvent é](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) normalmente usado para monitorizar padrões de utilização, mas os dados que envia também aparecem em Eventos Personalizados em pesquisa de diagnóstico. Os eventos são nomeados e podem transportar propriedades de cordas e métricas numéricas nas quais pode [filtrar as suas pesquisas de diagnóstico](../../azure-monitor/app/diagnostic-search.md).
* [TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) permite-lhe enviar dados mais longos, tais como informações postais.
* [TrackException()](#exceptions) envia vestígios de pilhas. [Mais sobre exceções.](#exceptions)
* Se já utilizar uma estrutura de registo como log4Net ou NLog, pode [capturar esses registos](asp-net-trace-logs.md) e vê-los em pesquisa de diagnóstico ao lado de dados de pedido e de exceção.

Para ver estes eventos, abra [a Pesquisa](../../azure-monitor/app/diagnostic-search.md) a partir do menu esquerdo, selecione os tipos de **eventos**de menu suspensos , e, em seguida, escolha Evento Personalizado, Trace ou Exceção.

![Explorar](./media/asp-net-exceptions/customevents.png)

> [!NOTE]
> Se a sua aplicação gerar uma grande quantidade de telemetria, o módulo de amostragem adaptável irá reduzir automaticamente o volume que é enviado para o portal, enviando apenas uma fração representativa dos eventos. Os eventos que fazem parte da mesma operação serão selecionados ou desselecionados em grupo, para que possa navegar entre eventos relacionados. [Saiba mais sobre amostragem.](../../azure-monitor/app/sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Como ver dados post a pedido
Os detalhes do pedido não incluem os dados enviados para a sua aplicação numa chamada POST. Para que estes dados sejam comunicados:

* [Instale o SDK](../../azure-monitor/app/asp-net.md) no seu projeto de candidatura.
* Insira o código na sua aplicação para ligar para [Microsoft.ApplicationInsights.TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace). Envie os dados do POST no parâmetro da mensagem. Há um limite para o tamanho permitido, por isso deve tentar enviar apenas os dados essenciais.
* Quando investigar um pedido falhado, encontre os vestígios associados.

## <a name="exceptions"></a>Captura de exceções e dados de diagnóstico relacionados
No início, não verá no portal todas as exceções que causam falhas na sua aplicação. Verá quaisquer exceções ao navegador (se estiver a utilizar o [JavaScript SDK](../../azure-monitor/app/javascript.md) nas suas páginas web). Mas a maioria das exceções do servidor são apanhadas pelo IIS e você tem que escrever um pouco de código para vê-los.

Pode:

* **Faça log exceções explicitamente** inserindo código em manipuladores de exceção para reportar as exceções.
* **Capture automaticamente exceções** configurando o seu quadro ASP.NET. As adições necessárias são diferentes para diferentes tipos de enquadramento.

## <a name="reporting-exceptions-explicitly"></a>Comunicação de exceções explicitamente
A forma mais simples é inserir uma chamada para trackException() num manipulador de exceção.

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

As propriedades e medições são opcionais, mas são úteis para [filtrar e adicionar](../../azure-monitor/app/diagnostic-search.md) informações extra. Por exemplo, se tiver uma aplicação que pode executar vários jogos, poderá encontrar todos os relatórios de exceção relacionados com um determinado jogo. Pode adicionar quantos itens quiser a cada dicionário.

## <a name="browser-exceptions"></a>Exceções de browser
A maioria das exceções do navegador são reportadas.

Se a sua página web incluir ficheiros de script saem de redes de entrega de conteúdos ou outros domínios, certifique-se de que a sua etiqueta de script tem o atributo ```crossorigin="anonymous"```, e que o servidor envia [cabeçalhos CORS](https://enable-cors.org/). Isto permitir-lhe-á obter um traço de pilha e detalhes para exceções javaScript não manuseadas a partir destes recursos.

## <a name="reuse-your-telemetry-client"></a>Reutilizar o seu cliente de telemetria

> [!NOTE]
> Recomenda-se que o TelemettryClient seja instantaneamente utilizado uma vez e reutilizado ao longo da vida de uma aplicação.

Abaixo está um exemplo usando telemetriaClient corretamente.

```csharp
public class GoodController : ApiController
{
    // OK
    private static readonly TelemetryClient telemetryClient;

    static GoodController()
    {
        telemetryClient = new TelemetryClient();
    }
}
```


## <a name="web-forms"></a>Formulários web
Para formulários web, o Módulo HTTP será capaz de recolher as exceções quando não houver redirecionamentos configurados com Erros Personalizados.

Mas se tiver redirecionamentos ativos, adicione as seguintes linhas à função Application_Error em Global.asax.cs. (Adicione um ficheiro Global.asax se ainda não tiver um.)

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
Começando pela versão 2.6 do Web SDK (beta3 e mais tarde), a Application Insights recolhe automaticamente exceções não manipuladas lançadas nos métodos dos controladores MVC 5+. Se tiver adicionado anteriormente um manipulador personalizado para rastrear tais exceções (conforme descrito nos seguintes exemplos), poderá removê-lo para evitar o duplo rastreio de exceções.

Há uma série de casos que os filtros de exceção não conseguem lidar. Por exemplo:

* Exceções lançadas de construtores controladores.
* Exceções lançadas dos manipuladores de mensagens.
* Exceções lançadas durante o encaminhamento.
* Exceções lançadas durante a serialização do conteúdo de resposta.
* Exceção lançada durante o arranque da aplicação.
* Exceção lançada em tarefas de fundo.

Todas as exceções *tratadas* por aplicação ainda precisam de ser rastreadas manualmente.
Exceções não tratadas originárias de controladores normalmente resultam em 500 respostas "Erro interno do servidor". Se tal resposta for construída manualmente em resultado de uma exceção manipulada (ou nenhuma exceção) é rastreada na telemetria de pedido correspondente com `ResultCode` 500, no entanto, o Application Insights SDK não consegue rastrear a correspondente exceção.

### <a name="prior-versions-support"></a>Suporte de versões anteriores
Se utilizar o MVC 4 (e anterior) do Web SDK 2.5 (e anterior), consulte os seguintes exemplos para rastrear exceções.

Se a configuração [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) for `Off`, então estarão disponíveis exceções para que o [Módulo HTTP](https://msdn.microsoft.com/library/ms178468.aspx) possa recolher. No entanto, se for `RemoteOnly` (predefinido), ou `On`, então a exceção será apurada e não estará disponível para que os Insights de Aplicação recolham automaticamente. Pode corrigir isso substituindo a [classe System.Web.Mvc.HandleErrorAttribute,](https://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx)e aplicando a classe overridden como mostrado para as diferentes versões MVC abaixo[(fonte GitHub](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

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
Substitua o atributo HandleError pelo seu novo atributo nos seus controladores.

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
Registe `AiHandleErrorAttribute` como filtro global em Global.asax.cs:

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
Registe o AiHandleErrorAttribute como filtro global em FilterConfig.cs:

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
Começando pela versão 2.6 do Web SDK (beta3 e mais tarde), a Application Insights recolhe exceções não manipuladas lançadas nos métodos do controlador automaticamente para webAPI 2+. Se tiver adicionado anteriormente um manipulador personalizado para rastrear tais exceções (conforme descrito nos seguintes exemplos), poderá removê-lo para evitar o duplo rastreio de exceções.

Há uma série de casos que os filtros de exceção não conseguem lidar. Por exemplo:

* Exceções lançadas de construtores controladores.
* Exceções lançadas dos manipuladores de mensagens.
* Exceções lançadas durante o encaminhamento.
* Exceções lançadas durante a serialização do conteúdo de resposta.
* Exceção lançada durante o arranque da aplicação.
* Exceção lançada em tarefas de fundo.

Todas as exceções *tratadas* por aplicação ainda precisam de ser rastreadas manualmente.
Exceções não tratadas originárias de controladores normalmente resultam em 500 respostas "Erro interno do servidor". Se essa resposta for construída manualmente em resultado de uma exceção manipulada (ou nenhuma exceção) é rastreada numa telemetria de pedido correspondente com `ResultCode` 500, no entanto, o SDK de Insights de Aplicação não consegue rastrear a correspondente exceção.

### <a name="prior-versions-support"></a>Suporte de versões anteriores
Se utilizar webAPI 1 (e anterior) de Web Insights Web SDK 2.5 (e anterior), consulte os seguintes exemplos para rastrear exceções.

#### <a name="web-api-1x"></a>Web API 1.x
Sistema de sobreposição.web.http.filters.ExceptionFilterAttribute:

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

Pode adicionar este atributo overridden a controladores específicos, ou adicioná-lo à configuração global do filtro na classe WebApiConfig:

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

Adicione isto aos serviços na WebApiConfig:

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

Como alternativas, poderia:

1. Substitua o único ExceptionHandler por uma implementação personalizada do IExceptionHandler. Isto só é chamado quando o quadro ainda é capaz de escolher qual mensagem de resposta enviar (não quando a ligação é abortada, por exemplo)
2. Filtros de exceção (conforme descrito na secção dos controladores Web API 1.x acima) - não chamados em todos os casos.

## <a name="wcf"></a>WCF
Adicione uma classe que alarga o Atributo e implementa iErrorHandler e IServiceBehavior.

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
Se tiver instalado o Agente de Insights de [Aplicação](../../azure-monitor/app/monitor-performance-live-website-now.md) no seu servidor, pode obter um gráfico da taxa de exceções, medida por .NET. Isto inclui exceções .NET manuseadas e não manuseadas.

Abra um separador Metric Explorer, adicione um novo gráfico e selecione taxa de **exceção,** listada em Contadores de Desempenho.

O quadro .NET calcula a taxa contando o número de exceções num intervalo e dividindo-se pelo comprimento do intervalo.

Isto é diferente da contagem de "Exceções" calculada pelo portal Application Insights contando relatórios TrackException. Os intervalos de amostragem são diferentes, e o SDK não envia relatórios TrackException para todas as exceções manuseadas e não manipuladas.

## <a name="next-steps"></a>Passos seguintes
* [Monitor REST, SQL, e outras chamadas para dependências](../../azure-monitor/app/asp-net-dependencies.md)
* [Monitorize os tempos de carregamento da página, exceções ao navegador e chamadas AJAX](../../azure-monitor/app/javascript.md)
* [Monitorize contadores de desempenho](../../azure-monitor/app/performance-counters.md)
