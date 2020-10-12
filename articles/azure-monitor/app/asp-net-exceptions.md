---
title: Diagnosticar falhas e exceções com a Azure Application Insights
description: Capture exceções de aplicações ASP.NET juntamente com a telemetria de pedido.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 07/11/2019
ms.openlocfilehash: 36e916eabfca8e997fc3d46ff10f6201203457cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88936508"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Diagnosticar exceções nas aplicações Web com o Application Insights
As exceções na sua aplicação web ao vivo são reportadas pela [Application Insights](./app-insights-overview.md). Pode correlacionar pedidos falhados com exceções e outros eventos tanto no cliente como no servidor, para que possa diagnosticar rapidamente as causas.

## <a name="set-up-exception-reporting"></a>Configurar relatórios de exceções
* Para ter exceções reportadas a partir da sua aplicação de servidor:
  * Aplicativos web Azure: Adicionar a [extensão de Insights de Aplicação](./azure-web-apps.md)
  * Azure VM e Azure série de máquinas virtuais conjunto aplicativos hospedados no IIS: Adicionar a [extensão de monitorização de aplicações](./azure-vm-vmss-apps.md)
  * Instale [o App Insights SDK](./asp-net.md) no seu código de aplicação, ou
  * Servidores web IIS: Executar Agente [de Insights de Aplicações;](./monitor-performance-live-website-now.md) ou
  * Aplicativos java web: Ativar o [agente Java](./java-in-process-agent.md)
* Instale o [snippet JavaScript](./javascript.md) nas suas páginas web para apanhar exceções ao navegador.
* Em alguns quadros de aplicação ou com algumas configurações, você precisa tomar alguns passos adicionais para obter mais exceções:
  * [Formulários web](#web-forms)
  * [MVC](#mvc)
  * [Web API 1.*](#web-api-1x)
  * [Web API 2.*](#web-api-2x)
  * [WCF](#wcf)

  Este artigo está especificamente focado em aplicações .NET Framework numa perspetiva de exemplo de código. Alguns dos métodos que funcionam para o Quadro .NET são obsoletos no .NET Core SDK. Consulte a [documentação .NET Core SDK](./asp-net-core.md) se tiver uma aplicação .NET Core.

## <a name="diagnosing-exceptions-using-visual-studio"></a>Diagnosticar exceções usando o Visual Studio
Abra a solução de aplicativo no Visual Studio para ajudar na depuragem.

Execute a aplicação, seja no seu servidor ou na sua máquina de desenvolvimento utilizando F5.

Abra a janela de pesquisa de insights de aplicação no Estúdio Visual e desabropei-a para exibir eventos a partir da sua aplicação. Enquanto está a depurar, pode fazê-lo basta clicar no botão 'Insights de Aplicação'.

![Clique com o botão direito no projeto e escolha Application Insights, Open.](./media/asp-net-exceptions/34.png)

Note que pode filtrar o relatório para mostrar apenas exceções.

*Sem exceções? Ver [exceções de captura](#exceptions).*

Clique num relatório de exceção para mostrar o seu traço de pilha.
Clique numa referência de linha no traço da pilha, para abrir o ficheiro de código relevante.

No código, note que o CodeLens mostra dados sobre as exceções:

![CodeLens notificação de exceções.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Diagnóstico de falhas usando o portal Azure
O Application Insights vem com uma experiência APM com curadoria para ajudá-lo a diagnosticar falhas nas suas aplicações monitorizadas. Para começar, clique na opção Falhas no menu de recursos 'Insights de Aplicação' localizado na secção Investigar.
Deve ver uma visão de ecrã completo que lhe mostre as tendências de taxa de falha para os seus pedidos, quantos deles estão a falhar e quantos utilizadores são impactados. À direita, você verá algumas das distribuições mais úteis específicas para a operação falhada selecionada, incluindo os três principais códigos de resposta, três tipos de exceção de topo e três tipos de dependência falhados.

![Visão de triagem de falhas (separador de operações)](./media/asp-net-exceptions/failures0719.png)

Num único clique, pode então rever amostras representativas de cada um destes subconjuntos de operações. Em particular, para diagnosticar exceções, pode clicar na contagem de uma exceção particular para ser apresentado com o separador detalhes de transação de ponta a ponta, como este:

![Separador de detalhes de transações de ponta a ponta](./media/asp-net-exceptions/end-to-end.png)

**Em alternativa,** em vez de olhar para exceções de uma operação de falha específica, pode começar da visão geral das exceções, mudando para o separador Exceções no topo. Aqui pode ver todas as exceções recolhidas para a sua aplicação monitorizada.

*Sem exceções? Ver [exceções de captura](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Dados de rastreio e registo personalizados
Para obter dados de diagnóstico específicos da sua app, pode inserir código para enviar os seus próprios dados de telemetria. Isto apresentado em pesquisa de diagnóstico ao lado do pedido, visualização da página e outros dados recolhidos automaticamente.

Tem várias opções:

* [TrackEvent()](./api-custom-events-metrics.md#trackevent) é normalmente usado para monitorizar padrões de uso, mas os dados que envia também aparecem em Eventos Personalizados na pesquisa de diagnóstico. Os eventos são nomeados, e podem transportar propriedades de cordas e métricas numéricas nas quais você pode [filtrar as suas pesquisas de diagnóstico.](./diagnostic-search.md)
* [TrackTrace()](./api-custom-events-metrics.md#tracktrace) permite-lhe enviar dados mais longos, como informações POST.
* [TrackException()](#exceptions) envia traços de pilha. [Mais sobre exceções.](#exceptions)
* Se já utilizar uma estrutura de registo como Log4Net ou NLog, pode [capturar esses registos](asp-net-trace-logs.md) e vê-los na pesquisa de diagnóstico juntamente com os dados de pedido e exceção.

Para ver estes eventos, abra [a pesquisa](./diagnostic-search.md) a partir do menu esquerdo, selecione os **tipos**de eventos do menu suspenso e, em seguida, escolha Evento Personalizado, Trace ou Exceção.

![Pormenorização](./media/asp-net-exceptions/customevents.png)

> [!NOTE]
> Se a sua aplicação gerar uma grande quantidade de telemetria, o módulo de amostragem adaptável irá reduzir automaticamente o volume que é enviado para o portal, enviando apenas uma fração representativa dos eventos. Eventos que fazem parte da mesma operação serão selecionados ou desescolados como um grupo, para que possa navegar entre eventos relacionados. [Aprenda a provar.](./sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Como ver pedido dados POST
Os dados do pedido não incluem os dados enviados para a sua aplicação numa chamada POST. Para ter estes dados relatados:

* [Instale o SDK](./asp-net.md) no seu projeto de candidatura.
* Insira código na sua aplicação para ligar para [Microsoft.ApplicationInsights.TrackTrace()](./api-custom-events-metrics.md#tracktrace). Envie os dados POST no parâmetro da mensagem. Existe um limite para o tamanho permitido, por isso deve tentar enviar apenas os dados essenciais.
* Quando investigar um pedido falhado, encontre os vestígios associados.

## <a name="capturing-exceptions-and-related-diagnostic-data"></a><a name="exceptions"></a> Captura de exceções e dados de diagnóstico conexos
No início, não verá no portal todas as exceções que causam falhas na sua app. Verá quaisquer exceções ao navegador (se estiver a utilizar o [SDK JavaScript](./javascript.md) nas suas páginas web). Mas a maioria das exceções ao servidor são apanhadas pelo IIS e tens de escrever um código para as veres.

Pode:

* **Registar exceções explicitamente** inserindo código em manipuladores de exceções para reportar as exceções.
* **Capture as exceções automaticamente** configurando a sua estrutura de ASP.NET. As adições necessárias são diferentes para diferentes tipos de enquadramento.

## <a name="reporting-exceptions-explicitly"></a>Notificação de exceções explicitamente
A forma mais simples é inserir uma chamada para TrackException num manipulador de exceções.

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

Os parâmetros de propriedades e medições são opcionais, mas são úteis para [filtrar e adicionar](./diagnostic-search.md) informações extra. Por exemplo, se tiveres uma aplicação que pode executar vários jogos, podes encontrar todos os relatórios de exceção relacionados com um determinado jogo. Pode adicionar quantos itens quiser a cada dicionário.

## <a name="browser-exceptions"></a>Exceções de browser
A maioria das exceções ao navegador são reportadas.

Se a sua página web incluir ficheiros de scripts a partir de redes de entrega de conteúdos ou outros domínios, certifique-se de que a sua etiqueta de script tem o atributo ```crossorigin="anonymous"``` , e que o servidor envia [cabeçalhos CORS](https://enable-cors.org/). Isto permitir-lhe-á obter um traço de stack e detalhes para exceções javaScript não manuseadas destes recursos.

## <a name="reuse-your-telemetry-client"></a>Reutilizar o seu cliente de telemetria

> [!NOTE]
> Recomenda-se que o TelemetriaClient seja instantâneo uma vez e reutilizado ao longo da vida de uma aplicação.

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
Para formulários web, o Módulo HTTP será capaz de recolher as exceções quando não houver redirecionamentos configurados com Personalizadores.

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
Começando com a versão 2.6 da Web SDK da Aplicação Insights (beta3 e posteriormente), o Application Insights recolhe automaticamente exceções não manipuladas lançadas nos métodos dos controladores MVC 5+. Se tiver adicionado previamente um manipulador personalizado para rastrear tais exceções (como descrito nos exemplos seguintes), pode removê-lo para evitar o duplo rastreio de exceções.

Há uma série de casos que os filtros de exceção não conseguem lidar. Por exemplo:

* Exceções emitidas a partir dos construtores de controladores.
* Exceções emitidas a partir dos processadores de mensagens.
* Exceções emitidas durante o encaminhamento.
* Exceções emitidas durante a serialização de conteúdos de resposta.
* Exceção emitida durante o arranque da aplicação.
* Exceção emitida em tarefas em segundo plano.

Todas as exceções *tratadas* por aplicação ainda precisam de ser rastreadas manualmente.
As exceções não tratadas originárias dos controladores resultam normalmente numa resposta de 500 "Erro interno do servidor". Se tal resposta for construída manualmente como resultado de uma exceção manuseada (ou nenhuma exceção) é rastreada na telemetria de pedido correspondente com `ResultCode` 500, no entanto a Application Insights SDK não consegue acompanhar a respetiva exceção.

### <a name="prior-versions-support"></a>Suporte de versões anteriores
Se utilizar O MVC 4 (e anterior) da Aplicação Insights Web SDK 2.5 (e anterior), consulte os seguintes exemplos para rastrear exceções.

Se a configuração [CustomErrors](/previous-versions/dotnet/netframework-4.0/h0hfz6fc(v=vs.100)) for `Off` , então estarão disponíveis exceções para o [Módulo HTTP](/previous-versions/dotnet/netframework-3.0/ms178468(v=vs.85)) recolher. No entanto, se for `RemoteOnly` (predefinitivo), `On` ou, então, a exceção será apurada e não disponível para os Insights de Aplicação recolherem automaticamente. Pode corrigi-lo sobressaíndo a [classe System.Web.Mvc.HandleErrorAttribute](/dotnet/api/system.web.mvc.handleerrorattribute?view=aspnet-mvc-5.2), e aplicando a classe overridden como mostrado para as diferentes versões MVC abaixo[(fonte GitHub](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

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

[Amostra](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Registe-se `AiHandleErrorAttribute` como filtro global em Global.asax.cs:

```csharp
    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...
```

[Amostra](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5
Registe-se AiHandleErrorAttribute como um filtro global em FilterConfig.cs:

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

[Amostra](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>API Web
Começando com a versão 2.6 da Web SDK da Aplicação Insights Web SDK (beta3 e posteriormente), a Application Insights recolhe exceções não manipuladas lançadas nos métodos do controlador automaticamente para o WebAPI 2+. Se tiver adicionado previamente um manipulador personalizado para rastrear tais exceções (como descrito nos exemplos seguintes), pode removê-lo para evitar o duplo rastreio de exceções.

Há uma série de casos que os filtros de exceção não conseguem lidar. Por exemplo:

* Exceções emitidas a partir dos construtores de controladores.
* Exceções emitidas a partir dos processadores de mensagens.
* Exceções emitidas durante o encaminhamento.
* Exceções emitidas durante a serialização de conteúdos de resposta.
* Exceção emitida durante o arranque da aplicação.
* Exceção emitida em tarefas em segundo plano.

Todas as exceções *tratadas* por aplicação ainda precisam de ser rastreadas manualmente.
As exceções não tratadas originárias dos controladores resultam normalmente numa resposta de 500 "Erro interno do servidor". Se tal resposta for construída manualmente como resultado de uma exceção manuseada (ou nenhuma exceção) é rastreada numa telemetria de pedido correspondente com `ResultCode` 500, no entanto a Application Insights SDK não consegue acompanhar a respetiva exceção.

### <a name="prior-versions-support"></a>Suporte de versões anteriores
Se utilizar o WebAPI 1 (e anterior) da Aplicação Insights Web SDK 2.5 (e anterior), consulte os seguintes exemplos para rastrear exceções.

#### <a name="web-api-1x"></a>Web API 1.x
Override System.Web.Http.Filters.ExceptionFilterAttribute:

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

Pode adicionar este atributo overridden a controladores específicos ou adicioná-lo à configuração global do filtro na classe WebApiConfig:

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

[Amostra](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

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

[Amostra](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Como alternativas, poderia:

1. Substitua o único ExceptionHandler por uma implementação personalizada do IExceptionHandler. Isto só é chamado quando o quadro ainda é capaz de escolher qual mensagem de resposta enviar (não quando a ligação é abortada, por exemplo)
2. Filtros de exceção (conforme descrito na secção nos controladores Web API 1.x acima) - não chamados em todos os casos.

## <a name="wcf"></a>WCF
Adicione uma classe que estende o Atributo e implementa iErrorHandler e IServiceBehavior.

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

[Amostra](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Contadores de desempenho de exceção
Se [tiver instalado o Application Insights Agent](./monitor-performance-live-website-now.md) no seu servidor, pode obter um gráfico da taxa de exceções, medido por .NET. Isto inclui exceções manuseadas e não manipuladas .NET.

Abra um separador Metric Explorer, adicione um novo gráfico e selecione **taxa de exceção,** listada nos Contadores de Desempenho.

O quadro .NET calcula a taxa contando o número de exceções num intervalo e dividindo-se pelo comprimento do intervalo.

Isto é diferente da contagem de 'Excepções' calculada pelo portal Application Insights contando relatórios TrackException. Os intervalos de amostragem são diferentes, e o SDK não envia relatórios TrackException para todas as exceções manuseadas e não manipuladas.

## <a name="next-steps"></a>Passos seguintes
* [Monitor REST, SQL e outras chamadas para dependências](./asp-net-dependencies.md)
* [Monitorizar os tempos de carregamento da página, exceções ao navegador e chamadas AJAX](./javascript.md)
* [Monitorize balcões de desempenho](./performance-counters.md)

