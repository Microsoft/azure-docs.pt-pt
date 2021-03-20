---
title: Gráfico de métricas do consultor de métricas
titleSuffix: Azure Cognitive Services
description: Como configurar o gráfico métrica e visualizar anomalias relacionadas nos seus dados.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbullwin
ms.openlocfilehash: bcedef4a1339dacaff3fe841e97f985c42320819
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92043158"
---
# <a name="how-to-build-a-metrics-graph-to-analyze-related-metrics"></a>Como fazer: Construir um gráfico de métricas para analisar métricas relacionadas

Cada métrica em Metrics Advisor é monitorizada separadamente por um modelo que aprende com dados históricos para prever tendências futuras. Cada métrica tem um modelo separado que lhe é aplicado. Em alguns casos, no entanto, várias métricas podem relacionar-se entre si, e as anomalias precisam de ser analisadas em várias métricas. O **Gráfico Métrico** ajuda com isto. 

Como exemplo, se tiver diferentes fluxos de telemetria em métricas separadas, o Metrics Advisor irá monitorá-los separadamente. Se as anomalias numa métrica causarem anomalias noutras, encontrar essas relações e a causa principal nos seus dados pode ser útil ao abordar incidentes. O gráfico de métricas permite-lhe criar um gráfico de topologia visual de anomalias encontradas. 

## <a name="select-a-metric-to-put-the-first-node-to-the-graph"></a>Selecione uma métrica para colocar o primeiro nó no gráfico

Clique no **separador Gráfico Métricas** na barra de navegação. O primeiro passo na construção de um gráfico de métricas é colocar um nó no gráfico. Selecione um feed de dados e métrica no topo da página. Aparecerá um nó no painel inferior. 

:::image type="content" source="../media/graph/metrics-graph-select.png" alt-text="Selecionar métrica":::

## <a name="add-a-noderelation-on-existing-node"></a>Adicione um nó/relação no nó existente

Em seguida, precisa adicionar outro nó e especificar uma relação com um nó(s) existente. Selecione um nó existente e clique no botão direito nele. Um menu de contexto será apresentado com várias opções. 

Clique **em Adicionar a relação**, e poderá escolher outra métrica e especificar o tipo de relação entre os dois nós. Também pode aplicar filtros de dimensão específica. 

:::image type="content" source="../media/graph/metrics-graph-node-action.png" alt-text="Adicione um nó e uma relação":::

Depois de repetir os passos acima, terá um gráfico de métricas descrevendo as relações entre todas as métricas relacionadas.
**Dica sobre as cores do nó**
> [!TIP]
> - Ao selecionar um filtro métrico e de dimensão, todos os nós com o mesmo filtro métrico e de dimensão no gráfico serão coloridos como **<font color=blue>azuis</font>**.
> - Os nós não selecionados que representam uma métrica no gráfico serão **<font color=green>coloridos verdes.</font>**
> - Se houver uma anomalia observada na métrica atual, o nó será colorido **<font color=red>vermelho.</font>**

## <a name="view-related-metrics-anomaly-status-in-incident-hub"></a>Ver estado de anomalia de métricas relacionadas no centro de incidentes

Quando o gráfico de métricas é construído, sempre que uma anomalia é detetada em métricas dentro do gráfico, você será capaz de ver os estados de anomalia relacionados, e obter uma visão de alto nível do incidente. 

Clique num incidente dentro do gráfico e desloque-se para baixo para cruzar a **análise de métricas,** abaixo da informação de diagnóstico.

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="Ver métricas e anomalias relacionadas":::

## <a name="next-steps"></a>Passos seguintes

- [Ajustar a deteção de anomalias através de comentários](anomaly-feedback.md)
- [Diagnosticar um incidente.](diagnose-incident.md)
- [Configurar métricas e otimizar a configuração da deteção](configure-metrics.md)
