---
title: Diagnósticos de transação do Aplicativo Azure insights | Microsoft Docs
description: Application Insights de diagnóstico de transação de ponta a ponta
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/19/2018
ms.reviewer: sdash
ms.openlocfilehash: 1ed3713fe4a6c9403be13f444d0409af459a1e70
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899577"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Diagnóstico de transação entre componentes unificados

A experiência de diagnóstico unificada correlaciona automaticamente a telemetria do lado do servidor em todos os seus Application Insights componentes monitorados em uma única exibição. Não importa se você tem vários recursos com chaves de instrumentação separadas. Application Insights detecta a relação subjacente e permite que você diagnostique facilmente o componente do aplicativo, a dependência ou a exceção que causou lentidão na transação ou falha.

## <a name="what-is-a-component"></a>O que é um componente?

Os componentes são partes implantáveis independentemente do seu aplicativo distribuído/de microserviços. Os desenvolvedores e as equipes de operações têm visibilidade de nível de código ou acesso à telemetria gerada por esses componentes de aplicativos.

* Os componentes são diferentes das dependências externas "observadas", como SQL, EventHub, etc. às quais sua equipe/organização pode não ter acesso (código ou telemetria).
* Os componentes são executados em qualquer número de instâncias de servidor/função/contêiner.
* Os componentes podem ser separados Application Insights chaves de instrumentação (mesmo se as assinaturas forem diferentes) ou diferentes funções subordinadas a uma única chave de instrumentação de Application Insights. A nova experiência mostra detalhes em todos os componentes, independentemente de como eles foram configurados.

> [!NOTE]
> * **Os links de itens relacionados estão ausentes?** Todas as telemetrias relacionadas estão nas seções [superior](#cross-component-transaction-chart) e [inferior](#all-telemetry-with-this-operation-id) do lado esquerdo. 

## <a name="transaction-diagnostics-experience"></a>Experiência de diagnóstico de transação
Essa exibição tem quatro partes-chave: lista de resultados, um gráfico de transações entre componentes, uma lista de sequências de tempo de toda a telemetria relacionada a essa operação e o painel de detalhes para qualquer item de telemetria selecionado à esquerda.

![Principais partes](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Gráfico de transações entre componentes

Este gráfico fornece uma linha do tempo com barras horizontais para a duração de solicitações e dependências entre os componentes. Todas as exceções coletadas também são marcadas na linha do tempo.

* A linha superior neste gráfico representa o ponto de entrada, a solicitação de entrada para o primeiro componente chamado nesta transação. A duração é o tempo total necessário para a transação ser concluída.
* Todas as chamadas para dependências externas são linhas simples não recolhíveis, com ícones que representam o tipo de dependência.
* As chamadas para outros componentes são linhas recolhíveis. Cada linha corresponde a uma operação específica invocada no componente.
* Por padrão, a solicitação, a dependência ou a exceção que você selecionou são exibidas no lado direito.
* Selecione qualquer linha para ver seus [detalhes à direita](#details-of-the-selected-telemetry). 

> [!NOTE]
> As chamadas para outros componentes têm duas linhas: uma linha representa a chamada de saída (dependência) do componente chamador e a outra linha corresponde à solicitação de entrada no componente chamado. O ícone à esquerda e o estilo distinto das barras de duração ajudam a diferenciá-las.

## <a name="all-telemetry-with-this-operation-id"></a>Toda a telemetria com esta ID de operação

Esta seção mostra a exibição de lista simples em uma sequência de tempo de toda a telemetria relacionada a essa transação. Ele também mostra os eventos personalizados e os rastreamentos que não são exibidos no gráfico de transações. Você pode filtrar essa lista para a telemetria gerada por um componente/chamada específica. Você pode selecionar qualquer item de telemetria nessa lista para ver [os detalhes correspondentes à direita](#details-of-the-selected-telemetry).

![Sequência de tempo de toda a telemetria](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Detalhes da telemetria selecionada

Este painel recolhível mostra os detalhes de qualquer item selecionado do gráfico de transações ou da lista. "Mostrar tudo" lista todos os atributos padrão que são coletados. Todos os atributos personalizados são listados separadamente abaixo do conjunto padrão. Clique em "..." abaixo da janela de rastreamento de pilha para obter uma opção para copiar o rastreamento. "Abrir rastreamentos do criador de perfil" ou "abrir instantâneo de depuração" mostra o diagnóstico de nível de código nos painéis de detalhes correspondentes.

![Detalhe da exceção](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Resultados da pesquisa

Este painel recolhível mostra os outros resultados que atendem aos critérios de filtro. Clique em qualquer resultado para atualizar os respectivos detalhes das três seções listadas acima. Tentamos encontrar exemplos que têm mais probabilidade de ter os detalhes disponíveis de todos os componentes, mesmo se a amostragem estiver em vigor em qualquer um deles. Eles são mostrados como exemplos "sugeridos".

![Resultados da pesquisa](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Criador de perfil e depurador de instantâneos

[Application insights Profiler](../../azure-monitor/app/profiler.md) ou o [depurador de instantâneos](snapshot-debugger.md) com diagnóstico de nível de código de problemas de desempenho e falha. Com essa experiência, você pode ver os rastreamentos ou instantâneos do profiler de qualquer componente com um único clique.

Se você não conseguiu obter o criador de perfil, entre em contato com **serviceprofilerhelp\@Microsoft.com**

Se você não pôde obter Depurador de Instantâneos funcionando, entre em contato com **snapshothelp\@Microsoft.com**

![Integração do criador de perfil](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>FAQ

*Vejo um único componente no gráfico e os outros são exibidos apenas como dependências externas, sem nenhum detalhe do que aconteceu nesses componentes.*

Possíveis motivos:

* Os outros componentes são instrumentados com Application Insights?
* Eles estão usando o SDK de Application Insights estável mais recente?
* Se esses componentes forem recursos Application Insights separados, você terá acesso necessário à sua telemetria?

Se você tiver acesso e os componentes forem instrumentados com os SDKs de Application Insights mais recentes, informe-nos por meio do canal de comentários da parte superior direita.

*Vejo linhas duplicadas para as dependências. Isso é esperado?*

Neste momento, estamos mostrando a chamada de dependência de saída separada da solicitação de entrada. Normalmente, as duas chamadas parecem idênticas apenas com o valor de duração sendo diferente devido à viagem de ida e volta da rede. O ícone à esquerda e o estilo distinto das barras de duração ajudam a diferenciá-las. Esta apresentação dos dados é confusa? Envie-nos seus comentários!

*E quanto às distorções de relógio em diferentes instâncias de componente?*

As linhas do tempo são ajustadas para distorções de relógio no gráfico de transações. Você pode ver os carimbos de data/hora exatos no painel de detalhes ou usando a análise.

*Por que a nova experiência está faltando na maioria das consultas de itens relacionados?*

Esta ação é propositada. Todos os itens relacionados, em todos os componentes, já estão disponíveis no lado esquerdo (seções superior e inferior). A nova experiência tem dois itens relacionados que o lado esquerdo não cobre: toda a telemetria de cinco minutos antes e depois desse evento e da linha do tempo do usuário.
