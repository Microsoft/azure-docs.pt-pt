---
title: Ler e escrever dados espaciais / Microsoft Azure Maps
description: Saiba ler e escrever dados utilizando o módulo Espacial IO, fornecido pelo Azure Maps Web SDK.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 458b307cf1158c467100e032e3f789462e8fdcca
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370911"
---
# <a name="read-and-write-spatial-data"></a>Ler e escrever dados espaciais

A tabela abaixo lista os formatos de ficheiros espaciais que são suportados para operações de leitura e escrita com o módulo Espacial IO.

| Formato de Dados       | Leitura | Escrita |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| Georss            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| GPX               | ✓  |  ✓  |
| KML               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| CSV espacial       | ✓  |  ✓  |
| Texto bem conhecido   | ✓  |  ✓  |

Estas secções seguintes delineiam todas as diferentes ferramentas para ler e escrever dados espaciais usando o módulo Espacial IO.

## <a name="read-spatial-data"></a>Ler dados espaciais

A função `atlas.io.read` é a principal função usada para ler formatos de dados espaciais comuns, tais como FICHEIROS KML, GPX, GeoRSS, GeoJSON e CSV com dados espaciais. Esta função também pode ler versões comprimidos destes formatos, como um ficheiro zip ou um ficheiro KMZ. O formato de ficheiro KMZ é uma versão comprimida do KML que também pode incluir ativos como imagens. Em alternativa, a função de leitura pode assumir um URL que aponta para um ficheiro em qualquer um destes formatos. Os URLs devem ser alojados num ponto final ativado por CORs, ou deve ser prestado um serviço de procuração nas opções de leitura. O serviço proxy é usado para carregar recursos em domínios que não estão ativados. A função de leitura devolve a promessa de adicionar os ícones de imagem ao mapa, e processa os dados de forma assíncrona para minimizar o impacto no fio UI.

Ao ler um ficheiro comprimido, seja como um fecho de correr ou um KMZ, este será desapertado e digitalizado para o primeiro ficheiro válido. Por exemplo, doc.kml, ou um ficheiro com outra extensão válida, tais como: .kml, .xml, geojson, .json, .csv, .tsv ou .txt. Em seguida, as imagens referenciadas nos ficheiros KML e GeoRSS estão pré-carregadas para garantir que estão acessíveis. Os dados de imagem inacessíveis podem carregar uma imagem alternativa de recuo ou serão removidos dos estilos. As imagens extraídas de ficheiros KMZ serão convertidas em URIs de dados.

O resultado da função de leitura é um objeto `SpatialDataSet`. Este objeto estende a classe GeoJSON FeatureCollection. Pode facilmente ser passado para um `DataSource` como é para tornar as suas características num mapa. O `SpatialDataSet` não só contém informações sobre funcionalidades, como também pode incluir sobreposições de solo KML, métricas de processamento e outros detalhes, conforme descrito na tabela seguinte.

| Nome da propriedade | Tipo | Descrição | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Caixa de delimitação de todos os dados do conjunto de dados. |
| `features` | `Feature[]` | GeoJSON apresenta-se dentro do conjunto de dados. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | Uma variedade de KmL GroundOverlays. |
| `icons` | Gravar&lt;corda,&gt; de cordas | Um conjunto de URLs de ícones. Chave = nome do ícone, Valor = URL. |
| propriedades | qualquer | Informações de propriedade fornecidas ao nível do documento de um conjunto de dados espaciais. |
| `stats` | `SpatialDataSetStats` | Estatísticas sobre o tempo de conteúdo e processamento de um conjunto de dados espaciais. |
| `type` | `'FeatureCollection'` | Leia apenas o valor do tipo GeoJSON. |

## <a name="examples-of-reading-spatial-data"></a>Exemplos de leitura de dados espaciais

O código seguinte mostra como ler um simples conjunto de dados espaciais, e torná-lo no mapa usando a classe `SimpleDataLayer`. O código usa um ficheiro GPX apontado por um URL.

<br/>

<iframe height='500' scrolling='no' title='Carregar dados espaciais simples' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte os <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>dados espaciais de carga</a> da caneta Simples por Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

A próxima demonstração de código mostra como ler e carregar KML, ou KMZ, para o mapa. A KML pode conter sobreposições terrestres, que serão sob a forma de uma `ImageLyaer` ou `OgcMapLayer`. Estas sobreposições devem ser adicionadas no mapa separadamente das características. Além disso, se o conjunto de dados tiver ícones personalizados, esses ícones precisam de ser carregados para os recursos dos mapas antes de as funcionalidades serem carregadas.

<br/>

<iframe height='500' scrolling='no' title='Carregar KML no mapa' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>Pen Load KML onto Map</a> by Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

Pode facultar opcionalmente um serviço de procuração para aceder a ativos de domínio cruzado que podem não ter CORs habilitados. Este fragmento de código mostra que você poderia fornecer um serviço de procuração:

```javascript

//Set the location of your proxyServiceUrl file 
var proxyServiceUrl = window.location.origin + '/CorsEnabledProxyService.ashx?url=';

//Read a KML file from a URL or pass in a raw KML string.
atlas.io.read('https://s3-us-west-2.amazonaws.com/s.cdpn.io/1717245/2007SanDiegoCountyFires.kml', {
    //Provide a proxy service
    proxyService: proxyServiceUrl
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

A última demonstração abaixo mostra como ler um ficheiro delimitado e torná-lo no mapa. Neste caso, o código utiliza um ficheiro CSV que tem colunas de dados espaciais.

<br/>

<iframe height='500' scrolling='no' title='Adicione um Ficheiro Delimitado' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>Adicionar um Ficheiro Delimitado</a> por Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="write-spatial-data"></a>Escrever dados espaciais

Existem duas funções principais de escrita no módulo IO espacial. A função `atlas.io.write` gera uma cadeia, enquanto a função `atlas.io.writeCompressed` gera um ficheiro zip comprimido. O ficheiro zip comprimido conteria um ficheiro baseado em texto com os dados espaciais nele. Ambas as funções devolvem a promessa de adicionar os dados ao ficheiro. E ambos podem escrever qualquer um dos seguintes dados: `SpatialDataSet`, `DataSource`, `ImageLayer`, `OgcMapLayer`, recolha de recursos, funcionalidade, geometria ou um conjunto de qualquer combinação destes tipos de dados. Ao escrever usando qualquer uma das funções, pode especificar o formato de ficheiro procurado. Se o formato de ficheiro não for especificado, os dados serão escritos como KML.

A ferramenta abaixo demonstra a maioria das opções de escrita que podem ser usadas com a função `atlas.io.write`.

<br/>

<iframe height='700' scrolling='no' title='Opções de escrita de dados espaciais' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>opções</a> de escrita de dados pen spatial por Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="example-of-writing-spatial-data"></a>Exemplo de escrita de dados espaciais

A amostra seguinte permite arrastar e largar e, em seguida, carregar ficheiros espaciais no mapa. Pode exportar dados da GeoJSON a partir do mapa e escrevê-lo num dos formatos de dados espaciais suportados como uma cadeia ou como um ficheiro comprimido.

<br/>

<iframe height='700' scrolling='no' title='Arraste e deixe cair ficheiros espaciais no mapa' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Pen <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>Drag e deixe cair ficheiros espaciais no mapa</a> por Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

Pode facultar opcionalmente um serviço de procuração para aceder a ativos de domínio cruzado que podem não ter CORs habilitados. Este fragmento de código mostra que pode incorporar um serviço de procuração:

```javascript

//Set the location of your proxyServiceUrl file 
var proxyServiceUrl = window.location.origin + '/CorsEnabledProxyService.ashx?url=';

function readData(data, fileName) {
    loadingIcon.style.display = '';
    fileCount++;
    //Attempt to parse the file and add the shapes to the map.
    atlas.io.read(data, {
        //Provide a proxy service
        proxyService: proxyServiceUrl
    }).then(
        //Success
        function(r) {
            //some code goes here ...
        }
    );
}
```

## <a name="read-and-write-well-known-text-wkt"></a>Ler e escrever Texto Bem Conhecido (WKT)

[O Texto Bem Conhecido](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (WKT) é um padrão do Consórcio Geoespacial Aberto (OGC) para representar geometrias espaciais como texto. Muitos sistemas geoespaciais suportam o WKT, como o Azure SQL e o Azure PostgreSQL utilizando o plugin PostGIS. Como a maioria dos padrões da OGC, as coordenadas são formatadas como "latitude longitude" para alinhar com a convenção "x y". Como exemplo, um ponto de longitude -110 e latitude 45 pode ser escrito como `POINT(-110 45)` usando o formato WKT.

Texto bem conhecido pode ser lido usando a função `atlas.io.ogc.WKT.read`, e escrito usando a função `atlas.io.ogc.WKT.write`.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Exemplos de leitura e escrita de texto bem conhecido (WKT)

O código que se segue mostra como ler a conhecida cadeia de texto `POINT(-122.34009 47.60995)` e torná-la no mapa usando uma camada de bolha.

<br/>

<iframe height='500' scrolling='no' title='Ler texto bem conhecido' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>Read Bem Conhecida Texto</a> por Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

O código que se segue demonstra a leitura e a escrita de texto sonorizador estonado para trás e para a frente.

<br/>

<iframe height='700' scrolling='no' title='Ler e escrever Texto Bem Conhecido' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>Leia e escreva Texto Bem Conhecido</a> por Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="read-and-write-gml"></a>Ler e escrever GML

GML é uma especificação de ficheiro XML espacial que é frequentemente usada como uma extensão a outras especificações XML. Os dados da GeoJSON podem ser escritos como XML com etiquetas GML utilizando a função `atlas.io.core.GmlWriter.write`. O XML que contém GML pode ser lido utilizando a função `atlas.io.core.GmlReader.read`. A função de leitura tem duas opções:

- A opção `isAxisOrderLonLat` - A ordem do eixo das coordenadas "latitude, longitude" ou "longitude, latitude" pode variar entre conjuntos de dados, e nem sempre está bem definida. Por defeito, o leitor da GML lê os dados da coordenada como "latitude, longitude", mas definindo esta opção como verdadeira irá lê-lo como "longitude, latitude".
- A opção `propertyTypes` - Esta opção é uma tabela de pesquisa de valor chave onde a chave é o nome de uma propriedade no conjunto de dados. O valor é o tipo de objeto para lançar o valor para quando analisar. Os valores de tipo suportados são: `string`, `number`, `boolean`e `date`. Se uma propriedade não estiver na mesa de olhar para cima ou o tipo não estiver definido, a propriedade será analisada como uma corda.

A função `atlas.io.read` será indefinida na função `atlas.io.core.GmlReader.read` quando detetar que os dados de entrada são XML, mas os dados não são um dos outros formatos xml espaciais de suporte.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [atlas.io funções estáticas](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

> [!div class="nextstepaction"]
> [Conjunto de Datatos Espaciais](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

> [!div class="nextstepaction"]
> [Estetátetos de Datatos Espaciais](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

> [!div class="nextstepaction"]
> [Leitor GmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [GmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [funções atlas.io.ogc.WKT](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicione uma camada de mapa OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Ligue-se a um serviço WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Alavancar operações de núcleo](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Detalhes do formato de dados suportados](spatial-io-supported-data-format-details.md)
