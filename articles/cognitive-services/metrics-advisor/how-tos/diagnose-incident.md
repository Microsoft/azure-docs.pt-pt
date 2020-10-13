---
title: Diagnosticar incidentes usando o Advisor Métrica
titleSuffix: Azure Cognitive Services
description: Saiba como diagnosticar um incidente usando o Metrics Advisor e obtenha uma visão detalhada das anomalias nos seus dados.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: aahi
ms.openlocfilehash: fb70745cf7773e8caa91b31048af97e8e626bb91
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91597950"
---
# <a name="how-to-diagnose-an-incident-using-metrics-advisor"></a>Como: Diagnosticar um incidente usando o Advisor Métrico

O Metrics Advisor fornece várias funcionalidades para diagnósticos, e dá uma visão aprofundada dos incidentes detetados e fornece uma análise de causa-raiz. Quando um grupo de anomalias detetadas numa métrica, o Metrics Advisor agrupa anomalias numa hierarquia e analisa-a em cima.

> [!NOTE]
> Atualmente, o Metrics Advisor suporta diagnósticos de incidentes para métricas com pelo menos uma dimensão, e medir com o tipo *numérico.* A sua métrica precisa de ter um valor de dimensão agregado como a SUM para cada dimensão, que é usada para construir a hierarquia de diagnóstico. O Metrics Advisor oferece [**configurações de roll-up automáticas**](onboard-your-data.md#automatic-roll-up-settings) para ajudar a gerar valores agregados. 

Clique no **centro de incidentes** na janela de navegação esquerda para ver todos os incidentes sob uma determinada métrica. No topo da página, pode selecionar diferentes métricas para ver as suas configurações de deteção e resultados de deteção e alterar o intervalo de tempo.

> [!TIP]
> Você também pode chegar ao **centro de incidentes** por:
> * Clicar num ponto de dados na visualização da sua métrica e utilizar os links na parte inferior da janela de **feedback Add** que aparece.
> * Clicando numa das anomalias no separador **incidentes** para a sua métrica. 

A secção **geral** contém resultados de deteção, incluindo as contagens das anomalias e alertas dentro do intervalo de tempo selecionado.

:::image type="content" source="../media/diagnostics/incident-hub-overview.png" alt-text="Centro de incidentes" lightbox="../media/diagnostics/incident-hub-overview.png":::

Incidentes detetados dentro da métrica selecionada e intervalo de tempo estão listados na **lista de Incidentes.** Existem opções para filtrar e encomendar os incidentes. Por exemplo, por gravidade. Clique num dos incidentes para ir à página **'Incidente'** para mais diagnósticos.

:::image type="content" source="../media/diagnostics/incident-list.png" alt-text="Centro de incidentes" lightbox="../media/diagnostics/incident-list.png":::

A secção **de Diagnóstico** permite-lhe realizar análises aprofundadas sobre um incidente e ferramentas para identificar as causas de raiz.

:::image type="content" source="../media/diagnostics/diagnose-incident.png" alt-text="Centro de incidentes" lightbox="../media/diagnostics/diagnose-incident.png" :::

## <a name="root-cause-advice"></a>Conselhos de causa de raiz

Quando um grupo de anomalias é detetado numa métrica e causa um incidente, o Metrics Advisor tentará analisar a causa principal do incidente. **Os conselhos de causa** raiz fornecem sugestões automáticas para as causas prováveis de um incidente. Esta funcionalidade só está disponível se houver um valor agregado dentro da dimensão. Se a métrica não tiver dimensão, a causa raiz será a própria. As causas das raízes estão listadas no painel do lado direito e pode haver várias razões listadas. Se não houver dados na tabela, significa que a sua dimensão não satisfaz os requisitos para realizar a análise.

:::image type="content" source="../media/diagnostics/root-cause-advice.png" lightbox="../media/diagnostics/root-cause-advice.png" alt-text="Centro de incidentes":::


Quando a métrica da causa da raiz é fornecida com dimensões específicas, você pode clicar **em ir à métrica** para ver mais detalhes da métrica.

## <a name="incident-tree"></a>Árvore incidente

Juntamente com uma análise automatizada sobre potenciais causas de raiz, o Metrics Advisor suporta a análise manual da causa da raiz, utilizando a **Árvore incidente**. Há dois tipos de árvore incidente na página do incidente: a árvore **de diagnóstico rápido,** e a **árvore interativa.**

A árvore de diagnóstico rápido é para diagnosticar um incidente atual, e o nó raiz está limitado ao nó raiz do incidente atual. Pode expandir e colapsar os nós das árvores clicando nele, e a sua série será mostrada juntamente com a série de incidentes atuais na tabela acima da árvore.

A árvore interativa permite diagnosticar incidentes atuais, bem como incidentes mais antigos, e os que estão relacionados. Ao utilizar a árvore interativa, clique em um nó para abrir um menu de ação, onde pode escolher uma dimensão para perfurar através dos nós de raiz, e uma dimensão para perfurar para cada nó. Ao clicar no botão de cancelamento da lista de dimensões no topo, pode remover a perfuração para cima ou para baixo a partir desta dimensão. clique num nó para selecioná-lo e mostrar a sua série juntamente com as séries de incidentes atuais na tabela.

:::image type="content" source="../media/diagnostics/incident-tree.png" alt-text="Centro de incidentes" lightbox="../media/diagnostics/incident-tree.png" :::

## <a name="anomaly-drill-down"></a>Perfuração de anomalia para baixo

Ao visualizar informações sobre incidentes, poderá precisar de obter informações mais detalhadas, por exemplo, para diferentes dimensões e datas de tempo. Se os seus dados ímis 1 ou mais dimensões, pode utilizar a função de perfuração para obter uma visão mais detalhada. 

Para utilizar a função de perfuração para baixo, clique no **separador de perfuração métrica** no **centro incidente**. 

:::image type="content" source="../media/diagnostics/metric-drilling.png" lightbox="../media/diagnostics/metric-drilling.png" alt-text="Centro de incidentes":::

A definição **de Dimensões** é uma lista de dimensões para um incidente, pode selecionar outros valores de dimensão disponíveis para cada um. Depois de alterados os valores de dimensão. A **definição de Timestamp** permite-lhe visualizar o incidente atual em diferentes momentos do tempo.

### <a name="select-drilling-options-and-choose-a-dimension"></a>Selecione opções de perfuração e escolha uma dimensão

Existem dois tipos de opções de perfuração: **Perfurar para baixo** e **comparação horizontal**.

> [!Note]
> 1. Para aprofundar, pode explorar os dados de diferentes valores de dimensão, exceto as dimensões selecionadas. 
> 2. Para comparação horizontal, pode explorar os dados de diferentes valores de dimensão, exceto as dimensões all-up.

:::image type="content" source="../media/diagnostics/drill-down-dimension.png"  lightbox="../media/diagnostics/drill-down-dimension.png" alt-text="Centro de incidentes":::

### <a name="value-comparison-for-different-dimension-values"></a>Comparação de valor para diferentes valores de dimensão

A segunda secção da aba para baixo da broca é uma tabela com comparações para valores de dimensão diferentes. Inclui o valor, valor base, valor de diferença, valor delta e se é uma anomalia.
 
:::image type="content" source="../media/diagnostics/drill-down-comparison.png" alt-text="Centro de incidentes" lightbox="../media/diagnostics/drill-down-comparison.png":::


### <a name="value-and-expected-value-comparisons-for-different-dimension-value"></a>Valor e comparações de valor esperado para diferentes valor de dimensão

A terceira secção da aba para baixo é uma histograma com os valores e valores esperados, para valores de dimensão diferentes. O histograma é classificado pela diferença entre valor e valor esperado. Você pode encontrar o valor inesperado com o maior impacto facilmente. Por exemplo, na imagem acima, podemos descobrir que, exceto o valor total, **US7** contribui mais para a anomalia.

:::image type="content" source="../media/diagnostics/drill-down-table.png" alt-text="Centro de incidentes" lightbox="../media/diagnostics/drill-down-table.png":::

### <a name="raw-value-visualization"></a>Visualização de valor bruto
A última parte da aba de perfuração é um gráfico de linha dos valores brutos. Com este gráfico fornecido, não precisa navegar para a página métrica para ver detalhes.

:::image type="content" source="../media/diagnostics/drill-down-line-chart.png" alt-text="Centro de incidentes" lightbox="../media/diagnostics/drill-down-line-chart.png":::

## <a name="view-similar-anomalies-using-time-series-clustering"></a>Ver anomalias semelhantes usando clustering de séries de tempo

Ao visualizar um incidente, pode utilizar o **separador de agrupamento de séries de tempo semelhante** para ver as várias séries associadas. As séries de um grupo são resumidas em conjunto. Pela imagem acima, podemos saber que há pelo menos dois grupos de séries. Esta funcionalidade só está disponível se forem cumpridos os seguintes requisitos:

1. As métricas devem ter uma ou mais dimensões ou valores de dimensão.
2. A série dentro de uma métrica deve ter uma tendência semelhante.

As dimensões disponíveis estão listadas no topo do separador, e pode fazer uma seleção para especificar a série.

:::image type="content" source="../media/diagnostics/series-group.png" lightbox="../media/diagnostics/series-group.png"alt-text="Centro de incidentes":::

## <a name="compare-time-series"></a>Comparar séries de tempo

Às vezes, quando uma anomalia é detetada numa série de tempo específica, é útil compará-la com várias outras séries numa única visualização. Clique no **separador 'Comparar ferramentas'** e, em seguida, clique no botão azul **+ Adicionar.** 

:::image type="content" source="../media/diagnostics/add-series.png" alt-text="Centro de incidentes" lightbox="../media/diagnostics/add-series.png":::

Selecione uma série a partir do seu feed de dados. Pode escolher a mesma granularidade ou outra. Selecione as dimensões do alvo e carregue a tendência da série e, em seguida, clique em **Ok** para compará-la com uma série anterior. A série será montada numa única visualização. Pode continuar a adicionar mais séries para comparação e obter mais informações. Clique no menu drop down no topo do separador **ferramentas Compare** para comparar os dados da série de tempo durante um período alterado no tempo.  

> [!Warning]
> Para fazer uma comparação, a análise de dados de séries de tempo pode exigir mudanças em pontos de dados, pelo que a granularidade dos seus dados deve apoiá-lo. Por exemplo, se os seus dados forem semanais e utilizar o **Dia ao longo do dia,** não obterá resultados. Neste exemplo, em vez disso, utilizaria o **Mês ao longo do mês.**

Depois de selecionar uma comparação de mudança de tempo, pode selecionar se deseja comparar os valores de dados, os valores delta ou a percentagem delta.

> [!Note]
> * **O valor dos dados** é o valor bruto dos dados.
> * **O valor delta** é a diferença entre o valor bruto e o valor comparado.
> * **O valor delta percentual** é a diferença entre o valor bruto e o valor comparado dividido pelo valor comparado.

## <a name="related-incidents-across-metrics"></a>Incidentes relacionados através de métricas

Por vezes, pode ser necessário verificar os incidentes de diferentes métricas ao mesmo tempo, ou incidentes relacionados noutras métricas. Pode encontrar uma lista de incidentes relacionados na secção **de Análise de Métricas Cruzadas.** 

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="Centro de incidentes":::

Antes de ver incidentes relacionados para a métrica atual, precisa adicionar uma relação entre métricas. Clique em **Definições de Gráficos métricas** para adicionar uma relação. Só podem estar relacionadas métricas com nomes de dimensão igual. Utilize os parâmetros seguintes.

- Feed de dados atuais & Métrica: o feed de dados e a métrica do incidente atual
- Direção: a direção da relação entre duas métricas. (não afetar agora a lista de incidentes relacionados)
- Outro feed de dados & Métrica: o feed de dados e a métrica para se conectar com a métrica atual


## <a name="next-steps"></a>Passos seguintes 

- [Ajustar a deteção de anomalias através de comentários](anomaly-feedback.md)
- [Configurar métricas e otimizar a configuração da deteção](configure-metrics.md)
