---
title: 'Quickstart: Monitor Node.js com Insights de Aplicação do Monitor Azure'
description: Fornece instruções para configurar rapidamente uma aplicação web Node.js para monitorização com Azure Monitor Application Insights
ms.subservice: application-insights
ms.topic: quickstart
author: lgayhardt
ms.author: lagayhar
ms.date: 07/12/2019
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: e5fc7c71c1ced4542f00fe862699442c6b43bc69
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210363"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>Quickstart: Comece a monitorizar a sua aplicação web Node.js com insights de aplicação Azure

Neste arranque rápido, adiciona-se a versão 0.22 do Application Insights SDK para Node.js a uma aplicação web Node.js existente.

Com o Azure Application Insights, pode monitorizar facilmente a sua aplicação Web quanto à disponibilidade, ao desempenho e à utilização. Também pode identificar e diagnosticar erros rapidamente na sua aplicação sem ter de esperar que um utilizador os comunique. A partir da versão 0.20 do SDK em diante, pode monitorizar pacotes de terceiros comuns, incluindo o MongoDB, MySQL e Redis.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Uma aplicação de Node.js funcional.

## <a name="enable-application-insights"></a>Ativar o Application Insights

O Application Insights pode recolher dados de telemetria a partir de qualquer aplicação ligada à Internet, quer esteja ou não a funcionar no local ou na nuvem. Utilize os passos seguintes para começar a ver estes dados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione **Criar um recurso** Developer  >  **tools**  >  **Application Insights**.

   ![Adicione um recurso Azure Application Insights](./media/nodejs-quick-start/azure-app-insights-create-resource.png)

   > [!NOTE]
   >Se esta for a sua primeira vez a criar um recurso Application Insights, pode aprender mais visitando o Doc [Criar um Recurso de Insights de Aplicação.](../app/create-new-resource.md)

   Aparece uma página de configuração; utilize a seguinte tabela para preencher os campos de entrada. 

    | Definições        | Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor Exclusivo Global | Nome que identifica a app que está a monitorizar |
   | **Grupo de Recursos**     | myResourceGroup      | Nome para o novo grupo de recursos para hospedar dados appInsights. Pode criar um novo grupo de recursos ou utilizar um existente. |
   | **Localização** | E.U.A. Leste | Escolha uma localização perto de si ou perto do local onde a sua aplicação está alojada |

3. Selecione **Criar**.

## <a name="configure-appinsights-sdk"></a>Configurar AppInsights SDK

1. Selecione **'Visão Geral'** e copie a Chave de Instrumentação da sua **aplicação**.

   ![Ver a Chave de Instrumentação de Insights de Aplicação](./media/nodejs-quick-start/azure-app-insights-instrumentation-key.png)

2. Adicionar o SDK do Application Insights para Node.js à sua aplicação. A partir da pasta raiz da sua aplicação, execute:

   ```bash
   npm install applicationinsights --save
   ```

3. Edite o primeiro ficheiro *.js* da sua aplicação e adicione as duas linhas abaixo à parte mais alta do seu script. Se estiver a utilizar a [aplicaçãoNode.js quickstart,](../../app-service/quickstart-nodejs.md)modificaria o ficheiro *index.js.* `<instrumentation_key>`Substitua-a pela tecla de instrumentação da sua aplicação. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Reinicie a aplicação.

> [!NOTE]
> Os dados demoram entre três e cinco minutos a aparecer no portal. Se esta aplicação for uma aplicação de teste de tráfego reduzido, tenha em atenção que a maioria das métricas só é capturada quando existem operações ou pedidos ativos em curso.

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar a monitorização no portal do Azure

1. Agora, pode reabrir a página **Descrição geral** do Application Insights no portal do Azure, onde obteve a sua chave de instrumentação, para ver detalhes sobre a aplicação que se encontra em execução.

   ![Menu geral de insights de aplicação](./media/nodejs-quick-start/azure-app-insights-overview-menu.png)

2. Selecione **mapa de aplicação** para um layout visual das relações de dependência entre os seus componentes de aplicação. Cada componente mostra KPIs, tais como carga, desempenho, falhas e alertas.

   ![Mapa da aplicação insights](./media/nodejs-quick-start/azure-app-insights-application-map.png)

3. Selecione o ícone do mapa de aplicações do ícone **app Analytics** ![ Ver em ](./media/nodejs-quick-start/azure-app-insights-analytics-icon.png) **Analytics**.  Esta ação abre **o Application Insights Analytics,** que fornece uma linguagem de consulta rica para analisar todos os dados recolhidos pela Application Insights. Neste caso, é gerada uma consulta que compõe a contagem de pedidos como um gráfico. Pode escrever as suas próprias consultas para analisar outros dados.

   ![Gráficos de Análise de Insights de Aplicação](./media/nodejs-quick-start/azure-app-insights-analytics-queries.png)

4. Volte à página de **Descrição geral** e examine os gráficos de KPI.  Este dashboard fornece estatísticas sobre o estado de funcionamento da aplicação, incluindo o número de pedidos recebidos, a duração desses pedidos e quaisquer falhas que ocorram.

   ![Gráficos de linha de tempo de visão geral da saúde de insights de aplicação](./media/nodejs-quick-start/azure-app-insights-health-overview.png)

   Para ativar o gráfico **Duração de Carregamento da Vista de Página** para preencher com **dados telemétricos do lado do cliente**, adicione este script a cada página que queira controlar:

   ```HTML
   <!-- 
   To collect user behavior analytics tools about your application, 
   insert the following script into each page you want to track.
   Place this code immediately before the closing </head> tag,
   and before any other scripts. Your first data will appear 
   automatically in just a few seconds.
   -->
   <script type="text/javascript">
     var appInsights=window.appInsights||function(config){
       function i(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s="AuthenticatedUserContext",h="start",c="stop",l="Track",a=l+"Event",v=l+"Page",y=u.createElement(o),r,f;y.src=config.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(y);try{t.cookie=u.cookie}catch(p){}for(t.queue=[],t.version="1.0",r=["Event","Exception","Metric","PageView","Trace","Dependency"];r.length;)i("track"+r.pop());return i("set"+s),i("clear"+s),i(h+a),i(c+a),i(h+v),i(c+v),i("flush"),config.disableExceptionTracking||(r="onerror",i("_"+r),f=e[r],e[r]=function(config,i,u,e,o){var s=f&&f(config,i,u,e,o);return s!==!0&&t["_"+r](config,i,u,e,o),s}),t
       }({
           instrumentationKey:"<insert instrumentation key>"
       });
       
       window.appInsights=appInsights;
       appInsights.trackPageView();
   </script>
   ```

5. À esquerda, selecione **Métricas**. Utilize o explorador de métricas para investigar a saúde e utilização do seu recurso. Pode selecionar **Adicionar um novo gráfico** para criar vistas personalizadas adicionais ou selecionar **Editar** para modificar os tipos de gráficos existentes, altura, paleta de cores, agrupamentos e métricas. Por exemplo, pode fazer um gráfico que exibe o tempo médio de carregamento da página do navegador selecionando "Browser page load time" a partir das métricas que descem e "Avg" da agregação. Para saber mais sobre o Azure Metrics Explorer, visite [Começar com o Azure Metrics Explorer](../platform/metrics-getting-started.md).

   ![Gráfico de métricas do Servidor de Insights de Aplicação](./media/nodejs-quick-start/azure-app-insights-server-metrics.png)

Para saber mais sobre a monitorização Node.js, consulte os [AppInsights adicionais Node.js documentação.](../app/nodejs.md)

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar os testes, pode eliminar o grupo de recursos e todos os recursos relacionados. Para fazê-lo siga os passos abaixo.

> [!NOTE]
> Se utilizar um grupo de recursos existente, as instruções abaixo não funcionarão e terá de eliminar apenas o recurso Individual Application Insights. Tenha em mente que sempre que eliminar um grupo de recursos todos os recursos que são membros desse grupo serão eliminados.

1. A partir do menu à esquerda no portal Azure, selecione **grupos de Recursos** e, em seguida, selecione **myResourceGroup**.
2. Na sua página de grupo de recursos, selecione **Delete**, insira o **myResourceGroup** na caixa de texto e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Encontrar e diagnosticar problemas de desempenho](../log-query/log-query-overview.md)

