---
title: Análise de uso com o Aplicativo Azure insights | Microsoft docs
description: Entenda seus usuários e o que eles fazem com seu aplicativo.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/19/2019
ms.openlocfilehash: 7131cf1902cc92fed66ae4db59449700973c6913
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899436"
---
# <a name="usage-analysis-with-application-insights"></a>Análise de utilização com o Application Insights

Quais recursos de seu aplicativo Web ou móvel são mais populares? Os usuários atingem suas metas com seu aplicativo? Eles são suspensos em pontos específicos e retornam mais tarde?  O [aplicativo Azure insights](../../azure-monitor/app/app-insights-overview.md) ajuda você a obter informações poderosas sobre como as pessoas usam seu aplicativo. Sempre que atualizar seu aplicativo, você poderá avaliar como ele funciona para os usuários. Com esse conhecimento, você pode tomar decisões orientadas por dados sobre seus próximos ciclos de desenvolvimento.

## <a name="send-telemetry-from-your-app"></a>Enviar telemetria do seu aplicativo

A melhor experiência é obtida com a instalação do Application Insights no código do servidor de aplicativos e em suas páginas da Web. Os componentes de cliente e servidor do seu aplicativo enviam a telemetria de volta para a portal do Azure para análise.

1. **Código do servidor:** Instale o módulo apropriado para seu [ASP.net](../../azure-monitor/app/asp-net.md), [Azure](../../azure-monitor/app/app-insights-overview.md), [Java](../../azure-monitor/app/java-get-started.md), [node. js](../../azure-monitor/app/nodejs.md)ou [outro](../../azure-monitor/app/platforms.md) aplicativo.

    * *Não deseja instalar o código do servidor? Basta [criar um recurso de informações de aplicativo Azure](../../azure-monitor/app/create-new-resource.md ).*

2. **Código da página da Web:** Adicione o script a seguir à sua página da Web antes de fechar ``</head>``. Substitua a chave de instrumentação pelo valor apropriado para seu recurso de Application Insights:
    
    ```html
    <script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
    </script>
    ```

    Para saber mais sobre configurações avançadas para o monitoramento de sites, confira o [artigo de referência do SDK do JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

3. **Código do aplicativo móvel:** Use o SDK do App Center para coletar eventos de seu aplicativo e, em seguida, envie cópias desses eventos para Application Insights para análise seguindo [este guia](../../azure-monitor/learn/mobile-center-quickstart.md).

4. **Obter telemetria:** Execute seu projeto no modo de depuração por alguns minutos e, em seguida, procure resultados na folha visão geral em Application Insights.

    Publique seu aplicativo para monitorar o desempenho do aplicativo e descubra o que os usuários estão fazendo com seu aplicativo.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Incluir ID de usuário e de sessão em sua telemetria
Para controlar os usuários ao longo do tempo, Application Insights requer uma maneira de identificá-los. A ferramenta de eventos é a única ferramenta de uso que não requer uma ID de usuário ou uma ID de sessão.

Comece a enviar IDs de usuário e de sessão usando [este processo](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Explorar estatísticas e dados demográficos de uso
Descubra quando as pessoas usam seu aplicativo, em quais páginas eles estão mais interessados, onde os usuários estão localizados, quais navegadores e sistemas operacionais eles usam. 

Os relatórios de usuários e sessões filtram seus dados por páginas ou eventos personalizados e os segmentam por propriedades como local, ambiente e página. Você também pode adicionar seus próprios filtros.

![Utilizadores](./media/usage-overview/users.png)  

As informações à direita apontam padrões interessantes no conjunto de dados.  

* O relatório de **usuários** conta os números de usuários exclusivos que acessam suas páginas dentro dos períodos de tempo escolhidos. Para aplicativos Web, os usuários são contados usando cookies. Se alguém acessar seu site com diferentes navegadores ou computadores cliente, ou desmarcar seus cookies, eles serão contados mais de uma vez.
* O relatório **sessões** conta o número de sessões de usuário que acessam seu site. Uma sessão é um período de atividade por um usuário, terminada por um período de inatividade de mais de meia hora.

[Mais sobre as ferramentas de usuários, sessões e eventos](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Retenção-quantos usuários retornam?

A retenção ajuda a entender com que frequência os usuários retornam para usar seu aplicativo, com base em coortes de usuários que executaram alguma ação comercial durante um determinado Bucket de tempo. 

- Entenda quais recursos específicos fazem com que os usuários voltem mais do que outros 
- Formate as mesmas com base nos dados reais do usuário 
- Determinar se a retenção é um problema em seu produto 

![Retenção](./media/usage-overview/retention.png) 

Os controles de retenção na parte superior permitem que você defina eventos específicos e o intervalo de tempo para calcular a retenção. O grafo no meio fornece uma representação visual da porcentagem de retenção geral pelo intervalo de tempo especificado. O grafo na parte inferior representa a retenção individual em um determinado período de tempo. Esse nível de detalhe permite que você entenda o que os usuários estão fazendo e o que pode afetar o retorno de usuários em uma granularidade mais detalhada.  

[Mais sobre a ferramenta de retenção](usage-retention.md)

## <a name="custom-business-events"></a>Eventos de negócios personalizados

Para ter uma compreensão clara do que os usuários fazem com seu aplicativo, é útil inserir linhas de código para registrar eventos personalizados. Esses eventos podem controlar tudo, desde ações detalhadas do usuário, como clicar em botões específicos, até eventos de negócios mais significativos, como fazer uma compra ou ganhar um jogo. 

Embora, em alguns casos, as exibições de página possam representar eventos úteis, isso não é verdade em geral. Um usuário pode abrir uma página de produto sem comprar o produto. 

Com eventos de negócios específicos, você pode gráfico do progresso de seus usuários por meio de seu site. Você pode descobrir suas preferências para diferentes opções e onde elas se descartam ou têm dificuldades. Com esse conhecimento, você pode tomar decisões informadas sobre as prioridades em seu registro posterior de desenvolvimento.

Os eventos podem ser registrados no lado do cliente do aplicativo:

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

Você pode anexar valores de propriedade a esses eventos, para que você possa filtrar ou dividir os eventos ao inspecioná-los no Portal. Além disso, um conjunto padrão de propriedades é anexado a cada evento, como ID de usuário anônimo, que permite rastrear a sequência de atividades de um usuário individual.

Saiba mais sobre [Propriedades](../../azure-monitor/app/api-custom-events-metrics.md#properties)e [eventos personalizados](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) .

### <a name="slice-and-dice-events"></a>Eventos de fatia e de

Nas ferramentas usuários, sessões e eventos, você pode fatiar e dividir os eventos personalizados por usuário, nome do evento e propriedades.
![Utilizadores](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Criar a telemetria com o aplicativo

Ao criar cada recurso do seu aplicativo, considere como você vai medir seu sucesso com os usuários. Decida quais eventos de negócios você precisa registrar e codifique as chamadas de rastreamento para esses eventos em seu aplicativo desde o início.

## <a name="a--b-testing"></a>A | B teste
Se você não souber qual variante de um recurso será mais bem-sucedido, libere ambos, tornando cada um acessível para usuários diferentes. Meça o sucesso de cada um deles e, em seguida, passe para uma versão unificada.

Para essa técnica, você anexa valores de propriedade distintos a todas as telemetrias enviadas por cada versão do seu aplicativo. Você pode fazer isso definindo propriedades no TelemetryContext ativo. Essas propriedades padrão são adicionadas a cada mensagem de telemetria que o aplicativo envia – não apenas suas mensagens personalizadas, mas também a telemetria padrão.

No portal de Application Insights, filtre e divida os dados nos valores de propriedade, para comparar as diferentes versões.

Para fazer isso, [Configure um inicializador de telemetria](../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer):

**Aplicativos ASP.NET**

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

No inicializador do aplicativo Web, como Global.asax.cs:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**ASP.NET Core aplicativos**

> [!NOTE]
> Adicionar inicializador usando `ApplicationInsights.config` ou usando `TelemetryConfiguration.Active` não é válido para aplicativos ASP.NET Core. 

Para aplicativos [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) , adicionar um novo `TelemetryInitializer` é feito adicionando-o ao contêiner de injeção de dependência, como mostrado abaixo. Isso é feito em `ConfigureServices` método de sua classe `Startup.cs`.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Todos os novos TelemetryClients adicionam automaticamente o valor da propriedade que você especificar. Eventos de telemetria individuais podem substituir os valores padrão.

## <a name="next-steps"></a>Passos seguintes
   - [Utilizadores, Sessões, Eventos](usage-segmentation.md)
   - [Funis](usage-funnels.md)
   - [Retenção](usage-retention.md)
   - [Fluxos do Utilizador](usage-flows.md)
   - [Livros](../../azure-monitor/app/usage-workbooks.md)
   - [Adicionar contexto de usuário](usage-send-user-context.md)
