---
title: Análise de utilização com o Azure Application Insights | Documentos da Microsoft
description: Compreenda os seus utilizadores e o que fazem com a sua aplicação.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/10/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: ba29688958ee11aa9906a820f7a3d2bf41223743
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798172"
---
# <a name="usage-analysis-with-application-insights"></a>Análise de utilização com o Application Insights

Quais recursos da sua aplicação móvel ou web são mais populares? Os seus utilizadores alcançar seus objetivos com a sua aplicação? Que a deixam em pontos específicos e eles voltar mais tarde?  [O Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) ajuda-o a obter informações importantes sobre como as pessoas utilizam a sua aplicação. Sempre que atualizar a sua aplicação, pode avaliar a eficiência com que ele funciona para os utilizadores. Com esse conhecimento, pode fazer dados orientados pela decisões sobre os ciclos de desenvolvimento seguintes.

## <a name="send-telemetry-from-your-app"></a>Enviar telemetria a partir da sua aplicação

A melhor experiência é obtida pela instalação do Application Insights no seu código de servidor de aplicação e nas suas páginas web. Os componentes de cliente e servidor da sua aplicação enviam telemetria para o portal do Azure para análise.

1. **Código de servidor:** Instalar o módulo apropriado para sua [ASP.NET](../../azure-monitor/app/asp-net.md), [Azure](../../azure-monitor/app/app-insights-overview.md), [Java](../../azure-monitor/app/java-get-started.md), [node. js](../../azure-monitor/app/nodejs.md), ou [outros](../../azure-monitor/app/platforms.md) aplicação.

    * *Não pretende instalar o código de servidor? Basta [criar um recurso do Azure Application Insights](../../azure-monitor/app/create-new-resource.md ).*

2. **Código de página da Web:** Adicione o seguinte script para sua página da web antes do fechar ``</head>``. Substitua a chave de instrumentação com o valor apropriado para o seu recurso do Application Insights:

   ```javascript
      <script type="text/javascript">
        var appInsights=window.appInsights||function(a){
            function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
        }({
            instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
        });
        
        window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
    </script>
    ```
    Para obter configurações mais avançadas para monitorização de sites, veja a [referência da API do SDK JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

3. **Código de aplicação móvel:** Utilizar o SDK do App Center para recolher eventos a partir da sua aplicação, em seguida, envie cópias desses eventos para o Application Insights para análise por [seguir este guia](../../azure-monitor/learn/mobile-center-quickstart.md).

4. **Obter dados de telemetria:** Execute o seu projeto no modo de depuração para alguns minutos e, em seguida, procure os resultados no painel de descrição geral no Application Insights.

    Publique a sua aplicação para monitorizar o desempenho da sua aplicação e saber o que os usuários estão fazendo com a sua aplicação.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Incluir o ID de utilizador e a sessão na sua telemetria
Para controlar os utilizadores ao longo do tempo, o Application Insights requer uma maneira para identificá-los. A ferramenta de eventos é a única ferramenta de utilização que não é necessário um ID de utilizador ou um ID de sessão.

Começar a enviar o utilizador e IDs da sessão usando [esse processo](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Explorar dados demográficos de utilização e as estatísticas
Descubra quando as pessoas utilizam a sua aplicação, quais são as páginas que estiverem mais interessados, onde estão localizados os seus utilizadores, quais navegadores e sistemas operativos que a utilizam. 

Os relatórios de utilizadores e sessões de filtrar os seus dados por páginas ou eventos personalizados e segmentá-los por propriedades, tais como a localização, ambientais e de página. Também pode adicionar seus próprios filtros.

![Utilizadores](./media/usage-overview/users.png)  

Informações à direita ressaltar padrões interessantes no conjunto de dados.  

* O **utilizadores** relatório conta os números de utilizadores exclusivos que suas páginas de acesso nos seus períodos de tempo escolhido. Para aplicações web, os utilizadores são contabilizados ao utilizar cookies. Se alguém acede ao seu local em browsers diferentes ou máquinas de cliente ou limpa os cookies, em seguida, eles serão contados mais de uma vez.
* O **sessões** relatório conta o número de sessões de utilizador ao seu site. Uma sessão é um período de atividade por um utilizador, terminada por um período de inatividade de mais do que meia hora.

[Mais informações sobre as ferramentas de utilizadores, sessões e eventos](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Retenção - voltar o número de utilizadores?

Retenção ajuda-o a compreender a frequência com que os utilizadores regressam para utilizar a sua aplicação, com base em coortes de utilizadores que efetuou alguma ação de negócios durante um determinado bucket de tempo. 

- Compreender quais recursos específicos fazem com que os utilizadores fique mais que outras pessoas 
- Hipóteses de formulário com base nos dados de utilizador real 
- Determinar se o período de retenção é um problema em seu produto 

![Retenção](./media/usage-overview/retention.png) 

Os controles de retenção na parte superior permitem-lhe definir eventos específicos e o intervalo de tempo para calcular a retenção. O gráfico no meio fornece uma representação visual da percentagem de retenção geral por intervalo de tempo especificado. O gráfico na parte inferior representa retenção individual num determinado período de tempo. Este nível de detalhe permite-lhe compreender o que os usuários estão fazendo e o que podem afetar a devolver os utilizadores sobre uma granularidade mais detalhada.  

[Mais informações sobre a ferramenta de retenção](usage-retention.md)

## <a name="custom-business-events"></a>Eventos de negócios personalizados

Para obter uma compreensão clara de que os utilizadores que fazer com a sua aplicação, é útil inserir linhas de código para registar eventos personalizados. Esses eventos podem controlar qualquer coisa de ações de utilizador detalhadas, como clicar de botões específicos, para eventos de negócios mais significativos, como o fazer uma aquisição ou ganhar um jogo. 

Embora em alguns casos, as vistas de página podem representar eventos úteis, não é verdadeiro em geral. Um utilizador pode abrir uma página de produto, sem ter de comprar o produto. 

Com eventos de negócios específicas, pode gráfico progresso dos seus utilizadores através do seu site. Pode encontrar as suas preferências para as diferentes opções e onde eles drop fora ou têm dificuldades. Com esse conhecimento, pode tomar decisões informadas sobre as prioridades em sua lista de pendências de desenvolvimento.

Eventos podem ser registados no lado do cliente da aplicação:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Ou do lado do servidor:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Pode anexar os valores de propriedade a esses eventos, para que pode filtrar ou divida os eventos quando inspecioná-las no portal. Além disso, um conjunto padrão de propriedades está anexado a cada evento, como ID de utilizador anónimo, que permite-lhe rastrear a sequência de atividades de um utilizador individual.

Saiba mais sobre [eventos personalizados](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) e [propriedades](../../azure-monitor/app/api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Eventos de examinar

Nas ferramentas utilizadores, sessões e eventos, pode segmentar e decomposição de eventos personalizados por utilizador, nome de eventos e propriedades.
![Utilizadores](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Design a telemetria com a aplicação

Durante o projeto de cada funcionalidade da sua aplicação, considere como irá medir seu sucesso com os seus utilizadores. Decida quais eventos comerciais, precisa de gravar e o controlo de chamadas para esses eventos na sua aplicação desde o início do código.

## <a name="a--b-testing"></a>R | Teste de B
Se não souber qual variante de um recurso será a terem mais êxito, versão ambas, tornando cada acessível para diferentes utilizadores. Medir o sucesso de cada uma e, em seguida, mover para uma versão unificada.

Para essa técnica é anexar valores de propriedade distintos para toda a telemetria que é enviada por cada versão da sua aplicação. Pode fazer isso definindo propriedades no TelemetryContext Active Directory. Essas propriedades predefinidas são adicionadas a todas as mensagens de telemetria que a aplicação envia - não apenas as mensagens personalizadas, mas também a telemetria standard.

No portal do Application Insights, filtrar e dividir os dados nos valores de propriedade, de modo a comparar as diferentes versões.

Para fazer isso, [configurar um inicializador de telemetria](../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer):

**Aplicações ASP.NET**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

No inicializador de aplicação web, como Global.asax.cs:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**Aplicações ASP.NET Core**

> [!NOTE]
> Adicionar inicializador usando `ApplicationInsights.config` ou utilizar `TelemetryConfiguration.Active` não é válido para aplicativos do ASP.NET Core. 

Para [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) aplicativos, adicionar um novo `TelemetryInitializer` é feito adicionando-o para o contêiner de Injeção de dependência, conforme mostrado abaixo. Isso é feito `ConfigureServices` método de sua `Startup.cs` classe.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Todos os TelemetryClients novo adiciona automaticamente o valor de propriedade que especificar. Eventos de telemetria individuais podem substituir os valores predefinidos.

## <a name="next-steps"></a>Passos seguintes
   - [Utilizadores, Sessões, Eventos](usage-segmentation.md)
   - [Funis](usage-funnels.md)
   - [Retenção](usage-retention.md)
   - [Fluxos do Utilizador](usage-flows.md)
   - [Livros](../../azure-monitor/app/usage-workbooks.md)
   - [Adicionar contexto de utilizador](usage-send-user-context.md)
