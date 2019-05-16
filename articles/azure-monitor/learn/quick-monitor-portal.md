---
title: Monitorizar uma aplicação Web ASP.NET com o Azure Application Insights | Microsoft Docs
description: Fornece instruções para configurar rapidamente uma aplicação Web ASP.NET para monitorização com o Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/01/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 08745c3ef3d4996340ec40af496f8f0a5e0201c3
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595732"
---
# <a name="start-monitoring-your-aspnet-web-application"></a>Iniciar a Monitorização de uma Aplicação Web ASP.NET

Com o Azure Application Insights, pode monitorizar facilmente a sua aplicação Web quanto à disponibilidade, ao desempenho e à utilização.  Também pode identificar e diagnosticar erros rapidamente na sua aplicação sem ter de esperar que um utilizador os comunique.  Com as informações que recolher com o Application Insights sobre o desempenho e a eficácia da sua aplicação, pode tomar decisões informadas para manter e melhorar a sua aplicação.

Este início rápido mostra como adicionar o Application Insights a uma aplicação Web ASP.NET existente e iniciar a análise das estatísticas em direto, um dos vários métodos que pode utilizar para analisar a sua aplicação. Se não tiver uma aplicação web ASP.NET, pode criar uma seguindo o [criar um guia de introdução da aplicação Web ASP.NET](../../app-service/app-service-web-get-started-dotnet-framework.md).

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de início rápido:

- Instale [Visual Studio 2019](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
    - Desenvolvimento ASP.NET e Web
    - Desenvolvimento do Azure


Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="enable-application-insights"></a>Ativar o Application Insights

1. Abra o projeto no Visual Studio 2019.
2. Selecione **Configurar o Application Insights** no menu Projeto. O Visual Studio adiciona o Application Insights SDK à sua aplicação.

    > [!IMPORTANT]
    > O processo de adicionar o Application Insights varia consoante o tipo de modelo do ASP.NET. Se estiver a utilizar o modelo **Vazio** ou **Aplicação Móvel do Azure**, selecione **Projeto** > **Adicionar Telemetria do Application Insights**. Relativamente a todos os outros modelos ASP.NET, consulte as instruções no passo acima. 

3. Clique em **Introdução** (as versões anteriores do Visual Studio apresentam um botão **Iniciar a Versão Gratuita**).

    ![Adicionar o Application Insights ao Visual Studio](./media/quick-monitor-portal/add-application-insights-b.png)

4. Selecione a sua subscrição e clique em **Registo**.

5. Execute a sua aplicação selecionando **Iniciar Depuração** no menu **Depurar** ou premindo a tecla F5.

## <a name="confirm-app-configuration"></a>Confirmar a configuração da aplicação

O Application Insights recolhe dados de telemetria da sua aplicação, independentemente de estar ou não em execução. Utilize os passos seguintes para começar a ver estes dados.

1. Abra o Application Insights com um clique em **Vista** -> **Outras Janelas** -> **Application Insights Search**.  É apresentada a telemetria da sua sessão atual.<BR><br>![Telemetria no Visual Studio](./media/quick-monitor-portal/telemetry-in-vs.png)

2. Clique no primeiro pedido na lista (GET Home/Index neste exemplo) para ver os detalhes do pedido. Tenha em atenção que o código de estado e o tempo de resposta são incluídos juntamente com outras informações importantes sobre o pedido.<br><br>![Detalhes de resposta no Visual Studio](media/quick-monitor-portal/request-details.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar a monitorização no portal do Azure

Agora, pode abrir o Application Insights no portal do Azure para ver os vários detalhes sobre a sua aplicação em execução.

1. Expanda a **serviços ligados** pasta (ícone de nuvem e plug) no Solution Explorer, em seguida, clique com botão direito no **Application Insights** pasta e clique em **abrir Portal do Application Insights** .  São apresentadas algumas informações sobre a sua aplicação e uma variedade de opções.

    ![Mapa da Aplicação](media/quick-monitor-portal/4overview.png)

2. Clique em **Mapa da Aplicação** para obter um esquema visual das relações de dependência entre os componentes da aplicação.  Cada componente mostra KPIs, tais como carga, desempenho, falhas e alertas.

    ![Mapa da Aplicação](media/quick-monitor-portal/5appmap.png)

3. Clique nas **análise da aplicação** ícone ![mapa da aplicação](media/quick-monitor-portal/app-analytics-icon.png) **ver na análise** em um dos componentes da aplicação. Esta ação abre o **Application Insights Analytics**, que fornece uma linguagem de consulta avançada para analisar todos os dados recolhidos pelo Application Insights.  Neste caso, é gerada uma consulta que compõe a contagem de pedidos como um gráfico. Pode escrever as suas próprias consultas para analisar outros dados.

    ![Análise](media/quick-monitor-portal/6viewanalytics.png)

4. Clique em **Stream de métricas em direto** à esquerda em investigar. Isto mostra estatísticas em direto sobre a sua aplicação em execução. Inclui informações como o número de pedidos recebidos, a duração desses pedidos e quaisquer falhas que ocorram. Também pode inspecionar métricas críticas de desempenho, tais como processador e memória.

    ![Live Stream](media/quick-monitor-portal/7livemetrics.png)

    Se está pronto para alojar a sua aplicação no Azure, pode publicá-la agora. Siga os passos descritos em [Início Rápido para Criar uma Aplicação Web ASP.NET](../../app-service/app-service-web-get-started-dotnet.md#update-the-app-and-redeploy).

5. Se utilizar o Visual Studio para adicionar a monitorização do Application Insights, pode adicionar automaticamente a monitorização do lado do cliente. Para adicionar monitorização manualmente do lado do cliente a uma aplicação, adicione o seguinte JavaScript à sua aplicação:

```html
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

Para saber mais, visite o repositório do GitHub para o nosso [SDK de JavaScript de open source](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="video"></a>Vídeo

* Externo vídeo passo a passo sobre [configuração do Application Insights com uma aplicação .NET do zero](https://www.youtube.com/watch?v=blnGAVgMAfA).

## <a name="clean-up-resources"></a>Limpar recursos
Quando tiver terminado, teste, pode eliminar o grupo de recursos e todos os recursos relacionados. Para tal, siga os passos abaixo.
1. No menu do lado esquerdo no portal do Azure, clique em **Grupos de recursos** e, em seguida, clique em **myResourceGroup**.
2. Na página do grupo de recursos, clique em **Eliminar**, escreva **myResourceGroup** na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes
Neste início rápido, ativou a aplicação para monitorização pelo Azure Application Insights.  Continue para os tutoriais para saber como utilizá-lo para monitorizar as estatísticas e detetar problemas na sua aplicação.

> [!div class="nextstepaction"]
> [Azure Application Insights](tutorial-runtime-exceptions.md)
