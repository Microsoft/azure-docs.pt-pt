---
title: 'Tutorial: Encontre caminho para um local / Microsoft Azure Maps'
description: Tutorial sobre como encontrar uma rota para um ponto de interesse. Veja como definir coordenadas de endereço e consultar o serviço de Rota Azure Maps para as direções ao ponto.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 3cb9bee65ab7fa2c29185c40ecb48fd531192187
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91321718"
---
# <a name="tutorial-how-to-display-route-directions-using-azure-maps-route-service-and-map-control"></a>Tutorial: Como exibir direções de rota usando o serviço de rota Azure Maps e o controlo do mapa

Este tutorial mostra-lhe como usar o [serviço AZure](https://docs.microsoft.com/rest/api/maps/route) Maps Route API e [o controlo do mapa](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) para exibir as direções da rota do início ao ponto final. Neste tutorial, irá aprender a:

> [!div class="checklist"]
> * Crie e exiba o controlo do Mapa numa página web. 
> * Defina a renderização do visor da rota definindo [camadas de símbolo](map-add-pin.md) e [camadas de linha](map-add-line-layer.md).
> * Crie e adicione objetos GeoJSON ao Mapa para representar pontos de partida e de fim.
> * Obtenha direções de rota de início e de ponta utilizando a [API de direções Get Route](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

Pode obter o código fonte completo para a amostra [aqui.](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html) Uma amostra viva pode ser encontrada [aqui.](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

## <a name="prerequisites"></a>Pré-requisitos

1. [Faça uma conta Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição.

<a id="getcoordinates"></a>

## <a name="create-and-display-the-map-control"></a>Criar e exibir o controlo do Mapa

Os passos a seguir mostram-lhe como criar e exibir o controlo do Mapa numa página web.

1. No seu computador local, crie um novo ficheiro e dê a este o nome de **MapRoute.html**.
2. Copiar/colar a seguinte marcação HTML no ficheiro.

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

    O cabeçalho HTML inclui os ficheiros de recursos CSS e JavaScript alojados pela biblioteca Azure Map Control. O evento do corpo `onload` chama a `GetMap` função. No próximo passo, adicionaremos o código de inicialização do controlo do mapa.

3. Adicione o seguinte código JavaScript à função `GetMap`. Substitua a cadeia `<Your Azure Maps Key>` pela chave primária que copiou da sua conta Maps.

    ```JavaScript
   //Instantiate a map object
   var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
           authType: 'subscriptionKey',
           subscriptionKey: '<Your Azure Maps Key>'
        }
   });
   ```

4. Guarde o ficheiro e abra-o no browser. Um simples é exibido.

     :::image type="content" source="./media/tutorial-route-location/basic-map.png" alt-text="Renderização de mapas básicos do controlo do mapa":::

## <a name="define-route-display-rendering"></a>Definir renderização de ecrã de rota

Neste tutorial, faremos a rota usando uma camada de linha. Os pontos de partida e de fim serão renderizados com uma camada de símbolo. Para obter mais informações sobre a adição de camadas de linha, consulte [Adicione uma camada de linha a um mapa](map-add-line-layer.md). Para saber mais sobre as camadas de símbolos, consulte [Adicione uma camada de símbolo a um mapa.](map-add-pin.md)

1. Anexar o seguinte código JavaScript na `GetMap` função. Este código implementa o manipulador de eventos do controlo do `ready` mapa. O resto do código neste tutorial será colocado dentro do `ready` manipulador do evento.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
            lineJoin: 'round',
            lineCap: 'round'
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
           },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
        }));
    });
    ```

    No manipulador de eventos do controlo do `ready` mapa, é criada uma fonte de dados para armazenar a rota do início ao ponto final. Para definir como a linha de rota será renderizada, uma camada de linha é criada e anexada à fonte de dados.  Para garantir que a linha de rota não cubra as etiquetas da estrada, passamos um segundo parâmetro com o valor de `'labels'` .

    Em seguida, uma camada de símbolo é criada e anexada à fonte de dados. Esta camada especifica como os pontos de partida e final são renderizados. Foram adicionadas expressões para recuperar a imagem do ícone e a informação do rótulo de texto a partir de propriedades em cada objeto de ponto. Para saber mais sobre expressões, consulte [expressões de estilo orientadas por Dados.](data-driven-style-expressions-web-sdk.md)

2. Estabeleça o ponto de partida como Microsoft, e o ponto final como posto de gasolina em Seattle.  No manipulador de eventos do controlo do `ready` mapa, apeia o seguinte código.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end points of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: "Redmond",
        icon: "pin-blue"
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: "Seattle",
        icon: "pin-round-blue"
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 80
    });
    ```

    Este código cria dois [objetos GeoJSON Point](https://en.wikipedia.org/wiki/GeoJSON) para representar pontos de partida e de fim, que são adicionados à fonte de dados. 

    O último bloco de código define a vista da câmara utilizando a latitude e longitude dos pontos de partida e de fim. Os pontos de início e de fim são adicionados à origem de dados. A caixa delimitadora para os pontos de início e de fim é calculada com a função `atlas.data.BoundingBox.fromData`. Esta caixa de delimitação é usada para definir a visualização das câmaras de mapa em toda a rota utilizando a `map.setCamera` função. O estofamento é adicionado para compensar as dimensões dos pixels dos ícones do símbolo. Para obter mais informações sobre a propriedade do setCamera do controlo do mapa, consulte [setCamera (CameraOptions ] CameraBoundsOptions & AnimationOptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-&preserve-view=false) propriedade.

3. Guarde **MapRoute.htmi** e refresque o seu navegador. O mapa está agora centrado em Seattle. O pino azul lágrima marca o ponto de partida. O pino azul redondo marca o ponto final.

    :::image type="content" source="./media/tutorial-route-location/map-pins.png" alt-text="Renderização de mapas básicos do controlo do mapa":::

<a id="getroute"></a>

## <a name="get-route-directions"></a>Obtenha direções de rota

Esta secção mostra-lhe como utilizar a API das Direções de Rota do Azure Maps para obter as direções da rota e a hora estimada de chegada de um ponto para outro.

>[!TIP]
>Os serviços da Rota Azure Maps oferecem APIs para planear rotas com base em diferentes tipos de rotas, tais como rotas *mais rápidas,* *mais curtas,* *eco,* ou rotas *emocionantes* baseadas na distância, condições de tráfego e modo de transporte utilizados. O serviço permite ainda aos utilizadores planearem futuras rotas com base em condições históricas de tráfego. Os utilizadores podem ver a previsão das durações da rota durante qualquer momento. Para mais informações, consulte [a API de direções da Rota.](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)

1. Na `GetMap` função, dentro do manipulador de eventos do `ready` controlo, adicione o seguinte ao código JavaScript.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential`Cria um para `SubscriptionKeyCredentialPolicy` autenticar pedidos HTTP ao Azure Maps com a chave de subscrição. A `atlas.service.MapsURL.newPipeline()` política toma conta da política e cria uma instância `SubscriptionKeyCredential` [pipeline.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline) O `routeURL` representa uma URL para as operações da Rota [dos](https://docs.microsoft.com/rest/api/maps/route) Mapas Azure.

2. Depois de configurar as credenciais e o URL, apeia o seguinte código no manipulador de eventos do `ready` comando. Este código constrói a rota do ponto de partida para o ponto final. Solicita `routeURL` a AZure Maps Route service API para calcular as direções da rota. Uma recolha de recursos GeoJSON da resposta é então extraída usando o `geojson.getFeatures()` método e adicionada à fonte de dados.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();
        datasource.add(data);
    });
    ```

3. Guarde o ficheiro **MapRoute.html** e atualize o browser. O mapa deve agora mostrar a rota do início ao ponto final.

     :::image type="content" source="./media/tutorial-route-location/map-route.png" alt-text="Renderização de mapas básicos do controlo do mapa":::

Pode obter o código fonte completo para a amostra [aqui.](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html) Uma amostra viva pode ser encontrada [aqui.](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

## <a name="next-steps"></a>Passos seguintes

O próximo tutorial mostra-lhe como criar uma consulta de rota com restrições, como modo de viagem ou tipo de carga. Em seguida, pode apresentar várias rotas no mesmo mapa.

> [!div class="nextstepaction"]
> [Localizar trajetos para diferentes meios de deslocação](./tutorial-prioritized-routes.md)