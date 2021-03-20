---
title: Como escalar o seu ambiente - Azure Time Series Insights| Microsoft Docs
description: Aprenda a escalar o seu ambiente Azure Time Series Insights utilizando o portal Azure.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 0dadf523c5d17ffb91f4fefa71b52d1d1855c978
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91570215"
---
# <a name="how-to-scale-your-azure-time-series-insights-gen1-environment"></a>Como escalar o seu ambiente Azure Time Series Insights Gen1

> [!CAUTION]
> Este é um artigo da Gen1.

Este artigo descreve como alterar a capacidade do seu ambiente Azure Time Series Insights utilizando o [portal Azure](https://portal.azure.com). A capacidade é o multiplicador aplicado à taxa de entrada, capacidade de armazenamento e custo associado ao seu SKU selecionado.

Pode utilizar o portal Azure para aumentar ou diminuir a capacidade dentro de um determinado preço SKU.

No entanto, não é permitida a alteração do nível de preços SKU. Por exemplo, um ambiente com um SKU de preços S1 não pode ser convertido num S2, ou vice-versa.

## <a name="ga-limits"></a>Limites de GA

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>Alterar a capacidade do seu ambiente

1. No portal Azure, localize e selecione o ambiente Azure Time Series Insights.

1. No menu para o ambiente Azure Time Series Insights, selecione **a Configuração de Armazenamento**.

   [![Configure a sua capacidade de Insights da Série de Tempo Azure](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. Ajuste o slider **de capacidade** para selecionar a capacidade que satisfaz os requisitos para as suas taxas de entrada e capacidade de armazenamento. Note a **taxa de Entrada,** capacidade de **armazenamento** e atualização de **custos estimadas** dinamicamente para mostrar o impacto da mudança.

   [![Configure o seu ambiente usando o slider de capacidade](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   Em alternativa, pode digitar o número do multiplicador de capacidade na caixa de texto à direita do deslizador.

1. **Selecione Guardar** para escalar o ambiente. O indicador de progresso é apresentado até que a alteração seja comprometida, momentaneamente.

1. Verifique se a nova capacidade é [suficiente para evitar estrangulamentos](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações, [reveja a retenção de compreensão em Azure Time Series Insights](time-series-insights-concepts-retention.md).

- Saiba como [configurar a retenção de dados em Azure Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Saiba como [planear o seu ambiente.](time-series-insights-environment-planning.md)
