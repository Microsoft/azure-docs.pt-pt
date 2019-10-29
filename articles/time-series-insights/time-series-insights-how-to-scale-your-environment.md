---
title: Como dimensionar seu ambiente de Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como dimensionar seu ambiente de Azure Time Series Insights. Use o portal do Azure para adicionar ou subtrair capacidade em um SKU de preços.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 3f03f5ed75c720c9b0daf30d721ef4d2aee9749c
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991165"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Como dimensionar seu ambiente de Time Series Insights

Este artigo descreve como alterar a capacidade de seu ambiente de Time Series Insights usando o [portal do Azure](https://portal.azure.com). A capacidade é o multiplicador aplicado à taxa de entrada, à capacidade de armazenamento e ao custo associado à SKU selecionada.

Você pode usar a portal do Azure para aumentar ou diminuir a capacidade em um determinado SKU de preços.

No entanto, a alteração da SKU do tipo de preço não é permitida. Por exemplo, um ambiente com um SKU de preço S1 não pode ser convertido em um S2, ou vice-versa.

## <a name="ga-limits"></a>Limites de GA

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>Alterar a capacidade de seu ambiente

1. Na portal do Azure, localize e selecione seu ambiente de Time Series Insights.

1. No menu de seu ambiente de Time Series Insights, selecione **Configurar**.

   [![configurar. png](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. Ajuste o controle deslizante **capacidade** para selecionar a capacidade que atende aos requisitos para suas taxas de entrada e capacidade de armazenamento. Observe a **taxa de entrada**, a **capacidade de armazenamento**e a atualização de **custo estimado** dinamicamente para mostrar o impacto da alteração.

   [Controle deslizante de![](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   Como alternativa, você pode digitar o número do multiplicador de capacidade na caixa de texto à direita do controle deslizante.

1. Selecione **salvar** para dimensionar o ambiente. O indicador de progresso é exibido até que a alteração seja confirmada, momentaneamente.

1. Verifique se a nova capacidade é [suficiente para evitar a limitação](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações, consulte [noções básicas sobre retenção em time Series insights](time-series-insights-concepts-retention.md).

- Saiba mais sobre como [Configurar a retenção de dados no Azure Time Series insights](time-series-insights-how-to-configure-retention.md).

- Saiba mais sobre como [planejar o seu ambiente](time-series-insights-environment-planning.md).