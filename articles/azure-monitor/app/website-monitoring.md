---
title: 'Quickstart: Monitorize websites com Insights de Aplicação do Monitor Azure'
description: Neste quickstart, aprenda a configurar a monitorização do site do lado do cliente/navegador com o Azure Monitor Application Insights.
ms.topic: quickstart
ms.date: 08/19/2020
ms.custom: mvc
ms.openlocfilehash: 0a30fb12e93058690fbd7611ae64fc0a7dd96f64
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100583447"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Quickstart: Comece a monitorizar o seu website com insights de aplicação do Monitor Azure

Neste arranque rápido, aprende a adicionar o SDK de aplicações de código aberto ao seu website. Também aprende a entender melhor a experiência do lado do cliente/navegador para os visitantes do seu website.

Com o Azure Monitor Application Insights, pode monitorizar facilmente o seu site quanto à disponibilidade, ao desempenho e à utilização. Também pode identificar e diagnosticar erros rapidamente na sua aplicação sem ter de esperar que um utilizador os comunique. O Application Insights fornece tanto a monitorização do lado do servidor como as capacidades de monitorização do lado do cliente/browser.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Um website ao qual pode adicionar o SDK JavaScript De Aplicações.

## <a name="enable-application-insights"></a>Ativar o Application Insights

O Application Insights pode recolher dados de telemetria a partir de qualquer aplicação ligada à Internet que esteja a funcionar no local ou na nuvem. Utilize os seguintes passos para visualizar estes dados:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Selecione **Criar um recurso**  >  **Ferramentas de Gestão** de  >  **Informações Insights** de aplicação .

   > [!NOTE]
   >Se esta for a sua primeira vez a criar um recurso Application Insights, consulte [Criar um recurso 'Insights de Aplicação'.](./create-new-resource.md)
1. Quando a caixa de configuração aparecer, utilize a seguinte tabela para completar os campos de entrada:

    | Definições        | Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor Exclusivo Global | O nome que identifica a aplicação que está a monitorizar. |
   | **Grupo de Recursos**     | myResourceGroup      | O nome do novo grupo de recursos para hospedar dados de Insights de Aplicação. Pode criar um novo grupo de recursos ou utilizar um existente. |
   | **Localização** | E.U.A. Leste | Escolha um local perto de si ou perto do local onde a sua aplicação está hospedada. |
1. Selecione **Criar**.

## <a name="create-an-html-file"></a>Criar um ficheiro HTML

1. No computador local, crie um ficheiro chamado ``hello_world.html``. Para este exemplo, crie o ficheiro na raiz da unidade C para que pareça ``C:\hello_world.html`` .
1. Copiar e colar o seguinte script em ``hello_world.html`` :

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations, visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-application-insights-sdk"></a>Configurar a aplicação Insights SDK

1. Selecione   >  **Overview Essentials** e, em seguida, copie a Chave de Instrumentação da sua **aplicação**.

   ![Formulário de recurso novo do Application Insights](media/website-monitoring/instrumentation-key-001.png)

1. Adicione o seguinte script ao seu ``hello_world.html`` ficheiro antes da etiqueta de ``</head>`` fecho:

    ```javascript
    <script type="text/javascript">
    !function(T,l,y){var S=T.location,u="script",k="instrumentationKey",D="ingestionendpoint",C="disableExceptionTracking",E="ai.device.",I="toLowerCase",b="crossOrigin",w="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"4",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[I](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,p,l,u;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][I]()]=i[1])}if(!e[D]){var r=e.endpointsuffix,o=r?e.location:null;e[D]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[k]||d[k]||"",p=s[D],l=p?p+"/v2/track":config.endpointUrl,(u=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=l,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),u.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,l)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:w,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(w,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(u,l))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(u);n.src=h;var e=y[b];return!e&&""!==e||"undefined"==n[b]||(n[b]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(u)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[C]&&!0!==s[C]){method="onerror",t(["_"+method]);var c=T[method];T[method]=function(e,t,n,a,i){var r=c&&c(e,t,n,a,i);return!0!==r&&m["_"+method]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);(T[t]=n).queue&&0===n.queue.length&&n.trackPageView({})}(window,document,{
    src: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js", // The SDK URL Source
    //name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
    //ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
    //useXhr: 1, // Use XHR instead of fetch to report failures (if available),
    //crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag 
    cfg: { // Application Insights Configuration
        instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
        /* ...Other Configuration Options... */
    }});
    </script>
    ```
    
1. Edite ``hello_world.html`` e adicione a chave de instrumentação.

1. Abra ``hello_world.html`` numa sessão de browser local. Esta ação cria uma única visualização de página. Pode refrescar o seu navegador para gerar várias visualizações de página de teste.

## <a name="monitor-your-website-in-the-azure-portal"></a>Monitorize o seu website no portal Azure

1. Reabra a página **'Insights Overview'** da aplicação no portal Azure para ver os detalhes da sua aplicação atualmente em execução. A **página 'Visão Geral'** é onde recuperou a sua chave de instrumentação.

   Os quatro gráficos predefinidos na página de descrição geral estão confinados aos dados da aplicação do lado do servidor. Como estamos a instrumentar as interações do lado do cliente/navegador com o SDK JavaScript, esta visão em particular não se aplica a menos que também tenhamos um SDK do lado do servidor instalado.

1. Selecione o ícone do mapa de aplicações **de analítica** ![ ](media/website-monitoring/006.png) .  Esta ação abre **o Analytics,** que fornece uma linguagem de consulta rica para analisar todos os dados recolhidos pela Application Insights. Para visualizar dados relacionados com os pedidos do navegador do lado do cliente, execute a seguinte consulta:

    ```kusto
    // average pageView duration by name
    let timeGrain=1s;
    let dataset=pageViews
    // additional filters can be applied here
    | where timestamp > ago(15m)
    | where client_Type == "Browser" ;
    // calculate average pageView duration for all pageViews
    dataset
    | summarize avg(duration) by bin(timestamp, timeGrain)
    | extend pageView='Overall'
    // render result in a chart
    | render timechart
    ```

   ![Gráfico de análise de pedidos de utilizador durante um período de tempo](./media/website-monitoring/analytics-query.png)

1. Volte à página **Descrição geral**. Sob o cabeçalho **Investigar,** selecione **Browser** e, em seguida, selecione **Performance**.  Métricas relacionadas com o desempenho do seu site aparecem. Existe uma visão correspondente para analisar falhas e exceções no seu website. Pode selecionar **amostras** para aceder [aos dados de transação de ponta a ponta.](./transaction-diagnostics.md)

   ![Gráficos de métricas de servidor](./media/website-monitoring/browser-performance.png)

1. No menu principal de Insights de Aplicação, no cabeçalho **De Utilização,** selecione [**Utilizadores**](./usage-segmentation.md) para começar a explorar as [ferramentas de análise](./usage-overview.md)de comportamento do utilizador . Como estamos a testar a partir de uma única máquina, só veremos dados para um utilizador. Para um site ao vivo, a distribuição dos utilizadores pode ser assim:

     ![Gráfico de utilizadores](./media/website-monitoring/usage-users.png)

1. Para um site mais complexo com várias páginas, pode utilizar a ferramenta Fluxos de Utilizador para rastrear o caminho que os [**visitantes**](./usage-flows.md) percorrem as várias partes do seu website.

   ![Visualização dos Fluxos de Utilizador](./media/website-monitoring/user-flows.png)

Para obter configurações mais avançadas para monitorizar websites, consulte a [referência API do JavaScript SDK](./javascript.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Se planeia continuar a trabalhar com quickstarts ou tutoriais adicionais, não limpe os recursos criados neste arranque rápido. Caso contrário, utilize os seguintes passos para eliminar todos os recursos criados por este quickstart no portal Azure.

> [!NOTE]
> Se utilizar um grupo de recursos existente, as seguintes instruções não funcionarão. Em vez disso, pode simplesmente eliminar o recurso Individual Application Insights. Tenha em mente que, quando elimina um grupo de recursos, todos os recursos que são membros desse grupo também são eliminados.

1. No menu esquerdo no portal Azure, selecione **grupos de Recursos** e, em seguida, selecione **myResourceGroup** ou o nome do seu grupo de recursos temporários.
1. Na sua página de grupo de recursos, selecione **Delete**, insira o **myResourceGroup** na caixa de texto e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Encontrar e diagnosticar problemas de desempenho](../logs/log-query-overview.md)

