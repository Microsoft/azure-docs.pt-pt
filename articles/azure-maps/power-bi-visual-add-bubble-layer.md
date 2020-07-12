---
title: Adicione uma camada de bolha ao Azure Maps Power BI visual Microsoft Azure Maps
description: Neste artigo, você vai aprender a usar a camada de bolha no Microsoft Azure Maps visual para Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 4443b0f479079a4722a5d62fea40afcb4a58632d
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261938"
---
# <a name="add-a-bubble-layer"></a>Adicionar uma camada de bolha

A **camada bolha** torna os dados de localização como círculos dimensionizados no mapa.

> [!div class="mx-imgBorder"]
> ![Um mapa exibindo dados de ponto usando a camada de bolha](media/power-bi-visual/bubble-layer-with-legend-color.png)

Inicialmente, todas as bolhas têm a mesma cor de enchimento. Se um campo for passado para o balde **da lenda** do painel **Fields,** as bolhas serão coloridas com base na sua categorização. O contorno das bolhas é branco ser padrão, mas pode ser alterado para uma nova cor ou permitindo a opção de contorno de alto contraste. A opção **de contorno de alto contraste** atribui dinamicamente uma cor de contorno que é uma variante de alto contraste da cor de preenchimento. Isto ajuda a garantir que as bolhas são claramente visíveis independentemente do estilo do mapa. Seguem-se as definições primárias no painel **de formato** que estão disponíveis na secção camada **de bolha.**

| Definição               | Descrição    |
|-----------------------|----------------|
| Tamanho                  | O tamanho de cada bolha. Esta opção é escondida quando um campo é passado para o balde **tamanho** do painel **fields.** Opções adicionais aparecerão como delineadas no tópico de escala do [tamanho da Bolha](#bubble-size-scaling) mais abaixo neste artigo. |
| Cor de preenchimento            | Cor de cada bolha. Esta opção é ocultada quando um campo é passado para o balde **da Lenda** do painel **Fields** e uma secção de cores **de dados** separada aparecerá no painel **de formato.** |
| Preencher a transparência     | Transparência de cada bolha. |
| Contorno de alto contraste | Faz o contraste de cor do contorno com a cor de preenchimento para uma melhor acessibilidade usando uma variante de alto contraste da cor de preenchimento. |
| Cor do contorno         | Cor que descreve a bolha. Esta opção é ocultada quando a opção **de contorno de alto contraste** está ativada. |
| Contorno da transparência  | Transparência do contorno. |
| Largura do contorno         | Largura do contorno em pixels. |
| Borrão                  | Quantidade de borrão aplicada ao contorno. Um valor de 1 borra as bolhas de tal forma que só o ponto central não tem transparência. Um valor de 0 aplica qualquer efeito de borrão. |
| Alinhamento de arremesso       | Especifica como as bolhas ficam quando o mapa é lançado. <br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Viewport - Bolhas aparecem na borda do mapa relativamente ao viewport. (Predefinição)<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Mapa - As bolhas são renderizadas planas na superfície do mapa. |
| Escala de zoom            | Montante as bolhas devem escalar em relação ao nível de zoom. Uma escala de zoom de um significa não escalar. Os grandes valores tornarão as bolhas menores quando ampliadas e maiores quando ampliadas. Isto ajuda a reduzir a confusão no mapa quando ampliado, mas garante que os pontos se destacam mais quando ampliados. Um valor de 1 não aplica qualquer escalonamento. |
| Min zoom              | Estão disponíveis telhas de nível mínimo de zoom. |
| Zoom máximo              | Estão disponíveis telhas de nível máximo de zoom. |
| Posição da camada        | Especifica a posição da camada em relação a outras camadas de mapa. |

## <a name="bubble-size-scaling"></a>Escala do tamanho da bolha

Se um campo for passado para o balde **tamanho** do painel **fields,** as bolhas serão dimensionados relativamente ao valor de medida de cada ponto de dados. A opção **Tamanho** na secção **camada de bolha** do painel de **formato** desaparecerá quando um campo é passado para o balde **Tamanho,** uma vez que as bolhas terão o seu radii dimensionado entre um min e o valor máximo. As seguintes opções aparecerão na secção **camada de bolha** do painel de **formato** quando um balde **de tamanho** tiver um campo especificado.

| Definição             | Descrição  |
|---------------------|--------------|
| Tamanho min            | Tamanho mínimo da bolha ao escalonar os dados.|
| Tamanho máximo            | Tamanho máximo da bolha ao escalonar os dados.|
| Método de escala de tamanho | Algoritmo de escala usado para determinar o tamanho relativo da bolha.<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Linear - Gama de dados de entrada linearmente mapeados para o tamanho min e máximo. (Predefinição)<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Log - Gama de dados de entrada logaritmicamente mapeados para o tamanho min e max.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Cúbico-Bezier - Especificar os valores X1, Y1, X2, Y2 de uma curva Cubic-Bezier para criar um método de escala personalizado. |

Quando o **método de escalagem size** estiver definido para **Registar,** serão disponibilizadas as seguintes opções.

| Definição   | Descrição      |
|-----------|------------------|
| Escala de registo | A escala logarítmica a aplicar ao calcular o tamanho das bolhas. |

Quando o **método de escalagem size** estiver definido para **Cubic-Bezier,** serão disponibilizadas as seguintes opções para personalizar a curva de escala.

| Definição | Descrição                           |
|---------|---------------------------------------|
| X1      | Parâmetro X1 de uma curva cúbica de Bezier. |
| Y1      | Parâmetro X2 de uma curva cúbica de Bezier. |
| X2      | Parâmetro Y1 de uma curva cúbica de Bezier. |
| Y2      | Parâmetro Y2 de uma curva cúbica de Bezier. |

> [!TIP]
> [https://cubic-bezier.com/](https://cubic-bezier.com/)tem uma ferramenta útil para criar os parâmetros para curvas Cubic-Bezier.

## <a name="next-steps"></a>Passos seguintes

Altere a forma como os seus dados são apresentados no mapa:

> [!div class="nextstepaction"]
> [Adicione uma camada de gráfico de barras](power-bi-visual-add-bar-chart-layer.md)

Adicione mais contexto ao mapa:

> [!div class="nextstepaction"]
> [Adicione uma camada de referência](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de mosaico](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Mostrar tráfego em tempo real](power-bi-visual-show-real-time-traffic.md)

Personalize o visual:

> [!div class="nextstepaction"]
> [Sugestões e truques para a formatação de cores no Power BI](https://docs.microsoft.com/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [Personalizar os títulos, fundos e legendas das visualizações](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)