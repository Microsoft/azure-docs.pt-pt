---
title: Diagnóstico de transação das informações de aplicação do Azure | Documentos da Microsoft
description: Diagnóstico de transação de ponta a ponta do aplicativo das informações
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: ba72f273724bb45418b104b1245648419ababbf0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57903628"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Diagnóstico de transação de vários componentes unificada

O diagnóstico unificado automaticamente experiência correlaciona a telemetria de lado do servidor a partir de entre todos os componentes do Application Insights monitorizadas numa única vista. Não importa se tiver vários recursos com chaves de instrumentação separado. Application Insights Deteta a relação subjacente e permite-lhe diagnosticar facilmente o componente da aplicação, a dependência ou a exceção que causou um abrandamento de transação ou falha.

## <a name="what-is-a-component"></a>O que é um componente?

Os componentes são partes implementáveis independentemente do aplicativo/microsserviços distribuídos. As equipes de operações e os desenvolvedores têm visibilidade de nível de código ou de acesso à telemetria gerado por estes componentes da aplicação.

* Componentes são diferentes de "observadas" dependências externas, como SQL, etc. de EventHub que sua equipe/organização não pode ter acesso ao (código ou telemetria).
* Componentes são executados em qualquer número de instâncias de função/servidor/contentor.
* Componentes podem ser separadas chaves de instrumentação do Application Insights (mesmo que as subscrições são diferentes) ou de diferentes funções de relatório para uma única chave de instrumentação do Application Insights. A nova experiência mostra os detalhes em todos os componentes, independentemente da forma como eles foram definidos.

> [!NOTE]
> * **Em falta as ligações de item relacionado?** Toda a telemetria relacionada estão no [top](#cross-component-transaction-chart) e [na parte inferior](#all-telemetry-with-this-Operation-Id) seções do lado esquerdo. 

## <a name="transaction-diagnostics-experience"></a>Experiência de diagnóstico de transação
Esta vista tem quatro partes-chave: resulta de lista, um gráfico de transações entre componentes, uma lista da sequência de tempo de toda a telemetria relacionada com esta operação e o painel de detalhes de qualquer item de telemetria selecionado à esquerda.

![Partes-chave](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Gráfico de transações entre componentes

Este gráfico fornece uma linha cronológica com barras horizontais para a duração dos pedidos e dependências entre componentes. Todas as exceções que são recolhidas são também marcadas na linha da tempo.

* A linha superior neste gráfico representa o ponto de entrada, a solicitação de entrada para o primeiro componente chamado nesta transação. A duração é o tempo total decorrido para a conclusão da transação.
* Todas as chamadas para dependências externas são linhas não recolhível simples, com ícones que representa o tipo de dependência.
* Chamadas para outros componentes são linhas recolhíveis. Cada linha corresponde a uma operação específica invocada no componente.
* Por predefinição, o pedido, dependência ou exceção que selecionou é apresentado no lado direito.
* Selecione qualquer linha para ver seu [detalhes à direita](#details-of-the-selected-telemetry). 

> [!NOTE]
> Chamadas para outros componentes tem duas linhas: uma linha representa a chamada de saída (dependência) do componente do autor da chamada do e a outra linha corresponde ao pedido de entrada, o componente chamado. O ícone à esquerda e o estilo distinto das barras de duração ajudar a diferenciar entre eles.

## <a name="all-telemetry-with-this-operation-id"></a>Toda a telemetria com este Id de operação

Esta secção mostra a vista de lista simples de uma seqüência de tempo de toda a telemetria relacionados com esta transação. Ela também mostra os eventos personalizados e os rastreios que não são apresentados no gráfico de transação. Pode filtrar esta lista para a telemetria gerada por uma chamada/componente específica. Pode selecionar qualquer item de telemetria de nesta lista para ver o correspondente [detalhes à direita](#details-of-the-selected-telemetry).

![Sequência de tempo de toda a telemetria](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Detalhes da telemetria selecionada

Este painel recolhível mostra os detalhes de qualquer item selecionado do gráfico de transação ou na lista. "Mostrar todos os" lista todos os atributos padrão que são recolhidos. Todos os atributos personalizados em separado estão listados abaixo do conjunto padrão. Clique no "..." abaixo a janela de rastreio de pilha para obter uma opção para copiar o rastreio. "Rastreios do profiler abrir" ou "Abrir instantâneo de depuração" mostra o diagnóstico ao nível do código nos painéis de detalhes correspondente.

![Detalhe da exceção](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Resultados da pesquisa

Este painel recolhível mostra os outros resultados que cumprem os critérios de filtro. Clique em qualquer resultado para atualizar os respectivos detalhes 3 secções listados acima. Vamos tentar encontrar exemplos que são mais provável que tenha os detalhes disponíveis em todos os componentes, mesmo que a amostragem está em vigor em qualquer um deles. Estes são apresentados como exemplos "sugeridos".

![Resultados da pesquisa](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Depurador Profiler e o instantâneo

[O criador de perfil do Application Insights](../../azure-monitor/app/profiler.md) ou [depurador de instantâneos](snapshot-debugger.md) ajudar com o nível de código de diagnóstico de problemas de desempenho e falha. Com essa experiência, pode ver os rastreios do profiler ou instantâneos de qualquer componente com um único clique.

Se não foi possível obter o Profiler a funcionar, contacte **serviceprofilerhelp\@microsoft.com**

Se não foi possível obter o trabalho de depurador de instantâneos, entre em contato **snapshothelp\@microsoft.com**

![Integração do Profiler](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>FAQ

*Posso ver um único componente no gráfico e a outras pessoas estão a aparecer apenas como dependências externas sem nenhum detalhe do que aconteceu dentro desses componentes.*

Razões possíveis:

* Os outros componentes são equipados com o Application Insights?
* Elas estão usando o estável mais recente de Application Insights SDK?
* Se esses componentes são recursos do Application Insights separados, tem acesso necessário à sua telemetria?

Se tem acesso e os componentes são equipados com os SDKs do Application Insights mais recente, fale conosco através do canal de comentários direita superior.

*Posso ver as linhas duplicadas para as dependências. É isto esperado?*

Neste momento, estamos mostrando a chamada de dependência de saída separado da solicitação de entrada. Normalmente, as duas chamadas dar uma olhada idênticas com apenas o valor de duração a ser diferente devido à rede de ida e volta. O ícone à esquerda e o estilo distinto das barras de duração ajudar a diferenciar entre eles. Esta apresentação dos dados é confuso? Forneça seus comentários!

*O que dizer sobre o relógio inclina em instâncias do componente diferentes?*

As linhas do tempo são ajustadas para desvios de relógio no gráfico de transação. Pode ver os carimbos exatos no painel de detalhes ou com a análise.

*Por que a nova experiência está em falta a maioria das consultas de itens relacionados?*

Esta ação é propositada. Todos os itens relacionados, em todos os componentes, já estão disponíveis no lado esquerdo (superior e inferior secções). A nova experiência tem dois itens relacionados à esquerda não abrange: toda a telemetria de cinco minutos antes e depois deste evento e a linha cronológica do utilizador.
