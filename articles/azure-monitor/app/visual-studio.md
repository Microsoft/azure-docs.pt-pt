---
title: Depurar aplicativos com o Azure Application Insights no Visual Studio | Documentos da Microsoft
description: Análise de desempenho da aplicação Web e diagnóstico durante a depuração e produção.
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: carmonm
ms.assetid: 2059802b-1131-477e-a7b4-5f70fb53f974
ms.service: application-insights
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/07/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: b22f52ce9aa7f57e5b6638eef580a2675d5c9b1f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60372571"
---
# <a name="debug-your-applications-with-azure-application-insights-in-visual-studio"></a>Depurar seus aplicativos com o Azure Application Insights no Visual Studio
No Visual Studio (versão 2015 e posterior), pode analisar o desempenho e diagnosticar problemas de depuração e produção na sua aplicação Web ASP.NET, com a telemetria do [Application Insights do Azure](../../azure-monitor/app/app-insights-overview.md).

Se criou a aplicação Web ASP.NET com o Visual Studio 2017 ou posterior, já tem o SDK do Application Insights. Caso contrário, se ainda não o tiver feito, [adicione o Application Insights à sua aplicação](../../azure-monitor/app/asp-net.md).

Para monitorizar a aplicação quando estiver a ser produzida, vê normalmente a telemetria do Application Insights no [portal do Azure](https://portal.azure.com), onde pode definir alertas e aplicar ferramentas de monitorização poderosas. Mas, para a depuração, também pode procurar e analisar a telemetria no Visual Studio. Pode usar o Visual Studio para analisar a telemetria do seu site de produção e de depuração é executado no computador de desenvolvimento. Em último caso, pode analisar as execuções das depurações, mesmo que ainda não tenha configurado o SDK para enviar telemetria para o portal do Azure. 

## <a name="run"></a> Depurar o projeto
Execute a aplicação Web no modo de depuração local com o F5. Abra páginas diferentes para gerar alguma telemetria.

No Visual Studio, verá uma contagem dos eventos que tenham sido registados pelo módulo do Application Insights no seu projeto.

![No Visual Studio, o botão do Application Insights é apresentado durante a depuração.](./media/visual-studio/appinsights-09eventcount.png)

Clique neste botão para procurar a sua telemetria. 

## <a name="application-insights-search"></a>Pesquisa do Application Insights
A janela Pesquisa do Application Insights mostra os eventos que foram registados. (Se iniciou sessão Azure ao configurar o Application Insights, pode pesquisar os mesmos eventos no portal do Azure).

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
Apresentação de relatórios de exceção na janela de Pesquisa. (Em alguns tipos mais antigos da aplicação ASP.NET, tem de [configurar a monitorização de exceção](../../azure-monitor/app/asp-net-exceptions.md) para ver as exceções que são processadas pelo framework.)

Clique numa exceção para obter um rastreio de pilha. Se abrir o código da aplicação no Visual Studio, pode clicar no rastreio de pilha para a linha relevante do código.

![Rastreio de pilha de exceção](./media/visual-studio/17.png)

## <a name="view-request-and-exception-summaries-in-the-code"></a>Consulte os resumos de pedido e exceção no código
Na linha código lente acima de cada método de manipulador, verá uma contagem dos pedidos e exceções registadas pelo Application Insights nas últimas 24 horas.

![Rastreio de pilha de exceção](./media/visual-studio/21.png)

> [!NOTE] 
> O Código Lente mostra os dados do Application Insights apenas se tiver [configurado a sua aplicação para enviar telemetria para o portal do Application Insights](../../azure-monitor/app/asp-net.md).
>

[Saiba mais sobre o Application Insights no Código Lente](../../azure-monitor/app/visual-studio-codelens.md)

## <a name="trends"></a>Tendências
Tendências é uma ferramenta para visualizar a forma como a aplicação se comporta ao longo do tempo. 

Selecione **Explorar Tendências de Telemetria** no botão da barra de ferramentas do Application Insights ou na janela Pesquisa do Application Insights Escolha uma das cinco consultas comuns para começar. Pode analisar os diferentes conjuntos de dados com base em tipos de telemetria, intervalos de tempo e outras propriedades. 

Para localizar anomalias nos seus dados, escolha uma das opções anómalas na lista pendente "Tipo de Vista". As opções de filtragem na parte inferior da janela facilitam a maximização de subconjuntos específicos da sua telemetria.

![Tendências](./media/visual-studio/51.png)

[Mais informações sobre as Tendências](../../azure-monitor/app/visual-studio-trends.md).

## <a name="local-monitoring"></a>Monitorização local
(A partir do Visual Studio 2015 Update 2) Se ainda não tiver configurado o SDK para enviar telemetria ao portal do Application Insights (para que não existe nenhuma chave de instrumentação no applicationinsights. config), em seguida, a janela de diagnóstico apresenta a telemetria da sua sessão de depuração mais recente. 

Esta ação é desejável se já tiver publicado uma versão anterior da aplicação. De certeza que não quer que a telemetria das suas sessões de depuração seja misturada com a telemetria no portal do Application Insights da aplicação publicada.

Também poderá ser útil se tiver alguma [telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md) que pretende depurar antes de enviar a telemetria ao portal.

* *Inicialmente, configurei totalmente o Application Insights para enviar telemetria para o portal. No entanto, agora, apenas quero a telemetria no Visual Studio.*
  
  * Nas Definições da janela Pesquisa, encontrará uma opção para procurar o diagnóstico local, mesmo se a sua aplicação enviar telemetria para o portal.
  * Para parar o envio de telemetria para o portal, comente a linha `<instrumentationkey>...` no ApplicationInsights.com. Quando estiver pronto para enviar novamente telemetria para o portal, anule o comentário.


## <a name="next-steps"></a>Passos Seguintes
|  |  |
| --- | --- |
| **[Adicionar mais dados](../../azure-monitor/app/asp-net-more.md)**<br/>Monitorize a utilização, a disponibilidade, as dependências e as exceções. Integre rastreios a partir de arquiteturas de registo. Grave a telemetria personalizada. |![Visual Studio](./media/visual-studio/64.png) |
| **[Trabalhar com o portal do Application Insights](../../azure-monitor/app/app-insights-dashboards.md)**<br/>Ver dashboards, ferramentas de diagnósticos e análise poderosas, alertas, mapa de dependência em direto da aplicação e dados de telemetria exportado. |![Visual Studio](./media/visual-studio/62.png) |

