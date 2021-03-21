---
title: Ver métricas de utilização da API Azure Maps | Microsoft Azure Maps
description: Saiba como ver as métricas de utilização da API do Azure Maps, tais como pedidos totais, erros totais e disponibilidade. Veja como filtrar dados e dividir resultados.
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/06/2018
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c9b732bd25e7ef8aa084c98d5b059d422f86a4b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96003519"
---
# <a name="view-azure-maps-api-usage-metrics"></a>Ver as métricas de utilização da API do Azure Maps

Este artigo mostra-lhe como ver as métricas de utilização da API, para a sua conta Azure Maps, no [portal Azure](https://portal.azure.com). As métricas são mostradas num formato gráfico conveniente ao longo de uma duração de tempo personalizável.

## <a name="view-metric-snapshot"></a>Ver instantâneo métrico

Pode ver algumas métricas comuns na página **geral** da sua conta Maps. Atualmente mostra *Total Requests*, *Total Errors* e *Disponibilidade* durante uma duração de tempo selecionável.

![Métricas de utilização do Azure Maps](media/how-to-view-api-usage/portal-overview.png)

Continue para a secção seguinte se precisar de personalizar estes gráficos para a sua análise particular.

## <a name="view-detailed-metrics"></a>Ver métricas detalhadas

1. Inscreva-se na sua subscrição Azure no [portal](https://portal.azure.com).

2. Clique no item do menu **de todos os recursos** no lado esquerdo e navegue na sua Conta *Azure Maps*.

3. Assim que a sua conta Maps estiver aberta, clique no menu **Métricas** à esquerda.

4. No painel **Métricas,** escolha uma das seguintes opções:

   1. **Disponibilidade** - que mostra a *disponibilidade média* de API durante um período de tempo.
   2. **Utilização** - que mostra como o uso *conta* para a sua conta.

      ![Painel de métricas de utilização Azure Maps](media/how-to-view-api-usage/portal-metrics.png)

5. Em seguida, pode selecionar o *intervalo de tempo* clicando nas **últimas 24 horas (Automática)**. Por predefinição, o intervalo de tempo é definido para 24 horas. Depois de clicar, verá todos os intervalos de tempo selecionáveis. Pode selecionar a *granularidade tempo* e optar por mostrar a hora como *local* ou *GMT* na mesma entrega. Clique em **Aplicar**.

    ![Intervalo de tempo de métricas Azure Maps](media/how-to-view-api-usage/time-range.png)

6. Uma vez que adicione a sua métrica, pode **adicionar filtro** das propriedades relevantes a essa métrica. Em seguida, selecione o valor do imóvel que pretende ver refletido no gráfico.

    ![Filtro de métricas de utilização Azure Maps](media/how-to-view-api-usage/filter.png)

7. Também pode **aplicar a divisão** para a sua métrica com base na sua propriedade métrica selecionada. Permite que o gráfico seja dividido em vários gráficos, por cada valor dessa propriedade. Na imagem seguinte, a cor de cada gráfico corresponde ao valor da propriedade mostrado na parte inferior do gráfico.

    ![Métricas de utilização do Azure Maps dividindo-se](media/how-to-view-api-usage/splitting.png)

8. Também pode observar várias métricas no mesmo gráfico, bastando clicar no botão **métrico Add** em cima.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as APIs Azure Maps que pretende acompanhar o uso para:
> [!div class="nextstepaction"] 
> [Azure Maps Web SDK How-To](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Azure Maps Android SDK Como Fazer](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Documentação AZURE Maps REST API](/rest/api/maps)