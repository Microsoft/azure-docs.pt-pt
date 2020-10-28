---
title: Como utilizar o módulo IO espacial Azure Maps Microsoft Azure Maps
description: Saiba como utilizar o módulo Spatial IO fornecido pelo Azure Maps Web SDK. Este módulo fornece funcionalidades robustas para facilitar aos desenvolvedores a integração de dados espaciais com o SdK web Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/28/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: e64df21b31d686b72b210d2c62956745350891f5
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895397"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>Como utilizar o módulo Azure Maps Spatial IO

O Azure Maps Web SDK fornece o **módulo IO espacial,** que integra dados espaciais com o SDK web Azure Maps utilizando JavaScript ou TypeScript. As funcionalidades robustas deste módulo permitem aos desenvolvedores:

- [Leia e escreva ficheiros de dados espaciais comuns.](spatial-io-read-write-spatial-data.md) Os formatos de ficheiros suportados incluem: KML, KMZ, GPX, GeoRSS, GML, GeoJSON e CSV que contêm colunas com informações espaciais. Também suporta Well-Known Texto (WKT).
- [Conecte-se aos serviços do Open Geospatial Consortium (OGC) e integre-se com a Azure Maps web SDK. Sobreponha os serviços de mapa web (WMS) e os serviços de azulejos do mapa web (WMTS) como camadas no mapa.](spatial-io-add-ogc-map-layer.md)
- [Dados de consulta num Serviço de Recursos Web (WFS)](spatial-io-connect-wfs-service.md).
- [Sobreponha conjuntos de dados complexos que contenham informações de estilo e os tornem automaticamente usando código mínimo](spatial-io-add-simple-data-layer.md).
- [Aproveite as aulas de XML de alta velocidade e delimited e deslimited.](spatial-io-core-operations.md)

Neste guia, aprenderemos a integrar e utilizar o módulo IO Espacial numa aplicação web.

Este vídeo fornece uma visão geral do módulo IO espacial no Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Easily-integrate-spatial-data-into-the-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0" title="Integrar facilmente dados espaciais no Azure Maps - Microsoft Channel 9 Video"></iframe>


> [!WARNING]
> Utilize apenas dados e serviços que sejam de uma fonte em que confie, especialmente se os referenciar de outro domínio. O módulo IO espacial toma medidas para minimizar o risco, no entanto a abordagem mais segura não permite que quaisquer dados danageros na sua aplicação comecem por começar. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder utilizar o módulo IO Espacial, terá de [fazer uma conta Azure Maps](./quick-demo-map-app.md#create-an-azure-maps-account) e obter a chave de [subscrição primária para a sua conta.](./quick-demo-map-app.md#get-the-primary-key-for-your-account)

## <a name="installing-the-spatial-io-module"></a>Instalação do módulo IO espacial

Pode carregar o módulo IO espacial Azure Maps utilizando uma das duas opções:

* O Azure CDN, apresentado globalmente para o módulo IO espacial Azure Maps. Para esta opção, adicione uma referência ao JavaScript no `<head>` elemento do ficheiro HTML.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* O código fonte para [azure-maps-spatial-io](https://www.npmjs.com/package/azure-maps-spatial-io) pode ser carregado localmente e, em seguida, hospedado com a sua app. Este pacote também inclui definições typeScript. Para esta opção, utilize o seguinte comando para instalar a embalagem:

    ```sh
    npm install azure-maps-spatial-io
    ```

    Em seguida, adicione uma referência ao JavaScript no `<head>` elemento do documento HTML:

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>Utilizando o módulo IO espacial

1. Crie um novo ficheiro HTML.

2. Carregue o Azure Maps Web SDK e inicialize o controlo do mapa. Consulte o guia de controlo do [mapa Azure Maps](./how-to-use-map-control.md) para obter os detalhes. Uma vez terminado este passo, o seu ficheiro HTML deve ficar assim:

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <script type='text/javascript'>

            var map;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    // Write your code here to make sure it runs once the map resources are ready

                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

2. Carregue o módulo IO espacial Azure Maps. Para este exercício, utilize o CDN para o módulo IO espacial Azure Maps. Adicione a referência abaixo ao `<head>` elemento do seu ficheiro HTML:

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. Inicialize um `datasource` , e adicione a fonte de dados ao mapa. Inicialize um `layer` , e adicione a fonte de dados à camada do mapa. Em seguida, torne a fonte de dados e a camada. Antes de deslocar para baixo para ver o código completo no passo seguinte, pense nos melhores lugares para colocar a fonte de dados e os cortes de código de camada. Lembre-se que, antes de manipularmos programáticamente o mapa, devemos esperar até que o recurso do mapa esteja pronto.

    ```javascript
    var datasource, layer;
    ```

    e

    ```javascript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Add a simple data layer for rendering the data.
    layer = new atlas.layer.SimpleDataLayer(datasource);
    map.layers.add(layer);
    ```

4. Juntando tudo, o seu código HTML deve parecer o seguinte código. Esta amostra demonstra como ler um ficheiro XML a partir de um URL. Em seguida, carregue e exiba os dados de funcionalidade do ficheiro no mapa. 

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title>Spatial IO Module Example</title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <!-- Add reference to the Azure Maps Spatial IO module. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

        <script type='text/javascript'>
            var map, datasource, layer;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Add a simple data layer for rendering the data.
                    layer = new atlas.layer.SimpleDataLayer(datasource);
                    map.layers.add(layer);

                    //Read an XML file from a URL or pass in a raw XML string.
                    atlas.io.read('superCoolKmlFile.xml').then(r => {
                        if (r) {
                            //Add the feature data to the data source.
                            datasource.add(r);

                            //If bounding box information is known for data, set the map view to it.
                            if (r.bbox) {
                                map.setCamera({
                                    bounds: r.bbox,
                                    padding: 50
                                });
                            }
                        }
                    });
                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

5. Lembre-se de substituir `<Your Azure Maps Key>` pela sua chave primária. Abra o seu ficheiro HTML e verá resultados semelhantes à seguinte imagem:

    <center>

    ![Exemplo de dados espaciais](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>Passos seguintes

A funcionalidade que aqui demonstramos é apenas uma das muitas funcionalidades disponíveis no módulo IO Espacial. Leia os guias abaixo para saber como utilizar outras funcionalidades no módulo IO espacial:

> [!div class="nextstepaction"]
> [Adicionar uma camada de dados simples](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [Ler e escrever dados espaciais](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de mapa OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Ligar a um serviço WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Tirar partido de operações principais](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Detalhes do formato de dados suportado](spatial-io-supported-data-format-details.md)

Consulte a documentação do Azure Maps Spatial IO:

> [!div class="nextstepaction"]
> [Pacote Azure Maps Spatial IO](/javascript/api/azure-maps-spatial-io/)