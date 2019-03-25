---
title: Configurar a análise de aplicações Web para ASP.NET com o Azure Application Insights | Microsoft Docs
description: Configurar o desempenho, disponibilidade e ferramentas de análise de comportamento de utilizador do seu site ASP.NET, alojado no local ou no Azure.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: mbullwin
ms.openlocfilehash: fbb8eabebd5728c3ff772f78f01b8ea93ff0a526
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400201"
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Configurar o Application Insights para o seu site ASP.NET

Este procedimento configura a sua aplicação Web do ASP.NET para enviar telemetria para o serviço [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Funciona para aplicações ASP.NET alojadas no seu próprio servidor IIS no local ou na Cloud. Obtém gráficos e um idioma de consulta poderoso que o ajudam a compreender o desempenho da sua aplicação e como as pessoas estão a utilizá-la, bem como alertas automáticos sobre falhas ou problemas de desempenho. Muitos programadores acham que estas funcionalidades são excelentes tal como estão, mas também pode expandir e personalizar a telemetria se precisar.

A configuração demora apenas alguns cliques no Visual Studio. Tem a opção de evitar encargos ao limitar o volume de telemetria. Esta funcionalidade permite-lhe experimentar e depurar, ou monitorizar um site com poucos utilizadores. Quando decidir que pretende continuar e monitorizar o seu site de produção, pode elevar o limite facilmente mais tarde.

## <a name="prerequisites"></a>Pré-requisitos
Para adicionar o Application Insights ao site ASP.NET, é necessário:

- Instale o [Visual Studio 2017 para Windows](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
    - Desenvolvimento ASP.NET e Web
    - Desenvolvimento do Azure

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="ide"></a> Passo 1: Adicionar o Application Insights SDK

> [!IMPORTANT]
> As capturas de ecrã neste exemplo baseiam-se no Visual Studio 2017 versão 15.9.9. A experiência para adicionar o Application Insights varia entre as versões do Visual Studio 2017, bem como por tipo de modelo do ASP.NET. As versões mais antigas podem ter o texto alternativo, como "A configurar o Application Insights".

Clique no nome da sua aplicação web no Explorador de soluções e escolha **Add** > **telemetria do Application Insights**

![Captura de ecrã do Explorador de Soluções, com a opção Configurar Application Insights realçada](./media/asp-net/add-telemetry-new.png)

(Dependendo da versão do SDK do Application Insights, poderá ser-lhe pedido para atualizar para a versão mais recente do SDK. Se lhe for pedido, selecione **Atualizar SDK**.)

![Captura de ecrã: Uma nova versão do SDK do Microsoft Application Insights está disponível. Atualizar SDK realçado](./media/asp-net/0002-update-sdk.png)

Ecrã de Configuração do Application Insights:

Selecione **começar**.

![Captura de ecrã da página Registar a sua aplicação no Application Insights](./media/asp-net/00004-start-free.png)

Se pretender definir o grupo de recursos ou a localização onde os seus dados são armazenado, clique em **Configurar definições**. Os grupos de recursos são utilizados para controlar o acesso aos dados. Por exemplo, se tiver várias aplicações que fazem parte do mesmo sistema, poderá colocar os seus dados do Application Insights no mesmo grupo de recursos.

 Selecione **Registar**.

![Captura de ecrã da página Registar a sua aplicação no Application Insights](./media/asp-net/00005-register-ed.png)

 A telemetria será enviada para o [portal do Azure](https://portal.azure.com), durante a depuração e após ter publicado a aplicação.
> [!NOTE]
> Se não pretender enviar telemetria para o portal enquanto estiver a depurar, basta adicionar o SDK do Application Insights à sua aplicação, mas não configure um recurso no portal. Pode ver a telemetria no Visual Studio enquanto faz a depuração. Mais tarde, pode voltar a esta página de configuração ou pode esperar até depois de implementar a aplicação e [ativar a telemetria em tempo de execução](../../azure-monitor/app/monitor-performance-live-website-now.md).

## <a name="run"></a> Passo 2: Executar a aplicação
Execute a aplicação com F5. Abra páginas diferentes para gerar alguma telemetria.

No Visual Studio, verá uma contagem dos eventos que foram registados.

![Captura de ecrã do Visual Studio. O botão do Application Insights é apresentado durante a depuração.](./media/asp-net/00006-Events.png)

## <a name="step-3-see-your-telemetry"></a>Passo 3: Ver a telemetria
Pode ver a sua telemetria no Visual Studio ou no portal Web do Application Insights. Procure telemetria no Visual Studio para o ajudar a depurar a sua aplicação. Monitorize o desempenho e a utilização no portal Web quando o sistema estiver em direto. 

### <a name="see-your-telemetry-in-visual-studio"></a>Consulte a sua telemetria no Visual Studio

No Visual Studio, para ver os dados do Application Insights.  Selecione **Explorador de Soluções** > **Serviços Ligados** > com o botão direito **Application Insights**e, em seguida, clique em **Pesquisa Telemetria Em Direto**.

Na janela de Pesquisa do Visual Studio Application Insights, verá os dados da aplicação para ver a telemetria gerada no lado do servidor da aplicação. Experimente os filtros e clique em qualquer evento para ver mais detalhes.

![Captura de ecrã da vista Dados da sessão de Depuração na janela do Application Insights.](./media/asp-net/55.png)

> [!Tip]
> Se não existirem quaisquer dados, certifique-se de que o intervalo de tempo está correto e clique no ícone de Pesquisa.

[Saiba mais sobre as ferramentas do Application Insights no Visual Studio](../../azure-monitor/app/visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Consulte a telemetria no portal Web

Também pode ver a telemetria no portal Web do Application Insights, (a não ser que tenha optado por instalar apenas o SDK). O portal tem mais gráficos, ferramentas de análise e vistas de vários componentes que o Visual Studio. O portal também fornece alertas.

Abra o recurso do Application Insights. Inicie sessão no [portal do Azure](https://portal.azure.com/) e encontre-o aí ou selecione **Explorador de Soluções** > **Serviços Ligados** > com o botão direito **Application Insights** > **Abrir Portal do Application Insights** e permita que o mesmo o guie.

O portal é aberto na vista da telemetria da sua aplicação.

![Captura de ecrã da página de descrição geral do Application Insights](./media/asp-net/007.png)

No portal, clique em qualquer gráfico ou mosaico para ver mais detalhes.

[Saiba mais sobre como utilizar o Application Insights no portal do Azure](../../azure-monitor/app/app-insights-dashboards.md).

## <a name="step-4-publish-your-app"></a>Passo 4: Publicar a aplicação
Publique a sua aplicação no seu servidor IIS ou no Azure. Veja o [Live Metrics Stream](../../azure-monitor/app/metrics-explorer.md#live-metrics-stream) para garantir que não existem problemas.

A telemetria acumula-se no portal do Application Insights, onde pode monitorizar métricas, pesquisar a sua telemetria e configurar [dashboards](../../azure-monitor/app/app-insights-dashboards.md). Também pode utilizar a poderosa [linguagem de consulta de Kusto](/azure/kusto/query/) para analisar a utilização e o desempenho ou para encontrar eventos específicos.

Também pode continuar a analisar a sua telemetria no [Visual Studio](../../azure-monitor/app/visual-studio.md) com ferramentas como a pesquisa de diagnóstico e as [tendências](../../azure-monitor/app/visual-studio-trends.md).

> [!NOTE]
> Se a sua aplicação enviar telemetria suficiente para se aproximar dos [limites de monitorização](../../azure-monitor/app/pricing.md#limits-summary), é ativada a [amostragem](../../azure-monitor/app/sampling.md) automática. A amostragem reduz a quantidade de telemetria enviada pela sua aplicação, conservando os dados correlacionados para efeitos de diagnóstico.
>
>

## <a name="land"></a>Está tudo pronto

Parabéns! Instalou o pacote do Application Insights na sua aplicação e configurou o mesmo para enviar telemetria para o serviço do Application Insights no Azure.

O recurso do Azure que recebe a telemetria da sua aplicação é identificado por uma *chave de instrumentação*. Encontrará esta chave no ficheiro ApplicationInsights.config.


## <a name="upgrade-to-future-sdk-versions"></a>Atualizar para versões futuras do SDK
Para atualizar para uma [nova versão do SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), abra o **gestor de pacotes NuGet** e filtre os pacotes instalados. Selecione **Microsoft.ApplicationInsights.Web** e escolha **Atualizar**.

Se tiver realizado personalizações no ApplicationInsights.config, guarde uma cópia do mesmo antes de atualizar. Em seguida, intercale as suas alterações na nova versão.

## <a name="next-steps"></a>Passos Seguintes

Existem tópicos alternativos que pode ver se estiver interessado em:

* [Instrumentar aplicações Web no runtime](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Cloud Services](../../azure-monitor/app/cloudservices.md)

## <a name="video"></a>Vídeo

* Externo vídeo passo a passo sobre [configuração do Application Insights com uma aplicação .NET do zero](https://www.youtube.com/watch?v=blnGAVgMAfA).

### <a name="more-telemetry"></a>Mais telemetria

* **[Dados de carregamento da página e do browser](../../azure-monitor/app/javascript.md)** - insira um fragmento de código nas suas páginas Web.
* **[Obtenha monitorização de dependência e exceção mais detalhada](../../azure-monitor/app/monitor-performance-live-website-now.md)** - instale o Monitor de Estado no seu servidor.
* **[Eventos personalizados de código](../../azure-monitor/app/api-custom-events-metrics.md)** para ações do utilizador de contagem, hora ou medida.
* **[Obtenha dados de registo](../../azure-monitor/app/asp-net-trace-logs.md)** - correlacione registos de dados com a sua telemetria.

### <a name="analysis"></a>Análise

* **[Trabalhar com o Application Insights no Visual Studio](../../azure-monitor/app/visual-studio.md)**<br/>Inclui informações sobre depuração com telemetria, pesquisa de diagnóstico e exploração do código.
* **[Trabalhar com o portal do Application Insights](../../azure-monitor/app/app-insights-dashboards.md)**<br/> Inclui informações sobre dashboards, ferramentas avançadas de diagnóstico e análise, alertas, um mapa de dependência em direto da aplicação e exportação da telemetria.
* **[Análise](../../azure-monitor/log-query/get-started-portal.md)** - a linguagem de consulta poderosa.

### <a name="alerts"></a>Alertas

* [Testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md): Crie testes para se certificar de que o site está visível na web.
* [Diagnóstico inteligente](../../azure-monitor/app/proactive-diagnostics.md): Esses testes são executados automaticamente, para que não tenha de fazer nada para os configurar. Estes indicam se a aplicação tem uma taxa de pedidos com falha fora do normal.
* [Alertas de métricas](../../azure-monitor/app/alerts.md): Definir alertas para o avisar se uma métrica ultrapassa um limiar. Pode defini-los em métricas personalizadas que introduz no código da sua aplicação.

### <a name="automation"></a>Automatização

* [Automatizar a criação de um recurso do Application Insights](../../azure-monitor/app/powershell.md)
