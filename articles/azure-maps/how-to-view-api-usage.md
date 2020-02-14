---
title: Ver as métricas de utilização da API do Azure Maps  Microsoft Azure Maps
description: Neste artigo, você vai aprender a ver as métricas para as suas chamadas API Do Microsoft Azure Maps no portal Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: cd60552abf247f448acdaad700396dca0cd1a1f7
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198127"
---
# <a name="view-azure-maps-api-usage-metrics"></a>Ver as métricas de utilização da API do Azure Maps

Este artigo mostra-lhe como ver as métricas de utilização da API, para a sua conta Azure Maps, no [portal Azure](https://portal.azure.com). As métricas são mostradas num formato gráfico conveniente ao longo de uma duração de tempo personalizável.

## <a name="view-metric-snapshot"></a>Ver instantâneo métrico

Pode ver algumas métricas comuns na página **de visão geral** da sua conta Maps. Atualmente mostra *Pedidos Totais,* *Erros Totais*e *Disponibilidade* durante uma duração de tempo selecionável.

![Visão geral das métricas de utilização do Azure Maps](media/how-to-view-api-usage/portal-overview.png)

Continue na secção seguinte se precisar personalizar estes gráficos para a sua análise particular.

## <a name="view-detailed-metrics"></a>Ver métricas detalhadas

1. Inscreva-se na sua assinatura Azure no [portal.](https://portal.azure.com)

2. Clique no item do menu **Todos os recursos** no lado esquerdo e navegue para a sua Conta Mapas *Azure*.

3. Assim que a sua conta Maps estiver aberta, clique no menu **Métricas** à esquerda.

4. No painel **das Métricas,** escolha uma das seguintes opções:

   1. **Disponibilidade** - que mostra a *média* de disponibilidade da API durante um período de tempo.
   2. **Utilização** - que mostra como o uso *Conta* para a sua conta.

      ![Painel de métricas de utilização do Azure Maps](media/how-to-view-api-usage/portal-metrics.png)

5. Em seguida, poderá selecionar a *gama de tempo* clicando nas **últimas 24 horas (Automáticas)** . Por predefinição, o intervalo de tempo está definido para 24 horas. Depois de clicar, verá todos os intervalos de tempo selecionáveis. Pode selecionar a *granularidade* do tempo e optar por mostrar o tempo como *local* ou *GMT* na mesma queda. Clique em **Aplicar**.

    ![Alcance de tempo das métricas do Azure Maps](media/how-to-view-api-usage/time-range.png)

6. Uma vez adicionado a sua métrica, pode **adicionar filtro** das propriedades relevantes a essa métrica. Em seguida, selecione o valor da propriedade que pretende ver refletida no gráfico.

    ![Filtro de métricas de utilização do Azure Maps](media/how-to-view-api-usage/filter.png)

7. Também pode **Aplicar a divisão** para a sua métrica com base na sua propriedade métrica selecionada. Permite que o gráfico seja dividido em vários gráficos, por cada valor dessa propriedade. Na imagem seguinte, a cor de cada gráfico corresponde ao valor de propriedade mostrado na parte inferior do gráfico.

    ![Métricas de utilização do Azure Maps que se dividem](media/how-to-view-api-usage/splitting.png)

8. Também pode observar várias métricas no mesmo gráfico, simplesmente clicando no botão **métrica Adicionar** por cima.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as APIs do Azure Maps que pretende rastrear o uso para:
> [!div class="nextstepaction"] 
> [Azure Maps Web SDK How-To](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Azure Maps Android SDK How-To](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Documentação da API do Azure Maps REST](https://docs.microsoft.com/rest/api/maps)
