---
title: Adicione uma camada de gráfico de barras ao Azure Maps Power BI visual / Microsoft Azure Maps
description: Neste artigo, você vai aprender a usar a camada de gráfico de barras no Microsoft Azure Maps visual para Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 136676b46df6e32f98dca99fccba19d4aa369dbe
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896281"
---
# <a name="add-a-bar-chart-layer"></a>Adicionar uma camada de gráfico de barras

A **camada de gráfico de Barras** é útil para levar dados para a dimensão seguinte, permitindo a visualização de dados de localização como barras 3D ou cilindros no mapa. Semelhante à camada de bolha, o gráfico de barras mais tarde pode facilmente visualizar duas métricas ao mesmo tempo usando cor e altura relativa. Para que as barras tenham altura, é necessário adicionar uma medida ao balde **tamanho** do painel **fields.** Se não for fornecida uma medida, as barras sem altura apresentam-se como quadrados planos ou círculos, dependendo da opção de forma de **Barra.**

> [!div class="mx-imgBorder"]
> ![Um mapa exibindo dados de ponto usando a camada de gráfico de barra](media/power-bi-visual/bar-chart-layer-styled.png)

Os utilizadores podem inclinar e rodar o mapa para visualizar os seus dados de diferentes perspetivas. O mapa pode ser inclinado ou lançado usando um dos seguintes métodos.

-   Ligue a opção **de controlos de navegação** nas **definições** do mapa do painel **de formato.** Isto irá adicionar um botão para inclinar o mapa.
-   Pressione o botão do rato direito para baixo e arraste o rato para cima ou para baixo.
-   Utilizando um ecrã táctil, toque no mapa com dois dedos e arraste-os para cima ou para baixo juntos.
-   Com o mapa focado, segure a tecla **Shift** e pressione as **teclas** de seta **para cima** ou para baixo.

O mapa pode ser rodado utilizando um dos seguintes métodos.

-   Ligue a opção **de controlos de navegação** nas **definições** do mapa do painel **de formato.** Isto irá adicionar um botão para rodar o mapa.
-   Pressione o botão do rato direito para baixo e arraste o rato para a esquerda ou para a direita.
-   Utilizando um ecrã táctil, toque no mapa com dois dedos e rode.
-   Com o mapa focado, segure a tecla **Shift** e pressione as **teclas** de seta **esquerda** ou direita.

Seguem-se todas as definições no painel **de formato** que estão disponíveis na secção de camadas de gráfico de **barras.**

| Definições              | Descrição      |
|----------------------|------------------|
| Forma de barra            | A forma da barra 3D.<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Caixa – Barras renderizadas como caixas retangulares.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Cilindro – Barras renderizadas como cilindros. |
| Height               | A altura de cada barra. Se um campo for passado para o balde **tamanho** do painel **Fields,** as barras serão dimensionados em relação a este valor de altura. |
| Altura da balança no zoom | Especifica se a altura das barras deve escalar em relação ao nível de zoom. |
| Width                | A largura de cada barra.  |
| Largura da escala no zoom  | Especifica se a largura das barras deve escalar em relação ao nível de zoom.  |
| Cor de preenchimento           | Cor de cada barra. Esta opção é ocultada quando um campo é passado para o balde **da Lenda** do painel **Fields** e uma secção de cores **de dados** separada aparecerá no painel **de formato.** |
| Transparência         | Transparência de cada barra. |
| Min zoom             | Estão disponíveis telhas de nível mínimo de zoom. |
| Zoom máximo             | Estão disponíveis telhas de nível máximo de zoom. |
| Posição da camada       | Especifica a posição da camada em relação a outras camadas de mapa. |

> [!NOTE]
> Se as barras tiverem um pequeno valor de largura e a largura da escala na opção **de zoom** for desativada, podem desaparecer quando ampliadas, uma vez que a sua largura renderizada seria inferior a um pixel de tamanho. No entanto, quando a largura da escala na opção **de zoom** é ativada, são realizados cálculos adicionais quando o nível de zoom muda, o que pode impactar o desempenho de grandes conjuntos de dados.

## <a name="next-steps"></a>Passos seguintes

Adicione mais contexto ao mapa:

> [!div class="nextstepaction"]
> [Adicionar uma camada de referência](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de mosaico](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Mostrar tráfego em tempo real](power-bi-visual-show-real-time-traffic.md)

Personalize o visual:

> [!div class="nextstepaction"]
> [Sugestões e truques para a formatação de cores no Power BI](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [Personalizar os títulos, fundos e legendas das visualizações](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)