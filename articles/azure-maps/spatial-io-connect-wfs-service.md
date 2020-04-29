---
title: Ligue-se a um serviço de Serviço de Funcionalidadeweb (WFS) [ Microsoft Azure Maps
description: Aprenda a ligar-se a um serviço WFS, em seguida, consulte o serviço WFS usando o Web SDK do Azure Maps e o módulo Espacial IO.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8b511395eb61e8845aaa11e5ca7a490dc461424d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334210"
---
# <a name="connect-to-a-wfs-service"></a>Ligue-se a um serviço WFS

Um Serviço de FuncionalidadeWeb (WFS) é um serviço web para consulta de dados espaciais que tem uma API padronizada que é definida pelo Consórcio Geoespacial Aberto (OGC). A `WfsClient` classe no módulo IO espacial permite que os desenvolvedores se conectem a um serviço WFS e consulta maçar dados do serviço.

As seguintes funcionalidades `WfsClient` são suportadas pela classe:

- Versões suportadas: `1.0.0`, `1.1.0`e`2.0.0`
- Operadores de filtros suportados: comparações binárias, lógica, matemática, valor e `bbox`.
- Os pedidos são `HTTP GET` feitos usando apenas.
- Operações apoiadas:

    | | |
    | :-- | :-- |
    | Capacidades Get | Gera um documento de metadados com operações e parâmetros WFS válidos |
    | GetFeature | Devolve uma seleção de funcionalidades a partir de uma fonte de dados |
    | Descrever o Tipo de Características | Devolve os tipos de funcionalidades suportados |

## <a name="using-the-wfs-client"></a>Usando o cliente WFS

A `atlas.io.ogc.WfsClient` classe no módulo IO espacial facilita a consulta de um serviço WFS e converte as respostas em objetos GeoJSON. Este objeto GeoJSON pode então ser utilizado para outros fins de mapeamento.

O código seguinte consulta um serviço WFS e torna as funcionalidades devolvidas no mapa.

<br/>

<iframe height='700' scrolling='no' title='Exemplo simples do WFS' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o exemplo da Pen Simple<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>WFS</a> da Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Filtros suportados

A especificação para a norma WFS utiliza filtros OGC. Os filtros abaixo são suportados pelo cliente WFS, assumindo que o serviço que está a ser chamado também suporta estes filtros. As cordas de filtro personalizadas podem ser passadas para a `CustomFilter` aula.

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

O código que se segue demonstra a utilização de diferentes filtros com o cliente WFS.

<br/>

<iframe height='500' scrolling='no' title= 'Exemplos de filtro WFS' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte os <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>exemplos</a> de filtro Pen<a href='https://codepen.io/azuremaps'>@azuremaps</a>WFS da Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>Explorador de serviço wfs

O seguinte código utiliza o cliente WFS para explorar os serviços WFS. Selecione uma camada de tipo de propriedade dentro do serviço e consulte a lenda associada.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'Explorador de serviço wfs' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o explorador de serviço Pen<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>WFS</a> da Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

Para aceder aos serviços WFS hospedados em pontos finais não CORS habilitados, um serviço de proxy ativado CORS pode ser passado para a opção `proxyService` do cliente WFS, como mostrado abaixo. 

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
> [WfsClient](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [Opções wfsService](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Alavancar operações de núcleo](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Detalhes do formato de dados suportados](spatial-io-supported-data-format-details.md)
