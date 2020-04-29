---
title: Adicione uma camada de mapa do Consórcio Geoespacial Aberto (OGC) Microsoft Azure Maps
description: Aprenda a sobrepor uma camada de mapa oGC no mapa e como usar as diferentes opções na classe OgcMapLayer.
author: philmea
ms.author: philmea
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b753ecfc07cfb3806838f8a05dbe33ef0bb92730
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334290"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Adicione uma camada de mapa do Consórcio Geoespacial Aberto (OGC)

A `atlas.layer.OgcMapLayer` classe pode sobrepor imagens de Web Map Services (WMS) e web map tile Services (WMTS) no mapa. WMS é um protocolo padrão desenvolvido pela OGC para servir imagens de mapas georeferenciadas através da internet. A georreferenciação da imagem são os processos de associar uma imagem a uma localização geográfica. WMTS é também um protocolo padrão desenvolvido pela OGC. É projetado para servir azulejos de mapa pré-renderizados e georeferenciados.

As seguintes secções delineiam as funcionalidades `OgcMapLayer` do serviço de mapas web que são suportadas pela classe.

**Serviço de mapas web (WMS)**

- Versões suportadas: `1.0.0`, `1.1.0`, `1.1.1`e`1.3.0`
- O serviço deve `EPSG:3857` suportar o sistema de projeção ou lidar com as projeções.
- O GetFeatureInfo requer o `EPSG:4326` serviço para suportar ou lidar com as reprojecções. 
- Operações apoiadas:

    | | |
    | :-- | :-- |
    | Capacidades Get | Recupera metadados sobre o serviço com as capacidades suportadas |
    | GetMap | Recupera uma imagem de mapa para uma região especificada |
    | GetFeatureInfo | Recupera `feature_info`, que contém dados subjacentes sobre a funcionalidade |

**Serviço de azulejos do mapa web (WMTS)**

- Versões suportadas:`1.0.0`
- Os azulejos devem `TileWidth == TileHeight`ser quadrados, de tal forma que.
- CRS suportado: `EPSG:3857` ou`GoogleMapsCompatible` 
- O identificador TileMatrix deve ser um valor inteiro que corresponda a um nível de zoom no mapa. Num mapa azul, o nível de `"0"` `"22"`zoom é um valor entre e . Portanto, `"0"` é apoiado, `"00"` mas não é apoiado.
- Operações apoiadas:

    | | |
    | :-- | :-- |
    | Capacidades Get | Recupera as operações e funcionalidades suportadas |
    | Rio GetTile | Recupera imagens para um azulejo particular |

## <a name="overlay-an-ogc-map-layer"></a>Sobreponha uma camada de mapa ogc

Pode `url` ser o URL base para o serviço ou um URL completo com a consulta para obter as capacidades do serviço. Dependendo dos detalhes fornecidos, o cliente WFS poderá experimentar vários formatos DE URL padrão para determinar como aceder inicialmente ao serviço.

O código que se segue mostra como sobrepor uma camada de mapa oGC no mapa.

<br/>

<iframe height='700' scrolling='no' title='Exemplo de camada de mapa ogc' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja o exemplo da camada do Mapa<a href='https://codepen.io/azuremaps'>@azuremaps</a>Pen <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>OGC</a> por Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-map-layer-options"></a>Opções de camada de mapa oGC

A amostra abaixo demonstra as diferentes opções de camada de mapa oGC. Pode clicar no botão de caneta de código no canto superior direito para editar a caneta de código.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de mapa oGC' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>opções</a> de camada de<a href='https://codepen.io/azuremaps'>@azuremaps</a>gráfico Pen OGC por Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>Explorador de serviço de mapa web da OGC

A seguinte ferramenta sobrepõe imagens dos Serviços de Mapas Web (WMS) e Web Map Tile Services (WMTS) como camadas. Pode selecionar quais as camadas do serviço que são renderizadas no mapa. Também pode ver as lendas associadas para estas camadas.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='Explorador de serviço de mapa web da OGC' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o explorador do Serviço de Mapas<a href='https://codepen.io/azuremaps'>@azuremaps</a>Web Pen <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>OGC</a> pela Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

Também pode especificar as definições do mapa para utilizar um serviço de procuração. O serviço proxy permite-lhe carregar recursos que estão hospedados em domínios que não têm CORS habilitado.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [OgcMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [Opções OgcMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Consulte os seguintes artigos, que contêm amostras de código que pode adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Ligue-se a um serviço WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Alavancar operações de núcleo](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Detalhes do formato de dados suportados](spatial-io-supported-data-format-details.md)
