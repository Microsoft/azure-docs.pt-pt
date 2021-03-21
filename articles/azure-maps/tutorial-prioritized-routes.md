---
title: 'Tutorial: Encontre várias rotas por modo de viagem | Microsoft Azure Maps'
description: Tutorial sobre como usar o Azure Maps para encontrar rotas para modos de viagem específicos para pontos de interesse. Veja como exibir várias rotas nos mapas.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 5e7f38e4bbc95e04bf54f8f7a2dd80e21891e8df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102215498"
---
# <a name="tutorial-find-and-display-routes-for-different-modes-of-travel-using-azure-maps"></a>Tutorial: Encontrar e exibir rotas para diferentes modos de viagem usando mapas Azure

Este tutorial mostra-lhe como utilizar o serviço Azure Maps [Route](/rest/api/maps/route) e [o controlo do mapa](./how-to-use-map-control.md) para exibir direções de rota tanto para veículos particulares como para veículos comerciais (camiões) com tipo de `USHazmatClass2` carga. Além disso, vamos contar-lhe como visualizar dados de tráfego em tempo real num mapa. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar e exibir o controlo do Mapa numa página web
> * Renderizar dados de tráfego em tempo real num mapa
> * Solicitar e exibir rotas de veículos particulares e comerciais num mapa

## <a name="prerequisites"></a>Pré-requisitos

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. [Criar uma conta Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps.](how-to-manage-authentication.md)

Pode obter o código fonte completo para a amostra [aqui.](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html) Uma amostra viva pode ser encontrada [aqui.](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

## <a name="create-a-new-web-page-using-the-map-control-api"></a>Criar uma nova página Web com a API de Controlo de Mapas

Os passos a seguir mostram-lhe como criar e exibir o controlo do Mapa numa página web.

1. No seu computador local, crie um novo ficheiro e dê-lhe o nome **MapTruckRoute.html**.
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

    :::image type="content" source="./media/tutorial-prioritized-routes/basic-map.png" alt-text="Renderização de mapas básicos do controlo do mapa":::

## <a name="render-real-time-traffic-data-on-a-map"></a>Renderizar dados de tráfego em tempo real num mapa

1. Anexar o seguinte código JavaScript na `GetMap` função. Este código implementa o manipulador de eventos do controlo do `ready` mapa. O resto do código neste tutorial será colocado dentro do `ready` manipulador do evento.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    No manipulador de eventos do `ready` mapa, a definição do fluxo de tráfego no mapa está definida para `relative` , que é a velocidade da estrada em relação ao fluxo livre. Para mais opções de tráfego, consulte [a interface TrafficOptions](/javascript/api/azure-maps-control/atlas.trafficoptions).

2. Guarde o ficheiro **MapTruckRoute.html** e atualize a página no browser. Se você aproximar em qualquer cidade, como Los Angeles, você verá que as ruas exibem com os dados atuais do fluxo de tráfego.

    :::image type="content" source="./media/tutorial-prioritized-routes/traffic-map.png" alt-text="Ver tráfego em um mapa":::

<a id="queryroutes"></a>

## <a name="define-route-display-rendering"></a>Definir renderização de ecrã de rota

Neste tutorial, dois trajetos serão calculados e processados no mapa. A primeira rota será calculada para um veículo particular (carro). A segunda rota será calculada para que um veículo comercial (caminhão) demonstre a diferença entre os resultados. Quando renderizado, o mapa apresentará um ícone de símbolo para os pontos de partida e final da rota, e geometrias da linha de rota com cores diferentes para cada caminho de rota. Para obter mais informações sobre a adição de camadas de linha, consulte [Adicione uma camada de linha a um mapa](map-add-line-layer.md). Para saber mais sobre as camadas de símbolos, consulte [Adicione uma camada de símbolo a um mapa.](map-add-pin.md)

1. No manipulador de eventos do controlo do `ready` mapa, apeia o seguinte código.

    ```JavaScript

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Add a layer for rendering the route lines and have it render under the map labels.
    map.layers.add(new atlas.layer.LineLayer(datasource, null, {
        strokeColor: ['get', 'strokeColor'],
        strokeWidth: ['get', 'strokeWidth'],
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

    ```


    No manipulador de eventos do Controlo do `ready` Mapa, é criada uma fonte de dados para armazenar a rota do início ao fim. [As expressões](data-driven-style-expressions-web-sdk.md) são usadas para recuperar a largura e cor da linha de linha a partir de propriedades na linha de rota. Para garantir que a linha de rota não cubra as etiquetas da estrada, passamos um segundo parâmetro com o valor de `'labels'` .

    Em seguida, uma camada de símbolo é criada e anexada à fonte de dados. Esta camada especifica como os pontos de partida e final são renderizados. Foram adicionadas expressões para recuperar a imagem do ícone e a informação do rótulo de texto a partir de propriedades em cada objeto de ponto. Para saber mais sobre expressões, consulte [expressões de estilo orientadas por Dados.](data-driven-style-expressions-web-sdk.md)

2. Definir o ponto de partida como uma empresa fictícia em Seattle chamada Fabrikam, e o ponto final como um escritório da Microsoft.  No manipulador de eventos do controlo do `ready` mapa, apeia o seguinte código.


    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-round-blue'
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });

    ```

    Este código cria dois [objetos GeoJSON Point](https://en.wikipedia.org/wiki/GeoJSON) para representar pontos de partida e de fim, que são adicionados à fonte de dados.

    O último bloco de código define a vista da câmara utilizando a latitude e longitude dos pontos de partida e de fim. Os pontos de início e de fim são adicionados à origem de dados. A caixa delimitadora para os pontos de início e de fim é calculada com a função `atlas.data.BoundingBox.fromData`. Esta caixa de delimitação é usada para definir a visualização das câmaras de mapa em toda a rota utilizando a `map.setCamera` função. O estofamento é adicionado para compensar as dimensões dos pixels dos ícones do símbolo. Para obter mais informações sobre a propriedade do setCamera do controlo do mapa, consulte [setCamera (CameraOptions | CameraBoundsOptions & AnimationOptions)](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) propriedade.

3. Guarde **TruckRoute.htmi** e refresque o seu navegador. O mapa está agora centrado em Seattle. O pino azul lágrima marca o ponto de partida. O pino azul redondo marca o ponto final.

   :::image type="content" source="./media/tutorial-prioritized-routes/pins-map.png" alt-text="Ver mapa com os pontos de partida e de início":::

<a id="multipleroutes"></a>

## <a name="request-and-display-private-and-commercial-vehicle-routes-on-a-map"></a>Solicitar e exibir rotas de veículos particulares e comerciais num mapa

Esta secção mostra-lhe como utilizar o serviço Azure Maps Route para obter direções de um ponto para outro, com base no seu modo de transporte. Vamos usar dois modos de transporte: camião e carro.

>[!TIP]
>O serviço Route fornece APIs para planear rotas *mais rápidas,* *mais curtas,* *eco,* ou *emocionantes* com base na distância, condições de tráfego e modo de transporte utilizados. O serviço permite ainda aos utilizadores planearem futuras rotas com base em condições históricas de tráfego. Os utilizadores podem ver a previsão das durações da rota durante qualquer momento. Para mais informações, consulte [a API de direções da Rota.](/rest/api/maps/route/getroutedirections)

1. Na `GetMap` função, dentro do manipulador de eventos do `ready` controlo, adicione o seguinte ao código JavaScript.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential`Cria um para `SubscriptionKeyCredentialPolicy` autenticar pedidos HTTP ao Azure Maps com a chave de subscrição. A `atlas.service.MapsURL.newPipeline()` política toma conta da política e cria uma instância `SubscriptionKeyCredential` [pipeline.](/javascript/api/azure-maps-rest/atlas.service.pipeline) O `routeURL` representa uma URL para as operações da Rota [dos](/rest/api/maps/route) Mapas Azure.

2. Depois de configurar credenciais e o URL, adicione o seguinte código JavaScript para construir uma rota de rota do caminhão do início ao ponto final. Esta rota é criada e exibida para um caminhão que transporta `USHazmatClass2` carga classificada.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request for a truck vehicle type
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates,{
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```

    O código acima consulta o serviço Azure Maps Route através da API de [Rota de Mapas Azure.](/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-) A linha de rota é então extraída da coleção de recursos GeoJSON da resposta que é extraída usando o `geojson.getFeatures()` método. Finalmente, a linha de rota é adicionada à fonte de dados. Estamos a adicioná-lo no índice de 0, para garantir que a rota do caminhão seja feita antes de qualquer outra linha na fonte de dados, porque o cálculo da rota do caminhão será muitas vezes mais lento do que um cálculo da rota do carro. Se a linha de rota do caminhão for adicionada à fonte de dados após a rota do carro, ela renderá acima dela. Duas propriedades são adicionadas à linha da rota do caminhão: uma cor de traçado azul, e uma largura de curso de nove pixels.

    >[!TIP]
    > Para ver todas as opções e valores possíveis para a API das Direções de Rota do Azure Maps, consulte [parâmetros URI para Direções de Rota Pós-Rota.](/rest/api/maps/route/postroutedirections#uri-parameters)

3. Agora apecê-lo o seguinte código JavaScript para construir uma rota para um carro.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. This will add the car route after the truck route.  
        datasource.add(routeLine);
    });
    ```

    O código acima consulta o serviço de encaminhamento Azure Maps através do método [API de Rota de Mapas Azure.](/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-) A linha de rota é então extraída da coleção de recursos GeoJSON da resposta que é extraída usando o `geojson.getFeatures()` método. Finalmente, a linha de rota é adicionada à fonte de dados. Duas propriedades são adicionadas à linha da rota do caminhão: uma cor de traçado roxo, e uma largura de traçado de cinco píxeis.

4. Guarde o **TruckRoute.htmficheiro i** e refresque o seu navegador web. O mapa deve agora mostrar as rotas do caminhão e do carro.

    :::image type="content" source="./media/tutorial-prioritized-routes/prioritized-routes.png" alt-text="Rotas de veículos particulares e comerciais em um mapa com Serviço de Rota Azure":::

    A rota do caminhão é exibida com uma linha azul grossa. A rota do carro é exibida utilizando uma linha roxa fina. A rota do carro atravessa o Lago Washington através da I-90, passando por túneis sob áreas residenciais. Como os túneis estão perto de áreas residenciais, a carga de resíduos perigosos é restrita. A rota do caminhão, que especifica um tipo de `USHazmatClass2` carga, é direcionada para usar uma estrada diferente.

Pode obter o código fonte completo para a amostra [aqui.](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html) Uma amostra viva pode ser encontrada [aqui.](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

Também pode [utilizar expressões de estilo orientadas por dados](data-driven-style-expressions-web-sdk.md)

## <a name="clean-up-resources"></a>Limpar os recursos

Não há recursos que exijam limpeza.

## <a name="next-steps"></a>Passos seguintes

O próximo tutorial demonstra o processo de criação de um localizador de loja simples utilizando o Azure Maps.

> [!div class="nextstepaction"]
> [Criar um localizador de loja usando Azure Maps](./tutorial-create-store-locator.md)