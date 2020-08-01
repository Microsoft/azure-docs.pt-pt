---
title: Configurar a análise de aplicações Web para ASP.NET com o Azure Application Insights | Microsoft Docs
description: Configure as ferramentas de análise de desempenho, disponibilidade e comportamento do utilizador para o seu website ASP.NET, hospedado no local ou em Azure.
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: acfba63cba520631831888a1480929be3b1897f0
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475537"
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Configurar o Application Insights para o seu site ASP.NET

Este procedimento configura a sua aplicação Web do ASP.NET para enviar telemetria para o serviço [Azure Application Insights](./app-insights-overview.md). Funciona para aplicações ASP.NET alojadas no seu próprio servidor IIS no local ou na Cloud. Obtém gráficos e um idioma de consulta poderoso que o ajudam a compreender o desempenho da sua aplicação e como as pessoas estão a utilizá-la, bem como alertas automáticos sobre falhas ou problemas de desempenho. Muitos programadores acham que estas funcionalidades são excelentes tal como estão, mas também pode expandir e personalizar a telemetria se precisar.

A configuração demora apenas alguns cliques no Visual Studio. Tem a opção de evitar encargos ao limitar o volume de telemetria. Esta funcionalidade permite-lhe experimentar e depurar, ou monitorizar um site com poucos utilizadores. Quando decidir que pretende continuar e monitorizar o seu site de produção, pode elevar o limite facilmente mais tarde.

## <a name="prerequisites"></a>Pré-requisitos
Para adicionar o Application Insights ao site ASP.NET, é necessário:

- Instale [o Visual Studio 2019 para windows](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
    - ASP.NET e desenvolvimento web (Não desmarque os componentes opcionais)
    - Desenvolvimento do Azure

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="step-1-add-the-application-insights-sdk"></a><a name="ide"></a> Passo 1: Adicionar o Application Insights SDK

> [!IMPORTANT]
> As imagens deste exemplo são baseadas na versão 15.9.9 do Visual Studio 2017 e posteriormente. A experiência de adicionar Application Insights varia em todas as versões do Visual Studio, bem como por ASP.NET tipo de modelo. As versões mais antigas podem ter textos alternativos, como "Configure Application Insights".

Clique com o botão direito no nome da sua aplicação web no Solution Explorer e escolha **Adicionar**  >  **Telemetria de Insights de Aplicação**

![Captura de ecrã do Explorador de Soluções, com a opção Configurar Application Insights realçada](./media/asp-net/add-telemetry-new.png)

(Dependendo da versão do SDK do Application Insights, poderá ser-lhe pedido para atualizar para a versão mais recente do SDK. Se lhe for pedido, selecione **Atualizar SDK**.)

![Captura de ecrã: uma nova versão do SDK do Microsoft Application Insights está disponível. Atualizar SDK realçado](./media/asp-net/0002-update-sdk.png)

Ecrã de Configuração do Application Insights:

**Selecione Começar**.

![Captura de ecrã da página Registar a sua aplicação no Application Insights](./media/asp-net/00004-start-free.png)

Se pretender definir o grupo de recursos ou a localização onde os seus dados são armazenado, clique em **Configurar definições**. Os grupos de recursos são utilizados para controlar o acesso aos dados. Por exemplo, se tiver várias aplicações que fazem parte do mesmo sistema, poderá colocar os seus dados do Application Insights no mesmo grupo de recursos.

 Selecione **Registar**.

![Captura de ecrã da página Registar a sua aplicação no Application Insights](./media/asp-net/00005-register-ed.png)

 Selecione **Project**  >  **Manage NuGet Packages Packages**  >  **Packages: nuget.org** > Confirme que tem a mais recente versão estável do Application Insights SDK.

 A telemetria será enviada para o [portal do Azure](https://portal.azure.com), durante a depuração e após ter publicado a aplicação.
> [!NOTE]
> Se não pretender enviar telemetria para o portal enquanto estiver a depurar, basta adicionar o SDK do Application Insights à sua aplicação, mas não configure um recurso no portal. Pode ver a telemetria no Visual Studio enquanto faz a depuração. Mais tarde, pode voltar a esta página de configuração ou pode esperar até depois de implementar a aplicação e [ativar a telemetria em tempo de execução](./status-monitor-v2-overview.md).

## <a name="step-2-run-your-app"></a><a name="run"></a> Passo 2: Executar a aplicação
Execute a aplicação com F5. Abra páginas diferentes para gerar alguma telemetria.

No Visual Studio, verá uma contagem dos eventos que foram registados.

![Captura de ecrã do Visual Studio. O botão do Application Insights é apresentado durante a depuração.](./media/asp-net/00006-Events.png)

## <a name="step-3-see-your-telemetry"></a>Passo 3: Ver a telemetria
Pode ver a sua telemetria no Visual Studio ou no portal Web do Application Insights. Procure telemetria no Visual Studio para o ajudar a depurar a sua aplicação. Monitorize o desempenho e a utilização no portal Web quando o sistema estiver em direto. 

### <a name="see-your-telemetry-in-visual-studio"></a>Consulte a sua telemetria no Visual Studio

No Visual Studio, para ver os dados do Application Insights.  Selecione Serviços Conectados do Explorador de **Solução**  >  **Connected Services** > clique à direita em Insights de **aplicação**e, em seguida, clique em **Search Live Telemetria**.

Na janela de Pesquisa do Visual Studio Application Insights, verá os dados da aplicação para ver a telemetria gerada no lado do servidor da aplicação. Experimente os filtros e clique em qualquer evento para ver mais detalhes.

![Captura de ecrã da vista Dados da sessão de Depuração na janela do Application Insights.](./media/asp-net/55.png)

> [!Tip]
> Se não existirem quaisquer dados, certifique-se de que o intervalo de tempo está correto e clique no ícone de Pesquisa.

[Saiba mais sobre as ferramentas do Application Insights no Visual Studio](./visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Consulte a telemetria no portal Web

Também pode ver a telemetria no portal Web do Application Insights, (a não ser que tenha optado por instalar apenas o SDK). O portal tem mais gráficos, ferramentas de análise e vistas de vários componentes que o Visual Studio. O portal também fornece alertas.

Abra o recurso do Application Insights. Inicie sessão no [portal do Azure](https://portal.azure.com/) e encontre-o aí ou selecione **Explorador de Soluções** > **Serviços Ligados** > com o botão direito **Application Insights** > **Abrir Portal do Application Insights** e permita que o mesmo o guie.

O portal é aberto na vista da telemetria da sua aplicação.

![Captura de ecrã da página de descrição geral do Application Insights](./media/asp-net/007.png)

No portal, clique em qualquer gráfico ou mosaico para ver mais detalhes.

## <a name="step-4-publish-your-app"></a>Passo 4: Publicar a aplicação
Publique a sua aplicação no seu servidor IIS ou no Azure. Veja o [Live Metrics Stream](./live-stream.md) para garantir que não existem problemas.

A sua telemetria acumula-se no portal Application Insights, onde pode monitorizar métricas, pesquisar a sua telemetria. Você também pode usar a poderosa [linguagem de consulta Kusto](/azure/kusto/query/) para analisar o uso e desempenho, ou para encontrar eventos específicos.

Também pode continuar a analisar a sua telemetria no [Visual Studio](./visual-studio.md) com ferramentas como a pesquisa de diagnóstico e as [tendências](./visual-studio-trends.md).

> [!NOTE]
> Se a sua aplicação enviar telemetria suficiente para se aproximar dos [limites de monitorização](./pricing.md#limits-summary), é ativada a [amostragem](./sampling.md) automática. A amostragem reduz a quantidade de telemetria enviada pela sua aplicação, conservando os dados correlacionados para efeitos de diagnóstico.
>
>

## <a name="youre-all-set"></a><a name="land"></a>Está tudo preparado.

Parabéns! Instalou o pacote do Application Insights na sua aplicação e configurou o mesmo para enviar telemetria para o serviço do Application Insights no Azure.

O recurso do Azure que recebe a telemetria da sua aplicação é identificado por uma *chave de instrumentação*. Encontrará esta chave no ficheiro ApplicationInsights.config.


## <a name="upgrade-to-future-sdk-versions"></a>Atualizar para versões futuras do SDK

* [Notas de Versão](./release-notes.md)

Para atualizar para um novo lançamento do SDK, abra o **gestor de pacotes NuGet**e filtre em pacotes instalados. Selecione **Microsoft.ApplicationInsights.Web**e escolha **Upgrade**.

Se tiver realizado personalizações no ApplicationInsights.config, guarde uma cópia do mesmo antes de atualizar. Em seguida, intercale as suas alterações na nova versão.

## <a name="next-steps"></a>Passos seguintes

Existem tópicos alternativos que pode ver se estiver interessado em:

* [Instrumentar aplicações Web no runtime](./monitor-performance-live-website-now.md)
* [Cloud Services do Azure](./cloudservices.md)

### <a name="more-telemetry"></a>Mais telemetria

* **[Dados de carregamento da página e do browser](./javascript.md)** - insira um fragmento de código nas suas páginas Web.
* **[Obtenha monitorização de dependência e exceção mais detalhada](./monitor-performance-live-website-now.md)** - instale o Monitor de Estado no seu servidor.
* **[Código eventos personalizados](./api-custom-events-metrics.md)** para contar, tempo ou medir ações do utilizador.
* **[Obtenha dados de registo](./asp-net-trace-logs.md)** - correlacione registos de dados com a sua telemetria.

### <a name="analysis"></a>Análise

* **[Trabalhar com o Application Insights no Visual Studio](./visual-studio.md)**<br/>Inclui informações sobre depuração com telemetria, pesquisa de diagnóstico e exploração do código.
* **[Análise](../log-query/get-started-portal.md)** - a linguagem de consulta poderosa.

### <a name="alerts"></a>Alertas

* [Testes de disponibilidade](./monitor-web-app-availability.md): Crie testes para garantir que o seu site está visível na Web.
* [Diagnóstico inteligente](./proactive-diagnostics.md): Estes testes são executados automaticamente, pelo que não precisa de fazer nada para os configurar. Estes indicam se a aplicação tem uma taxa de pedidos com falha fora do normal.
* [Alertas métricos](../platform/alerts-log.md): Desave os alertas para o avisar se uma métrica atravessar um limiar. Pode defini-los em métricas personalizadas que introduz no código da sua aplicação.

### <a name="automation"></a>Automatização

* [Automatizar a criação de um recurso do Application Insights](./powershell.md)

