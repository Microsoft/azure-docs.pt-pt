---
title: Ler e escrever dados espaciais | Microsoft Azure Maps
description: Saiba ler e escrever dados utilizando o módulo Spatial IO, fornecido pela Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 133674e6764e12742f5b238946e943d9b5011cd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92891334"
---
# <a name="read-and-write-spatial-data"></a>Ler e escrever dados espaciais

A tabela abaixo lista os formatos de ficheiros espaciais suportados para operações de leitura e escrita com o módulo IO espacial.

| Formato de Dados       | Leitura | Escrita |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| GPX               | ✓  |  ✓  |
| KML               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| CSV espacial       | ✓  |  ✓  |
| texto de Well-Known   | ✓  |  ✓  |

Estas próximas secções delineiam todas as diferentes ferramentas para ler e escrever dados espaciais utilizando o módulo IO espacial.

## <a name="read-spatial-data"></a>Ler dados espaciais

A `atlas.io.read` função é a principal função usada para ler formatos comuns de dados espaciais tais como KML, GPX, GeoRSS, GeoJSON e CSV com dados espaciais. Esta função também pode ler versões comprimidas destes formatos, como um ficheiro zip ou um ficheiro KMZ. O formato de ficheiro KMZ é uma versão comprimido de KML que também pode incluir ativos como imagens. Em alternativa, a função de leitura pode ter um URL que aponta para um ficheiro em qualquer um destes formatos. Os URLs devem ser hospedados num ponto final ativado pelo CORS, ou deve ser prestado um serviço de procuração nas opções de leitura. O serviço proxy é usado para carregar recursos em domínios que não estão habilitados com CORS. A função de leitura retorna a promessa de adicionar os ícones de imagem ao mapa, e processa os dados assíncronamente para minimizar o impacto no fio de UI.

Ao ler um ficheiro comprimido, seja como um fecho ou um KMZ, será desapertado e digitalizado para o primeiro ficheiro válido. Por exemplo, doc.kml, ou um ficheiro com outra extensão válida, tais como: .kml, .xml, geojson, .json, .csv, .tsv ou .txt. Em seguida, as imagens referenciadas nos ficheiros KML e GeoRSS são pré-carregadas para garantir que estão acessíveis. Os dados de imagem inacessíveis podem carregar uma imagem alternativa de recuo ou serão removidos dos estilos. As imagens extraídas de ficheiros KMZ serão convertidas em URIs de dados.

O resultado da função de leitura é um `SpatialDataSet` objeto. Este objeto estende a classe GeoJSON FeatureCollection. Pode facilmente ser passado para um `DataSource` as-é para tornar as suas características num mapa. O `SpatialDataSet` não só contém informações sobre as características, como também pode incluir sobreposições de terra KML, métricas de processamento e outros detalhes, tal como descritos no quadro seguinte.

| Nome da propriedade | Tipo | Description | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Caixa de limitação de todos os dados do conjunto de dados. |
| `features` | `Feature[]` | GeoJSON possui dentro do conjunto de dados. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | Uma variedade de terralays KML. |
| `icons` | &lt;Corda de gravação, corda&gt; | Um conjunto de URLs de ícone. Tecla = nome do ícone, Valor = URL. |
| propriedades | qualquer | Informação de propriedade fornecida ao nível do documento de um conjunto de dados espaciais. |
| `stats` | `SpatialDataSetStats` | Estatísticas sobre o conteúdo e tempo de processamento de um conjunto de dados espaciais. |
| `type` | `'FeatureCollection'` | Valor do tipo GeoJSON apenas para leitura. |

## <a name="examples-of-reading-spatial-data"></a>Exemplos de leitura de dados espaciais

O código que se segue mostra como ler um conjunto de dados espaciais e torná-lo no mapa usando a `SimpleDataLayer` classe. O código utiliza um ficheiro GPX apontado por um URL.

<br/>

<iframe height='500' scrolling='no' title='Carregar dados espaciais simples' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte os <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>dados espaciais de carga de</a> caneta simples por Azure Maps ( ) no <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

A próxima demonstração de código mostra como ler e carregar KML, ou KMZ, para o mapa. A KML pode conter sobreposições terrestres, que serão na forma de um `ImageLyaer` ou `OgcMapLayer` . Estas sobreposições devem ser adicionadas no mapa separadamente das características. Além disso, se o conjunto de dados tiver ícones personalizados, esses ícones precisam de ser carregados para os recursos dos mapas antes de as funcionalidades serem carregadas.

<br/>

<iframe height='500' scrolling='no' title='Carregue KML no mapa' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>KmL de Carga</a> de Canetas no mapa por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

Pode opcionalmente fornecer um serviço de procuração para aceder a ativos de domínio cruzado que podem não ter CORS ativado. A função de leitura tentará aceder a ficheiros noutro domínio utilizando primeiro o CORS. Após a primeira vez que não consegue aceder a qualquer recurso noutro domínio utilizando o CORS, só solicitará ficheiros adicionais se um serviço de procuração tiver sido fornecido. A função de leitura anexa o URL do ficheiro ao fim do URL de procuração fornecido. Este corte de código mostra como passar um serviço de procuração para a função de leitura:

```javascript
//Read a file from a URL or pass in a raw data as a string.
atlas.io.read('https://nonCorsDomain.example.com/mySuperCoolData.xml', {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

A demonstração abaixo mostra como ler um ficheiro delimitado e torná-lo no mapa. Neste caso, o código utiliza um ficheiro CSV que tem colunas de dados espaciais.

<br/>

<iframe height='500' scrolling='no' title='Adicionar um ficheiro delimitado' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Pen <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>Adicione um ficheiro delimitado</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="write-spatial-data"></a>Escrever dados espaciais

Existem duas funções principais de escrita no módulo IO espacial. A `atlas.io.write` função gera uma corda, enquanto a `atlas.io.writeCompressed` função gera um ficheiro zip comprimido. O ficheiro zip comprimido conteria um ficheiro baseado em texto com os dados espaciais nele contidos. Ambas as funções devolvem a promessa de adicionar os dados ao ficheiro. E ambos podem escrever qualquer um dos seguintes dados: `SpatialDataSet` , , , , recolha de `DataSource` `ImageLayer` `OgcMapLayer` recursos, característica, geometria, ou uma variedade de qualquer combinação destes tipos de dados. Ao escrever usando ambas as funções, pode especificar o formato de ficheiro procurado. Se o formato do ficheiro não for especificado, os dados serão escritos como KML.

A ferramenta abaixo demonstra a maioria das opções de escrita que podem ser usadas com a `atlas.io.write` função.

<br/>

<iframe height='700' scrolling='no' title='Opções de escrita de dados espaciais' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>opções de escrita de dados do</a> Pen Spatial por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="example-of-writing-spatial-data"></a>Exemplo de escrita de dados espaciais

A amostra que se segue permite arrastar e largar e, em seguida, carregar ficheiros espaciais no mapa. Pode exportar dados da GeoJSON a partir do mapa e escrevê-lo num dos formatos de dados espaciais suportados como uma cadeia ou como um ficheiro comprimido.

<br/>

<iframe height='700' scrolling='no' title='Arraste e deixe cair ficheiros espaciais no mapa' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja o Pen <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>Drag e deixe cair ficheiros espaciais no mapa</a> por Azure Maps ( ) no <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

Pode opcionalmente fornecer um serviço de procuração para aceder a ativos de domínio cruzado que podem não ter CORS ativado. Este corte de código mostra que pode incorporar um serviço de procuração:

```javascript
atlas.io.read(data, {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(
    //Success
    function(r) {
        //some code goes here ...
    }
);
```

## <a name="read-and-write-well-known-text-wkt"></a>Ler e escrever Well-Known Texto (WKT)

[Bem Conhecido Texto](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (WKT) é um padrão de Consórcio Geoespacial Aberto (OGC) para representar geometrias espaciais como texto. Muitos sistemas geoespaciais suportam wKT, como Azure SQL e Azure PostgreSQL usando o plugin PostGIS. Como a maioria dos padrões de OGC, as coordenadas são formatadas como "latitude de longitude" para alinhar com a convenção "x y". Como exemplo, um ponto de longitude -110 e latitude 45 pode ser escrito como `POINT(-110 45)` usando o formato WKT.

Texto bem conhecido pode ser lido usando a `atlas.io.ogc.WKT.read` função, e escrito usando a `atlas.io.ogc.WKT.write` função.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Exemplos de leitura e escrita Well-Known Texto (WKT)

O código que se segue mostra como ler a conhecida cadeia de texto `POINT(-122.34009 47.60995)` e torná-la no mapa usando uma camada de bolha.

<br/>

<iframe height='500' scrolling='no' title='Ler Well-Known Texto' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Pen <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>Read Well-Known Text</a> by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

O código que se segue demonstra a leitura e a escrita de textos bem conhecidos para trás e para a frente.

<br/>

<iframe height='700' scrolling='no' title='Ler e escrever Well-Known Texto' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>Pen Read e escreva Well-Known Texto</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="read-and-write-gml"></a>Ler e escrever GML

GML é uma especificação de ficheiros XML espacial que é frequentemente usada como uma extensão a outras especificações de XML. Os dados da GeoJSON podem ser escritos como XML com tags GML utilizando a `atlas.io.core.GmlWriter.write` função. O XML que contém GML pode ser lido utilizando a `atlas.io.core.GmlReader.read` função. A função de leitura tem duas opções:

- A `isAxisOrderLonLat` opção - A ordem do eixo das coordenadas "latitude, longitude" ou "longitude, latitude" pode variar entre conjuntos de dados, e nem sempre está bem definida. Por predefinição, o leitor da GML lê os dados da coordenada como "latitude, longitude", mas definir esta opção para verdade irá lê-lo como "longitude, latitude".
- A `propertyTypes` opção - Esta opção é uma tabela de procura de valor chave onde a chave é o nome de uma propriedade no conjunto de dados. O valor é o tipo de objeto para lançar o valor para quando a análise. Os valores do tipo suportado são: `string` `number` , , e `boolean`  `date` . Se uma propriedade não estiver na mesa de pesquisa ou o tipo não estiver definido, a propriedade será analisada como uma corda.

A `atlas.io.read` função irá desreser o padrão da `atlas.io.core.GmlReader.read` função quando detetar que os dados de entrada são XML, mas os dados não são um dos outros formatos de suporte de XML espacial.

As coordenadas de análise de `GmlReader` testamento que têm um dos seguintes SRIDs:

- EPSG:4326 (Preferido)
- EPSG:4269, EPSG:4283, EPSG:4258, EPSG:4308, EPSG:4230, EPSG:4272, EPSG:4271, EPSG:4267, EPSG:4608, EPSG:4674 possivelmente com uma pequena margem de erro.
- EPSG:3857, EPSG:102100, EPSG:3785, EPSG:900913, EPSG:102113, EPSG:41001, EPSG:54004

## <a name="more-resources"></a>Mais recursos

Saiba mais sobre as aulas e métodos utilizados neste artigo:

[atlas.io funções estáticas](/javascript/api/azure-maps-spatial-io/atlas.io)

[Conjunto de Dados Espaciais](/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

[Estatísticas espaciais de DadosSetStats](/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

[GmlReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader)

[GmlWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter)

[funções atlas.io.ogc.WKT](/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

[Ligar a um serviço WFS](spatial-io-connect-wfs-service.md)

[Tirar partido de operações principais](spatial-io-core-operations.md)

[Detalhes do formato de dados suportado](spatial-io-supported-data-format-details.md)


## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

[Adicionar uma camada de mapa OGC](spatial-io-add-ogc-map-layer.md)