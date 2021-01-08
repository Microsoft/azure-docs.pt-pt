---
title: Redimensione um cluster Azure Stream Analytics
description: Aprenda a escalar para cima e para baixo do tamanho de um cluster Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: e469e9bfd13bf8d89084b66e954cb51b27c2ebc2
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020031"
---
# <a name="resize-an-azure-stream-analytics-cluster"></a>Redimensione um cluster Azure Stream Analytics

A capacidade de um cluster Stream Analytics é medida em Unidades de Streaming (SU). Vários postos de trabalho podem funcionar em paralelo no mesmo agrupamento, desde que a soma das SUs atribuídas a todos os postos de trabalho em funcionamento não exceda a capacidade do cluster.

A capacidade do cluster pode ser dimensionada para cima e para baixo para corresponder ao tamanho das suas cargas de trabalho de streaming. Escalar um cluster leva tempo e a escala frequente não é pretendida. Recomendamos que planeie e forisque um cluster com um número preciso de SUs que pretende consumir.

## <a name="change-the-scale-of-your-cluster"></a>Altere a escala do seu cluster

1. No portal Azure, localize e selecione o seu cluster Stream Analytics.

1. Na secção **'Visão Geral',** selecione **Scale**. Pode ver quantas SUs estão atribuídas ao seu aglomerado. Utilize o seletor para aumentar ou diminuir as SUs conforme necessário.

   ![aglomerado de escala](./media/scale-cluster/scale-cluster.png)

A operação de escalagem não afeta quaisquer postos de trabalho atualmente em curso.

## <a name="next-steps"></a>Próximos passos

Agora sabe escalar para cima e para baixo os seus clusters Stream Analytics. Em seguida, pode aprender sobre a gestão de pontos finais privados e autoscalar os seus empregos:

* [Gerir pontos finais privados num cluster Azure Stream Analytics](private-endpoints.md)
* [Gerir empregos num cluster Azure Stream Analytics](manage-jobs-cluster.md)
