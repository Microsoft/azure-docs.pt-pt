---
title: 'Quickstart: Monitorize websites com Insights de Aplicação do Monitor Azure'
description: Neste arranque rápido, aprenda a configurar a monitorização do website do lado do cliente/navegador com o Azure Monitor Application Insights.
ms.topic: quickstart
ms.date: 07/15/2019
ms.custom: mvc
ms.openlocfilehash: b47f3ce1ebed12d14dffd68e87dd013bb86218ea
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801642"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Quickstart: Comece a monitorizar o seu website com insights de aplicação do Monitor Azure

Neste arranque rápido, aprende a adicionar ao seu website os Insights De Aplicação De código aberto JavaScript SDK. Também aprende a compreender melhor a experiência cliente/navegador para os visitantes do seu website.

Com o Azure Monitor Application Insights, pode monitorizar facilmente o seu site quanto à disponibilidade, ao desempenho e à utilização. Também pode identificar e diagnosticar erros rapidamente na sua aplicação sem ter de esperar que um utilizador os comunique. O Application Insights fornece capacidades de monitorização do lado do servidor e do lado do cliente/navegador.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Um site ao qual pode adicionar os Insights de Aplicação JavaScript SDK.

## <a name="enable-application-insights"></a>Ativar o Application Insights

Os Insights de Aplicação podem recolher dados de telemetria de qualquer aplicação ligada à Internet que esteja a funcionar no local ou na nuvem. Utilize os seguintes passos para visualizar estes dados:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Selecione **Criar uma** > **ferramenta de** > gestão de recursos**Aplicações Insights**.

   > [!NOTE]
   >Se esta for a primeira vez que cria um recurso Deinsights de Aplicação, consulte [Criar um recurso Deinsights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)de Aplicação .
1. Quando a caixa de configuração aparecer, utilize a seguinte tabela para completar os campos de entrada:

    | Definições        | Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor Exclusivo Global | O nome que identifica a aplicação que está a monitorizar. |
   | **Grupo de Recursos**     | myResourceGroup      | O nome do novo grupo de recursos para acolher dados de Insights de Aplicação. Pode criar um novo grupo de recursos ou utilizar um existente. |
   | **Localização** | E.U.A. Leste | Escolha um local perto de si, ou perto do local onde a sua aplicação está hospedada. |
1. Selecione **Criar**.

## <a name="create-an-html-file"></a>Criar um ficheiro HTML

1. No computador local, crie um ficheiro chamado ``hello_world.html``. Para este exemplo, crie o ficheiro na raiz da ``C:\hello_world.html``unidade C para que se pareça com .
1. Copiar e colar o ``hello_world.html``seguinte script em:

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

## <a name="configure-application-insights-sdk"></a>Configure Insights de Aplicação SDK

1. Selecione**Essentials**de **visão geral** > e, em seguida, copie a chave de **instrumentação**da sua aplicação .

   ![Formulário de recurso novo do Application Insights](media/website-monitoring/instrumentation-key-001.png)

1. Adicione o seguinte ``hello_world.html`` script ao ``</head>`` seu ficheiro antes da etiqueta de fecho:

   ```javascript
   <script type="text/javascript">
      var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
      {
         instrumentationKey:"INSTRUMENTATION_KEY"
      }
      );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
   </script>
   ```

1. Edite ``hello_world.html`` e adicione a chave de instrumentação.

1. Abra ``hello_world.html`` numa sessão de browser local. Esta ação cria uma visão de página única. Pode atualizar o seu navegador para gerar múltiplas visualizações de página de teste.

## <a name="monitor-your-website-in-the-azure-portal"></a>Monitorize o seu website no portal Azure

1. Reabra a página **de visão geral** da aplicação insights no portal Azure para ver detalhes da sua aplicação em execução. A página **'Overview'** é onde recuperou a chave de instrumentação.

   Os quatro gráficos predefinidos na página de descrição geral estão confinados aos dados da aplicação do lado do servidor. Como estamos a instrumentalar as interações cliente/navegador com o JavaScript SDK, esta vista em particular não se aplica a menos que também tenhamos um SDK do lado do servidor instalado.

1. Selecione o](media/website-monitoring/006.png)ícone do mapa de aplicações **do Analytics** ![.  Esta ação abre o **Analytics**, que fornece uma linguagem de consulta rica para analisar todos os dados recolhidos pela Application Insights. Para visualizar dados relacionados com os pedidos do navegador do lado do cliente, execute a seguinte consulta:

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

1. Volte à página **Descrição geral**. Sob o cabeçalho **Investigar,** selecione **Browser**, e, em seguida, selecione **Performance**.  As métricas relacionadas com o desempenho do seu website aparecem. Há uma visão correspondente para analisar falhas e exceções no seu site. Pode selecionar **Amostras** para aceder aos detalhes da [transação de ponta a ponta](../../azure-monitor/app/transaction-diagnostics.md).

   ![Gráficos de métricas de servidor](./media/website-monitoring/browser-performance.png)

1. No menu principal de Insights de Aplicação, sob o cabeçalho **de Utilização,** selecione [**Utilizadores**](../../azure-monitor/app/usage-segmentation.md) para começar a explorar as [ferramentas de análise](../../azure-monitor/app/usage-overview.md)de comportamento do utilizador . Como estamos a testar a partir de uma única máquina, só veremos dados para um utilizador. Para um site ao vivo, a distribuição dos utilizadores pode ser assim:

     ![Gráfico de utilizadores](./media/website-monitoring/usage-users.png)

1. Para um site mais complexo com várias páginas, pode utilizar a ferramenta [**User Flows**](../../azure-monitor/app/usage-flows.md) para seguir o caminho que os visitantes percorrem através das várias partes do seu website.

   ![Visualização dos Fluxos de Utilizador](./media/website-monitoring/user-flows.png)

Para saber configurações mais avançadas para monitorizar websites, consulte a [referência JavaScript SDK API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se pretende continuar a trabalhar com quickstarts ou tutoriais adicionais, não limpe os recursos criados neste arranque rápido. Caso contrário, utilize os seguintes passos para eliminar todos os recursos criados por este arranque rápido no portal Azure.

> [!NOTE]
> Se usou um grupo de recursos existente, as seguintes instruções não funcionarão. Em vez disso, pode simplesmente eliminar o recurso individual de Insights de Aplicação. Tenha em mente que, ao eliminar um grupo de recursos, todos os recursos que são membros desse grupo também são eliminados.

1. No menu esquerdo do portal Azure, selecione **Grupos de Recursos**e, em seguida, selecione **myResourceGroup** ou o nome do seu grupo de recursos temporários.
1. Na página do grupo de recursos, selecione **Eliminar**, introduza o **myResourceGroup** na caixa de texto e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Encontrar e diagnosticar problemas de desempenho](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
