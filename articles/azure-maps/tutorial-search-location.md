---
title: 'Tutorial: Procure locais próximos num mapa | Microsoft Azure Maps'
description: Tutorial sobre como procurar pontos de interesse num mapa. Veja como usar o Azure Maps Web SDK para adicionar capacidades de pesquisa e caixas popup interativas a um mapa.
author: anastasia-ms
ms.author: v-stharr
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 31dd1c06b0f17b469454593131ccdc93b45b2446
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624971"
---
# <a name="tutorial-search-nearby-points-of-interest-using-azure-maps"></a>Tutorial: Procure pontos de interesse nas proximidades usando Azure Maps

Este tutorial mostra como configurar uma conta com o Azure Maps e, em seguida, utilizar as APIs do Maps para procurar um ponto de interesse. Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Criar uma conta do Azure Maps
> * Obter a chave primária para a sua conta do Maps
> * Criar uma nova página Web com a API de Controlo de Mapas
> * Utilizar o serviço de pesquisa do Maps para localizar um ponto de interesse nas proximidades

## <a name="prerequisites"></a>Pré-requisitos

<a id="createaccount"></a>
<a id="getkey"></a>

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
2. [Faça uma conta Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps.](how-to-manage-authentication.md)

<a id="createmap"></a>

## <a name="create-a-new-map"></a>Criar um novo mapa

A API de Controlo de Mapas é uma biblioteca de clientes conveniente. Esta API permite-lhe integrar facilmente o Maps na sua aplicação web. Esconde a complexidade das chamadas de serviço REST e aumenta a sua produtividade com componentes personalizáveis. Os passos seguintes mostram como criar uma página HTML estática incorporada com a API de Controlo de Mapas.

1. No seu computador local, crie um novo ficheiro e dê-lhe o nome **MapSearch.html**.
2. Adicione os seguintes componentes HTML ao ficheiro:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Search</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
        function GetMap(){
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

   Repare que o cabeçalho HTML inclui os ficheiros de recursos CSS e JavaScript alojados pela biblioteca de Controlo de Mapas do Azure. Observe o evento `onload` no corpo da página, que irá chamar a função `GetMap` quando o corpo da página for carregada. A `GetMap` função conterá o código JavaScript inline para aceder às APIs do Azure Maps.

3. Adicione o seguinte código JavaScript à função `GetMap` do ficheiro HTML. Substitua a cadeia `<Your Azure Maps Key>` pela chave primária que copiou da sua conta Maps.

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

   Este segmento inicializa a API de Controlo de Mapas para a sua chave de conta do Azure Maps. `atlas` é o espaço de nome que contém a API e componentes visuais relacionados. `atlas.Map` fornece o controlo para um mapa web visual e interativo.

4. Guarde as alterações efetuadas ao ficheiro e abra a página HTML num browser. O mapa mostrado é o mapa mais básico que pode fazer ligando `atlas.Map` usando a chave da sua conta.

   ![Ver o mapa](./media/tutorial-search-location/basic-map.png)

5. Na função `GetMap`, após inicializar o mapa, adicione o seguinte código JavaScript.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering point data.
        var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: 'pin-round-darkblue',
                anchor: 'center',
                allowOverlap: true
            },
            textOptions: {
                anchor: "top"
            }
        });

        map.layers.add(resultLayer);
    });
    ```

   Neste segmento de código, é adicionado um `ready` evento ao mapa, que disparará quando os recursos do mapa tiverem sido carregados e o mapa estiver pronto para ser acedido. No manipulador de eventos do `ready` mapa, é criada uma fonte de dados para armazenar dados de resultados. É criada e anexada uma camada de símbolo à origem de dados. Esta camada especifica como os dados de resultados na fonte de dados devem ser renderizados. Neste caso, o resultado é renderizado com um ícone de pino redondo azul escuro, centrado sobre a coordenada de resultados, e permite que outros ícones se sobreponham. A camada de resultados é adicionada às camadas do mapa.

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Adicionar capacidades de pesquisa

Esta secção mostra como usar a [API de Pesquisa de](/rest/api/maps/search) Mapas para encontrar um ponto de interesse no seu mapa. É uma API RESTful concebida para os programadores procurarem endereços, pontos de interesse e outras informações geográficas. O serviço de Pesquisa atribui informações de latitude e longitude a um endereço especificado. O **Módulo Serviço** explicado abaixo pode ser utilizado para procurar uma localização com a API de Pesquisa do Maps.

### <a name="service-module"></a>Módulo Serviço

1. No manipulador de eventos do `ready` mapa, construa o URL do serviço de pesquisa adicionando o seguinte código JavaScript.

    ```JavaScript
   // Use SubscriptionKeyCredential with a subscription key
   var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

   // Use subscriptionKeyCredential to create a pipeline
   var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

   // Construct the SearchURL object
   var searchURL = new atlas.service.SearchURL(pipeline); 
   ```

   `SubscriptionKeyCredential`Cria um para `SubscriptionKeyCredentialPolicy` autenticar pedidos HTTP ao Azure Maps com a chave de subscrição. A `atlas.service.MapsURL.newPipeline()` política toma conta da política e cria uma instância `SubscriptionKeyCredential` [pipeline.](/javascript/api/azure-maps-rest/atlas.service.pipeline) O `searchURL` representa uma URL para as operações de [pesquisa](/rest/api/maps/search) de mapas Azure.

2. Em seguida, adicione o seguinte bloco de script para criar a consulta de pesquisa. Utiliza o Fuzzy Search Service, que é uma API de pesquisa básica do Search Service. O Fuzzy Search Service processa a maioria das entradas difusas, como quaisquer endereços, lugares e pontos de interesse (POI). Este código procura estações de gasolina próximas dentro do raio especificado da latitude e longitude fornecidas. Uma recolha de recursos GeoJSON da resposta é então extraída usando o `geojson.getFeatures()` método e adicionada à fonte de dados, o que resulta automaticamente na prestação de dados no mapa através da camada de símbolo. A última parte do script define a vista da câmera dos mapas com a caixa delimitadora dos resultados através da propriedade [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do Mapa.

    ```JavaScript
    var query =  'gasoline-station';
    var radius = 9000;
    var lat = 47.64452336193245;
    var lon = -122.13687658309935;

    searchURL.searchPOI(atlas.service.Aborter.timeout(10000), query, {
        limit: 10,
        lat: lat,
        lon: lon,
        radius: radius
    }).then((results) => {

        // Extract GeoJSON feature collection from the response and add it to the datasource
        var data = results.geojson.getFeatures();
        datasource.add(data);

        // set camera to bounds to show the results
        map.setCamera({
            bounds: data.bbox,
            zoom: 10
        });
    });
    ```

3. Guarde o ficheiro **MapSearch.html** e atualize o browser. Você deve ver o mapa centrado em Seattle com pinos azuis redondos para locais de postos de gasolina na área.

   ![Ver o mapa com os resultados da pesquisa](./media/tutorial-search-location/pins-map.png)

4. Pode ver os dados não processados que o mapa está a compor, introduzindo o seguinte HTTPRequest no seu browser. \<Your Azure Maps Key\>Substitua-a pela chave primária.

   ```http
   https://atlas.microsoft.com/search/poi/json?api-version=1.0&query=gasoline%20station&subscription-key=<subscription-key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

Neste momento, a página de MapSearch pode apresentar as localizações dos pontos de interesse devolvidos por uma consulta de pesquisa difusa. Vamos adicionar algumas capacidades interativas e mais informações sobre as localizações.

## <a name="add-interactive-data"></a>Adicionar dados interativos

O mapa criado até ao momento está focado apenas nos dados de longitude/latitude para os resultados da pesquisa. No entanto, o JSON cru que o serviço de pesquisa maps contém informações adicionais sobre cada posto de gasolina. Incluindo o nome e o endereço de rua. Pode incorporar esses dados no mapa com caixas de pop-up interativas.

1. Adicione as seguintes linhas de código no manipulador de eventos do mapa `ready` após o código para consultar o serviço de pesquisa difuso. Este código criará uma instância de um Popup e adicionará um evento de mouseover à camada de símbolo.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```

    A API `*atlas.Popup` fornece uma janela de informação ancorada na posição exigida no mapa. 

2. Adicione o seguinte código dentro da `GetMap` função, para mostrar a informação do rato sobre o resultado no pop-up.

    ```JavaScript
    function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occurred on.

        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = `
          <div style="padding:5px">
            <div><b>${p.poi.name}</b></div>
            <div>${p.address.freeformAddress}</div>
            <div>${position[1]}, ${position[0]}</div>
          </div>`;

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html,
            position: position
        });

        //Open the popup.
        popup.open(map);
    }
    ```

3. Guarde o ficheiro e atualize o browser. Agora, o mapa no browser mostra pop-ups de informação quando paira o rato sobre qualquer um dos marcadores de pesquisa.

    ![Search Service e Controlo de Mapas do Azure](./media/tutorial-search-location/popup-map.png)

Para ver o código completo para este tutorial, clique [aqui.](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html) Para ver a amostra ao vivo, clique [aqui](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

## <a name="clean-up-resources"></a>Limpar os recursos

Não há recursos que exijam limpeza.

## <a name="next-steps"></a>Passos seguintes

O próximo tutorial demonstra como apresentar um percurso entre duas localizações.

> [!div class="nextstepaction"]
> [Rota para um destino](./tutorial-route-location.md)