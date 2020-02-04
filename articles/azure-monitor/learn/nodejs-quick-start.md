---
title: 'Quickstart: Monitor Node.js com Insights de Aplicação do Monitor Azure'
description: Fornece instruções para configurar rapidamente uma Web App Node.js para monitorização com insights de aplicação do Monitor Azure
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/12/2019
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: a6d6d70336badeaa86c9982dfa977ea389ed5402
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963534"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>Início rápido: iniciar o monitoramento do aplicativo Web node. js com insights Aplicativo Azure

Neste arranque rápido, adiciona a versão SDK de Insights de Aplicação 0.22 para node.js a uma aplicação web node.js existente.

Com o Azure Application Insights, pode monitorizar facilmente a sua aplicação Web quanto à disponibilidade, ao desempenho e à utilização. Também pode identificar e diagnosticar erros rapidamente na sua aplicação sem ter de esperar que um utilizador os comunique. A partir da versão 0.20 do SDK em diante, pode monitorizar pacotes de terceiros comuns, incluindo o MongoDB, MySQL e Redis.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Uma aplicação de Node.js funcional.

## <a name="enable-application-insights"></a>Ativar o Application Insights

Os Insights de Aplicação podem recolher dados de telemetria de qualquer aplicação ligada à Internet, quer esteja ou não a funcionar no local ou na nuvem. Utilize os passos seguintes para começar a ver estes dados.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Selecione **Criar um recurso** > **Ferramentas de programador** > **Application Insights**.

   ![Adicionar um recurso do insights Aplicativo Azure](./media/nodejs-quick-start/azure-app-insights-create-resource.png)

   > [!NOTE]
   >Se esta for a primeira vez que você cria um recurso de Application Insights, você pode aprender mais visitando o documento [criar um Application insights recurso](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) .

   Uma página de configuração é exibida; Use a tabela a seguir para preencher os campos de entrada. 

    | Definições        | Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor Exclusivo Global | Nome que identifica o aplicativo que você está monitorando |
   | **Grupo de Recursos**     | myResourceGroup      | Nome para o novo grupo de recursos para hospedar dados appInsights. Você pode criar um novo grupo de recursos ou usar um existente. |
   | **Localização** | E.U.A. Leste | Escolha uma localização perto de si ou perto do local onde a sua aplicação está alojada |

3. Selecione **Criar**.

## <a name="configure-appinsights-sdk"></a>Configure AppInsights SDK

1. Selecione **visão geral** e copie a **chave de instrumentação**do aplicativo.

   ![Exibir a chave de instrumentação de Application Insights](./media/nodejs-quick-start/azure-app-insights-instrumentation-key.png)

2. Adicionar o SDK do Application Insights para Node.js à sua aplicação. A partir da pasta raiz da sua aplicação, execute:

   ```bash
   npm install applicationinsights --save
   ```

3. Edite o primeiro ficheiro *.js* da sua aplicação e adicione as duas linhas abaixo na parte mais alta do seu script. Se estiver a utilizar a [aplicação Node.js quickstart,](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs)modifica o ficheiro *index.js.* Substitua `<instrumentation_key>` com a chave de instrumentação da sua aplicação. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Reinicie a aplicação.

> [!NOTE]
> Os dados demoram entre três e cinco minutos a aparecer no portal. Se esta aplicação for uma aplicação de teste de tráfego reduzido, tenha em atenção que a maioria das métricas só é capturada quando existem operações ou pedidos ativos em curso.

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar a monitorização no portal do Azure

1. Agora, pode reabrir a página **Descrição geral** do Application Insights no portal do Azure, onde obteve a sua chave de instrumentação, para ver detalhes sobre a aplicação que se encontra em execução.

   ![Application Insights menu visão geral](./media/nodejs-quick-start/azure-app-insights-overview-menu.png)

2. Selecione **mapa do aplicativo** para obter um layout visual das relações de dependência entre os componentes do aplicativo. Cada componente mostra KPIs, tais como carga, desempenho, falhas e alertas.

   ![Mapa do aplicativo Application Insights](./media/nodejs-quick-start/azure-app-insights-application-map.png)

3. Selecione o ícone do **aplicativo Analytics** ![ícone do mapa de aplicações](./media/nodejs-quick-start/azure-app-insights-analytics-icon.png) **Visualização no Analytics**.  Esta ação abre o **Application Insights Analytics,** que fornece uma linguagem de consulta rica para analisar todos os dados recolhidos pela Application Insights. Neste caso, é gerada uma consulta que compõe a contagem de pedidos como um gráfico. Pode escrever as suas próprias consultas para analisar outros dados.

   ![Gráficos de análise de Application Insights](./media/nodejs-quick-start/azure-app-insights-analytics-queries.png)

4. Volte à página de **Descrição geral** e examine os gráficos de KPI.  Este dashboard fornece estatísticas sobre o estado de funcionamento da aplicação, incluindo o número de pedidos recebidos, a duração desses pedidos e quaisquer falhas que ocorram.

   ![Gráficos de linha do tempo de visão geral da integridade Application Insights](./media/nodejs-quick-start/azure-app-insights-health-overview.png)

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

5. À esquerda, selecione **Métricas**. Use o Metrics Explorer para investigar a integridade e a utilização do recurso. Você pode selecionar **Adicionar novo gráfico** para criar exibições personalizadas adicionais ou selecionar **Editar** para modificar os tipos de gráfico, a altura, a paleta de cores, os agrupamentos e as métricas existentes. Por exemplo, você pode criar um gráfico que exibe o tempo médio de carregamento da página do navegador selecionando "tempo de carregamento de página do navegador" no menu suspenso métricas e "Méd" da agregação. Para saber mais sobre o Azure Metrics Explorer, visite [Getting started with Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md).

   ![Grafo de métricas do Application Insights Server](./media/nodejs-quick-start/azure-app-insights-server-metrics.png)

Para saber mais sobre a monitorização do Node.js, confira a documentação adicional do [Node.js da AppInsights.](../../azure-monitor/app/nodejs.md)

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar os testes, pode eliminar o grupo de recursos e todos os recursos relacionados. Para fazer isso, siga as etapas abaixo.

> [!NOTE]
> Se você usou um grupo de recursos existente, as instruções abaixo não funcionarão e você precisará apenas excluir o recurso de Application Insights individual. Tenha em mente sempre que você excluir um grupo de recursos, todos os recursos do underyling que são membros desse grupo serão excluídos.

1. No menu à esquerda na portal do Azure, selecione **grupos de recursos** e, em seguida, selecione grupo de **recursos**.
2. Na página do grupo de recursos, selecione **excluir**, digite **MyResource** Group na caixa de texto e, em seguida, selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Encontrar e diagnosticar problemas de desempenho](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
