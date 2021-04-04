---
title: Adicione uma camada de mapa do Consórcio Geoespacial Aberto (OGC) | Microsoft Azure Maps
description: Saiba como sobrepor uma camada de mapa OGC no mapa e como usar as diferentes opções na classe OgcMapLayer.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7d94a681fa987a4b23dbcda744c2f8516da4437a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92891670"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Adicione uma camada de mapa do Consórcio Geoespacial Aberto (OGC)

A `atlas.layer.OgcMapLayer` classe pode sobrepor imagens de Web Map Services (WMS) e imagens de Serviços de Azulejos do Mapa Web (WMTS) no mapa. WMS é um protocolo padrão desenvolvido pela OGC para servir imagens de mapas georreferenciadas através da internet. Georeferencing de imagem são os processos de associar uma imagem a uma localização geográfica. O WMTS é também um protocolo padrão desenvolvido pela OGC. É projetado para servir azulejos de mapa pré-renderizados e georreferenciados.

As secções seguintes descrevem as funcionalidades do serviço de mapa web que são suportadas pela `OgcMapLayer` classe.

**Serviço de mapas web (WMS)**

- Versões apoiadas: `1.0.0` `1.1.0` , `1.1.1` e `1.3.0`
- O serviço deve suportar o `EPSG:3857` sistema de projeção ou lidar com reprojecções.
- O GetFeatureInfo requer o serviço para suportar `EPSG:4326` ou lidar com reprojecções. 
- Operações apoiadas:

    | Operação | Description |
    | :-- | :-- |
    | ObterCapabilidades | Recupera metadados sobre o serviço com as capacidades suportadas |
    | GetMap | Recupera uma imagem de mapa para uma região especificada |
    | GetFeatureInfo | Recupera `feature_info` , que contém dados subjacentes sobre a funcionalidade |

**Serviço de azulejos do mapa web (WMTS)**

- Versões suportadas: `1.0.0`
- Os azulejos devem ser quadrados, de tal forma `TileWidth == TileHeight` que.
- SIR apoiado: `EPSG:3857` ou `GoogleMapsCompatible` 
- O identificador TileMatrix deve ser um valor inteiro que corresponda a um nível de zoom no mapa. Num mapa azul, o nível de zoom é um valor entre `"0"` e `"22"` . Então, `"0"` é apoiado, mas `"00"` não é apoiado.
- Operações apoiadas:

    | Operação | Description |
    | :-- | :-- |
    | ObterCapabilidades | Recupera as operações e funcionalidades suportadas |
    | GetTile | Recupera imagens para um determinado azulejo |

## <a name="overlay-an-ogc-map-layer"></a>Sobreponha uma camada de mapa OGC

`url`Pode ser o URL base para o serviço ou um URL completo com a consulta para obter as capacidades do serviço. Dependendo dos detalhes fornecidos, o cliente WFS pode tentar vários formatos url padrão para determinar como aceder inicialmente ao serviço.

O código que se segue mostra como sobrepor uma camada de mapa OGC no mapa.

<br/>

<iframe height='700' scrolling='no' title='Exemplo da camada do mapa do OGC' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja o exemplo da camada do mapa pen <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>OGC</a> por Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-map-layer-options"></a>Opções de camada de mapa OGC

A amostra abaixo demonstra as diferentes opções de camada de mapa OGC. Pode clicar no botão de caneta de código no canto superior direito para editar a caneta de código.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de mapa OGC' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as opções de <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>camada de mapa pen OGC</a> por Azure Maps ( ) no <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>Explorador de serviço de mapa web OGC

A ferramenta que se segue sobrepõe as imagens dos Serviços de Mapa Web (WMS) e web Map Tile Services (WMTS) como camadas. Pode selecionar quais as camadas do serviço que são renderizadas no mapa. Você também pode ver as lendas associadas para estas camadas.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='Explorador de serviço de mapa web OGC' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o explorador do <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>Serviço de Mapas Web</a> Pen OGC por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

Também pode especificar as definições do mapa para utilizar um serviço de procuração. O serviço proxy permite-lhe carregar recursos que estão hospedados em domínios que não têm CORS habilitado.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [OgcMapLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [Opções OgcMapLayer](/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Consulte os seguintes artigos, que contêm amostras de código que pode adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Ligar a um serviço WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Tirar partido de operações principais](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Detalhes do formato de dados suportado](spatial-io-supported-data-format-details.md)