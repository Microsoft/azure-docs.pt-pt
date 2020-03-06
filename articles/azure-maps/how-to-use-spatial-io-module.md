---
title: Como utilizar o módulo IO espacial Do Azure Maps  Microsoft Azure Maps
description: Saiba como utilizar o módulo Espacial IO fornecido pelo Azure Maps Web SDK. Este módulo fornece funcionalidades robustas para facilitar a integração de dados espaciais com o Web SDK do Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: c309473529666d369e8accd1617021249867fb19
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78371041"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>Como utilizar o módulo Azure Maps Spatial IO

O Azure Maps Web SDK fornece o **módulo Espacial IO,** que integra dados espaciais com o Web SDK do Azure Maps utilizando javaScript ou TypeScript. As características robustas neste módulo permitem aos desenvolvedores:

- [Leia e escreva dados para ficheiros espaciais comuns](spatial-io-read-write-spatial-data.md). Os formatos de ficheiros suportados incluem: FICHEIROS KML, KMZ, GPX, GeoRSS, GML e CSV contendo colunas com informações espaciais.
- [Ligue-se aos serviços do Consórcio Geoespacial Aberto (OGC) e integre-se com a Web SDK do Azure Maps. Sobreposição dos Serviços de Mapeamento web (WMS) e dos Serviços de Azulejos do Mapa Web (WMTS) como camadas no mapa.](spatial-io-add-ogc-map-layer.md)
- Dados de consulta num Serviço de [Funcionalidadeweb (WFS)](spatial-io-connect-wfs-service.md).
- [Sobreponha conjuntos de dados complexos que contenham informações de estilo e os façam render automaticamente](spatial-io-add-simple-data-layer.md).
- Aproveite o XML de alta velocidade e as [aulas de leitores de ficheiros e escritores delimitados.](spatial-io-core-operations.md)

Neste guia, vamos aprender a integrar e utilizar o módulo Espacial IO numa aplicação web.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder utilizar o módulo Espacial IO, terá de [fazer uma conta Azure Maps](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#create-an-account-with-azure-maps) e obter a chave de [subscrição principal para a sua conta](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#get-the-primary-key-for-your-account).

## <a name="installing-the-spatial-io-module"></a>Instalação do módulo Espacial IO

Pode carregar o módulo IO espacial Do Azure Maps utilizando uma das duas opções:

* O CDN azure globalmente hospedado para o módulo IO espacial Azure Maps. Para esta opção, adicione uma referência ao JavaScript no elemento `<head>` do ficheiro HTML.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* O código fonte para [o azure-maps-spatial-io](https://www.npmjs.com/package/azure-maps-spatial-io) pode ser carregado localmente e, em seguida, hospedado com a sua app. Este pacote também inclui definições de TypeScript. Para esta opção, utilize o seguinte comando para instalar a embalagem:

    ```sh
    npm install azure-maps-spatial-io
    ```

    Em seguida, adicione uma referência ao JavaScript no elemento `<head>` do documento HTML:

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>Utilização do módulo Espacial IO

1. Crie um novo ficheiro HTML.

2. Carregue o Azure Maps Web SDK e inicialize o controlo do mapa. Consulte o guia de controlo do [mapa do Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) para obter os detalhes. Uma vez terminado este passo, o seu ficheiro HTML deve ser assim:

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

2. Carregue o módulo IO espacial Azure Maps. Para este exercício, utilize o CDN para o módulo IO espacial Do Azure Maps. Adicione a referência abaixo ao elemento `<head>` do seu ficheiro HTML:

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. Inicialize uma `datasource`e adicione a fonte de dados ao mapa. Inicialize um `layer`e adicione a fonte de dados à camada do mapa. Em seguida, renderizar tanto a fonte de dados como a camada. Antes de rolar para baixo para ver o código completo no próximo passo, pense nos melhores lugares para colocar a fonte de dados e os fragmentos de código de camada. Lembre-se que, antes de manipularmos programáticamente o mapa, devemos esperar até que o recurso do mapa esteja pronto.

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

5. Lembre-se de substituir `<Your Azure Maps Key>` com a sua chave principal. Abra o seu ficheiro HTML e verá resultados semelhantes à seguinte imagem:

    <center>

    ![Exemplo de Dados Espaciais](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>Passos seguintes

A funcionalidade que demonstrámos aqui é apenas uma das muitas funcionalidades disponíveis no módulo Espacial IO. Leia os guias abaixo para saber como utilizar outras funcionalidades no módulo IO Espacial:

> [!div class="nextstepaction"]
> [Adicione uma camada de dados simples](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [Ler e escrever dados espaciais](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Adicione uma camada de mapa OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Ligue-se a um serviço WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Alavancar operações de núcleo](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Detalhes do formato de dados suportados](spatial-io-supported-data-format-details.md)

Consulte a documentação Do IO espacial do Azure Maps:

> [!div class="nextstepaction"]
> [Pacote Azure Maps Spatial IO](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/)
