---
title: Compreender camadas no Azure Maps Power BI | Microsoft Azure Maps
description: Neste artigo, você vai aprender sobre as diferentes camadas disponíveis no Microsoft Azure Maps visual para Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a5c7296a0e7b7f6ea33c1f4a669675efd90f9e9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86261848"
---
# <a name="understanding-layers-in-the-azure-maps-power-bi-visual"></a>Compreender camadas no Azure Maps Power BI visual

Existem dois tipos de camadas disponíveis no visual do Azure Maps. O primeiro tipo centra-se na renderização de dados que são transmitidos para **o** painel fields do visual e consistem nas seguintes camadas, vamos chamar estas camadas de renderização de dados.

:::row:::
    :::column span="":::
        **Camada de bolhas**

        Apresenta pontos como círculos escalonado no mapa.

        ![Camada de bolha no mapa](media/power-bi-visual/bubble-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Camada de gráfico de barra**

        Apresenta pontos como barras 3D no mapa.
        
        ![Camada de gráfico de barra no mapa](media/power-bi-visual/bar-chart-layer-thumb.png)
    :::column-end:::
:::row-end:::

O segundo tipo de camada liga fontes externas de dados adicionais para mapear para fornecer mais contexto e consiste nas seguintes camadas.

:::row:::
    :::column span="":::
        **Camada de referência**

        Sobreponha um ficheiro GeoJSON carregado no topo do mapa.

        ![Camada de referência no mapa](media/power-bi-visual/reference-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Camada de azulejo**

        Sobreponha uma camada de azulejos personalizados em cima do mapa.
        
        ![Camada de azulejo no mapa](media/power-bi-visual/tile-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Camada de tráfego**

        Sobreponha informações de tráfego em tempo real no mapa.
        
        ![Camada de tráfego no mapa](media/power-bi-visual/traffic-layer-thumb.png)
    :::column-end:::
:::row-end:::

Todas as camadas de renderização de dados, bem como a **camada de azulejos,** têm opções para os níveis de zoom min e max que são usados para especificar uma gama de nível de zoom em que estas camadas devem ser exibidas. Isto permite que um tipo de camada de renderização seja usado a um nível de zoom e uma transição para outra camada de renderização em outro nível de zoom.

Estas camadas também têm uma opção a ser posicionadas em relação a outras camadas no mapa. Quando são utilizadas várias camadas de renderização de dados, a ordem na qual são adicionadas ao mapa determina a sua ordem de camadas relativa quando têm o mesmo valor **de posição camada.**

## <a name="general-layer-settings"></a>Configurações gerais da camada

A secção geral da camada do painel **de formato** são configurações comuns que se aplicam às camadas que estão ligadas ao conjunto de dados Power BI no painel **fields** (camada de bolha, gráfico de barras).

| Definições     | Descrição   |
|-------------|---------------|
| Transparência nãolectionada | A transparência das formas que não são selecionadas, quando uma ou mais formas são selecionadas.  |
| Mostrar zeros              | Especifica se os pontos que têm um valor de tamanho zero devem ser mostrados no mapa usando o raio mínimo. |
| Mostrar negativos          | Especifica se o valor absoluto dos valores de tamanho negativo deve ser traçado.   |
| Valor de dados min          | O valor mínimo dos dados de entrada para escalar contra. Bom para recortar forasteiros.  |
| Valor máximo dos dados          | O valor máximo dos dados de entrada para escalar contra. Bom para recortar forasteiros.  |

## <a name="next-steps"></a>Passos seguintes

Altere a forma como os seus dados são apresentados no mapa:

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolha](power-bi-visual-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de gráfico de barras](power-bi-visual-add-bar-chart-layer.md)

Adicione mais contexto ao mapa:

> [!div class="nextstepaction"]
> [Adicionar uma camada de referência](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de mosaico](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Mostrar tráfego em tempo real](power-bi-visual-show-real-time-traffic.md)
