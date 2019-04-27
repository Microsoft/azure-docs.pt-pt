---
title: Azure Application Insights para Windows Server e funções de trabalho | Microsoft Docs
description: Adicione manualmente o SDK do Application Insights à sua aplicação do ASP.NET para analisar a utilização, a disponibilidade e o desempenho.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 106ba99b-b57a-43b8-8866-e02f626c8190
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: mbullwin
ms.openlocfilehash: 85764c0ee5b8ed117fb191657d54abe5bd10a703
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60784491"
---
# <a name="manually-configure-application-insights-for-net-applications"></a>Configurar manualmente o Application Insights para aplicações .NET

Pode configurar o [Application Insights](../../azure-monitor/app/app-insights-overview.md) para monitorizar uma vasta gama de aplicações ou funções de aplicação, componentes ou microsserviços. Para serviços e aplicações Web, o Visual Studio oferece [configuração de um passo](../../azure-monitor/app/asp-net.md). Para outros tipos de aplicações .NET, como funções de servidor de back-end ou aplicações de ambiente de trabalho, pode configurar o Application Insights manualmente.

![Gráficos de exemplo da monitorização do desempenho](./media/windows-services/10-perf.png)

#### <a name="before-you-start"></a>Antes de começar

É necessário:

* Uma subscrição do [Microsoft Azure](https://azure.com). Se a sua equipa ou organização tiver uma subscrição do Azure, o proprietário pode adicioná-lo à mesma, utilizando a sua [conta Microsoft](https://live.com).
* Visual Studio 2013 ou posterior.

## <a name="add"></a>1. Escolher um recurso do Application Insights

O recurso é onde os dados são recolhidos e apresentados no portal do Azure. Tem de decidir se quer criar um novo ou partilhar um existente.

### <a name="part-of-a-larger-app-use-existing-resource"></a>Parte de uma aplicação maior: Utilizar o recurso existente

Se a sua aplicação Web tiver vários componentes, por exemplo, uma aplicação Web de front-end e um ou mais serviços de back-end, deve enviar a telemetria de todos os componentes para o mesmo recurso. Isto permite apresentá-los num único Mapa de Aplicações e rastrear um pedido de um componente para outro.

Assim, se já estiver a monitorizar outros componentes desta aplicação, basta utilizar o mesmo recurso.

Abra o recurso no [portal do Azure](https://portal.azure.com/). 

### <a name="self-contained-app-create-a-new-resource"></a>Aplicação autónoma: Criar um novo recurso

Se a nova aplicação não estiver relacionada com outras aplicações, deve ter o seu próprio recurso.

Inicie sessão no [Portal do Azure](https://portal.azure.com/) e crie um novo recurso do Application Insights. Escolha ASP.NET como o tipo de aplicação.

![Clicar em Novo, Application Insights](./media/windows-services/01-new-asp.png)

A escolha do tipo de aplicação define o conteúdo predefinido dos painéis de recursos.

## <a name="2-copy-the-instrumentation-key"></a>2. Copiar a Chave de Instrumentação
A chave identifica o recurso. Deverá instalá-la logo no SDK para direcionar os dados para o recurso.

![Clicar em Propriedades, selecionar a chave e premir Ctrl+C](./media/windows-services/02-props-asp.png)

## <a name="sdk"></a>3. Instalar o pacote do Application Insights na sua aplicação
A instalação e configuração do pacote do Application Insights varia consoante a plataforma em que está a trabalhar. 

1. No Visual Studio, clique com o botão direito do rato no seu projeto e selecione **Gerir Pacotes Nuget**.
   
    ![Clicar com o botão direito do rato no projeto e selecionar Gerir Pacotes Nuget](./media/windows-services/03-nuget.png)
2. Instale o pacote do Application Insights para aplicações do Windows Server, "Microsoft.ApplicationInsights.WindowsServer".
   
    ![Procurar “Application Insights”](./media/windows-services/04-ai-nuget.png)
   
    *Que versão?*

    Selecione **Incluir pré-lançamento** se quiser experimentar as nossas funcionalidades mais recentes. Os documentos ou blogues relevantes reparam se precisa de uma versão de pré-lançamento.
    
    *Posso utilizar outros pacotes?*
   
    Sim. Selecione "Microsoft.ApplicationInsights" se quiser utilizar a API para enviar a sua própria telemetria. O pacote do Windows Server inclui a API, assim como outros pacotes, como a recolha de contadores de desempenho e a monitorização de dependência. 

### <a name="to-upgrade-to-future-package-versions"></a>Para atualizar para versões futuras do pacote
Lançamos novas versões do SDK, ocasionalmente.

Para atualizar para uma [nova versão do pacote](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases/), abra o gestor de pacotes NuGet novamente e filtre os pacotes instalados. Selecione **Microsoft.ApplicationInsights.WindowsServer** e escolha **Atualizar**.

Se tiver efetuado personalizações no ApplicationInsights.config, guarde uma cópia do mesmo antes de atualizar e, posteriormente, intercale as alterações na nova versão.

## <a name="4-send-telemetry"></a>4. Enviar telemetria
**Se instalou apenas o pacote de API:**

* Defina a chave de instrumentação no código, por exemplo `main()`: 
  
    `TelemetryConfiguration.Active.InstrumentationKey = "` *a sua chave* `";` 
* [Escreva a sua própria telemetria utilizando a API](../../azure-monitor/app/api-custom-events-metrics.md#ikey).

**Se tiver instalado outros pacotes do Application Insights,** pode, se preferir, utilizar o ficheiro .config para definir a chave de instrumentação:

* Edite ApplicationInsights.config (que foi adicionado à instalação do NuGet). Insira-o imediatamente antes da tag de fecho:
  
    `<InstrumentationKey>` *a chave de instrumentação que copiou* `</InstrumentationKey>`
* Certifique-se de que as propriedades de ApplicationInsights.config no Explorador de Soluções estão definidas para **Ação de Compilação = Conteúdo, Copiar para o Diretório de Saída = Copiar**.

É útil definir a chave de instrumentação no código, se quiser [mudar a chave de diferentes configurações de compilações](../../azure-monitor/app/separate-resources.md). Se definir a chave no código, não tem de defini-la no ficheiro `.config`.

## <a name="run"></a> Executar o projeto
Utilize **F5** para executar e experimentar a aplicação: abra páginas diferentes para gerar alguma telemetria.

No Visual Studio, verá uma contagem dos eventos que foram enviados.

![Contagem de eventos no Visual Studio](./media/windows-services/appinsights-09eventcount.png)

## <a name="monitor"></a> Ver a telemetria
Volte ao [Portal do Azure](https://portal.azure.com/) e navegue para o recurso do Application Insights.

Procure dados nos gráficos Descrição Geral. Inicialmente, verá apenas um ou dois pontos. Por exemplo:

![Clicar para mais dados](./media/windows-services/12-first-perf.png)

Clique em qualquer gráfico para ver métricas mais detalhadas. [Saiba mais sobre métricas.](../../azure-monitor/app/web-monitor-performance.md)

### <a name="no-data"></a>Não existem dados?
* Utilize a aplicação, abrindo páginas diferentes, de modo a gerar alguma telemetria.
* Abra o mosaico [Pesquisa](../../azure-monitor/app/diagnostic-search.md) para ver eventos individuais. Por vezes, os eventos demoram um pouco mais de tempo a chegar ao pipeline de métricas.
* Aguarde alguns segundos e clique em **Atualizar**. Os gráficos atualizam-se periodicamente, mas pode atualizá-los manualmente se estiver à espera que apareçam alguns dados.
* Veja [Resolução de Problemas](../../azure-monitor/app/troubleshoot-faq.md).

## <a name="publish-your-app"></a>Publicar a aplicação
Em seguida, implemente a aplicação no servidor ou no Azure e veja os dados a acumularem.

![Utilizar o Visual Studio para publicar a aplicação](./media/windows-services/15-publish.png)

Quando é executada em modo de depuração, a telemetria é emitida através do pipeline, pelo que deve ver os dados que aparecem em segundos. Ao implementar a aplicação na configuração da Versão, os dados acumulam-se mais lentamente.

### <a name="no-data-after-you-publish-to-your-server"></a>Não existem dados depois de publicar no servidor?
Abra portas para o tráfego de saída na firewall do servidor. Consulte [esta página](https://docs.microsoft.com/azure/application-insights/app-insights-ip-addresses) para ver a lista de endereços necessários 

### <a name="trouble-on-your-build-server"></a>Problemas no servidor de compilação?
Veja [este item de Resolução de Problemas](../../azure-monitor/app/asp-net-troubleshoot-no-data.md#NuGetBuild).

> [!NOTE]
> Se a sua aplicação gerar uma grande quantidade de telemetria, o módulo de amostragem adaptável irá reduzir automaticamente o volume que é enviado para o portal, enviando apenas uma fração representativa dos eventos. No entanto, os eventos relacionados com o mesmo pedido serão selecionados ou desmarcados como um grupo, para que possa navegar entre os eventos relacionados. 
> [Saiba mais sobre a amostragem](../../azure-monitor/app/sampling.md).
> 
> 

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Passos Seguintes
* [Adicione mais telemetria](../../azure-monitor/app/asp-net-more.md) para obter a vista completa em 360 graus da sua aplicação.

