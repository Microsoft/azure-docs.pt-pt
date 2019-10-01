---
title: 'Início rápido: Monitorize com o Azure Application Insights'
description: Fornece instruções para configurar rapidamente uma Aplicação Web Node.js para monitorização com o Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/12/2019
ms.service: application-insights
ms.custom: mvc, seo-javascript-september2019
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 1db26002167f4b7c5b4fc19699ddb021de8ac23d
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703018"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>Início rápido: Iniciar o monitoramento de seu aplicativo Web node. js com insights Aplicativo Azure

Com o Azure Application Insights, pode monitorizar facilmente a sua aplicação Web quanto à disponibilidade, ao desempenho e à utilização. Também pode identificar e diagnosticar erros rapidamente na sua aplicação sem ter de esperar que um utilizador os comunique. A partir da versão 0.20 do SDK em diante, pode monitorizar pacotes de terceiros comuns, incluindo o MongoDB, MySQL e Redis.

Este início rápido explica-lhe como adicionar a versão 0.22 do SDK do Application Insights para Node.js a uma aplicação Web Node.js existente.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

- Irá precisar de uma Subscrição do Azure e de uma aplicação Web Node.js existente.

Se não tiver uma aplicação Web Node.js, pode criar uma ao seguir o [Início Rápido de Criação de uma aplicação Web Node.js](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs).

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Ativar o Application Insights

O Application Insights pode recolher dados telemétricos de qualquer aplicação ligada à Internet, independentemente de estar a ser executado no local ou na cloud. Utilize os passos seguintes para começar a ver estes dados.

1. Selecione **Criar um recurso** > **Ferramentas de programador** > **Application Insights**.

   ![Adicionar um Recurso do Application Insights](./media/nodejs-quick-start/1createresourseappinsights.png)

   > [!NOTE]
   >Se esta for a primeira vez que você cria um recurso de Application Insights, você pode aprender mais visitando o documento [criar um Application insights recurso](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) .

   Uma página de configuração é exibida; Use a tabela a seguir para preencher os campos de entrada. 

    | Definições        | Value           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Name**      | Valor Exclusivo Global | Nome que identifica a aplicação que está a monitorizar |
   | **Tipo de Aplicação** | Aplicação Node.js | Tipo de aplicação que está a monitorizar |
   | **Location** | East US | Escolha uma localização perto de si ou perto do local onde a sua aplicação está alojada |

2. Selecione **Criar**.

## <a name="configure-app-insights-sdk"></a>Configurar o SDK do Application Insights

1. Selecione **visão geral** e copie a **chave**de instrumentação do aplicativo.

   ![Formulário de recurso novo do App Insights](./media/nodejs-quick-start/3key.png)

2. Adicionar o SDK do Application Insights para Node.js à sua aplicação. A partir da pasta raiz da sua aplicação, execute:

   ```bash
   npm install applicationinsights --save
   ```

3. Edite o primeiro ficheiro .js da sua aplicação e adicione as duas linhas abaixo à parte superior do seu script. Se estiver a utilizar a [aplicação de início rápido do Node.js](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs), modificaria o ficheiro index.js. Substitua &lt;instrumentation_key&gt; pela chave de instrumentação da aplicação. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Reinicie a aplicação.

> [!NOTE]
> Os dados demoram entre três e cinco minutos a aparecer no portal. Se esta aplicação for uma aplicação de teste de tráfego reduzido, tenha em atenção que a maioria das métricas só é capturada quando existem operações ou pedidos ativos em curso.

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar a monitorização no portal do Azure

1. Agora, pode reabrir a página **Descrição geral** do Application Insights no portal do Azure, onde obteve a sua chave de instrumentação, para ver detalhes sobre a aplicação que se encontra em execução.

   ![Menu de Descrição Geral do Application Insights](./media/nodejs-quick-start/4overview.png)

2. Selecione **mapa do aplicativo** para obter um layout visual das relações de dependência entre os componentes do aplicativo. Cada componente mostra KPIs, tais como carga, desempenho, falhas e alertas.

   ![Mapeamento de Aplicações](./media/nodejs-quick-start/5appmap.png)

3. Selecione o ícone ![do **app Analytics** exibição do](./media/nodejs-quick-start/006.png) ícone **do mapa do aplicativo na análise**.  Esta ação abre o **Application Insights Analytics**, que fornece uma linguagem de consulta avançada para analisar todos os dados recolhidos pelo Application Insights. Neste caso, é gerada uma consulta que compõe a contagem de pedidos como um gráfico. Pode escrever as suas próprias consultas para analisar outros dados.

   ![Gráfico de análise de pedidos de utilizador durante um período de tempo](./media/nodejs-quick-start/6analytics.png)

4. Volte à página de **Descrição geral** e examine os gráficos de KPI.  Este dashboard fornece estatísticas sobre o estado de funcionamento da aplicação, incluindo o número de pedidos recebidos, a duração desses pedidos e quaisquer falhas que ocorram.

   ![Gráficos de linha cronológica de Descrição Geral do Estado de Funcionamento](./media/nodejs-quick-start/7kpidashboards.png)

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

5. À esquerda, selecione **métricas**. Use o Metrics Explorer para investigar a integridade e a utilização do recurso. Você pode selecionar **Adicionar novo gráfico** para criar exibições personalizadas adicionais ou selecionar **Editar** para modificar os tipos de gráfico, a altura, a paleta de cores, os agrupamentos e as métricas existentes. Por exemplo, você pode criar um gráfico que exibe o tempo médio de carregamento da página do navegador selecionando "tempo de carregamento de página do navegador" no menu suspenso métricas e "Méd" da agregação. Para saber mais sobre o Azure Metrics Explorer visite [a introdução ao azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md).

   ![Gráficos de métricas de servidor](./media/nodejs-quick-start/8metrics.png)

Para saber mais sobre a monitorização de Node.js, veja a [Documentação adicional de Node.js do App Insights](../../azure-monitor/app/nodejs.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar o teste, você poderá excluir o grupo de recursos e todos os recursos relacionados. Para fazer isso, siga as etapas abaixo.

1. No menu à esquerda na portal do Azure, selecione **grupos de recursos** e, em seguida, selecione grupo de **recursos**.
2. Na página do grupo de recursos, selecione **excluir**, digite **MyResource** Group na caixa de texto e, em seguida, selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Encontrar e diagnosticar problemas de desempenho](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
