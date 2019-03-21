---
title: Início rápido com o Azure Application Insights | Microsoft Docs
description: Fornece instruções para configurar rapidamente uma aplicação Web do ASP.NET Core para monitorização com o Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/11/2018
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: fe986d24df8dce6a390d21a262056f7ab857070c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57886733"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Iniciar a Monitorização de uma Aplicação Web ASP.NET Core

Com o Azure Application Insights, pode monitorizar facilmente a sua aplicação Web quanto à disponibilidade, ao desempenho e à utilização. Também pode identificar e diagnosticar erros rapidamente na sua aplicação sem ter de esperar que um utilizador os comunique. 

Este início rápido orienta-o ao processo de adicionar o Application Insights SDK para um aplicativo de web do ASP.NET Core existente. 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

- [Instale o Visual Studio 2017](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
  - Desenvolvimento ASP.NET e Web
  - Desenvolvimento do Azure
- [Instale o SDK .NET Core 2.0](https://www.microsoft.com/net/core)
- Irá precisar de uma subscrição do Azure e de uma aplicação Web .NET Core existente.

Se não tiver uma aplicação web ASP.NET Core, pode usar o nosso guia passo a passo para [criar uma aplicação ASP.NET Core e adicionar o Application Insights.](../../azure-monitor/app/asp-net-core.md)

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Ativar o Application Insights

O Application Insights pode recolher dados telemétricos de qualquer aplicação ligada à Internet, independentemente de estar a ser executado no local ou na cloud. Utilize os passos seguintes para começar a ver estes dados.

1. Selecione **Criar um recurso** > **Monitorização + Gestão** > **Application Insights**.

   ![Adicionar um Recurso do Application Insights](./media/dotnetcore-quick-start/0001-dc.png)

    É apresentada uma caixa de configuração; utilize a tabela abaixo para preencher os campos de texto.

    | Definições        |  Value           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor Exclusivo Global | Nome que identifica a aplicação que está a monitorizar |
   | **Tipo de Aplicação** | Aplicação Web ASP.NET | Tipo de aplicação que está a monitorizar |
   | **Grupo de Recursos**     | myResourceGroup      | Nome do novo grupo de recursos para alojar os dados do Application Insights |
   | **Localização** | EUA Leste | Escolha uma localização perto de si ou perto do local onde a sua aplicação está alojada |

2. Clique em **Criar**.

## <a name="configure-app-insights-sdk"></a>Configurar o SDK do Application Insights

1. Abra o seu **projeto** da Aplicação Web ASP.NET Core no Visual Studio > Clique com o botão direito do rato em AppName no **Explorador de Soluções** > Selecione **Adicionar** > **Telemetria do Application Insights**.

    ![Adicionar Telemetria do Application Insights](./media/dotnetcore-quick-start/0001.png)

2. Clique no botão **Começar Gratuitamente** > Selecione o **Recurso existente** que criou no portal do Azure > Clique em **Registar-se**.

3. Selecione **Depurar** > **Iniciar sem Depuração** (Ctrl + F5) para Iniciar a sua aplicação

> [!NOTE]
> Os dados demoram entre três e cinco minutos a aparecer no portal. Se esta aplicação for uma aplicação de teste de tráfego reduzido, tenha em atenção que a maioria das métricas só é capturada quando existem operações ou pedidos ativos.

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar a monitorização no portal do Azure

1. Agora, pode reabrir a página **Descrição geral** do Application Insights no portal do Azure ao selecionar **Projeto** > **Application Insights** > **Abrir o Portal do Application Insights** para ver os detalhes sobre a aplicação que se encontra em execução.

   ![Menu de Descrição Geral do Application Insights](./media/dotnetcore-quick-start/overview-001.png)

2. Clique em **Mapa da aplicação** para obter um esquema visual das relações de dependência entre os componentes da aplicação. Cada componente mostra KPIs, tais como carga, desempenho, falhas e alertas.

   ![Mapeamento de Aplicações](./media/dotnetcore-quick-start/application-map.png)

3. Clique no ícone do **App Analytics** ![Ícone do Mapa da Aplicação](./media/dotnetcore-quick-start/006.png).  Esta ação abre o **Application Insights Analytics**, que fornece uma linguagem de consulta avançada para analisar todos os dados recolhidos pelo Application Insights. Neste caso, é gerada uma consulta que compõe a contagem de pedidos como um gráfico. Pode escrever as suas próprias consultas para analisar outros dados.

   ![Gráfico de análise de pedidos de utilizador durante um período de tempo](./media/dotnetcore-quick-start/0007-dc.png)

4. Retorno para o **descrição geral** página e examinar os Dashboards de KPI.  Este dashboard fornece estatísticas sobre o estado de funcionamento da aplicação, incluindo o número de pedidos recebidos, a duração desses pedidos e quaisquer falhas que ocorram. 

   ![Gráficos de linha cronológica de Descrição Geral do Estado de Funcionamento](./media/dotnetcore-quick-start/overview-graphs.png)

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
           instrumentationKey:"<insert instrumentation key>"
       });
       
       window.appInsights=appInsights;
       appInsights.trackPageView();
   </script>
   ```

5. Clique em **Browser** no cabeçalho **Investigar**. Aqui, poderá encontrar métricas relacionadas com o desempenho das suas páginas de aplicação. Pode clicar em **Adicionar novo gráfico** criar vistas personalizadas adicionais ou selecionar **Editar** para modificar os tipos de gráficos existentes, a altura, a paleta de cores, os agrupamentos e as métricas.

   ![Gráficos de métricas de servidor](./media/dotnetcore-quick-start/009-Black.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser continuar a trabalhar com inícios rápidos subsequentes ou com os tutoriais, não limpe os recursos criados neste início rápido. Se não quiser continuar, utilize os passos seguintes para eliminar todos os recursos criados por este guia de introdução no portal do Azure.

1. No menu do lado esquerdo no portal do Azure, clique em **Grupos de recursos** e, em seguida, clique em **myResourceGroup**.
2. Na página do grupo de recursos, clique em **Eliminar**, escreva **myResourceGroup** na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Encontrar e diagnosticar exceções de runtime](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)
