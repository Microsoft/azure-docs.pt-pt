---
title: Ligue-se a um serviço de serviço de recursos web (WFS) | Microsoft Azure Maps
description: Aprenda a ligar-se a um serviço WFS e, em seguida, consulte o serviço WFS utilizando o SDK web Azure Maps e o módulo IO espacial.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 75a7da41e4f0d5356ca135eb3cb4cbe5f29e8116
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92891459"
---
# <a name="connect-to-a-wfs-service"></a>Ligar a um serviço WFS

Um Serviço de Recursos Web (WFS) é um serviço web para consulta de dados espaciais que tem uma API padronizada que é definida pelo Open Geospatial Consortium (OGC). A `WfsClient` classe no módulo IO espacial permite que os desenvolvedores se conectem a um serviço WFS e consulta dados do serviço.

As seguintes funcionalidades são suportadas pela `WfsClient` classe:

- Versões apoiadas: `1.0.0` `1.1.0` , e `2.0.0`
- Operadores de filtros suportados: comparações binárias, lógica, matemática, valor e `bbox` .
- Os pedidos são feitos apenas com `HTTP GET` recurso.
- Operações apoiadas:

    | Operação | Description |
    | :-- | :-- |
    | ObterCapabilidades | Gera um documento de metadados com operações e parâmetros válidos do WFS |
    | GetFeature | Devolve uma seleção de funcionalidades de uma fonte de dados |
    | DescreverFeatureType | Devolve os tipos de funcionalidades suportados |

## <a name="using-the-wfs-client"></a>Usando o cliente WFS

A `atlas.io.ogc.WfsClient` classe no módulo IO espacial facilita a consulta de um serviço WFS e converte as respostas em objetos GeoJSON. Este objeto GeoJSON pode então ser utilizado para outros fins de mapeamento.

O código seguinte consulta um serviço WFS e torna as funcionalidades devolvidas no mapa.

<br/>

<iframe height='700' scrolling='no' title='Exemplo simples do WFS' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>exemplo pen Simple WFS</a> by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Filtros suportados

A especificação da norma WFS utiliza os filtros OGC. Os filtros abaixo são suportados pelo cliente WFS, assumindo que o serviço que está a ser chamado também suporta estes filtros. As cordas de filtro personalizadas podem ser passadas para a `CustomFilter` classe.

**Operadores lógicos**

- `And`
- `Or`
- `Not`

**Operadores de valor**

- `GmlObjectId`
- `ResourceId`

**Operadores de matemática**

- `Add`
- `Sub`
- `Mul`
- `Div`

**Operadores de comparação**

- `PropertyIsEqualTo`
- `PropertyIsNotEqualTo`
- `PropertyIsLessThan`
- `PropertyIsGreaterThan`
- `PropertyIsLessThanOrEqualTo`
- `PropertyIsGreaterThanOrEqualTo`
- `PropertyIsLike`
- `PropertyIsNull`
- `PropertyIsNil`
- `PropertyIsBetween`

O seguinte código demonstra a utilização de filtros diferentes com o cliente WFS.

<br/>

<iframe height='500' scrolling='no' title= 'Exemplos de filtro WFS' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte os exemplos de <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>filtro Pen WFS</a> por Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>Explorador de serviços WFS

O seguinte código utiliza o cliente WFS para explorar os serviços WFS. Selecione uma camada de tipo de propriedade dentro do serviço e veja a lenda associada.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'Explorador de serviços WFS' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o explorador de serviços Pen <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>WFS</a> da Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

Para aceder aos serviços WFS alojados em pontos finais não-CORS habilitados, um serviço de procuração habilitado ao CORS pode ser transmitido para a `proxyService` opção do cliente WFS, como mostrado abaixo. 

```JavaScript
//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
});
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [WfsClient](/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [Opções WfsService](/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Tirar partido de operações principais](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Detalhes do formato de dados suportado](spatial-io-supported-data-format-details.md)