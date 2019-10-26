---
title: Monitorize um site do SharePoint com o Application Insights
description: Comece a monitorizar uma nova aplicação com uma nova chave de instrumentação
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/11/2018
ms.openlocfilehash: 271ef590d7cdbefc824a7f80aef110b0c08b6d6d
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899875"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>Monitorize um site do SharePoint com o Application Insights
O Azure Application Insights monitoriza a disponibilidade, o desempenho e a utilização das suas aplicações. Aqui, irá aprender a configurá-lo para um site do SharePoint.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights
No [Portal do Azure](https://portal.azure.com), crie um novo recurso do Application Insights. Escolha ASP.NET como o tipo de aplicação.

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
var appInsights=window.appInsights||function(a){
  function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
  }({
      instrumentationKey:"<your instrumentation key>"
  });
  
window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
</script>
```

Insira o script logo antes da &lt;/Head&gt; marca de cada página que você deseja controlar. Se seu site tiver uma página mestra, você poderá colocar o script lá. Por exemplo, num projeto de MVC do ASP.NET, colocaria em View\Shared\_Layout.cshtml

O script contém a chave de instrumentação que direciona a telemetria para o recurso do Application Insights.

### <a name="add-the-code-to-your-site-pages"></a>Adicione o código às páginas do seu site
#### <a name="on-the-master-page"></a>Na página mestra
Se conseguir editar a página mestra no site, irá fornecer a monitorização para todas as páginas no site.

Confira a página mestra e edite-a através do SharePoint Designer ou qualquer outro editor.

![](./media/sharepoint/03-master.png)

Adicione o código imediatamente antes da etiqueta </head>. 

![](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>Ou em páginas individuais
Para monitorizar um conjunto limitado de páginas, adicione o script em separado para cada página. 

Insira uma peça Web e incorpore o fragmento de código na mesma.

![](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>Veja dados sobre a sua aplicação
Volte a implementar a aplicação.

Regresse ao seu painel da aplicação no [portal do Azure](https://portal.azure.com).

Os primeiros eventos irão aparecer na Pesquisa. 

![](./media/sharepoint/09-search.png)

Clique em Atualizar após alguns segundos, se estiver à espera de mais dados.

## <a name="capturing-user-id"></a>Capturar Id do Utilizador
O fragmento de código da página Web standard não captura o ID do utilizador do SharePoint, mas pode fazê-lo com uma pequena modificação.

1. Copie a chave de instrumentação a partir da lista pendente do Essentials no Application Insights. 

    ![](./media/sharepoint/02-props.png)

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



## <a name="next-steps"></a>Próximos Passos
* [Testes Web](../../azure-monitor/app/monitor-web-app-availability.md) para monitorizar a disponibilidade do seu site.
* [Application Insights](../../azure-monitor/app/app-insights-overview.md) para outros tipos de aplicação.

<!--Link references-->


