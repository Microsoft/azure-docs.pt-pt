---
title: Enviar IDs de contexto de usuário para habilitar experiências de uso no Aplicativo Azure insights | Microsoft Docs
description: Acompanhe como os usuários se movem pelo serviço atribuindo a cada um deles uma cadeia de caracteres de ID exclusiva e persistente em Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/03/2019
ms.reviewer: abgreg;mbullwin
ms.openlocfilehash: b105e8ba79e99be02420ca1821f6a080b0862d6f
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73887401"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Enviar IDs de contexto de usuário para habilitar experiências de uso no Aplicativo Azure insights

## <a name="tracking-users"></a>Controlando usuários

Application Insights permite que você monitore e acompanhe seus usuários por meio de um conjunto de ferramentas de uso do produto:

- [Utilizadores, Sessões, Eventos](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
- [Funis](https://docs.microsoft.com/azure/application-insights/usage-funnels)
- [Retenção](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention) Coortes
- [Livros](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

Para acompanhar o que um usuário faz ao longo do tempo, Application Insights precisa de uma ID para cada usuário ou sessão. Inclua as seguintes IDs em cada exibição de página ou evento personalizado.

- Usuários, Funils, retenção e coortes: incluir ID de usuário.
- Sessões: incluir ID de sessão.

> [!NOTE]
> Este é um artigo avançado que descreve as etapas manuais para controlar a atividade do usuário com Application Insights. Com muitos aplicativos Web, **essas etapas podem não ser necessárias**, já que os SDKs padrão do lado do servidor em conjunto com o [SDK do JavaScript do lado do cliente](../../azure-monitor/app/website-monitoring.md ), geralmente são suficientes para rastrear automaticamente a atividade do usuário. Se você não tiver configurado o [monitoramento no lado do cliente](../../azure-monitor/app/website-monitoring.md ) além do SDK do lado do servidor, faça isso primeiro e teste para ver se as ferramentas de análise de comportamento do usuário estão sendo executadas conforme o esperado.

## <a name="choosing-user-ids"></a>Escolhendo IDs de usuário

As IDs de usuário devem persistir nas sessões do usuário para controlar como os usuários se comportam com o tempo. Há várias abordagens para persistir a ID.

- Uma definição de um usuário que você já tem em seu serviço.
- Se o serviço tiver acesso a um navegador, ele poderá passar o navegador um cookie com uma ID. A ID persistirá enquanto o cookie permanecer no navegador do usuário.
- Se necessário, você pode usar uma nova ID em cada sessão, mas os resultados sobre os usuários serão limitados. Por exemplo, você não conseguirá ver como o comportamento de um usuário muda ao longo do tempo.

A ID deve ser um GUID ou outra cadeia de caracteres complexa o suficiente para identificar cada usuário exclusivamente. Por exemplo, pode ser um número aleatório longo.

Se a ID contiver informações de identificação pessoal sobre o usuário, não será um valor apropriado a ser enviado para Application Insights como uma ID de usuário. Você pode enviar tal ID como uma [ID de usuário autenticado](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users), mas ele não atende ao requisito de ID de usuário para cenários de uso.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>Aplicativos ASP.NET: Configurando o contexto do usuário em um ITelemetryInitializer

Crie um inicializador de telemetria, conforme descrito em detalhes [aqui](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#addmodify-properties-itelemetryinitializer). Passe a ID da sessão por meio da telemetria de solicitação e defina Context.User.Id e Context.Session.Id.

Este exemplo define a ID de usuário para um identificador que expira após a sessão. Se possível, use uma ID de usuário que persiste entre as sessões.

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

- Para habilitar as experiências de uso, comece a enviar [eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [exibições de página](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se você já enviar eventos personalizados ou exibições de página, explore as ferramentas de uso para saber como os usuários usam seu serviço.
    - [Visão geral de uso](usage-overview.md)
    - [Users, Sessions, and Events](usage-segmentation.md) (Utilizadores, Sessões e Eventos)
    - [Funis](usage-funnels.md)
    - [Retenção](usage-retention.md)
    - [Livros](../../azure-monitor/app/usage-workbooks.md)
