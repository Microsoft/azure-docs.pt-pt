---
title: Adicione uma camada de azulejo ao Azure Maps Power BI visual ! Microsoft Azure Maps
description: Neste artigo, você vai aprender a usar a camada de azulejos no Microsoft Azure Maps visual para Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 8a0d930d1c0fd30c48d97b0d1d4b94548077fbca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261939"
---
# <a name="add-a-tile-layer"></a>Adicionar uma camada de mosaico

A função de camada de azulejo, tal como a característica da camada de referência, permite que dados adicionais sejam sobrepostos no mapa para fornecer mais contexto. As camadas de azulejos permitem-lhe sobrepor imagens em cima dos azulejos do mapa de base do Azure Maps. Esta é uma ótima maneira de sobrepor conjuntos de dados grandes ou complexos, como imagens de drones, ou milhões de linhas de dados.

> [!div class="mx-imgBorder"]
> ![Um mapa mostrando uma camada de bolha acima de uma camada de azulejos mostrando dados atuais do tempo infravermelho do Azure Maps](media/power-bi-visual/radar-tile-layer-with-bubbles.png)

Uma camada de azulejos carrega em azulejos de um servidor. Estas imagens podem ser pré-renderizadas ou dinamicamente renderizadas. As imagens pré-renderizadas são armazenadas como qualquer outra imagem num servidor usando uma convenção de nomeação que a camada de azulejos compreende. Imagens dinamicamente renderizadas usam um serviço para carregar as imagens perto de tempo real. As camadas de azulejos são uma ótima maneira de visualizar grandes conjuntos de dados no mapa. Não só uma camada de azulejo pode ser gerada a partir de uma imagem, os dados vetoriais também podem ser renderizados como uma camada de azulejos também.

A faixa de limite e de zoom de onde está disponível um serviço de azulejos pode ser passada como configurações para limitar onde os azulejos são solicitados. Este é simultaneamente um melhoramento de desempenho tanto para o serviço visual como para o azulejo. Abaixo está uma visão geral de todas as definições disponíveis no painel **de formato** que estão disponíveis na secção camada **de azulejos.**

| Definições        | Descrição   |
|----------------|---------------|
| Url            | Um URL formatado que aponta para um serviço de azulejos.  |
| Tamanho do mosaico      | Um valor inteiro que especifica tanto a largura como as dimensões de altura dos azulejos.   |
| Norte    | Latitude norte da caixa de delimitação onde os azulejos estão disponíveis. |
| Rumo ao sul    | Latitude sul da caixa de delimitação onde os azulejos estão disponíveis. |
| Ligado para leste     | Longitude oriental da caixa de delimitação onde os azulejos estão disponíveis.  |
| Ligado ao oeste     | Longitude ocidental da caixa de delimitação onde os azulejos estão disponíveis.   |
| Transparência   | Transparência da camada de azulejos.   |
| É TMS         | Serviços de mapa de azulejos, uma especificação que inverte o eixo de coordenada Y da camada de azulejos. |
| Min zoom       | Estão disponíveis telhas de nível mínimo de zoom. |
| Zoom máximo       | Estão disponíveis telhas de nível máximo de zoom.  |
| Posição da camada | Especifica a posição da camada em relação a outras camadas de mapa. |

## <a name="tile-url-formatting"></a>Formatação url de azulejos

Existem três diferentes convenções de nomeação de azulejos apoiadas pelo visual Azure Maps:

-   **X, Y, Notação zoom** - X é a coluna, Y é a posição de linha do azulejo na grelha de azulejos, e a notação zoom um valor baseado no nível de zoom.
-   **Notação quadkey** - Combina informação x, y e zoom em um único valor de corda. Este valor de corda torna-se um identificador único para um único azulejo.
-   **Caixa limite -** Especifique uma imagem no formato de coordenadas da caixa bounding: `{west},{south},{east},{north}` . Este formato é comumente utilizado pelos [Serviços de Mapeamento Web (WMS)](https://www.opengeospatial.org/standards/wms).

O URL de azulejos um URL https para um modelo de URL de azulejos que utiliza os seguintes parâmetros:

-   `{x}` - X posição do azulejo. Também precisa `{y}` `{z}` e. .
-   `{y}` - Posição Y do azulejo. Também precisa `{x}` `{z}` e. .
-   `{z}` - Zoom nível do azulejo. Também precisa `{x}` `{y}` e. .
-   `{quadkey}` - `quadkey` Identificador de azulejos baseado na convenção de nomeação do sistema de azulejos Bing Maps.
-   `{bbox-epsg-3857}` - Uma cadeia de caixa de limitação com o formato `{west},{south},{east},{north}` no sistema de referência espacial EPSG 3857.

Como exemplo, o seguinte é um URL de azulejos formatado para o [serviço de azulejos de radar meteorológico](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) em Azure Maps. Note que `[subscription-key]` é um espaço reservado para a sua chave de subscrição Azure Maps.

> `https://atlas.microsoft.com/map/tile?zoom={z}&x={x}&y={y}&tilesetId=microsoft.weather.radar.main&api-version=2.0&subscription-key=[subscription-key]`

Para obter mais informações sobre o sistema de inclinação Azure Maps, consulte [os níveis de Zoom e a grelha de azulejos.](zoom-levels-and-tile-grid.md)

## <a name="next-steps"></a>Passos seguintes

Adicione mais contexto ao mapa:

> [!div class="nextstepaction"]
> [Mostrar tráfego em tempo real](power-bi-visual-show-real-time-traffic.md)
