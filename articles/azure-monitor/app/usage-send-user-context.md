---
title: IDs de contexto do utilizador para acompanhar a atividade - Insights de aplicação Azure
description: Acompanhe a forma como os utilizadores se movem através do seu serviço, atribuindo a cada um deles uma linha de ID única e persistente em Insights de Aplicação.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/03/2019
ms.reviewer: abgreg;mbullwin
ms.openlocfilehash: beb5a0f7ad3733aaf12b0880af4fba23a705a7e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670938"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Envie IDs de contexto do utilizador para permitir experiências de utilização em Insights de Aplicação Azure

## <a name="tracking-users"></a>Rastreio de utilizadores

Os Insights de Aplicação permitem-lhe monitorizar e rastrear os seus utilizadores através de um conjunto de ferramentas de utilização do produto:

- [Utilizadores, Sessões, Eventos](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
- [Funis](https://docs.microsoft.com/azure/application-insights/usage-funnels)
- [Retenção](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention) Cohorts
- [Livros](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

Para acompanhar o que um utilizador faz ao longo do tempo, o Application Insights necessita de um ID para cada utilizador ou sessão. Inclua as seguintes IDs em todos os eventos ou visualizações de página personalizadas.

- Utilizadores, Funis, Retenção e Cohorts: Incluir id do utilizador.
- Sessões: Incluir ID da sessão.

> [!NOTE]
> Este é um artigo avançado que descreve os passos manuais para rastrear a atividade do utilizador com insights de aplicação. Com muitas aplicações web **estes passos podem não ser necessários**, uma vez que os SDKs do lado do servidor predefinidos em conjunto com o [JavaScript SDK](../../azure-monitor/app/website-monitoring.md )do lado do cliente/navegador , são muitas vezes suficientes para rastrear automaticamente a atividade do utilizador. Se não configurar [a monitorização do lado do cliente](../../azure-monitor/app/website-monitoring.md ) para além do SDK do lado do servidor, faça isso primeiro e teste para ver se as ferramentas de análise do comportamento do utilizador estão a funcionar como esperado.

## <a name="choosing-user-ids"></a>Escolher iDs de utilizador

As iDs dos utilizadores devem persistir em todas as sessões do utilizador para acompanhar o funcionar dos utilizadores ao longo do tempo. Existem várias abordagens para persistir a identificação.

- Uma definição de utilizador que já tem ao seu serviço.
- Se o serviço tiver acesso a um navegador, pode passar ao navegador um cookie com um ID no mesmo. O ID irá persistir enquanto o cookie permanecer no navegador do utilizador.
- Se necessário, pode utilizar um novo ID em cada sessão, mas os resultados sobre os utilizadores serão limitados. Por exemplo, não poderá ver como o comportamento de um utilizador muda ao longo do tempo.

O ID deve ser um Oriente ou outro complexo de cordas o suficiente para identificar cada utilizador de forma única. Por exemplo, pode ser um número aleatório longo.

Se o ID contiver informações de identificação pessoal sobre o utilizador, não é um valor apropriado enviar para A aplicação Insights como um ID do utilizador. Pode enviar um ID como [um ID autenticado](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users)do utilizador, mas não cumpre o requisito de ID do utilizador para cenários de utilização.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>ASP.NET aplicações: Definir o contexto do utilizador num ITelemettryInitializer

Crie um inicializador de telemetria, como descrito em detalhe [aqui.](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#addmodify-properties-itelemetryinitializer) Passe a identificação da sessão através da telemetria do pedido, e detetete o Context.User.Id e o Context.Session.Id.

Este exemplo define o ID do utilizador para um identificador que expira após a sessão. Se possível, utilize um ID do utilizador que persista em sessões.

### <a name="telemetry-initializer"></a>Inicializador de telemetria

```csharp
using System;
using System.Web;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that sets the user ID.
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var ctx = HttpContext.Current;

        // If telemetry initializer is called as part of request execution and not from some async thread
        if (ctx != null)
        {
            var requestTelemetry = ctx.GetRequestTelemetry();
 
            // Set the user and session ids from requestTelemetry.Context.User.Id, which is populated in Application_PostAcquireRequestState in Global.asax.cs.
            if (requestTelemetry != null && !string.IsNullOrEmpty(requestTelemetry.Context.User.Id) &&
                (string.IsNullOrEmpty(telemetry.Context.User.Id) || string.IsNullOrEmpty(telemetry.Context.Session.Id)))
            {
                // Set the user id on the Application Insights telemetry item.
                telemetry.Context.User.Id = requestTelemetry.Context.User.Id;
 
                // Set the session id on the Application Insights telemetry item.
                telemetry.Context.Session.Id = requestTelemetry.Context.User.Id;
            }
        }
    }
  }
}
```

### <a name="globalasaxcs"></a>Global.asax.cs

```csharp
using System.Web;
using System.Web.Http;
using System.Web.Mvc;
using System.Web.Optimization;
using System.Web.Routing;

namespace MvcWebRole.Telemetry
{
    public class MvcApplication : HttpApplication
    {
        protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            GlobalConfiguration.Configure(WebApiConfig.Register);
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
        }
 
        protected void Application_PostAcquireRequestState()
        {
            var requestTelemetry = Context.GetRequestTelemetry();
 
            if (HttpContext.Current.Session != null && requestTelemetry != null && string.IsNullOrEmpty(requestTelemetry.Context.User.Id))
            {
                requestTelemetry.Context.User.Id = Session.SessionID;
            }
        }
    }
}
```

## <a name="next-steps"></a>Passos seguintes

- Para permitir experiências de utilização, comece a enviar [eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [visualizações de páginas.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)
- Se já envia eventos personalizados ou visualizações de página, explore as ferramentas de Utilização para saber como os utilizadores usam o seu serviço.
    - [Visão geral do uso](usage-overview.md)
    - [Users, Sessions, and Events](usage-segmentation.md) (Utilizadores, Sessões e Eventos)
    - [Funis](usage-funnels.md)
    - [Retenção](usage-retention.md)
    - [Livros](../../azure-monitor/app/usage-workbooks.md)
