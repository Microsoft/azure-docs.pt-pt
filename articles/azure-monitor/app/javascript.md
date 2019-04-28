---
title: Azure Application Insights para aplicações Web JavaScript | Microsoft Docs
description: Obtenha contagens de sessões e visualizações de páginas, dados de clientes Web e controle padrões de utilização. Detete exceções e problemas de desempenho em páginas Web de JavaScript.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b710d09-6ab4-4004-b26a-4fa840039500
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: fee172eccd79fd28e281b2beece9702630ac39b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60901737"
---
# <a name="application-insights-for-web-pages"></a>Application Insights para páginas Web
Saiba mais sobre o desempenho e a utilização da sua aplicação ou página Web. Se adicionar o [Application Insights](app-insights-overview.md) ao seu script de página, obterá as temporizações de carregamentos de página e chamadas AJAX, contagens e detalhes de falhas de AJAX e exceções de browser, bem como contagens de utilizadores e sessões. Todas estas podem ser segmentadas por página, SO de cliente e versão do browser, geolocalização e outras dimensões. Pode definir alertas em contagens de falhas ou carregamento lento de página. E, ao inserir chamadas de rastreio no seu código JavaScript, pode controlar a utilização das diferentes funcionalidades da sua aplicação da página Web.

O Application Insights pode ser utilizado com quaisquer páginas Web - basta adicionar um pequeno pedaço de JavaScript. Se o seu serviço Web for [Java](java-get-started.md) ou [ASP.NET](asp-net.md), pode integrar telemetria a partir do seu servidor e clientes.

![Em portal.azure.com, abra o recurso da sua aplicação e clique em Browser](media/javascript/03.png)

Precisa de uma subscrição do [Microsoft Azure](https://azure.com). Se a sua equipa tiver uma subscrição organizacional, peça ao proprietário para adicionar a sua conta Microsoft à mesma.

## <a name="set-up-application-insights-for-your-web-page"></a>Configurar o Application Insights para a sua página Web
Adicione o fragmento de código do carregador às suas páginas Web, da seguinte forma.

### <a name="open-or-create-application-insights-resource"></a>Abra ou crie um recurso do Application Insights
O recurso do Application Insights é onde são apresentados os dados sobre o desempenho e a utilização da sua página. 

Inicie sessão no [portal do Azure](https://portal.azure.com).

Se já configurou a monitorização no lado do servidor da sua aplicação, já tem um recurso:

![Escolha Procurar, Serviços de Programação, Application Insights.](media/javascript/01-find.png)

Se não tiver um, crie-o:

![Escolha Novo, Serviços de Programação, Application Insights.](media/javascript/01-create.png)

*Já tem dúvidas?* [Mais sobre a criação de um recurso](create-new-resource.md ).

### <a name="add-the-sdk-script-to-your-app-or-web-pages"></a>Adicione o script SDK à sua aplicação ou páginas Web

```HTML
<!-- 
To collect user behavior analytics about your application, 
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

Insira o script imediatamente antes da etiqueta `</head>` de todas as páginas que quer controlar. Se o seu site tiver uma página mestra, pode colocar aí o script. Por exemplo:

* Num projeto MVC do ASP.NET, coloca-lo-ia em `View\Shared\_Layout.cshtml`
* Num site do SharePoint, no painel de controlo, abra [Definições do site / Página mestra](sharepoint.md).

O script contém a chave de instrumentação que direciona os dados para o recurso do Application Insights. 

([Explicação mais aprofundada do script.](https://apmtips.com/blog/2015/03/18/javascript-snippet-explained/))

## <a name="detailed-configuration"></a>Configuração detalhada
Existem vários [parâmetros](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) que pode definir, embora na maioria dos casos não deverá ser preciso. Por exemplo, pode desativar ou limitar o número de chamadas Ajax reportadas por visualização de página (para reduzir o tráfego). Pode ainda configurar o modo de depuração para que a telemetria se mova rapidamente no pipeline sem estar em lote.

Para definir estes parâmetros, procure esta linha no fragmento de código e adicione mais itens separados por vírgulas a seguir à mesma:

    })({
      instrumentationKey: "..."
      // Insert here
    });

Os [parâmetros disponíveis](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) incluem:

    // Send telemetry immediately without batching.
    // Remember to remove this when no longer required, as it
    // can affect browser performance.
    enableDebug: boolean,

    // Don't log browser exceptions.
    disableExceptionTracking: boolean,

    // Don't log ajax calls.
    disableAjaxTracking: boolean,

    // Limit number of Ajax calls logged, to reduce traffic.
    maxAjaxCallsPerView: 10, // default is 500

    // Time page load up to execution of first trackPageView().
    overridePageViewDuration: boolean,

    // Set dynamically for an authenticated user.
    accountId: string,

## <a name="run"></a>Executar a aplicação
Execute a sua aplicação Web, utilize-a durante algum tempo para gerar telemetria e espere alguns segundos. Pode executá-la com a tecla **F5** do seu computador de desenvolvimento ou publicá-la e permitir que os utilizadores a testem.

Se quiser verificar na telemetria o que uma aplicação Web está a enviar para o Application Insights, utilize as ferramentas de depuração do seu browser (**F12** em muitos browsers). Os dados são enviados para dc.services.visualstudio.com.

## <a name="explore-your-browser-performance-data"></a>Explorar os dados de desempenho do browser
Abra o painel Browser para mostrar dados de desempenho agregados dos browsers dos seus utilizadores.

![Em portal.azure.com, abra o recurso da sua aplicação e clique em Definições, Browser](./media/javascript/03.png)

Ainda não existem dados? Clique em **Atualizar** no início da página. Ainda nada? Veja [Resolução de Problemas](troubleshoot-faq.md).

O painel Browser é um [painel do Explorador de Métricas](metrics-explorer.md) que predefine filtros e seleções de gráficos. Se quiser, pode editar o intervalo de tempo, os filtros e a configuração de gráficos e guardar o resultado como um favorito. Clique em **Restaurar predefinições** para voltar à configuração de painel original.

## <a name="page-load-performance"></a>Desempenho de carregamento de página
Na parte superior, está um gráfico segmentado dos tempos de carregamento de página. A altura total do gráfico representa o tempo médio para carregar e apresentar páginas a partir da sua aplicação nos browsers dos seus utilizadores. O tempo é medido desde que o browser envia o pedido HTTP inicial até que todos os eventos de carregamento síncronos tenham sido processados, incluindo esquema e scripts em execução. Não inclui tarefas assíncronas, como carregar peças Web a partir de chamadas AJAX.

O gráfico segmenta o tempo total de carregamento da página nas [temporizações padrão definidas pelo W3C](https://www.w3.org/TR/navigation-timing/#processing-model). 

![](./media/javascript/08-client-split.png)

Tenha em atenção que o tempo de *ligação de rede* é, muitas vezes, inferior ao que seria de esperar, porque corresponde a uma média de todos os pedidos do browser para o servidor. Muitos pedidos individuais têm um tempo de ligação de 0 porque já existe uma ligação ativa ao servidor.

### <a name="slow-loading"></a>Carregamento lento?
Os carregamentos lentos de página são uma das principais fontes de insatisfação para os utilizadores. Se o gráfico indicar carregamentos lentos de página, é fácil fazer alguma pesquisa de diagnóstico.

O gráfico mostra a média de todos os carregamentos de página na sua aplicação. Para ver se o problema está limitado a páginas específicas, procure mais abaixo no painel, onde existe uma grelha segmentada por URL de página:

![](./media/javascript/09-page-perf.png)

Repare na contagem de visualizações de página e no desvio padrão. Se a contagem de páginas for muito baixa, o problema não afeta muito os utilizadores. Um desvio padrão elevado (comparável à própria média) indica muita variação entre as medidas individuais.

**Amplie um URL e uma visualização de página.** Clique em qualquer nome de página para ver um painel de gráficos de browser, filtrado apenas para esse URL; e, em seguida, numa instância de uma visualização de página.

![](./media/javascript/35.png)

Clique em `...` para ver uma lista completa de propriedades para esse evento ou inspecione as chamadas Ajax e os eventos relacionados. As chamadas Ajax lentas afetam o tempo total de carregamento de página se forem síncronas. Eventos relacionados incluem pedidos de servidor para o mesmo URL (se tiver configurado o Application Insights no seu servidor Web).

**Desempenho da página ao longo do tempo.** De volta ao painel Browsers, altere a grelha Duração de carregamento da visualização de página para um gráfico de linhas para ver se ocorreram picos em alturas específicas:

![Clique no cabeçalho da grelha e selecione um novo tipo de gráfico](./media/javascript/10-page-perf-area.png)

**Segmentar por outras dimensões.** Talvez as suas páginas carreguem mais lentamente num determinado browser, SO de cliente ou localidade de utilizador? Adicione um novo gráfico e experimente a dimensão **Agrupar por**.

![](./media/javascript/21.png)

## <a name="ajax-performance"></a>Desempenho de AJAX
Certifique-se de que quaisquer chamadas AJAX nas suas páginas Web estão a ter um bom desempenho. São frequentemente utilizadas para preencher partes da sua página de modo assíncrono. Embora a página completa possa carregar de imediato, os seus utilizadores podem ficar frustrados a olhar para peças Web em branco, à espera que os dados apareçam no ecrã.

As chamadas AJAX feitas a partir da sua página Web são apresentadas no painel Browsers como dependências.

Existem gráficos de resumo na parte superior do painel:

![](./media/javascript/31.png)

e grelhas detalhadas mais abaixo:

![](./media/javascript/33.png)

Clique em qualquer linha para ver detalhes específicos.

> [!NOTE]
> Se eliminar o filtro Browsers no painel, tanto as dependências de servidor como de AJAX são incluídas nestes gráficos. Clique em Restaurar predefinições para reconfigurar o filtro.
> 
> 

**Para pesquisar chamadas Ajax falhadas**, desloque o ecrã para baixo até à grelha de falhas de Dependência e, em seguida, clique numa linha para ver instâncias específicas.

![](./media/javascript/37.png)

Clique em `...` para ver a telemetria completa de uma chamada Ajax.

### <a name="no-ajax-calls-reported"></a>Nenhuma chamada Ajax reportada?
As chamadas AJAX incluem quaisquer chamadas HTTP/HTTPS feitas a partir do script da sua página Web. Se não as vir reportadas, verifique se o fragmento de código não define os [parâmetros](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) `disableAjaxTracking` ou `maxAjaxCallsPerView`.

## <a name="browser-exceptions"></a>Exceções de browser
No painel Browsers, existe um gráfico de resumo de exceções e uma grelha dos tipos de exceção mais abaixo no painel.

![](./media/javascript/39.png)

Se não vir exceções de browser reportadas, verifique se o fragmento de código não define o [parâmetro](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) `disableExceptionTracking`.

## <a name="inspect-individual-page-view-events"></a>Inspecionar eventos individuais de visualização de página

Normalmente, a telemetria de visualizações de página é analisada pelo Application Insights e o utilizador vê apenas os relatórios cumulativos, cuja média de todos os utilizadores foi calculada. Mas, para fins de depuração, também pode ver eventos individuais de visualização de página.

No painel Pesquisa de Diagnóstico, defina Filtros como Visualização de Página.

![](./media/javascript/12-search-pages.png)

Selecione qualquer evento para ver mais detalhes. Na página de detalhes, clique em "…" para ver ainda mais detalhes.

> [!NOTE]
> Se usar [pesquisa](diagnostic-search.md), tenha em atenção que tem de corresponder palavras inteiras: "Sobr" e "bre" não correspondem "About".
> 
> 

Também pode utilizar o [Idioma de consulta do Log Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-tour) avançado para procurar visualizações de página.

### <a name="page-view-properties"></a>Propriedades de visualização de página
* **Duração de visualização de página** 
  
  * Por predefinição, corresponde ao tempo que demora a carregar a página, desde o pedido do cliente até ao carregamento total (incluindo ficheiros auxiliares, mas excluindo tarefas assíncronas, tais como chamadas Ajax). 
  * Se definir `overridePageViewDuration` na [configuração da página](#detailed-configuration), corresponde ao intervalo entre o pedido do cliente e a execução do primeiro `trackPageView`. Se moveu trackPageView da sua posição habitual após a inicialização do script, irá refletir um valor diferente.
  * Se `overridePageViewDuration` estiver definido e um argumento de duração for fornecido na chamada `trackPageView()`, o valor do argumento é utilizado em alternativa. 

## <a name="custom-page-counts"></a>Contagens personalizadas de páginas
Por predefinição, uma contagem de páginas ocorre sempre que uma nova página for carregada no browser do cliente.  Porém, pode querer contar visualizações de página adicionais. Por exemplo, uma página poderá apresentar o respetivo conteúdo em separadores e quer contar uma página quando o utilizador mudar de separador. Ou o código JavaScript na página poderá carregar conteúdo novo sem alterar o URL do browser.

Insira uma chamada de JavaScript como esta no ponto adequado do código de cliente:

    appInsights.trackPageView(myPageName);

O nome da página pode conter os mesmos carateres que um URL, mas qualquer caráter a seguir a "#" ou "?" é ignorado.

## <a name="usage-tracking"></a>Controlo de utilização
Quer descobrir o que os utilizadores fazem com a sua aplicação?

* [Saiba mais sobre as ferramentas de análise de comportamento do utilizador](usage-overview.md)
* [Saiba mais sobre a API de métricas e eventos personalizados](api-custom-events-metrics.md).

## <a name="video"></a> Vídeo


> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]



## <a name="next"></a> Passos seguintes
* [Controlar a utilização](usage-overview.md)
* [Métricas e eventos personalizados](api-custom-events-metrics.md)
* [Build-measure-learn](usage-overview.md)

