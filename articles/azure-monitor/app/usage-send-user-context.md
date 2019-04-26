---
title: Enviar o contexto de usuário IDs para ativar a utilização de experiências no Application Insights do Azure | Documentos da Microsoft
description: Controle como os utilizadores passam por meio do seu serviço atribuindo uma cadeia de ID exclusiva, persistente no Application Insights para cada um deles.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: csharp
ms.topic: conceptual
ms.date: 01/03/2019
ms.reviewer: abgreg;mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 7c458867b89a76a2f19bbd632c8a884c629f5765
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371840"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Enviar o contexto de utilizador IDs para permitir experiências de utilização no Azure Application Insights

## <a name="tracking-users"></a>Usuários de rastreamento

O Application Insights permite-lhe monitorizar e controlar os seus utilizadores através de um conjunto de ferramentas de utilização de produto:

- [Utilizadores, Sessões, Eventos](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
- [Funis](https://docs.microsoft.com/azure/application-insights/usage-funnels)
- [Retenção](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention) coortes
- [Livros](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

Para poder controlar o que faz um utilizador ao longo do tempo, o Application Insights tem um ID para cada utilizador ou a sessão. Inclua os seguintes IDs de em cada vista de evento ou página personalizada.

- Os utilizadores, funis, retenção e coortes: Incluir o ID de utilizador.
- Sessões: Incluir o ID de sessão.

> [!NOTE]
> Este é um artigo avançado que descreva os passos manuais para controlar a atividade do utilizador com o Application Insights. Com muitos aplicativos web **estes passos podem não ser necessários**, como o servidor de predefinição SDKs em conjunto com o [SDK de JavaScript do lado do cliente/Browser](../../azure-monitor/app/website-monitoring.md ), costumam ser suficiente para monitorizar automaticamente atividade do utilizador. Se ainda não configurado [monitorização do lado do cliente](../../azure-monitor/app/website-monitoring.md ) além do SDK do lado do servidor, faça-o primeiro e testar para ver se as ferramentas de análise de comportamento do utilizador são executadas conforme esperado.

## <a name="choosing-user-ids"></a>Escolher os IDs de utilizador

IDs de utilizador devem manter em sessões de utilizador para controlar o comportam de utilizadores ao longo do tempo. Existem várias abordagens para persistir o ID.

- Uma definição de um utilizador que já tem no seu serviço.
- Se o serviço tem acesso a um browser, que pode transmitir o navegador um cookie com um ID na mesma. O ID serão mantidas para, desde que o cookie permanece no navegador do usuário.
- Se necessário, pode utilizar um novo ID de cada sessão, mas os resultados sobre os utilizadores serão limitados. Por exemplo, não será capaz de ver como o comportamento de um utilizador altera ao longo do tempo.

O ID deve ser um Guid ou outra cadeia suficientemente complexa para identificar exclusivamente a cada utilizador. Por exemplo, pode ser um número aleatório muito tempo.

Se o ID contém informações de identificação pessoal sobre o utilizador, não é um valor adequado para enviar para o Application Insights como um ID de utilizador. Pode enviar esse um ID como um [autenticado o ID de utilizador](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users), mas não cumpre o requisito de ID de utilizador para cenários de utilização.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>Aplicações do ASP.NET: Definir o contexto de usuário num ITelemetryInitializer

Criar um inicializador de telemetria, conforme descrito em detalhe [aqui](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer). Passar o ID de sessão através de telemetria de pedido e definir o Context.User.Id e o Context.Session.Id.

Este exemplo define o ID de utilizador como um identificador que expira após a sessão. Se possível, utilize um ID de utilizador que persiste entre sessões.

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

## <a name="next-steps"></a>Passos Seguintes

- Para permitir experiências de utilização, começar a enviar [eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [vistas de página](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se já tiver enviar eventos personalizados ou vistas de página, explore as ferramentas de utilização para saber como os utilizadores utilizam o seu serviço.
    - [Descrição geral da utilização](usage-overview.md)
    - [Users, Sessions, and Events](usage-segmentation.md) (Utilizadores, Sessões e Eventos)
    - [Funis](usage-funnels.md)
    - [Retenção](usage-retention.md)
    - [Livros](../../azure-monitor/app/usage-workbooks.md)
