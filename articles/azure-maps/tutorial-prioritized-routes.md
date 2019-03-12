---
title: Vários trajetos com o Azure Maps | Microsoft Docs
description: Localizar trajetos para diferentes meios de deslocação com o Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 2af3981e05482a5f59b19cfaa2e400ae47295763
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588844"
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>Localizar trajetos para diferentes meios de deslocação com o Azure Maps

Este tutorial mostra como utilizar a sua conta do Azure Maps e o serviço de rotas para encontrar o caminho para o seu ponto de interesse, priorizado pelo seu meio de transporte. Apresente dois trajetos diferentes no seu mapa, um para carros e outro para camiões que possam ter restrições de trânsito, devido a altura, peso ou carga perigosa. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma nova página Web com a API de controlo de mapas
> * Visualizar o fluxo de tráfego no seu mapa
> * Criar consultas de trajeto que declaram o modo de deslocação
> * Apresentar vários trajetos no seu mapa

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, siga os passos do primeiro tutoria para [criar a sua conta do Azure Maps](./tutorial-search-location.md#createaccount) e [obter a chave da subscrição para a sua conta](./tutorial-search-location.md#getkey).

## <a name="create-a-new-map"></a>Criar um novo mapa

Os passos seguintes mostram como criar uma página HTML estática incorporada com a API de Controlo de Mapas.

1. No seu computador local, crie um novo ficheiro e dê-lhe o nome **MapTruckRoute.html**.
2. Adicione os seguintes componentes de HTML ao ficheiro:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=2" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=2"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=2"></script>

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

3. Adicione o seguinte código JavaScript à função `GetMap`. Substitua a cadeia **\<A Sua Chave do Azure Maps\>** pela chave primária que copiou a partir da sua conta do Maps.

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

    O ficheiro **atlas.Map** fornece o controlo para um mapa Web visual e interativo, sendo um componente da API do Controlo de Mapas do Azure.

4. Guarde o ficheiro e abra-o no browser. Neste momento, tem um mapa básico que pode aprimorar mais.

   ![Ver mapa básico](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Visualizar o fluxo do tráfego

1. Adicione a apresentação do fluxo de tráfego ao mapa. Depois de o mapa estar totalmente carregado, `map.events.add` garante que todas as funções dos mapas adicionadas ao mapa são carregadas.

    ```JavaScript
    map.events.add("load", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```
    
     Um evento de carregamento é adicionado ao mapa, que será acionado quando os recursos do mapa forem totalmente carregados. No processador de eventos de carregamento do mapa, a definição de fluxo de tráfego no mapa está definida como `relative`, que é a velocidade da estrada relativa ao fluxo livre. Também pode defini-lo como velocidade da estrada `absolute` ou `relative-delay`, o que apresenta a velocidade relativa onde difere do fluxo livre.

2. Guarde o ficheiro **MapTruckRoute.html** e atualize a página no browser. Se interage com o mapa e aplicar zoom no Lamego deverá ver as ruas com os dados de tráfego atuais.

   ![Ver mapa do tráfego](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definir como o trajeto será composto

Neste tutorial, dois trajetos serão calculados e processados no mapa. Um trajeto com estradas acessíveis a carros e o outro com estradas acessíveis a camiões. Quando o trajeto for composto, apresentaremos um ícone de símbolo para o início e o fim do trajeto e diferentes linhas coloridas para o caminho de cada trajeto.

1. Após inicializar o mapa, adicione o seguinte código JavaScript no evento de carregamento de mapa.

    ```JavaScript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Add a layer for rendering the route lines and have it render under the map labels.
    map.layers.add(new atlas.layer.LineLayer(datasource, null, {
        strokeColor: ['get', 'strokeColor'],
        strokeWidth: ['get', 'strokeWidth'],
        lineJoin: 'round',
        lineCap: 'round',
        filter: ['==', '$type', 'LineString']
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
        filter: ['==', '$type', 'Point']
    }));
    ```

    Um evento de carregamento é adicionado ao mapa, que será acionado quando os recursos do mapa forem totalmente carregados. No processador de eventos de carregamento do mapa, é criada uma origem de dados para armazenar as linhas do trajeto, bem como os pontos de início e fim. Uma camada de linha é criada e anexada à origem de dados para definir como será renderizada a linha de rota. São utilizadas expressões para obter a largura e a cor da linha a partir das propriedades na funcionalidade de linha do trajeto. Um filtro é adicionado para garantir que esta camada compõe apenas dados LineString GeoJSON. Ao adicionar a camada ao mapa, é passado um segundo parâmetro com o valor `'labels'`, no qual especifica a composição desta camada abaixo das etiquetas do mapa. Isto garante que a linha do trajeto não cobre as etiquetas de viagem. É criada e anexada uma camada de símbolo à origem de dados. Esta camada especifica como os pontos de início e de fim serão compostos, neste caso, foram adicionadas expressões para obter as informações de etiqueta de texto e imagem do ícone das propriedades em cada objeto de ponto.
    
2. Para este tutorial, defina o ponto de início como uma empresa fictícia em Seattle chamado Fabrikam e o ponto final como um edifício na sede da Microsoft. No processador de eventos de carregamento do mapa, adicione o seguinte código.

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
    
    Este código cria dois [objetos GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) para representar os pontos de início e de fim da rota. É adicionada uma propriedade `title` e `icon` a cada ponto.

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
    Os pontos de início e de fim são adicionados à origem de dados. A caixa delimitadora para os pontos de início e de fim é calculada com a função `atlas.data.BoundingBox.fromData`. Esta caixa delimitadora é usada para definir a exibição de câmaras de mapa com o uso de rota de toda o `map.setCamera` função. É adicionado um preenchimento para compensar as dimensões de píxel dos ícones de símbolo.

4. Guarde o ficheiro e atualize o browser para ver os marcadores no seu mapa. Agora, o mapa está centrado em Seattle e consegue ver o alfinete azul redondo a marcar o ponto de partida e o alfinete azul a marcar o ponto de chegada.

   ![Ver mapa com os pontos de partida e de início](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Compor rotas priorizadas por meio de transporte

Esta secção mostra como utilizar a API do mapas route service para localizar várias rotas a partir de um determinado ponto inicial para o ponto de extremidade com base no seu meio de transporte. O serviço de trajetos oferece APIs para planear os trajetos *mais rápidos*, *mais curtos*, *mais ecológicos* ou *mais emocionantes* entre dois locais, considerando as condições de tráfego atuais. Também permite aos utilizadores planear rotas no futuro através da extensa base de dados de tráfego histórico e da previsão das durações das rotas para qualquer dia e hora. Para obter mais informações, veja [Get Route Directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Obter indicações de trajetos). Todos os blocos de código seguintes devem ser adicionados no **eventListener de carregamento do mapa** para garantir que são carregados depois de o mapa estar totalmente carregado.

1. Na função GetMap, adicione o seguinte código Javascript.

    ```Javascript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```
   O **SubscriptionKeyCredential** cria um **SubscriptionKeyCredentialPolicy** para autenticar pedidos HTTP para o Azure Maps com a chave de subscrição. O **atlas.service.MapsURL.newPipeline()** aceita a **SubscriptionKeyCredential** política e cria um [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) instância. O **routeURL** representa um URL para o Azure Maps [rota](https://docs.microsoft.com/rest/api/maps/route) operações.

2. Depois de configurar as credenciais e o URL, adicione o seguinte JavaScript o código para construir uma rota de início para o ponto de extremidade para um camião portando USHazmatClass2 classificado carga e exibir os resultados.

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
    Este fragmento de código acima consulta o serviço de encaminhamento de mapas do Azure através da [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-iot-typescript-latest) método. A linha de rota, em seguida, é extraída da coleção de funcionalidade GeoJSON da resposta que é extraída usando a **geojson.getFeatures()** método. A linha de rota, em seguida, é adicionada à origem de dados. Ele também adiciona um índice de 0 para se certificar de que é processado antes de quaisquer outras linhas na origem de dados. Isto é feito porque o cálculo do trajeto de camião será, muitas vezes, mais lento do que o cálculo de um trajeto de carro e, se a linha do trajeto de camião for adicionada à origem de dados após o trajeto de carro, será composta acima dela. Duas propriedades são adicionadas à linha do trajeto de camião: uma cor do traço que corresponde a uma tonalidade de azul e uma largura do traço de 9 pixels.

3. Adicione o seguinte código JavaScript para construir uma rota para um carro e exibir os resultados.

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

    Este fragmento de código acima consulta o serviço de encaminhamento de mapas do Azure através da [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-iot-typescript-latest) método. A linha de rota, em seguida, é extraída da coleção de funcionalidade GeoJSON da resposta que é extraída usando a **geojson.getFeatures()** método. A linha de rota, em seguida, é adicionada à origem de dados. Duas propriedades são adicionadas à linha do trajeto de carro: uma cor do traço que corresponde a uma tonalidade de roxo e uma largura do traço de 5 pixels.  

4. Guarde o ficheiro **MapTruckRoute.html** e atualize o browser para ver o resultado. Para uma ligação com êxito às APIs do Maps, deverá ver um mapa semelhante ao seguinte.

    ![Rotas priorizadas com o Route Service do Azure](./media/tutorial-prioritized-routes/prioritized-routes.png)

    O trajeto do camião é azul e mais espesso, ao passo que o do carro é roxo e mais fino. O trajeto do carro passa por Lake Washington através da autoestrada interestadual I-90, que passa por túneis em zonas residenciais, pelo que não são permitidas cargas com resíduos perigosos. O trajeto do camião, que especifica uma carga do tipo USHazmatClass2, é direcionado corretamente para outra autoestrada.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma nova página Web com a API de controlo de mapas
> * Visualizar o fluxo de tráfego no seu mapa
> * Criar consultas de trajeto que declaram o modo de deslocação
> * Apresentar vários trajetos no seu mapa

Pode aceder ao código de exemplo deste tutorial aqui:

> [Multiple routes with Azure Maps](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html) (Vários trajetos com o Azure Maps)

[Veja o exemplo aqui ao vivo](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

O próximo tutorial demonstra o processo de criação de um localizador de arquivo simples com o Azure Maps.

> [!div class="nextstepaction"]
> [Criar um localizador de arquivo com o Azure Maps](./tutorial-create-store-locator.md)
