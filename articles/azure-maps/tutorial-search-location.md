---
title: 'Tutorial: pesquisar locais próximos em um mapa | Mapas do Microsoft Azure'
description: Neste tutorial, você aprenderá a procurar pontos de interesse em um mapa usando mapas de Microsoft Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9b390806e678c7900b166a07316d7f8ac32a5153
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716182"
---
# <a name="tutorial-search-nearby-points-of-interest-using-azure-maps"></a>Tutorial: Pesquisar pontos de interesse próximos usando o Azure Maps

Este tutorial mostra como configurar uma conta com o Azure Maps e, em seguida, utilizar as APIs do Maps para procurar um ponto de interesse. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma conta do Azure Maps
> * Obter a chave primária para a sua conta do Maps
> * Criar uma nova página Web com a API de controlo de mapas
> * Utilizar o serviço de pesquisa do Maps para localizar um ponto de interesse nas proximidades

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Criar uma conta com o Azure Maps

Crie uma nova conta dos Maps com os seguintes passos:

1. No canto superior esquerdo do [portal do Azure](https://portal.azure.com), clique em **Criar um recurso**.
2. Na caixa *Procurar no Marketplace*, escreva **Maps**.
3. Em *Resultados*, selecione **Maps**. Clique no botão **Criar** que aparece abaixo do mapa.
4. Na página **Criar Conta do Maps**, introduza os seguintes valores:
    * A *Subscrição* que quer utilizar para esta conta.
    * O nome do *Grupo de recursos* para esta conta. Pode optar por *Criar um grupo de recursos novo* ou *Utilizar um grupo de recursos existente*.
    * O *Nome* da nova conta.
    * O *nível de preços* desta conta.
    * Leia a *Licença* e a *Declaração de Privacidade*, e selecione a caixa de verificação para aceitar os termos.
    * Clique no botão **Criar**.

![Criar conta do Azure Maps no portal do Azure](./media/tutorial-search-location/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Obter a chave primária para a sua conta

Depois de a sua conta do Maps ser criada com êxito, obtenha a chave que lhe permite consultar as APIs do Maps. É recomendável usar a chave primária da sua conta como a chave de assinatura ao chamar os serviços do Azure Maps.

1. Abra a sua conta do Maps no portal.
2. Na secção definições, **selecione Autenticação**.
3. Copie a **Chave primária** para a área de transferência. Guarde-a localmente para a utilizar mais tarde neste tutorial.

![Obter chave primária no portal do Azure](./media/tutorial-search-location/get-key.png)

Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps](how-to-manage-authentication.md).

<a id="createmap"></a>

## <a name="create-a-new-map"></a>Criar um novo mapa

A API de Controle de Mapeamento é uma biblioteca de cliente conveniente. Essa API permite que você integre facilmente os mapas em seu aplicativo Web. Ele oculta a complexidade das chamadas de serviço Bare-REST e aumenta sua produtividade com componentes personalizáveis. Os passos seguintes mostram como criar uma página HTML estática incorporada com a API de Controlo de Mapas.

1. No seu computador local, crie um novo ficheiro e dê-lhe o nome **MapSearch.html**.
2. Adicione os seguintes componentes de HTML ao ficheiro:

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

   Repare que o cabeçalho HTML inclui os ficheiros de recursos CSS e JavaScript alojados pela biblioteca de Controlo de Mapas do Azure. Observe o evento `onload` no corpo da página, que irá chamar a função `GetMap` quando o corpo da página for carregada. A função `GetMap` conterá o código JavaScript inline para aceder às APIs do Azure Maps.

3. Adicione o seguinte código JavaScript à função `GetMap` do ficheiro HTML. Substitua a `<Your Azure Maps Key>` de cadeias com a chave primária que copiou da sua conta Maps.

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

   Este segmento inicializa a API de Controlo de Mapas para a sua chave de conta do Azure Maps. `atlas` é o espaço de nome que contém a API e componentes visuais relacionados. `atlas.Map` fornece o controlo de um mapa web visual e interativo.

4. Guarde as alterações efetuadas ao ficheiro e abra a página HTML num browser. O mapa mostrado é o mapa mais básico que se pode fazer chamando `atlas.Map` usando a chave da sua conta.

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

   Neste segmento de código, um evento `ready` é adicionado ao mapa, que irá disparar quando os recursos do mapa tiverem sido carregados e o mapa estiver pronto para ser acedido. No mapa `ready` manipulador de eventos, é criada uma fonte de dados para armazenar dados de resultados. É criada e anexada uma camada de símbolo à origem de dados. Essa camada especifica como os dados de resultado na fonte de dados devem ser renderizados. Nesse caso, o resultado é renderizado com um ícone de pino redondo azul escuro, centralizado sobre a coordenada de resultados e permite que outros ícones se sobreponham. A camada de resultado é adicionada às camadas do mapa.

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Adicionar capacidades de pesquisa

Esta secção mostra como usar a [API](https://docs.microsoft.com/rest/api/maps/search) de pesquisa de mapas para encontrar um ponto de interesse no seu mapa. É uma API RESTful concebida para os programadores procurarem endereços, pontos de interesse e outras informações geográficas. O serviço de Pesquisa atribui informações de latitude e longitude a um endereço especificado. O **Módulo Serviço** explicado abaixo pode ser utilizado para procurar uma localização com a API de Pesquisa do Maps.

### <a name="service-module"></a>Módulo Serviço

1. No mapa `ready` manipulador de eventos, construa o URL do serviço de pesquisa adicionando o seguinte código Javascript.

    ```JavaScript
   // Use SubscriptionKeyCredential with a subscription key
   var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

   // Use subscriptionKeyCredential to create a pipeline
   var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

   // Construct the SearchURL object
   var searchURL = new atlas.service.SearchURL(pipeline); 
   ```

   O `SubscriptionKeyCredential` cria uma `SubscriptionKeyCredentialPolicy` para autenticar pedidos DE HTTP para o Azure Maps com a chave de subscrição. O `atlas.service.MapsURL.newPipeline()` apodera-se da política `SubscriptionKeyCredential` e cria uma instância [de Pipeline.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) O `searchURL` representa um URL para operações de [pesquisa](https://docs.microsoft.com/rest/api/maps/search) do Azure Maps.

2. Em seguida, adicione o seguinte bloco de script para criar a consulta de pesquisa. Utiliza o Fuzzy Search Service, que é uma API de pesquisa básica do Search Service. O Fuzzy Search Service processa a maioria das entradas difusas, como quaisquer endereços, lugares e pontos de interesse (POI). Esse código procura estações de gasolina próximas dentro do raio especificado da latitude e longitude fornecidas. Uma recolha de funcionalidades GeoJSON da resposta é então extraída utilizando o método `geojson.getFeatures()` e adicionada à fonte de dados, o que resulta automaticamente na prestação dos dados no mapa através da camada de símbolo. A última parte do script define a vista da câmera dos mapas com a caixa delimitadora dos resultados através da propriedade [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do Mapa.

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

3. Guarde o ficheiro **MapSearch.html** e atualize o browser. Você deve ver o mapa centralizado em Seattle com Pins redondos-azul para locais de estações de gasolina na área.

   ![Ver o mapa com os resultados da pesquisa](./media/tutorial-search-location/pins-map.png)

4. Pode ver os dados não processados que o mapa está a compor, introduzindo o seguinte HTTPRequest no seu browser. Substitua a \<Sua Chave do Azure Maps\> pela chave primária.

   ```http
   https://atlas.microsoft.com/search/poi/json?api-version=1.0&query=gasoline%20station&subscription-key=<subscription-key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

Neste momento, a página de MapSearch pode apresentar as localizações dos pontos de interesse devolvidos por uma consulta de pesquisa difusa. Vamos adicionar algumas capacidades interativas e mais informações sobre as localizações.

## <a name="add-interactive-data"></a>Adicionar dados interativos

O mapa criado até ao momento está focado apenas nos dados de longitude/latitude para os resultados da pesquisa. No entanto, o JSON bruto que o serviço de pesquisa do Maps retorna contém informações adicionais sobre cada estação de gás. Incluindo o nome e o endereço. Pode incorporar esses dados no mapa com caixas de pop-up interativas.

1. Adicione as seguintes linhas de código no mapa `ready` manipulador de eventos após o código para consultar o serviço de pesquisa fuzzy. Esse código criará uma instância de um popup e adicionará um evento de mouseover à camada de símbolo.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```

    A API `*atlas.Popup` fornece uma janela de informação ancorada na posição exigida no mapa. 

2. Adicione o seguinte código dentro da função `GetMap`, para mostrar a informação do resultado moused no popup.

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

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma conta com o Azure Maps
> * Obter a chave primária para a sua conta
> * Criar uma nova página Web com a API de Controlo de Mapas
> * Utilizar o Search Service para localizar pontos de interesse nas proximidades

> [!div class="nextstepaction"]
> [Ver código fonte completo](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)

> [!div class="nextstepaction"]
> [Ver amostra ao vivo](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

O próximo tutorial demonstra como apresentar um percurso entre duas localizações.

> [!div class="nextstepaction"]
> [Encaminhar para um destino](./tutorial-route-location.md)
