---
title: Azure Application Insights Transaction Diagnostics / Microsoft Docs
description: Diagnósticos de transações de informação de aplicação
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.openlocfilehash: 552e80d6c90a18180ab41dce72e995b6804a91bc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002056"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Diagnósticos unificados de transações de componentes cruzados

A experiência de diagnóstico unificada correlaciona automaticamente a telemetria do lado do servidor de todos os seus componentes monitores application insights numa única visão. Não importa se tem vários recursos com chaves de instrumentação separadas. A Application Insights deteta a relação subjacente e permite diagnosticar facilmente o componente de aplicação, dependência ou exceção que causou um abrandamento ou falha de transação.

## <a name="what-is-a-component"></a>O que é um Componente?

Os componentes são peças desdobráveis independentemente da sua aplicação distribuída/microserviços. Os desenvolvedores e equipas de operações têm visibilidade de nível de código ou acesso à telemetria gerada por estes componentes de aplicação.

* Os componentes são diferentes das dependências externas "observadas", tais como SQL, EventHub, etc. às quais a sua equipa/organização pode não ter acesso (código ou telemetria).
* Os componentes funcionam em qualquer número de instâncias do servidor/função/contentor.
* Os componentes podem ser teclas de instrumentação de Insights de Aplicação separadas (mesmo que as subscrições sejam diferentes) ou diferentes funções reportando a uma única chave de instrumentação application insights. A nova experiência mostra detalhes em todos os componentes, independentemente da forma como foram criados.

> [!NOTE]
> * **Faltando os links de artigos relacionados?** Toda a telemetria relacionada está nas secções [superior](#cross-component-transaction-chart) e [inferior](#all-telemetry-with-this-operation-id) do lado esquerdo. 

## <a name="transaction-diagnostics-experience"></a>Experiência de diagnóstico de transações
Esta vista tem quatro partes-chave: lista de resultados, um gráfico de transação de componentes cruzados, uma lista de sequência de tempo de toda a telemetria relacionada com esta operação, e o painel de detalhes para qualquer item de telemetria selecionado à esquerda.

![Peças-chave](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Gráfico de transações de componentes cruzados

Este gráfico fornece uma linha do tempo com barras horizontais para a duração dos pedidos e dependências entre os componentes. Quaisquer exceções recolhidas também estão marcadas na linha do tempo.

* A primeira linha deste gráfico representa o ponto de entrada, o pedido de entrada para o primeiro componente chamado nesta transação. A duração é o tempo total necessário para a transação ser concluída.
* Quaisquer chamadas para dependências externas são linhas simples não desmontáveis, com ícones que representam o tipo de dependência.
* As chamadas para outros componentes são linhas desmontáveis. Cada linha corresponde a uma operação específica invocada no componente.
* Por predefinição, o pedido, dependência ou exceção que selecionou é apresentado no lado direito.
* Selecione qualquer linha para ver os seus [detalhes à direita](#details-of-the-selected-telemetry). 

> [!NOTE]
> As chamadas para outros componentes têm duas linhas: uma linha representa a chamada de saída (dependência) do componente do chamador, e a outra linha corresponde ao pedido de entrada no componente chamado. O ícone principal e o estilo distinto das barras de duração ajudam a diferenciar entre elas.

## <a name="all-telemetry-with-this-operation-id"></a>Toda a telemetria com esta Operação Id

Esta secção mostra a vista da lista plana numa sequência de tempo de toda a telemetria relacionada com esta transação. Também mostra os eventos personalizados e vestígios que não são exibidos no gráfico de transações. Pode filtrar esta lista para telemetria gerada por um componente/chamada específico. Pode selecionar qualquer item de telemetria desta lista para ver [os detalhes correspondentes à direita](#details-of-the-selected-telemetry).

![Sequência de tempo de toda a telemetria](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Detalhes da telemetria selecionada

Este painel desmontável mostra o detalhe de qualquer item selecionado a partir do gráfico de transações, ou da lista. "Mostrar tudo" lista todos os atributos padrão que são recolhidos. Quaisquer atributos personalizados são listados separadamente abaixo do conjunto padrão. Clique no "..." abaixo da janela de vestígios de pilha para obter uma opção para copiar o traço. "Vestígios de perfis abertos" ou "Imagem de depurado aberto" mostra diagnósticos de nível de código em painéis de detalhe correspondentes.

![Detalhe de exceção](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Resultados da pesquisa

Esta vidraça desmontável mostra os outros resultados que satisfazem os critérios do filtro. Clique em qualquer resultado para atualizar os respetivos detalhes das 3 secções acima listadas. Tentamos encontrar amostras que sejam mais prováveis de ter os detalhes disponíveis de todos os componentes, mesmo que a amostragem esteja em vigor em qualquer um deles. Estas são mostradas como amostras "sugeridas".

![Resultados da pesquisa](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Profiler e snapshot debugger

[O profiler de Insights de Aplicação](./profiler.md) ou [o depurador instantâneo](snapshot-debugger.md) ajudam com diagnósticos de nível de código de problemas de desempenho e falha. Com esta experiência, pode ver vestígios de perfis ou instantâneos de qualquer componente com um único clique.

Se não conseguir pôr o Profiler a funcionar, contacte **o serviceprofilerhelp \@ microsoft.com**

Se não conseguir pôr o Snapshot Debugger a funcionar, contacte **o snapshot microsoft.com \@**

![Integração de Perfis](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>FAQ

*Vejo um único componente na tabela, e os outros só mostram como dependências externas sem qualquer detalhe do que aconteceu dentro desses componentes.*

Razões potenciais:

* Os outros componentes são instrumentados com Insights de Aplicação?
* Estão a usar o mais recente SDK de Insights de Aplicação estável?
* Se estes componentes forem recursos separados do Application Insights, tem necessidade de acesso à sua telemetria?

Se tiver acesso e os componentes forem instrumentados com os mais recentes SDKs application insights, informe-nos através do canal de feedback superior direito.

*Vejo filas duplicadas para as dependências. Isto é esperado?*

Neste momento, estamos a mostrar a chamada de dependência de saída separada do pedido de entrada. Normalmente, as duas chamadas parecem idênticas, sendo apenas o valor de duração diferente devido à viagem de ida e volta da rede. O ícone principal e o estilo distinto das barras de duração ajudam a diferenciar entre elas. Esta apresentação dos dados é confusa? Dê-nos o seu feedback!

*E o relógio distorce diferentes instâncias componentes?*

As linhas de tempo são ajustadas para distorções de relógio na tabela de transações. Pode ver os tempos exatos no painel de detalhes ou utilizando o Analytics.

*Porque é que a nova experiência está a perder a maioria das consultas relacionadas com itens?*

Esta ação é propositada. Todos os itens relacionados, em todos os componentes, já estão disponíveis no lado esquerdo (secções superiores e inferiores). A nova experiência tem dois itens relacionados que o lado esquerdo não cobre: toda a telemetria de cinco minutos antes e depois deste evento e a linha temporal do utilizador.

