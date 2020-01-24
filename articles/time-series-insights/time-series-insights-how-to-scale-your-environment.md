---
title: Como escalar o seu ambiente - Azure Time Series Insights. Microsoft Docs
description: Aprenda a escalar o seu ambiente Azure Time Series Insights utilizando o portal Azure.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 23efda2793ef5d323089ee5b72fb1ea873de6b20
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310990"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Como escalar o seu ambiente Time Series Insights

Este artigo descreve como alterar a capacidade do seu ambiente Time Series Insights usando o [portal Azure](https://portal.azure.com). A capacidade é o multiplicador aplicado à taxa de entrada, capacidade de armazenamento e custo associado ao seu SKU selecionado.

Pode utilizar o portal Azure para aumentar ou diminuir a capacidade dentro de um determinado SKU de preços.

No entanto, não é permitida a alteração do nível de preços SKU. Por exemplo, um ambiente com um SKU de preços S1 não pode ser convertido num S2, ou vice-versa.

## <a name="ga-limits"></a>Limites de GA

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>Mude a capacidade do seu ambiente

1. No portal Azure, localize e selecione o seu ambiente Time Series Insights.

1. No menu para o ambiente Time Series Insights, selecione **Configuração de Armazenamento**.

   [![Configure a sua capacidade de Insights da Série De Tempo](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. Ajuste o slider **de capacidade** para selecionar a capacidade que satisfaz os requisitos para as suas taxas de ingresso e capacidade de armazenamento. Note a **taxa Ingress,** capacidade de **armazenamento**e atualização **de custos estimada** dinamicamente para mostrar o impacto da mudança.

   [![Configure o seu ambiente utilizando o slider de capacidade](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   Em alternativa, pode digitar o número do multiplicador de capacidade na caixa de texto à direita do slider.

1. Selecione **Guardar** para escalar o ambiente. O indicador de progresso é apresentado até que a mudança seja comprometida, momentaneamente.

1. Verifique se a nova capacidade é [suficiente para evitar o estrangulamento](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações, consulte [noções básicas sobre retenção em time Series insights](time-series-insights-concepts-retention.md).

- Saiba sobre a configuração da retenção de dados em Insights da [Série De Tempo Azure](time-series-insights-how-to-configure-retention.md).

- Saiba mais sobre como [planejar o seu ambiente](time-series-insights-environment-planning.md).