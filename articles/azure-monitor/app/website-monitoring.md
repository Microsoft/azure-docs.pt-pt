---
title: 'Quickstart: Monitorize websites com Insights de Aplicação do Monitor Azure'
description: Neste quickstart, aprenda a configurar a monitorização do site do lado do cliente/navegador com o Azure Monitor Application Insights.
ms.topic: quickstart
ms.date: 07/15/2019
ms.custom: mvc
ms.openlocfilehash: fa2ecd5d953ec0411a122dc7107ce23de1ae5bc6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87014053"
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
1. Selecione **Criar um recurso**  >  **Ferramentas de Gestão**de  >  **Informações Insights**de aplicação .

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

1. Selecione **Overview**  >  **Overview Essentials**e, em seguida, copie a Chave de Instrumentação da sua **aplicação**.

   ![Formulário de recurso novo do Application Insights](media/website-monitoring/instrumentation-key-001.png)

1. Adicione o seguinte script ao seu ``hello_world.html`` ficheiro antes da etiqueta de ``</head>`` fecho:

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

1. Volte à página **Descrição geral**. Sob o cabeçalho **Investigar,** selecione **Browser**e, em seguida, selecione **Performance**.  Métricas relacionadas com o desempenho do seu site aparecem. Existe uma visão correspondente para analisar falhas e exceções no seu website. Pode selecionar **amostras** para aceder [aos dados de transação de ponta a ponta.](../../azure-monitor/app/transaction-diagnostics.md)

   ![Gráficos de métricas de servidor](./media/website-monitoring/browser-performance.png)

1. No menu principal de Insights de Aplicação, no cabeçalho **De Utilização,** selecione [**Utilizadores**](../../azure-monitor/app/usage-segmentation.md) para começar a explorar as [ferramentas de análise](../../azure-monitor/app/usage-overview.md)de comportamento do utilizador . Como estamos a testar a partir de uma única máquina, só veremos dados para um utilizador. Para um site ao vivo, a distribuição dos utilizadores pode ser assim:

     ![Gráfico de utilizadores](./media/website-monitoring/usage-users.png)

1. Para um site mais complexo com várias páginas, pode utilizar a ferramenta Fluxos de Utilizador para rastrear o caminho que os [**visitantes**](../../azure-monitor/app/usage-flows.md) percorrem as várias partes do seu website.

   ![Visualização dos Fluxos de Utilizador](./media/website-monitoring/user-flows.png)

Para obter configurações mais avançadas para monitorizar websites, consulte a [referência API do JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se planeia continuar a trabalhar com quickstarts ou tutoriais adicionais, não limpe os recursos criados neste arranque rápido. Caso contrário, utilize os seguintes passos para eliminar todos os recursos criados por este quickstart no portal Azure.

> [!NOTE]
> Se utilizar um grupo de recursos existente, as seguintes instruções não funcionarão. Em vez disso, pode simplesmente eliminar o recurso Individual Application Insights. Tenha em mente que, quando elimina um grupo de recursos, todos os recursos que são membros desse grupo também são eliminados.

1. No menu esquerdo no portal Azure, selecione **grupos de Recursos**e, em seguida, selecione **myResourceGroup** ou o nome do seu grupo de recursos temporários.
1. Na sua página de grupo de recursos, selecione **Delete**, insira o **myResourceGroup** na caixa de texto e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Encontrar e diagnosticar problemas de desempenho](../log-query/log-query-overview.md)
