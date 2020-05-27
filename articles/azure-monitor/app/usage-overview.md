---
title: Análise de utilização com insights de aplicação azure / Microsoft docs
description: Compreenda os seus utilizadores e o que eles fazem com a sua aplicação.
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 7f4f46f183291684fa59e5aa35b65c8ac3352563
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797782"
---
# <a name="usage-analysis-with-application-insights"></a>Análise de utilização com o Application Insights

Quais as funcionalidades da sua aplicação web ou móvel mais populares? Os seus utilizadores alcançam os seus objetivos com a sua aplicação? Desistem em determinados pontos e regressam mais tarde?  [O Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) ajuda-o a obter informações poderosas sobre como as pessoas usam a sua aplicação. Sempre que atualizar a sua aplicação, pode avaliar o quão bem funciona para os utilizadores. Com este conhecimento, pode tomar decisões baseadas em dados sobre os seus próximos ciclos de desenvolvimento.

## <a name="send-telemetry-from-your-app"></a>Envie telemetria da sua aplicação

A melhor experiência é obtida instalando Insights de Aplicação tanto no código do servidor da sua aplicação como nas suas páginas web. Os componentes do cliente e do servidor da sua aplicação enviam telemetria de volta para o portal Azure para análise.

1. **Código do servidor:** Instale o módulo apropriado para o seu [ASP.NET](../../azure-monitor/app/asp-net.md), [Azure,](../../azure-monitor/app/app-insights-overview.md) [Java,](../../azure-monitor/app/java-get-started.md) [Node.js](../../azure-monitor/app/nodejs.md)ou [outra](../../azure-monitor/app/platforms.md) aplicação.

    * *Não quer instalar o código do servidor? Basta [criar um recurso Azure Application Insights](../../azure-monitor/app/create-new-resource.md ).*

2. **Código da página web:** Adicione o seguinte script à sua página web antes do fecho ``</head>`` . Substitua a chave de instrumentação pelo valor adequado para o seu recurso Application Insights:
    
    ```html
    <script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
    </script>
    ```

    Para saber configurações mais avançadas para monitorizar websites, consulte o artigo de [referência JavaScript SDK](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

3. Código de **aplicativomóvel:** Utilize o App Center SDK para recolher eventos da sua aplicação e, em seguida, envie cópias destes eventos para A aplicação Insights para análise [seguindo este guia](../../azure-monitor/learn/mobile-center-quickstart.md).

4. **Obter telemetria:** Execute o seu projeto em modo de depuração durante alguns minutos e, em seguida, procure resultados na lâmina de visão geral em Insights de Aplicação.

    Publique a sua aplicação para monitorizar o desempenho da sua aplicação e descubra o que os seus utilizadores estão a fazer com a sua aplicação.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Inclua id de utilizador e sessão na sua telemetria
Para rastrear os utilizadores ao longo do tempo, o Application Insights requer uma forma de os identificar. A ferramenta Eventos é a única ferramenta de utilização que não requer um ID do utilizador ou um ID de sessão.

Comece a enviar iDs de utilizador e sessão utilizando [este processo](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Explore a demografia e as estatísticas de uso
Descubra quando as pessoas usam a sua aplicação, em que páginas estão mais interessadas, onde estão localizados os seus utilizadores, quais os navegadores e sistemas operativos que utilizam. 

Os relatórios utilizadores e sessões filtram os seus dados por páginas ou eventos personalizados, e segmentam-nos por propriedades como localização, ambiente e página. Também pode adicionar os seus próprios filtros.

![Utilizadores](./media/usage-overview/users.png)  

Insights sobre o certo apontam padrões interessantes no conjunto de dados.  

* O relatório **Utilizadores** conta o número de utilizadores únicos que acedem às suas páginas dentro dos períodos de tempo escolhidos. Para aplicações web, os utilizadores são contados usando cookies. Se alguém aceder ao seu site com diferentes navegadores ou máquinas de clientes, ou limpar os seus cookies, então serão contados mais de uma vez.
* O relatório **Sessions** conta o número de sessões de utilizador que acedem ao seu site. Uma sessão é um período de atividade por parte de um utilizador, encerrado por um período de inatividade superior a meia hora.

[Mais sobre as ferramentas Utilizadores, Sessões e Eventos](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Retenção - quantos utilizadores voltam?

A retenção ajuda-o a perceber com que frequência os seus utilizadores voltam a utilizar a sua app, com base em coortes de utilizadores que realizaram alguma saque de negócio durante um determinado balde de tempo. 

- Entenda quais as funcionalidades específicas que fazem com que os utilizadores voltem mais do que outros 
- Formar hipóteses com base em dados reais do utilizador 
- Determine se a retenção é um problema no seu produto 

![Retenção](./media/usage-overview/retention.png) 

Os controlos de retenção em cima permitem definir eventos específicos e intervalo de tempo para calcular a retenção. O gráfico no meio dá uma representação visual da percentagem de retenção global pelo intervalo de tempo especificado. O gráfico na parte inferior representa a retenção individual num determinado período de tempo. Este nível de detalhe permite-lhe compreender o que os seus utilizadores estão a fazer e o que pode afetar o retorno dos utilizadores numa granularidade mais detalhada.  

[Mais sobre a ferramenta Retenção](usage-retention.md)

## <a name="custom-business-events"></a>Eventos comerciais personalizados

Para obter uma compreensão clara do que os utilizadores fazem com a sua aplicação, é útil inserir linhas de código para registar eventos personalizados. Estes eventos podem rastrear qualquer coisa desde ações detalhadas do utilizador, como clicar em botões específicos, até eventos de negócio saem mais significativos, como fazer uma compra ou ganhar um jogo. 

Embora em alguns casos, as visualizações de páginas possam representar eventos úteis, não é verdade em geral. Um utilizador pode abrir uma página de produto sem comprar o produto. 

Com eventos de negócioespecíficos, pode traçar o progresso dos seus utilizadores através do seu site. Você pode descobrir suas preferências por diferentes opções, e onde eles desistem ou têm dificuldades. Com este conhecimento, pode tomar decisões informadas sobre as prioridades no seu atraso de desenvolvimento.

Os eventos podem ser registados a partir do lado cliente da app:

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

Pode anexar valores de propriedade a estes eventos, para que possa filtrar ou dividir os eventos quando os inspecionar no portal. Além disso, um conjunto padrão de propriedades é anexado a cada evento, como o ID do utilizador anónimo, que permite rastrear a sequência de atividades de um utilizador individual.

Saiba mais sobre eventos e [propriedades](../../azure-monitor/app/api-custom-events-metrics.md#properties) [personalizadas.](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)

### <a name="slice-and-dice-events"></a>Eventos de fatias e dados

Nas ferramentas Utilizadores, Sessões e Eventos, pode cortar e dado eventos personalizados pelo utilizador, nome do evento e propriedades.
![Utilizadores](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Desenhe a telemetria com a app

Quando estiver a desenhar cada funcionalidade da sua aplicação, considere como vai medir o seu sucesso com os seus utilizadores. Decida quais os eventos empresariais que precisa de gravar e codifique as chamadas de rastreio desses eventos para a sua aplicação desde o início.

## <a name="a--b-testing"></a>A B Testes
Se não souber qual a variante de uma funcionalidade será mais bem sucedida, liberte ambos, tornando cada um acessível a diferentes utilizadores. Meça o sucesso de cada um e, em seguida, passe para uma versão unificada.

Para esta técnica, anexa valores de propriedade distintos a toda a telemetria que é enviada por cada versão da sua aplicação. Pode fazê-lo definindo propriedades no TelemettryContext ativo. Estas propriedades padrão são adicionadas a todas as mensagens de telemetria que a aplicação envia - não apenas as suas mensagens personalizadas, mas também a telemetria padrão.

No portal Application Insights, filtre e divida os seus dados sobre os valores da propriedade, de modo a comparar as diferentes versões.

Para tal, [instale um infetante de telemetria:](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)

**Aplicações ASP.NET**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry item)
            {
                var itemProperties = item as ISupportProperties;
                if (itemProperties != null && !itemProperties.Properties.ContainsKey("AppVersion"))
                {
                    itemProperties.Properties["AppVersion"] = "v2.1";
                }
            }
    }
```

No inicializador da aplicação web, como Global.asax.cs:

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
> Adicionar inicializador `ApplicationInsights.config` utilizando ou utilizar não é válido para `TelemetryConfiguration.Active` aplicações ASP.NET Core. 

Para ASP.NET aplicações [Core,](asp-net-core.md#adding-telemetryinitializers) a adição de uma nova é feita adicionando-a ao recipiente de injeção de `TelemetryInitializer` dependência, como mostrado abaixo. Isto é feito no `ConfigureServices` método da sua `Startup.cs` classe.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Todos os novos TelemettryClients adicionam automaticamente o valor da propriedade que especifica. Eventos individuais de telemetria podem anular os valores padrão.

## <a name="next-steps"></a>Passos seguintes
   - [Utilizadores, Sessões, Eventos](usage-segmentation.md)
   - [Funis](usage-funnels.md)
   - [Retenção](usage-retention.md)
   - [Fluxos do Utilizador](usage-flows.md)
   - [Livros](../../azure-monitor/platform/workbooks-overview.md)
   - [Adicionar contexto de utilizador](usage-send-user-context.md)
