---
title: Monitorize um site do SharePoint com o Application Insights
description: Comece a monitorizar uma nova aplicação com uma nova chave de instrumentação
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: f07c317a61c91daf7a469c1f9eaf1fb80d4ecc49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91825280"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>Monitorize um site do SharePoint com o Application Insights

O Azure Application Insights monitoriza a disponibilidade, o desempenho e a utilização das suas aplicações. Aqui, irá aprender a configurá-lo para um site do SharePoint.

> [!NOTE]
> Devido a questões de segurança, não é possível adicionar diretamente o script descrito neste artigo às suas páginas web no UX moderno do SharePoint. Como alternativa, pode utilizar [o SharePoint Framework (SPFx)](/sharepoint/dev/spfx/extensions/overview-extensions) para construir uma extensão personalizada que pode utilizar para instalar Insights de Aplicação nos seus sites SharePoint.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights
No [Portal do Azure](https://portal.azure.com), crie um recurso do Application Insights novo. Escolha ASP.NET como o tipo de aplicação.

![Clicar em Propriedades, selecionar a chave e premir Ctrl+C](./media/sharepoint/001.png)

A janela que se abre é o local onde verá os dados de utilização e de desempenho da sua aplicação. Para voltar à mesma da próxima vez em que iniciar sessão no Azure, deve encontrar um mosaico para o mesmo no ecrã Início. Em alternativa clique em Procurar para encontrá-lo.

## <a name="add-the-script-to-your-web-pages"></a>Adicionar o script às suas páginas Web

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

Insira o script pouco antes &lt; da etiqueta /cabeça &gt; de cada página que pretende rastrear. Se o seu site tiver uma página principal, pode colocar o script lá. Por exemplo, num projeto de MVC do ASP.NET, colocaria em View\Shared\_Layout.cshtml

O script contém a chave de instrumentação que direciona a telemetria para o recurso do Application Insights.

### <a name="add-the-code-to-your-site-pages"></a>Adicione o código às páginas do seu site
#### <a name="on-the-master-page"></a>Na página mestra
Se conseguir editar a página mestra no site, irá fornecer a monitorização para todas as páginas no site.

Confira a página mestra e edite-a através do SharePoint Designer ou qualquer outro editor.

![Screenshot que mostra como editar a página principal usando o Sharepoing Designer ou outro editor.](./media/sharepoint/03-master.png)

Adicione o código pouco antes da </head> tag. 

![Screenshot que mostra onde adicionar o código à página do seu site.](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>Ou em páginas individuais
Para monitorizar um conjunto limitado de páginas, adicione o script em separado para cada página. 

Insira uma peça Web e incorpore o fragmento de código na mesma.

![Screenshot que mostra adicionar o script para monitorizar um conjunto limitado de páginas.](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>Veja dados sobre a sua aplicação
Volte a implementar a aplicação.

Regresse ao seu painel da aplicação no [portal do Azure](https://portal.azure.com).

Os primeiros eventos irão aparecer na Pesquisa. 

![Screenshot que mostra os novos dados que pode ver na aplicação.](./media/sharepoint/09-search.png)

Clique em Atualizar após alguns segundos, se estiver à espera de mais dados.

## <a name="capturing-user-id"></a>Capturar Id do Utilizador
O fragmento de código da página Web standard não captura o ID do utilizador do SharePoint, mas pode fazê-lo com uma pequena modificação.

1. Copie a chave de instrumentação a partir da lista pendente do Essentials no Application Insights. 

    ![Screenshot que mostra a cópia da instrumentação da aplicação a partir do dropdown Essentials em Application Insights.](./media/sharepoint/02-props.png)

1. Substitua a chave de instrumentação de "XXXX" no fragmento abaixo. 
2. Incorpore o script na sua aplicação do SharePoint, em vez do fragmento que obtém a partir do portal.

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## <a name="next-steps"></a>Passos Seguintes
* [Testes Web](./monitor-web-app-availability.md) para monitorizar a disponibilidade do seu site.
* [Application Insights](./app-insights-overview.md) para outros tipos de aplicação.

<!--Link references-->
