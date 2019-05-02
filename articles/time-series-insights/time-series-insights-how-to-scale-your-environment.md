---
title: Como dimensionar o seu ambiente do Azure Time Series Insights | Documentos da Microsoft
description: Este artigo descreve como dimensionar o seu ambiente do Azure Time Series Insights. Utilize o portal do Azure para adicionar ou subtrair capacidade dentro de um SKU de preços.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.custom: seodec18
ms.openlocfilehash: fcdfc78a62d901bdaf50c18e9286e6f2d30c5b13
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64714125"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Como dimensionar o seu ambiente do Time Series Insights

Este artigo descreve como alterar a capacidade do seu ambiente de seu ambiente do Time Series Insights com o portal do Azure. A capacidade é o multiplicador aplicado a taxa de entrada, a capacidade de armazenamento e custos associados com o SKU selecionado. 

Pode utilizar o portal do Azure para aumentar ou diminuir a capacidade dentro de um determinado SKU de preços. 

No entanto, o escalão de preço SKU não é permitido alterar. Por exemplo, um ambiente com um S1 SKU de preços não pode ser convertido em S2, ou vice versa. 


## <a name="s1-sku-ingress-rates-and-capacities"></a>As taxas de entrada de S1 SKU e as capacidades

| S1 Capacidade SKU | Taxa de entrada | Capacidade máxima de armazenamento
| --- | --- | --- |
| 1 | 1 GB (1 milhão de eventos) | 30 GB (30 milhões de eventos) por mês |
| 10 | 10 GB (10 milhões de eventos) | 300 GB (300 milhões de eventos) por mês |

## <a name="s2-sku-ingress-rates-and-capacities"></a>As taxas de entrada de S2 SKU e as capacidades

| S2 Capacidade SKU | Taxa de entrada | Capacidade máxima de armazenamento
| --- | --- | --- |
| 1 | 10 GB (10 milhões de eventos) | 300 GB (300 milhões de eventos) por mês |
| 10 | 100 GB (100 milhões de eventos) | 3 TB (3 mil milhões de eventos) por mês |

As capacidades de maneira linear, dimensionar, para que uma SKU S1 com capacidade 2 oferece suporte a eventos de (2 milhões) de 2 GB por taxa de entrada do dia e 60 GB (60 milhões de eventos) por mês.

## <a name="change-the-capacity-of-your-environment"></a>Alterar a capacidade do seu ambiente
1. No portal do Azure, localize e selecione o seu ambiente do Time Series Insights. 

2. No menu do seu ambiente do Time Series Insights, selecione **configurar**.

   ![configure.png](media/scale-your-environment/configure.png)

3. Ajustar a **capacidade** controlo de deslize para selecionar a capacidade de que cumpre os requisitos para as taxas de entrada e a capacidade de armazenamento. Observe que o **taxa de entrada**, **capacidade de armazenamento**, e **custo estimado** atualização dinamicamente para mostrar o impacto da alteração. 

   ![Controlo de deslize](media/scale-your-environment/slider.png)

   Em alternativa, pode digitar o número do multiplicador de capacidade na caixa de texto à direita do controlo de deslize. 

4. Selecione **guardar** para dimensionar o ambiente. O indicador de progresso é apresentado até que a alteração é consolidada, em instantes. 

## <a name="next-steps"></a>Passos Seguintes

- Certifique-se de que é a nova capacidade [suficiente para evitar a limitação](time-series-insights-diagnose-and-solve-problems.md).