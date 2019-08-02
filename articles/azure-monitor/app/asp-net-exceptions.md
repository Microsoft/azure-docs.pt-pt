---
title: Diagnosticar falhas e exceções em aplicativos Web com o Aplicativo Azure insights | Microsoft Docs
description: Capture exceções de aplicativos ASP.NET juntamente com telemetria de solicitação.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d1e98390-3ce4-4d04-9351-144314a42aa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: mbullwin
ms.openlocfilehash: c8d46ddc834cb12aa63720673c83d745ab53ab4d
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226884"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Diagnosticar exceções em seus aplicativos Web com Application Insights
As exceções em seu aplicativo Web em tempo real são relatadas pelo [Application insights](../../azure-monitor/app/app-insights-overview.md). Você pode correlacionar solicitações com falha com exceções e outros eventos no cliente e no servidor, para que você possa diagnosticar as causas rapidamente.

## <a name="set-up-exception-reporting"></a>Configurar relatório de exceções
* Para que as exceções sejam relatadas do aplicativo de servidor:
  * Aplicativos Web do Azure: Adicionar a [extensão de Application insights](../../azure-monitor/app/azure-web-apps.md)
  * VM do Azure e conjunto de dimensionamento de máquinas virtuais do Azure aplicativos hospedados pelo IIS: Adicionar a [extensão de monitoramento de aplicativo](../../azure-monitor/app/azure-vm-vmss-apps.md)
  * Instalar [Application insights SDK](../../azure-monitor/app/asp-net.md) no seu código do aplicativo ou
  * Servidores Web do IIS: Executar [agente de Application insights](../../azure-monitor/app/monitor-performance-live-website-now.md); or
  * Aplicativos Web Java: Instalar o [agente Java](../../azure-monitor/app/java-agent.md)
* Instale o [trecho de JavaScript](../../azure-monitor/app/javascript.md) em suas páginas da Web para capturar exceções de navegador.
* Em algumas estruturas de aplicativo ou com algumas configurações, você precisa executar algumas etapas adicionais para capturar mais exceções:
  * [Formulários da Web](#web-forms)
  * [MVC](#mvc)
  * [API Web 1. *](#web-api-1x)
  * [API Web 2. *](#web-api-2x)
  * [WCF](#wcf)

## <a name="diagnosing-exceptions-using-visual-studio"></a>Diagnosticando exceções usando o Visual Studio
Abra a solução de aplicativo no Visual Studio para ajudar na depuração.

Execute o aplicativo, em seu servidor ou em seu computador de desenvolvimento usando F5.

Abra a janela de pesquisa Application Insights no Visual Studio e defina-a para exibir eventos de seu aplicativo. Enquanto estiver Depurando, você pode fazer isso apenas clicando no botão Application Insights.

![Clique com o botão direito do mouse no projeto e escolha Application Insights, abrir.](./media/asp-net-exceptions/34.png)

Observe que você pode filtrar o relatório para mostrar apenas as exceções.

*Nenhuma exceção aparecendo? Consulte [capturar exceções](#exceptions).*

Clique em um relatório de exceções para mostrar seu rastreamento de pilha.
Clique em uma referência de linha no rastreamento de pilha para abrir o arquivo de código relevante.

No código, observe que CodeLens mostra dados sobre as exceções:

![Notificação de exceções do CodeLens.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Diagnosticar falhas usando o portal do Azure
O Application Insights vem com uma experiência de APM organizada para ajudá-lo a diagnosticar falhas em seus aplicativos monitorados. Para iniciar, clique na opção falhas no menu Application Insights recurso localizado na seção investigar.
Você deve ver uma exibição de tela inteira que mostra as tendências de taxa de falha para suas solicitações, quantos deles estão falhando e quantos usuários são afetados. À direita, você verá algumas das distribuições mais úteis específicas para a operação com falha selecionada, incluindo os três principais códigos de resposta, os três principais tipos de exceção e os três principais tipos de dependência com falha.

![Exibição de triagem de falhas (guia operações)](./media/asp-net-exceptions/failures0719.png)

Em um único clique, você pode examinar amostras representativas para cada um desses subconjuntos de operações. Em particular, para diagnosticar exceções, você pode clicar na contagem de uma exceção específica a ser apresentada com a guia detalhes da transação de ponta a ponta, como esta:

![Guia detalhes da transação de ponta a ponta](./media/asp-net-exceptions/end-to-end.png)

**Como alternativa,** em vez de examinar as exceções de uma operação com falha específica, você pode iniciar na exibição geral de exceções, alternando para a guia exceções na parte superior. Aqui você pode ver todas as exceções coletadas para seu aplicativo monitorado.

*Nenhuma exceção aparecendo? Consulte [capturar exceções](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Rastreamento personalizado e dados de log
Para obter dados de diagnóstico específicos para seu aplicativo, você pode inserir o código para enviar seus próprios dados de telemetria. Isso é exibido na pesquisa de diagnóstico junto com a solicitação, a exibição de página e outros dados coletados automaticamente.

Você tem várias opções:

* [TrackEvent ()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) normalmente é usado para monitorar padrões de uso, mas os dados que ele envia também aparecem em eventos personalizados na pesquisa de diagnóstico. Os eventos são nomeados e podem conter Propriedades de cadeia de caracteres e métricas numéricas nas quais você pode [filtrar as pesquisas de diagnóstico](../../azure-monitor/app/diagnostic-search.md).
* [TrackTrace ()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) permite que você envie dados mais longos, como informações de postagem.
* [Trackexception ()](#exceptions) envia rastreamentos de pilha. [Mais sobre exceções](#exceptions).
* Se você já usa uma estrutura de registro em log como Log4Net ou NLog, você pode [capturar esses logs](asp-net-trace-logs.md) e vê-los na pesquisa de diagnóstico junto com os dados de solicitação e exceção.

Para ver esses eventos, abra [Pesquisar](../../azure-monitor/app/diagnostic-search.md) no menu à esquerda, selecione os **tipos de evento**de menu suspenso e, em seguida, escolha evento personalizado, rastreamento ou exceção.

![Explorar](./media/asp-net-exceptions/customevents.png)

> [!NOTE]
> Se a sua aplicação gerar uma grande quantidade de telemetria, o módulo de amostragem adaptável irá reduzir automaticamente o volume que é enviado para o portal, enviando apenas uma fração representativa dos eventos. Os eventos que fazem parte da mesma operação serão selecionados ou desmarcados como um grupo, para que você possa navegar entre os eventos relacionados. [Saiba mais sobre amostragem.](../../azure-monitor/app/sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Como ver dados de POSTAgem de solicitação
Os detalhes da solicitação não incluem os dados enviados ao seu aplicativo em uma chamada POST. Para que esses dados sejam relatados:

* [Instale o SDK](../../azure-monitor/app/asp-net.md) em seu projeto de aplicativo.
* Insira o código em seu aplicativo para chamar [Microsoft. ApplicationInsights. TrackTrace ()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace). Envie os dados de POSTAgem no parâmetro Message. Há um limite para o tamanho permitido, portanto, você deve tentar enviar apenas os dados essenciais.
* Ao investigar uma solicitação com falha, localize os rastreamentos associados.

## <a name="exceptions"></a>Capturando exceções e dados de diagnóstico relacionados
A princípio, você não verá no portal todas as exceções que causam falhas em seu aplicativo. Você verá quaisquer exceções de navegador (se estiver usando o [SDK do JavaScript](../../azure-monitor/app/javascript.md) em suas páginas da Web). Mas a maioria das exceções de servidor é detectada pelo IIS e você precisa escrever um pouco de código para vê-las.

Pode:

* **Registre exceções explicitamente** inserindo código nos manipuladores de exceção para relatar as exceções.
* **Capturar exceções automaticamente** Configurando sua estrutura ASP.net. As adições necessárias são diferentes para tipos diferentes de estrutura.

## <a name="reporting-exceptions-explicitly"></a>Relatando exceções explicitamente
A maneira mais simples é inserir uma chamada para Trackexception () em um manipulador de exceção.

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

Os parâmetros de propriedades e medidas são opcionais, mas são úteis para [Filtrar e adicionar](../../azure-monitor/app/diagnostic-search.md) informações extras. Por exemplo, se você tiver um aplicativo que possa executar vários jogos, poderá encontrar todos os relatórios de exceção relacionados a um determinado jogo. Você pode adicionar quantos itens desejar para cada dicionário.

## <a name="browser-exceptions"></a>Exceções de browser
A maioria das exceções de navegador é relatada.

Se sua página da Web incluir arquivos de script de redes de distribuição de conteúdo ou outros domínios, verifique se a ```crossorigin="anonymous"```marca do script tem o atributo e se o servidor envia [cabeçalhos CORS](https://enable-cors.org/). Isso permitirá que você obtenha um rastreamento de pilha e detalhes para exceções de JavaScript sem tratamento desses recursos.

## <a name="reuse-your-telemetry-client"></a>Reutilizar o cliente de telemetria

> [!NOTE]
> O TelemetryClient é recomendado para ser instanciado uma vez e reutilizado durante toda a vida útil de um aplicativo.

Veja abaixo um exemplo que usa TelemetryClient corretamente.

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


## <a name="web-forms"></a>Formulários da Web
Para formulários da Web, o módulo HTTP poderá coletar as exceções quando não houver redirecionamentos configurados com customErrors.

Mas se você tiver redirecionamentos ativos, adicione as seguintes linhas à função Application_Error em Global.asax.cs. (Adicione um arquivo global. asax se você ainda não tiver um.)

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
A partir do Application Insights Web SDK versão 2,6 (beta3 e posterior), Application Insights coleta exceções sem tratamento lançadas nos métodos de controladores MVC 5 + automaticamente. Se você tiver adicionado anteriormente um manipulador personalizado para controlar essas exceções (conforme descrito nos exemplos a seguir), você poderá removê-lo para evitar o controle duplo de exceções.

Há vários casos que os filtros de exceção não podem manipular. Por exemplo:

* Exceções geradas de construtores do controlador.
* Exceções geradas por manipuladores de mensagens.
* Exceções geradas durante o roteamento.
* Exceções geradas durante a serialização do conteúdo da resposta.
* Exceção lançada durante a inicialização do aplicativo.
* Exceção lançada em tarefas em segundo plano.

Todas as ** exceções manipuladas pelo aplicativo ainda precisam ser rastreadas manualmente.
Exceções sem tratamento originadas de controladores normalmente resultam em uma resposta de 500 "erro interno do servidor". Se essa resposta for construída manualmente como resultado da exceção manipulada (ou nenhuma exceção), ela será controlada na telemetria de solicitação correspondente `ResultCode` com 500, no entanto Application insights SDK não consegue rastrear a exceção correspondente.

### <a name="prior-versions-support"></a>Suporte a versões anteriores
Se você usar o MVC 4 (e anterior) do Application Insights Web SDK 2,5 (e anterior), consulte os exemplos a seguir para rastrear exceções.

Se a [](https://msdn.microsoft.com/library/h0hfz6fc.aspx) configuração de customErrors for `Off`, as exceções estarão disponíveis para o [módulo http](https://msdn.microsoft.com/library/ms178468.aspx) coletar. No entanto, se `RemoteOnly` for (padrão), `On`ou, a exceção será apagada e não estará disponível para Application insights coletar automaticamente. Você pode corrigir isso substituindo a [classe System. Web. Mvc. HandleErrorAttribute](https://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx)e aplicando a classe substituída, conforme mostrado nas diferentes versões do MVC abaixo ([origem do GitHub](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

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
Substitua o atributo HandleError pelo novo atributo em seus controladores.

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
Registre `AiHandleErrorAttribute` -se como um filtro global no global.asax.cs:

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
Registrar AiHandleErrorAttribute como um filtro global no FilterConfig.cs:

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
A partir do Application Insights Web SDK versão 2,6 (beta3 e posterior), Application Insights coleta exceções sem tratamento lançadas nos métodos do controlador automaticamente para WebAPI 2 +. Se você tiver adicionado anteriormente um manipulador personalizado para controlar essas exceções (conforme descrito nos exemplos a seguir), você poderá removê-lo para evitar o controle duplo de exceções.

Há vários casos que os filtros de exceção não podem manipular. Por exemplo:

* Exceções geradas de construtores do controlador.
* Exceções geradas por manipuladores de mensagens.
* Exceções geradas durante o roteamento.
* Exceções geradas durante a serialização do conteúdo da resposta.
* Exceção lançada durante a inicialização do aplicativo.
* Exceção lançada em tarefas em segundo plano.

Todas as ** exceções manipuladas pelo aplicativo ainda precisam ser rastreadas manualmente.
Exceções sem tratamento originadas de controladores normalmente resultam em uma resposta de 500 "erro interno do servidor". Se essa resposta for construída manualmente como resultado da exceção manipulada (ou nenhuma exceção), ela será rastreada em uma telemetria de solicitação `ResultCode` correspondente com 500, no entanto Application insights SDK não consegue rastrear a exceção correspondente.

### <a name="prior-versions-support"></a>Suporte a versões anteriores
Se você usar o WebAPI 1 (e anterior) do Application Insights Web SDK 2,5 (e anterior), consulte os exemplos a seguir para rastrear as exceções.

#### <a name="web-api-1x"></a>API Web 1. x
Substitua System. Web. http. Filters. ExceptionFilterAttribute:

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

Você pode adicionar esse atributo substituído a controladores específicos ou adicioná-lo à configuração de filtro global na classe WebApiConfig:

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

#### <a name="web-api-2x"></a>API Web 2. x
Adicione uma implementação de IExceptionLogger:

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

Adicione isso aos serviços no WebApiConfig:

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

Como alternativas, você pode:

1. Substitua o único ExceptionHandler por uma implementação personalizada de IExceptionHandler. Isso é chamado apenas quando a estrutura ainda é capaz de escolher qual mensagem de resposta enviar (não quando a conexão é anulada por instância)
2. Filtros de exceção (conforme descrito na seção sobre controladores da API Web 1. x acima) – não é chamado em todos os casos.

## <a name="wcf"></a>WCF
Adicione uma classe que estenda o atributo e implemente IErrorHandler e IServiceBehavior.

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
Se você [instalou o agente de Application insights](../../azure-monitor/app/monitor-performance-live-website-now.md) em seu servidor, poderá obter um gráfico da taxa de exceções, medido pelo .net. Isso inclui exceções de .NET tratadas e sem tratamento.

Abra uma guia do Gerenciador de métricas, adicione um novo gráfico e selecione **taxa de exceção**, listada em contadores de desempenho.

O .NET Framework calcula a taxa contando o número de exceções em um intervalo e dividindo pelo comprimento do intervalo.

Isso é diferente da contagem de ' exceções ' calculada pelo portal de Application Insights contando relatórios Trackexception. Os intervalos de amostragem são diferentes, e o SDK não envia relatórios Trackexception para todas as exceções tratadas e não tratadas.

## <a name="next-steps"></a>Passos seguintes
* [Monitorar REST, SQL e outras chamadas para dependências](../../azure-monitor/app/asp-net-dependencies.md)
* [Monitorar tempos de carregamento de página, exceções de navegador e chamadas AJAX](../../azure-monitor/app/javascript.md)
* [Monitorar contadores de desempenho](../../azure-monitor/app/performance-counters.md)