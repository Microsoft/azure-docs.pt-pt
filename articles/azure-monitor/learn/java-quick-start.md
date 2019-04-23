---
title: Início rápido com o Azure Application Insights | Microsoft Docs
description: Fornece instruções para configurar rapidamente uma aplicação Web Java para monitorização com o Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.reviewer: lagayhar
ms.date: 04/18/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: e1574b55f9f14daba1831ba7f73b7f9ebde4c7f6
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006901"
---
# <a name="start-monitoring-your-java-web-application"></a>Iniciar a Monitorização de uma Aplicação Web Java

Com o Azure Application Insights, pode monitorizar facilmente a sua aplicação Web quanto à disponibilidade, ao desempenho e à utilização. Também pode identificar e diagnosticar erros rapidamente na sua aplicação sem ter de esperar que um utilizador os comunique. Com o Application Insights SDK para Java, pode monitorizar pacotes de terceiros comuns, incluindo o MongoDB, MySQL e Redis.

Este manual de início rápido orienta-o ao longo da adição do Application Insights SDK a um Projeto Web Dinâmico Java.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

- Instalar o JRE 1.7 ou 1.8
- Instale o [Eclipse IDE for Java EE Developers gratuito](https://www.eclipse.org/downloads/). Este início rápido utiliza o Eclipse Oxygen (4.7)
- Irá precisar de uma Subscrição do Azure e de um Projeto Web Dinâmico Java existente
 
Se não tiver um Projeto Web Dinâmico Java, pode criar um com o [Início rápido Criar uma aplicação Web Java](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-java).

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se preferir o Spring framework, experimente o [configurar uma aplicação do inicializador de Spring Boot para usar o Guia do Application Insights](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-java-applicationinsights)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Ativar o Application Insights

O Application Insights pode recolher dados telemétricos de qualquer aplicação ligada à Internet, independentemente de estar a ser executado no local ou na cloud. Utilize os passos seguintes para começar a ver estes dados.

1. Selecione **Criar um recurso** > **Ferramentas de programador** > **Application Insights**.

   ![Adicionar um Recurso do Application Insights](./media/java-quick-start/1createresourseappinsights.png)

   ![Adicionar um Recurso do Application Insights](./media/java-quick-start/2createjavaapp.png)

   É apresentada uma caixa de configuração; utilize a tabela abaixo para preencher os campos de texto.

    | Definições        | Value           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor Exclusivo Global | Nome que identifica a aplicação que está a monitorizar |
   | **Tipo de Aplicação** | Aplicação Web Java | Tipo de aplicação que está a monitorizar |
   | **Grupo de Recursos**     | myResourceGroup      | Nome do novo grupo de recursos para alojar os dados do Application Insights |
   | **Localização** | EUA Leste | Escolha uma localização perto de si ou perto do local onde a sua aplicação está alojada |

2. Clique em **Criar**.

## <a name="install-app-insights-plugin"></a>Instalar o Plug-in do Application Insights

1. Inicie o **Eclipse** > Clique em **Help (Ajuda)** > Selecione **Install New Software (Instalar Software Novo)**.

   ![Formulário de recurso novo do App Insights](./media/java-quick-start/000-j.png)

2. Copie ```https://dl.microsoft.com/eclipse``` para o campo "Work With" > Marque **Azure Toolkit for Java** > Selecione **Application Insights Plugin for Java** > **Desmarque** "Contact all update sites during install to find required software" (Contactar todos os sites de atualizações durante a instalação para localizar o software necessário).

3. Assim que a instalação estiver concluída, ser-lhe-á pedido para **Restart Eclipse (Reiniciar o Eclipse)**.

## <a name="configure-app-insights-plugin"></a>Configurar o Plug-in do Application Insights

1. Inicie o **Eclipse** > Abra o seu **Projeto** > Clique com o botão direito do rato no nome do projeto no **Project Explorer (Explorador de Projetos)** > Selecione **Azure** > Clique em **Sign In (Iniciar Sessão)**.

2. Selecione o Método de Autenticação **Interactive (Interativo)** > Clique em **Sign In (Iniciar Sessão)** > Quando lhe for pedido, introduza as suas **credenciais do Azure** > Selecione a sua **Subscrição do Azure**.

3. Clique com o botão direito do rato no nome do projeto no **Project Explorer (Explorador de Projetos)** > Selecione **Azure** > Clique em **Configure Application Insights (Configurar o Application Insights)**.

4. Marque **Enable telemetry with Application Insights (Ativar telemetria com o Application Insights)** > Selecione o recurso do App Insights e a **Chave de instrumentação** associada que pretende ligar à sua aplicação Java.

   ![Menu de Configuração do Azure no Eclipse](./media/java-quick-start/0007-j.png)

5. Depois de configurar o plug-in do Application Insights terá [publicar/voltar a publicá](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-java#publish-the-web-app-to-azure) seu aplicativo novamente, antes ele poderá começar a enviar telemetria.

> [!NOTE]
> O Application Insights SDK para Java é capaz de capturar e visualizar métricas dinâmicas, mas, quando ativa primeiro a recolha de dados telemétricos, pode demorar alguns minutos até que os dados comecem a aparecer no portal. Se esta aplicação for uma aplicação de teste de tráfego reduzido, tenha em atenção que a maioria das métricas só é capturada quando existem operações ou pedidos ativos.

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar a monitorização no portal do Azure

1. Agora, pode reabrir o Application Insights **descrição geral** página no portal do Azure para ver os detalhes sobre a sua aplicação em execução.

   ![Menu de Descrição Geral do Application Insights](./media/java-quick-start/3overview.png)

2. Clique em **Mapa da aplicação** para obter um esquema visual das relações de dependência entre os componentes da aplicação. Cada componente mostra KPIs, tais como carga, desempenho, falhas e alertas.

   ![Mapeamento de Aplicações](./media/java-quick-start/4appmap.png)

3.  Clique nas **análise da aplicação** ícone ![ícone do mapa da aplicação](./media/java-quick-start/006.png) **ver na análise**.  Esta ação abre o **Application Insights Analytics**, que fornece uma linguagem de consulta avançada para analisar todos os dados recolhidos pelo Application Insights. Neste caso, é gerada uma consulta que compõe a contagem de pedidos como um gráfico. Pode escrever as suas próprias consultas para analisar outros dados.

   ![Gráfico de análise de pedidos de utilizador durante um período de tempo](./media/java-quick-start/5analytics.png)

4. Volte à página de **Descrição geral** e examine os gráficos de KPI. Este dashboard fornece estatísticas sobre o estado de funcionamento da aplicação, incluindo o número de pedidos recebidos, a duração desses pedidos e quaisquer falhas que ocorram.

   ![Gráficos de linha cronológica de Descrição Geral do Estado de Funcionamento](./media/java-quick-start/6kpidashboards.png)

   Para ativar o gráfico **Duração de Carregamento da Vista de Página** para preencher com **dados telemétricos do lado do cliente**, adicione este script a cada página que queira controlar:

   ```HTML
   <!-- 
   To collect user behavior analytics about your application, 
   insert the following script into each page you want to track.
   Place this code immediately before the closing </head> tag,
   and before any other scripts. Your first data will appear 
   automatically in just a few seconds.
   -->
   <script type="text/javascript">
     var appInsights=window.appInsights||function(config){
     function i(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s="AuthenticatedUserContext",h="start",c="stop",l="Track",a=l+"Event",v=l+"Page",y=u.createElement(o),r,f;y.src=config.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(y);try{t.cookie=u.cookie}catch(p){}for(t.queue=[],t.version="1.0",r=["Event","Exception","Metric","PageView","Trace","Dependency"];r.length;)i("track"+r.pop());return i("set"+s),i("clear"+s),i(h+a),i(c+a),i(h+v),i(c+v),i("flush"),config.disableExceptionTracking||(r="onerror",i("_"+r),f=e[r],e[r]=function(config,i,u,e,o){var s=f&&f(config,i,u,e,o);return s!==!0&&t["_"+r](config,i,u,e,o),s}),t
    }({
        instrumentationKey:"<instrumentation key>"
    });

    window.appInsights=appInsights;
    appInsights.trackPageView();
   </script>
    ```

5. Clique em **Live Stream**. Aqui, poderá encontrar métricas dinâmicas relacionadas com o desempenho da sua aplicação Web Java. O **Live Metrics Stream** inclui dados referentes ao número de pedidos recebidos, à duração desses pedidos e a quaisquer falhas que ocorram. Também pode monitorizar métricas críticas de desempenho, tais como de processador e memória em tempo real.

   ![Gráficos de métricas de servidor](./media/java-quick-start/7livemetrics.png)

Para saber mais sobre a monitorização de Java, veja a [documentação adicional de Java do App Insights](./../../azure-monitor/app/java-get-started.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado, teste, pode eliminar o grupo de recursos e todos os recursos relacionados. Para tal, siga os passos abaixo.

1. No menu do lado esquerdo no portal do Azure, clique em **Grupos de recursos** e, em seguida, clique em **myResourceGroup**.
2. Na página do grupo de recursos, clique em **Eliminar**, escreva **myResourceGroup** na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Encontrar e diagnosticar problemas de desempenho](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)