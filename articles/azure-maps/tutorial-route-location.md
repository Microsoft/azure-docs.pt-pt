---
title: 'Tutorial: Encontre o caminho para um local  Microsoft Azure Maps'
description: Este tutorial mostra-lhe como tornar a rota para um local (ponto de interesse) num mapa utilizando o Microsoft Azure Maps Routing Service.
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: ba9ef8ad98dd33bdd61875e5c1cf02e15a739c03
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208085"
---
# <a name="tutorial-route-to-a-point-of-interest-using-azure-maps"></a>Tutorial: Rota para um ponto de interesse usando o Azure Maps

Este tutorial mostra como utilizar a sua conta do Azure Maps e o SDK do Route Service para encontrar o trajeto para o seu ponto de interesse. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma nova página Web com a API de controlo de mapas
> * Definir coordenadas de endereços
> * Consultar o Route Service para obter direções para o ponto de interesse

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, siga as instruções em [Criar uma conta,](quick-demo-map-app.md#create-an-account-with-azure-maps)precisa de uma subscrição com o nível de preços S1. Siga os passos para obter a [chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account) para obter a chave principal para a sua conta. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps](how-to-manage-authentication.md).

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>Criar um novo mapa

Os passos seguintes mostram como criar uma página HTML estática incorporada com a API de Controlo de Mapas.

1. No seu computador local, crie um novo ficheiro e dê a este o nome de **MapRoute.html**.
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

3. Adicione o seguinte código JavaScript à função `GetMap`. Substitua a `<Your Azure Maps Key>` de cadeias com a chave primária que copiou da sua conta Maps.

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

    O ficheiro `atlas.Map` oferece o controlo para um mapa Web visual e interativo, sendo um componente da API do Controlo de Mapas do Azure.

4. Guarde o ficheiro e abra-o no browser. Neste momento, tem um mapa básico que pode aprimorar mais.

   ![Ver mapa básico](media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>Definir como o trajeto será composto

Neste tutorial, será composta uma rota simples com um ícone de símbolo para o início e o fim da rota, e uma linha para o caminho da rota.

1. Depois de inicializar o mapa, adicione o seguinte código JavaScript.

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
    
    Nos mapas `ready` manipulador de eventos, é criada uma fonte de dados para armazenar a linha de rota e os pontos de partida e fim. É criada uma camada de linhas e anexada à origem de dados para definir como será composta a linha de rotas. A linha de rota será renderizada como um belo tom de azul. Terá uma largura de cinco píxeis, juntas de linha arredondada e tampas. Ao adicionar a camada ao mapa, é passado um segundo parâmetro com o valor `'labels'`, no qual especifica a composição desta camada abaixo das etiquetas do mapa. Isto garante que a linha do trajeto não cobre as etiquetas de viagem. É criada e anexada uma camada de símbolo à origem de dados. Esta camada especifica como os pontos de partida e de fim são renderizados. Neste caso, foram adicionadas expressões para recuperar a imagem do ícone e as informações da etiqueta de texto das propriedades de cada objeto de ponto. 
    
2. Para este tutorial, defina o ponto de início como Microsoft e o ponto de fim como uma bomba de gasolina em Seattle. Nos mapas `ready` manipulador de eventos, adicione o seguinte código.

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

    Este código cria dois [objetos GeoJSON Point](https://en.wikipedia.org/wiki/GeoJSON) para representar os pontos de partida e final da rota e adiciona os pontos à fonte de dados. É adicionada uma propriedade `title` e `icon` a cada ponto. O último bloco define a vista da câmara usando a latitude e longitude dos pontos de partida e fim, utilizando a propriedade [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do Mapa.

3. Guarde o ficheiro **MapRoute.html** e atualize o browser. Agora o mapa está centrado sobre Seattle, e você pode ver o pino azul marcando o ponto de partida e o pino azul redondo marcando o ponto de chegada.

   ![Ver rotas de início e ponto final no mapa](media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Obter direções

Esta secção mostra como utilizar o serviço de rota Azure Maps API. O serviço de rota API encontra a rota de um determinado ponto de partida para um ponto final. Dentro deste serviço, existem APIs para planear rotas *mais rápidas,* *mais curtas,* *eco*ou *emocionantes* entre dois locais. Este serviço também permite que os utilizadores planeiem rotas no futuro utilizando a extensa base de dados de tráfego histórico do Azure. Os utilizadores podem ver a previsão das durações da rota em qualquer dia e hora escolhidos. Para obter mais informações, veja [Get route directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Obter indicações de trajetos). Todas as seguintes funcionalidades devem ser adicionadas dentro do evento pronto para **o mapaListener** para garantir que carregam após os recursos do mapa estarem prontos para serem acedidos.

1. Na função GetMap, adicione o seguinte ao código JavaScript.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   O `SubscriptionKeyCredential` cria uma `SubscriptionKeyCredentialPolicy` para autenticar pedidos DE HTTP para o Azure Maps com a chave de subscrição. O `atlas.service.MapsURL.newPipeline()` apodera-se da política `SubscriptionKeyCredential` e cria uma instância [de Pipeline.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) O `routeURL` representa um URL para operações da [Rota](https://docs.microsoft.com/rest/api/maps/route) dos Mapas Azure.

2. Depois de configurar as credenciais e o URL, adicione o seguinte código JavaScript para construir a rota do ponto de início ao fim. O `routeURL` solicita ao serviço de rota Azure Maps para calcular as direções da rota. Uma recolha de funcionalidades GeoJSON da resposta é então extraída utilizando o método `geojson.getFeatures()` e adicionada à fonte de dados.

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

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma nova página Web com a API de controlo de mapas
> * Definir coordenadas de endereços
> * Consultar o serviço de trajetos para obter direções para o ponto de interesse

> [!div class="nextstepaction"]
> [Ver código fonte completo](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

> [!div class="nextstepaction"]
> [Ver amostra ao vivo](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

O tutorial seguinte demonstra como criar uma consulta de trajeto com restrições, como o meio de deslocação ou o tipo de carga, e apresentar vários trajetos no mesmo mapa.

> [!div class="nextstepaction"]
> [Localizar trajetos para diferentes meios de deslocação](./tutorial-prioritized-routes.md)
