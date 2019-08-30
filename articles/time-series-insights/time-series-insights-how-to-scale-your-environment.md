---
title: Como dimensionar seu ambiente de Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como dimensionar seu ambiente de Azure Time Series Insights. Use o portal do Azure para adicionar ou subtrair capacidade em um SKU de preços.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/27/2019
ms.custom: seodec18
ms.openlocfilehash: 13fa2b892013cf4a3fb96220c901030c6b0aee0b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129091"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Como dimensionar seu ambiente de Time Series Insights

Este artigo descreve como alterar a capacidade de seu ambiente Time Series Insights ambiente usando o portal do Azure. A capacidade é o multiplicador aplicado à taxa de entrada, à capacidade de armazenamento e ao custo associado à SKU selecionada.

Você pode usar a portal do Azure para aumentar ou diminuir a capacidade em um determinado SKU de preços.

No entanto, a alteração da SKU do tipo de preço não é permitida. Por exemplo, um ambiente com um SKU de preço S1 não pode ser convertido em um S2, ou vice-versa.

## <a name="s1-sku-ingress-rates-and-capacities"></a>Taxas e capacidades de entrada de SKU S1

| Capacidade de SKU S1 | Taxa de entrada | Capacidade máxima de armazenamento
| --- | --- | --- |
| 1 | 1 GB (1 milhão eventos) | 30 GB (30 milhões eventos) por mês |
| 10 | 10 GB (10 milhões eventos) | 300 GB (300 milhões eventos) por mês |

## <a name="s2-sku-ingress-rates-and-capacities"></a>Taxas e capacidades de entrada de SKU S2

| Capacidade de SKU S2 | Taxa de entrada | Capacidade máxima de armazenamento
| --- | --- | --- |
| 1 | 10 GB (10 milhões eventos) | 300 GB (300 milhões eventos) por mês |
| 10 | 100 GB (100 milhões eventos) | 3 TB (3.000.000.000 eventos) por mês |

As capacidades são dimensionadas linearmente, de modo que uma SKU S1 com capacidade 2 dá suporte a 2 GB (2 milhões) de eventos por dia e 60 GB (eventos 60 milhões) por mês.

## <a name="change-the-capacity-of-your-environment"></a>Alterar a capacidade de seu ambiente

1. Na portal do Azure, localize e selecione seu ambiente de Time Series Insights.

1. No menu de seu ambiente de Time Series Insights, selecione **Configurar**.

   [![configure.png](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. Ajuste o controle deslizante **capacidade** para selecionar a capacidade que atende aos requisitos para suas taxas de entrada e capacidade de armazenamento. Observe a **taxa de entrada**, a **capacidade de armazenamento**e a atualização de **custo estimado** dinamicamente para mostrar o impacto da alteração.

   [![Classificação](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   Como alternativa, você pode digitar o número do multiplicador de capacidade na caixa de texto à direita do controle deslizante.

1. Selecione **salvar** para dimensionar o ambiente. O indicador de progresso é exibido até que a alteração seja confirmada, momentaneamente.

## <a name="next-steps"></a>Passos seguintes

- Verifique se a nova capacidade é [suficiente para evitar a limitação](time-series-insights-diagnose-and-solve-problems.md).