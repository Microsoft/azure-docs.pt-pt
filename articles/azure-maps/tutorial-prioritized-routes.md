---
title: 'Tutorial: Encontre várias rotas por modo de viagem Microsoft Azure Maps'
description: Saiba como usar o Azure Maps para encontrar rotas para modos de viagem específicos para pontos de interesse. Veja como exibir várias rotas nos mapas.
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-javascript
ms.openlocfilehash: 7d17b9474edef245f7db16f33c72d722f356f712
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037597"
---
# <a name="tutorial-find-routes-for-different-modes-of-travel-using-azure-maps"></a>Tutorial: Encontre rotas para diferentes modos de viagem usando mapas Azure

Este tutorial mostra como usar a sua conta Azure Maps e o serviço de rotas. O serviço de rota pode encontrar a rota para o seu ponto de interesse, priorizada pelo seu modo de viagem. Pode exibir duas rotas diferentes no seu mapa, uma para carros e outra para camiões. O serviço de encaminhamento tem em consideração limitações devido à altura e peso do veículo, ou se o veículo transporta carga perigosa. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma nova página Web com a API de Controlo de Mapas
> * Visualizar o fluxo de tráfego no seu mapa
> * Criar consultas de trajeto que declaram o modo de deslocação
> * Apresentar vários trajetos no seu mapa

## <a name="prerequisites"></a>Pré-requisitos
Antes de continuar, siga as instruções na [Criar uma conta](quick-demo-map-app.md#create-an-azure-maps-account) e selecione o nível de preços S1. Siga os passos para obter a [chave principal](quick-demo-map-app.md#get-the-primary-key-for-your-account) para obter a chave principal para a sua conta. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps.](how-to-manage-authentication.md)

## <a name="create-a-new-map"></a>Criar um novo mapa

Os passos seguintes mostram como criar uma página HTML estática incorporada com a API de Controlo de Mapas.

1. No seu computador local, crie um novo ficheiro e dê-lhe o nome **MapTruckRoute.html**.
2. Adicione os seguintes componentes HTML ao ficheiro:

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

    Repare que o cabeçalho HTML inclui os ficheiros de recursos CSS e JavaScript alojados pela biblioteca de Controlo de Mapas do Azure. Observe o evento `onload` no corpo da página, que irá chamar a função `GetMap` quando o corpo da página for carregada. Esta função irá conter o código JavaScript inline para aceder às APIs do Azure Maps.

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

    A `atlas.Map` classe fornece o controlo para um mapa web visual e interativo, e é um componente da API de Controlo de Mapas Azure.

4. Guarde o ficheiro e abra-o no browser. Neste momento, tem um mapa básico que pode aprimorar mais.

   ![Ver mapa básico](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Visualizar o fluxo do tráfego

1. Adicione a apresentação do fluxo de tráfego ao mapa. O evento de `ready` mapas aguarda até que os recursos dos mapas estejam carregados e prontos para interagir com ele com segurança.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    No manipulador de eventos do `ready` mapa, a definição do fluxo de tráfego no mapa está definida para `relative` , que é a velocidade da estrada em relação ao fluxo livre. Também pode defini-lo como velocidade da estrada `absolute` ou `relative-delay`, o que apresenta a velocidade relativa onde difere do fluxo livre.

2. Guarde o ficheiro **MapTruckRoute.html** e atualize a página no browser. Se interagir com o mapa e aproximar-se de Los Angeles, deve ver as ruas com os dados de tráfego atuais.

   ![Ver tráfego em um mapa](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definir como o trajeto será composto

Neste tutorial, dois trajetos serão calculados e processados no mapa. Um trajeto com estradas acessíveis a carros e o outro com estradas acessíveis a camiões. Quando renderizado, mostraremos um ícone de símbolo para o início e fim do percurso, e diferentes linhas coloridas para cada caminho de rota.

1. Depois de rubricar o mapa, adicione o seguinte código JavaScript no manipulador de eventos de `ready` mapas.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('ready', function () {

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
    });
    ```
    
    No manipulador de eventos de `ready` mapas, é criada uma fonte de dados para armazenar as linhas de rota e os pontos de partida e de fim. É criada uma camada de linhas e anexada à origem de dados para definir como será composta a linha de rotas. São utilizadas expressões para obter a largura e a cor da linha a partir das propriedades na funcionalidade de linha do trajeto. Ao adicionar a camada ao mapa, é passado um segundo parâmetro com o valor `'labels'`, no qual especifica a composição desta camada abaixo das etiquetas do mapa. Isto garante que a linha de rota não cobre as etiquetas da estrada. É criada e anexada uma camada de símbolo à origem de dados. Esta camada especifica como os pontos de partida e final serão renderizados. Neste caso, foram adicionadas expressões para recuperar a imagem do ícone e a informação do rótulo de texto a partir de propriedades em cada objeto de ponto. 
    
2. Neste tutorial, defina o ponto de partida como uma empresa fictícia em Seattle, chamada Fabrikam, e o ponto de chegada como um escritório da Microsoft. No manipulador de eventos de `ready` mapas, adicione o seguinte código.

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
    ```

    Este código cria dois [objetos GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) para representar os pontos iniciais e finais do caminho. É adicionada uma propriedade `title` e `icon` a cada ponto.

3. Em seguida, adicione o seguinte código do JavaScript para adicionar alfinetes aos pontos iniciais e finais ao mapa:

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```

    Os pontos de início e de fim são adicionados à origem de dados. A caixa delimitadora para os pontos de início e de fim é calculada com a função `atlas.data.BoundingBox.fromData`. Esta caixa de delimitação é usada para definir a visualização das câmaras de mapa em toda a rota utilizando a `map.setCamera` função. É adicionado um preenchimento para compensar as dimensões de píxel dos ícones de símbolo.

4. Guarde o ficheiro e atualize o browser para ver os marcadores no seu mapa. Agora o mapa está centrado em Seattle. Pode ver o pino azul redondo marcando o ponto de partida e o pino azul marcando o ponto de chegada.

   ![Ver mapa com os pontos de partida e de início](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Compor rotas priorizadas por meio de transporte

Esta secção mostra como utilizar o serviço de rota Maps API. A rota API é usada para encontrar várias rotas de um dado ponto de partida até ao ponto final, com base no seu modo de transporte. O serviço de rotas fornece APIs para planear rotas *mais rápidas,* *mais curtas,* *eco*ou *emocionantes.* Não só as APIs planeiam rotas entre dois locais, como também consideram as condições de tráfego em vigor. 

A rota API permite que os utilizadores planeiem rotas no futuro usando a extensa base de dados de tráfego histórico da Azure. A API pode prever durações de rota para um determinado dia e hora. Para obter mais informações, veja [Get Route Directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Obter indicações de trajetos). 

Todos os seguintes blocos de código devem ser adicionados **dentro do evento de carga do mapaListener** para garantir que carregam após o mapa carregar completamente.

1. Na função GetMap, adicione o seguinte ao código JavaScript.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential`Cria um para `SubscriptionKeyCredentialPolicy` autenticar pedidos HTTP ao Azure Maps com a chave de subscrição. A `atlas.service.MapsURL.newPipeline()` política toma conta da política e cria uma instância `SubscriptionKeyCredential` [pipeline.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) O `routeURL` representa uma URL para as operações da Rota [dos](https://docs.microsoft.com/rest/api/maps/route) Mapas Azure.

2. Depois de configurar credenciais e o URL, adicione o seguinte código JavaScript para construir uma rota do início ao ponto final para um caminhão que transporta carga classificada USHazmatClass2 e mostrar os resultados.

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

    Este código corta o código acima questiona o serviço de encaminhamento Azure Maps através do método [getRouteDirections.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) A linha de rota é então extraída da coleção de recursos GeoJSON da resposta que é extraída usando o `geojson.getFeatures()` método. A linha de rota é então adicionada à fonte de dados. Um índice de 0 garante que é renderizado antes de qualquer outra linha na fonte de dados. Isto é feito porque o cálculo da rota do caminhão será muitas vezes mais lento do que um cálculo da rota do carro. Se a linha de rota do caminhão for adicionada à fonte de dados após a rota do carro, ela renderá acima dela. Duas propriedades são adicionadas à linha da rota do caminhão, uma cor de traçado que é um belo tom de azul, e uma largura de traçado de nove pixels.

3. Adicione o seguinte código JavaScript para construir uma rota para um carro e mostrar os resultados.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.  
        datasource.add(routeLine);
    });
    ```

    Este código corta o código acima questiona o serviço de encaminhamento Azure Maps através do método [getRouteDirections.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) A linha de rota é então extraída da coleção de recursos GeoJSON da resposta que é extraída usando o `geojson.getFeatures()` método. A linha de rota é então adicionada à fonte de dados. Duas propriedades são adicionadas à linha de rota do carro, uma cor de traçado que é um tom de roxo, e uma largura de traçado de cinco pixels.  

4. Guarde o ficheiro **MapTruckRoute.html** e atualize o browser para ver o resultado. Para uma ligação com êxito às APIs do Maps, deverá ver um mapa semelhante ao seguinte.

    ![Rotas priorizadas com o Route Service do Azure](./media/tutorial-prioritized-routes/prioritized-routes.png)

    A rota do caminhão é azul grosso, e a rota do carro é fina roxo. A rota do carro atravessa o Lago Washington através da I-90, que passa por túneis em áreas residenciais. Como os túneis estão perto de áreas residenciais, a carga de resíduos perigosos é restrita. A rota do caminhão, que especifica um tipo de carga USHazmatClass2, é direcionada para usar uma estrada diferente.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma nova página Web com a API de Controlo de Mapas
> * Visualizar o fluxo de tráfego no seu mapa
> * Criar consultas de trajeto que declaram o modo de deslocação
> * Apresentar vários trajetos no seu mapa

> [!div class="nextstepaction"]
> [Ver código fonte completo](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

> [!div class="nextstepaction"]
> [Ver amostra ao vivo](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

O próximo tutorial demonstra o processo de criação de um localizador de loja simples utilizando o Azure Maps.

> [!div class="nextstepaction"]
> [Criar um localizador de loja usando Azure Maps](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [Utilizar expressões de estilo com base em dados](data-driven-style-expressions-web-sdk.md)