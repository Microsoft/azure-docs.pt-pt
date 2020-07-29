---
title: Debug em Estúdio Visual com Azure Application Insights
description: Análise de desempenho da aplicação Web e diagnóstico durante a depuração e produção.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/07/2017
ms.reviewer: mbullwin
ms.custom: vs-azure
ms.openlocfilehash: 1bb18cc43ef306a208500ff49ec41dbfb74b788e
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323473"
---
# <a name="debug-your-applications-with-azure-application-insights-in-visual-studio"></a>Depurar as suas aplicações com a Azure Application Insights no Visual Studio
No Visual Studio (versão 2015 e posterior), pode analisar o desempenho e diagnosticar problemas de depuração e produção na sua aplicação Web ASP.NET, com a telemetria do [Application Insights do Azure](./app-insights-overview.md).

Se criou a aplicação Web ASP.NET com o Visual Studio 2017 ou posterior, já tem o SDK do Application Insights. Caso contrário, se ainda não o tiver feito, [adicione o Application Insights à sua aplicação](./asp-net.md).

Para monitorizar a aplicação quando estiver a ser produzida, vê normalmente a telemetria do Application Insights no [portal do Azure](https://portal.azure.com), onde pode definir alertas e aplicar ferramentas de monitorização poderosas. Mas, para a depuração, também pode procurar e analisar a telemetria no Visual Studio. Pode utilizar o Visual Studio para analisar a telemetria tanto a partir do seu local de produção como a partir de depurar corridas na sua máquina de desenvolvimento. Em último caso, pode analisar as execuções das depurações, mesmo que ainda não tenha configurado o SDK para enviar telemetria para o portal do Azure. 

## <a name="debug-your-project"></a><a name="run"></a> Depurar o projeto
Execute a aplicação Web no modo de depuração local com o F5. Abra páginas diferentes para gerar alguma telemetria.

No Visual Studio, vê uma contagem dos eventos que foram registados pelo módulo Application Insights no seu projeto.

![No Visual Studio, o botão do Application Insights é apresentado durante a depuração.](./media/visual-studio/appinsights-09eventcount.png)

Clique neste botão para procurar a sua telemetria. 

## <a name="application-insights-search"></a>Pesquisa do Application Insights
A janela Pesquisa do Application Insights mostra os eventos que foram registados. (Se se inscreveu no Azure quando configurar o Application Insights, pode pesquisar os mesmos eventos no portal Azure.)

![Clique com o botão direito do rato no projeto e escolha Application Insights, Pesquisa](./media/visual-studio/34.png)

> [!NOTE] 
> Depois de selecionar ou desmarcar filtros, clique no botão Pesquisar no fim do campo de pesquisa de texto.
>

A pesquisa de texto livre funciona em todos campos dos eventos. Por exemplo, procurar parte do URL de uma página; ou o valor de uma propriedade como a cidade do cliente; ou palavras específicas no registo de rastreio.

Clique em qualquer evento para ver as respetivas propriedades detalhadas.

Para pedidos para a sua aplicação Web, pode clicar para o código.

![Em Detalhes do Pedido, clique no código](./media/visual-studio/31.png)

Pode ainda abrir os itens relacionados para ajudar a diagnosticar os pedidos falhados ou as exceções.

![Em Detalhes do Pedido, desloque para baixo para itens relacionados](./media/visual-studio/41.png)

## <a name="view-exceptions-and-failed-requests"></a>Ver exceções e pedidos falhados
Apresentação de relatórios de exceção na janela de Pesquisa. (Em alguns tipos mais antigos da aplicação ASP.NET, tem de [configurar a monitorização de exceção](./asp-net-exceptions.md) para ver as exceções que são processadas pelo framework.)

Clique numa exceção para obter um rastreio de pilha. Se abrir o código da aplicação no Visual Studio, pode clicar no rastreio de pilha para a linha relevante do código.

![Rastreio de pilha de exceção](./media/visual-studio/17.png)

## <a name="view-request-and-exception-summaries-in-the-code"></a>Ver resumos de pedido e exceção no código
Na linha De Lente de Código acima de cada método de manipulador, vê uma contagem dos pedidos e exceções registados pela Application Insights nos últimos 24 h.

![Rastreio de pilha de exceção](./media/visual-studio/21.png)

> [!NOTE] 
> O Código Lente mostra os dados do Application Insights apenas se tiver [configurado a sua aplicação para enviar telemetria para o portal do Application Insights](./asp-net.md).
>

[Saiba mais sobre o Application Insights no Código Lente](./visual-studio-codelens.md)

## <a name="trends"></a>Tendências
Tendências é uma ferramenta para visualizar a forma como a aplicação se comporta ao longo do tempo. 

Selecione **Explorar Tendências de Telemetria** no botão da barra de ferramentas do Application Insights ou na janela Pesquisa do Application Insights Escolha uma das cinco consultas comuns para começar. Pode analisar os diferentes conjuntos de dados com base em tipos de telemetria, intervalos de tempo e outras propriedades. 

Para localizar anomalias nos seus dados, escolha uma das opções anómalas na lista pendente "Tipo de Vista". As opções de filtragem na parte inferior da janela facilitam a maximização de subconjuntos específicos da sua telemetria.

![Tendências](./media/visual-studio/51.png)

[Mais informações sobre as Tendências](./visual-studio-trends.md).

## <a name="local-monitoring"></a>Monitorização local
(A partir do Visual Studio 2015 Update 2) Se ainda não configurar o SDK para enviar telemetria para o portal Application Insights (de modo a que não haja uma chave de instrumentação no ApplicationInsights.config) então a janela de diagnóstico exibe telemetria a partir da sua última sessão de depuração. 

Esta ação é desejável se já tiver publicado uma versão anterior da aplicação. De certeza que não quer que a telemetria das suas sessões de depuração seja misturada com a telemetria no portal do Application Insights da aplicação publicada.

Também poderá ser útil se tiver alguma [telemetria personalizada](./api-custom-events-metrics.md) que pretende depurar antes de enviar a telemetria ao portal.

* *No início, configurei completamente os Insights de Aplicação para enviar telemetria para o portal. Mas agora gostaria de ver a telemetria apenas no Estúdio Visual.*
  
  * Nas Definições da janela Pesquisa, encontrará uma opção para procurar o diagnóstico local, mesmo se a sua aplicação enviar telemetria para o portal.
  * Para impedir que a telemetria seja enviada para o portal, comente a linha `<instrumentationkey>...` a partir de ApplicationInsights.config. Quando estiver pronto para enviar telemetria para o portal de novo, desconseça-la.


## <a name="next-steps"></a>Passos seguintes
|  |  |
| --- | --- |
| **[Adicionar mais dados](./asp-net-more.md)**<br/>Monitorize a utilização, a disponibilidade, as dependências e as exceções. Integre rastreios a partir de arquiteturas de registo. Grave a telemetria personalizada. |![Visual Studio](./media/visual-studio/64.png) |
| **[Trabalhar com o portal do Application Insights](./overview-dashboard.md)**<br/>Veja dashboards, ferramentas de diagnóstico e analítica poderosas, alertas, um mapa de dependência ao vivo da sua aplicação e dados de telemetria exportados. |![Visual Studio](./media/visual-studio/62.png) |

