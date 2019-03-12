---
title: Encontrar o trajeto com o Azure Maps | Microsoft Docs
description: Ir para um ponto de interesse com o Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b17a9660e16a1cb05c088e97d4ad18dd20fd4216
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588794"
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>Ir para um ponto de interesse com o Azure Maps

Este tutorial mostra como utilizar a sua conta do Azure Maps e o SDK do Route Service para encontrar o trajeto para o seu ponto de interesse. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma nova página Web com a API de controlo de mapas
> * Definir coordenadas de endereços
> * Consultar o Route Service para obter direções para o ponto de interesse

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, siga os passos do tutorial anterior para [criar a sua conta do Azure Maps](./tutorial-search-location.md#createaccount) e [obter a chave de subscrição para a sua conta](./tutorial-search-location.md#getkey).

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>Criar um novo mapa

Os passos seguintes mostram como criar uma página HTML estática incorporada com a API de Controlo de Mapas.

1. No seu computador local, crie um novo ficheiro e dê a este o nome de **MapRoute.html**.
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

   ![Ver mapa básico](./media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>Definir como o trajeto será composto

Neste tutorial, será composta uma rota simples com um ícone de símbolo para o início e o fim da rota, e uma linha para o caminho da rota.

1. Após inicializar o mapa, adicione o seguinte código JavaScript.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('load', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
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
    });
    ```

    Um evento de carregamento é adicionado ao mapa, que será acionado quando os recursos do mapa forem totalmente carregados. No processador de eventos de carregamento do mapa, é criada uma origem de dados para armazenar a linha de rota, bem como os pontos de início e fim. Uma camada de linha é criada e anexada à origem de dados para definir como será renderizada a linha de rota. A linha de rota será renderizada num tom de azul com uma largura de 5 pixéis e associações de linha arredondado e limites. Um filtro é adicionado para garantir que esta camada compõe apenas dados LineString GeoJSON. Ao adicionar a camada ao mapa, é passado um segundo parâmetro com o valor `'labels'`, no qual especifica a composição desta camada abaixo das etiquetas do mapa. Isto garante que a linha do trajeto não cobre as etiquetas de viagem. É criada e anexada uma camada de símbolo à origem de dados. Esta camada especifica como os pontos de início e de fim serão compostos, neste caso, foram adicionadas expressões para obter as informações de etiqueta de texto e imagem do ícone das propriedades em cada objeto de ponto.

2. Para este tutorial, defina o ponto de início como campus da Microsoft e o ponto final como uma estação de gás em Seattle. No processador de eventos de carregamento do mapa, adicione o seguinte código.

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

    Este código cria dois [objetos GeoJSON Point](https://en.wikipedia.org/wiki/GeoJSON) para representar os pontos inicial e final da rota e adiciona os pontos para a origem de dados. É adicionada uma propriedade `title` e `icon` a cada ponto. O último bloco define o modo de exibição de câmera utilizando as informações de latitude e longitude dos pontos inicial e final, usando o mapa [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) propriedade.

3. Guarde o ficheiro **MapRoute.html** e atualize o browser. Agora o mapa centra-se ao longo de Seattle, e pode ver o pin azul marcar o ponto inicial e o pin azul redondo marcar o ponto final.

   ![Ver mapa com os pontos de partida e de chegada](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Obter direções

Esta secção mostra como utilizar a API do Azure Maps route service para encontrar o caminho de um determinado ponto inicial para o ponto final. O serviço de trajetos fornece APIs para planear os trajetos *mais rápidos*, *mais curtos*, *mais ecológicos* ou *mais emocionantes* entre dois locais. Também permite aos utilizadores planear rotas no futuro através da extensa base de dados de tráfego histórico e da previsão das durações das rotas para qualquer dia e hora. Para obter mais informações, veja [Get route directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Obter indicações de trajetos). Todas as funcionalidades seguintes devem ser adicionadas no **eventListener de carregamento do mapa** para garantir que são carregadas depois de o mapa estar totalmente carregado.

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

2. Depois de configurar as credenciais e o URL, adicione o seguinte código JavaScript para construir a rota do início ao ponto final. O **routeURL** serviço para calcular as direções de rota encaminham os pedidos do Azure Maps. Uma coleção de recursos de GeoJSON da resposta é extraída, em seguida, utilizar o **geojson.getFeatures()** método e adicionado à origem de dados.

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

3. Guarde o ficheiro **MapRoute.html** e atualize o browser. Para uma ligação com êxito às APIs do Maps, deverá ver um mapa semelhante ao seguinte.

    ![Route Service e Controlo de Mapas do Azure](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma nova página Web com a API de controlo de mapas
> * Definir coordenadas de endereços
> * Consultar o serviço de trajetos para obter direções para o ponto de interesse

Pode aceder ao código de exemplo deste tutorial aqui:

> [Localizar trajeto com o Azure Maps](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

[Veja este exemplo aqui ao vivo](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

O tutorial seguinte demonstra como criar uma consulta de trajeto com restrições, como o meio de deslocação ou o tipo de carga, e apresentar vários trajetos no mesmo mapa.

> [!div class="nextstepaction"]
> [Localizar trajetos para diferentes meios de deslocação](./tutorial-prioritized-routes.md)
