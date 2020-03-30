---
title: 'Quickstart: Monitorize websites com Insights de Aplicação do Monitor Azure'
description: Fornece instruções de arranque rápido configuração de website cliente/browser-side com Insights de aplicação do Monitor Azure
ms.topic: quickstart
ms.date: 07/15/2019
ms.custom: mvc
ms.openlocfilehash: 495c40ca8e383dd5a3cf3ba9e5bd42e2936ea015
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80132372"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Quickstart: Comece a monitorizar o seu website com insights de aplicação do Monitor Azure

Neste arranque rápido, aprende a adicionar ao seu website os Insights De Aplicação De código aberto JavaScript SDK. Também aprende a compreender melhor a experiência cliente/navegador para os visitantes do seu website.

Com o Azure Monitor Application Insights, pode monitorizar facilmente o seu site quanto à disponibilidade, ao desempenho e à utilização. Também pode identificar e diagnosticar erros rapidamente na sua aplicação sem ter de esperar que um utilizador os comunique. O Application Insights fornece capacidades de monitorização do lado do servidor e do lado do cliente/navegador.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Um site ao qual pode adicionar os Insights de Aplicação JavaScript SDK.

## <a name="enable-application-insights"></a>Ativar o Application Insights

O Application Insights pode recolher dados telemétricos de qualquer aplicação ligada à Internet, executada no local ou na cloud. Utilize os passos seguintes para começar a ver estes dados.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Criar uma** > **ferramenta de** > gestão de recursos**Aplicações Insights**.

   > [!NOTE]
   >Se for a primeira vez que cria um recurso de Insights de Aplicação, pode aprender mais visitando o artigo [Create a Application Insights Resource.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)

   É apresentada uma caixa de configuração; utilize a tabela abaixo para preencher os campos de texto.

    | Definições        | Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor Exclusivo Global | Nome que identifique a app que está a monitorizar |
   | **Grupo de Recursos**     | myResourceGroup      | Nome para o novo grupo de recursos para acolher dados de Insights de Aplicação. Pode criar um novo grupo de recursos ou utilizar um existente. |
   | **Localização** | E.U.A. Leste | Escolha uma localização perto de si ou perto do local onde a sua aplicação está alojada |

3. Clique em **Criar**.

## <a name="create-an-html-file"></a>Criar um ficheiro HTML

1. No computador local, crie um ficheiro chamado ``hello_world.html``. Neste exemplo, o ficheiro será colocado na raiz da unidade C: em ``C:\hello_world.html``.
2. Copie o script abaixo para ``hello_world.html``:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-application-insights-sdk"></a>Configure Insights de Aplicação SDK

1. Selecione **visão geral** > **Essencial** > Copie a chave de **instrumentação**da sua aplicação .

   ![Formulário de recurso novo do Application Insights](media/website-monitoring/instrumentation-key-001.png)

2. Adicione o script seguinte a ``hello_world.html`` antes da etiqueta de fecho ``</head>``:

   ```javascript
   <script type="text/javascript">
      var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
      {
         instrumentationKey:"INSTRUMENTATION_KEY"
      }
      );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
   </script>
   ```

3. Edite ``hello_world.html`` e adicione a chave de instrumentação.

4. Abra ``hello_world.html`` numa sessão de browser local. Esta ação cria uma única visão de página. Pode atualizar o browser para gerar várias vistas de página de teste.

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar a monitorização no portal do Azure

1. Agora pode reabrir a página de **visão geral** da Aplicação Insights no portal Azure para ver detalhes sobre a sua aplicação em execução. A página **'Overview'** é onde recuperou a chave de instrumentação. Os quatro gráficos predefinidos na página de descrição geral estão confinados aos dados da aplicação do lado do servidor. Uma vez que estamos a instrumentalar as interações cliente/navegador com o JavaScript SDK, esta vista em particular não se aplica a menos que também tenhamos um SDK do lado do servidor instalado.

2. Clique no ![ícone do Mapa da Aplicação](media/website-monitoring/006.png) **Analytics**.  Esta ação abre o **Analytics**, que fornece uma linguagem de consulta rica para analisar todos os dados recolhidos pela Application Insights. Para ver dados relacionados com os pedidos do browser do lado do cliente, execute a seguinte consulta:

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

3. Volte à página **Descrição geral**. Clique em **Browser** no cabeçalho **Investigar** e, em seguida, selecione **Desempenho**. Aqui, pode encontrar as métricas relacionadas com o desempenho do seu site. Há também uma visão correspondente para analisar falhas e exceções no seu site. Pode clicar em **Exemplos** para explorar os detalhes de transação individual. A partir daqui, pode aceder à experiência de [detalhes de transação ponto a ponto](../../azure-monitor/app/transaction-diagnostics.md).

   ![Gráficos de métricas de servidor](./media/website-monitoring/browser-performance.png)

4. Para começar a explorar as [ferramentas de análise de comportamento do utilizador](../../azure-monitor/app/usage-overview.md), no menu principal do Application Insights, selecione [**Utilizadores**](../../azure-monitor/app/usage-segmentation.md) no cabeçalho **Utilização**. Já que estamos a testar a partir de uma única máquina, só veremos dados para um utilizador. Para um site em direto, a distribuição de utilizadores será semelhante à seguinte:

     ![Gráfico de utilizadores](./media/website-monitoring/usage-users.png)

5. Se tivéssemos instrumentado um site mais complexo com múltiplas páginas, outra ferramenta útil seria os [**Fluxos de Utilizador**](../../azure-monitor/app/usage-flows.md). Com os **Fluxos de Utilizador**, pode controlar o caminho seguido pelos visitantes nas várias partes do seu site.

   ![Visualização dos Fluxos de Utilizador](./media/website-monitoring/user-flows.png)

Para obter configurações mais avançadas para monitorização de sites, veja a [referência da API do SDK JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se pretende continuar a trabalhar com quickstarts adicionais ou com os tutoriais, não limpe os recursos criados neste arranque rápido. Caso contrário, se não pretende continuar, utilize os seguintes passos para eliminar todos os recursos criados por este arranque rápido no portal Azure.

> [!NOTE]
> Se utilizou um grupo de recursos existente, as instruções abaixo não funcionarão e terá de eliminar o recurso Individual Application Insights. Tenha em mente que sempre que eliminar um grupo de recursos, todos os recursos que são membros desse grupo serão eliminados.

1. No menu esquerdo do portal Azure, clique em **grupos de recursos,** e clique no **myResourceGroup** ou no nome do seu grupo de recursos temporários.
2. Na página do grupo de recursos, clique em **Eliminar**, escreva **myResourceGroup** na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Encontrar e diagnosticar problemas de desempenho](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
