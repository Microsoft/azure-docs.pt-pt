---
title: Detalhes do formato de dados suportados / Microsoft Azure Maps
description: Saiba como os dados espaciais delimitados são analisados no módulo IO espacial.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: fff801731c3c3a94b4039a8c65ad8ccaab7cc725
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402743"
---
# <a name="supported-data-format-details"></a>Detalhes do formato de dados suportados

Este artigo fornece detalhes sobre o suporte de leitura e escrita para todas as tags XML e tipos de geometria de texto bem conhecidos. Também detalha a forma como os dados espaciais delimitados são analisados no módulo IO espacial.

## <a name="supported-xml-namespaces"></a>Espaços de nome SxMl suportados

O módulo IO espacial suporta as etiquetas XML dos seguintes espaços de nome.

| Prefixo espaço de nome | Espaço de nome URI   | Notas                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | Leia apenas suporte em ficheiros GeoRSS.           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | Leia apenas suporte em ficheiros GeoRSS.                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | Leia apenas suporte em ficheiros GPX. Parses e usa displayColor. Todas as outras propriedades adicionadas para moldar metadados. |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | Suportado em ficheiros GPX. Usa a cor da linha. |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | Leia apenas. GeoRSS escreve usando o formato Atom.              |

## <a name="supported-xml-elements"></a>Elementos XML suportados

O módulo IO espacial suporta os seguintes elementos XML. Quaisquer etiquetas XML que não sejam suportadas serão convertidas num objeto JSON. Em seguida, cada etiqueta será adicionada como uma propriedade no campo `properties` da forma ou camada dos pais.

### <a name="kml-elements"></a>Elementos KML

O módulo IO espacial suporta os seguintes elementos KML.

| Nome do elemento         | Leitura    | Escrita   | Notas                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | parcial | sim     | O objeto é analisado, mas não é usado para posicionar a forma.                                                                    |
| `AddressDetails`     | parcial | não      | O objeto é analisado, mas não é usado para posicionar a forma.                                                                    |
| `atom:author`        | sim     | sim     |                                                                                                                            |
| `atom:link`          | sim     | sim     |                                                                                                                            |
| `atom:name`          | sim     | sim     |                                                                                                                            |
| `BalloonStyle`       | parcial | parcial | `displayMode` não é apoiado. Convertido para um `PopupTemplate`. Para escrever, adicione uma propriedade `popupTemplate` como propriedade da funcionalidade para a qual pretende escrevê-la. |
| `begin`              | sim     | sim     |                                                                                                                            |
| `color`              | sim     | sim     | Inclui `#AABBGGRR` e `#BBGGRR`. Analisado em uma cadeia de cores CSS                                                           |
| `colorMode`          | sim     | não      |                                                                                                                            |
| `coordinates`        | sim     | sim     |                                                                                                                            |
| `Data`               | sim     | sim     |                                                                                                                            |
| `description`        | sim     | sim     |                                                                                                                            |
| `displayName`        | sim     | sim     |                                                                                                                            |
| `Document`           | sim     | sim     |                                                                                                                            |
| `drawOrder`          | parcial | não      | Leia para sobreposições no chão e costumava separa-las. 
| `east`               | sim     | sim     |                                                                                                                            |
| `end`                | sim     | sim     |                                                                                                                            |
| `ExtendedData`       | sim     | sim     | Suporta `Data`não digitados, `SimpleData` ou `Schema`, e substituições de entidades do formulário `$[dataName]`.                      |
| `extrude`            | parcial | parcial | Apenas apoiado para polígonos. A multigeometria que tem polígonos de diferentes alturas será dividida em características individuais. Os estilos de linha não são suportados. Os polígonos com uma altitude de 0 serão renderizados como um polígono plano. Ao ler, a altitude da primeira coordenada no anel exterior será adicionada como uma propriedade de altura do polígono. Em seguida, a altitude da primeira coordenada será usada para renderizar o polígono no mapa. |
| `fill`               | sim     | sim     |                                                                                                                            |
| `Folder`             | sim     | sim     |                                                                                                                            |
| `GroundOverlay`      | sim     | sim     | `color` não é apoiado                                                                                                   |
| `heading`            | parcial | não      | Analisado, mas não prestado por `SimpleDataLayer`. Só escreve se os dados forem armazenados na propriedade da forma.                 |
| `hotSpot`            | sim     | parcial | Só escreve se os dados forem armazenados na propriedade da forma. As unidades são saídas apenas como "pixels".                         |
| `href`               | sim     | sim     |                                                                                                                            |
| `Icon`               | parcial | parcial | Analisado, mas não prestado por `SimpleDataLayer`. Apenas escreve a propriedade do ícone da forma se contiver um dados URI. Só `href` é apoiado. |
| `IconStyle`          | parcial | parcial | `icon`, `heading`, `colorMode`e `hotspots` valores são analisados, mas não são prestados por `SimpleDataLayer`         |
| `innerBoundaryIs`    | sim     | sim     |                                                                                                                            |
| `kml`                | sim     | sim     |                                                                                                                            |
| `LabelStyle`         | não      | não      |                                                                                                                            |
| `LatLonBox`          | sim     | sim     |                                                                                                                            |
| `gx:LatLonQuad`      | sim     | sim     |                                                                                                                            |
| `LinearRing`         | sim     | sim     |                                                                                                                            |
| `LineString`         | sim     | sim     |                                                                                                                            |
| `LineStyle`          | sim     | sim     | `colorMode` não é apoiado.                                                                                         |
| `Link`               | sim     | não      | Apenas a propriedade `href` é suportada para links de rede.                                                                   |
| `MultiGeometry`      | parcial | parcial | Pode ser dividido em características individuais quando lido.                                                                     |
| `name`               | sim     | sim     |                                                                                                                            |
| `NetworkLink`        | sim     | não      | Os links têm de estar no mesmo domínio que o documento.                                                                  |
| `NetworkLinkControl` | não      | não      |                                                                                                                            |
| `north`              | sim     | sim     |                                                                                                                            |
| `open`               | sim     | sim     |                                                                                                                            |
| `outerBoundaryIs`    | sim     | sim     |                                                                                                                            |
| `outline`            | sim     | sim     |                                                                                                                            |
| `overlayXY`          | não      | não      |                                                                                                                            |
| `Pair`               | parcial | não      | Apenas o estilo `normal` numa `StyleMap` é apoiado. `highlight` não é apoiado.                                   |
| `phoneNumber`        | sim     | sim     |                                                                                                                            |
| `PhotoOverlay`       | não      | não      |                                                                                                                            |
| `Placemark`          | sim     | sim     |                                                                                                                            |
| `Point`              | sim     | sim     |                                                                                                                            |
| `Polygon`            | sim     | sim     |                                                                                                                            |
| `PolyStyle`          | sim     | sim     |                                                                                                                            |
| `Region`             | parcial | parcial | `LatLongBox` é suportado a nível documental.                                                                      |
| `rotation`           | não      | não      |                                                                                                                            |
| `rotationXY`         | não      | não      |                                                                                                                            |
| `scale`              | não      | não      |                                                                                                                            |
| `Schema`             | sim     | sim     |                                                                                                                            |
| `SchemaData`         | sim     | sim     |                                                                                                                            |
| `schemaUrl`          | parcial | sim     | Não suporta estilos de carregamento de documentos externos que não estão incluídos num KMZ.                             |
| `ScreenOverlay`      | não      | não      |                                                                                                                            |
| `screenXY`           | não      | não      |                                                                                                                            |
| `SimpleData`         | sim     | sim     |                                                                                                                            |
| `SimpleField`        | sim     | sim     |                                                                                                                            |
| `size`               | não      | não      |                                                                                                                            |
| `Snippet`            | parcial | parcial | `maxLines` atributo é ignorado.                                                                                  |
| `south`              | sim     | sim     |                                                                                                                            |
| `Style`              | sim     | sim     |                                                                                                                            |
| `StyleMap`           | parcial | não      | Apenas o estilo normal de um `StyleMap` é suportado.                                                                        |
| `styleUrl`           | parcial | sim     | URLs de estilo externo não são suportados.                                                                         |
| `text`               | sim     | sim     | Substituição de `$[geDirections]` não é suportada                                                                          |
| `textColor`          | sim     | sim     |                                                                                                                            |
| `TimeSpan`           | sim     | sim     |                                                                                                                            |
| `TimeStamp`          | sim     | sim     |                                                                                                                            |
| `value`              | sim     | sim     |                                                                                                                            |
| `viewRefreshMode`    | parcial | não      |  Se apontar para um serviço WMS, então apenas `onStop` é suportado para sobreposições terrestres. Anexará `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` ao URL e atualizará à medida que o mapa se move.  |
| `visibility`         | sim     | sim     |                                                                                                                            |
| `west`               | sim     | sim     |                                                                                                                            |
| `when`               | sim     | sim     |                                                                                                                            |
| `width`              | sim     | sim     |                                                                                                                            |

### <a name="georss-elements"></a>Elementos GeoRSS

O módulo IO espacial suporta os seguintes elementos GeoRSS.

| Nome do elemento             | Leitura    | Escrita | Notas                                                                                          |
|--------------------------|---------|-------|------------------------------------------------------------------------------------------------|
| `atom:author`            | sim     | sim   |                                                                                                |
| `atom:category`          | sim     | sim   |                                                                                                |
| `atom:content`           | sim     | sim   |                                                                                                |
| `atom:contributor`       | sim     | sim   |                                                                                                |
| `atom:email`             | sim     | sim   |                                                                                                |
| `atom:entry`             | sim     | sim   |                                                                                                |
| `atom:feed`              | sim     | sim   |                                                                                                |
| `atom:icon`              | sim     | sim   |                                                                                                |
| `atom:id`                | sim     | sim   |                                                                                                |
| `atom:link`              | sim     | sim   |                                                                                                |
| `atom:logo`              | sim     | sim   |                                                                                                |
| `atom:name`              | sim     | sim   |                                                                                                |
| `atom:published`         | sim     | sim   |                                                                                                |
| `atom:rights`            | sim     | sim   |                                                                                                |
| `atom:source`            | sim     | sim   |                                                                                                |
| `atom:subtitle`          | sim     | sim   |                                                                                                |
| `atom:summary`           | sim     | sim   |                                                                                                |
| `atom:title`             | sim     | sim   |                                                                                                |
| `atom:updated`           | sim     | sim   |                                                                                                |
| `atom:uri`               | sim     | sim   |                                                                                                |
| `geo:lat`                | sim     | não    | Escrito como um `georss:point`.                                                                   |
| `geo:lon`                | sim     | não    | Escrito como um `georss:point`.                                                                   |
| `geo:long`               | sim     | não    | Escrito como um `georss:point`.                                                                   |
| `georss:box`             | sim     | não    | Ler como um polígono e dado um `subType` propriedade de "Retangle"                                |
| `georss:circle`          | sim     | sim   |                                                                                                |
| `georss:elev`            | sim     | sim   |                                                                                                |
| `georss:featurename`     | sim     | sim   |                                                                                                |
| `georss:featuretypetag`  | sim     | sim   |                                                                                                |
| `georss:floor`           | sim     | sim   |                                                                                                |
| `georss:line`            | sim     | sim   |                                                                                                |
| `georss:point`           | sim     | sim   |                                                                                                |
| `georss:polygon`         | sim     | sim   |                                                                                                |
| `georss:radius`          | sim     | sim   |                                                                                                |
| `georss:relationshiptag` | sim     | sim   |                                                                                                |
| `georss:where`           | sim     | sim   |                                                                                                |
| `geourl:latitude`        | sim     | não    | Escrito como um `georss:point`.                                                                   |
| `geourl:longitude`       | sim     | não    | Escrito como um `georss:point`.                                                                   |
| `position`               | sim     | não    | Alguns feeds XML embrulharão a GML com uma etiqueta de posição em vez de embrulhá-la com uma georss:onde etiqueta. Lerá esta etiqueta, mas escreverá usando uma georss:onde etiqueta. |
| `rss`                    | sim     | não    | GeoRSS escrito em formato ATOM.                                                                 |
| `rss:author`             | sim     | parcial | Escrito como um `atom:author`.                                                                 |
| `rss:category`           | sim     | parcial | Escrito como um `atom:category`.                                                               |
| `rss:channel`            | sim     | não    |                                                                                                |
| `rss:cloud`              | sim     | não    |                                                                                                |
| `rss:comments`           | sim     | não    |                                                                                                |
| `rss:copyright`          | sim     | parcial | Escrito como um `atom:rights` se a forma já não tem uma propriedade `rights` `properties`.       |
| `rss:description`        | sim     | parcial | Escrito como um `atom:content` se a forma já não tem uma propriedade `content` `properties`.      |
| `rss:docs`               | sim     | não    |                                                                                                |
| `rss:enclosure`          | sim     | não    |                                                                                                |
| `rss:generator`          | sim     | não    |                                                                                                |
| `rss:guid`               | sim     | parcial | Escrito como um `atom:id` se a forma já não tem uma propriedade `id` `properties`.         |
| `rss:image`              | sim     | parcial | Escrito como um `atom:logo` se a forma já não tem uma propriedade `logo` `properties`.      |
| `rss:item`               | sim     | parcial | Escrito como um `atom:entry`.                                                                  |
| `rss:language`           | sim     | não    |                                                                                                |
| `rss:lastBuildDate`      | sim     | parcial | Escrito como um `atom:updated` se a forma já não tem uma propriedade `updated` `properties`.     |
| `rss:link`               | sim     | parcial | Escrito como um `atom:link`.                                                                   |
| `rss:managingEditor`     | sim     | parcial | Escrito como um `atom:contributor`.                                                            |
| `rss:pubDate`            | sim     | parcial | Escrito como um `atom:published` se a forma já não tem uma propriedade `published` `properties`.  |
| `rss:rating`             | sim     | não    |                                                                                                |
| `rss:skipDays`           | sim     | não    |                                                                                                |
| `rss:skipHours`          | sim     | não    |                                                                                                |
| `rss:source`             | sim     | parcial | Escrito como um `atom:source` contendo um `atom:link`.                                       |
| `rss:textInput`          | sim     | não    |                                                                                                |
| `rss:title`              | sim     | parcial | Escrito como um `atom:title`.                                                                  |
| `rss:ttl`                | sim     | não    |                                                                                                |
| `rss:webMaster`          | sim     | não    |                                                                                                |

### <a name="gml-elements"></a>Elementos GML

O módulo IO espacial suporta os seguintes elementos GML. 

| Nome do elemento            | Leitura | Escrita | Notas                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | sim  | não    | Escrito como `gml:posList`.                                                              |
| `gml:curveMember`       | sim  | não    |                                                                                        |
| `gml:curveMembers`      | sim  | não    |                                                                                        |
| `gml:Box`               | sim  | não    | Escrito como `gml:Envelope`.                                                             |
| `gml:description`       | sim  | sim   |                                                                                        |
| `gml:Envelope`          | sim  | sim   |                                                                                        |
| `gml:exterior`          | sim  | sim   |                                                                                        |
| `gml:Feature`           | sim  | não    | Escrito como uma forma.                                                                    |
| `gml:FeatureCollection` | sim  | não    | Escrito como uma coleção de geometria.                                                      |
| `gml:featureMember`     | sim  | não    | Escrito como uma coleção de geometria.                                                      |
| `gml:geometry`          | sim  | não    | Escrito como uma forma.                                                                    |
| `gml:geometryMember`    | sim  | sim   |                                                                                        |
| `gml:geometryMembers`   | sim  | sim   |                                                                                        |
| `gml:identifier`        | sim  | sim   |                                                                                        |
| `gml:innerBoundaryIs`   | sim  | não    | Escrito com `gml.interior`.                                                          |
| `gml:interior`          | sim  | sim   |                                                                                        |
| `gml:LinearRing`        | sim  | sim   |                                                                                        |
| `gml:LineString`        | sim  | sim   |                                                                                        |
| `gml:lineStringMember`  | sim  | sim   |                                                                                        |
| `gml:lineStringMembers` | sim  | não    |                                                                                        |
| `gml:MultiCurve`        | sim  | não    | Só lê `gml:LineString` membros. Escrito como `gml.MultiLineString`                  |
| `gml:MultiGeometry`     | parcial  | parcial   | Apenas lido como FeatureCollection.                                              |
| `gml:MultiLineString`   | sim  | sim   |                                                                                        |
| `gml:MultiPoint`        | sim  | sim   |                                                                                        |
| `gml:MultiPolygon`      | sim  | sim   |                                                                                        |
| `gml:MultiSurface`      | sim  | não    | Só lê `gml:Polygon` membros. Escrito como `gml.MultiPolygon`                        |
| `gml:name`              | sim  | sim   |                                                                                        |
| `gml:outerBoundaryIs`   | sim  | não    | Escrito com `gml.exterior`.                                                          |
| `gml:Point`             | sim  | sim   |                                                                                        |
| `gml:pointMember`       | sim  | sim   |                                                                                        |
| `gml:pointMembers`      | sim  | não    |                                                                                        |
| `gml:Polygon`           | sim  | sim   |                                                                                        |
| `gml:polygonMember`     | sim  | sim   |                                                                                        |
| `gml:polygonMembers`    | sim  | não    |                                                                                        |
| `gml:pos`               | sim  | sim   |                                                                                        |
| `gml:posList`           | sim  | sim   |                                                                                        |
| `gml:surfaceMember`     | sim  | sim   |                                                                                        |

#### <a name="additional-notes"></a>notas adicionais

- Os elementos membros serão procurados por uma geometria que possa ser enterrada dentro de elementos infantis. Esta operação de pesquisa é necessária, pois muitos formatos XML que se estendem a partir de GML não podem colocar uma geometria como uma criança direta de um elemento membro.
- `srsName` é parcialmente suportado para as coordenadas WGS84 e os seguintes códigos:[EPSG:4326](https://epsg.io/4326)), e web Mercator[(EPSG:3857](https://epsg.io/3857) ou um dos seus códigos alternativos. Qualquer outro sistema de coordenadas será analisado como WGS84 como está.
- Salvo especificação ao ler um feed XML, a ordem do eixo é determinada com base em sugestões no feed XML. É dada uma preferência pela ordem do eixo "latitude, longitude".
- A menos que seja especificado um espaço de nome GML personalizado para as propriedades ao escrever para um ficheiro GML, não serão adicionadas informações adicionais sobre propriedades.

### <a name="gpx-elements"></a>Elementos GPX

O módulo IO espacial suporta os seguintes elementos GPX.

| Nome do elemento             | Leitura    | Escrita   | Notas                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | sim     | sim     |                                                                                             |
| `gpx:author`             | sim     | sim     |                                                                                             |
| `gpx:bounds`             | sim     | sim     | Convertido em Rect Local quando lido.                                                    |
| `gpx:cmt`                | sim     | sim     |                                                                                             |
| `gpx:copyright`          | sim     | sim     |                                                                                             |
| `gpx:desc`               | sim     | sim     | Copiado para uma propriedade de descrição quando lido para alinhar com outros formatos XML.               |
| `gpx:dgpsid`             | sim     | sim     |                                                                                             |
| `gpx:ele`                | sim     | sim     |                                                                                             |
| `gpx:extensions`         | parcial | parcial | Quando lido, a informação do estilo é extraída. Todas as outras extensões serão achatadas num simples objeto JSON. Apenas a informação do estilo de forma está escrita. |
| `gpx:geoidheight`        | sim     | sim     |                                                                                             |
| `gpx:gpx`                | sim     | sim     |                                                                                             |
| `gpx:hdop`               | sim     | sim     |                                                                                             |
| `gpx:link`               | sim     | sim     |                                                                                             |
| `gpx:magvar`             | sim     | sim     |                                                                                             |
| `gpx:metadata`           | sim     | sim     |                                                                                             |
| `gpx:name`               | sim     | sim     |                                                                                             |
| `gpx:pdop`               | sim     | sim     |                                                                                             |
| `gpx:rte`                | sim     | sim     |                                                                                             |
| `gpx:rtept`              | sim     | sim     |                                                                                             |
| `gpx:sat`                | sim     | sim     |                                                                                             |
| `gpx:src`                | sim     | sim     |                                                                                             |
| `gpx:sym`                | sim     | sim     | O valor é capturado, mas não é usado para alterar o ícone do pino.                               |
| `gpx:text`               | sim     | sim     |                                                                                             |
| `gpx:time`               | sim     | sim     |                                                                                             |
| `gpx:trk`                | sim     | sim     |                                                                                             |
| `gpx:trkpt`              | sim     | sim     |                                                                                             |
| `gpx:trkseg`             | sim     | sim     |                                                                                             |
| `gpx:type`               | sim     | sim     |                                                                                             |
| `gpx:vdop`               | sim     | sim     |                                                                                             |
| `gpx:wpt`                | sim     | sim     |                                                                                             |
| `gpx_style:color`        | sim     | sim     |                                                                                             |
| `gpx_style:line`         | parcial | parcial | `color`, `opacity`, `width`, `lineCap` são apoiados.                                           |
| `gpx_style:opacity`      | sim     | sim     |                                                                                             |
| `gpx_style:width`        | sim     | sim     |                                                                                             |
| `gpxx:DisplayColor`      | sim     | não      | Usado para especificar a cor de uma forma. Ao escrever, `gpx_style:line` cor será usada.  |
| `gpxx:RouteExtension`    | parcial | não      | Todas as propriedades são lidas em `properties`. Só `DisplayColor` é usado.                     |
| `gpxx:TrackExtension`    | parcial | não      | Todas as propriedades são lidas em `properties`. Só `DisplayColor` é usado.                     |
| `gpxx:WaypointExtension` | parcial | não      | Todas as propriedades são lidas em `properties`. Só `DisplayColor` é usado.                     |
| `gpx:keywords`           | sim     | sim     |                                                                                             |
| `gpx:fix`                | sim     | sim     |                                                                                             |

#### <a name="additional-notes"></a>notas adicionais

Ao escrever;

- Os MultiPoints serão divididos em pontos de passagem individuais.
- Polígonos e MultiPolgões serão escritos como faixas. 
  
## <a name="supported-well-known-text-geometry-types"></a>Tipos de geometria de texto bem conhecidos

| Tipo de geometria | Leitura | Escrita |
|--------------|:----:|:-----:|
| PONTO | x | x |
| PONTO Z | x | x | 
| PONTO M | x | x<sup>[2]</sup> |
| PONTO ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| CORDA DE LINHA | x | x |
| LINHACORD Z | x | x | 
| LINHACORD M | x | x<sup>[2]</sup> |
| LINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| POLÍGONO | x | x |
| POLYGON Z | x | x |
| POLYGON M | x | x<sup>[2]</sup> |
| POLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| PONTO MULTIPONTO | x | x |
| MULTIPOINT Z | x | x | 
| MULTIPOINT M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| CADEIA MULTILINE | x | x |
| MULTILINESTRING Z | x | x | 
| MULTILINESTRING M | x | x<sup>[2]</sup> |
| MULTILINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MultiPOLIGON | x | x |
| MULTIPOLYGON Z | x | x | 
| MULTIPOLYGON M | x | x<sup>[2]</sup> |
| MULTIPOLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| GEOMETRIACOLEÇÃO | x | x |
| GEOMETRIACOLEÇÃO Z | x | x | 
| GEOMETRIACOLEÇÃO M | x | x<sup>[2]</sup> | 
| GEOMETRYCOLLECTION ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1\] O único parâmetro Z é capturado e adicionado como um terceiro valor no valor da Posição.

\[parâmetro de 2\] M não foi capturado.

## <a name="delimited-spatial-data-support"></a>Suporte de dados espaciais delimitado

Dados espaciais delimitados, tais como ficheiros de valor separados pela vírlém (CSV), muitas vezes têm colunas que contêm dados espaciais. Por exemplo, pode haver colunas que contenham informação sobre latitude e longitude. No formato de Texto Bem Conhecido, pode haver uma coluna que contém dados de geometria espacial.

### <a name="spatial-data-column-detection"></a>Deteção de colunas de dados espaciais

Ao ler um ficheiro delimitado que contenha dados espaciais, o cabeçalho será analisado para determinar quais as colunas que contêm campos de localização. Se o cabeçalho contiver informações do tipo, será utilizado para lançar os valores celulares para o tipo apropriado. Se não for especificado um cabeçalho, a primeira linha será analisada e usada para gerar um cabeçalho. Ao analisar a primeira linha, é executado um cheque para combinar nomes de colunas com os seguintes nomes de uma forma insensível. A ordem dos nomes é a prioridade, no caso de existirem dois ou mais nomes num ficheiro.

#### <a name="latitude"></a>Latitude

- `latitude`
- `lat`
- `latdd`
- `lat_dd`
- `latitude83`
- `latdecdeg`
- `y`
- `ycenter`
- `point-y`

#### <a name="longitude"></a>Longitude

- `longitude`
- `lon`
- `lng`
- `long`
- `longdd`
- `long_dd`
- `longitude83`
- `longdecdeg`
- `x`
- `xcenter`
- `point-x`

#### <a name="elevation"></a>Elevação

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>Geografia

A primeira linha de dados será digitalizada para cordas que estão em formato de Texto bem conhecido. 

### <a name="delimited-data-column-types"></a>Tipos de colunas de dados delimitadas

Ao digitalizar a linha do cabeçalho, qualquer informação do tipo que esteja no nome da coluna será extraída e usada para lançar as células nessa coluna. Aqui está um exemplo de um nome de coluna que tem um valor de tipo: "Nome de coluna (nome de letra)". Os seguintes nomes de tipo de caso insensível são suportados:

#### <a name="numbers"></a>Números

- edm.int64
- int
- long
- edm.double
- float
- double
- número

#### <a name="booleans"></a>Booleans

- edm.boolean
- bool
- boolean

#### <a name="dates"></a>Datas

- edm.datetime
- date
- datetime

#### <a name="geography"></a>Geografia

- edm.geografia
- Geografia

#### <a name="strings"></a>Cadeias

- edm.string
- varchar
- texto
- caso 'corda

Se nenhuma informação do tipo puder ser extraída do cabeçalho, e a opção dinâmica de dactilografia estiver ativada durante a leitura, então cada célula será analisada individualmente para determinar qual o tipo de dados que é mais adequado para ser lançado como.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Ler e escrever dados espaciais](spatial-io-read-write-spatial-data.md)
