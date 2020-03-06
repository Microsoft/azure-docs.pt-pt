---
title: Adicione uma camada de mapa do Consórcio Geoespacial Aberto (OGC) Microsoft Azure Maps
description: Aprenda a sobrepor uma camada de mapa oGC no mapa e como usar as diferentes opções na classe OgcMapLayer.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: c41ee293c853572ec9e1f9dd3edf001c805924d3
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402769"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Adicione uma camada de mapa do Consórcio Geoespacial Aberto (OGC)

A classe `atlas.layer.OgcMapLayer` pode sobrepor imagens de Web Mapping Services (WMS) e web mapping tile Services (WMTS) no mapa. WMS é um protocolo padrão desenvolvido pela OGC para servir imagens de mapas georeferenciadas através da internet. A georreferenciação da imagem são os processos de associar uma imagem a uma localização geográfica. WMTS é também um protocolo padrão desenvolvido pela OGC. É projetado para servir azulejos de mapa pré-renderizados e georeferenciados.

As seguintes secções delineiam as funcionalidades do Serviço de Mapeamento Web que são suportadas pela classe `OgcMapLayer`.

**Serviço de Mapeamento Web (WMS)**

- Versões suportadas: `1.0.0`, `1.1.0`, `1.1.1`e `1.3.0`
- O serviço deve suportar o sistema de projeção `EPSG:3857`, ou o serviço deve ser capaz de lidar com as projeções.
- O GetFeatureInfo requer que o serviço suporte `EPSG:4326` ou manuseie as reprojecções. 
- Operações apoiadas:

    | | |
    | :-- | :-- |
    | Capacidades Get | Recupera metadados sobre o serviço com as capacidades suportadas |
    | GetMap | Recupera uma imagem de mapa para uma região especificada |
    | GetFeatureInfo | Recupera `feature_info`, que contém dados subjacentes sobre a funcionalidade |

**Serviço de azulejos de mapeamento web (WMTS)**

- Versões suportadas: `1.0.0`
- Os azulejos devem ser quadrados, de modo a que `TileWidth == TileHeight`.
- CRS suportado: `EPSG:3857` ou `GoogleMapsCompatible` 
- O identificador TileMatrix deve ser um valor inteiro que corresponda a um nível de zoom no mapa. Num mapa azul, o nível de zoom é um valor entre `"0"` e `"22"`. Então, `"0"` é apoiado, mas `"00"` não é apoiado.
- Operações apoiadas:

    | | |
    | :-- | :-- |
    | Capacidades Get | Recupera as operações e funcionalidades suportadas |
    | Rio GetTile | Recupera imagens para um azulejo particular |

## <a name="overlay-an-ogc-map-layer"></a>Sobreponha uma camada de mapa ogc

O `url` pode ser o URL base para o serviço ou um URL completo com a consulta para obter as capacidades do serviço. Dependendo dos detalhes fornecidos, o cliente WFS poderá experimentar vários formatos DE URL padrão para determinar como aceder inicialmente ao serviço.

O código que se segue mostra como sobrepor uma camada de mapa oGC no mapa.

<br/>

<iframe height='700' scrolling='no' title='Exemplo de camada de mapa ogc' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja o exemplo da camada do Mapa Pen <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>OGC</a> por Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-map-layer-options"></a>Opções de camada de mapa oGC

A amostra abaixo demonstra as diferentes opções de camada de mapa oGC. Pode clicar no botão de caneta de código no canto superior direito para editar a caneta de código.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de mapa oGC' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>opções</a> de camada de gráfico Pen OGC por Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>Explorador de serviço de mapa web da OGC

A seguinte ferramenta sobrepõe imagens dos Serviços de Mapas Web (WMS) e Web Map Tile Services (WMTS) como camadas. Pode selecionar quais as camadas do serviço que são renderizadas no mapa. Também pode ver as lendas associadas para estas camadas.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='Explorador de serviço de mapa web da OGC' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o explorador do Serviço de Mapas Web Pen <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>OGC</a> pela Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

Também pode especificar as definições do mapa para utilizar um serviço de procuração. O serviço proxy permite-lhe carregar recursos que estão hospedados em domínios que não têm CORs habilitados.

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
