---
title: Diagnósticos de transações de insights de aplicação Azure / Microsoft Docs
description: Diagnósticos de transações de ponta a ponta dos Insights
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.openlocfilehash: 39c4c51a1bc84e06efac3674b1ee5b487f9e6729
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671142"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Diagnósticos unificados de transações de componentes cruzados

A experiência de diagnóstico unificada correlaciona automaticamente a telemetria do lado do servidor de todos os seus componentes monitorizados application Insights numa única vista. Não importa se tem vários recursos com chaves de instrumentação separadas. Application Insights deteta a relação subjacente e permite diagnosticar facilmente o componente de aplicação, dependência ou exceção que causou um abrandamento ou falha de transação.

## <a name="what-is-a-component"></a>O que é um Componente?

Os componentes são partes desdobráveis independentemente da sua aplicação distribuída/microserviços. Os desenvolvedores e equipas de operações têm visibilidade de nível de código ou acesso à telemetria gerado por estes componentes de aplicação.

* Os componentes são diferentes das dependências externas "observadas", tais como SQL, EventHub, etc. a que a sua equipa/organização pode não ter acesso (código ou telemetria).
* Os componentes são executados em qualquer número de instâncias de servidor/role/contentor.
* Os componentes podem ser teclas de instrumentação de informação de aplicação separadas (mesmo que as subscrições sejam diferentes) ou funções diferentes que reportem a uma única chave de instrumentação de Insights de Aplicação. A nova experiência mostra detalhes em todos os componentes, independentemente da forma como foram configurados.

> [!NOTE]
> * **Faltando os links de item relacionados?** Toda a telemetria relacionada está nas secções [superior](#cross-component-transaction-chart) e [inferior](#all-telemetry-with-this-operation-id) do lado esquerdo. 

## <a name="transaction-diagnostics-experience"></a>Experiência de diagnóstico de transações
Esta vista tem quatro partes-chave: lista de resultados, um gráfico de transações de componentes cruzados, uma lista de sequência de tempo de toda a telemetria relacionada com esta operação, e os detalhes desfiguram-se para qualquer item de telemetria selecionado à esquerda.

![Peças-chave](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Gráfico de transações de componentes cruzados

Este gráfico fornece uma linha temporal com barras horizontais durante a duração dos pedidos e dependências entre componentes. As exceções recolhidas também estão marcadas na linha do tempo.

* A primeira linha deste gráfico representa o ponto de entrada, o pedido de entrada para o primeiro componente chamado nesta transação. A duração é o tempo total dedura para que a transação esteja concluída.
* Quaisquer chamadas para dependências externas são linhas simples não desmontáveis, com ícones que representam o tipo de dependência.
* As chamadas para outros componentes são linhas desmontáveis. Cada linha corresponde a uma operação específica invocada no componente.
* Por predefinição, o pedido, dependência ou exceção que selecionou é exibido no lado direito.
* Selecione qualquer linha para ver os seus [detalhes à direita](#details-of-the-selected-telemetry). 

> [!NOTE]
> As chamadas para outros componentes têm duas linhas: uma linha representa a chamada de saída (dependência) do componente de chamada, e a outra linha corresponde ao pedido de entrada no componente chamado. O ícone principal e o estilo distinto das barras de duração ajudam a diferenciar entre eles.

## <a name="all-telemetry-with-this-operation-id"></a>Toda a telemetria com esta Operação Id

Esta secção mostra a visão da lista plana numa sequência temporal de toda a telemetria relacionada com esta transação. Também mostra os eventos personalizados, e vestígios que não são exibidos na tabela de transações. Pode filtrar esta lista para telemetria gerada por um componente/chamada específico. Pode selecionar qualquer artigo de telemetria nesta lista para ver [os detalhes correspondentes à direita](#details-of-the-selected-telemetry).

![Sequência de tempo de toda a telemetria](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Detalhes da telemetria selecionada

Este painel desmontável mostra o detalhe de qualquer item selecionado do gráfico de transações, ou da lista. "Mostrar tudo" lista todos os atributos padrão que são recolhidos. Quaisquer atributos personalizados são listados separadamente abaixo do conjunto padrão. Clique no "..." abaixo da janela de rastreio da pilha para obter uma opção para copiar o vestígio. "Rastreios de perfis abertos" ou "Instantâneo de depuração aberto" mostra diagnósticos de nível de código em vidraças de detalhes correspondentes.

![Detalhes de exceção](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Resultados da procura

Este painel desmontável mostra os outros resultados que satisfazem os critérios do filtro. Clique em qualquer resultado para atualizar os respetivos detalhes das 3 secções acima listadas. Tentamos encontrar amostras que sejam mais prováveis de ter os detalhes disponíveis de todos os componentes, mesmo que a amostragem esteja em vigor em qualquer um deles. Estas são mostradas como amostras "sugeridas".

![Resultados da procura](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Profiler e snapshot debugger

[O perfil de Insights](../../azure-monitor/app/profiler.md) de aplicação ou o [debugger instantâneo](snapshot-debugger.md) ajudam com diagnósticos de desempenho e falhas ao nível do código. Com esta experiência, pode ver vestígios de perfis ou instantâneos de qualquer componente com um único clique.

Se não conseguir pôr o Profiler a funcionar, contacte o **profilerhelp\@microsoft.com**

Se não conseguiu que o Snapshot Debugger funcionasse, por favor contacte **a snapshothelp\@microsoft.com**

![Integração de Perfis](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>FAQ

*Vejo um único componente na tabela, e os outros só mostram como dependências externas sem qualquer detalhe do que aconteceu dentro desses componentes.*

Razões potenciais:

* Os outros componentes são instrumentados com Insights de Aplicação?
* Estão a usar o mais recente SDK de aplicação estável?
* Se estes componentes forem recursos separados da Application Insights, tem o acesso à sua telemetria?

Se tiver acesso e os componentes estiverem instrumentados com os mais recentes SDKs de Insights de Aplicação, avise-nos através do canal de feedback superior direito.

*Vejo filas duplicadas para as dependências. Isto é esperado?*

Neste momento, estamos a mostrar a chamada de dependência de saída separada do pedido de entrada. Normalmente, as duas chamadas parecem idênticas, com apenas o valor de duração a ser diferente devido à viagem de ida e volta da rede. O ícone principal e o estilo distinto das barras de duração ajudam a diferenciar entre eles. Esta apresentação dos dados é confusa? Dê-nos o seu feedback!

*E o relógio passa por diferentes instâncias componentes?*

As linhas de tempo são ajustadas para distorções de relógio no gráfico de transações. Pode ver os carimbos de hora exatos no painel de detalhes ou utilizando o Analytics.

*Porque é que a nova experiência está a perder a maioria das consultas de itens relacionados?*

Esta ação é propositada. Todos os itens relacionados, em todos os componentes, já estão disponíveis no lado esquerdo (secções superior e inferior). A nova experiência tem dois itens relacionados que o lado esquerdo não cobre: toda a telemetria de cinco minutos antes e depois deste evento e da linha do tempo do utilizador.
