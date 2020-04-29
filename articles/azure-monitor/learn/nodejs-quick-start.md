---
title: 'Quickstart: Monitor Node.js com Insights de Aplicação do Monitor Azure'
description: Fornece instruções para configurar rapidamente uma Web App Node.js para monitorização com insights de aplicação do Monitor Azure
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/12/2019
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 694d2ae529202223869fcbb2a084e32bccaedbf1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77660228"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>Quickstart: Comece a monitorizar a sua aplicação Web Node.js com insights de aplicação Azure

Neste arranque rápido, adiciona a versão SDK de Insights de Aplicação 0.22 para node.js a uma aplicação web node.js existente.

Com o Azure Application Insights, pode monitorizar facilmente a sua aplicação Web quanto à disponibilidade, ao desempenho e à utilização. Também pode identificar e diagnosticar erros rapidamente na sua aplicação sem ter de esperar que um utilizador os comunique. A partir da versão 0.20 do SDK em diante, pode monitorizar pacotes de terceiros comuns, incluindo o MongoDB, MySQL e Redis.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Uma aplicação de Node.js funcional.

## <a name="enable-application-insights"></a>Ativar o Application Insights

Os Insights de Aplicação podem recolher dados de telemetria de qualquer aplicação ligada à Internet, quer esteja ou não a funcionar no local ou na nuvem. Utilize os passos seguintes para começar a ver estes dados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione **Criar um programador de recursos** > **Ferramentas** > **De aplicação Insights**.

   ![Adicione um recurso Azure Application Insights](./media/nodejs-quick-start/azure-app-insights-create-resource.png)

   > [!NOTE]
   >Se for a primeira vez que cria um recurso de Insights de Aplicação, pode aprender mais visitando o [Create a Application Insights Resource](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) doc.

   Aparece uma página de configuração; utilize a tabela seguinte para preencher os campos de entrada. 

    | Definições        | Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor Exclusivo Global | Nome que identifique a app que está a monitorizar |
   | **Grupo de Recursos**     | myResourceGroup      | Nome para o novo grupo de recursos para hospedar dados appInsights. Pode criar um novo grupo de recursos ou utilizar um existente. |
   | **Localização** | E.U.A. Leste | Escolha uma localização perto de si ou perto do local onde a sua aplicação está alojada |

3. Selecione **Criar**.

## <a name="configure-appinsights-sdk"></a>Configure AppInsights SDK

1. Selecione **visão geral** e copie a chave de **instrumentação**da sua aplicação .

   ![Ver a chave de instrumentação de insights de aplicação](./media/nodejs-quick-start/azure-app-insights-instrumentation-key.png)

2. Adicionar o SDK do Application Insights para Node.js à sua aplicação. A partir da pasta raiz da sua aplicação, execute:

   ```bash
   npm install applicationinsights --save
   ```

3. Edite o primeiro ficheiro *.js* da sua aplicação e adicione as duas linhas abaixo na parte mais alta do seu script. Se estiver a utilizar a [aplicação Node.js quickstart,](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs)modifica o ficheiro *index.js.* Substitua-a `<instrumentation_key>` com a chave de instrumentação da sua aplicação. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Reinicie a aplicação.

> [!NOTE]
> Os dados demoram entre três e cinco minutos a aparecer no portal. Se esta aplicação for uma aplicação de teste de tráfego reduzido, tenha em atenção que a maioria das métricas só é capturada quando existem operações ou pedidos ativos em curso.

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar a monitorização no portal do Azure

1. Agora, pode reabrir a página **Descrição geral** do Application Insights no portal do Azure, onde obteve a sua chave de instrumentação, para ver detalhes sobre a aplicação que se encontra em execução.

   ![Menu de visão geral de insights de aplicação](./media/nodejs-quick-start/azure-app-insights-overview-menu.png)

2. Selecione o mapa de **aplicação** para um layout visual das relações de dependência entre os seus componentes de aplicação. Cada componente mostra KPIs, tais como carga, desempenho, falhas e alertas.

   ![Mapa de aplicação de insights de aplicação](./media/nodejs-quick-start/azure-app-insights-application-map.png)

3. Selecione o ![ícone](./media/nodejs-quick-start/azure-app-insights-analytics-icon.png) do ícone do **aplicativo Analytics** **Visualizar no Analytics**.  Esta ação abre o **Application Insights Analytics,** que fornece uma linguagem de consulta rica para analisar todos os dados recolhidos pela Application Insights. Neste caso, é gerada uma consulta que compõe a contagem de pedidos como um gráfico. Pode escrever as suas próprias consultas para analisar outros dados.

   ![Insights de aplicação Gráficos de análise](./media/nodejs-quick-start/azure-app-insights-analytics-queries.png)

4. Volte à página de **Descrição geral** e examine os gráficos de KPI.  Este dashboard fornece estatísticas sobre o estado de funcionamento da aplicação, incluindo o número de pedidos recebidos, a duração desses pedidos e quaisquer falhas que ocorram.

   ![Insights de aplicação Gráficos de linha do tempo geral de visão geral](./media/nodejs-quick-start/azure-app-insights-health-overview.png)

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

5. À esquerda, selecione **Métricas**. Utilize o explorador de métricas para investigar a saúde e utilização do seu recurso. Pode selecionar **Adicionar um novo gráfico** para criar vistas personalizadas adicionais ou selecionar **Editar** para modificar os tipos de gráficos existentes, altura, paleta de cores, agrupamentos e métricas. Por exemplo, pode fazer um gráfico que exiba o tempo médio de carga da página do navegador selecionando "Tempo de carga da página do navegador" a partir das métricas que descem e "Avg" da agregação. Para saber mais sobre o Azure Metrics Explorer, visite [Getting started with Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md).

   ![Gráfico de métricas do servidor insights de aplicação](./media/nodejs-quick-start/azure-app-insights-server-metrics.png)

Para saber mais sobre a monitorização do Node.js, confira a documentação adicional do [Node.js da AppInsights.](../../azure-monitor/app/nodejs.md)

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar os testes, pode eliminar o grupo de recursos e todos os recursos relacionados. Para isso siga os passos abaixo.

> [!NOTE]
> Se utilizou um grupo de recursos existente, as instruções abaixo não funcionarão e terá de eliminar o recurso Individual Application Insights. Tenha em mente que sempre que eliminar um grupo de recursos, todos os recursos que são membros desse grupo serão eliminados.

1. A partir do menu à esquerda no portal Azure, selecione **grupos de Recursos** e, em seguida, selecione **myResourceGroup**.
2. Na página do grupo de recursos, selecione **Eliminar**, introduza o **myResourceGroup** na caixa de texto e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Encontrar e diagnosticar problemas de desempenho](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
