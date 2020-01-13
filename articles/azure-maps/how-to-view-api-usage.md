---
title: Exibir métricas de uso da API do Azure Maps | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a exibir as métricas para suas chamadas de API do Microsoft Azure Maps no portal do Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 7ba50f63fb015a8696904df3decd13c811625459
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911325"
---
# <a name="view-azure-maps-api-usage-metrics"></a>Ver as métricas de utilização da API do Azure Maps

Este artigo mostra como exibir as métricas de uso da API para sua conta do Azure Maps no [portal](https://portal.azure.com). As métricas são mostradas em um formato de gráfico conveniente ao longo de uma duração de tempo personalizável.

## <a name="view-metric-snapshot"></a>Exibir instantâneo de métrica

Você pode ver algumas métricas comuns na página **visão geral** da sua conta do Maps. Atualmente, ele mostra *solicitações totais*, *total de erros*e *disponibilidade* em uma duração de tempo selecionável.

![Visão geral das métricas de uso do Azure Maps](media/how-to-view-api-usage/portal-overview.png)

Continue na próxima seção se precisar personalizar esses grafos para sua análise específica.

## <a name="view-detailed-metrics"></a>Exibir métricas detalhadas

1. Entre em sua assinatura do Azure no [portal](https://portal.azure.com).

2. Clique no item de menu **todos os recursos** no lado esquerdo e navegue até sua *conta do Azure Maps*.

3. Quando sua conta do Maps estiver aberta, clique no menu **métrica** à esquerda.

4. No painel **métricas** , escolha entre um dos seguintes:

   1. **Disponibilidade** -que mostra a *média* da disponibilidade da API em um período de tempo.
   2. **Uso** -que mostra como a *contagem* de uso para sua conta.

      ![Painel de métricas de uso do Azure Maps](media/how-to-view-api-usage/portal-metrics.png)

5. Em seguida, você pode selecionar o *intervalo de tempo* clicando em **últimas 24 horas (automático)** . Por padrão, o intervalo de tempo é definido como 24 horas. Depois de clicar, você verá todos os intervalos de tempo selecionáveis. Você pode selecionar a *granularidade de tempo* e optar por mostrar a hora como *local* ou *GMT* na mesma lista suspensa. Clique em **Aplicar**.

    ![Intervalo de tempo das métricas do Azure Maps](media/how-to-view-api-usage/time-range.png)

6. Depois de adicionar sua métrica, você pode **Adicionar o filtro** entre as propriedades relevantes para essa métrica e, em seguida, selecionar o valor da propriedade para a qual você deseja ver o grafo.

    ![Filtro de métricas de uso do Azure Maps](media/how-to-view-api-usage/filter.png)

7. Você também pode **aplicar a divisão** para sua métrica com base na propriedade de métrica selecionada. Isso permite que o grafo seja dividido em vários grafos, um para cada valor dessa propriedade. Na imagem a seguir, a cor de cada grafo corresponde ao valor da propriedade mostrado na parte inferior do grafo.

    ![Divisão de métricas de uso do Azure Maps](media/how-to-view-api-usage/splitting.png)

8. Você também pode observar várias métricas no mesmo grafo, simplesmente clicando no botão **Adicionar métrica** na parte superior.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as APIs do Azure Maps para as quais você deseja acompanhar o uso:
> [!div class="nextstepaction"] 
> [Instruções do SDK para Web do Azure Maps](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Instruções do Azure Maps SDK do Android](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Documentação da API REST do Azure Maps](https://docs.microsoft.com/rest/api/maps)
