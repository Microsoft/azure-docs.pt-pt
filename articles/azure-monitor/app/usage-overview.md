---
title: Análise de utilização com Azure Application Insights | Docs da Microsoft
description: Compreenda os seus utilizadores e o que fazem com a sua aplicação.
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 32c817fea00cfd28a3e0187cc7c581d828412c69
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726160"
---
# <a name="usage-analysis-with-application-insights"></a>Análise de utilização com o Application Insights

Quais as funcionalidades da sua web ou aplicação móvel mais populares? Os seus utilizadores alcançam os seus objetivos com a sua app? Desempatam em determinados pontos, e voltam mais tarde?  [O Azure Application Insights](./app-insights-overview.md) ajuda-o a obter informações poderosas sobre como as pessoas usam a sua aplicação. Sempre que atualizar a sua aplicação, pode avaliar o quão bem funciona para os utilizadores. Com este conhecimento, pode tomar decisões orientadas para dados sobre os seus próximos ciclos de desenvolvimento.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Cijb]

## <a name="send-telemetry-from-your-app"></a>Envie telemetria a partir da sua app

A melhor experiência obtém-se através da instalação de Application Insights tanto no código do servidor da sua aplicação como nas suas páginas web. Os componentes do cliente e do servidor da sua aplicação enviam a telemetria de volta para o portal Azure para análise.

1. **Código do servidor:** Instale o módulo apropriado para o seu [ASP.NET](./asp-net.md), [Azure,](./app-insights-overview.md) [Java,](./java-get-started.md) [Node.js](./nodejs.md)ou [outra](./platforms.md) aplicação.

    * *Não quer instalar o código do servidor? Basta [criar um recurso Azure Application Insights](./create-new-resource.md).*

2. **Código da página web:** Adicione o seguinte script à sua página web antes do fecho ``</head>`` . Substitua a tecla de instrumentação pelo valor adequado para o seu recurso Application Insights:
    
    O snippet atual (listado abaixo) é a versão "5", a versão está codificada no snippet como sv:"#" e a [versão atual também está disponível no GitHub](https://go.microsoft.com/fwlink/?linkid=2156318).

    ```html
    <script type="text/javascript">
    !function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{nConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[C]&&!0!==s[C]){method="onerror",t(["_"+method]);var c=T[method];T[method]=function(e,t,n,a,i){var r=c&&c(e,t,n,a,i);return!0!==r&&m["_"+method]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);(T[t]=n).queue&&0===n.queue.length&&n.trackPageView({})}(window,document,{
    src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
    // name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
    // ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
    // useXhr: 1, // Use XHR instead of fetch to report failures (if available),
    crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
    // onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
    cfg: { // Application Insights Configuration
      instrumentationKey:"INSTRUMENTATION_KEY"
    }});
    </script>
    ```

    Para obter configurações mais avançadas para monitorizar websites, consulte o [artigo de referência javaScript SDK](./javascript.md).

3. **Código de aplicativo móvel:** Utilize o App Center SDK para recolher eventos da sua aplicação e, em seguida, envie cópias destes eventos para a Aplicação Insights para [análise, seguindo este guia.](../app/mobile-center-quickstart.md)

4. **Obter telemetria:** Execute o seu projeto em modo de depurar durante alguns minutos e, em seguida, procure resultados na lâmina de visão geral em Application Insights.

    Publique a sua aplicação para monitorizar o desempenho da sua aplicação e descubra o que os seus utilizadores estão a fazer com a sua aplicação.

## <a name="explore-usage-demographics-and-statistics"></a>Explorar a demografia de utilização e as estatísticas
Descubra quando as pessoas usam a sua app, quais as páginas em que estão mais interessados, onde os seus utilizadores estão localizados, quais os navegadores e sistemas operativos que utilizam. 

Os relatórios de Utilizadores e Sessões filtram os seus dados por páginas ou eventos personalizados, e segmentam-nos por propriedades como localização, ambiente e página. Também pode adicionar os seus próprios filtros.

![A captura de ecrã mostra a página de Visão Geral dos Utilizadores para uma empresa fictícia.](./media/usage-overview/users.png)  

Insights sobre a direita apontam padrões interessantes no conjunto de dados.  

* O relatório **dos Utilizadores** conta os números de utilizadores únicos que acedem às suas páginas dentro dos períodos de tempo escolhidos. Para aplicações web, os utilizadores são contados usando cookies. Se alguém aceder ao seu site com diferentes navegadores ou máquinas de clientes, ou limpar os seus cookies, então serão contados mais de uma vez.
* O relatório **sessions** conta o número de sessões de utilizador que acedem ao seu site. Uma sessão é um período de atividade por um utilizador, terminado por um período de inatividade superior a meia hora.

[Mais sobre as ferramentas de Utilizadores, Sessões e Eventos](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Retenção - quantos utilizadores voltam?

A retenção ajuda-o a perceber com que frequência os seus utilizadores voltam a usar a sua app, com base em coortes de utilizadores que realizaram alguma ação comercial durante um determinado balde de tempo. 

- Entenda que funcionalidades específicas fazem com que os utilizadores voltem mais do que outros 
- Hipóteses de formulário com base em dados reais do utilizador 
- Determine se a retenção é um problema no seu produto 

![A captura do ecrã mostra a página "Retenção Geral", que exibe informações sobre a frequência com que os utilizadores voltam a utilizar a sua aplicação.](./media/usage-overview/retention.png) 

Os controlos de retenção em cima permitem definir eventos específicos e intervalo de tempo para calcular a retenção. O gráfico no meio dá uma representação visual da percentagem global de retenção pelo intervalo de tempo especificado. O gráfico na parte inferior representa a retenção individual num determinado período de tempo. Este nível de detalhe permite-lhe entender o que os seus utilizadores estão a fazer e o que pode afetar o retorno dos utilizadores numa granularidade mais detalhada.  

[Mais sobre a ferramenta de retenção](usage-retention.md)

## <a name="custom-business-events"></a>Eventos de negócios personalizados

Para obter uma compreensão clara do que os utilizadores fazem com a sua aplicação, é útil inserir linhas de código para registar eventos personalizados. Estes eventos podem rastrear qualquer coisa, desde ações detalhadas do utilizador, como clicar em botões específicos, até eventos de negócio mais significativos, como fazer uma compra ou ganhar um jogo.

Também pode utilizar o [Plugin de recolha automática click Analytics](javascript-click-analytics-plugin.md) para recolher eventos personalizados.

Embora, em alguns casos, as vistas de página possam representar eventos úteis, não é verdade em geral. Um utilizador pode abrir uma página de produto sem comprar o produto. 

Com eventos de negócios específicos, pode traçar o progresso dos seus utilizadores através do seu site. Você pode descobrir suas preferências para diferentes opções, e onde eles desempoiam ou têm dificuldades. Com este conhecimento, pode tomar decisões informadas sobre as prioridades no seu atraso de desenvolvimento.

Os eventos podem ser registados do lado cliente da aplicação:

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

Pode anexar valores de propriedade a estes eventos, para que possa filtrar ou dividir os eventos quando os inspeciona no portal. Além disso, um conjunto padrão de propriedades é anexado a cada evento, como iD de utilizador anónimo, que permite rastrear a sequência de atividades de um utilizador individual.

Saiba mais sobre eventos e [propriedades](./api-custom-events-metrics.md#properties) [personalizadas.](./api-custom-events-metrics.md#trackevent)

### <a name="slice-and-dice-events"></a>Eventos de fatias e dados

Nas ferramentas Utilizadores, Sessões e Eventos, pode cortar e picar eventos personalizados pelo utilizador, nome do evento e propriedades.
![A captura de ecrã mostra a página de Visão Geral dos Utilizadores para uma empresa fictícia.](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Desenhe a telemetria com a app

Quando estiver a desenhar cada funcionalidade da sua app, considere como vai medir o seu sucesso com os seus utilizadores. Decida quais eventos de negócios precisa de gravar e codi venda as chamadas de rastreio para esses eventos na sua app desde o início.

## <a name="a--b-testing"></a>Um | B Testes
Se não sabe qual a variante de uma funcionalidade que será mais bem sucedida, liberte ambas, tornando cada uma acessível a diferentes utilizadores. Meça o sucesso de cada um e, em seguida, passe para uma versão unificada.

Para esta técnica, você anexa valores de propriedade distintos a toda a telemetria que é enviada por cada versão da sua app. Pode fazê-lo definindo propriedades no TelemetriaContexto ativo. Estas propriedades predefinidos são adicionadas a todas as mensagens de telemetria que a aplicação envia - não apenas as suas mensagens personalizadas, mas também a telemetria padrão.

No portal Application Insights, filtre e divida os seus dados sobre os valores da propriedade, de modo a comparar as diferentes versões.

Para tal, [crie um inicializador de telemetria:](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)

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

No inicializador de aplicações web, como Global.asax.cs:

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
> Adicionar inicializador usando `ApplicationInsights.config` ou usando `TelemetryConfiguration.Active` não é válido para aplicações core ASP.NET. 

Para ASP.NET aplicações [Core,](asp-net-core.md#adding-telemetryinitializers) a adição de um novo `TelemetryInitializer` é feita adicionando-a ao recipiente de Injeção de Dependência, como mostrado abaixo. Isto é feito no `ConfigureServices` método da sua `Startup.cs` classe.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Todos os novos TelemetriaClients adicionam automaticamente o valor da propriedade que especifica. Os eventos individuais de telemetria podem sobrepor-se aos valores predefinidos.

## <a name="next-steps"></a>Passos seguintes
   - [Utilizadores, Sessões, Eventos](usage-segmentation.md)
   - [Funis](usage-funnels.md)
   - [Retenção](usage-retention.md)
   - [Fluxos de Utilizador](usage-flows.md)
   - [Livros](../visualize/workbooks-overview.md)
