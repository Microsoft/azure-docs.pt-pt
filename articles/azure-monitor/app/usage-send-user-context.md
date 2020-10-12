---
title: IDs de contexto do utilizador para acompanhar a atividade - Azure Application Insights
description: Acompanhe como os utilizadores se movem através do seu serviço atribuindo a cada um deles uma cadeia de ID única e persistente em Application Insights.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/03/2019
ms.reviewer: abgreg;mbullwin
ms.openlocfilehash: 46b7479df6d087915cfe81895a786a528da6b9bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87327910"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Envie IDs de contexto de utilizador para permitir experiências de utilização em Azure Application Insights

## <a name="tracking-users"></a>Utilizadores de rastreio

O Application Insights permite-lhe monitorizar e rastrear os seus utilizadores através de um conjunto de ferramentas de utilização do produto:

- [Utilizadores, Sessões, Eventos](./usage-segmentation.md)
- [Funis](./usage-funnels.md)
- [Retenção](./usage-retention.md) Coortes
- [Livros](../platform/workbooks-overview.md)

Para acompanhar o que um utilizador faz ao longo do tempo, o Application Insights necessita de um ID para cada utilizador ou sessão. Inclua os seguintes IDs em cada evento personalizado ou vista de página.

- Utilizadores, Funis, Retenção e Cohorts: Inclua iD do utilizador.
- Sessões: Incluir iD de sessão.

> [!NOTE]
> Este é um artigo avançado que descreve os passos manuais para rastrear a atividade do utilizador com o Application Insights. Com muitas aplicações **web, estes passos podem não ser necessários**, uma vez que os SDKs do lado do servidor padrão em conjunto com o [JavaScript SDK do lado do Cliente/Browser](./website-monitoring.md), são muitas vezes suficientes para rastrear automaticamente a atividade do utilizador. Se ainda não configurar [a monitorização do lado](./website-monitoring.md) do cliente para além do SDK do lado do servidor, faça-o primeiro e teste para ver se as ferramentas de análise de comportamento do utilizador estão a funcionar como esperado.

## <a name="choosing-user-ids"></a>Escolher iDs de utilizador

Os IDs do utilizador devem persistir em todas as sessões do utilizador para acompanhar o comportamento dos utilizadores ao longo do tempo. Existem várias abordagens para persistir na identificação.

- Uma definição de utilizador que já tem ao seu serviço.
- Se o serviço tiver acesso a um browser, pode passar ao navegador um cookie com um ID no mesmo. O ID persistirá enquanto o cookie permanecer no navegador do utilizador.
- Se necessário, pode utilizar um novo ID em cada sessão, mas os resultados sobre os utilizadores serão limitados. Por exemplo, não será capaz de ver como o comportamento de um utilizador muda ao longo do tempo.

O ID deve ser um complexo de cordas guid ou outro o suficiente para identificar cada utilizador de forma única. Por exemplo, pode ser um número longo e aleatório.

Se o ID contiver informações de identificação pessoal sobre o utilizador, não é um valor apropriado enviar para o Application Insights como ID do utilizador. Pode enviar um ID tal como um [ID de utilizador autenticado,](./api-custom-events-metrics.md#authenticated-users)mas não preenche o requisito de ID do utilizador para cenários de utilização.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>ASP.NET apps: Definir o contexto do utilizador num ITelemetryInitializer

Crie um inicializador de telemetria, conforme descrito em detalhe [aqui](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer). Passe a identificação da sessão através da telemetria do pedido, e desempenhe o Context.User.Id e o Context.Session.Id.

Este exemplo define o ID do utilizador a um identificador que expira após a sessão. Se possível, utilize um ID do utilizador que persista em todas as sessões.

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

- Para ativar experiências de utilização, comece a enviar [eventos personalizados](./api-custom-events-metrics.md#trackevent) ou [vistas de página.](./api-custom-events-metrics.md#page-views)
- Se já envia eventos personalizados ou vistas de página, explore as ferramentas de Utilização para saber como os utilizadores usam o seu serviço.
    - [Visão geral do uso](usage-overview.md)
    - [Users, Sessions, and Events](usage-segmentation.md) (Utilizadores, Sessões e Eventos)
    - [Funis](usage-funnels.md)
    - [Retenção](usage-retention.md)
    - [Livros](../platform/workbooks-overview.md)

