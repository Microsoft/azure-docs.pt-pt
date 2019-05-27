---
title: Como ver a utilização da API de mapas do Azure | Documentos da Microsoft
description: Saiba como ver as métricas de suas chamadas de API de mapas do Azure no portal.
author: dsk-2015
ms.author: dkshir
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 31522436de97062432af2afe101f85d376243a38
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957264"
---
# <a name="view-azure-maps-api-usage"></a>Ver a utilização de API do Azure Maps

Este artigo mostra-lhe como ver as métricas de utilização da API para a sua conta do Azure Maps no [portal](https://portal.azure.com). As métricas são exibidas num formato gráfico conveniente ao longo de um período de tempo personalizáveis.

## <a name="view-metric-snapshot"></a>Ver instantâneo de métrica

Pode ver algumas métricas comuns sobre o **descrição geral** página da sua conta de mapas. Mostra atualmente *Total de pedidos*, *Total de erros*, e *disponibilidade* através de uma duração de tempo selecionável.

![Descrição geral das métricas de mapas do Azure](media/how-to-view-api-usage/portal-overview.png)

Continue para a secção seguinte, se precisar de personalizar nestes gráficos para sua análise específico.

## <a name="view-detailed-metrics"></a>Ver métricas detalhadas

1. Inicie sessão na sua subscrição do Azure no [portal](https://portal.azure.com).

2. Clique nas **todos os recursos** menu no lado esquerdo do item e navegue até à sua *conta do Azure Maps*.

3. Assim que a sua conta do Maps estiver aberta, clique nas **métricas** menu à esquerda.

4. Sobre o **métricas** painel, escolher entre um dos seguintes:

   1. **Disponibilidade** -que mostra a *médio* de disponibilidade da API durante um período de tempo.
   2. **Utilização** -que mostra como a utilização *contagem* para a sua conta.

      ![Painel de métricas de mapas do Azure](media/how-to-view-api-usage/portal-metrics.png)

5. Em seguida, pode selecionar o *intervalo de tempo* ao clicar em **últimas 24 horas (automático)**. Por predefinição, o intervalo de tempo é definido como 24 horas. Depois de clicar, verá todos os intervalos de tempo selecionável. Pode selecionar o *granularidade de tempo* e optar por mostrar o tempo como *local* ou *GMT* no mesmo menu pendente. Clique em **Aplicar**.

    ![Intervalo de tempo de métricas de mapas do Azure](media/how-to-view-api-usage/time-range.png)

6. Depois de adicionar sua métrica, pode então **Adicionar filtro** de entre as propriedades relevantes para que métrica e, em seguida, selecione o valor da propriedade que pretende ver o gráfico para.

    ![Filtro de métricas do Azure Maps](media/how-to-view-api-usage/filter.png)

7. Também poderá **aplicam-se de que a divisão** para sua métrica com base na sua propriedade métrica selecionada. Isso permite que o gráfico ser dividido em vários gráficos, um para cada valor dessa propriedade. Na imagem seguinte, a cor de cada gráfico corresponde ao valor de propriedade mostrado na parte inferior do gráfico.

    ![Métricas de divisão de mapas do Azure](media/how-to-view-api-usage/splitting.png)

8. Também pode ver várias métricas no mesmo gráfico, simplesmente clicando no **adicionar métrica** botão na parte superior.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as APIs de mapas do Azure que pretende controlar a utilização de:
> [!div class="nextstepaction"] 
> [Procedimentos SDK de Web do Azure Maps](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Procedimentos Android SDK do Azure Maps](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Documentação da API de REST de mapas do Azure](https://docs.microsoft.com/rest/api/maps)
